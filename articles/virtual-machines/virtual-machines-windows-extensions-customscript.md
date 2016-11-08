---
title: Scripts personalizados en máquinas virtuales de Windows mediante el uso de plantillas | Microsoft Docs
description: Automatización de tareas de configuración de máquinas virtuales Windows mediante la extensión de script personalizado con plantillas de administrador de recursos
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap

---
# Uso de las extensiones de script personalizadas para máquinas virtuales Windows con plantillas de Azure Resource Manager
[!INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Ejemplo de plantilla para una máquina virtual de Windows
Defina el siguiente recurso en la sección Recurso de la plantilla.

       {
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "MyCustomScriptExtension",
       "apiVersion": "2015-05-01-preview",
       "location": "[parameters('location')]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.Compute",
           "type": "CustomScriptExtension",
           "typeHandlerVersion": "1.7",
           "autoUpgradeMinorVersion":true,
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }

En el ejemplo anterior, reemplace la dirección URL de archivo y el nombre de archivo por sus propios valores. Una vez creada la plantilla, puede implementarla con Azure Powershell.

Si desea mantener de forma privada las direcciones URL y los parámetros del script, puede establecer la dirección URL del script como **privada**. Si la dirección URL del script está establecida como **privada**, solo se puede acceder a ella con un nombre de cuenta de almacenamiento y una clave, enviados como configuración protegida. También se pueden proporcionar los parámetros del script como configuración protegida con la versión 1.7 o posterior de la extensión de script personalizado.

## Ejemplo de plantilla para una máquina virtual Windows con configuración protegida
        {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "settings": {
        "fileUris": [
        "http: //Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
        ]
        },
        "protectedSettings": {
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -start.ps1",
        "storageAccountName": "yourStorageAccountName",
        "storageAccountKey": "yourStorageAccountKey"
        }
        }
Para más información acerca del esquema de las versiones más recientes de la extensión de script personalizado, consulte [Ejemplos de configuración de la extensión de máquina virtual Windows de Azure](virtual-machines-windows-extensions-configuration-samples.md).

Para obtener ejemplos de configuración de aplicación en una máquina virtual con la extensión de script personalizado, consulte la [extensión de script personalizado en una máquina virtual Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/).

<!---HONumber=AcomDC_0824_2016-->