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
ms.openlocfilehash: 9cdbe4a12a0b16341a1e52f176901045baf327b5
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>Desagrupación, ámbitos y bucles de aplicaciones lógicas
  
Logic Apps proporciona una serie de métodos para trabajar con matrices, colecciones, lotes y bucles en un flujo de trabajo.
  
## <a name="foreach-loop-and-arrays"></a>Matrices y bucle ForEach
  
Las aplicaciones lógicas permiten crear bucles en un conjunto de datos y realizar una acción en cada elemento.  Crear bucles en una colección es posible con la acción `foreach`.  En el diseñador, puede agregar un bucle ForEach.  Después de seleccionar la matriz en la que desea realizar la iteración, puede empezar a agregar acciones.  Puede agregar varias acciones por bucle ForEach.  En el bucle se puede empezar a especificar qué debe ocurrir en cada valor de la matriz.

  Este ejemplo envía un correo electrónico a cada dirección de correo electrónico que contenga "microsoft.com". Si utiliza la vista de código, puede especificar un bucle ForEach como el ejemplo a continuación:

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
  
  Una acción `foreach` puede recorrer en iteración matrices con miles de entidades.  De forma predeterminada, las iteraciones se ejecutan en paralelo.  Consulte [Límites y configuración](logic-apps-limits-and-config.md) para más información acerca de los límites de matriz y simultaneidad.

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
  
  Puede realizar una acción o una serie de acciones hasta que se cumpla una condición.  El escenario más común para usar un bucle Until es llamar a un punto de conexión hasta que obtenga la respuesta que busca.  En el diseñador, puede especificar que se agregue un bucle Until.  Después de agregar las acciones dentro del bucle, puede establecer la condición de salida, así como los límites de bucle.
  
  Este ejemplo llama a un punto de conexión HTTP hasta que el cuerpo de respuesta tenga el valor "Completado".  Completa cuando se da una de las siguientes situaciones: 
  
  * La respuesta HTTP tenga el estado "Completado".
  * Se han realizado intentos durante una hora
  * Haya entrado en bucle 100 veces.
  
  Si utiliza la vista de código, puede especificar un bucle Until como el ejemplo a continuación:
  
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

A veces, un desencadenador puede recibir una matriz de elementos que desea desagrupar e iniciar un flujo de trabajo por elemento.  Esta desagrupación puede realizarse a través del comando `spliton`.  De forma predeterminada, si el archivo swagger desencadenador especifica una carga que es una matriz, se agregará un comando `spliton`. El comando `spliton` inicia una ejecución por cada elemento de la matriz.  SplitOn solo pueden agregarse a un desencadenador que se pueda configurar o invalidar manualmente. No puede tener `spliton` ni tampoco implementar el patrón de respuesta sincrónica.  Cualquier flujo de trabajo llamado que tiene una acción `response`, además de `spliton`, se ejecuta de forma asincrónica y envía una respuesta `202 Accepted` inmediata.  

  Este ejemplo recibe una matriz de elementos y realiza desagrupaciones en cada fila. SplitOn se puede especificar en la vista código como en el siguiente ejemplo:

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

Se pueden agrupar una serie de acciones con un ámbito.  Los ámbitos son especialmente útiles para implementar el control de excepciones.  En el diseñador puede agregar un nuevo ámbito y empezar a agregar las acciones dentro de él.  Puede definir ámbitos en la vista código de forma similar al siguiente ejemplo:


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
