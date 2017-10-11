---
title: "Lógica de reintento en el SDK de Media Services para .NET | Microsoft Docs"
description: "El tema proporciona una descripción de la lógica de reintento en el SDK de Media Services para .NET."
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: juliako
ms.openlocfilehash: 859dd76db4ba06196a853469a1385703d835fa22
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Lógica de reintento en el SDK de Media Services para .NET
Cuando se trabaja con servicios de Microsoft Azure, pueden producirse errores transitorios. Si se produce un error transitorio, en la mayoría de los casos, el funcionamiento se produce después de algunos reintentos. El SDK de Media Services para .NET implementa la lógica de reintento para administrar los errores transitorios asociados a las excepciones y los errores causados por las solicitudes web, que ejecutan consultas, guardan cambios y operaciones de almacenamiento.  De forma predeterminada, el SDK de Media Services para .NET ejecuta cuatro reintentos antes de volver a producir la excepción en la aplicación. El código de la aplicación debe controlar esta excepción correctamente.  

 La siguiente es una breve directriz de las directivas de solicitud de servicio web, almacenamiento, consulta y SaveChanges:  

* La directiva de almacenamiento se usa para las operaciones de Blob Storage (cargas o descarga de archivos de recursos).  
* La directiva de solicitud web se utiliza para las solicitudes web genéricas (por ejemplo, para obtener un token de autenticación y resolver el punto de conexión del clúster de los usuarios).  
* La directiva de consulta se usa para consultar entidades de REST (por ejemplo, mediaContext.Assets.Where(...)).  
* La directiva de SaveChanges se utiliza para realizar cualquier acción que cambia los datos en el servicio (por ejemplo, la creación de una entidad que actualiza una entidad, o una llamada a una función de servicio para una operación).  
  
  En este tema se enumeran los tipos de excepción y los códigos de error que administra el SDK de Media Services para la lógica de reintento de .NET.  

## <a name="exception-types"></a>Tipos de excepciones
En la tabla siguiente se describen las excepciones que administra el SDK de Media Services para .NET o no administra algunas operaciones que pueden provocar errores transitorios.  

| Excepción | Solicitud web | Almacenamiento | Consultar | SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>Para más información, vea la sección [Códigos de estado de WebException](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus). |yes |Sí |Sí |Sí |
| DataServiceClientException<br/> Para obtener más información, consulte [Códigos de estado de error HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |No |Sí |Sí |Sí |
| DataServiceQueryException<br/> Para obtener más información, consulte [Códigos de estado de error HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |No |Sí |Sí |yes |
| DataServiceRequestException<br/> Para obtener más información, consulte [Códigos de estado de error HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |No |Sí |Sí |Sí |
| DataServiceTransportException |No |No |Sí |Sí |
| TimeoutException |Sí |Sí |Sí |No |
| SocketException |Sí |Sí |Sí |yes |
| StorageException |No |Sí |No |No |
| IOException |No |Sí |No |No |

### <a name="WebExceptionStatus"></a> Códigos de estado WebException
La tabla siguiente se muestra para qué códigos de error de WebException se implementa la lógica de reintento. La enumeración [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) define los códigos de estado.  

| Estado | Solicitud web | Almacenamiento | Consultar | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Sí |Sí |Sí |Sí |
| NameResolutionFailure |yes |Sí |Sí |Sí |
| ProxyNameResolutionFailure |Sí |Sí |Sí |yes |
| SendFailure |Sí |Sí |Sí |yes |
| PipelineFailure |yes |Sí |Sí |No |
| ConnectionClosed |yes |Sí |Sí |No |
| KeepAliveFailure |yes |Sí |Sí |No |
| UnknownError |Sí |Sí |Sí |No |
| ReceiveFailure |Sí |Sí |Sí |No |
| RequestCanceled |Sí |Sí |Sí |No |
| Tiempo de espera |Sí |Sí |Sí |No |
| ProtocolError <br/>El reintento de ProtocolError se controla mediante la administración del código de estado HTTP. Para obtener más información, consulte [Códigos de estado de error HTTP](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |yes |Sí |Sí |Sí |

### <a name="HTTPStatusCode"></a> Códigos de estado de error HTTP
Cuando las operaciones Query y SaveChanges producen DataServiceClientException, DataServiceQueryException o DataServiceQueryException, se devuelve el código de estado de error HTTP en la propiedad StatusCode.  La tabla siguiente se muestra para qué códigos de error se implementa la lógica de reintento.  

| Estado | Solicitud web | Almacenamiento | Consultar | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |No |Sí |No |No |
| 403 |No |Sí<br/>Administración de reintentos con esperas más prolongadas. |No |No |
| 408 |yes |Sí |Sí |Sí |
| 429 |Sí |Sí |Sí |yes |
| 500 |Sí |Sí |Sí |No |
| 502 |yes |Sí |Sí |No |
| 503 |Sí |Sí |Sí |Sí |
| 504 |Sí |Sí |Sí |No |

Si desea echar un vistazo a la implementación real del SDK de Media Services para la lógica de reintento. NET, consulte [azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

