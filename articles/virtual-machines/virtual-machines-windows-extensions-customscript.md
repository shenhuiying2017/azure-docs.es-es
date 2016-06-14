<properties
   pageTitle="Scripts personalizados en máquinas virtuales de Windows mediante el uso de plantillas | Microsoft Azure"
   description="Automatización de tareas de configuración de máquinas virtuales Windows mediante la extensión de script personalizado con plantillas de administrador de recursos"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Uso de la extensión de script personalizada para máquinas virtuales de Windows con plantillas de Azure Resource Manager

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Ejemplo de plantilla para una máquina virtual de Windows

Definición del siguiente recurso en la sección Recurso de la plantilla

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

En el ejemplo anterior, reemplace la dirección URL de archivo y el nombre de archivo con su propia configuración.

Una vez creada la plantilla, puede implementarla con Azure Powershell.

Consulte el ejemplo siguiente para obtener ejemplos completo de configuración de aplicaciones en una máquina virtual con una extensión de script personalizado.

* [Extensión de script personalizada en una máquina virtual de Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

<!---HONumber=AcomDC_0601_2016-->