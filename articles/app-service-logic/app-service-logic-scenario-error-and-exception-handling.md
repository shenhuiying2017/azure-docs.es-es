<properties
    pageTitle="Registro y control de errores en Logic Apps | Microsoft Azure"
    description="Visualización de un caso de uso real del control y registro de errores avanzado con Logic Apps"
    keywords=""
    services="logic-apps"
    authors="hedidin"
    manager=""
    editor=""
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="b-hoedid"/>

# Registro y control de errores en Logic Apps

En este artículo se describe cómo extender una aplicación lógica para un mejor control de excepciones. Este es un caso de uso real y constituye nuestra respuesta a la pregunta: ¿Admite Logic Apps control de excepciones y errores?

>[AZURE.NOTE] La versión actual de la característica de Logic Apps del Servicio de aplicaciones de Microsoft Azure proporciona una plantilla estándar para las respuestas de la acción. Esto incluye la validación interna y las respuestas de error devueltas desde una aplicación de API.

## Información general sobre el caso de uso y el escenario

La siguiente historia es un caso de uso para este artículo. Una conocida organización sanitaria nos contrató para que desarrolláramos una solución de Azure que crearía un portal para pacientes mediante Microsoft Dynamics CRM Online. Necesitaban enviar registros de citas entre el portal para pacientes de Dynamics CRM Online y Salesforce. Se nos pidió que usáramos la norma [FHIR HL7](http://www.hl7.org/implement/standards/fhir/) para todos los registros de paciente.

El proyecto tenía dos requisitos principales:

 -  Un método para registrar las entradas enviadas desde el portal de Dynamics CRM Online
 -  Una manera de ver todos los errores que se producían dentro del flujo de trabajo.


## ¿Cómo resolvimos el problema?

>[AZURE.TIP] Puede ver un vídeo de alto nivel del proyecto en la página de [Integration User Group](http://www.integrationusergroup.com/do-logic-apps-support-error-handling/ "Integration User Group").

Elegimos [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/ "DocumentDB de Azure") como repositorio para los registros y entradas de error (en DocumentDB se hace referencia a los registros como documentos). Puesto que Logic Apps tiene una plantilla estándar para todas las respuestas, no sería necesario crear un esquema personalizado. Podíamos crear una aplicación de API para **insertar** y **consultar** registros de errores y registros. También podíamos definir un esquema para cada uno de ellos dentro de la aplicación de API.

Otro requisito era que se purgaran los registros después de una determinada fecha. DocumentDB presenta una propiedad llamada [período de vida](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Período de vida") (TTL), que nos permitió establecer un valor de **período de vida** para cada registro o colección. De esta manera se suprimía la necesidad de eliminar manualmente los registros en DocumentDB.

### Creación de la aplicación lógica

El primer paso es crear una aplicación lógica y cargarla en el diseñador. En este ejemplo, vamos a usar aplicaciones lógicas primarias y secundarias. Supongamos que ya ha creado la primaria y va a crear una aplicación lógica secundaria.

Puesto que vamos a registrar los registros procedentes de Dynamics CRM Online, vamos a comenzar por lo más importante. Debemos usar un desencadenador de solicitud, ya que la aplicación lógica primaria desencadenará la secundaria.

> [AZURE.IMPORTANT] Para completar este tutorial, deberá crear una base de datos de DocumentDB y dos colecciones (Registro y Errores).

### Desencadenador de aplicación lógica

Vamos a usar un desencadenador de solicitud tal como se muestra en el ejemplo siguiente.

```` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

````


### Pasos

Es necesario registrar el origen (solicitud) del registro del paciente desde el portal de Dynamics CRM Online.

1. Debemos obtener un registro de nueva cita de Dynamics CRM Online. El desencadenador procedente de CRM nos proporcionará los valores de **CRM PatentId**, **tipo de registro**, **registro nuevo o actualizado** (valor booleano nuevo o actualizado) y **SalesforceId**. El valor de **SalesforceId** puede ser null porque solo se utiliza para obtener una actualización. Se obtendrá el registro de CRM mediante los valores de **PatientID** y **tipo de registro** de CRM.
1. A continuación, es necesario agregar a nuestra aplicación de API de DocumentDB la operación **InsertLogEntry**, tal y como se muestra en las ilustraciones siguientes:


#### Vista de diseñador para insertar entrada de registro

![Insertar entrada de registro](./media/app-service-logic-scenario-error-and-exception-handling/lognewpatient.png)

#### Vista de diseñador para insertar entrada de error
![Insertar entrada de registro](./media/app-service-logic-scenario-error-and-exception-handling/insertlogentry.png)

#### Comprobación de errores en la creación de registro

![Condición](./media/app-service-logic-scenario-error-and-exception-handling/condition.png)


## Código fuente de la aplicación lógica

>[AZURE.NOTE]  Los siguientes son solo ejemplos. Como este tutorial se basa en una implementación actualmente en producción, es posible que el valor de un **nodo de origen** no muestre las propiedades que están relacionadas con la programación de una cita.

### Registro
El siguiente ejemplo de código de aplicación lógica muestra cómo controlar el registro.

#### Entrada de registro
Este es el código fuente de la aplicación lógica para insertar una entrada de registro.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### Solicitud de registro

Este es el mensaje de solicitud de registro publicado en la aplicación de API.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
	    "date": "Fri, 10 Jun 2016 22:31:56 GMT",
	    "operation": "New Patient",
	    "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
	    "providerId": "",
	    "source": "{"Pragma":"no-cache","x-ms-request-id":"e750c9a9-bd48-44c4-bbba-1688b6f8a132","OData-Version":"4.0","Cache-Control":"no-cache","Date":"Fri, 10 Jun 2016 22:31:56 GMT","Set-Cookie":"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1","Server":"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0","X-AspNet-Version":"4.0.30319","X-Powered-By":"ASP.NET","Content-Length":"1935","Content-Type":"application/json; odata.metadata=minimal; odata.streaming=true","Expires":"-1"}"
    	}
    }

```


#### Respuesta de registro

Este es el mensaje de respuesta de registro desde la aplicación de API.

``` json
{
    "statusCode": 200,
    "headers": {
	    "Pragma": "no-cache",
	    "Cache-Control": "no-cache",
	    "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
	    "Server": "Microsoft-IIS/8.0",
	    "X-AspNet-Version": "4.0.30319",
	    "X-Powered-By": "ASP.NET",
	    "Content-Length": "964",
	    "Content-Type": "application/json; charset=utf-8",
	    "Expires": "-1"
    },
    "body": {
	    "ttl": 2592000,
	    "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
	    "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
	    "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
	    "_ts": 1465597936,
	    "_etag": ""0400fc2f-0000-0000-0000-575b3ff00000"",
	    "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
	    "timestamp": "2016-06-10T22:31:56Z",
	    "source": "{"Pragma":"no-cache","x-ms-request-id":"e750c9a9-bd48-44c4-bbba-1688b6f8a132","OData-Version":"4.0","Cache-Control":"no-cache","Date":"Fri, 10 Jun 2016 22:31:56 GMT","Set-Cookie":"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1","Server":"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0","X-AspNet-Version":"4.0.30319","X-Powered-By":"ASP.NET","Content-Length":"1935","Content-Type":"application/json; odata.metadata=minimal; odata.streaming=true","Expires":"-1"}",
	    "operation": "New Patient",
	    "salesforceId": "",
	    "expired": false
    }
}

```

Ahora veamos los pasos de control de errores:


### Control de errores

El siguiente ejemplo de código de Logic Apps muestra cómo puede implementar el control de errores.

#### Creación de un registro de errores

Este es el código fuente de Logic Apps para crear un registro de errores.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}  	       
```

#### Insertar error en DocumentDB: solicitud

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{"Account_Class_vod__c":"PRAC","Account_Status_MED__c":"I","CRM_HUB_ID__c":"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0","Credentials_vod__c","DTC_ID_MED__c":"","Fax":"","FirstName":"A","Gender_vod__c":"","IMS_ID__c":"","LastName":"BAILEY","MasterID_mp__c":"","C_ID_MED__c":"851588","Middle_vod__c":"","NPI_vod__c":"","PDRP_MED__c":false,"PersonDoNotCall":false,"PersonEmail":"","PersonHasOptedOutOfEmail":false,"PersonHasOptedOutOfFax":false,"PersonMobilePhone":"","Phone":"","Practicing_Specialty__c":"FM - FAMILY MEDICINE","Primary_City__c":"","Primary_State__c":"","Primary_Street_Line2__c":"","Primary_Street__c":"","Primary_Zip__c":"","RecordTypeId":"012U0000000JaPWIA0","Request_Date__c":"2016-06-10T22:31:55.9647467Z","ONY_ID__c":"","Specialty_1_vod__c":"","Suffix_vod__c":"","Website":""}",
        "statusCode": "400"
    }
}
```

#### Insertar error en DocumentDB: respuesta


``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": ""0c00eaac-0000-0000-0000-575b3fdc0000"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{"Account_Class_vod__c":"PRAC","Account_Status_MED__c":"I","CRM_HUB_ID__c":"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0","Credentials_vod__c":"DO - Degree level is DO","DTC_ID_MED__c":"","Fax":"","FirstName":"A","Gender_vod__c":"","IMS_ID__c":"","LastName":"BAILEY","MterID_mp__c":"","Medicis_ID_MED__c":"851588","Middle_vod__c":"","NPI_vod__c":"","PDRP_MED__c":false,"PersonDoNotCall":false,"PersonEmail":"","PersonHasOptedOutOfEmail":false,"PersonHasOptedOutOfFax":false,"PersonMobilePhone":"","Phone":"","Practicing_Specialty__c":"FM - FAMILY MEDICINE","Primary_City__c":"","Primary_State__c":"","Primary_Street_Line2__c":"","Primary_Street__c":"","Primary_Zip__c":"","RecordTypeId":"012U0000000JaPWIA0","Request_Date__c":"2016-06-10T22:31:55.9647467Z","XXXXXXX":"","Specialty_1_vod__c":"","Suffix_vod__c":"","Website":""}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### Respuesta de error de Salesforce

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### Devolución de la respuesta a la aplicación lógica primaria

Una vez que tiene la respuesta, puede pasarla a la aplicación lógica primaria.

#### Devolución de respuesta satisfactoria a la aplicación lógica primaria

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "	Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### Devolución de respuesta de error a la aplicación lógica primaria

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## Portal y repositorio de DocumentDB

Nuestra solución agregó funcionalidades adicionales con [DocumentDB](https://azure.microsoft.com/services/documentdb).

### Portal de administración de errores

Para ver los errores, puede crear una aplicación web de MVC que muestre los registros de error de DocumentDB. En la versión actual, se incluyen las operaciones de **lista**, **detalles**, **edición** y **eliminación**.

> [AZURE.NOTE] Operación de edición: DocumentDB realiza un reemplazo de todo el documento. Los registros mostrados en las vistas de **lista** y **detalles** son solo ejemplos. No son registros reales de citas de pacientes.

Los siguientes son ejemplos de nuestros detalles de la aplicación de MVC creados con el enfoque descrito anteriormente.

#### Lista de administración de errores

![Lista de errores](./media/app-service-logic-scenario-error-and-exception-handling/errorlist.png)

#### Vista de detalles de administración de errores

![Detalles del error](./media/app-service-logic-scenario-error-and-exception-handling/errordetails.png)

### Portal de administración de registros

Para ver los registros, también creamos una aplicación web de MVC. Los siguientes son ejemplos de nuestros detalles de la aplicación de MVC creados con el enfoque descrito anteriormente.

#### Vista de detalles del registro de ejemplo

![Vista de detalles del registro](./media/app-service-logic-scenario-error-and-exception-handling/samplelogdetail.png)

### Detalles de la aplicación de API

#### API de administración de excepciones de Logic Apps

Nuestra aplicación de API de administración de excepciones de Logic Apps de código abierto proporciona la siguiente funcionalidad.

Hay dos controladores

- **ErrorController** inserta un registro de error (documento) en una colección de DocumentDB.
- **LogController** inserta una entrada de registro (documento) en una colección de DocumentDB.

> [AZURE.TIP] Ambos controladores usan operaciones `async Task<dynamic>`. Esto permite que las operaciones se resuelvan en tiempo de ejecución, por lo que podemos crear el esquema de DocumentDB en el cuerpo de la operación.

Todos los documentos de DocumentDB deben tener un identificador único. Vamos a utilizar `PatientId` y a agregar una marca de tiempo que se convertirá en un valor de marca de tiempo de Unix (doble). Se trunca para quitar el valor fraccionario.

Puede ver el código fuente de la API de controlador de errores [procedente de GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs).

Llamamos a la API desde una aplicación lógica mediante la sintaxis siguiente:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

La expresión del ejemplo de código anterior buscará el estado **Error** para *Create\_NewPatientRecord*.

## Resumen

- Puede implementar fácilmente el registro y control de errores en una aplicación lógica.
- Puede usar DocumentDB como repositorio para entradas de registro y registros de error (documentos).
- Puede usar MVC para crear un portal que muestre las entradas de registro y los registros de error.

### Código fuente
El código fuente de la aplicación de API de administración de excepciones de Logic Apps está disponible en este [repositorio de GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "API de administración de excepciones de Logic Apps").


## Pasos siguientes
- [Más ejemplos y escenarios de Logic Apps](app-service-logic-examples-and-scenarios.md)
- [Más información acerca de las herramientas de supervisión de Logic Apps](app-service-logic-monitor-your-logic-apps.md)
- [Creación de una plantilla de implementación automatizada de Logic Apps](app-service-logic-create-deploy-template.md)

<!---HONumber=AcomDC_0817_2016-->