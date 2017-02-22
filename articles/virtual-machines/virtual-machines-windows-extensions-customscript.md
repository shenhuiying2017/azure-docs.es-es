---
title: "Extensión de script personalizado de Azure para Windows | Microsoft Docs"
description: "Automatización de tareas de configuración de máquinas virtuales Windows mediante la extensión de script personalizado"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: f9ea0d14a99a7881b816606058e5ad72a0fef499
ms.openlocfilehash: 01adbd43c5c77c3a80c5141e06a2ab14a49b8454


---
# <a name="custom-script-extension-for-windows"></a>Extensión de la secuencia de comandos personalizada para Windows

La extensión de script personalizado descarga y ejecuta scripts en máquinas virtuales de Azure. Esta extensión es útil para la configuración posterior a la implementación, la instalación de software o cualquier otra tarea de configuración o administración. Los scripts se pueden descargar desde Azure Storage o GitHub, o se pueden proporcionar a Azure Portal en el tiempo de ejecución de la extensión. La extensión de script personalizado se integra con las plantillas de Azure Resource Manager y también se puede ejecutar mediante la CLI de Azure, PowerShell, Azure Portal o la API de REST de máquina virtual de Azure.

En este documento se detalla cómo usar la extensión de script personalizado mediante el módulo de Azure PowerShell y plantillas de Azure Resource Manager, y se detallan también los pasos para solucionar problemas en los sistemas Windows.

## <a name="prerequisites"></a>Requisitos previos

### <a name="operating-system"></a>Sistema operativo

La extensión de script personalizado para Windows se puede ejecutar en Windows Server 2008 R2, 2012, 2012 R2 y 2016.

### <a name="script-location"></a>Ubicación del script

El script debe almacenarse en Azure Storage o en cualquier otra ubicación a la que se pueda tener acceso a través de una dirección URL válida.

### <a name="internet-connectivity"></a>Conectividad de Internet

La extensión de script personalizado para Windows requiere que la máquina virtual de destino esté conectada a Internet. 

## <a name="extension-schema"></a>Esquema de extensión

El siguiente JSON muestra el esquema para la extensión de script personalizado. La extensión requiere una ubicación de script (Azure Storage u otra ubicación con dirección URL válida) y un comando para ejecutar. Si usa Azure Storage como origen del script, se requiere un nombre y una clave de cuenta de Azure Storage. Estos elementos se deben tratar como datos confidenciales y se deben especificar en la configuración protegida de las extensiones. Los datos de configuración protegida de la extensión de VM de Azure están cifrados y solo se descifran en la máquina virtual de destino.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
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
        "typeHandlerVersion": "1.8",
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

| Nombre | Valor / ejemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Compute |
| type | extensions |
| typeHandlerVersion | 1.8 |
| fileUris (p. ej.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute (p. ej.) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 |
| storageAccountName (p. ej.) | examplestorageacct |
| storageAccountKey (p. ej.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== |

## <a name="template-deployment"></a>Implementación de plantilla

Las extensiones de VM de Azure pueden implementarse con plantillas de Azure Resource Manager. El esquema JSON detallado en la sección anterior se puede usar en una plantilla de Azure Resource Manager para ejecutar la extensión de script personalizado durante la implementación de una plantilla de Azure Resource Manager. Aquí ([GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)) puede encontrar una plantilla de ejemplo que incluye la extensión de script personalizado.

## <a name="powershell-deployment"></a>Implementación de PowerShell

El comando `Set-AzureRmVMCustomScriptExtension` se puede usar para agregar la extensión de script personalizado a una máquina virtual existente. Para más información, consulte [Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
-VMName myVM `
-Location myLocation `
-FileUri myURL `
-Run 'myScript.ps1' `
-Name DemoScriptExtension
```

## <a name="troubleshoot-and-support"></a>Solución de problemas y soporte técnico

### <a name="troubleshoot"></a>Solución de problemas

Los datos sobre el estado de las implementaciones de extensiones pueden recuperarse desde Azure Portal y mediante el módulo Azure PowerShell. Para ver el estado de implementación de las extensiones de una VM determinada, ejecute el comando siguiente.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

El resultado de la ejecución de las extensiones se registra en los archivos que se encuentran en el siguiente directorio de la máquina virtual de destino.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

El propio script se descarga en el siguiente directorio de la máquina virtual de destino.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads
```

### <a name="support"></a>Soporte técnico

Si necesita más ayuda con cualquier aspecto de este artículo, puede ponerse en contacto con los expertos de Azure en los [foros de MSDN Azure o Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/en-us/support/options/) y seleccione Obtener soporte. Para obtener información sobre el uso del soporte técnico, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).





<!--HONumber=Jan17_HO3-->


