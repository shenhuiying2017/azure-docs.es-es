---
title: "Introducción a la configuración de estado deseado de Azure | Microsoft Docs"
description: "Información general para usar el controlador de extensiones de Microsoft Azure en la Configuración de estado deseado de PowerShell. Incluidos los requisitos previos, arquitectura, cmdlets, etc."
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
ms.openlocfilehash: ed8b5bc54baa3a5abfb596b202f0af58e1b6c74f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introducción al controlador de extensiones de configuración de estado deseado de Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

El agente de máquina virtual de Azure y las extensiones asociadas forman parte de los servicios de infraestructura de Microsoft Azure.
Las extensiones de máquina virtual son componentes de software que amplían la funcionalidad de la máquina virtual y simplifican diversas operaciones de administración de máquinas virtuales.

El caso de uso principal de la extensión Desired State Configuration es arrancar una máquina virtual en el [servicio DSC de Azure Automation](../../automation/automation-dsc-overview.md) que proporcione [ventajas](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig.md#pull-service), como la administración continua de la configuración de la máquina virtual y la integración en otras herramientas operativas, como Supervisión de Azure.

También es posible utilizar la extensión DSC por separado del servicio DSC de Azure Automation; sin embargo, se trata de una acción singular que se produce durante la implementación y no hay ningún informe en curso ni administración de la configuración, excepto localmente dentro de la máquina virtual.

Este artículo contiene información para ambos escenarios, la extensión DSC para la incorporación de Azure Automation y cómo utilizar la extensión DSC como herramienta para asignar configuraciones a las máquinas virtuales mediante Azure SDK.

## <a name="prerequisites"></a>requisitos previos

- **Máquina local**: para interactuar con la extensión de máquina virtual de Azure, será necesario usar Azure Portal o el SDK de Azure PowerShell.
- **Agente invitado**: es preciso que la máquina virtual de Azure que cuenta con la configuración de DSC tenga un SO que admita Windows Management Framework (WMF) 4.0 o posterior. La lista completa de las versiones compatibles del SO puede encontrarse en la página [Release history for the Azure DSC Extension](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/) (Historial de versiones de la extensión DSC de Azure).

## <a name="terms-and-concepts"></a>Términos y conceptos

Esta guía presupone que está familiarizado con los conceptos siguientes:

- **Configuración**: un documento de configuración de DSC.
- **Nodo**: un destino para una configuración de DSC. En este documento, "nodo" siempre hace referencia a una máquina virtual de Azure.
- **Datos de configuración**: un archivo .psd1 que contiene datos del entorno de una configuración.

## <a name="architectural-overview"></a>Información general acerca de la arquitectura

La extensión DSC de Azure usa el marco del agente de máquina virtual de Azure para entregar y aplicar las configuraciones de DSC que se ejecutan en las máquinas virtuales de Azure, así como informar sobre estas.
La extensión DSC acepta un documento de configuración y un conjunto de parámetros.
Si no se proporciona ningún archivo, se inserta un [script de configuración predeterminada](###default-configuration-script) con la extensión que se usa solo para establecer metadatos en el [administrador de configuración local](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).

Cuando se llama a la extensión por primera vez, se instala una versión de Windows Management Framework (WMF) usando la siguiente lógica:

1. Si el SO de la máquina virtual de Azure es Windows Server 2016, no se realiza ninguna acción. Windows Server 2016 ya tiene instalada la versión más reciente de PowerShell.
2. Si está especificada la propiedad `wmfVersion` , se instala esta versión de WMF, salvo que sea incompatible con el SO de la máquina virtual.
3. Si no hay ninguna propiedad `wmfVersion` especificada, se instala la versión aplicable más reciente de WMF.

La instalación de WMF requiere un reinicio.
Después del reinicio, la extensión descarga el archivo .zip especificado en la propiedad `modulesUrl`, si se proporciona.
Si esta ubicación se encuentra en Almacenamiento de blobs de Azure, se puede especificar un token de SAS en la propiedad `sasToken` para acceder al archivo.
Después de que el archivo .zip se descargue y descomprima, se ejecuta la función de configuración definida en `configurationFunction` para generar un archivo MOF.
Luego, la extensión ejecuta `Start-DscConfiguration -Force` con el archivo MOF generado.
La extensión captura la salida y la escribe en el canal de estado de Azure.

### <a name="default-configuration-script"></a>Script de configuración predeterminada

La extensión DSC de Azure incluye un script de configuración predeterminada diseñado para utilizarse al incorporar una máquina virtual al servicio DSC de Azure Automation.
Los parámetros del script se alinean con las propiedades configurables del [administrador de configuración local](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).
Los parámetros del script están [documentados](extensions-dsc-template.md##default-configuration-script) y el script completo está disponible en [GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-arm-templates"></a>Extensión DSC en las plantillas de ARM

Las plantillas de implementación de Azure Resource Manager (ARM) son la manera esperada de trabajar con la extensión DSC para la mayoría de los escenarios.
Para obtener información y ejemplos sobre la inclusión de la extensión DSC en las plantillas de implementación de ARM, vea la página de documentación específica [Desired State Configuration Extension with Azure Resource Manager templates](extensions-dsc-template.md) (Extensión de configuración de estado deseado con plantillas de Azure Resource Manager).

## <a name="dsc-extension-powershell-cmdlets"></a>Cmdlets de PowerShell de la extensión DSC

Los cmdlets de PowerShell para administrar la extensión DSC son útiles para solucionar problemas interactivos y escenarios de recopilación de información.
Los cmdlets pueden utilizarse para empaquetar, publicar y supervisar las implementaciones de la extensión DSC.
Tenga en cuenta que los cmdlets para la extensión DSC aún no se han actualizado para trabajar con el [script de configuración predeterminada](###default-configuration-script).

`Publish-AzureRMVMDscConfiguration` toma un archivo de configuración, lo examina en busca de recursos de DSC dependientes y crea un archivo .zip que contiene la configuración y los recursos de DSC necesarios para aplicar la configuración.
Puede crear también el paquete localmente mediante el parámetro `-ConfigurationArchivePath` .
En caso contrario, publica el archivo .zip en Azure Blob Storage y lo protege con un token de SAS.

El archivo .zip que crea este cmdlet tiene el script de configuración. ps1 en la raíz de la carpeta de archivos.
Los recursos tienen la carpeta del módulo colocada en la carpeta de archivos.

`Set-AzureRMVMDscExtension` inserta la configuración que necesita la extensión DSC de PowerShell en un objeto de configuración de máquina virtual.

`Get-AzureRMVMDscExtension` recupera el estado de la extensión DSC de una máquina virtual concreta.

`Get-AzureRMVMDscExtensionStatus` recupera el estado de la configuración de DSC aplicada por el controlador de extensiones DSC.
Esta acción puede realizarse en una sola máquina virtual o en un grupo de máquinas virtuales.

`Remove-AzureRMVMDscExtension` quita el controlador de extensiones de una máquina virtual dada.
Este cmdlet **no** quita la configuración, ni desinstala WMF ni cambia la configuración aplicada en la máquina virtual.
Solo quita el controlador de extensiones. 

Información importante sobre los cmdlets de la extensión DSC de AzureRM:

- Los cmdlets de Azure Resource Manager son sincrónicos.
- ResourceGroupName, VMName, ArchiveStorageAccountName, Version y Location son todos parámetros obligatorios.
- ArchiveResourceGroupName es un parámetro opcional. Se puede especificar este parámetro cuando la cuenta de almacenamiento pertenezca a un grupo de recursos que no sea el mismo que en el que se crea la máquina virtual.
- El modificador AutoUpdate permite la actualización automática del controlador de extensiones a la versión más reciente cuando esté disponible, y tal como esté disponible. Tenga en cuenta que este parámetro puede provocar que la máquina virtual se reinicie cuando se lance una nueva versión de WMF.

### <a name="getting-started-with-cmdlets"></a>Introducción a los cmdlets

La extensión DSC de Azure es capaz de usar documentos de configuración de DSC directamente para configurar máquinas virtuales de Azure durante la implementación, aunque esto no registrará el nodo en Azure Automation, de modo que el nodo **NO* se administrará de forma centralizada.

A continuación encontrará un ejemplo sencillo de una configuración.
Guárdelo localmente como "IisInstall.ps1":

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

Los siguientes pasos colocan el script IisInstall.ps1 en la máquina virtual especificada, ejecutan la configuración y devuelven información acerca del estado.

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Funcionalidad de Azure Portal

Vaya a una máquina virtual. En Configuración -> General, haga clic en "Extensiones".
Se crea un nuevo panel.
Haga clic en "Agregar" y seleccione DSC de PowerShell.

El portal necesita una entrada.
**Módulos o script de configuración**: este campo es obligatorio (el formulario no se ha actualizado para el [script de configuración predeterminada](###default-configuration-script)).
Requiere un archivo .ps1 que contenga un script de configuración o un archivo .zip con un script de configuración .ps1 en la raíz y todos los recursos dependientes en las carpetas de módulos dentro del archivo .zip.
Se puede crear con el cmdlet `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` que se incluye en el SDK de Azure PowerShell.
El archivo .zip se carga en el almacenamiento de blobs de usuario protegido por un token de SAS.

**Archivo PSD1 de datos de configuración**: este campo es opcional.
Si la configuración requiere un archivo de datos de configuración en .psd1, utilice este campo para seleccionarlo y cargarlo en el almacenamiento de blobs de usuario, donde está protegido por un token de SAS.

**Nombre completo del módulo de configuración**: los archivos .ps1 pueden tener varias funciones de configuración.
Escriba el nombre del script .ps1 de configuración seguido de un '\'' y el nombre de la función de configuración.
Por ejemplo, si su script .ps1 tiene el nombre "configuration.ps1" y la configuración es "IisInstall", escriba: `configuration.ps1\IisInstall`

**Argumentos de configuración**: si la función de configuración adopta argumentos, escríbalos aquí con el formato `argumentName1=value1,argumentName2=value2`.
Tenga en cuenta que se trata de un formato distinto a cómo se aceptan argumentos de configuración a través de los cmdlets de PowerShell o las plantillas de Resource Manager.

## <a name="logging"></a>Registro
Los registros se colocan en:

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\[Version Number]
```

## <a name="next-steps"></a>pasos siguientes
Para más información sobre DSC de PowerShell, [visite el centro de documentación de PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Examine la [plantilla de Azure Resource Manager para la extensión de DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para buscar otras funcionalidades que se puedan administrar con DSC de PowerShell, [examine la Galería de PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) para encontrar más recursos de DSC.

Para más información sobre cómo pasar parámetros confidenciales a configuraciones, consulte [Cómo pasar las credenciales al controlador de extensiones de la DSC de Azure](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
