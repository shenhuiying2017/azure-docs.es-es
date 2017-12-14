---
title: Ejemplos de directivas de Azure API Management | Microsoft Docs
description: "Aprenda sobre las directivas de transformación disponibles para su uso en Azure API Management."
services: api-management
documentationcenter: 
author: Juliako
manager: cflower
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: e951eb47290a2d1b6f892f1ccf923f6ce0c1899c
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="api-management-policy-samples"></a>Ejemplos de directivas de API Management

Las [directivas](api-management-howto-policies.md) constituyen una funcionalidad del sistema eficaz que permite al editor cambiar el comportamiento de la API mediante la configuración. Las directivas son una colección de declaraciones que se ejecutan secuencialmente en la solicitud o respuesta de una API. En la tabla siguiente se incluyen vínculos a ejemplos y una breve descripción de cada ejemplo.

|||
|---|---|
|**Directivas de entrada**||
|[Incorporación de un encabezado de reenviado para permitir que la API de back-end cree direcciones URL correctas](./policies/set-header-to-enable-backend-to-construct-urls.md?toc=api-management/toc.json) |Muestra cómo agregar un encabezado de reenviado a la solicitud de entrada para permitir que la API de back-end cree direcciones URL correctas.|
|[Incorporación de un encabezado con un identificador de correlación](./policies/add-correlation-id.md?toc=api-management/toc.json) |Muestra cómo agregar un encabezado con un identificador de correlación para la solicitud de entrada.|
|[Incorporación de funcionalidades a un servicio back-end y almacenamiento de la respuesta en caché](./policies/cache-response.md?toc=api-management/toc.json) |Muestra cómo agregar funcionalidades a un servicio back-end. Por ejemplo, aceptar el nombre de un lugar en vez de su latitud y longitud en una API de pronóstico meteorológico.|
|[Autorización de acceso con notificaciones JWT](./policies/authorize-request-based-on-jwt-claims.md?toc=api-management/toc.json) |Muestra cómo autorizar el acceso a los métodos HTTP específicos en una API con notificaciones JWT.|
|[Autorización de acceso con el token de OAuth de Google](./policies/use-google-as-oauth-token-provider.md?toc=api-management/toc.json) |Muestra cómo autorizar el acceso a los puntos de conexión con Google como proveedor de tokens de OAuth.|
|[Generación de la firma de acceso compartido y reenvío de la solicitud a Azure Storage](./policies/generate-shared-access-signature.md?toc=api-management/toc.json) |Muestra cómo generar una [firma de acceso compartido](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) mediante expresiones y reenviar la solicitud a Azure Storage con la directiva de reescritura del identificador URI. |
|[Obtención del token de acceso de OAuth2 desde AAD y reenvío al back-end](./policies/use-oauth2-for-authorization.md?toc=api-management/toc.json) |Proporciona un ejemplo de uso de OAuth2 para la autorización entre la puerta de enlace y un back-end. Muestra cómo obtener un token de acceso en AAD y reenviarlo al back-end.|
|[Obtención del token de X-CSRF en la puerta de enlace de SAP mediante la directiva de solicitud de envío](./policies/get-x-csrf-token-from-sap-gateway.md?toc=api-management/toc.json) |Muestra cómo implementar el patrón X-CSRF que utilizan muchas API. En este ejemplo es específico de la puerta de enlace de SAP. |
|[Enrutamiento de la solicitud en función del tamaño de su cuerpo](./policies/route-requests-based-on-size.md?toc=api-management/toc.json) |Muestra cómo enrutar las solicitudes en función del tamaño de su cuerpo.|
|[Envío de información contextual de la solicitud al servicio de back-end](./policies/send-request-context-info-to-backend-service.md?toc=api-management/toc.json) |Muestra cómo enviar información contextual al servicio de back-end para el registro o el procesamiento.|
|[Establecimiento de la duración en caché de las respuestas](./policies/set-cache-duration.md?toc=api-management/toc.json) |Muestra cómo establecer la duración en caché de las respuestas con el valor maxAge en el encabezado de control de caché que envía el back-end.|
|**Directivas de salida**||
|[Filtrado del contenido de la respuesta](./policies/filter-response-content.md?toc=api-management/toc.json) | Muestra cómo filtrar los elementos de datos de la carga de respuesta según el producto asociado a la solicitud.|
|**Directivas de errores**||
|[Registro de errores en Stackify](./policies/log-errors-to-stackify.md?toc=api-management/toc.json) |Muestra cómo agregar una directiva de registro de errores para enviarlos a Stackify para el registro.|
