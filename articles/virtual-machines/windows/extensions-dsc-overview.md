---
title: "Introducción a Desired State Configuration de Azure | Microsoft Docs"
description: "Aprenda a usar el controlador de extensiones de Microsoft Azure para Desired State Configuration (DSC) de PowerShell. El artículo incluye los requisitos previos, la arquitectura y los cmdlets."
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: 14d29223435e9a133b112a61f2ecdde0aad581a2
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introducción al controlador de extensiones de configuración de estado deseado de Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

El agente de VM de Azure y las extensiones asociadas forman parte de los servicios de infraestructura de Microsoft Azure. Las extensiones de VM son componentes de software que amplían la funcionalidad de la VM y simplifican diversas operaciones de administración de VM.

El caso de uso principal de la extensión Desired State Configuration (DSC) de Azure es arrancar una máquina virtual en el [servicio DSC de Azure Automation](../../automation/automation-dsc-overview.md). El arranque de una VM proporciona [ventajas](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig#pull-service) que incluyen la administración continua de la configuración de VM y la integración con otras herramientas operativas, como Supervisión de Azure.

Puede utilizar la extensión de DSC independientemente del servicio de DSC de Automatización. Sin embargo, se trata de una acción singular que se produce durante la implementación. No hay ningún informe en curso ni administración de configuración disponible, excepto localmente en la VM.

Este artículo proporciona información sobre ambos escenarios de uso de la extensión DSC: para la incorporación de Automation y como herramienta para asignar configuraciones a las VM mediante el SDK de Azure.

## <a name="prerequisites"></a>requisitos previos

- **Máquina local**: para interactuar con la extensión de VM de Azure, debe usar Azure Portal o el SDK de Azure PowerShell.
- **Agente invitado**: la VM de Azure que cuenta con la configuración de DSC debe tener un SO que admita Windows Management Framework (WMF) 4.0 o posterior. La lista completa de las versiones compatibles del SO puede encontrarse en [DSC extension version history](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/) (Historial de versiones de la extensión DSC).

## <a name="terms-and-concepts"></a>Términos y conceptos

Esta guía asume que está familiarizado con los conceptos siguientes:

- **Configuración**: un documento de configuración de DSC.
- **Nodo**: un destino para una configuración de DSC. En este documento, *nodo* siempre hace referencia a una VM de Azure.
- **Datos de configuración**: un archivo .psd1 que tiene datos del entorno de una configuración.

## <a name="architecture"></a>Architecture

La extensión DSC de Azure usa el marco del agente de máquina virtual de Azure para entregar y aplicar las configuraciones de DSC que se ejecutan en las máquinas virtuales de Azure, así como informar sobre estas. La extensión DSC acepta un documento de configuración y un conjunto de parámetros. Si no se proporciona ningún archivo, se inserta un [script de configuración predeterminada](#default-configuration-script) con la extensión. El script de configuración predeterminada se usa únicamente para establecer metadatos en el [administrador de configuración Local](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).

Cuando se llama a la extensión por primera vez, se instala una versión de (WMF) usando la siguiente lógica:

* Si el SO de la máquina virtual de Azure es Windows Server 2016, no se realiza ninguna acción. Windows Server 2016 ya tiene instalada la versión más reciente de PowerShell.
* Si está especificada la propiedad **wmfVersion**, se instala esta versión de WMF, salvo que dicha versión sea incompatible con el SO de la máquina virtual.
* Si no hay ninguna propiedad **wmfVersion** especificada, se instala la versión aplicable más reciente de WMF.

La instalación de WMF requiere un reinicio. Después del reinicio, la extensión descarga el archivo .zip que se especifica en la propiedad **modulesUrl**, si se proporciona. Si esta ubicación se encuentra en Azure Blob Storage, se puede especificar un token de SAS en la propiedad **sasToken** para acceder al archivo. Después de que el archivo .zip se descargue y descomprima, se ejecuta la función de configuración definida en **configurationFunction** para generar un archivo .mof. Después, se ejecuta la extensión **Start-DscConfiguration -Force** mediante el archivo .mof generado. La extensión captura la salida y la escribe en el canal de estado de Azure.

### <a name="default-configuration-script"></a>Script de configuración predeterminada

La extensión DSC de Azure incluye un script de configuración predeterminada diseñado para utilizarse al incorporar una VM al servicio DSC de Azure Automation. Los parámetros del script se alinean con las propiedades configurables del [administrador de configuración local](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig). Para los parámetros de script, consulte [Script de configuración predeterminada](extensions-dsc-template.md#default-configuration-script) en [Extensión Desired State Configuration con plantillas de Azure Resource Manager](extensions-dsc-template.md). Para el script completo, consulte la [plantilla de inicio rápido de Azure en GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-resource-manager-templates"></a>Extensión DSC en plantillas de Resource Manager

En la mayoría de los escenarios, las plantillas de implementación de Resource Manager son la manera esperada de trabajar con la extensión DSC. Para más información y ejemplos sobre cómo incluir la extensión DSC en las plantillas de implementación de Resource Manager, vea [Extensión Desired State Configuration con plantillas de Azure Resource Manager](extensions-dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Cmdlets de PowerShell de la extensión DSC

Los cmdlets de PowerShell que se usan para administrar la extensión DSC son útiles para solucionar problemas interactivos y escenarios de recopilación de información. Puede usar los cmdlets para empaquetar, publicar y supervisar las implementaciones de la extensión DSC. Tenga en cuenta que los cmdlets para la extensión DSC aún no se han actualizado para trabajar con el [script de configuración predeterminada](#default-configuration-script).

El cmdlet **Publish-AzureRMVMDscConfiguration** toma en un archivo de configuración, busca los recursos de DSC dependientes y luego crea un archivo zip. El archivo zip contiene la configuración y recursos de DSC que son necesarios para aplicar la configuración. El cmdlet también puede crear el paquete localmente mediante el parámetro *-ConfigurationArchivePath*. En caso contrario, el cmdlet publica el archivo .zip en Blob Storage y lo protege con un token de SAS.

El script de configuración .ps1 que el cmdlet crea se encuentra en el archivo .zip en la raíz de la carpeta de archivos. La carpeta del módulo se encuentra en la carpeta de archivos en los recursos.

El cmdlet **Set-AzureRMVMDscExtension** inserta la configuración que la extensión DSC de PowerShell necesita en un objeto de configuración de VM.

El cmdlet **Get-AzureRMVMDscExtension** recupera el estado de la extensión DSC de una VM específica.

El cmdlet **Get-AzureRMVMDscExtensionStatus** recupera el estado de la configuración de DSC que el controlador de la extensión DSC aplica. Esta acción puede realizarse en una sola VM o en un grupo de VM.

El cmdlet **Remove-AzureRMVMDscExtension** quita el controlador de extensiones de una VM específica. Este cmdlet *no* quita la configuración, ni desinstala WMF ni cambia la configuración aplicada en la VM. Solo quita el controlador de extensiones. 

Información importante acerca de los cmdlets de la extensión DSC de Resource Manager:

- Los cmdlets de Azure Resource Manager son sincrónicos.
- *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *Version* y *Location* son todos parámetros obligatorios.
- *ArchiveResourceGroupName* es un parámetro opcional. Se puede especificar este parámetro cuando la cuenta de almacenamiento pertenezca a un grupo de recursos que no sea el mismo que en el que se crea la VM.
- Use el modificador **AutoUpdate** para actualizar automáticamente el controlador de extensiones a la versión más reciente cuando esté disponible. Tenga en cuenta que este parámetro puede provocar que la VM se reinicie cuando se lance una nueva versión de WMF.

### <a name="get-started-with-cmdlets"></a>Introducción a los cmdlets

La extensión DSC de Azure puede usar documentos de configuración de DSC para configurar directamente las VM de Azure durante la implementación. Este paso no registra el nodo en Automation. El nodo *no* se administrada centralmente.

En el ejemplo siguiente se muestra un ejemplo sencillo de una configuración. Guarde la configuración de forma local como IisInstall.ps1.

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

Los siguientes comandos colocan el script IisInstall.ps1 en la VM especificada. Los comandos también ejecutan la configuración y luego informan sobre el estado.

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script to user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Funcionalidad del Portal de Azure

Para configurar DSC en el portal:

1. Vaya a la VM. 
2. En **Configuración** > **General**, seleccione **Extensiones**. 
3. En el panel nuevo que se creó, seleccione **Agregar** y luego seleccione **DSC de PowerShell**.

El portal requiere la siguiente información:

* **Módulos o script de configuración**: este campo es obligatorio (el formulario no se ha actualizado para el [script de configuración predeterminada](#default-configuration-script)). Los scripts y módulos de configuración requieren un archivo. ps1 que tiene un script de configuración o un archivo .zip con un script de configuración. ps1 en la raíz. Si utiliza un archivo .zip, todos los recursos dependientes se deben incluir en las carpetas de módulo en el archivo .zip. Puede crear el archivo .zip mediante el cmdlet **Publish-AzureVMDscConfiguration -ConfigurationArchivePath** que se incluye en el SDK de Azure PowerShell. El archivo .zip se carga en el almacenamiento de blob de usuario y se protege mediante un token de SAS.

* **Archivo PSD1 de datos de configuración**: este campo es opcional. Si la configuración requiere un archivo de datos de configuración en .psd1, utilice este campo para seleccionar el campo de datos y cargarlo en el almacenamiento de blobs de usuario. El archivo de datos de configuración se protege mediante un token SAS en el almacenamiento de blobs.

* **Nombre completo del módulo de configuración**: puede incluir varias funciones de configuración en un archivo .ps1. Escriba el nombre del script .ps1 de configuración seguido de \\ y el nombre de la función de configuración. Por ejemplo, si el script .ps1 tiene el nombre configuration.ps1 y la configuración es **IisInstall**, escriba **configuration.ps1\IisInstall**.

* **Argumentos de configuración**: si la función de configuración adopta argumentos, escríbalos aquí con el formato **argumentName1=value1,argumentName2=value2**. Este formato es un formato distinto en el que se aceptan argumentos de configuración en cmdlets de PowerShell o las plantillas de Resource Manager.

## <a name="logs"></a>Registros
Coloque registros en esta ubicación:

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>
```

## <a name="next-steps"></a>pasos siguientes
* Para obtener más información sobre DSC de PowerShell, vaya al [centro de documentación de PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 
* Examine la [plantilla de Resource Manager para la extensión DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* Para obtener más funcionalidades que pueda administrar con DSC de PowerShell y más recursos de DSC, examine la [Galería de PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
* Para detalles sobre cómo pasar parámetros confidenciales a configuraciones, consulte [Pasar credenciales al controlador de extensiones DSC de Azure](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

