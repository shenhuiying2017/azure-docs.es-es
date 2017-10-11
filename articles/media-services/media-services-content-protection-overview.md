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
ms.openlocfilehash: 64be4ea104bd11b8e191e2c8d4170a2de88acb47
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="protecting-content-overview"></a>Información general de protección del contenido
Servicios multimedia de Microsoft Azure le permite proteger su contenido multimedia desde el momento en que deja el equipo a través de almacenamiento, procesamiento y entrega. Media Services permite entregar el contenido cifrado de forma dinámica con Estándar de cifrado avanzado (AES) (mediante claves de cifrado de 128 bits) o cualquiera de los DRM principales: Microsoft PlayReady, Google Widevine y Apple FairPlay. Media Services también proporciona un servicio para entregar claves AES y licencias de DMR (PlayReady, Widevine y FairPlay) a los clientes autorizados. 

La siguiente imagen muestra los flujos de trabajo de protección de contenido que admite AMS. 

![Protección con PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

>[!NOTE]
>Cuando se crea la cuenta de AMS, se agrega un punto de conexión de streaming **predeterminado** a la cuenta en estado **Stopped** (Detenido). Para iniciar la transmisión del contenido y aprovechar el empaquetado dinámico y el cifrado dinámico, el punto de conexión de streaming desde el que va a transmitir el contenido debe estar en estado **Running** (En ejecución). 

En este tema se explican los [conceptos y terminología](media-services-content-protection-overview.md) pertinentes para conocer la protección de contenido con AMS. El tema también contiene [vínculos](media-services-content-protection-overview.md#common-scenarios) a temas que muestran cómo realizar las tareas de protección de contenido. 

## <a name="dynamic-encryption"></a>Cifrado dinámico
Microsoft Azure Media Services permite proporcionar el contenido cifrado de forma dinámica con la clave sin cifrado AES o cifrado DRM: Microsoft PlayReady, Google Widevine y Apple FairPlay.

Actualmente puede cifrar los formatos de streaming siguientes: HLS, MPEG DASH y Smooth Streaming. No se pueden cifrar las descargas progresivas.

Si desea que Servicios multimedia cifren un recurso, debe asociar una clave de cifrado (CommonEncryption o EnvelopeEncryption) con su recurso y, además, configurar directivas de autorización para la clave.

También necesita configurar la directiva de entrega del recurso. Si desea transmitir un recurso cifrado de almacenamiento, asegúrese de configurar la directiva de entrega del recurso para especificar cómo desea entregarlo.

Cuando un reproductor solicita una transmisión, Media Services usa la clave especificada para cifrar de forma dinámica el contenido mediante la clave sin cifrado de AES o el cifrado DRM. Para descifrar la secuencia, el reproductor solicitará la clave del servicio de entrega de claves. Para decidir si el usuario está o no autorizado para obtener la clave, el servicio evalúa las directivas de autorización que especificó para la clave.


## <a name="storage-encryption"></a>Cifrado de almacenamiento
Utilice el cifrado de almacenamiento para cifrar el contenido no cifrado de manera local mediante el cifrado de AES de 256 bits y luego cargarlo a Azure Storage donde se almacena cifrado en reposo. Los recursos protegidos con el cifrado de almacenamiento se descifran automáticamente y se colocan en un sistema de archivos cifrados antes de la codificación y, opcionalmente, se vuelven a cifrar antes de volver a cargarlos como un nuevo recurso de salida. El caso de uso principal para el cifrado de almacenamiento es cuando desea proteger los archivos multimedia de entrada de alta calidad con un sólido cifrado en reposo en disco.

Para entregar a un recurso cifrado de almacenamiento, debe configurar la directiva de entrega del recurso para que Servicios multimedia sepa cómo desea entregar el contenido. Antes de poder transmitir el activo, el servidor de streaming quita el cifrado de almacenamiento y transmite el contenido usando la directiva de entrega especificada (por ejemplo, AES, cifrado común o sin cifrado).

## <a name="common-encryption-cenc"></a>Cifrado común (CENC)
El cifrado común se utiliza al cifrar el contenido con PlayReady o Widewine.

## <a name="using-cbcs-aapl-encryption"></a>Uso del cifrado cbcs-aapl
Cbcs-aapl se usa al cifrar el contenido con FairPlay.

## <a name="envelope-encryption"></a>Cifrado de sobre
Utilice esta opción si desea proteger su contenido con la clave sin cifrado de AES-128. Si desea una opción más segura, elija uno de los DRM enumerados en este tema. 

## <a name="licenses-and-keys-delivery-service"></a>Servicio de entrega de licencias y claves
Media Services proporciona un servicio para proporcionar licencias DRM (PlayReady, Widevine, FairPlay) y claves sin cifrado de AES a clientes autorizados. Puede utilizar [Azure Portal](media-services-portal-protect-content.md), la API de REST o SDK de Media Services para .NET para configurar las directivas de autorización y autenticación de sus licencias y claves.

## <a name="token-restriction"></a>Restricción de token
La directiva de autorización de claves de acceso podría tener una o más restricciones de autorización: abrir o restricción de token. La directiva con restricción token debe ir acompañada de un token emitido por un Servicio de tokens seguros (STS). Servicios multimedia admite tokens en formato Token de web simple (SWT) y en formato Token de web JSON (JWT). Los Servicios multimedia no proporcionan Servicios de tokens seguros. Puede crear un STS personalizado o aprovechar el Servicio de control de acceso (ACS) de Microsoft Azure para emitir tokens. Se debe configurar el STS para crear un token firmado con las notificaciones de clave y emisión que especificó en la configuración de restricción de tokens. El servicio de entrega de claves de Servicios multimedia devolverá la clave solicitada (o licencia) al cliente si el token es válido y las reclamaciones del token coinciden con las configuradas para la clave (o licencia).

Al configurar la directiva de restricción de token, debe especificar los parámetros de clave de comprobación principal, emisor y público. La clave de comprobación principal contiene la clave con la que se firmó el token y el emisor es el servicio de tokens seguros que emite el token. El público (a veces denominado ámbito) describe la intención del token o del recurso cuyo acceso está autorizado por el token. El servicio de entrega de claves de los Servicios multimedia valida que estos valores del token coincidan con los valores de la plantilla.

## <a name="streaming-urls"></a>Direcciones URL de streaming
Si el recurso se cifró con más de un DRM, debe usar una etiqueta de cifrado en la dirección URL de streaming: (formato = 'm3u8-aapl', cifrado = 'xxx').

Se aplican las siguientes consideraciones:

* Se puede especificar solo un tipo de cifrado o ninguno.
* El tipo de cifrado no tiene que especificarse en la dirección URL si solo se aplicó un cifrado al recurso.
* El tipo de cifrado distingue mayúsculas de minúsculas.
* Se pueden especificar los siguientes tipos de cifrado:  
  * **cenc**: cifrado común (Playready o Widevine)
  * **cbcs-aapl**: Fairplay
  * **cbc**: cifrado de sobre AES.

## <a name="common-scenarios"></a>Escenarios comunes
Los temas siguientes muestran cómo proteger el contenido en el almacenamiento, proporcionar medios de streaming cifrados dinámicamente, uso del servicio de entrega de claves\licencia de AMS

* [Protección con AES](media-services-protect-with-aes128.md) 
* [Protección con PlayReady y/o Widevine ](media-services-protect-with-drm.md)
* [Transmisión de contenido HLS protegido con Apple FairPlay o PlayReady](media-services-protect-hls-with-fairplay.md)

### <a name="additional-scenarios"></a>Otros escenarios
* [How to integrate Azure PlayReady License service with your own encryptor/streaming server](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server)(Integración del servicio de licencia de PlayReady de Azure con su propio servidor de streaming/sistema de cifrado).
* [Uso de castLabs para entregar licencias de DRM a Servicios multimedia de Azure](media-services-castlabs-integration.md)

>[!NOTE]
>Un escenario en el que usa un servidor DRM externo (tecnología) y flujo de AMS no se admite actualmente.


## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Vínculos relacionados
[Anuncio de PlayReady como servicio y cifrado dinámico AES con Servicios multimedia de Azure](http://mingfeiy.com/playready)

[Explicación del precio de la entrega de licencia de PlayReady de Servicios multimedia de Azure](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Depuración del flujo cifrado de AES en Servicios multimedia de Azure](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[Autenticación de token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integración de la aplicación OWIN basada en MVC de Servicios multimedia de Azure con Azure Active Directory y restricción de la entrega de claves de contenido basada en notificaciones de JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Uso de ACS de Azure para emitir tokens](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
