---
title: "Configuración de la directiva de autorización de claves de contenido mediante Azure Portal | Microsoft Docs"
description: "Aprenda a configurar una directiva de autorización para una clave de contenido."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ee82a3fa-c34b-48f2-a108-8ba321f1691e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 5a35c7255a1c30a693862589c14f6a22a1900790
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="configure-content-key-authorization-policy"></a>Configuración de la directiva de autorización de claves de contenido
[!INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Información general
Servicios multimedia de Microsoft Azure permute entregar transmisiones MPEG-DASH, Smooth Streaming y HTTP-Live-Streaming (HLS) protegidas con Estándar de cifrado avanzado (AES) (usando claves de cifrado de 128 bits) o [DRM de Microsoft PlayReady](https://www.microsoft.com/playready/overview/). AMS también permite entregar transmisiones DASH cifradas con DRM de Widevine. Tanto PlayReady como Widewine se cifran según la especificación de cifrado común (ISO/IEC 23001-7 CENC).

Servicios multimedia también proporciona un **servicio de entrega de claves/licencias** con el que los clientes pueden obtener claves AES o licencias de PlayReady/Widevine con el fin de reproducir el contenido cifrado.

Este tema muestra cómo usar Azure Portal para configurar la directiva de autorización de claves de contenido. La clave se puede usar posteriormente para cifrar el contenido de forma dinámica. Tenga en cuenta que actualmente puede cifrar los formatos de streaming siguientes: HLS, MPEG DASH y Smooth Streaming. No se pueden cifrar las descargas progresivas.

Cuando un reproductor solicita una transmisión que se establece para cifrarse de forma dinámica, Servicios multimedia usa la clave configurada para cifrar dinámicamente el contenido mediante cifrado AES o DRM. Para descifrar la secuencia, el reproductor solicitará la clave del servicio de entrega de claves. Para decidir si el usuario está o no autorizado para obtener la clave, el servicio evalúa las directivas de autorización que especificó para la clave.

Si planea tener varias claves de contenido o desea especificar una dirección URL del **servicio de entrega de claves/licencias** que no sea el servicio de entrega de claves de Servicios multimedia, use el SDK de Servicios multimedia para .NET o las API de REST.

[Configuración de la directiva de autorización de claves mediante el SDK de Servicios multimedia para .NET](media-services-dotnet-configure-content-key-auth-policy.md)

[Configuración de la directiva de autorización de claves mediante la API de REST de Servicios multimedia](media-services-rest-configure-content-key-auth-policy.md)

### <a name="some-considerations-apply"></a>Se aplican algunas consideraciones:
* Cuando se crea la cuenta de AMS, se agrega un punto de conexión de streaming **predeterminado** a la cuenta en estado **Stopped** (Detenido). Para iniciar la transmisión del contenido y aprovechar el empaquetado dinámico y el cifrado dinámico, el punto de conexión de streaming debe estar en estado **Running** (En ejecución). 
* El recurso debe contener un conjunto de archivos MP4 de velocidad de bits adaptable o archivos Smooth Streaming de velocidad de bits adaptable. Para obtener más información, consulte [Codificación de un recurso](media-services-encode-asset.md).
* El servicio de entrega de claves almacena en caché ContentKeyAuthorizationPolicy y sus objetos relacionados (opciones y restricciones de directiva) durante 15 minutos.  Si crea una entidad ContentKeyAuthorizationPolicy y especifica el uso de una restricción "Token", pruébela y, a continuación, actualice la directiva a la restricción "Open"; la directiva tardará aproximadamente 15 minutos antes de cambiar a la versión "Open" de la misma.

## <a name="how-to-configure-the-key-authorization-policy"></a>Procedimiento: configuración de la directiva de autorización de claves
Para configurar la directiva de autorización de claves, seleccione la página **PROTECCIÓN DE CONTENIDO** .

Servicios multimedia admite varias formas de autenticar a los usuarios que realizan solicitudes de clave. La directiva de autorización de claves de contenido puede tener restricciones de autorización **open**, **token** o **IP** (**IP** puede configurarse con REST o el SDK de .NET).

### <a name="open-restriction"></a>Restricción open
La restricción **open** significa que el sistema entregará la clave a cualquier persona que realice una solicitud de clave. Esta restricción puede ser útil para realizar pruebas.

![OpenPolicy][open_policy]

### <a name="token-restriction"></a>Restricción de token
Para elegir la directiva de token restringida, presione el botón **TOKEN** .

La directiva con restricción **token** debe ir acompañada de un token emitido por un **Servicio de tokens seguros** (STS). Media Services admite tokens en formato **Token de web simple** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) y en formato **JSON Web Token** (JWT). Para obtener información, consulte [Autenticación de token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Servicios multimedia no proporciona **Servicios de tokens seguros**. Puede crear un STS personalizado o aprovechar el Servicio de control de acceso (ACS) de Microsoft Azure para emitir tokens. Se debe configurar el STS para crear un token firmado con las notificaciones de clave y emisión que especificó en la configuración de restricción de tokens. El servicio de entrega de claves de los Servicios multimedia devolverá la clave de cifrado al cliente si el token es válido y las notificaciones del token coinciden con las configuradas para la clave de contenido. Para obtener más información, consulte [Uso de Azure ACS para emitir tokens](http://mingfeiy.com/acs-with-key-services).

Al configurar la directiva con restricción **TOKEN**, debe establecer los valores de **clave de verificación**, **emisor** y **público**. La clave de comprobación principal contiene la clave con la que se firmó el token y el emisor es el servicio de tokens seguros que emite el token. El público (a veces denominado ámbito) describe la intención del token o del recurso cuyo acceso está autorizado por el token. El servicio de entrega de claves de los Servicios multimedia valida que estos valores del token coincidan con los valores de la plantilla.

### <a name="playready"></a>PlayReady
Al proteger su contenido con **PlayReady**, una de las cosas que debe especificar en la directiva de autorización es una cadena XML que defina la plantilla de licencia de PlayReady. De forma predeterminada, se establece la siguiente directiva:

<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1"> <LicenseTemplates> <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices> <ContentKey i:type="ContentEncryptionKeyFromHeader" /> <LicenseType>Nonpersistent</LicenseType> <PlayRight> <AllowPassingVideoContentToUnknownOutput>Permitido</AllowPassingVideoContentToUnknownOutput> </PlayRight> </PlayReadyLicenseTemplate> </LicenseTemplates> </PlayReadyLicenseResponseTemplate>

Puede hacer clic en el botón **Importar directiva xml** y proporcionar un archivo XML diferente que se ajuste al esquema XML definido [aquí](media-services-playready-license-template-overview.md).

## <a name="next-step"></a>Paso siguiente
Consulte las rutas de aprendizaje de Servicios multimedia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

