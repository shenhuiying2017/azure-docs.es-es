---
title: Notificaciones de cambios de DocumentDB con Logic Apps | Microsoft Docs
description: .
keywords: "notificación de cambio"
services: documentdb
author: hedidin
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 58925d95-dde8-441b-8142-482b487e4bdd
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: rest-api
ms.topic: article
ms.date: 09/23/2016
ms.author: b-hoedid
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 115d35bd56918ad8e93a9032cbff6e84a7b70e0c


---
# <a name="notifications-for-new-or-changed-documentdb-resources-using-logic-apps"></a>Notificaciones para los recursos de DocumentDB nuevos o modificados con Aplicaciones lógicas
Este artículo procede de una pregunta que vi publicada en uno de los foros de la comunidad de Azure DocumentDB. ¿La pregunta era ¿ **DocumentDB admite notificaciones para los recursos modificados**?

He trabajado con BizTalk Server durante muchos años, y este es un escenario muy común cuando se utiliza el [adaptador LOB de WCF](https://msdn.microsoft.com/library/bb798128.aspx). Por lo que decidí ver si podía duplicar esta funcionalidad en DocumentDB para los documentos nuevos o modificados.

En este artículo se ofrece información general sobre los componentes de la solución de notificación de cambio, que incluye un [desencadenador](documentdb-programming.md#trigger) y una [Aplicación lógica](../app-service-logic/app-service-logic-what-are-logic-apps.md). Se proporcionan fragmentos de código importantes en línea y toda la solución está disponible en [GitHub](https://github.com/HEDIDIN/DocDbNotifications).

## <a name="use-case"></a>Caso de uso
La siguiente historia es un caso de uso para este artículo.

DocumentDB es el repositorio para documentos de Fast Healthcare Interoperability Resources (FHIR) Health Level Seven International (HL7). Un hospital almacena datos de los pacientes en la base de datos "Pacientes" de DocumentDB.  Un hospital almacena datos de los pacientes en la base de datos "Pacientes" de DocumentDB. Hay varias colecciones dentro de la base de datos Pacientes; Datos clínicos, Identificación, etc. La información del paciente se encuentra en la identificación.  Tiene una colección denominada a "Paciente".

El departamento de cardiología está realizando el seguimiento de los datos de ejercicio y salud personales. La búsqueda de registros de pacientes nuevos o modificados lleva mucho tiempo. Preguntan al departamento de TI si había una forma en la que puedan recibir una notificación para registros de pacientes nuevos o modificados.  

El departamento de TI informa que puede proporcionarlos fácilmente. También nos dijeron que podrían insertar los documentos en [Almacenamiento de blobs de Azure](https://azure.microsoft.com/services/storage/) para que el departamento de cardiología pudiera obtener acceso a ellos fácilmente.

## <a name="how-the-it-department-solved-the-problem"></a>Solución del problema por parte del departamento de TI
Para crear esta aplicación, el departamento de TI decidió diseñarla primero.  Lo bueno que tiene utilizar el modelo de proceso empresarial y Notation (BPMN) es que los usuarios técnicos y no técnicos pueden comprenderlo fácilmente. Este proceso de notificación completo se considera un proceso empresarial. 

## <a name="high-level-view-of-notification-process"></a>Vista de alto nivel del proceso de notificación
1. Comience con una Aplicación lógica que tenga un desencadenador de temporizador. De forma predeterminada, el desencadenador se ejecuta cada hora.
2. A continuación, aplique la POST HTTP a la Aplicación lógica.
3. La Aplicación lógica realiza todo el trabajo.

![Vista de alto nivel](./media/documentdb-change-notification/high-level-view.png)

### <a name="lets-take-a-look-at-what-this-logic-app-does"></a>Echemos un vistazo a lo que hace esta Aplicación lógica
Si observamos la figura siguiente, hay varios pasos en el flujo de trabajo de la Aplicación lógica.

![Proceso de la lógica principal](./media/documentdb-change-notification/main-logic-app-process.png)

Los pasos son los siguientes:

1. Debe obtener la instancia DateTime de UTC actual de una aplicación de API.  El valor predeterminado es una hora anterior.
2. La instancia DateTime de UTC se convierte a un formato de marca de tiempo de Unix. Este es el formato predeterminado para las marcas en DocumentDB.
3. Aplica POST al el valor en una aplicación de API, que realiza una consulta de DocumentDB. El valor se utiliza en una consulta.
   
    ```SQL
         SELECT * FROM Patients p WHERE (p._ts >= @unixTimeStamp)
    ```
   
   > [!NOTE]
   > El _ts representa los metadatos de la marca de tiempo para todos los recursos de DocumentDB.
   > 
   > 
4. Si se encuentran documentos, el cuerpo de respuesta se envía al Almacenamiento de blobs de Azure.
   
   > [!NOTE]
   > Almacenamiento de blobs precisa de una cuenta de almacenamiento de Azure. Debe aprovisionar una cuenta de almacenamiento de blobs de Azure y agregar un nuevo blog denominado Pacientes. Para más información, vea [Información sobre cuentas Azure Storage](../storage/storage-create-storage-account.md) e [Introducción a Azure Blob Storage](../storage/storage-dotnet-how-to-use-blobs.md).
   > 
   > 
5. Por último, se envía un correo electrónico que informa al destinatario del número de documentos encontrados. Si no se encuentra ningún documento, el cuerpo del correo electrónico sería "0 documentos encontrados". 

Ahora que tiene una idea de lo que hace el flujo de trabajo, echemos un vistazo a la implementación.

### <a name="lets-start-with-the-main-logic-app"></a>Comencemos con la Aplicación lógica principal
Si no está familiarizado con Logic Apps, están disponibles en [Azure Marketplace](https://portal.azure.com/) y puede obtener más información sobre ellas en [¿Qué es Logic Apps?](../app-service-logic/app-service-logic-what-are-logic-apps.md)

Cuando se crea una nueva Aplicación lógica, se le preguntará: **¿cómo le gustaría comenzar?**

Al hacer clic dentro del cuadro de texto, podrá elegir entre varios eventos. Para esta Aplicación lógica, seleccione **Manual: cuando se recibe una solicitud HTTP** tal como se muestra a continuación.

![Inicio](./media/documentdb-change-notification/starting-off.png)

### <a name="design-view-of-your-completed-logic-app"></a>Vista de diseño de la Aplicación lógica completada
Vamos a avanzar y examinar la vista de diseño completada de la Aplicación lógica, que se denomina DocDB.

![Flujo de trabajo de Aplicación lógica](./media/documentdb-change-notification/workflow-expanded.png)

Al editar las acciones en el Diseñador de aplicaciones lógicas, tiene la opción de seleccionar **Salidas** de la solicitud HTTP o de la acción anterior, como se muestra en la siguiente acción de sendMail.

![Elija salidas](./media/documentdb-change-notification/choose-outputs.png)

Antes de cada acción del flujo de trabajo, puede tomar una decisión (**Agregar una acción** o **Agregar una condición**, como se muestra en la ilustración siguiente).

![Tomar una decisión](./media/documentdb-change-notification/add-action-or-condition.png)

Si selecciona **Agregar una condición**, se muestra un formulario, como aparece en la figura siguiente, para escribir su lógica.  Esto es en esencia una regla de negocio.  Si hace clic dentro de un campo, tendrá la opción de seleccionar parámetros de la acción anterior. También puede especificar los valores directamente.

![Agregar una condición](./media/documentdb-change-notification/condition1.png)

> [!NOTE]
> También tiene la capacidad de especificar todo en la vista Código.
> 
> 

Echemos un vistazo a la Aplicación lógica completada en la vista Código.  

```JSON

       "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
    "actions": {
        "Conversion": {
            "conditions": [
                {
                    "dependsOn": "GetUtcDate"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "currentdateTime": "@{body('GetUtcDate')}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "Createfile": {
            "conditions": [
                {
                    "expression": "@greater(length(body('GetDocuments')), 0)"
                },
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "@body('GetDocuments')",
                "host": {
                    "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['azureblob']['connectionId']"
                    }
                },
                "method": "post",
                "path": "/datasets/default/files",
                "queries": {
                    "folderPath": "/patients",
                    "name": "Patient_@{guid()}.json"
                }
            },
            "type": "ApiConnection"
        },
        "GetDocuments": {
            "conditions": [
                {
                    "dependsOn": "Conversion"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "unixTimeStamp": "@body('Conversion')"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "GetUtcDate": {
            "conditions": [],
            "inputs": {
                "method": "get",
                "queries": {
                    "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "sendMail": {
            "conditions": [
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
                "headers": {
                    "Content-type": "application/x-www-form-urlencoded"
                },
                "method": "POST",
                "uri": "https://api.sendgrid.com/api/mail.send.json"
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {
        "Results": {
            "type": "String",
            "value": "@{int(length(body('GetDocuments')))} Records Found"
        }
    },
    "parameters": {
        "$connections": {
            "defaultValue": {},
            "type": "Object"
        },
        "fromAddress": {
            "defaultValue": "user@msn.com",
            "type": "String"
        },
        "toAddress": {
            "defaultValue": "XXXXX@XXXXXXX.net",
            "type": "String"
        }
    },
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {
                    "properties": {},
                    "required": [],
                    "type": "object"
                }
            },
            "type": "Manual"
        }

```

Si no está familiarizado con lo que representa las diferentes secciones en el código, puede ver la documentación [Lenguaje de definición del flujo de trabajo de la Aplicación lógica](http://aka.ms/logicappsdocs) .

Para este flujo de trabajo, usa un [desencadenador de Webhook HTTP](https://sendgrid.com/blog/whats-webhook/). Si observa el código anterior, verá parámetros como en el ejemplo siguiente.

```C#

    =@{triggerBody()['Subject']}

```

`triggerBody()` representa los parámetros que se incluyen en el cuerpo de una solicitud POST de REST en la API de REST de la Aplicación lógica. `()['Subject']` representa el campo. Todos estos parámetros constituyen el cuerpo con formato JSON. 

> [!NOTE]
> Mediante un enlace web, puede tener acceso total al encabezado y el cuerpo de la solicitud del desencadenador. En esta aplicación desea el cuerpo.
> 
> 

Como se mencionó anteriormente, puede utilizar el diseñador para asignar parámetros o hacerlo en la vista Código.
Si lo hace en la vista Código, defina las propiedades que requieren un valor, como se muestra en el ejemplo de código siguiente. 

```JSON

    "triggers": {
        "manual": {
            "inputs": {
            "schema": {
                "properties": {
            "Subject": {
                "type" : "String"    

            }
            },
                "required": [
            "Subject"
                 ],
                "type": "object"
            }
            },
            "type": "Manual"
        }
        }
```

Lo que está haciendo es crear un esquema JSON que se pasa en el cuerpo de POST HTTP.
Para activar el desencadenador, necesitará una dirección URL de devolución de llamada.  Aprenderá a cómo generarla más adelante en el tutorial.  

## <a name="actions"></a>Acciones
Veamos lo que hace cada acción en nuestra Aplicación lógica.

### <a name="getutcdate"></a>GetUTCDate
**Vista de diseñador**

![](./media/documentdb-change-notification/getutcdate.png)

**Vista de código**

```JSON

    "GetUtcDate": {
            "conditions": [],
            "inputs": {
            "method": "get",
            "queries": {
                "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
            },
            "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
            },
            "metadata": {
            "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
            },
            "type": "Http"
        },

```

Esta acción HTTP realiza una operación GET.  Llama al método GetUtcDate de aplicación de API. El identificador URI usa la propiedad 'GetUtcDate_HoursBack' que se pasa al cuerpo del desencadenador.  El valor 'GetUtcDate_HoursBack' se establece en la primera Aplicación lógica. Aprenderá más acerca de la aplicación lógica del desencadenador más adelante en el tutorial.

Esta acción llama su aplicación de API para devolver el valor de cadena de fecha UTC.

#### <a name="operations"></a>Operaciones
**Solicitud**

```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization",
        "method": "get",
        "queries": {
          "hoursBack": "24"
        }
    }

```

**Respuesta**

```JSON

    {
        "statusCode": 200,
        "headers": {
          "pragma": "no-cache",
          "cache-Control": "no-cache",
          "date": "Fri, 26 Feb 2016 15:47:33 GMT",
          "server": "Microsoft-IIS/8.0",
          "x-AspNet-Version": "4.0.30319",
          "x-Powered-By": "ASP.NET"
        },
        "body": "Fri, 15 Jan 2016 23:47:33 GMT"
    }

```

El siguiente paso es convertir el valor de fecha y hora UTC en la marca de tiempo de Unix, que es un tipo Double de .NET.

### <a name="conversion"></a>Conversión
##### <a name="designer-view"></a>Vista de diseñador
![Conversión](./media/documentdb-change-notification/conversion.png)

##### <a name="code-view"></a>Vista de código
```JSON

    "Conversion": {
        "conditions": [
        {
            "dependsOn": "GetUtcDate"
        }
        ],
        "inputs": {
        "method": "post",
        "queries": {
            "currentDateTime": "@{body('GetUtcDate')}"
        },
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
        },
        "metadata": {
        "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
        },
        "type": "Http"
    },

```

En este paso, se pasa el valor devuelto de GetUTCDate.  Hay una condición dependsOn, lo que significa que la acción GetUTCDate debe completarse correctamente. Si no es así, se omite esta acción. 

Esta acción llama a la aplicación de API para controlar la conversión.

#### <a name="operations"></a>Operaciones
##### <a name="request"></a>Solicitud
```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion",
        "method": "post",
        "queries": {
        "currentDateTime": "Fri, 15 Jan 2016 23:47:33 GMT"
        }
    }   
```

##### <a name="response"></a>Respuesta
```JSON

    {
        "statusCode": 200,
        "headers": {
          "pragma": "no-cache",
          "cache-Control": "no-cache",
          "date": "Fri, 26 Feb 2016 15:47:33 GMT",
          "server": "Microsoft-IIS/8.0",
          "x-AspNet-Version": "4.0.30319",
          "x-Powered-By": "ASP.NET"
        },
        "body": 1452901653
    }
```

En la siguiente acción, se realizará una operación POST en nuestras aplicación de API.

### <a name="getdocuments"></a>GetDocuments
##### <a name="designer-view"></a>Vista de diseñador
![Obtener documentos](./media/documentdb-change-notification/getdocuments.png)

##### <a name="code-view"></a>Vista de código
```JSON

    "GetDocuments": {
        "conditions": [
        {
            "dependsOn": "Conversion"
        }
        ],
        "inputs": {
        "method": "post",
        "queries": {
            "unixTimeStamp": "@{body('Conversion')}"
        },
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
        },
        "metadata": {
        "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
        },
        "type": "Http"
    },

```

Para la acción GetDocuments, va a pasar el cuerpo de respuesta de la acción de conversión. Se trata de un parámetro en el identificador URI:

```C#

    unixTimeStamp=@{body('Conversion')}

```

La acción QueryDocuments realiza una operación HTTP POST en la aplicación de API. 

El método llamado es **QueryForNewPatientDocuments**.

#### <a name="operations"></a>Operaciones
##### <a name="request"></a>Solicitud
```JSON

    {
        "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient",
        "method": "post",
        "queries": {
        "unixTimeStamp": "1452901653"
        }
    }
```

##### <a name="response"></a>Respuesta
```JSON

    {
        "statusCode": 200,
        "headers": {
        "pragma": "no-cache",
        "cache-Control": "no-cache",
        "date": "Fri, 26 Feb 2016 15:47:35 GMT",
        "server": "Microsoft-IIS/8.0",
        "x-AspNet-Version": "4.0.30319",
        "x-Powered-By": "ASP.NET"
        },
        "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
            "status": "generated",
            "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
            {
                "use": "usual",
                "type": {
                "coding": [
                    {
                    "system": "http://hl7.org/fhir/v2/0203",
                    "code": "MR"
                    }
                ]
                },
                "system": "urn:oid:2.16.840.1.113883.19.5",
                "value": "12345"
            }
            ],
            "active": true,
            "name": [
            {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

La acción siguiente es guardar los documentos en [Almacenamiento de blobs de Azure](https://azure.microsoft.com/services/storage/). 

> [!NOTE]
> Almacenamiento de blobs precisa de una cuenta de almacenamiento de Azure. Debe aprovisionar una cuenta de almacenamiento de blobs de Azure y agregar un nuevo blog denominado Pacientes. Para obtener más información, vea [Introducción al Almacenamiento de blobs de Azure](../storage/storage-dotnet-how-to-use-blobs.md).
> 
> 

### <a name="create-file"></a>Creación de archivo
##### <a name="designer-view"></a>Vista de diseñador
![Creación de archivo](./media/documentdb-change-notification/createfile.png)

##### <a name="code-view"></a>Vista de código
```JSON

    {
    "host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

Se genera el código a partir de la acción en el diseñador. No tiene que modificar el código.

Si no está familiarizado con el uso de la API de blobs de Azure, consulte [Get started with the Azure blob storage API](../connectors/connectors-create-api-azureblobstorage.md)(Introducción a la API de Almacenamiento de blobs de Azure).

#### <a name="operations"></a>Operaciones
##### <a name="request"></a>Solicitud
```JSON

    "host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": "\"00007d01-0000-0000-0000-56b79ffc0000\"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },….


```

##### <a name="response"></a>Respuesta
```JSON

    {
        "statusCode": 200,
        "headers": {
        "pragma": "no-cache",
        "x-ms-request-id": "2b2f7c57-2623-4d71-8e53-45c26b30ea9d",
        "cache-Control": "no-cache",
        "date": "Fri, 26 Feb 2016 15:47:36 GMT",
        "set-Cookie": "ARRAffinity=29e552cea7db23196f7ffa644003eaaf39bc8eb6dd555511f669d13ab7424faf;Path=/;Domain=127.0.0.1",
        "server": "Microsoft-HTTPAPI/2.0",
        "x-AspNet-Version": "4.0.30319",
        "x-Powered-By": "ASP.NET"
        },
        "body": {
        "Id": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE",
        "Name": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "DisplayName": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "Path": "/Patient/Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
        "LastModified": "2016-02-26T15:47:36.215Z",
        "Size": 65647,
        "MediaType": "application/octet-stream",
        "IsFolder": false,
        "ETag": "\"c-g_a-1OtaH-kNQ4WBoXLp3Zv9s/MTQ1NjUwMTY1NjIxNQ\"",
        "FileLocator": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE"
        }
    }
```

El último paso consiste en enviar una notificación de correo electrónico

### <a name="sendemail"></a>sendEmail
##### <a name="designer-view"></a>Vista de diseñador
![Enviar correo electrónico](./media/documentdb-change-notification/sendemail.png)

##### <a name="code-view"></a>Vista de código
```JSON


    "sendMail": {
        "conditions": [
        {
            "dependsOn": "GetDocuments"
        }
        ],
        "inputs": {
        "body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
        "headers": {
            "Content-type": "application/x-www-form-urlencoded"
        },
        "method": "POST",
        "uri": "https://api.sendgrid.com/api/mail.send.json"
        },
        "type": "Http"
    }
```

En esta acción, envía una notificación de correo electrónico.  Use [SendGrid](https://sendgrid.com/marketing/sendgrid-services?cvosrc=PPC.Bing.sendgrib&cvo_cid=SendGrid%20-%20US%20-%20Brand%20-%20&mc=Paid%20Search&mcd=BingAds&keyword=sendgrib&network=o&matchtype=e&mobile=&content=&search=1&utm_source=bing&utm_medium=cpc&utm_term=%5Bsendgrib%5D&utm_content=%21acq%21v2%2134335083397-8303227637-1649139544&utm_campaign=SendGrid+-+US+-+Brand+-+%28English%29).   

El código se generó con una plantilla para la Aplicación lógica y SendGrid que se encuentra en el repositorio [101-logic-app-sendgrid Github](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sendgrid).

La operación HTTP es una POST. 

Los parámetros de autorización están en las propiedades del desencadenador

```JSON

    },
        "sendgridPassword": {
             "type": "SecureString"
         },
         "sendgridUsername": {
            "type": "String"
         }

        In addition, other parameters are static values set in the Parameters section of the Logic App. These are:
        },
        "toAddress": {
            "defaultValue": "XXXX@XXXX.com",
            "type": "String"
        },
        "fromAddress": {
            "defaultValue": "XXX@msn.com",
            "type": "String"
        },
        "emailBody": {
            "defaultValue": "@{string(concat(int(length(actions('QueryDocuments').outputs.body)) Records Found),'/n', actions('QueryDocuments').outputs.body)}",
            "type": "String"
        },

```

El emailBody concatena el número de documentos devueltos a partir de la consulta, que puede ser "0" o más, junto con, "Se encontraron registros". El resto de los parámetros se establecen a partir de los parámetros del desencadenador.

Esta acción depende de la acción **GetDocuments** .

#### <a name="operations"></a>Operaciones
##### <a name="request"></a>Solicitud
```JSON

    {
        "uri": "https://api.sendgrid.com/api/mail.send.json",
        "method": "POST",
        "headers": {
        "Content-type": "application/x-www-form-urlencoded"
        },
        "body": "api_user=azureuser@azure.com&api_key=Biz@Talk&from=user@msn.com&to=XXXX@XXXX.com&subject=New Patients&text=37 Documents Found"
    }

```

##### <a name="response"></a>Respuesta
```JSON

    {
        "statusCode": 200,
        "headers": {
        "connection": "keep-alive",
        "x-Frame-Options": "DENY,DENY",
        "access-Control-Allow-Origin": "https://sendgrid.com",
        "date": "Fri, 26 Feb 2016 15:47:35 GMT",
        "server": "nginx"
        },
        "body": {
        "message": "success"
        }
    }
```

Por último, desea poder ver los resultados de la Aplicación lógica en el Portal de Azure. Para ello, agregue un parámetro a la sección de resultados.

```JSON

    "outputs": {
        "Results": {
            "type": "String",
            "value": "@{int(length(actions('QueryDocuments').outputs.body))} Records Found"
        }

```

Esto devuelve el mismo valor que se envía en el cuerpo del correo electrónico. En la siguiente figura se muestra un ejemplo donde "Se encontraron 29 registros".

![Results](./media/documentdb-change-notification/logic-app-run.png)

## <a name="metrics"></a>Métricas
Puede configurar la supervisión de la Aplicación lógica principal en el portal. Esto le permite ver la latencia de ejecución y otros eventos, como se muestra en la figura siguiente.

![](./media/documentdb-change-notification/metrics.png)

## <a name="docdb-trigger"></a>Desencadenador DocDb
Esta aplicación lógica es el desencadenador que inicia el flujo de trabajo en la Aplicación lógica principal.

La figura siguiente muestra la vista Diseñador.

![](./media/documentdb-change-notification/trigger-recurrence.png)

```JSON

    {
        "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
        "actions": {
        "Http": {
            "conditions": [],
            "inputs": {
            "body": {
                "EmailTo": "XXXXXX@XXXXX.net",
                "GetUtcDate_HoursBack": "24",
                "Subject": "New Patients",
                "sendgridPassword": "********",
                "sendgridUsername": "azureuser@azure.com"
            },
            "method": "POST",
            "uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c"
            },
            "type": "Http"
        }
        },
        "contentVersion": "1.0.0.0",
        "outputs": {
        "Results": {
            "type": "String",
            "value": "@{body('Http')['status']}"
        }
        },
        "parameters": {},
        "triggers": {
        "recurrence": {
            "recurrence": {
            "frequency": "Hour",
            "interval": 24
            },
            "type": "Recurrence"
        }
        }
    }

```

El desencadenador se establece para una periodicidad de veinticuatro horas. La acción es una solicitud HTTP POST que utiliza la dirección URL de devolución de llamada para la Aplicación lógica principal. El cuerpo contiene los parámetros que se especifican en el esquema de JSON. 

#### <a name="operations"></a>Operaciones
##### <a name="request"></a>Solicitud
```JSON

    {
        "uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c",
        "method": "POST",
        "body": {
        "EmailTo": "XXXXXX@XXXXX.net",
        "GetUtcDate_HoursBack": "24",
        "Subject": "New Patients",
        "sendgridPassword": "********",
        "sendgridUsername": "azureuser@azure.com"
        }
    }

```

##### <a name="response"></a>Respuesta
```JSON

    {
        "statusCode": 202,
        "headers": {
        "pragma": "no-cache",
        "x-ms-ratelimit-remaining-workflow-writes": "7486",
        "x-ms-ratelimit-burst-remaining-workflow-writes": "1248",
        "x-ms-request-id": "westus:2d440a39-8ba5-4a9c-92a6-f959b8d2357f",
        "cache-Control": "no-cache",
        "date": "Thu, 25 Feb 2016 21:01:06 GMT"
        }
    }
```

Ahora veamos la aplicación de API.

## <a name="docdbnotificationapi"></a>DocDBNotificationApi
Aunque hay varias operaciones en la aplicación, solo va a utilizar tres.

* GetUTCDate
* ConvertToTimeStamp
* QueryForNewPatientDocuments

### <a name="docdbnotificationapi-operations"></a>DocDBNotificationApi Operations
Echemos un vistazo a la documentación de Swagger

> [!NOTE]
> Para poder llamar a las operaciones de forma externa, debe agregar un valor de origen permitido CORS de "*" (sin comillas) en la configuración de la aplicación de API tal como se muestra en la figura siguiente.
> 
> 

![Configuración de Cors](./media/documentdb-change-notification/cors.png)

#### <a name="getutcdate"></a>GetUTCDate
![G](./media/documentdb-change-notification/getutcdateswagger.png)

#### <a name="converttotimestamp"></a>ConvertToTimeStamp
![Obtener fecha de UTC](./media/documentdb-change-notification/converion-swagger.png)

#### <a name="queryfornewpatientdocuments"></a>QueryForNewPatientDocuments
![Consultar](./media/documentdb-change-notification/patientswagger.png)

Echemos un vistazo al código que hay detrás de esta operación.

#### <a name="getutcdate"></a>GetUTCDate
```C#

    /// <summary>
    /// Gets the current UTC Date value
    /// </summary>
    /// <returns></returns>
    [H ttpGet]
    [Metadata("GetUtcDate", "Gets the current UTC Date value minus the Hours Back")]
    [SwaggerOperation("GetUtcDate")]
    [SwaggerResponse(HttpStatusCode.OK, type: typeof (string))]
    [SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
    public string GetUtcDate(
       [Metadata("Hours Back", "How many hours back from the current Date Time")] int hoursBack)
    {


        return DateTime.UtcNow.AddHours(-hoursBack).ToString("r");
    }
```

Esta operación devuelve simplemente los resultados de la instancia DateTime de UTC actual menos el valor HoursBack.

#### <a name="converttotimestamp"></a>ConvertToTimeStamp
``` C#

        /// <summary>
        ///     Converts DateTime to double
        /// </summary>
        /// <param name="currentdateTime"></param>
        /// <returns></returns>
        [Metadata("Converts Universal DateTime to number")]
        [SwaggerResponse(HttpStatusCode.OK, null, typeof (double))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "DateTime is invalid")]
        [SwaggerResponse(HttpStatusCode.InternalServerError)]
        [SwaggerOperation(nameof(ConvertToTimestamp))]
        public double ConvertToTimestamp(
            [Metadata("currentdateTime", "DateTime value to convert")] string currentdateTime)
        {
            double result;

            try
            {
                var uncoded = HttpContext.Current.Server.UrlDecode(currentdateTime);

                var newDateTime = DateTime.Parse(uncoded);
                //create Timespan by subtracting the value provided from the Unix Epoch
                var span = newDateTime - new DateTime(1970, 1, 1, 0, 0, 0, 0).ToLocalTime();

                //return the total seconds (which is a UNIX timestamp)
                result = span.TotalSeconds;
            }
            catch (Exception e)
            {
                throw new Exception("unable to convert to Timestamp", e.InnerException);
            }

            return result;
        }

```

Esta operación convierte la respuesta de la operación GetUtcDate en un valor Double.

#### <a name="queryfornewpatientdocuments"></a>QueryForNewPatientDocuments
```C#

        /// <summary>
        ///     Query for new Patient Documents
        /// </summary>
        /// <param name="unixTimeStamp"></param>
        /// <returns>IList</returns>
        [Metadata("QueryForNewDocuments",
            "Query for new Documents where the Timestamp is greater than or equal to the DateTime value in the query parameters."
            )]
        [SwaggerOperation("QueryForNewDocuments")]
        [SwaggerResponse(HttpStatusCode.OK, type: typeof (Task<IList<Document>>))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "The syntax of the SQL Statement is incorrect")]
        [SwaggerResponse(HttpStatusCode.NotFound, "No Documents were found")]
        [SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
        // ReSharper disable once ConsiderUsingAsyncSuffix
        public IList<Document> QueryForNewPatientDocuments(
            [Metadata("UnixTimeStamp", "The DateTime value used to search from")] double unixTimeStamp)
        {
            var context = new DocumentDbContext();
            var filterQuery = string.Format(InvariantCulture, "SELECT * FROM Patient p WHERE p._ts >=  {0}",
                unixTimeStamp);
            var options = new FeedOptions {MaxItemCount = -1};


            var collectionLink = UriFactory.CreateDocumentCollectionUri(DocumentDbContext.DatabaseId,
                DocumentDbContext.CollectionId);

            var response =
                context.Client.CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();

            return response.ToList();
    }

```

Esta operación utiliza el [SDK para .NET de DocumentDB](documentdb-sdk-dotnet.md) para crear una consulta de documento. 

```C#
     CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();
```

Se pasa la respuesta de la operación ConvertToTimeStamp (unixTimeStamp). La operación devuelve una lista de documentos, `IList<Document>`.

Anteriormente hablamos sobre CallbackURL. Para iniciar el flujo de trabajo en la Aplicación lógica principal, debe llamar mediante CallbackURL.

## <a name="callbackurl"></a>CallbackURL
Para empezar, necesitará el token de Azure AD.  Puede ser difícil de obtener este token. Buscaba un método sencillo y Jeff Hollan, que es un administrador de programas de la Aplicación lógica de Azure, me recomendó utilizar [armclient](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) en PowerShell.  Puede instalarlo siguiendo las instrucciones proporcionadas.

Las operaciones que se van a utilizar son Iniciar sesión y Llamar a la API de ARM.

Iniciar sesión: use las mismas credenciales para iniciar sesión que en el Portal de Azure. 

La operación Llamar a la API de ARM generará CallBackURL.

En PowerShell, se realizará la llamada de la siguiente forma:    

```powershell

    ArmClient.exe post https://management.azure.com/subscriptions/[YOUR SUBSCRIPTION ID/resourcegroups/[YOUR RESOURCE GROUP]/providers/Microsoft.Logic/workflows/[YOUR LOGIC APP NAME/triggers/manual/listcallbackurl?api-version=2015-08-01-preview

```

El resultado debería tener este aspecto:

```powershell

    https://prod-02.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-prevaiew&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=XXXXXXXXXXXXXXXXXXX

```

Puede usar una herramienta como [postman](http://www.getpostman.com/) para probar la Aplicación lógica principal tal como se muestra en la figura siguiente.

![postman](./media/documentdb-change-notification/newpostman.png)

En la tabla siguiente aparecen los parámetros de desencadenador que componen el cuerpo de la aplicación lógica del desencadenador DocDB.

| Parámetro | Description |
| --- | --- |
| GetUtcDate_HoursBack |Se utiliza para establecer el número de horas para la fecha de inicio de la búsqueda |
| sendgridUsername |Se utiliza para establecer el número de horas para la fecha de inicio de la búsqueda |
| sendgridPassword |El nombre de usuario del correo electrónico de Send Grid |
| EmailTo |La dirección de correo electrónico que recibirá la notificación de correo electrónico |
| Asunto |El asunto del correo electrónico |

## <a name="viewing-the-patient-data-in-the-azure-blob-service"></a>Visualización de los datos del paciente en el servicio BLOB de Azure
Vaya a la cuenta de Almacenamiento de Azure y seleccione Blobs en servicios como se muestra en la figura siguiente.

![Cuenta de almacenamiento](./media/documentdb-change-notification/docdbstorageaccount.png) 

Podrá ver la información del archivo blob del paciente tal como se muestra a continuación.

![Servicio BLOB](./media/documentdb-change-notification/blobservice.png)

## <a name="summary"></a>Resumen
En este tutorial, ha aprendido lo siguiente:

* Es posible implementar notificaciones en DocumentDB.
* Mediante el uso de Aplicaciones lógicas, puede automatizar el proceso.
* Mediante el uso de Aplicaciones lógicas, puede reducir el tiempo necesario para proporcionar una aplicación.
* Mediante el protocolo HTTP, puede consumir fácilmente una aplicación de API dentro de una Aplicación lógica.
* Puede crear fácilmente una CallBackURL que reemplace a la escucha HTTP.
* Puede crear fácilmente flujos de trabajo personalizados con el Diseñador de aplicaciones lógicas.

La clave es planificar con antelación y modelar el flujo de trabajo.

## <a name="next-steps"></a>Pasos siguientes
Descargue y use el código de la Aplicación lógica proporcionado en [Github](https://github.com/HEDIDIN/DocDbNotifications). Le invito a crear la aplicación y enviar los cambios al repositorio. 

Para obtener más información sobre DocumentDB, visite la [ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/documentdb/).




<!--HONumber=Nov16_HO3-->


