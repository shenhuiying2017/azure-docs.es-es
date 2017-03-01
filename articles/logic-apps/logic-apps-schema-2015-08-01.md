---
title: "Nueva versión de esquema 2015-08-01-preview"
description: "Aprenda a escribir la definición de JSON de la versión más reciente de Aplicaciones lógicas"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: 061b68ec5fc9c14fd0c3f00cc0be9f09717fc1ad
ms.openlocfilehash: 8f1e2680aff5c1d5c9b90654a334cae2245db5fb
ms.lasthandoff: 02/15/2017


---
# <a name="new-schema-version-2015-08-01-preview"></a>Nueva versión de esquema 2015-08-01-preview
La nueva versión de esquema y API de Azure Logic Apps presenta varias mejoras que optimizan la confiabilidad y la facilidad de uso de las aplicaciones lógicas. Hay cuatro diferencias claves:

1. El tipo de acción **APIApp** se ha actualizado a un nuevo tipo de acción **APIConnection**.
2. La instrucción **Repeat** se llama ahora **Foreach**.
3. La aplicación de API **Agente de escucha HTTP** ya no es necesaria.
4. En la llamada a los flujos de trabajo secundarios se usa un nuevo esquema.

## <a name="1-moving-to-api-connections"></a>1. Paso a las conexiones de API
El cambio más importante es que ya no es necesario implementar aplicaciones de API en la suscripción de Azure para usar las API. Las API se pueden usar de dos formas:

* API administradas
* Sus API web personalizadas

Cada una se controla de forma algo distinta, ya que sus modelos de administración y hospedaje son diferentes. Una ventaja de este modelo es que ya no está limitado a los recursos que se implementan en el grupo de recursos. 

### <a name="managed-apis"></a>API administradas
Algunas API se administran de forma automática, como Office 365, Salesforce, Twitter y FTP. Puede usar algunas API administradas tal y como están, como en el caso de Bing Translate, mientras que otras requieren configuración. Esta configuración se conoce como *conexión*.

Por ejemplo, cuando utilice Office 365, deberá crear una conexión que contenga el token de inicio de sesión de Office 365. Este token se almacena y actualiza de forma segura para que la aplicación lógica pueda llamar siempre a la API de Office 365. Como alternativa, si desea conectarse a su servidor SQL o FTP, debe crear una conexión que tenga la cadena de conexión. 

En esta definición, estas acciones se denominan `APIConnection`. A continuación se muestra un ejemplo de una conexión que llama a Office 365 para enviar un correo electrónico:

```
{
    "actions": {
        "Send_Email": {
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
                "method": "post",
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

El objeto `host` es una parte de las entradas exclusiva para las conexiones de API y contiene dos partes: `api` y `connection`.

La parte `api` contiene la URL de tiempo de ejecución del lugar donde se hospeda la API administrada. Puede ver todas las API administradas disponibles mediante una llamada a `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

Al usar una API, esta puede o no tener **parámetros de conexión** definidos. Si no los tiene, no se requiere ninguna **conexión**. En caso de que sí los tenga, tiene que crear una conexión. Cuando crea esa conexión, la conexión tiene el nombre que elija. Haga referencia al nombre del objeto `connection` dentro del objeto `host`. Para crear una conexión en un grupo de recursos, llame a:

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

Con el siguiente cuerpo:

```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
    "parameterValues": {
        "accountName": "{The name of the storage account -- the set of parameters is different for each API}"
    }
  },
  "location": "{Logic app's location}"
}
```

### <a name="deploying-managed-apis-in-an-azure-resource-manager-template"></a>Implementación de API administradas en una plantilla de Azure Resource Manager
Puede crear una aplicación completa en una plantilla de Azure Resource Manager siempre que no se requiera inicio de sesión interactivo.
Si se requiere inicio de sesión, puede configurar todo con la plantilla de Azure Resource Manager, pero aun así tendrá que visitar el portal para autorizar las conexiones. 

```
    "resources": [{
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
        }
    }, {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-08-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"
        ],
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
                "actions": {
                    "Create_file": {
                        "type": "apiconnection",
                        "inputs": {
                            "host": {
                                "api": {
                                    "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                                },
                                "connection": {
                                    "name": "@parameters('$connections')['azureblob']['connectionId']"
                                }
                            },
                            "method": "post",
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
                "contentVersion": "1.0.0.0",
                "outputs": {},
                "parameters": {
                    "$connections": {
                        "defaultValue": {},
                        "type": "Object"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "Recurrence",
                        "recurrence": {
                            "frequency": "Day",
                            "interval": 1
                        }
                    }
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
            }
        }
    }]
```

En este ejemplo puede ver que las conexiones son solo recursos que residen en su grupo de recursos. Haga referencia a las API administradas disponibles en su suscripción.

### <a name="your-custom-web-apis"></a>Sus API web personalizadas
Si utiliza sus propias API (en concreto, las que no están administradas por Microsoft), debe usar la acción **HTTP** integrada para llamarlas. Para tener una experiencia ideal, debe exponer un punto de conexión de Swagger para la API. Este punto de conexión permite al Diseñador de aplicación lógica representar las entradas y salidas de la API. Sin Swagger, el diseñador solo puede mostrar las entradas y salidas como objetos JSON opacos.

Este es un ejemplo que muestra la nueva propiedad `metadata.apiDefinitionUrl` :

```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata": {
              "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method": "GET"
            }
        }
    }
}
```

Si hospeda su API web en **App Service**, esta se muestra automáticamente en la lista de acciones disponibles en el diseñador. De lo contrario, tiene que pegar la dirección URL directamente. El punto de conexión de Swagger debe estar sin autenticar para que se pueda usarlo dentro del Diseñador de aplicación lógica (aunque puede proteger la API propiamente dicha con cualquier método admitido en Swagger).

### <a name="using-your-already-deployed-api-apps-with-2015-08-01-preview"></a>Uso de las aplicaciones de API ya implementadas con 2015-08-01-preview
Si anteriormente implementó una aplicación de API, puede llamarla mediante la acción **HTTP** .

Por ejemplo, si utiliza Dropbox para enumerar archivos, la definición de la versión de esquema **2014-12-01-preview** puede presentarse de manera similar a:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
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
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Puede construir la acción HTTP equivalente como se indica a continuación (la sección de parámetros de la definición de aplicación lógica permanece sin cambios):

```
{
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
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Vamos a recorrer estas propiedades una a una:

| Propiedad de acción | Descripción |
| --- | --- |
| `type` |`Http` en lugar de `APIapp` |
| `metadata.apiDefinitionUrl` |Para usar esta acción en el Diseñador de aplicación lógica, incluya el punto de conexión de metadatos, que se construye a partir de: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` |Se construye a partir de: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` |Siempre `POST` |
| `inputs.body` |Idéntico a los parámetros de la aplicación de API |
| `inputs.authentication` |Idéntico a la autenticación de la aplicación de API |

Este enfoque debería funcionar para todas las acciones de aplicación de API. Sin embargo, tenga en cuenta que estas aplicaciones de API anteriores ya no se admiten, y debe cambiar a una de las otras dos opciones anteriores (una API administrada u hospedar la API web personalizada).

## <a name="2-repeat-renamed-to-foreach"></a>2. Cambio de nombre de Repeat a Foreach

En la versión de esquema anterior, se recibieron numerosos comentarios de los clientes en que se indicaba que la instrucción **Repeat** era confusa y no captaba adecuadamente que **Repeat** era realmente para cada bucle. Por ello, el nombre de **Repeat** se ha cambiado por **Foreach**. Por ejemplo, anteriormente escribiría:

```
{
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
}
```

Ahora debe escribir:

```
{
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
}
```

Anteriormente, la función `@repeatItem()` se utilizaba para hacer referencia al elemento actual que se itera. Esta función ahora se reduce a `@item()`. 

### <a name="referencing-the-outputs-of-the-foreach"></a>Referencia a las salidas de Foreach
Para simplificarlo aún más, las salidas de las acciones de **Foreach** no se encapsulan en un objeto llamado **repeatItems**. Mientras que las salidas de la instrucción Repeat anterior eran:

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

Ahora, estas salidas son:

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Al hacer referencia a estas salidas, para obtener el cuerpo de la acción, debe hacer lo siguiente:

```
{
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
}
```

Ahora puede hacer esto en su lugar:

```
{
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
}
```

Con estos cambios se eliminan las funciones `@repeatItem()`, `@repeatBody()` y `@repeatOutputs()`.

## <a name="3-native-http-listener"></a>3. Agente de escucha HTTP nativo
Las funcionalidades de agente de escucha HTTP ahora están integradas, así que ya no necesita implementar una aplicación de API de agente de escucha HTTP. Consulte aquí [los detalles completos de cómo hacer que los puntos de conexión de Aplicaciones lógicas se pueden llamar](../logic-apps/logic-apps-http-endpoint.md). 

Con estos cambios, se elimina la función `@accessKeys()` y se reemplaza por la función `@listCallbackURL()`, a efectos de obtener el punto de conexión (cuando sea necesario). Además, ahora debe definir al menos un desencadenador en la aplicación lógica. Si desea `/run` el flujo de trabajo, debe tener uno de estos desencadenadores: `manual`, `apiConnectionWebhook` o `httpWebhook`.

## <a name="4-calling-child-workflows"></a>4. Llamada a flujos de trabajo secundarios
Anteriormente, para llamar a los flujos de trabajo secundarios era necesario ir a cada uno de ellos, obtener el token de acceso y luego pegarlo en la definición de la aplicación lógica deseada. Con la nueva versión de esquema, el motor de Logic Apps genera automáticamente una firma SAS en tiempo de ejecución del flujo de trabajo secundario, lo que significa que no tendrá que pegar ningún secreto en la definición.  Aquí tiene un ejemplo:

```
"mynestedwf": {
    "type": "workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName": "myendpointtrigger"
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

Por último, hay cambios necesarios en el flujo de trabajo secundario. Mientras que antes podía llamar a un flujo de trabajo secundario directamente, ahora debe definir un punto de conexión de desencadenador en el flujo de trabajo para que el elemento primario realice la llamada. Por lo general, esto significa agregar un desencadenador del tipo **manual** y luego usarlo en la definición del elemento primario. Tenga en cuenta que la propiedad `host` tiene un valor `triggerName`, ya que siempre debe especificar el desencadenador que va a invocar.

## <a name="other-changes"></a>Otros cambios
### <a name="new-queries-property"></a>Nueva propiedad queries
Todos los tipos de acción admiten una nueva entrada llamada **queries**. Esta entrada puede ser un objeto estructurado en lugar de tener que ensamblar la cadena a mano.

### <a name="parse-function-renamed"></a>Cambio de nombre de la función parse()
Pronto se van a agregar más tipos de contenido, por lo que se cambia el nombre de la función `parse()` por `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Próximamente: API de Enterprise Integration
Aún no existen versiones administradas de API de Enterprise Integration (como AS2). Estas API se encontrarán disponibles pronto, como se explica en el [mapa de ruta](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). Mientras tanto, puede usar sus API de BizTalk implementadas existentes mediante la acción HTTP, como se ha explicado anteriormente en "Uso de las aplicaciones de API ya implementadas".


