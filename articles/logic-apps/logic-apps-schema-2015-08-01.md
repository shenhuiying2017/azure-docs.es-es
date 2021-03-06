---
title: 'Actualizaciones del esquema de versión preliminar del 1 de agosto de 2015: Azure Logic Apps | Microsoft Docs'
description: Creación de definiciones de JSON para Azure Logic Apps con la versión de esquema 2015-08-01-preview
author: stepsic-microsoft-com
manager: SyntaxC4
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 05/31/2016
ms.author: stepsic; LADocs
ms.openlocfilehash: bdadc2e33082421500f21d5926ac1e660f4164d4
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774308"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Actualizaciones de esquema para Azure Logic Apps de la versión preliminar del 1 de agosto de 2015

Esta versión de esquema y API de Azure Logic Apps incluye importantes mejoras que aportan una mayor confiabilidad a las aplicaciones lógicas y facilitan su uso:

* El tipo de acción **APIApp** ahora se denomina [**APIConnection**](#api-connections).
* La acción **Repeat** ahora se denomina [**Foreach**](#foreach).
* La aplicación de API [**Agente de escucha HTTP**](#http-listener) ya no es necesaria.
* En la llamada a los flujos de trabajo secundarios se usa un [nuevo esquema](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Paso a las conexiones de API

El cambio más importante es que ya no es necesario implementar aplicaciones de API en la suscripción de Azure para poder usar las API. Estas son las formas en que puede usar las API:

* API administradas
* Sus API web personalizadas

Cada una se controla de forma algo distinta, ya que sus modelos de administración y hospedaje son diferentes. Una ventaja de este modelo es que ya no está limitado a los recursos que se implementan en el grupo de recursos de Azure. 

### <a name="managed-apis"></a>API administradas

Algunas API se administran de forma automática, como Office 365, Salesforce, Twitter y FTP. Puede usar algunas API administradas tal y como están, como Bing Translate, mientras que otras requieren configuración, también denominada *conexión*.

Por ejemplo, cuando use Office 365, debe crear una conexión que incluya el token de inicio de sesión de Office 365. El token se almacena y actualiza de forma segura para que la aplicación lógica pueda llamar siempre a la API de Office 365. Si quiere conectarse al servidor SQL o FTP, debe crear una conexión que tenga la cadena de conexión. 

En esta definición, estas acciones se denominan `APIConnection`. A continuación se muestra un ejemplo de una conexión que llama a Office 365 para enviar un correo electrónico:

``` json
{
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "host": {
               "api": {
                  "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
               },
               "connection": {
                  "name": "@parameters('$connections')['shared_office365']['connectionId']"
               }
            },
            "method": "POST",
            "body": {
               "Subject": "Reminder",
               "Body": "Don't forget!",
               "To": "me@contoso.com"
            },
            "path": "/Mail"
         }
      }
   }
}
```

El objeto `host` es una parte de las entradas que es exclusiva de las conexiones de API y que contiene estas partes: `api` y `connection`. El objeto `api` especifica la URL de tiempo de ejecución del lugar donde se hospeda la API administrada. Puede ver todas las API administradas disponibles mediante una llamada a `GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Al usar una API, esta puede o no tener *parámetros de conexión* definidos. Por lo tanto, si la API no define estos parámetros, no se requiere ninguna conexión. Si la API define estos parámetros, debe crear una conexión con un nombre especificado.  
Haga referencia a ese nombre en el objeto `connection` dentro del objeto `host`. Para crear una conexión en un grupo de recursos, llame a este método:

```
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

Con el siguiente cuerpo:

``` json
{
   "properties": {
      "api": {
         "id": "/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/azureblob"
      },
      "parameterValues": {
         "accountName": "<Azure-storage-account-name-with-different-parameters-for-each-API>"
      }
   },
   "location": "<logic-app-location>"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Implementación de API administradas en una plantilla de Azure Resource Manager

Puede crear una aplicación completa en una plantilla de Azure Resource Manager siempre que no se requiera inicio de sesión interactivo.
Si se requiere inicio de sesión, puede configurar todo con la plantilla de Azure Resource Manager, pero aun así tiene que visitar Azure Portal para autorizar las conexiones. 

``` json
"resources": [ {
   "apiVersion": "2015-08-01-preview",
   "name": "azureblob",
   "type": "Microsoft.Web/connections",
   "location": "[resourceGroup().location]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
      },
      "parameterValues": {
         "accountName": "[parameters('storageAccountName')]",
         "accessKey": "[parameters('storageAccountKey')]"
      }
    },
},
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2015-08-01-preview",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"],
   "properties": {
      "sku": {
         "name": "[parameters('sku')]",
         "plan": {
            "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('svcPlanName'))]"
         }
      },
      "parameters": {
         "$connections": {
             "value": {
                  "azureblob": {
                     "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                     "connectionName": "azureblob",
                     "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                  }
             }
         }
      },
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "type": "Object",
            "$connections": {
               "defaultValue": {},
 
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Day",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Create_file": {
               "type": "ApiConnection",
               "inputs": {
                  "host": {
                     "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                     },
                     "connection": {
                       "name": "@parameters('$connections')['azureblob']['connectionId']"
                     }
                  },
                  "method": "POST",
                  "queries": {
                     "folderPath": "[concat('/', parameters('containerName'))]",
                     "name": "helloworld.txt"
                  },
                  "body": "@decodeDataUri('data:, Hello+world!')",
                  "path": "/datasets/default/files"
               },
               "conditions": []
            }
         },
         "outputs": {}
      }
   }
} ]
```

En este ejemplo puede ver que las conexiones son solo recursos que residen en su grupo de recursos. Haga referencia a las API administradas disponibles en su suscripción.

### <a name="your-custom-web-apis"></a>Sus API web personalizadas

Si usa sus propias API, las no administradas por Microsoft, use la acción integrada **HTTP** para llamarlas. Para tener una experiencia ideal, debe exponer un punto de conexión de Swagger para la API. Este punto de conexión permite al Diseñador de aplicación lógica representar las entradas y salidas de la API. Sin Swagger, el diseñador solo puede mostrar las entradas y salidas como objetos JSON opacos.

Este es un ejemplo que muestra la nueva propiedad `metadata.apiDefinitionUrl` :

``` json
"actions": {
   "mycustomAPI": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
      },
      "inputs": {
         "uri": "https://mysite.azurewebsites.net/api/getsomedata",
         "method": "GET"
      }
   }
}
```

Si hospeda su API web en Azure App Service, esta se muestra automáticamente en la lista de acciones disponibles en el diseñador. De lo contrario, tiene que pegar la dirección URL directamente. El punto de conexión de Swagger debe estar sin autenticar para que se pueda usar dentro del Diseñador de aplicación lógica, aunque puede proteger la API propiamente dicha con cualquier método que admita Swagger.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Llamada a las aplicaciones de API implementadas con 2015-08-01-preview

Si anteriormente ha implementado una aplicación de API, puede llamarla mediante la acción **HTTP**.
Por ejemplo, si utiliza Dropbox para enumerar archivos, la definición de la versión de esquema **2014-12-01-preview** puede presentarse de manera similar a:

``` json
"definition": {
   "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
         "defaultValue": "eyJ0eX...wCn90",
         "type": "String",
         "metadata": {
            "token": {
               "name": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
            }
         }
      }
    },
    "actions": {
       "dropboxconnector": {
          "type": "ApiApp",
          "inputs": {
             "apiVersion": "2015-01-14",
             "host": {
                "id": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                "gateway": "https://avdemo.azurewebsites.net"
             },
             "operation": "ListFiles",
             "parameters": {
                "FolderPath": "/myfolder"
             },
             "authentication": {
                "type": "Raw",
                "scheme": "Zumo",
                "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
             }
          }
       }
    }
}
```

Ahora puede construir la acción HTTP equivalente como en el siguiente ejemplo, dejando la sección de parámetros de la definición de aplicación lógica sin cambios:

``` json
"actions": {
   "dropboxconnector": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
      },
      "inputs": {
         "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
         "method": "POST",
         "body": {
            "FolderPath": "/myfolder"
         },
         "authentication": {
            "type": "Raw",
            "scheme": "Zumo",
            "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
         }
      }
   }
}
```

Vamos a recorrer estas propiedades una a una:

| Propiedad de acción | DESCRIPCIÓN |
| --- | --- |
| `type` | `Http` en lugar de `APIapp` |
| `metadata.apiDefinitionUrl` | Para usar esta acción en el Diseñador de aplicación lógica, incluya el punto de conexión de metadatos, que se construye a partir de: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Se construye a partir de: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Siempre `POST` |
| `inputs.body` | Idéntico a los parámetros de la aplicación de API |
| `inputs.authentication` | Idéntico a la autenticación de la aplicación de API |

Este enfoque debería funcionar para todas las acciones de aplicación de API. Sin embargo, recuerde que estas aplicaciones de API anteriores ya no se admiten. Por lo que debe mover una API administrada a una de las dos opciones anteriores u hospedar su propia API web personalizada.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Cambio de nombre de "repeat" a "foreach"

En la versión de esquema anterior, se recibieron numerosos comentarios de los clientes en que se indicaba que el nombre de acción **Repeat** era confuso y no captaba adecuadamente que **Repeat** era realmente un bucle for-each. Por eso se ha cambiado su nombre de `repeat` a `foreach`. Anteriormente esta acción se escribía como en este ejemplo:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "repeat": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{repeatItem()}"
      }
   }
}
```

Ahora se escribiría esta versión en su lugar:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "foreach": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{item()}"
      }
   }
}
```

Además, la función `repeatItem()`, que hacía referencia al elemento que estaba procesando el bucle durante la iteración actual, ha cambiado su nombre a `item()`. 

### <a name="reference-outputs-from-foreach"></a>Salidas de referencia de "foreach"

Por motivos de simplificación, los resultados de las acciones `foreach` ya no se ajustan en un objeto denominado `repeatItems`. Además, con estos cambios se eliminan las funciones `repeatItem()`, `repeatBody()` y `repeatOutputs()`.

Por lo tanto, al usar el ejemplo anterior `repeat`, se obtienen estos resultados:

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
   },
   "status": "Succeeded"
} ]
```

Ahora se obtienen estos resultados en su lugar:

``` json
[ {
   "name": "pingBing",
      "inputs": {
         "uri": "https://www.bing.com/search?q=0",
         "method": "GET"
      },
      "outputs": {
         "headers": { },
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
      },
      "status": "Succeeded"
} ]
```

Anteriormente, para obtener el elemento `body` de la acción al hacer referencia a estos resultados:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "repeat": "@outputs('pingBing').repeatItems",
      "inputs": {
         "method": "POST",
         "uri": "http://www.example.com",
         "body": "@repeatItem().outputs.body"
      }
   }
}
```

Ahora se puede usar esta versión en su lugar:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "foreach": "@outputs('pingBing')",
      "inputs": {
         "method": "POST",
         "uri": "http://www.example.com",
         "body": "@item().outputs.body"
      }
   }
}
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>Agente de escucha HTTP nativo

Las funcionalidades de la escucha HTTP ahora están integradas. Así que ya no necesita implementar una aplicación de API de escucha HTTP. Consulte aquí [los detalles completos de cómo hacer que los puntos de conexión de aplicación lógica](../logic-apps/logic-apps-http-endpoint.md). 

Con estos cambios, eliminamos la función `@accessKeys()`, que sustituimos por la función `@listCallbackURL()` para obtener el punto de conexión cuando sea necesario. Además, ahora debe definir al menos un desencadenador en la aplicación lógica. Si desea `/run` el flujo de trabajo, debe tener uno de estos desencadenadores: `manual`, `apiConnectionWebhook` o `httpWebhook`.

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Llamada a flujos de trabajo secundarios

Anteriormente, para llamar a los flujos de trabajo secundarios era necesario ir al flujo de trabajo, obtener el token de acceso y luego pegarlo en la definición de la aplicación lógica donde quiere llamar a ese flujo de trabajo secundario. Con la nueva versión de esquema, el motor de Logic Apps genera automáticamente una firma SAS en tiempo de ejecución para el flujo de trabajo secundario, lo que significa que no tiene que pegar ningún secreto en la definición. Este es un ejemplo:

``` json
"myNestedWorkflow": {
   "type": "Workflow",
   "inputs": {
      "host": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/myWorkflow001",
         "triggerName": "myEndpointTrigger"
      },
      "queries": {
         "extrafield": "specialValue"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "Content-type": "application/json"
      },
      "body": {
         "contentFieldOne": "value100",
         "anotherField": 10.001
      }
   },
   "conditions": []
}
```

Una segunda mejora es se va a proporcionar a los flujos de trabajo secundarios acceso completo a la solicitud entrante. Eso significa que puede pasar parámetros en la sección *queries* y en el objeto *headers*, y que puede definir completamente todo el cuerpo.

Por último, hay cambios necesarios en el flujo de trabajo secundario. Mientras que antes podía llamar a un flujo de trabajo secundario directamente, ahora debe definir un punto de conexión de desencadenador en el flujo de trabajo para que el elemento primario realice la llamada. Por lo general, agrega un desencadenador que tiene el tipo `manual` y luego usa desencadenador en la definición del elemento primario. Tenga en cuenta que la propiedad `host` tiene un valor `triggerName`, ya que siempre debe especificar el desencadenador que va a invocar.

## <a name="other-changes"></a>Otros cambios

### <a name="new-queries-property"></a>Nueva propiedad "queries"

Todos los tipos de acción admiten ahora una nueva entrada llamada `queries`. Esta entrada puede ser un objeto estructurado en lugar de tener que ensamblar la cadena a mano.

### <a name="renamed-parse-function-to-json"></a>Cambio de nombre de la función "parse()" a "json()"

Pronto se agregarán más tipos de contenido, por lo que hemos cambiado el nombre de la función `parse()` a `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Próximamente: API de Enterprise Integration

Aún no disponemos de versiones administradas de las API de integración empresarial, como AS2. Mientras tanto, puede usar sus API de BizTalk implementadas existentes a través de la acción HTTP. Para más información, consulte el uso de aplicaciones de API ya administradas en el [plan de integración](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
