---
title: "Implementación del servicio Site Recovery Mobility con DSC de Azure Automation| Microsoft Docs"
description: "Se describe cómo usar DSC de Azure Automation para implementar automáticamente el servicio Azure Site Recovery Mobility y el agente de Azure para la replicación de servidores físicos y VM de VMware en Azure."
services: site-recovery
documentationcenter: 
author: krnese
manager: lorenr
editor: 
ms.assetid: 1f8cd3ac-0522-48eb-a5f0-679ee9192ddb
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: krnese
ms.openlocfilehash: 118a2e775ae3d036f58989d9778104e372e8c701
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-the-mobility-service-with-azure-automation-dsc-for-replication-of-vm"></a>Implementación del servicio Mobility con DSC de Azure Automation para la replicación de VM
En Operations Management Suite, le proporcionamos una completa solución de copia de seguridad y recuperación ante desastres que puede usar como parte de su plan de continuidad empresarial.

Comenzamos este viaje con Hyper-V usando Réplica de Hyper-V. Pero nos hemos expandido para abarcar la configuración heterogénea dado que los clientes tienen varios hipervisores y plataformas en sus nubes.

Si actualmente ejecuta cargas de trabajo de VMware o servidores físicos, un servidor de administración ejecuta todos los componentes de Azure Site Recovery de su entorno para controlar la comunicación y la replicación de datos con Azure, si Azure es su destino.

## <a name="deploy-the-site-recovery-mobility-service-by-using-automation-dsc"></a>Implementación de Mobility Service de Site Recovery mediante DSC de Automatización
Para comenzar, vamos a realizar un desglose rápido de lo que hace este servidor de administración.

El servidor de administración ejecuta varios roles de servidor. Uno de ellos es el de *configuración*, que coordina la comunicación y administra los procesos de replicación y recuperación de datos.

Además, el rol de *proceso* actúa como puerta de enlace de replicación. Este rol recibe datos de replicación provenientes de máquinas de origen protegidas, los optimiza con almacenamiento en caché, compresión y cifrado y los envía a una cuenta de almacenamiento de Azure. Otra de las funciones del rol de proceso es insertar la instalación de Mobility Service en máquinas protegidas y llevar a cabo la detección automática de máquinas virtuales de VMware.

En el caso de una conmutación por recuperación de Azure, el rol de *destino maestro* será administrará los datos de replicación como parte de esta operación.

Para las máquinas protegidas, confiamos en *Mobility Service*. Este componente está implementado en cada máquina (máquina virtual de VMware o servidor físico) que quiere replicar en Azure. Lo que hace es capturar las escrituras de datos en la máquina y reenviarlas al servidor de administración (rol de proceso).

Cuando se trabaja hacia la continuidad empresarial, es importante comprender las cargas de trabajo, la infraestructura y los componentes que intervienen. Así puede cumplir los requisitos de los objetivos de tiempo de recuperación (RTO) y objetivos de punto de recuperación (RPO). En este contexto, Mobility Service es esencial para garantizar la protección de las cargas de trabajo de la manera prevista.

De modo que, ¿cómo puede garantizar, de una forma optimizada, que dispone de una configuración protegida confiable con la ayuda de algunos componentes de Operations Management Suite?

En este artículo se proporciona un ejemplo de cómo puede usar Configuración de estado deseado (DSC) de Azure Automation, en combinación con Site Recovery, para garantizar que:

* Mobility Service y el agente de máquina virtual de Azure están implementados en las máquinas virtuales que quiere proteger.
* Mobility Service y el agente de máquina virtual de Azure siempre se ejecutan cuando Azure es el destino de la replicación.

## <a name="prerequisites"></a>Requisitos previos
* Un repositorio para almacenar la instalación necesaria
* Un repositorio para almacenar la frase de contraseña necesaria para registrarse en el servidor de administración

  > [!NOTE]
  > Se genera una frase de contraseña única para cada servidor de administración. Si va a implementar varios servidores de administración, tendrá que asegurarse de que se almacene la frase de contraseña correcta en el archivo passphrase.txt.
  >
  >
* Windows Management Framework (WMF) 5.0 instalado en las máquinas virtuales en las que quiere habilitar la protección (un requisito para DSC de Azure Automation)

  > [!NOTE]
  > Si quiere usar DSC para máquinas Windows que tengan instalado WMF 4.0, consulte la sección [Uso de DSC en entornos desconectados](## Use DSC in disconnected environments).
  

Mobility Service se puede instalar mediante la línea de comandos y acepta varios argumentos. Por ese motivo debe tener los archivos binarios (después de extraerlos de la configuración) y almacenarlos en un lugar donde puede recuperarlos mediante una configuración de DSC.

## <a name="step-1-extract-binaries"></a>Paso 1: Extracción de los archivos binarios
1. Para extraer los archivos que necesita para esta configuración, vaya al siguiente directorio en el servidor de administración:

    **\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**

    En esta carpeta, debería ver un archivo MSI denominado:

    **Microsoft-ASR_UA_version_Windows_GA_date_Release.exe**

    Use el comando siguiente para extraer el instalador:

    **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**
2. Seleccione todos los archivos y envíelos a una carpeta comprimida (en zip).

Ahora dispone de los archivos binarios que necesita para automatizar la instalación de Mobility Service con DSC de Automatización.

### <a name="passphrase"></a>Frase de contraseña
A continuación, debe determinar dónde desea colocar esta carpeta comprimida. Puede utilizar una cuenta de almacenamiento de Azure, tal como se muestra más adelante, para almacenar la frase de contraseña que necesita para la instalación. A continuación, el agente se registra con el servidor de administración como parte del proceso.

La frase de contraseña que recibió al implementar el servidor de administración se puede guardar en un archivo de texto como passphrase.txt.

Coloque la carpeta comprimida y la frase de contraseña en un contenedor dedicado en la cuenta de almacenamiento de Azure.

![Ubicación de la carpeta](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Si prefiere mantener estos archivos en un recurso compartido de la red, puede hacerlo. Deberá asegurarse de que el recurso de DSC que se usará más adelante tenga acceso y pueda obtener la instalación y la frase de contraseña.

## <a name="step-2-create-the-dsc-configuration"></a>Paso 2: Creación de la configuración de DSC
La instalación depende de WMF 5.0. Para que la máquina aplique correctamente la configuración mediante DSC de automatización, es necesario que WMF 5.0 esté presente.

En el entorno se usa el siguiente ejemplo de configuración de DSC:

```powershell
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
La configuración hará lo siguiente:

* Las variables indicarán a la configuración dónde obtener los archivos binarios para Mobility Service y el agente de máquina virtual de Azure, dónde obtener la frase de contraseña y dónde almacenar la salida.
* La configuración importará el recurso de DSC xPSDesiredStateConfiguration, por lo que puede usar `xRemoteFile` para descargar los archivos del repositorio.
* La configuración creará el directorio donde quiere almacenar los archivos binarios.
* El recurso "Archive" extraerá los archivos de la carpeta comprimida.
* El recurso de instalación del paquete instalará Mobility Service desde el instalador UNIFIEDAGENT.EXE con los argumentos específicos. (Habrá que cambiar las variables que construyen los argumentos para que reflejen el entorno).
* El recurso AzureAgent del paquete instalará el agente de máquina virtual de Azure, que se recomienda en cada máquina virtual que se ejecuta en Azure. El agente de máquina virtual de Azure también hace posible agregar extensiones a la máquina virtual tras la conmutación por error.
* El recurso o los recursos de servicios garantizan que los servicios de Mobility Service y los servicios de Azure relacionados están siempre en ejecución.

Guarde la configuración como **ASRMobilityService**.

> [!NOTE]
> No olvide reemplazar CSIP en la configuración para que refleje el servidor de administración real, de forma que el agente se conecte sin problemas y con la frase de contraseña correcta.
>
>

## <a name="step-3-upload-to-automation-dsc"></a>Paso 3: Carga en DSC de automatización
Puesto que la configuración de DSC que ha realizado va a importar un módulo de recursos de DSC necesario (xPSDesiredStateConfiguration), debe importar ese módulo en Automation antes de cargar la configuración de DSC.

Inicie sesión en su cuenta de Automation, vaya a **Recursos** > **Módulos** y haga clic en **Examinar la galería**.

Aquí puede buscar el módulo e importarlo en su cuenta.

![Importar el módulo](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

Cuando termine, vaya a la máquina donde tiene instalados los módulos de Azure Resource Manager y continúe para importar la configuración de DSC recién creada.

### <a name="import-cmdlets"></a>Cmdlets de importación
En PowerShell, inicie sesión en su suscripción de Azure. Modifique los cmdlets para que reflejen el entorno y capturen la información de la cuenta de Automation en una variable:

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

Cargue la configuración en DSC de Automatización mediante el siguiente cmdlet:

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### <a name="compile-the-configuration-in-automation-dsc"></a>Compilación de la configuración en DSC de Automatización
A continuación, debe compilar la configuración en DSC de Automatización, para poder comenzar a registrar los nodos en ella. Para conseguirlo, ejecute el siguiente cmdlet:

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Esta operación puede tardar unos minutos, porque básicamente se está implementando la configuración en el servicio de extracción de DSC hospedado.

Después de compilar la configuración, puede recuperar la información de trabajo mediante PowerShell (Get-AzureRmAutomationDscCompilationJob) o el [Portal de Azure](https://portal.azure.com/).

![Recuperar trabajo](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

Ahora ya ha publicado y cargado correctamente la configuración de DSC en DSC de Automatización.

## <a name="step-4-onboard-machines-to-automation-dsc"></a>Paso 4: Incorporación de máquinas a DSC de automatización
> [!NOTE]
> Uno de los requisitos previos para completar este escenario es que las máquinas Windows estén actualizadas con la versión más reciente de WMF. Puede descargar e instalar la versión correcta para su plataforma desde el [Centro de descarga](https://www.microsoft.com/download/details.aspx?id=50395).
>
>

Ahora creará una metaconfiguración de DSC que se aplicará a los nodos. Para hacerlo correctamente, se debe recuperar la dirección URL del punto de conexión y la clave principal para la cuenta de Automation seleccionada en Azure. Puede encontrar estos valores en **Claves** en la hoja **Toda la configuración** de la cuenta de Automation.

![Valores clave](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

En este ejemplo, tenemos un servidor físico con Windows Server 2012 R2 que queremos proteger con Site Recovery.

### <a name="check-for-any-pending-file-rename-operations-in-the-registry"></a>Comprobación de operaciones pendientes de cambio de nombre de archivos en el Registro
Antes de empezar a asociar el servidor al punto de conexión de DSC de Automatización, recomendamos que compruebe las operaciones pendientes de cambio de nombre de archivos en el Registro. Podrían impedir que finalizara la configuración al haber un reinicio pendiente.

Ejecute el siguiente cmdlet para comprobar que no haya ningún reinicio pendiente en el servidor:

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Si no aparecen resultados, puede continuar. Si no es así, debe resolverlo reiniciando el servidor durante una ventana de mantenimiento.

Para aplicar la configuración en el servidor, inicie el Entorno de scripting integrado (ISE) de PowerShell y ejecute el siguiente script. Esta es básicamente una configuración local de DSC que indicará al motor del administrador de configuración local que se registre en el servicio DSC de Automatización y recupere la configuración específica (ASRMobilityService.localhost).

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Esta configuración hará que el motor del administrador de configuración local se registre en DSC de Automatización. También determina cómo debe funcionar el motor, qué debe hacer si se produce un desfase de configuración (ApplyAndAutoCorrect) y cómo se debe continuar con la configuración en caso de que sea necesario un reinicio.

Después de ejecutar este script, el nodo debe comenzar a registrarse en DSC de Automatización.

![Registro del nodo en curso](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Si vuelve al Portal de Azure, puede ver que ahora aparece el código recién registrado.

![Nodo registrado en el portal](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

En el servidor, puede ejecutar el siguiente cmdlet de PowerShell para comprobar que el nodo se ha registrado correctamente:

```powershell
Get-DscLocalConfigurationManager
```

Después de que se ha extraído la configuración y se ha aplicado al servidor, puede comprobar esto mediante la ejecución del siguiente script:

```powershell
Get-DscConfigurationStatus
```

La salida muestra que el servidor ha extraído correctamente su configuración:

![Salida](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

Además, la configuración de Mobility Service tiene su propio registro, que se encuentra en *SystemDrive*\ProgramData\ASRSetupLogs.

¡Ya está! Ahora ha implementado y registrado correctamente Mobility Service en la máquina que quiere proteger con Site Recovery. DSC se asegurará de que los servicios necesarios se estén siempre ejecutando.

![Implementación correcta](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

Después de que el servidor de administración detecta que la implementación es correcta, puede configurar la protección y habilitar la replicación en la máquina mediante Site Recovery.

## <a name="use-dsc-in-disconnected-environments"></a>Uso de DCS en entornos desconectados
Aunque las máquinas no estén conectadas a Internet, puede usar DSC para implementar y configurar Mobility Service en las cargas de trabajo que quiere proteger.

Puede crear instancias de su propio servidor de extracción de DSC en el entorno para proporcionar básicamente las mismas funcionalidades que obtiene de DSC de Automatización. Es decir, los clientes extraerán la configuración (después de registrarla) en el punto de conexión de DSC. Sin embargo, otra opción es insertar manualmente la configuración de DSC en las máquinas, bien localmente o de forma remota.

Tenga en cuenta que en este ejemplo, se ha agregado un parámetro para el nombre del equipo. Los archivos remotos se encuentran ahora en un recurso compartido remoto al que deben poder acceder las máquinas que quiere proteger. El final del script representa la configuración y luego comienza a aplicar la configuración de DSC al equipo de destino.

### <a name="prerequisites"></a>Requisitos previos
Asegúrese de que está instalado el módulo de PowerShell xPSDesiredStateConfiguration. Para máquinas Windows con WMF 5.0 instalado, puede instalar el módulo xPSDesiredStateConfiguration ejecutando el siguiente cmdlet en las máquinas de destino:

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

También puede descargar y guardar el módulo, en caso de que necesite distribuirlo a máquinas Windows que tienen WMF 4.0. Ejecute este cmdlet en una máquina donde esté presente PowerShellGet (WMF 5.0):

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

También para WMF 4.0, asegúrese de que la [actualización KB2883200 de Windows 8.1](https://www.microsoft.com/download/details.aspx?id=40749) está instalada en las máquinas.

La siguiente configuración se puede insertar en máquinas Windows que tengan WMF 5.0 y WMF 4.0:

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

Si quiere crear una instancia de su propio servidor de inserción de DSC en la red corporativa para imitar las funcionalidades que puede obtener en DSC de Automatización, consulte [Configuración de un servidor de extracción web de DSC](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396).

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>Opcional: Implementación de una configuración de DSC mediante una plantilla de Azure Resource Manager
Este artículo se ha centrado en cómo puede crear su propia configuración de DSC para implementar automáticamente Mobility Service y el agente de máquina virtual de Azure, y asegurarse de que se ejecutan en las máquinas que quiere proteger. También tenemos una plantilla de Azure Resource Manager que implementará esta configuración de DSC en una cuenta de Azure Automation nueva o existente. La plantilla usará parámetros de entrada para crear recursos de Automation que contendrán las variables de su entorno.

Después de implementar la plantilla, puede hacer referencia simplemente al paso 4 de esta guía para incorporar las máquinas.

La plantilla hará lo siguiente:

1. Usar una cuenta existente de Automation o crear una nueva
2. Aceptar los parámetros de entrada de:
   * ASRRemoteFile: la ubicación donde se almacena la configuración de Mobility Service
   * ASRPassphrase: la ubicación donde ha almacenado el archivo passphrase.txt
   * ASRCSEndpoint: la dirección IP del servidor de administración
3. Importar el módulo xPSDesiredStateConfiguration PowerShell
4. Crear y compilar la configuración de DSC

Todos los pasos anteriores se realizarán en el orden correcto, para que pueda comenzar a incorporar sus máquinas que reciban protección.

La plantilla, con instrucciones para la implementación, se encuentra en [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC).

Implemente la plantilla mediante PowerShell:

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>Pasos siguientes
Después de implementar los agentes de Mobility Service, puede [habilitar la replicación](site-recovery-vmware-to-azure.md) para las máquinas virtuales.
