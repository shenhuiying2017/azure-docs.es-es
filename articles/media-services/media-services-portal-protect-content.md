---
title: "Configuración de directivas de protección de contenido mediante Azure Portal | Microsoft Docs"
description: "En este artículo se muestra cómo usar Azure Portal para configurar las directivas de protección de contenido. El artículo también muestra cómo habilitar el cifrado dinámico para los recursos."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: ecc766abb5df38813b3eb6dde98cdc9afd24ac6b
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="configuring-content-protection-policies-using-the-azure-portal"></a>Configuración de directivas de protección de contenido mediante Azure Portal
Microsoft Azure Media Services (AMS) le permite proteger su contenido multimedia desde el momento en que deja el equipo a través de almacenamiento, procesamiento y entrega. Media Services permite entregar el contenido cifrado de forma dinámica con Estándar de cifrado avanzado (AES) (mediante claves de cifrado de 128 bits) y cifrado común (CENC) mediante PlayReady o Widevine DRM, and Apple FairPlay. 

AMS proporciona un servicio para proporcionar licencias de DRM y claves sin cifrado de AES a clientes autorizados. Azure Portal le permite crear una **directiva de autorización de licencias o claves** para todos los tipos de cifrado.

En este artículo se muestra cómo configurar una directiva de protección de contenido con Azure Portal. El artículo también muestra cómo aplicar el cifrado dinámico a los recursos.

## <a name="start-configuring-content-protection"></a>Empezar a configurar la protección de contenido
Para usar el portal para empezar a configurar la protección de contenido, para su cuenta de AMS, realice lo siguiente:
1. En [Azure Portal](https://portal.azure.com/), seleccione la cuenta de Azure Media Services.
2. Seleccione **Ajustes** > **Protección de contenido**.

![Proteger contenido](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>directiva de autorización de licencias o claves
AMS admite varias formas de autenticar a los usuarios que realizan solicitudes de clave o licencia. El usuario debe configurar la directiva de autorización de claves y el cliente debe conocerla para que se le proporcione la clave o licencia. La directiva de autorización de claves de acceso podría tener una o más restricciones de autorización: **abrir** o restricción de **token**.

Azure Portal le permite crear una **directiva de autorización de licencias o claves** para todos los tipos de cifrado.

### <a name="open-authorization"></a>Autorización open
La restricción open significa que el sistema entregará la clave a cualquier persona que realice una solicitud de clave. Esta restricción puede ser útil para realizar pruebas. 

### <a name="token-authorization"></a>Autorización token
La directiva con restricción token debe ir acompañada de un token emitido por un Servicio de tokens seguros (STS). Media Services admite tokens en formato Token de web simple (SWT) y en formato Token de web JSON (JWT). Media Services no proporciona Servicios de tokens seguros. Puede crear un STS personalizado o aprovechar el Servicio de control de acceso (ACS) de Microsoft Azure para emitir tokens. Se debe configurar el STS para crear un token firmado con las notificaciones de clave y emisión que especificó en la configuración de restricción de tokens. El servicio de entrega de claves de Media Services devolverá la clave solicitada (o licencia) al cliente si el token es válido y las reclamaciones del token coinciden con las configuradas para la clave (o licencia).

Al configurar la directiva de restricción de token, debe especificar los parámetros de clave de comprobación principal, emisor y público. La clave de comprobación principal contiene la clave con la que se firmó el token y el emisor es el servicio de tokens seguros que emite el token. El público (a veces denominado ámbito) describe la intención del token o del recurso cuyo acceso está autorizado por el token. El servicio de entrega de claves de los Media Services valida que estos valores del token coincidan con los valores de la plantilla.

![Proteger contenido](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>Plantilla de licencia de PlayReady
La plantilla de licencia de PlayReady establece la funcionalidad habilitada en la licencia de PlayReady. Para más información sobre la plantilla de licencia de PlayReady, consulte [Información general de plantillas de licencias de PlayReady de Media Services](media-services-playready-license-template-overview.md).

### <a name="non-persistent"></a>No persistente
Si configura la licencia como no persistente, solo se mantiene en memoria mientras el reproductor está usando la licencia.  

![Proteger contenido](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>Persistente
Si configura la licencia como persistente, se guarda en el almacenamiento persistente en el cliente.

![Proteger contenido](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Plantilla de licencia de Widevine
La plantilla de licencia de Widevine establece la funcionalidad habilitada en las licencias de Widevine.

### <a name="basic"></a>Básica
Al seleccionar **Básico**, la plantilla se creará con todos los valores predeterminados.

### <a name="advanced"></a>Avanzado
Para más información sobre la plantilla de derechos de Widevine, consulte [Información general sobre las plantillas de licencias de Widevine](media-services-widevine-license-template-overview.md).

![Proteger contenido](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>Configuración de FairPlay
Para habilitar el cifrado de FairPlay, debe proporcionar el certificado de la aplicación y la clave de secreto de aplicación (ASK) mediante la opción de configuración de FairPlay. Para más información sobre la configuración y los requisitos de FairPlay, consulte [este artículo](media-services-protect-hls-with-FairPlay.md) .

![Proteger contenido](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>Aplicación del cifrado dinámico al recurso
Para aprovechar las ventajas del cifrado dinámico, debe codificar el archivo de origen en un conjunto de archivos MP4 de velocidad de bits adaptativa.

### <a name="select-an-asset-that-you-want-to-encrypt"></a>Seleccione el recurso que desea cifrar.
Para ver todos sus recursos, seleccione **Configuración** > **Recursos**.

![Proteger contenido](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>Cifrado con AES o DRM
Cuando presiona **Cifrar** en un recurso, se le presentan dos opciones: **AES** o **DRM**. 

#### <a name="aes"></a>AES
El cifrado de claves sin cifrado AES se habilitará en todos los protocolos de streaming: Smooth Streaming, HLS y MPEG-DASH.

![Proteger contenido](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
Cuando se selecciona la pestaña DRM, se le presentan distintas opciones de directivas de protección de contenido (que debe haber configurado ya) más un conjunto de protocolos de streaming.

* **PlayReady y Widevine con MPEG-DASH** : cifrará dinámicamente su transmisión MPEG-DASH con DRM de PlayReady y Widevine.
* **PlayReady y Widevine con MPEG-DASH más FairPlay con HLS** : cifrará dinámicamente su transmisión MPEG-DASH con DRM de PlayReady y Widevine. También se cifrarán las transmisiones HLS con FairPlay.
* **PlayReady solo con Smooth Streaming, HLS y MPEG-DASH** : cifrará dinámicamente Smooth Streaming, HLS, transmisiones MPEG DASH con DRM de PlayReady.
* **Widevine solo con MPEG-DASH** : cifrará dinámicamente MPEG-DASH con DRM de Widevine.
* **FairPlay solo con HLS** : cifrará dinámicamente su transmisión HLS con FairPlay.

Para habilitar el cifrado de FairPlay, debe proporcionar el certificado de la aplicación y la clave de secreto de aplicación (ASK) mediante la opción de configuración de FairPlay de la hoja de configuración de Content Protection.

![Proteger contenido](./media/media-services-portal-content-protection/media-services-content-protection009.png)

Una vez realizada la selección de cifrado, pulse **Aplicar**.

>[!NOTE] 
>Si va a planear la reproducción de una instancia de HLS cifrada mediante AES en Safari, vea [la entrada de blog sobre HLS cifrado en Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>Pasos siguientes
Consulte las rutas de aprendizaje de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

