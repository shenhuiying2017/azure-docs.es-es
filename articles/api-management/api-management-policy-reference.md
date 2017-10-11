---
title: "Referencia de la directiva de Administración de API de Azure"
description: "Obtenga información acerca de las directivas disponibles para configurar API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 9d4ac609-b221-4032-93ae-e27a1254d43d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: adc0c4415e10ddd0b4994cecef17f026546e91a1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="azure-api-management-policy-reference"></a>Referencia de la directiva de Administración de API de Azure
Esta sección proporciona un índice de las directivas en la [Referencia de la directiva de API Management de Azure][API Management policy reference]. Para obtener más información sobre cómo agregar y configurar directivas, consulte [Directivas en API Management][Policies in API Management].

Las expresiones de directiva pueden utilizarse como valores de atributos o valores de texto en cualquiera de las directivas de Administración de API, a menos que la directiva especifique lo contrario. Algunas directivas como [Flujo de control][Control flow] y [Establecer variable][Set variable] se basan en expresiones de directiva. Para obtener más información, consulte [Directivas avanzadas][Advanced policies] y [Expresiones de directiva][Policy expressions].

## <a name="policy-reference-index"></a>Índice de referencia de directiva
* [Directivas de restricción de acceso][Access restriction policies]
  * [Activar encabezado HTTP][Check HTTP header]: aplica la existencia o el valor de un encabezado HTTP.
  * [Limitar la frecuencia de llamadas por suscripción][Limit call rate by subscription]: evita los picos de uso de la API limitando la frecuencia de llamadas, por suscripción.
  * [Limitar la frecuencia de llamadas por clave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) : evita los picos de uso de la API limitando la frecuencia de llamadas, por clave.
  * [Restringir IP de autor de llamada][Restrict caller IPs]: filtra (permite/deniega) las llamadas de direcciones IP específicas o de intervalos de direcciones.
  * [Establecer cuota de uso por suscripción][Set usage quota by subscription]: le permite aplicar un volumen de llamadas renovables o permanentes o una cuota de ancho de banda por suscripción.
  * [Establecer cuota de uso por clave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) : le permite aplicar un volumen de llamadas renovables o permanentes o una cuota de ancho de banda por clave.
  * [Validar JWT][Validate JWT]: aplica la existencia y la validez de un JWT extraído de un encabezado HTTP especificado o un parámetro de consulta especificado.
* [Directivas avanzadas][Advanced policies]
  * [Flujo de control][Control flow]: aplica condicionalmente instrucciones de directiva basadas en los resultados de la evaluación de [expresiones booleanas][expressions].
  * [Reenviar solicitud][Forward request]: reenvía la solicitud al servicio back-end.
  * [Registro en el centro de eventos][Log to Event Hub]: envía mensajes en el formato especificado a un destino de mensaje definido por una entidad del [registrador](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger).
  * [Reintentar](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry): reintenta ejecutar las instrucciones de directiva adjuntas, si y hasta que se cumple la condición. La ejecución se repite en los intervalos de tiempo especificados y hasta el número de reintentos indicado.
  * [Devolver respuesta](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) : anula la ejecución de la canalización y devuelve la respuesta especificada directamente al llamador.
  * [Enviar solicitud unidireccional](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) : envía una solicitud a la dirección URL especificada sin esperar una respuesta.
  * [Enviar solicitud](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) : envía una solicitud a la dirección URL especificada.
  * [Establecer método de solicitud](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) : le permite cambiar el método HTTP de una solicitud.
  * [Establecer estado](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) : cambia el código de estado HTTP al valor especificado.
  * [Establecer variable][Set variable]: conserva un valor en una variable de [contexto][context] con nombre para el acceso posterior.
  * [Seguimiento](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace): agrega una cadena a la salida de [API Inspector](api-management-howto-api-inspector.md).
  * [Esperar](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) : espera a que se completen las directivas adjuntas Enviar solicitud, Obtener el valor de caché o Flujo de control antes de continuar.
* [Directivas de autenticación][Authentication policies]
  * [Autenticar con opción básica][Authenticate with Basic]: autenticar con un servicio de back-end mediante la autenticación básica.
  * [Autenticar con certificado de cliente][Authenticate with client certificate]: autenticar con un servicio de back-end mediante certificados de cliente.
* [Directivas de almacenamiento en caché][Caching policies] 
  * [Obtener de caché][Get from cache]: realiza una búsqueda en caché y devuelve una respuesta en caché válida cuando esté disponible.
  * [Almacenar en caché][Store to cache]: almacena en caché la respuesta de acuerdo con la configuración de control de caché especificada.
  * [Obtener valor de caché](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) : recupere un elemento almacenado en caché por clave.
  * [Almacenar valor en caché](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) : almacene un elemento en la memoria caché por clave.
  * [Quitar valor de caché](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey); quita un elemento de la memoria caché por clave.
* [Directivas entre dominios][Cross domain policies] 
  * [Permitir llamadas entre dominios][Allow cross-domain calls]: permite que la API sea accesible desde los clientes basados en explorador de Adobe Flash y Microsoft Silverlight.
  * [CORS][CORS]: agrega soporte de uso compartido de recursos entre orígenes (CORS) a una operación o a una API para permitir llamadas entre dominios desde clientes basados en explorador.
  * [JSONP][JSONP]: agrega JSON con soporte de relleno (JSONP) a una operación o a una API para permitir llamadas entre dominios desde clientes basados en explorador de JavaScript.
* [Directivas de transformación][Transformation policies] 
  * [Convertir JSON a XML][Convert JSON to XML]: convierte el cuerpo de solicitud o respuesta de JSON a XML.
  * [Convertir XML a JSON][Convert XML to JSON]: convierte el cuerpo de solicitud o respuesta de XML a JSON.
  * [Buscar y reemplazar la cadena en el cuerpo][Find and replace string in body]: encuentra una subcadena de solicitud o de respuesta y la reemplaza por una subcadena diferente.
  * [Enmascarar URL en el contenido][Mask URLs in content]: reescribe (enmascara) vínculos en el cuerpo de respuesta para que apunten al vínculo equivalente a través de la puerta de enlace.
  * [Establecer el servicio back-end][Set backend service]: cambia el servicio back-end para una solicitud entrante.
  * [Establecer cuerpo][Set body]: establece el cuerpo del mensaje para las solicitudes entrantes y salientes.
  * [Establecer encabezado HTTP][Set HTTP header]: asigna un valor a un encabezado de respuesta o de solicitud existente o agrega un nuevo encabezado de este tipo.
  * [Establecer el parámetro de cadena de consulta][Set query string parameter]: agrega, reemplaza el valor o elimina el parámetro de la cadena de consulta de la solicitud.
  * [URL de reescritura][Rewrite URL]: convierte una URL de solicitud de su forma pública a la forma esperada por el servicio web.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de las expresiones de directivas, vea el siguiente vídeo.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Access restriction policies]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Check HTTP header]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Limit call rate by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restrict caller IPs]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Set usage quota by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validate JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[context]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Forward request]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Log to Event Hub]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Authentication policies]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Authenticate with Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Authenticate with client certificate]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Get from cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Store to cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Cross domain policies]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Allow cross-domain calls]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Transformation policies]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Convert JSON to XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Convert XML to JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Find and replace string in body]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Mask URLs in content]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Set backend service]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Set body]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Set HTTP header]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Set query string parameter]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Rewrite URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Policies in API Management]: api-management-howto-policies.md
[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx


