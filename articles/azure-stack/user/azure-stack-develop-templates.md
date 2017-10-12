---
title: Desarrollo de plantillas para Azure Stack | Microsoft Docs
description: "Información sobre las prácticas recomendadas de plantillas de Azure Stack"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: ffad7bfd4ffcd9159dea23b70640f0ee761fbae0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-resource-manager-template-considerations"></a>Consideraciones de la plantilla de Azure Resource Manager

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Al desarrollar la aplicación, es importante garantizar la portabilidad de las plantillas entre Azure y Azure Stack.  Este tema proporciona consideraciones para el desarrollo de [plantillas](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) de Azure Resource Manager, por lo que puede crear un prototipo de la implementación de prueba y la aplicación en Azure sin acceder a un entorno de Azure Stack.

## <a name="public-namespaces"></a>Espacios de nombres públicos
Como Azure Slack se hospeda en el centro de datos, tiene espacios de nombres de punto de conexión de servicio diferentes a la nube pública de Azure. Como resultado, se producirá un error en los puntos de conexión públicos codificados de forma rígida de las plantillas de Resource Manager al tratar de implementarlos en Azure Stack. En su lugar, puede usar las funciones *reference* y *concatenate* para crear dinámicamente el punto de conexión de servicio en función de los valores recuperados desde el proveedor de recursos durante la implementación. Por ejemplo, en lugar de especificar *blob.core.windows.net* en la plantilla, recupere[primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-simple-windows-vm/azuredeploy.json#L201) para establecer dinámicamente el punto de conexión *osDisk.URI*:

     "osDisk": {"name": "osdisk","vhd": {"uri": 
     "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
      '/',variables('OSDiskName'),'.vhd')]"}}

## <a name="api-versioning"></a>Control de versiones de la API
Las versiones de los servicios de Azure pueden diferir entre Azure y Azure Stack. Cada recurso requiere el atributo apiVersion, que define las funciones que ofrece. Para garantizar la compatibilidad de la versión de la API en Azure Stack, estas son versiones de API válidas para cada proveedor de recursos:

| Proveedor de recursos | apiVersion |
| --- | --- |
| Proceso |`'2015-06-15'` |
| Red |`'2015-06-15'`, `'2015-05-01-preview'` |
| Storage |`'2016-01-01'`, `'2015-06-15'`, `'2015-05-01-preview'` |
| KeyVault | `'2015-06-01'` |
| App Service |`'2015-08-01'` |
| MySQL |`'2015-09-01'` |
| SQL |`'2014-04-01-preview'` |

## <a name="template-functions"></a>Funciones de plantillas
Las [funciones](../../azure-resource-manager/resource-group-template-functions.md) de Resource Manager proporcionan las funcionalidades necesarias para crear plantillas dinámicas. Por ejemplo, puede utilizar funciones para tareas como las siguientes:

* Concatenar o recortar cadenas 
* Valores de referencia de otros recursos
* Efectuar una iteración en los recursos para implementar varias instancias 

Al compilar las plantillas, algunas funciones no están disponibles en el kit de desarrollo de Azure Stack y no deben usarse. Estas funciones son las siguientes:

* Skip
* Take

## <a name="resource-location"></a>Ubicación del recurso
Las plantillas de Resource Manager usan un atributo location para incluir recursos durante la implementación. En Azure, las ubicaciones hacen referencia a una región como oeste de Estados Unidos o Sudamérica. En Azure Stack, las ubicaciones son diferentes porque Azure Stack está en el centro de datos.  Para asegurarse de que las plantillas puedan transferirse entre Azure y Azure Stack, se debe hacer referencia a las ubicaciones del grupo de recursos al implementar recursos individuales. Puede hacerlo mediante `[resourceGroup().Location]` para garantizar que todos los recursos hereden la ubicación del grupo de recursos.  El siguiente fragmento de plantilla de Resource Manager es un ejemplo de uso de esta función al implementar una cuenta de almacenamiento:

    "resources": [
    {
      "name": "[variables('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "[variables('apiVersionStorage')]",
      "location": "[resourceGroup().location]",
      "comments": "This storage account is used to store the VM disks",
      "properties": {
      "accountType": "Standard_GRS"
      }
    }
    ]


## <a name="next-steps"></a>Pasos siguientes
* [Implementación de plantillas con PowerShell](azure-stack-deploy-template-powershell.md)
* [Implementación de plantillas con la CLI de Azure](azure-stack-deploy-template-command-line.md)
* [Implementación de plantillas con Visual Studio](azure-stack-deploy-template-visual-studio.md)

