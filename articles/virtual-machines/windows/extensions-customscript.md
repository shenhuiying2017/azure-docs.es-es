---
title: "Extensión de script personalizado de Azure para Windows | Microsoft Docs"
description: "Automatización de tareas de configuración de máquinas virtuales Windows mediante la extensión de script personalizado"
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/16/2017
ms.author: danis
ms.openlocfilehash: 9a8eae62d2dcb4c422b707909a27c84c7bf1aab3
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="custom-script-extension-for-windows"></a>Extensión de la secuencia de comandos personalizada para Windows

La extensión de script personalizado descarga y ejecuta scripts en máquinas virtuales de Azure. Esta extensión es útil para la configuración posterior a la implementación, la instalación de software o cualquier otra tarea de configuración o administración. Los scripts se pueden descargar desde Azure Storage o GitHub, o se pueden proporcionar a Azure Portal en el tiempo de ejecución de la extensión. La extensión de script personalizado se integra con las plantillas de Azure Resource Manager y también se puede ejecutar mediante la CLI de Azure, PowerShell, Azure Portal o la API de REST de máquina virtual de Azure.

En este documento se detalla cómo usar la extensión de script personalizado mediante el módulo de Azure PowerShell y plantillas de Azure Resource Manager, y se detallan también los pasos para solucionar problemas en los sistemas Windows.

## <a name="prerequisites"></a>Requisitos previos

> [!NOTE]  
> No use la extensión de script personalizado para ejecutar Update-AzureRmVM con la misma máquina virtual como su parámetro, ya tendrá que hacerlo ella misma.  
>   
> 

### <a name="operating-system"></a>Sistema operativo

La extensión de script personalizado para Windows se puede ejecutar en el cliente de Windows 10, Windows Server 2008 R2, 2012, 2012 R2 y 2016.

### <a name="script-location"></a>Ubicación del script

El script debe almacenarse en Azure Blob Storage o en cualquier otra ubicación a la que se pueda tener acceso a través de una dirección URL válida.

### <a name="internet-connectivity"></a>Conectividad de Internet

La extensión de script personalizado para Windows requiere que la máquina virtual de destino esté conectada a Internet. 

## <a name="extension-schema"></a>Esquema de extensión

El siguiente JSON muestra el esquema para la extensión de script personalizado. La extensión requiere una ubicación de script (Azure Storage u otra ubicación con dirección URL válida) y un comando para ejecutar. Si usa Azure Storage como origen del script, se requiere un nombre y una clave de cuenta de Azure Storage. Estos elementos se deben tratar como datos confidenciales y se deben especificar en la configuración protegida de las extensiones. Los datos de configuración protegida de la extensión de VM de Azure están cifrados y solo se descifran en la máquina virtual de destino.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

### <a name="property-values"></a>Valores de propiedad

| NOMBRE | Valor / ejemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Compute |
| Tipo | extensions |
| typeHandlerVersion | 1.9 |
| fileUris (p. ej.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute (p. ej.) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 |
| storageAccountName (p. ej.) | examplestorageacct |
| storageAccountKey (p. ej.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== |

**Nota**: Los nombres de propiedad distinguen entre mayúsculas y minúsculas. Use los nombres tal y como se ha mostrado anteriormente para evitar problemas de implementación.

## <a name="template-deployment"></a>Implementación de plantilla

Las extensiones de VM de Azure pueden implementarse con plantillas de Azure Resource Manager. El esquema JSON detallado en la sección anterior se puede usar en una plantilla de Azure Resource Manager para ejecutar la extensión de script personalizado durante la implementación de una plantilla de Azure Resource Manager. Aquí ([GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)) puede encontrar una plantilla de ejemplo que incluye la extensión de script personalizado.

## <a name="powershell-deployment"></a>Implementación de PowerShell

El comando `Set-AzureRmVMCustomScriptExtension` se puede usar para agregar la extensión de script personalizado a una máquina virtual existente. Para más información, consulte [Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="troubleshoot-and-support"></a>Solución de problemas y asistencia

### <a name="troubleshoot"></a>Solución de problemas

Los datos sobre el estado de las implementaciones de extensiones pueden recuperarse desde Azure Portal y mediante el módulo Azure PowerShell. Para ver el estado de implementación de las extensiones de una VM determinada, ejecute el comando siguiente.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

La salida de la ejecución de las extensiones se registra en los archivos que se encuentran en el siguiente directorio de la máquina virtual de destino.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Los archivos especificados se descargan en el siguiente directorio de la máquina virtual de destino.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
donde `<n>` es un entero decimal que puede variar entre las ejecuciones de la extensión.  El valor `1.*` coincide con el valor `typeHandlerVersion` actual y real de la extensión.  Por ejemplo, el directorio real podría ser `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Cuando se ejecute el comando `commandToExecute`, la extensión tendrá establecido este directorio (por ejemplo, `...\Downloads\2`) como directorio de trabajo actual. Esto permite el uso de rutas de acceso relativas para buscar los archivos descargados a través de la propiedad `fileURIs`. En la tabla siguiente se muestran algunos ejemplos.

Dado que la ruta de acceso absoluta de descarga puede variar con el paso del tiempo, es mejor optar por rutas de acceso relativas de archivo o script en la cadena `commandToExecute`, siempre que sea posible. Por ejemplo: 
```json
    "commandToExecute": "powershell.exe . . . -File './scripts/myscript.ps1'"
```

La información de ruta de acceso después del primer segmento de URI se conserva para los archivos descargados a través de la lista de propiedades `fileUris`.  Como se muestra en la tabla siguiente, los archivos descargados se asignan en los subdirectorios de descarga para reflejar la estructura de los valores `fileUris`.  

#### <a name="examples-of-downloaded-files"></a>Ejemplos de archivos descargados

| URI en fileUris | Ubicación descargada relativa | Ubicación descargada absoluta * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\*Como anteriormente, las rutas de acceso absolutas al directorio cambiarán a lo largo del ciclo de vida de la VM, pero no dentro de una ejecución única de la extensión CustomScript.

### <a name="support"></a>Soporte técnico

Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en los [foros de MSDN Azure y Stack Overflow] (https://azure.microsoft.com/es-es/support/forums/). Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/en-us/support/options/) y seleccione Obtener soporte. Para obtener información sobre el uso del soporte técnico, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).
