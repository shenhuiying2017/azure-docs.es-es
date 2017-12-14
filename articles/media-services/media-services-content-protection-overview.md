---
title: "Protección de su contenido con Azure Media Services | Microsoft Docs"
description: "En este artículo se ofrece información general de protección de contenido con Media Services."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 6475a865b9d1b263bd7cc68c99acdb5f6959531e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="protecting-content-overview"></a>Información general de protección del contenido
Microsoft Azure Media Services le permite proteger su contenido multimedia desde el momento en que deja el equipo a través de almacenamiento, procesamiento y entrega. Media Services permite entregar el contenido cifrado de forma dinámica en vivo y a petición con Estándar de cifrado avanzado (AES-128) o cualquiera de los tres sistemas DRM principales: Microsoft PlayReady, Google Widevine y Apple FairPlay. Media Services también proporciona un servicio para entregar claves AES y licencias de DMR (PlayReady, Widevine y FairPlay) a los clientes autorizados. 

En la siguiente imagen se ilustra el flujo de trabajo de protección de contenido de Azure Media Services. 

![Protección con PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

En este artículo se explican los conceptos y terminología pertinentes para conocer la protección de contenido con AMS. En el artículo también se proporcionan vínculos a artículos donde se indica cómo proteger el contenido. 

## <a name="dynamic-encryption"></a>Cifrado dinámico
Azure Media Services permite proporcionar el contenido cifrado de forma dinámica con la clave sin cifrado AES o cifrado DRM: Microsoft PlayReady, Google Widevine y Apple FairPlay. Actualmente puede cifrar los formatos de streaming siguientes: HLS, MPEG DASH y Smooth Streaming. No se admite el cifrado en descargas progresivas. Cada método de cifrado admite los siguientes protocolos de streaming:
- AES: MPEG-DASH, Smooth Streaming y HLS
- PlayReady: MPEG-DASH, Smooth Streaming y HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Para cifrar un recurso, debe asociar una clave de contenido de cifrado con el recurso y, además, configurar una directiva de autorización para la clave. Media Services puede especificar o generar automáticamente las claves de contenido.

También necesita configurar la directiva de entrega del recurso. Si desea transmitir un recurso cifrado de almacenamiento, asegúrese de configurar la directiva de entrega del recurso para especificar cómo desea entregarlo.

Cuando un reproductor solicita una transmisión, Media Services usa la clave especificada para cifrar de forma dinámica el contenido mediante la clave sin cifrado de AES o el cifrado DRM. Para descifrar la secuencia, el reproductor solicitará la clave del servicio de entrega de claves de AMS. Para decidir si el usuario está o no autorizado para obtener la clave, el servicio evalúa las directivas de autorización que especificó para la clave.

## <a name="aes-128-clear-key-vs-drm"></a>Clave sin cifrado AES-128 frente a DRM
Los clientes suelen preguntarse si deben usar el cifrado de AES o un sistema de DRM. La diferencia principal entre usar el cifrado de AES y los sistemas de DRM es que, con el cifrado de AES, la clave de contenido se transmite al cliente en un formato sin cifrar ("fuera de peligro"). Como resultado, la clave usada para cifrar el contenido se puede ver en un seguimiento de la red en el cliente en texto no cifrado. La clave sin cifrado AES-128 es adecuada para los casos de uso en los que el visor es una entidad de confianza (p. ej., cifrado de vídeos corporativos distribuidos dentro de una empresa para su visualización por parte de los empleados).

PlayReady, Widevine y FairPlay proporcionan un nivel de cifrado más alto en comparación con el cifrado de clave sin cifrado AES-128. La clave de contenido se transmite en un formato cifrado. Además, el descifrado se controla en un entorno seguro en el nivel de sistema operativo donde a un usuario malintencionado le resulta significativamente más difícil atacar. DRM se recomienda para los casos de uso en los que es posible que el visor no sea una entidad de confianza y usted requiere el nivel de seguridad más alto.

## <a name="storage-encryption"></a>Cifrado de almacenamiento
Puede utilizar cifrado de almacenamiento para cifrar el contenido no cifrado de manera local mediante el cifrado AES de 256 bits y luego cargarlo a Azure Storage donde se almacena cifrado en reposo. Los recursos protegidos con el cifrado de almacenamiento se descifran automáticamente y se colocan en un sistema de archivos cifrados antes de la codificación y, opcionalmente, se vuelven a cifrar antes de volver a cargarlos como un nuevo recurso de salida. El caso de uso principal para el cifrado de almacenamiento es cuando desea proteger los archivos multimedia de entrada de alta calidad con un sólido cifrado en reposo en disco.

Para entregar a un recurso cifrado de almacenamiento, debe configurar la directiva de entrega del recurso para que Media Services sepa cómo desea entregar el contenido. Antes de poder transmitir el recurso, el servidor de streaming descifra y transmite el contenido usando la directiva de entrega especificada (por ejemplo, AES, cifrado común o sin cifrado).

## <a name="types-of-encryption"></a>Tipos de cifrado
PlayReady y Widevine utilizan cifrado común (modo AES-CTR). FairPlay utiliza el cifrado de modo AES-CBC. El cifrado de clave sin cifrado AES-128 utiliza cifrado de sobre.

## <a name="licenses-and-keys-delivery-service"></a>Servicio de entrega de licencias y claves
Media Services proporciona un servicio de entrega de claves para proporcionar licencias DRM (PlayReady, Widevine, FairPlay) y claves AES a clientes autorizados. Puede utilizar [Azure Portal](media-services-portal-protect-content.md), la API de REST o SDK de Media Services para .NET para configurar las directivas de autorización y autenticación de sus licencias y claves.

## <a name="control-content-access"></a>Acceso al contenido de control
Puede controlar quién tiene acceso a su contenido configurando la directiva de autorización de claves de contenido. La directiva de autorización de claves de contenido admite la restricción open o la restricción de token.

### <a name="open-authorization"></a>Autorización open
Con una directiva de autorización open, la clave de contenido se envía a cualquier cliente (sin restricción).

### <a name="token-authorization"></a>Autorización de token
Con una directiva de autorización con restricción de token, la clave de contenido solo se enviará a un cliente que presente JSON Web Token (JWT) o Simple Web Token (SWT) válidos en la solicitud de clave/licencia. Un servicio de token seguro (STS) debe emitir este token. Puede usar Microsoft Active Directory como un STS o implementar un STS personalizado. Se debe configurar el STS para crear un token firmado con las notificaciones de clave y emisión que especificó en la configuración de restricción de tokens. El servicio de entrega de claves de Media Services devolverá la clave/licencia solicitada al cliente si el token es válido y las reclamaciones del token coinciden con las configuradas para la clave/licencia.

Al configurar la directiva de restricción de token, debe especificar los parámetros de clave de comprobación principal, emisor y público. La clave de comprobación principal contiene la clave con la que se firmó el token y el emisor es el servicio de tokens seguros que emite el token. El público, a veces denominado ámbito, describe la intención del token o del recurso cuyo acceso está autorizado por el token. El servicio de entrega de claves de los Media Services valida que estos valores del token coincidan con los valores de la plantilla.

## <a name="streaming-urls"></a>Direcciones URL de streaming
Si el recurso se cifró con más de un DRM, debe usar una etiqueta de cifrado en la dirección URL de streaming: (formato = 'm3u8-aapl', cifrado = 'xxx').

Se aplican las siguientes consideraciones:
* No se puede especificar más de un tipo de cifrado.
* El tipo de cifrado no tiene que especificarse en la dirección URL si solo se aplicó un cifrado al recurso.
* El tipo de cifrado distingue mayúsculas de minúsculas.
* Se pueden especificar los siguientes tipos de cifrado:  
  * **cenc**: para PlayReady o Widevine (cifrado común)
  * **cbcs-aapl**: para FairPlay (cifrado AES-CBC)
  * **cbc**: para el cifrado de sobre AES (cifrado de sobre)

## <a name="next-steps"></a>Pasos siguientes
En los próximos artículos se describen los siguientes pasos para empezar con la protección de contenido:
* [Protección con el cifrado de almacenamiento](media-services-rest-storage-encryption.md)
* [Protección con el cifrado AES](media-services-protect-with-aes128.md)
* [Protección con PlayReady y/o Widevine](media-services-protect-with-playready-widevine.md)
* [Protección con FairPlay](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Vínculos relacionados
[Explicación del precio de la entrega de licencia de PlayReady de Azure Media Services](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Depuración del flujo cifrado de AES en Azure Media Services](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[Autenticación de token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integración de la aplicación OWIN basada en MVC de Azure Media Services con Azure Active Directory y restricción de la entrega de claves de contenido basada en notificaciones de JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
