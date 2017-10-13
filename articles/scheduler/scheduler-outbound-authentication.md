---
title: "Autenticación saliente de Programador"
description: "Autenticación saliente de Programador"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 6707f82b-7e32-401b-a960-02aae7bb59cc
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/15/2016
ms.author: deli
ms.openlocfilehash: e345b2e22daae5b24c23645f7d2636f66df630ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="scheduler-outbound-authentication"></a>Autenticación saliente de Programador
Puede que los trabajos de Programador tengan que llamar a servicios que requieren autenticación. De este modo, un servicio llamado puede determinar si el trabajo de Programador puede tener acceso a sus recursos. Algunos de estos servicios incluyen otros servicios de Azure, Salesforce.com, Facebook y sitios web personalizados que sean seguros.

## <a name="adding-and-removing-authentication"></a>Incorporación y eliminación de autenticación
La incorporación de autenticación a un trabajo de Programador es sencilla: agregue un elemento secundario JSON `authentication` al elemento `request` al crear o actualizar un trabajo. Los secretos que se pasan al servicio Programador en una solicitud PUT, PATCH o POST, como parte del objeto `authentication` , nunca se devuelven en respuestas. En las respuestas, la información secreta se establece en null o puede que tenga un token público que represente la entidad autenticada.

Para quitar la autenticación, incluya explícitamente una solicitud PUT o PATCH en el trabajo, lo que establece el objeto `authentication` en null. No verá ninguna propiedad de autenticación en la respuesta.

Actualmente, los únicos tipos de autenticación compatibles son el modelo `ClientCertificate` (para usar los certificados de cliente SSL/TLS), el modelo `Basic` (para la autenticación básica) y el modelo `ActiveDirectoryOAuth` (para autenticación ActiveDirectoryOAuth).

## <a name="request-body-for-clientcertificate-authentication"></a>Cuerpo de la solicitud en autenticación ClientCertificate
Al agregar autenticación mediante el modelo `ClientCertificate` , especifique los siguientes elementos adicionales en el cuerpo de la solicitud.  

| Elemento | Description |
|:--- |:--- |
| *autenticación (elemento primario)* |Objeto de autenticación para usar un certificado de cliente SSL. |
| *type* |Obligatorio. Tipo de autenticación. En certificados de cliente SSL, el valor debe ser `ClientCertificate`. |
| *pfx* |Obligatorio. Contenido codificado en base 64 del archivo PFX. |
| *password* |Obligatorio. Contraseña de acceso al archivo PFX. |

## <a name="response-body-for-clientcertificate-authentication"></a>Cuerpo de la respuesta en autenticación ClientCertificate
Cuando se envía una solicitud con información de autenticación, la respuesta contiene los siguientes elementos relacionados con la autenticación.

| Elemento | Description |
|:--- |:--- |
| *autenticación (elemento primario)* |Objeto de autenticación para usar un certificado de cliente SSL. |
| *type* |Tipo de autenticación. Para los certificados de cliente SSL, el valor es `ClientCertificate`. |
| *certificateThumbprint* |Huella digital del certificado. |
| *certificateSubjectName* |Nombre distintivo del sujeto del certificado. |
| *certificateExpiration* |Fecha de expiración del certificado |

## <a name="sample-rest-request-for-clientcertificate-authentication"></a>Ejemplo de solicitud de REST para la autenticación ClientCertificate
```
PUT https://management.azure.com/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-clientcertificate-authentication"></a>Ejemplo de respuesta de REST para la autenticación ClientCertificate
```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="request-body-for-basic-authentication"></a>Cuerpo de la solicitud en autenticación básica
Al agregar autenticación mediante el modelo `Basic` , especifique los siguientes elementos adicionales en el cuerpo de la solicitud.

| Elemento | Description |
|:--- |:--- |
| *autenticación (elemento primario)* |Objeto de autenticación para usar autenticación básica. |
| *type* |Obligatorio. Tipo de autenticación. En autenticación básica, el valor debe ser `Basic`. |
| *username* |Obligatorio. Nombre de usuario para autenticar. |
| *password* |Obligatorio. Contraseña para autenticar. |

## <a name="response-body-for-basic-authentication"></a>Cuerpo de la respuesta en autenticación básica
Cuando se envía una solicitud con información de autenticación, la respuesta contiene los siguientes elementos relacionados con la autenticación.

| Elemento | Description |
|:--- |:--- |
| *autenticación (elemento primario)* |Objeto de autenticación para usar autenticación básica. |
| *type* |Tipo de autenticación. En autenticación básica, el valor es `Basic`. |
| *username* |Nombre del usuario autenticado. |

## <a name="sample-rest-request-for-basic-authentication"></a>Ejemplo de solicitud de REST para la autenticación Basic
```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-basic-authentication"></a>Ejemplo de respuesta de REST para la autenticación Basic
```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="request-body-for-activedirectoryoauth-authentication"></a>Cuerpo de la solicitud en autenticación ActiveDirectoryOAuth
Al agregar autenticación mediante el modelo `ActiveDirectoryOAuth` , especifique los siguientes elementos adicionales en el cuerpo de la solicitud.

| Elemento | Description |
|:--- |:--- |
| *autenticación (elemento primario)* |Objeto de autenticación para usar autenticación ActiveDirectoryOAuth. |
| *type* |Obligatorio. Tipo de autenticación. En autenticación ActiveDirectoryOAuth, el valor debe ser `ActiveDirectoryOAuth`. |
| *tenant* |Obligatorio. Identificador del inquilino de Azure AD. |
| *audience* |Obligatorio. Esto se establece en https://management.core.windows.net/. |
| *clientId* |Obligatorio. Proporcione el identificador de cliente para la aplicación de Azure AD. |
| *secret* |Obligatorio. Secreto del cliente que solicita el token. |

### <a name="determining-your-tenant-identifier"></a>Determinación del identificador del inquilino
Encontrará el identificador del inquilino de Azure AD ejecutando `Get-AzureAccount` en Azure PowerShell.

## <a name="response-body-for-activedirectoryoauth-authentication"></a>Cuerpo de la respuesta en autenticación ActiveDirectoryOAuth
Cuando se envía una solicitud con información de autenticación, la respuesta contiene los siguientes elementos relacionados con la autenticación.

| Elemento | Description |
|:--- |:--- |
| *autenticación (elemento primario)* |Objeto de autenticación para usar autenticación ActiveDirectoryOAuth. |
| *type* |Tipo de autenticación. En autenticación ActiveDirectoryOAuth, el valor es `ActiveDirectoryOAuth`. |
| *tenant* |Identificador del inquilino de Azure AD. |
| *audience* |Esto se establece en https://management.core.windows.net/. |
| *clientId* |Identificador de cliente para la aplicación de Azure AD. |

## <a name="sample-rest-request-for-activedirectoryoauth-authentication"></a>Ejemplo de solicitud de REST para la autenticación ActiveDirectoryOAuth
```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-activedirectoryoauth-authentication"></a>Ejemplo de respuesta de REST para la autenticación ActiveDirectoryOAuth
```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "tenant":"microsoft.onmicrosoft.com",
               "audience":"https://management.core.windows.net/",
               "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type":"ActiveDirectoryOAuth"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "lastExecutionTime":"2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime":"2016-03-16T19:11:00Z",
         "executionCount":5,
         "failureCount":5,
         "faultedCount":1
      }
   }
}
```

## <a name="see-also"></a>Otras referencias
 [¿Qué es Programador?](scheduler-intro.md)

 [Conceptos, terminología y jerarquía de entidades de Programador de Azure](scheduler-concepts-terms.md)

 [Introducción al Programador de Azure en el Portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en Programador de Azure](scheduler-plans-billing.md)

 [Referencia de API de REST de Programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de Programador de Azure](scheduler-powershell-reference.md)

 [Alta disponibilidad y confiabilidad de Programador de Azure](scheduler-high-availability-reliability.md)

 [Límites, valores predeterminados y códigos de error de Programador de Azure](scheduler-limits-defaults-errors.md)

