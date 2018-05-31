---
title: Desarrollo de plantillas para Azure Stack | Microsoft Docs
description: Información sobre las prácticas recomendadas de plantillas de Azure Stack
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 046866d9ed7ce65e3b46be1c67b4ab2058cefa4d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304154"
---
# <a name="azure-resource-manager-template-considerations"></a>Consideraciones de la plantilla de Azure Resource Manager

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Al desarrollar la aplicación, es importante garantizar la portabilidad de las plantillas entre Azure y Azure Stack. Este artículo proporciona consideraciones para el desarrollo de [plantillas](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) de Azure Resource Manager, por lo que puede crear un prototipo de la implementación de prueba y la aplicación en Azure sin acceder a un entorno de Azure Stack.

## <a name="resource-provider-availability"></a>Disponibilidad del proveedor de recursos

La plantilla que tenga previsto implementar debe utilizar solo servicios de Microsoft Azure que ya estén disponibles o esté en la versión preliminar en Azure Stack.

## <a name="public-namespaces"></a>Espacios de nombres públicos

Como Azure Slack se hospeda en el centro de datos, tiene espacios de nombres de punto de conexión de servicio diferentes a la nube pública de Azure. Como resultado, se producirá un error en los puntos de conexión públicos codificados de forma rígida de las plantillas de Azure Resource Manager al tratar de implementarlos en Azure Stack. Puede compilar dinámicamente puntos de conexión de servicio con las funciones *reference* y *concatenate* para recuperar valores del proveedor de recursos durante la implementación. Por ejemplo, en lugar de codificar de forma rígida *blob.core.windows.net* en la plantilla, recupere[primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-simple-windows-vm/azuredeploy.json#L201) para establecer dinámicamente el punto de conexión *osDisk.URI*:

     "osDisk": {"name": "osdisk","vhd": {"uri":
     "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
      '/',variables('OSDiskName'),'.vhd')]"}}

## <a name="api-versioning"></a>Control de versiones de la API

Las versiones de los servicios de Azure pueden diferir entre Azure y Azure Stack. Cada recurso requiere el atributo **apiVersion**, que define las funcionalidades que ofrece. Para garantizar la compatibilidad de la versión de la API en Azure Stack, estas son versiones de API válidas para cada proveedor de recursos:

| Proveedor de recursos | apiVersion |
| --- | --- |
| Proceso |`'2015-06-15'` |
| Red |`'2015-06-15'`, `'2015-05-01-preview'` |
| Storage |`'2016-01-01'`, `'2015-06-15'`, `'2015-05-01-preview'` |
| KeyVault | `'2015-06-01'` |
| App Service |`'2015-08-01'` |

## <a name="template-functions"></a>Funciones de plantillas

Las [funciones](../../azure-resource-manager/resource-group-template-functions.md) de Azure Resource Manager proporcionan las funcionalidades necesarias para crear plantillas dinámicas. Por ejemplo, puede utilizar funciones para tareas como las siguientes:

* Concatenar o recortar cadenas.
* Hacer referencia a valores de otros recursos.
* Efectuar una iteración en los recursos para implementar varias instancias.

Estas funciones no están disponibles en Azure Stack:

* Skip
* Take

## <a name="resource-location"></a>Ubicación del recurso

Las plantillas de Azure Resource Manager usan un atributo location para incluir recursos durante la implementación. En Azure, las ubicaciones hacen referencia a una región como oeste de Estados Unidos o Sudamérica. En Azure Stack, las ubicaciones son diferentes porque Azure Stack está en el centro de datos. Para asegurarse de que las plantillas puedan transferirse entre Azure y Azure Stack, se debe hacer referencia a las ubicaciones del grupo de recursos al implementar recursos individuales. Puede hacerlo mediante `[resourceGroup().Location]` para garantizar que todos los recursos hereden la ubicación del grupo de recursos. El siguiente fragmento es un ejemplo de uso de esta función al implementar una cuenta de almacenamiento:

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
