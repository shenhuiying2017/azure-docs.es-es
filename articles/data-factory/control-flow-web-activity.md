---
title: Actividad web en Azure Data Factory | Microsoft Docs
description: "Aprenda a usar la actividad web, una de las actividades de flujo de control admitidas por Data Factory, para invocar un punto de conexión REST desde una canalización."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 04b542bf1f77b75c1c92b147b578df630b86d0ac
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="web-activity-in-azure-data-factory"></a>Actividad web en Azure Data Factory
La actividad web se puede usar para llamar a un punto de conexión REST personalizado desde una canalización de Data Factory. Puede pasar conjuntos de datos y servicios vinculados que la actividad consumirá y a los que tendrá acceso. 

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte la [documentación de la versión 1 de Data Factory](v1/data-factory-introduction.md).

## <a name="syntax"></a>Sintaxis

```json
{  
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{  
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{  
         "Content-Type":"application/json"
      },
      "authentication":{  
         "type":"ClientCertificate",  
         "pfx":"****",
         "password":"****"
      },
      "datasets":[  
         {  
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{  
               ...
            }
         }
      ],
      "linkedServices":[  
         {  
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Propiedades de tipo

Propiedad | DESCRIPCIÓN | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | --------
Nombre | Nombre de la actividad web | string | Sí
Tipo | Se debe establecer en **WebActivity**. | string | Sí
estático | Método de API de REST para el punto de conexión de destino. | String. <br/><br/>Tipos admitidos: "GET", "POST" y "PUT" | Sí
URL | Punto de conexión y ruta de acceso de destino | Cadena (o expresión con un valor resultType de cadena) | Sí
encabezados | Encabezados que se envían a la solicitud. Por ejemplo, para establecer el idioma y el tipo en una solicitud: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Cadena (o expresión con un valor resultType de cadena) | Sí, el encabezado Content-type es necesario. `"headers":{ "Content-Type":"application/json"}`
Cuerpo | Representa la carga útil que se envía al punto de conexión. Necesario para los métodos POST o PUT.  | Cadena (o expresión con un valor resultType de cadena). <br/><br/>Vea el esquema de la carga de solicitud en la sección [Solicitar un esquema de carga](#request-payload-schema). | Sin 
Autenticación | Método de autenticación usado para llamar al punto de conexión. Los tipos admitidos son "Basic" y "ClientCertificate". Para más información, vea la sección [Autenticación](#authentication). Si la autenticación no es necesaria, excluya esta propiedad. | Cadena (o expresión con un valor resultType de cadena) | Sin 
conjuntos de datos | Lista de conjuntos de datos que se pasan al punto de conexión. | Matriz de referencias de conjunto de datos. Puede ser una matriz vacía. | Sí
linkedServices | Lista de servicios vinculados que se pasan al punto de conexión. | Matriz de referencias de servicios vinculados. Puede ser una matriz vacía. | Sí

> [!NOTE]
> Los puntos de conexión REST que invoca la actividad web deben devolver una respuesta de tipo JSON.

## <a name="authentication"></a>Autenticación

### <a name="none"></a>None
Si la autenticación no es necesaria, no incluya la propiedad "authentication".

### <a name="basic"></a>Básica
Especifique el nombre de usuario y la contraseña que se usarán con la autenticación básica. 

```json
"authentication":{  
   "type":"Basic,
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Certificado de cliente
Especifique un contenido codificado en base64 de un archivo PFX y la contraseña. 

```json
"authentication":{  
   "type":"ClientCertificate",
   "pfx":"****",   
   "password":"****"
}
```
## <a name="request-payload-schema"></a>Solicitar un esquema de carga
Al usar el método POST o PUT, la propiedad body representa la carga que se envía al punto de conexión. Puede pasar servicios vinculados y conjuntos de datos como parte de la carga. Este es el esquema de la carga: 

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
} 
```

## <a name="example"></a>Ejemplo
En este ejemplo, la actividad web de la canalización llama a un punto de conexión REST. Pasa un servicio vinculado de Azure SQL y un conjunto de datos de Azure SQL al punto de conexión. El punto de conexión REST usa la cadena de conexión de Azure SQL para conectarse al servidor de Azure SQL y devuelve el nombre de la instancia de SQL Server. 

### <a name="pipeline-definition"></a>Definición de la canalización

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>Valores de parámetro de canalización

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Código de punto de conexión del servicio web

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>pasos siguientes
Consulte otras actividades de flujo de control compatibles con Data Factory: 

- [Actividad de ejecución de canalización](control-flow-execute-pipeline-activity.md)
- [Para cada actividad](control-flow-for-each-activity.md)
- [Actividad de obtención de metadatos](control-flow-get-metadata-activity.md)
- [Actividad de búsqueda](control-flow-lookup-activity.md)
