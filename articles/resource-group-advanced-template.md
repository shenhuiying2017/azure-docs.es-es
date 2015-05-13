<properties
   pageTitle="Opciones avanzadas de plantilla del Administrador de recursos de Azure"
   description="Describe cómo utilizar las plantillas anidadas y la operación de copia en una plantilla del Administrador de recursos de Azure al implementar aplicaciones en Azure."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz;ilygre"/>

# Operaciones avanzadas de plantilla

En este tema se describe la operación de copia y las plantillas anidadas que puede usar para realizar tareas más avanzadas al implementar recursos en Azure.

## copia

Le permite usar la iteración a través de una matriz y utilizar cada elemento al implementar un recurso.
   
En el ejemplo siguiente se implementan tres sitios web denominados examplecopy-Contoso, examplecopy-Fabrikam, examplecopy-Coho.

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      },
      "count": { 
         "type": "int", 
         "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

## Plantilla anidada

En ocasiones puede que necesite combinar dos plantillas, o puede que necesite generar una plantilla secundaria a partir de una primaria. Esto se puede lograr mediante la utilización de un recurso de implementación dentro de la plantilla principal para implementar una plantilla secundaria. Proporcione el URI de la plantilla anidada, como se muestra a continuación.

    "variables": {"templatelink":"https://www.contoso.com/ArmTemplates/newStorageAccount.json"}, 
    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {"uri":"[variables('templatelink')]","contentVersion":"1.0.0.0"}, 
           "parameters": { 
              "StorageAccountName":{"value":"[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

## Pasos siguientes
- [Creación de plantillas del Administrador de recursos de Azure](./resource-group-authoring-templates.md)
- [Funciones de la plantilla del Administrador de recursos de Azure](./resource-group-template-functions.md)
- [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](./resouce-group-template-deploy.md)
- [Información general del Administrador de recursos de Azure](./resource-group-overview.md)

<!--HONumber=52-->
