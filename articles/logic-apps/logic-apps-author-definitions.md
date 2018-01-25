---
title: "Definición de flujos de trabajo con JSON: Azure Logic Apps | Microsoft Docs"
description: "Definiciones de flujo de trabajo en JSON para aplicaciones lógicas"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/29/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 7dde5bc4733af1aba34199f332379d2faf566725
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="create-workflow-definitions-for-logic-apps-using-json"></a>Creación de definiciones de flujo de trabajo para aplicaciones lógicas mediante JSON

Puede crear definiciones de flujo de trabajo para [Azure Logic Apps](logic-apps-overview.md) con un lenguaje JSON sencillo y declarativo. Si no lo ha hecho ya, primero consulte [cómo crear su primera aplicación lógica con el diseñador de aplicaciones lógicas](quickstart-create-first-logic-app-workflow.md). Consulte también la [referencia completa del lenguaje de definición de flujo de trabajo](http://aka.ms/logicappsdocs).

## <a name="repeat-steps-over-a-list"></a>Repetición de los pasos de una lista

Para repetir los elementos de una matriz que contiene hasta 10 000 elementos y realizar una acción para cada elemento, utilice el [tipo foreach](logic-apps-loops-and-scopes.md).

## <a name="handle-failures-if-something-goes-wrong"></a>Control de errores si algo va mal

Normalmente desea incluir un *paso de corrección*, es decir, cierta lógica que se ejecuta *si, y solo si*, una o varias llamadas no se han podido realizar correctamente. En este ejemplo, obtenemos datos desde diversos lugares, pero si se produce un error en la llamada, es necesario PUBLICAR un mensaje en alguna parte para poder localizar ese error más adelante:  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "postToErrorMessageQueue": {
      "type": "ApiConnection",
      "inputs": "...",
      "runAfter": {
        "readData": [
          "Failed"
        ]
      }
    }
  },
  "outputs": {}
}
```

Para especificar que `postToErrorMessageQueue` solo se ejecuta si `readData` es `Failed`, use la propiedad `runAfter`, por ejemplo, para especificar una lista de posibles valores, para que `runAfter` pueda ser `["Succeeded", "Failed"]`.

Por último, dado que en este ejemplo ya se ha controlado el error, la ejecución ya no se marcará como `Failed`. Como hemos agregado el paso para controlar este error en este ejemplo, la ejecución es `Succeeded` aunque un paso `Failed`.

## <a name="execute-two-or-more-steps-in-parallel"></a>Ejecución de dos o más pasos en paralelo

Para ejecutar varias acciones en paralelo, la propiedad `runAfter` debe ser equivalente en tiempo de ejecución. 

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "kind": "http",
      "type": "Request"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

En este ejemplo, se ha establecido que `branch1` y `branch2` se ejecuten después de `readData`. Como consecuencia, ambas acciones se ejecutan en paralelo. La marca de tiempo para ambas acciones es idéntica.

![Paralelo](media/logic-apps-author-definitions/parallel.png)

## <a name="join-two-parallel-branches"></a>Unión de dos ramas paralelas

Puede unir dos acciones que se han establecido para ejecutarse en paralelo al agregar elementos a la propiedad `runAfter` como en el ejemplo anterior.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {}
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "join": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "branch1": [
          "Succeeded"
        ],
        "branch2": [
          "Succeeded"
        ]
      }
    }
  },
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "Http",
      "inputs": {
        "schema": {}
      }
    }
  },
  "contentVersion": "1.0.0.0",
  "outputs": {}
}
```

![Paralelo](media/logic-apps-author-definitions/join.png)

## <a name="map-list-items-to-a-different-configuration"></a>Asignación de elementos de lista a una configuración diferente

A continuación, supongamos que queremos obtener contenido diferente según un valor de una propiedad. Podemos crear una asignación de valores a destinos como un parámetro:  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "http://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

En este caso, primero debemos obtener una lista de artículos. Según la categoría que se haya definido como parámetro, el segundo paso será utilizar una asignación para buscar la dirección URL para obtener el contenido.

Algunas observaciones a tener en cuenta aquí: 

*   La función [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) comprueba si la categoría coincide con alguna de las categorías definidas conocidas.

*   Una vez que se obtiene la categoría, se puede extraer el elemento de la asignación mediante corchetes: `parameters[...]`.

## <a name="process-strings"></a>Cadenas de proceso

Puede usar varias funciones para manipular cadenas. Por ejemplo, supongamos que tenemos una cadena que queremos pasar a un sistema, pero no estamos seguros acerca del control adecuado para la codificación de caracteres. Una opción consiste en codificar esta cadena con base64. Sin embargo, para evitar escapes en una dirección URL, vamos a reemplazar algunos caracteres. 

También queremos una subcadena del nombre del pedido, porque los cinco primeros caracteres no se usan.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "orderer": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Si trabajamos desde dentro hacia fuera:

1. Obtenga el valor de [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) del nombre del solicitante, lo que devuelve el número total de caracteres.

2. Reste 5 porque deseamos una cadena más corta.

3. Realmente, quitamos [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring). Comenzamos con el índice `5` y pasamos al resto de la cadena.

4. Convierta esta subcadena en una cadena [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64).

5. Use [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) para reemplazar todos los caracteres `+` por `-`.

6. Use [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) para reemplazar todos los caracteres `/` por `_`.

## <a name="work-with-date-times"></a>Trabajo con fechas

Las fechas pueden ser útiles, especialmente cuando intenta extraer datos de un origen de datos que no admite de forma natural *desencadenadores*. También puede utilizar las fechas para averiguar cuánto duran los distintos pasos.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

En este ejemplo, vamos a extraer el valor `startTime` del paso anterior. A continuación, obtenemos la hora actual y restamos un segundo:

[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) 

Puede usar otras unidades de tiempo, como `minutes` o `hours`. Por último, podemos comparar estos dos valores. Si el primero es inferior al segundo, significa que ha transcurrido más de un segundo desde la primera vez que se realizó el pedido.

Para dar formato a fechas, podemos usar formateadores de cadena. Por ejemplo, para obtener el valor de RFC1123, usamos [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow). Para obtener información sobre los formatos de fecha, consulte [Lenguaje de definición de flujo de trabajo](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow).

## <a name="deployment-parameters-for-different-environments"></a>Parámetros de implementación para entornos diferentes

Por lo general, un ciclo de vida de implementación tiene un entorno de desarrollo, un entorno de ensayo y un entorno de producción. Por ejemplo, puede utilizar la misma definición en todos estos entornos pero utilizar diferentes bases de datos. Del mismo modo, es posible que desee utilizar la misma definición en muchas regiones diferentes para lograr una alta disponibilidad, pero que cada instancia de aplicación lógica se comunique con la base de datos de esa región.
Este escenario no tiene que ver nada con tomar parámetros en *tiempo de ejecución* en aquellas ocasiones en las que debe utilizar la función `trigger()` como en el ejemplo anterior.

Puede empezar con una definición muy básica, como la de este ejemplo:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

En la solicitud `PUT` real para las aplicaciones lógicas, puede proporcionar el parámetro `uri`. Dado que ya no existe un valor predeterminado, la carga útil de la aplicación lógica requiere este parámetro:

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

En cada entorno puede proporcionar un valor diferente para el parámetro `connection`. 

Consulte la [documentación sobre la API de REST](https://msdn.microsoft.com/library/azure/mt643787.aspx) para conocer todas las opciones disponibles para crear y administrar aplicaciones lógicas. 
