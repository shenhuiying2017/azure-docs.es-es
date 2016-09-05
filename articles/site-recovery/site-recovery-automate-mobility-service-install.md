<properties
	pageTitle="Replicación de máquinas virtuales de VMware en Azure mediante Site Recovery con DSC de Automatización de Azure | Microsoft Azure"
	description="Describe cómo usar DSC de Automatización de Azure para implementar automáticamente ASR Mobility Service y el agente de Azure para máquinas virtuales o físicas en Azure."
	services="site-recovery"
	documentationCenter=""
	authors="krnese"
	manager="lorenr"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="krnese"/>

# Replicación de máquinas virtuales de VMware mediante Site Recovery con DSC de Automatización de Azure

En OMS, se proporciona una completa solución de copia de seguridad y recuperación ante desastres que puede aprovechar como parte de su plan de continuidad empresarial.

Este recorrido comenzó con Hyper-V usando Réplica de Hyper-V, pero se ha ampliado la compatibilidad a instalaciones heterogéneas, ya que es bien sabido que los clientes tienen varios hipervisores y plataformas en la nube.

Si se ejecutan cargas de trabajo de VMware o servidores físicos en la actualidad, se confía en un servidor de administración que ejecuta todos los componentes de Site Recovery presentes en su entorno para controlar la replicación de datos y la comunicación con Azure cuando Azure es el destino.

## Implementación de ASR Mobility Service con DSC de Automatización de OMS
Para empezar, se ofrece a continuación un desglose rápido de lo que hace realmente este servidor de administración:

El servidor de administración ejecuta varios roles del servidor, como el de **configuración**, que coordina la comunicación y administra los procesos de replicación y recuperación de datos.

Además, el rol de **proceso** actúa de puerta de enlace de replicación que recibe datos de replicación desde máquinas de origen protegidas y los optimiza con almacenamiento en caché, compresión y cifrado antes de enviarlos a la cuenta de almacenamiento de Azure. Otra de las funciones del rol de proceso es insertar la instalación de Mobility Service en máquinas protegidas y llevar a cabo la detección automática de máquinas virtuales de VMware.

En el caso de una conmutación por recuperación de Azure, el rol de **destino maestro** será responsable y administrará los datos de replicación como parte de esta operación.

Para las máquinas protegidas, se confía en **Mobility Service**, un componente que se implementa en todas las máquinas (máquina virtual de VMware o servidor físico) que se van a replicar en Azure. Su rol es capturar las escrituras de datos en la máquina y reenviarlas al servidor de administración (rol de proceso).

En lo que respecta a la continuidad del negocio, es importante comprender las cargas de trabajo, la infraestructura y también los componentes relacionados para cumplir los requisitos de RTO y RPO. En este contexto, el servicio de movilidad es esencial para garantizar la protección de las cargas de trabajo como se esperaría.

Entonces, ¿cómo es posible asegurarse, de forma optimizada, de que se cuenta con una instalación protegida de confianza con la ayuda de algunos componentes de OMS?

En este artículo se le proporcionará un ejemplo de cómo utilizar DSC de Automatización de OMS junto con Site Recovery de OMS para asegurarse de que Mobility Service y el agente de máquina virtual de Azure se implementen en las máquinas Windows que desea proteger y estén siempre en ejecución cuando Azure sea el destino de replicación.

## Requisitos previos

- Un repositorio para almacenar la instalación necesaria
- Un repositorio para almacenar la frase de contraseña necesaria para registrarse en el servidor de administración
- Windows Management Framework 5.0 instalado en las máquinas en las que desea habilitar la protección (requisito para DSC de Automatización de OMS)

Si desea usar DSC en las máquinas Windows con WMF 4.0, consulte la sección "Uso de DCS en entornos desconectados".

(Se genera una frase de contraseña única para cada servidor de administración. Si va a implementar varios servidores de administración, tendrá que asegurarse de que se almacene la frase de contraseña correcta en el archivo passphrase.txt).

Se puede instalar Mobility Service mediante la línea de comandos y acepta varios argumentos.

Por esta razón, se necesita tener los archivos binarios (después de extraerlos de la instalación) y almacenarlos en algún lugar del que se puedan recuperar mediante una configuración de DSC.

## Paso 1: Extracción de los archivos binarios

1. Para extraer los archivos necesarios para esta instalación, vaya al siguiente directorio en el servidor de administración: **\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository**.

    En esta carpeta, debería ver un archivo MSI denominado:

    **Microsoft-ASR\_UA\_<versión>_Windows\_GA_<fecha>\_Release.exe**

    Use el comando siguiente para extraer el instalador:

    .\\Microsoft-ASR\_UA\_9.1.0.0_Windows\_GA_02May2016\_release.exe /q /x:C:\\Users\\Administrator\\Desktop\\Mobility\_Service\\Extract

2. Seleccione todos los archivos y envíelos a una carpeta comprimida (en zip).

¡Ya está! Ahora dispone de los archivos binarios que necesita para automatizar la instalación del servicio de movilidad con DSC de Automatización de OMS.

### Frase de contraseña
A continuación, debe determinar dónde desea colocar esta carpeta comprimida. Puede utilizar una cuenta de almacenamiento de Azure, como se muestra más adelante, para almacenar la frase de contraseña que necesita para la instalación, de modo que el agente se registrará en el servidor de administración como parte del proceso.

La frase de contraseña que recibió al implementar el servidor de administración se puede guardar en un archivo txt como passphrase.txt.

Colocación de la carpeta comprimida y la frase de contraseña en un contenedor dedicado en la cuenta de almacenamiento de Azure

![ubicación de la carpeta](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Si prefiere mantener estos archivos en un recurso compartido de la red, puede hacerlo así también. Necesitará asegurarse de que el recurso de DSC que se usará más adelante realmente tenga acceso y pueda obtener la instalación y la frase de contraseña.

## Paso 2: Creación de la configuración de DSC
La instalación depende de WMF 5.0, lo que significa que, para que la máquina aplique correctamente la configuración mediante DSC de Automatización de OMS, WMF 5.0 debe estar presente.

Se usa el siguiente ejemplo de configuración de DSC en este entorno:

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

- Las variables indicarán a la configuración dónde obtener los archivos binarios para Mobility Service y el agente de máquina virtual de Azure, junto con la frase de contraseña. También indicarán dónde almacenar la salida.
- Importe el recurso de DSC xPSDesiredStateConfiguration de forma que se pueda usar "xRemoteFile" para descargar los archivos desde el repositorio.
- Cree un directorio para almacenar los archivos binarios.
- El recurso "Archive" extraerá los archivos de la carpeta comprimida.
- El recurso "Package Install" instalará Mobility Service desde el instalador UNIFIEDAGENT.EXE con los argumentos específicos (las variables que construyen los argumentos deben cambiarse para reflejar su entorno).
- El recurso "Package AzureAgent" instalará al agente de máquina virtual de Azure que se recomienda en cada máquina virtual que se ejecuta en Azure y también permite agregar extensiones a la máquina virtual después de una conmutación por error.
- El recurso o los recursos "Service" garantizarán que los servicios de movilidad relacionados y los servicios de Azure siempre se estén ejecutando.

Guarde la configuración como **ASRMobilityService**.

(No olvide reemplazar CSIP en la configuración para que refleje el servidor de administración real, de forma que el agente se conecte sin problemas y con la frase de contraseña correcta).

## Paso 3: Carga en DSC de Automatización de OMS

Puesto que la configuración de DSC que ha realizado va a importar un módulo de recursos de DSC necesario (xPSDesiredStateConfiguration), debe importar ese módulo en Automatización de OMS antes de cargar la configuración de DSC.

Inicie sesión en la cuenta de automatización y vaya a Recursos > Módulos y haga clic en Examinar la galería.

Aquí puede buscar el módulo e importarlo en su cuenta.

![importar módulo](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

Una vez hecho esto, en la máquina donde tiene instalados los módulos de Azure RM, proceda a importar la configuración de DSC recién creada.

### Cmdlets de importación

En PowerShell, inicie sesión en su suscripción de Azure y modifique los cmdlets para reflejar su entorno y capturar la información de su cuenta de Automatización en una variable:
```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

En primer lugar, cargue la configuración en DSC de Automatización de OMS mediante el siguiente cmdlet:

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

A continuación, debe compilar la configuración en DSC de Automatización de OMS, para poder comenzar a registrar los nodos en ella.

### Compilación de la configuración en DSC de Automatización de OMS

Para conseguirlo, ejecute el siguiente cmdlet:

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Puede tardar unos minutos, porque básicamente se está implementando la configuración en el servicio de extracción de DSC hospedado.

Una vez completado el proceso, puede recuperar la información del trabajo mediante PowerShell (Get-AzureRmAutomationDscCompilationJob) o en portal.azure.com.

![Recuperar trabajo](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

Ahora ya ha publicado y cargado correctamente la configuración de DSC en DSC de Automatización de OMS.

## Paso 4: Incorporación de máquinas a DSC de Automatización de OMS
*Uno de los requisitos previos para completar este escenario es que las máquinas Windows estén actualizadas con la versión más reciente de WMF. Puede descargar e instalar la versión correcta para su plataforma desde esta dirección URL: https://www.microsoft.com/download/details.aspx?id=50395*

Ahora creará una metaconfiguración de DSC que se aplicará a los nodos. Para hacerlo correctamente, se debe recuperar la dirección URL del punto de conexión y la clave principal para la cuenta de Automatización seleccionada en Azure.

Estos valores se encuentran en "Claves", en la hoja "Toda la configuración" de la cuenta de Automatización.

![Valores clave](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

En este ejemplo, tenemos un servidor físico con Windows Server 2012 R2 que queremos proteger con Site Recovery de OMS.

Antes de empezar a asociar el servidor con el punto de conexión de DSC de Automatización, se recomienda comprobar si quedan pendientes operaciones de cambio de nombre de archivos en el Registro, ya que esto puede impedir que se complete la instalación debido a un reinicio pendiente.

### Comprobación de operaciones pendientes de cambio de nombre de archivos en el Registro

Ejecute el siguiente cmdlet para comprobar que no haya ningún reinicio pendiente en el servidor:

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Si no aparecen resultados, puede continuar. Si no es así, debe resolverlo reiniciando el servidor durante una ventana de mantenimiento.

Para aplicar la configuración en el servidor, inicie PowerShell ISE y ejecute el script siguiente. Esencialmente, se trata de una configuración local de DSC que indicará al motor del administrador de configuración local que se registre con el servicio DSC de Automatización de OMS y recupere la configuración específica (ASRMobilityService.localhost)

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

Con esta configuración, se configura el administrador de configuración local para que se registre con DSC de Automatización de OMS y básicamente indique cómo debe funcionar el motor, qué debe hacer en caso de un desfase de configuración (ApplyAndAutoCorrect) y, si hay que reiniciar, que continúe con la configuración después.

Una vez que se ejecuta, el nodo debería empezar a registrarse con DSC de Automatización.

![Registrar nodo](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Si regresa al portal.azure.com, puede ver que el nodo recién registrado aparece ahora en el portal.

![Registrar nodo](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

En el servidor, puede ejecutar el siguiente cmdlet de PowerShell para comprobar que se ha registrado correctamente:

```powershell
Get-DscLocalConfigurationManager
```

Una vez que haya extraído la configuración y la haya aplicado al servidor, puede comprobarlo ejecutando:

```powershell
Get-DscConfigurationStatus
```

La salida muestra que el servidor ha extraído correctamente su configuración:

![Registrar nodo](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

Además, la instalación de Mobility Service tiene su propio registro, que se encuentra en "<unidadDelSistema>\\ProgramData\\ASRSetupLogs".

Y ya está. Ahora ha implementado y registrado correctamente Mobility Service en la máquina que se quiere proteger con Site Recovery y, gracias a DSC, puede confiar en que siempre se estarán ejecutando los servicios necesarios.

![Registrar nodo](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

Una vez que el servidor de administración lo ha detectado, podrá configurar la protección y habilitar la replicación en la máquina con Site Recovery.

## Uso de DCS en entornos desconectados

Si las máquinas no están conectadas a Internet, todavía se puede usar DSC para implementar y configurar Mobility Service en las cargas de trabajo que desee proteger.

Puede crear instancias de su propio servidor de extracción de DSC en su entorno, las cuales proporcionarán esencialmente las mismas funcionalidades que obtiene de DSC de Automatización de OMS. En este caso, los clientes extraerán la configuración una vez registrados en el punto de conexión de DSC. Sin embargo, existe otra opción que consiste en usar la inserción: se inserta manualmente la configuración de DSC en las máquinas, de forma local o remota.

Tenga en cuenta que, en este ejemplo, se ha agregado un parámetro para computername y los archivos remotos se encuentran ahora en un recurso compartido remoto que debería resultar accesible a las máquinas que quiere proteger. Al final del script, se establece la configuración y después se empieza a aplicar la configuración de DSC en el equipo de destino.

### Requisitos previos

· El módulo xPSDesiredStateConfiguration de PowerShell instalado

Para máquinas Windows con WMF 5.0 instalado, basta con instalar el módulo xPSDesiredStateConfiguration ejecutando el siguiente cmdlet en la máquina o las máquinas de destino:

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

También puede descargar y guardar el módulo, por si necesita distribuirlo a máquinas Windows que usen WMF 4.0, ejecutando este cmdlet en una máquina donde esté presente PowerShellGet (WMF 5.0):

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

También para WMF 4.0, asegúrese de que la siguiente actualización esté instalada en las máquinas:

https://www.microsoft.com/download/details.aspx?id=40749

La siguiente configuración se puede insertar en máquinas Windows con WMF 5.0 o 4.0.

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

Si desea crear una instancia de su propio servidor de extracción de DSC dentro de la red corporativa para imitar las funcionalidades que puede obtener de DSC de Automatización de OMS, consulte la guía siguiente: https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396

## Opcional: Implementación de la configuración de DSC mediante una plantilla de Azure Resource Manager

Este artículo se ha centrado en cómo crear su propia configuración de DSC para implementar automáticamente Mobility Service y el agente de máquina virtual de Azure, y asegurarse de que se están ejecutando en las máquinas que quiere proteger. Como extra, también se dispone de una plantilla de Azure Resource Manager## ## que implementará esta configuración de DSC en una cuenta de Automatización de Azure nueva o existente y creará recursos de automatización que contendrán las variables para su entorno por medio de parámetros de entrada en la plantilla.

Una vez implementada, basta con consultar el paso 4 de esta guía para incorporar las máquinas.

La plantilla hará lo siguiente:

· Usará una cuenta de Automatización de OMS existente o creará una.

· Aceptará parámetros de entrada para:

	· ASRRemoteFile – the location where you have stored the Mobility Service setup
	· ASRPassphrase – the location where you have stored the passphrase.txt file
	· ASRCSEndpoint – the IP address of your Management server
	· Import xPSDesiredStateConfiguration PowerShell module
	· Create and compile the DSC configuration


Todos los pasos anteriores se realizarán en el orden correcto, por lo que puede comenzar fácilmente con la incorporación de sus máquinas para protegerlas.

La plantilla con instrucciones de implementación se encuentra en:

https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC

Implementación mediante PowerShell:

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

## Pasos siguientes:

Después de implementar los agentes de Mobility Service, puede proceder a [habilitar la replicación](site-recovery-vmware-to-azure.md#step-6-replicate-applications) para las máquinas virtuales.

<!---HONumber=AcomDC_0824_2016-->