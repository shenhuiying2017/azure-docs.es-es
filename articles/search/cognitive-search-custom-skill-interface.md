---
title: Definición de interfaz para aptitudes personalizadas en una canalización de Cognitive Search (Azure Search) | Microsoft Docs
description: Interfaz de extracción de datos personalizada para la aptitud personalizada de Web API en la canalización de Cognitive Search en Azure Search.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: f415eb6080a02d25fc47c40b2719544d2ea99c5b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786854"
---
# <a name="how-to-add-a-custom-skill-to-a-cognitive-search-pipeline"></a>Cómo agregar una aptitud personalizada a una canalización de Cognitive Search

En este artículo, aprenderá a agregar una aptitud personalizada a una canalización de Cognitive Search. Se puede crear una [canalización de indexación de búsqueda Cognitive Search](cognitive-search-concept-intro.md) en Azure Search a partir de [aptitudes predefinidas](cognitive-search-predefined-skills.md) y aptitudes personalizadas que puede crear y agregar personalmente a la canalización.

Crear una aptitud personalizada le otorga una forma de insertar transformaciones únicas en el contenido. Una aptitud personalizada se ejecuta de forma independiente; además, puede aplicar cualquier paso de enriquecimiento que quiera. Por ejemplo, puede definir entidades personalizadas específicas de campo, crear modelos de clasificación personalizados para diferenciar contratos y documentos comerciales y financieros, o agregar una aptitud de reconocimiento de voz para profundizar en el contenido relevante de los archivos de audio. Para obtener un ejemplo paso a paso, consulte [Example: creating a custom skill](cognitive-search-create-custom-skill-example.md) (Ejemplo: crear una aptitud personalizada).

 Cualquiera que sea la capacidad personalizada que necesite, existe una interfaz simple y clara para conectar una aptitud personalizada al resto de la canalización de enriquecimiento. El único requisito para su inclusión en un [conjunto de aptitudes](cognitive-search-defining-skillset.md), es la capacidad de aceptar entradas y emitir salidas de manera que se puedan consumir dentro del conjunto de aptitudes en conjunto. Este artículo se centra en los formatos de entrada y salida que necesita la canalización de enriquecimiento.

## <a name="web-api-custom-skill-interface"></a>Interfaz de aptitudes personalizadas de Web API

Actualmente, el único mecanismo para interactuar con una aptitud personalizada es a través de una interfaz de Web API. Web API debe cumplir los requisitos que se describen en esta sección.

### <a name="1--web-api-input-format"></a>1.  Formato de entrada de Web API

Web API debe aceptar la matriz de registros que se va a procesar. Cada registro debe tener un "contenedor de propiedades" que será la entrada proporcionada a Web API. 

Supongamos que quiere crear una opción de enriquecimiento simple que identifique la primera fecha mencionada en el texto de un contrato. En este ejemplo, la aptitud acepta una sola entrada *contractText* como texto del contrato. Asimismo, la aptitud también tiene un único resultado, que es la fecha del contrato. Para que la opción de enriquecimiento sea más interesante, devuelva *contractDate* en la forma de un tipo complejo de varias partes.

Web API debería estar lista para recibir un lote de registros de entrada. Cada miembro de la matriz de *valores* representa la entrada de un registro en particular. Es necesario que cada registro tenga los siguientes elementos:

+ Un miembro *recordId* que sea el identificador único de un registro en particular. Cuando la operación de enriquecimiento devuelva los resultados, debe proporcionar el miembro *recordId* para permitir que el autor de la llamada haga coincidir los resultados del registro con los elementos de entrada.

+ Un miembro *data*, que es esencialmente una colección de campos de entrada de cada registro.

Para ser más concretos, si tomamos de referencia el ejemplo anterior, Web API debe esperar solicitudes que tengan este aspecto:

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
En realidad, es posible llamar al servicio con cientos o miles de registros en lugar de usar solo los tres que se muestran aquí.

### <a name="2-web-api-output-format"></a>2. Formato de salida de Web API

El formato de salida es un conjunto de registros que contiene un miembro *recordId* y un contenedor de propiedades. 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

Este ejemplo en particular solo tiene una salida, pero puede generar más de una propiedad. 

### <a name="errors-and-warning"></a>Errores y advertencias

Tal como se muestra en el ejemplo anterior, puede devolver mensajes de error y advertencia para cada registro.

## <a name="consuming-custom-skills-from-skillset"></a>Consumir aptitudes personalizadas del conjunto de aptitudes

Cuando crea una opción de enriquecimiento de Web API, puede describir encabezados y parámetros de HTTP como parte de la solicitud. En el siguiente fragmento de código se muestra la manera de describir los parámetros de solicitud y los encabezados HTTP como parte de la definición del conjunto de aptitudes.

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Pasos siguientes

+ [Ejemplo: crear una aptitud personalizada para la Translate Text API](cognitive-search-create-custom-skill-example.md)
+ [Cómo definir un conjunto de aptitudes](cognitive-search-defining-skillset.md)
+ [Crear un conjunto de aptitudes (REST)](ref-create-skillset.md)
+ [Cómo asignar campos enriquecidos](cognitive-search-output-field-mapping.md)
