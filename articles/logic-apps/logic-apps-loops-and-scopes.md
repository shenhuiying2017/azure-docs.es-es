---
title: "Creación de bucles y ámbitos, o división de datos en flujos de trabajo - Azure Logic Apps | Microsoft Docs"
description: "Cree bucles para recorrer los datos, agrupar acciones en ámbitos, o dividir datos para iniciar más flujos de trabajo en Azure Logic Apps."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 413a2ba9107ca259ed577825bf0a17ff5622f1ac
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Desagrupación, ámbitos y bucles de aplicaciones lógicas
  
Logic Apps proporciona una serie de métodos para trabajar con matrices, colecciones, lotes y bucles en un flujo de trabajo.
  
## <a name="foreach-loop-and-arrays"></a>Matrices y bucle ForEach
  
Las aplicaciones lógicas permiten crear bucles en un conjunto de datos y realizar una acción en cada elemento.  Esto es posible gracias a la acción `foreach` .  En el diseñador, puede especificar que se agregue un bucle ForEach.  Después de seleccionar la matriz en la que desea realizar la iteración, puede empezar a agregar acciones.  En estos momentos, solo puede agregar una acción por cada bucle ForEach, pero esta restricción se eliminará en las próximas semanas.  En el bucle se puede empezar a especificar qué debe ocurrir en cada valor de la matriz.

Si utiliza la vista de código, puede especificar un bucle ForEach como el siguiente.  Se trata de un ejemplo de un bucle ForEach que envía un correo electrónico a cada dirección de correo electrónico que contenga "microsoft.com":

``` json
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')"
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                },
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  Una acción `foreach` puede iterar hasta 5000 filas en las matrices.  De forma predeterminada, cada iteración se ejecutará en paralelo.  

### <a name="sequential-foreach-loops"></a>Bucles ForEach secuenciales

Para habilitar un bucle foreach para ejecutarse de manera secuencial, la operación `Sequential` se debe agregar la opción.

``` json
"forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "operationOptions": "Sequential",
        "..."
}
```
  
## <a name="until-loop"></a>Bucle Until
  
  Puede realizar una acción o una serie de acciones hasta que se cumpla una condición.  El escenario más común para este fin es llamar a un punto de conexión hasta que obtenga la respuesta que busca.  En el diseñador, puede especificar que se agregue un bucle Until.  Después de agregar las acciones dentro del bucle, puede establecer la condición de salida, así como los límites de bucle.  Hay un retraso de 1 minuto entre los ciclos de bucle.
  
  Si utiliza la vista de código, puede especificar un bucle Until como el siguiente.  Se trata de un ejemplo de una llamada a un punto de conexión HTTP hasta que el cuerpo de respuesta tenga el valor "Completado".  Se completará cuando: 
  
  * La respuesta HTTP tenga el estado "Completado".
  * Haya realizado intentos durante 1 hora.
  * Haya entrado en bucle 100 veces.
  
  ``` json
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed')",
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn y desagrupación

A veces, un desencadenador puede recibir una matriz de elementos que desea desagrupar e iniciar un flujo de trabajo por elemento.  Esto puede realizarse a través del comando `spliton` .  De forma predeterminada, si el archivo swagger desencadenador especifica una carga que es una matriz, se agregará `spliton` e iniciará una ejecución por cada elemento.  SplitOn solo puede agregarse a un desencadenador.  Puede invalidarse o configurarse manualmente en la definición de vista de código.  En estos momentos, SplitOn puede desagrupar hasta 5000 elementos en matrices.  No puede tener `spliton` ni tampoco implementar el patrón de respuesta sincrónica.  Cualquier flujo de trabajo que tiene una acción `response`, además de `spliton`, se ejecutará de forma asincrónica y enviará una respuesta `202 Accepted` inmediata.  

SplitOn se puede especificar en la vista código como en el siguiente ejemplo.  De este modo, se recibe una matriz de elementos y realiza desagrupaciones en cada fila.

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequencey": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>Ámbitos

Se pueden agrupar una serie de acciones con un ámbito.  Esto es especialmente útil para implementar el control de excepciones.  En el diseñador puede agregar un nuevo ámbito y empezar a agregar las acciones dentro de él.  Puede definir ámbitos en la vista código de forma similar a esta:


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```