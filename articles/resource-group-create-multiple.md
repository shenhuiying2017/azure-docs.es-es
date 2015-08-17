<properties
   pageTitle="Creación de varias instancias de recursos"
   description="Describe cómo usar la operación de copia en una plantilla del Administrador de recursos de Azure para iterar varias veces cuando se implementan recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2015"
   ms.author="tomfitz"/>

# Creación de varias instancias de recursos en el Administrador de recursos de Azure

En este tema se muestra cómo iterar en la plantilla del Administrador de recursos de Azure para crear varias instancias de un recurso.

## copy y copyIndex()

En el recurso que se va a crear varias veces, puede definir un objeto **copy** que especifica el número de veces que se va a iterar. La copia tiene el formato siguiente:

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Se puede obtener acceso al valor de iteración actual con la función **copyIndex()**, como se muestra a continuación dentro de la función concat.

    [concat('examplecopy-', copyIndex())]

## Uso del valor de índice en el nombre

Puede usar la operación de copia para crear varias instancias de un recurso que se nombra exclusivamente según el índice incremental. Por ejemplo, puede que desee agregar un número exclusivo al final de cada nombre de recurso que se implementa. Para implementar tres sitios web con los nombres:

- examplecopy-0
- examplecopy-1
- examplecopy-2.

Use la siguiente plantilla:

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

## Valor de índice de desplazamiento

Observará en el ejemplo anterior que el valor de índice va de cero a 2. Para desplazar el valor de índice, puede pasar un valor en la función **copyIndex()**, como por ejemplo **copyIndex(1)**. El número de iteraciones que se deben realizar todavía se especifica en el elemento copy, pero el valor de copyIndex se desplaza el valor especificado. Por lo tanto, con la misma plantilla que en el ejemplo anterior, pero especificando **copyIndex(1)**, implementaría tres sitios web con los nombres:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## Uso con la matriz
   
La operación de copia es especialmente útil al trabajar con matrices, ya que puede iterar a través de cada elemento de la matriz. Para implementar tres sitios web con los nombres:

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

Use la siguiente plantilla:

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

## Pasos siguientes
- [Creación de plantillas de Administrador de recursos de Azure](./resource-group-authoring-templates.md)
- [Funciones de la plantilla del Administrador de recursos de Azure](./resource-group-template-functions.md)
- [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](azure-portal/resource-group-template-deploy.md)

<!---HONumber=August15_HO6-->