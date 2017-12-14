---
title: "Paso del token de autenticación a Azure Media Services | Microsoft Docs"
description: "Obtenga información acerca de cómo enviar tokens de autenticación desde el cliente al servicio de entrega de claves de Azure Media Services."
services: media-services
keywords: content protection, DRM, token authentication
documentationcenter: 
author: dbgeorge
manager: jasonsue
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: dwgeo
ms.openlocfilehash: 0e56726266898e5738dd797a8a019987d457170e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="how-clients-pass-tokens-to-azure-media-services-key-delivery-service"></a>Cómo pasan los clientes tokens al servicio de entrega de claves de Azure Media Services
Recibimos constantemente preguntas acerca del modo en que un reproductor podría pasar un token a nuestros servicios de entrega de claves, que a continuación se comprobaría para que el reproductor obtenga la clave. Se admiten dos formatos de token: token web simple (SWT) y JSON Web Token (JWT). La autenticación de token puede aplicarse a cualquier tipo de clave: con independencia de que se esté aplicando el cifrado común o el cifrado de sobre AES en el sistema.

Estas son las siguientes maneras en que puede pasar el token con el reproductor, en función del reproductor y la plataforma de destino:
- A través del encabezado de autorización HTTP.
> [!NOTE]
> Tenga en cuenta que las especificaciones de OAuth 2.0 esperan el prefijo "Portador". Hay un reproductor de ejemplo con la configuración de token hospedado en la página de demostración de [Azure Media Player](http://ampdemo.azureedge.net/). Elija AES (token de JWT) o AES (token de SWT) para establecer un origen de vídeo. El token se pasa a través del encabezado de autorización.

- Mediante la adición de un parámetro de consulta de URL con "token=tokenvalue".  
> [!NOTE]
> Tenga en cuenta que no se espera ningún prefijo "Portador". Puesto que el token se envía a través de una dirección URL, debe proteger la cadena de token. Este es un código de ejemplo de C# sobre cómo hacerlo:

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

- A través del campo CustomData.
Únicamente para la adquisición de la licencia de PlayReady, a través del campo CustomData del desafío de adquisición de licencias de PlayReady. En este caso, el token debe estar dentro del documento xml que se describe a continuación.

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```
Coloque el token de autenticación en el elemento <Token>.

- A través de una lista de reproducción HLS alternativa. Si necesita configurar la autenticación de token para la reproducción AES + HLS en iOS/Safari, no hay una manera de enviar directamente el token. Vea este [entrada de blog](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) acerca de cómo alternar la lista de reproducción para habilitar este escenario.

## <a name="next-steps"></a>Pasos siguientes
Consulte las rutas de aprendizaje de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]