---
title: Implementación del servicio Site Recovery Mobility con DSC de Azure Automation| Microsoft Docs
description: Se describe cómo usar DSC de Azure Automation para implementar automáticamente el servicio Azure Site Recovery Mobility y el agente de Azure para la replicación de servidores físicos y VM de VMware en Azure.
services: site-recovery
author: krnese
manager: lorenr
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: krnese
ms.openlocfilehash: 0bf1b551ba578cd152201c131bd60470bdc9d86a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="deploy-the-mobility-service-with-azure-automation-dsc"></a>Implementación de Mobility Service con DSC de Azure Automation

Mobility Service se implementa en máquina virtuales de VMware y servidores físicos que quiera replicar en Azure mediante [Azure Site Recovery](site-recovery-overview.md).

En este artículo se describe cómo implementar el servicio en las máquinas Windows, utilizando Desired State Configuration (DSC) de Azure Automation para asegurarse de que:


## <a name="prerequisites"></a>requisitos previos

* Un repositorio para almacenar la instalación necesaria
* Un repositorio para almacenar la frase de contraseña necesaria para registrarse en el servidor de administración. Se genera una frase de contraseña única para un servidor de configuración específico. 
* Windows Management Framework (WMF) 5.0 debe estar instalado en las máquinas virtuales en las que quiere habilitar la protección. Se trata de un requisito para DSC de Automatización.
Si quiere usar DSC para máquinas Windows que tengan instalado WMF 4.0, consulte [Uso de DSC en entornos desconectados](## Use DSC in disconnected environments).
 

## <a name="extract-binaries"></a>Extracción de los archivos binarios

Mobility Service se puede instalar mediante la línea de comandos y acepta varios argumentos. Tiene que obtener los archivos binarios (después de extraerlos de la configuración) y almacenarlos en un lugar donde puede recuperarlos mediante una configuración de DSC.

1. Para extraer los archivos que necesita para esta configuración, vaya al siguiente directorio en el servidor de administración: **\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**
2. En esta carpeta, compruebe que existe un archivo MSI con nombre: **Microsoft-ASR_UA_version_Windows_GA_date_Release.exe**.
3. Use el siguiente comando para extraer el instalador: **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**
2. Seleccione todos los archivos y envíelos a una carpeta comprimida (con zip).

Ahora dispone de los archivos binarios que necesita para automatizar la instalación de Mobility Service con DSC de Automatización.

## <a name="store-the-passphrase"></a>Almacene la frase de contraseña

Tiene que decidir dónde desea colocar esta carpeta comprimida. Puede utilizar una cuenta de almacenamiento de Azure, tal como se muestra más adelante, para almacenar la frase de contraseña que necesita para la instalación. A continuación, el agente se registra con el servidor de administración como parte del proceso.

1. Guarde la frase de contraseña que recibió al implementar el servidor de configuración en un archivo de texto como passphrase.txt.
2. Coloque la carpeta comprimida y la frase de contraseña en un contenedor dedicado en la cuenta de almacenamiento de Azure.


Si prefiere mantener estos archivos en un recurso compartido de la red, puede hacerlo. Deberá asegurarse de que el recurso de DSC que se usará más adelante tenga acceso y pueda obtener la instalación y la frase de contraseña.

## <a name="create-the-dsc-configuration"></a>Creación de la configuración de DSC

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

## <a name="upload-to-automation-dsc"></a>Carga en DSC de Automatización
Puesto que la configuración de DSC que ha realizado va a importar un módulo de recursos de DSC necesario (xPSDesiredStateConfiguration), debe importar ese módulo en Automation antes de cargar la configuración de DSC.

1. Inicie sesión en su cuenta de Automation, vaya a **Recursos** > **Módulos** y haga clic en **Examinar la galería**.
2. Busque el módulo e impórtelo en su cuenta.
3. Cuando termine, vaya a la máquina donde tiene instalados los módulos de Azure Resource Manager y continúe para importar la configuración de DSC recién creada.

## <a name="import-cmdlets"></a>Cmdlets de importación

1. En PowerShell, inicie sesión en su suscripción de Azure.
2. Modifique los cmdlets para que reflejen el entorno y capturen la información de la cuenta de Automation en una variable:

    ```powershell
    $AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
    ```

3. Cargue la configuración en DSC de Automatización mediante el siguiente cmdlet:

    ```powershell
    $ImportArgs = @{
        SourcePath = 'C:\ASR\ASRMobilityService.ps1'
        Published = $true
        Description = 'DSC Config for Mobility Service'
    }
    $AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
    ```

## <a name="compile-the-configuration-in-automation-dsc"></a>Compilación de la configuración en DSC de Automatización

1. Compile la configuración en DSC de Automatización, para poder comenzar a registrar los nodos en ella. Para conseguirlo, ejecute el siguiente cmdlet:

    ```    powershell
    $AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
    ```

Esta operación puede tardar unos minutos, porque básicamente se está implementando la configuración en el servicio de extracción de DSC hospedado.

2. Después de compilar la configuración, puede recuperar la información de trabajo mediante PowerShell (Get-AzureRmAutomationDscCompilationJob) o el [Portal de Azure](https://portal.azure.com/).

    ![Recuperar trabajo](./media/vmware-azure-mobility-deploy-automation-dsc/retrieve-job.png)

Ahora ya ha publicado y cargado correctamente la configuración de DSC en DSC de Automatización.

## <a name="onboard-machines-to-automation-dsc"></a>Incorporación de máquinas a DSC de Automatización


1. Asegúrese de que las máquinas Windows se actualizan con la versión más reciente de WMF. Puede descargar e instalar la versión correcta para su plataforma desde el [Centro de descarga](https://www.microsoft.com/download/details.aspx?id=50395).
2. Cree una metaconfiguración para DSC que se aplicará a los nodos. Para hacerlo correctamente, se debe recuperar la dirección URL del punto de conexión y la clave principal para la cuenta de Automation seleccionada en Azure. Puede encontrar estos valores en **Claves** en la hoja **Toda la configuración** de la cuenta de Automation.

    ![Valores clave](./media/vmware-azure-mobility-deploy-automation-dsc/key-values.png)

En este ejemplo, tenemos un servidor físico con Windows Server 2012 R2 que queremos proteger con Site Recovery.

## <a name="check-for-any-pending-file-rename-operations"></a>Comprobación de operaciones pendientes de cambio de nombre de archivos

Antes de empezar a asociar el servidor al punto de conexión de DSC de Automatización, recomendamos que compruebe las operaciones pendientes de cambio de nombre de archivos en el Registro. Podrían impedir que finalizara la configuración al haber un reinicio pendiente.

1. Ejecute el siguiente cmdlet para comprobar que no haya ningún reinicio pendiente en el servidor:

    ```powershell
    Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
    ```
2. Si no aparecen resultados, puede continuar. Si no es así, debe resolverlo reiniciando el servidor durante una ventana de mantenimiento.
3. Para aplicar la configuración en el servidor, inicie el Entorno de scripting integrado (ISE) de PowerShell y ejecute el siguiente script. Esta es básicamente una configuración local de DSC que indicará al motor del administrador de configuración local que se registre en el servicio DSC de Automatización y recupere la configuración específica (ASRMobilityService.localhost).

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

1. Después de ejecutar este script, el nodo debe comenzar a registrarse en DSC de Automatización.

    ![Registro del nodo en curso](./media/vmware-azure-mobility-deploy-automation-dsc/register-node.png)

2. Si vuelve al Portal de Azure, puede ver que ahora aparece el código recién registrado.

    ![Nodo registrado en el portal](./media/vmware-azure-mobility-deploy-automation-dsc/registered-node.png)

3. En el servidor, puede ejecutar el siguiente cmdlet de PowerShell para comprobar que el nodo se ha registrado correctamente:

    ```powershell
    Get-DscLocalConfigurationManager
    ```

4. Después de que se ha extraído la configuración y se ha aplicado al servidor, puede comprobar esto mediante la ejecución del siguiente script:

    ```powershell
    Get-DscConfigurationStatus
    ```

La salida muestra que el servidor ha extraído correctamente su configuración:

![Salida](./media/vmware-azure-mobility-deploy-automation-dsc/successful-config.png)

Además, la configuración de Mobility Service tiene su propio registro, que se encuentra en *SystemDrive*\ProgramData\ASRSetupLogs.

¡Ya está! Ahora ha implementado y registrado correctamente Mobility Service en la máquina que quiere proteger con Site Recovery. DSC se asegurará de que los servicios necesarios se estén siempre ejecutando.

![Implementación correcta](./media/vmware-azure-mobility-deploy-automation-dsc/successful-install.png)

Después de que el servidor de administración detecta que la implementación es correcta, puede configurar la protección y habilitar la replicación en la máquina mediante Site Recovery.

## <a name="use-dsc-in-disconnected-environments"></a>Uso de DCS en entornos desconectados

Aunque las máquinas no estén conectadas a Internet, puede usar DSC para implementar y configurar Mobility Service en las cargas de trabajo que quiere proteger.

Puede crear instancias de su propio servidor de extracción de DSC en el entorno para proporcionar básicamente las mismas funcionalidades que obtiene de DSC de Automatización. Es decir, los clientes extraerán la configuración (después de registrarla) en el punto de conexión de DSC. Sin embargo, otra opción es insertar manualmente la configuración de DSC en las máquinas, bien localmente o de forma remota.

Tenga en cuenta que en este ejemplo, se ha agregado un parámetro para el nombre del equipo. Los archivos remotos se encuentran ahora en un recurso compartido remoto al que deben poder acceder las máquinas que quiere proteger. El final del script representa la configuración y luego comienza a aplicar la configuración de DSC al equipo de destino.

### <a name="prerequisites"></a>requisitos previos
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
Después de implementar los agentes de Mobility Service, puede [habilitar la replicación](vmware-azure-tutorial.md) para las máquinas virtuales.
