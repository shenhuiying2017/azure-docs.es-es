<properties
   pageTitle="Información general de Configuración de estado deseado de Azure | Microsoft Azure"
   description="Información general para usar el controlador de extensiones de Microsoft Azure en la Configuración de estado deseado de PowerShell. Incluidos los requisitos previos, arquitectura, cmdlets, etc."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="zachal"/>

# Introducción al controlador de extensiones de configuración de estado deseado de Azure #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

El agente de máquina virtual de Azure y las extensiones asociadas forman parte de los servicios de infraestructura de Microsoft Azure. Las extensiones de máquina virtual son componentes de software que amplían la funcionalidad de la máquina virtual y simplifican varias operaciones de administración de máquinas virtuales; por ejemplo, la extensión VMAccess se puede utilizar para restablecer la contraseña de una máquina virtual, mientras que la extensión Script personalizado se puede utilizar para ejecutar un script en la máquina virtual.

Este artículo presenta la extensión de configuración de estado deseado (DSC) de PowerShell para las máquinas virtuales de Azure como parte del SDK de Azure PowerShell. Puede usar cmdlets nuevos para cargar y aplicar una configuración de DSC de PowerShell en una máquina virtual de Azure habilitada con la extensión DSC de PowerShell. La extensión DSC de PowerShell llamará al DSC de PowerShell para aplicar la configuración de DSC recibida en la máquina virtual. Esta funcionalidad también está disponible a través de la interfaz de usuario del Portal de vista previa.

## Requisitos previos ##
**Máquina local** Para interactuar con la extensión de máquina virtual de Azure, será necesario usar la interfaz de usuario del Portal de vista previa o el SDK de Azure PowerShell.

**Agente invitado** Será preciso que la máquina virtual de Azure afectada por la configuración del DSC tenga un SO que admita Windows Management Framework (WMF) 4.0 o 5.0. La lista completa de las versiones compatibles del SO puede encontrarse en el historial de versiones de la extensión DSC.

## Términos y conceptos ##
Esta guía presupone que está familiarizado con los conceptos siguientes:

Configuración: un documento de configuración de DSC.

Nodo: un destino para una configuración de DSC. En este documento, "nodo" siempre hará referencia a una máquina virtual de Azure.

Datos de configuración: un archivo .psd1 que contiene datos del entorno de una configuración

## Información general acerca de la arquitectura ##

La extensión DSC de Azure usa el marco del agente de Azure para entregar, aplicar e informar acerca de las configuraciones de DSC que se ejecutan en las máquinas virtuales de Azure. La extensión DSC espera un archivo .zip que contiene al menos un documento de configuración y un conjunto de parámetros proporcionados a través del SDK de Azure PowerShell o de la interfaz de usuario del portal.

La primera vez que se llama a la extensión, se ejecuta un proceso de instalación. Dicho proceso instala una versión de Windows Management Framework (WMF) como se define a continuación:

1. Si el SO de la máquina virtual de Azure es Windows Server 2016, no se realiza ninguna acción. Windows Server 2016 ya tiene instalada la versión más reciente de PowerShell.
2. Si está especificada la propiedad `wmfVersion`, se instala dicha versión de WMF, salvo que sea incompatible con el SO de la máquina virtual.
3. Si no hay ninguna propiedad `wmfVersion` especificada, se instala la versión más reciente aplicable de WMF.

La instalación de WMF requiere un reinicio. Después del reinicio, la extensión descarga el archivo zip especificado en la propiedad `modulesUrl`. Si esta ubicación se encuentra en el almacenamiento de blobs de Azure, se puede especificar un token de SAS en la propiedad `sasToken` para acceder al archivo. Después de que el archivo .zip se descarga y descomprime, se ejecuta la función de configuración definida en `configurationFunction` para generar el archivo .MOF. A continuación, la extensión ejecuta `Start-DscConfiguration -force` en el archivo MOF generado. La extensión de captura de salida y la escribe de nuevo en el canal de estado de Azure. A partir de ese momento, el LCM de DSC controla la supervisión y la corrección de la forma habitual.

## Cmdlets de PowerShell ##

Los cmdlets de PowerShell se pueden utilizar con ARM o ASM para empaquetar, publicar y supervisar las implementaciones de la extensión DSC. Los siguientes cmdlets enumerados son los módulos ASM, pero "Azure" se puede reemplazar por "AzureRM" para utilizar el modelo ARM. Por ejemplo, `Publish-AzureVMDscConfiguration` utilizará ASM, donde `Publish-AzureRmVMDscConfiguration` utilizará ARM.

`Publish-AzureVMDscConfiguration` tomará un archivo de configuración, lo examinará en busca de recursos de DSC dependientes y creará un archivo .zip que contiene la configuración y los recursos de DSC necesarios para aplicar la configuración. Puede crear el paquete localmente mediante el parámetro `-ConfigurationArchivePath`. En caso contrario, publicará el archivo .zip en el almacenamiento de blobs de Azure y lo protegerá con un token de SAS.

El archivo .zip que crea este cmdlet tiene el script de configuración. ps1 en la raíz de la carpeta de archivos. Los recursos tienen la carpeta del módulo colocada en la carpeta de archivos.

`Set-AzureVMDscExtension` inserta la configuración que necesita la extensión DSC de PowerShell en un objeto de configuración de máquina virtual, que posteriormente puede aplicarse a una máquina virtual con `Update-AzureVm`.

`Get-AzureVMDscExtension` recupera el estado de la extensión DSC de una máquina virtual concreta.

`Get-AzureVMDscExtensionStatus` recupera el estado de la configuración de DSC aprobada por el controlador de extensiones DSC en una máquina virtual o un grupo de máquinas virtuales.

`Remove-AzureVMDscExtension` quita el controlador de extensión de una máquina virtual dada. Esto **no** quita la configuración, ni desinstala WMF ni cambia la configuración aplicada en la máquina virtual. Solo quita el controlador de extensiones.

**Principales diferencias entre los cmdlets de ASM y ARM**

- Los cmdlets de ARM son sincrónicos. Los cmdlets de ASM son asincrónicos.
- ResourceGroupName, VMName, ArchiveStorageAccountName, Version y Location son parámetros nuevos necesarios.
- ArchiveResourceGroupName es un nuevo parámetro opcional de ARM. Se puede especificar cuando la cuenta de almacenamiento pertenezca a un grupo de recursos no sea el mismo en el que se crea la máquina virtual.
- ConfigurationArchive se llama ArchiveBlobName en ARM
- ContainerName se llama ArchiveContainerName en ARM
- StorageEndpointSuffix se llama ArchiveStorageEndpointSuffix in ARM
- El modificador – AutoUpdate se ha agregado a ARM para habilitar la actualización automática del controlador de extensiones a la versión más reciente cuando esté disponible, y tal como esté disponible. Tenga en cuenta que esto puede provocar que la máquina virtual se reinicie cuando se lance una nueva versión de WMF. 


## Funcionalidad del Portal de vista previa ##
Vaya a una máquina virtual. En Configuración -> Administrar haga clic en "extensiones". Se crea un nuevo panel. Haga clic en "Agregar" y seleccione DSC de PowerShell.

El portal necesitará que se introduzcan datos. **Script o módulos de configuración**: este campo es obligatorio. Requiere un archivo. ps1, que contenga un script de configuración o un archivo .zip con un script de configuración. ps1 en la raíz y todos los recursos dependientes en las carpetas de módulos dentro del zip. Se puede crear con el cmdlet `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` que se incluye en el SDK de Azure PowerShell. El archivo zip se cargará en el almacenamiento de blobs de usuario protegido por un token de SAS.

**Archivo PSD1 de datos de configuración**: este campo es opcional. Si la configuración requiere un archivo de datos de configuración en psd1, utilice este campo para seleccionarlo y cargarlo en el almacenamiento de blobs de usuario, donde estará protegido por un token de SAS.
 
**Nombre completo del módulo de configuración**: los archivos. ps1 pueden tener varias funciones de configuración. Escriba el nombre del script .ps1 de configuración seguido de un '' y del nombre de la función de configuración, por ejemplo

**Argumentos de configuración**: si la función de la configuración adopta argumentos, escríbalos aquí con el formato `argumentName1=value1,argumentName2=value2`. Tenga en cuenta que se trata de un formato distinto a cómo se aceptan argumentos de configuración a través de los cmdlets de PowerShell o las plantillas de ARM.

## Introducción ##

La extensión DSC de Azure toma documentos de configuración de DSC y los aplica a máquinas virtuales de Azure. A continuación encontrará un ejemplo sencillo de una configuración. Guárdelo localmente como "IisInstall.ps1":

```powershell
configuration IISInstall 
{ 
    node ("localhost") 
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

Los siguientes pasos colocarán el script IisInstall.ps1 en la máquina virtual especificada, ejecutarán la configuración y devolverán información acerca del estado.
 
```powershell
#Requires Azure Powershell cmdlets
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -vm $demoVM -ConfigurationArchive "demo.ps1.zip" -StorageContext $storageContext -ConfigurationName "runScript" -verbose

#Azure Powershell batches commands until an update-AzureVM command is given
$demoVM | Update-AzureVM -Verbose

#check on status
get-azurevmdscextensionstatus -VM $demovm -verbose
```

## Registro ##

Los registros se colocan en:

C:\\WindowsAzure\\Logs\\Plugins\\Microsoft.Powershell.DSC[número de versión]

## Pasos siguientes ##

Para más información acerca de la DSC de PowerShell, [visite el centro de documentación de PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).

Para buscar otras funcionalidades que se puedan administrar con la DSC de PowerShell, [examine la Galería de PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) para encontrar más recursos de DSC.

Para más información sobre cómo pasar parámetros confidenciales en configuraciones, consulte [Passing credentials to the Azure DSC extension handler](virtual-machines-windows-extensions-dsc-credentials.md) (Paso de credenciales al controlador de extensiones DSC de Azure).

<!---HONumber=AcomDC_0420_2016-->