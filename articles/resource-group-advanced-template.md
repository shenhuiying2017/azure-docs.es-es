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
   ms.date="06/29/2015"
   ms.author="tomfitz"/>

# Operaciones avanzadas de plantilla

En este tema se describe la operación de copia y las plantillas anidadas que puede usar para realizar tareas más avanzadas al implementar recursos en Azure.

## copy

Permite usar la iteración un número determinado de veces mientras se implementa un recurso.
   
La operación de copia es especialmente útil al trabajar con matrices, ya que puede iterar a través de cada elemento de la matriz. La función **copyIndex()** devuelve el valor actual de la iteración. Puede implementar tres sitios web con los nombres:

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

con la siguiente plantilla.

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

También puede utilizar la operación de copia sin una matriz. Por ejemplo, puede que desee agregar un número que se va incrementando al final de cada nombre de recurso que se implementa. Puede implementar tres sitios web con los nombres:

- examplecopy-0
- examplecopy-1
- examplecopy-2.

con la siguiente plantilla.

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
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

Observará en el ejemplo anterior que el valor de índice va de cero a 2. Para desplazar el valor de índice, puede pasar un valor en la función **copyIndex()**, como por ejemplo **copyIndex(1)**. El número de iteraciones que se deben realizar todavía se especifica en el elemento copy, pero el valor de copyIndex se desplaza el valor especificado. Por lo tanto, con la misma plantilla que en el ejemplo anterior, pero especificando **copyIndex(1)**, implementaría tres sitios web con los nombres:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## Plantilla anidada

En ocasiones puede que necesite combinar dos plantillas, o puede que necesite generar una plantilla secundaria a partir de una principal. Esto se puede lograr mediante la utilización de un recurso de implementación dentro de la plantilla principal que apunte a la plantilla secundaria. Para ello, debe establecer la propiedad **templateLink** en el URI de la plantilla anidada. Puede proporcionar valores de parámetro para la plantilla anidada especificando los valores directamente en la plantilla o mediante la vinculación a un archivo de parámetros. El primer ejemplo utiliza la propiedad **parameters** para especificar un valor de parámetro directamente.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

El siguiente ejemplo utiliza la propiedad **parametersLink** para vincular a un archivo de parámetros.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/ArmTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

## Pasos siguientes
- [Creación de plantillas del Administrador de recursos de Azure](./resource-group-authoring-templates.md)
- [Funciones de la plantilla del Administrador de recursos de Azure](./resource-group-template-functions.md)
- [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](azure-portal/resource-group-template-deploy.md)
- [Información general del Administrador de recursos de Azure](./resource-group-overview.md)

<!---HONumber=July15_HO1-->