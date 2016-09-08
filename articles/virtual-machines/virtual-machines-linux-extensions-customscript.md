<properties
   pageTitle="Scripts personalizados en máquinas virtuales de Linux mediante el uso de plantillas | Microsoft Azure"
   description="Automatizar tareas de configuración de máquinas virtuales de Linux mediante la extensión de script personalizado con plantillas de administrador de recursos"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Uso de la extensión de script personalizada para máquinas virtuales de Linux con plantillas de Azure Resource Manager

En este artículo se proporciona información general sobre la escritura de plantillas del Administrador de recursos de Azure con la extensión de scripts personalizados para cargas de trabajo de arranque en una máquina virtual de Linux.

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Ejemplo de plantilla para una máquina virtual de Linux

Definición del siguiente recurso de extensión en la sección Recurso de la plantilla

      {
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "MyCustomScriptExtension",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
    "properties":
    {
      "publisher": "Microsoft.OSTCExtensions",
      "type": "CustomScriptForLinux",
      "typeHandlerVersion": "1.2",
      "autoUpgradeMinorVersion": true "settings": { 
      "fileUris": [ "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-ubuntu/mongo-install-ubuntu.sh" ], 
      "commandToExecute": "shmongo-install-ubuntu.sh"
     }, "protectedSettings": {}
      } 
      }

En el ejemplo anterior, reemplace la dirección URL de archivo y el nombre de archivo con su propia configuración.

Una vez creada la plantilla, puede implementarla con la CLI de Azure.

Consulte el ejemplo siguiente para obtener un ejemplo completo de configuración de aplicaciones en una máquina virtual con una extensión de script personalizado.

* [Extensión del script personalizado en una máquina virtual de Linux.](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

<!----HONumber=AcomDC_0824_2016-->