---
title: "Información sobre los codificadores recomendados por Azure Media Services | Microsoft Docs"
description: "Información sobre los codificadores recomendados por Media Services"
services: media-services
keywords: encoding;encoders;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: d0c5536d2339470eac058250cc14e1f250b86d90
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2017
---
# <a name="recommended-on-premises-encoders"></a>Codificadores locales recomendados
Cuando realice streaming en directo con Azure Media Services, puede especificar cómo desea que el canal reciba la transmisión de entrada. Si elige usar un codificador local con un canal de codificación en directo, el codificador debe insertar una transmisión con velocidad de bits única de alta calidad como salida. Si elige usar un codificador local con un canal de paso a través, el codificador debe insertar una transmisión con múltiples velocidades de bits como salida con todas las cualidades de salida deseadas. Para más información, consulte el artículo sobre [streaming en directo con codificadores locales](media-services-live-streaming-with-onprem-encoders.md).

Azure Media Services recomienda usar uno de los codificadores en directo que tienen RTMP como salida:
- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Telestream Wirecast 8.1
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4

Azure Media Services recomienda usar uno de los codificadores en directo que tienen Smooth Streaming con múltiples velocidades de bits como salida:
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3
- Media Excel Hero Live

> [!NOTE]
> Un codificador en directo puede enviar una transmisión con una sola velocidad de bits a un canal de paso a través, pero esta configuración no se recomienda porque no permite el streaming con velocidad de bits adaptable al cliente.

## <a name="how-to-become-an-on-prem-encoder-partner"></a>Convertirse en un socio de codificador local
Como socio de codificador local de Azure Media Services, Media Services promueve su producto al recomendar su codificador a clientes empresariales. Para convertirse en un socio de codificador local, debe comprobar la compatibilidad del codificador local con Media Services. Para hacerlo, complete las comprobaciones siguientes:

Comprobación del canal de paso a través
1. Cree o visite la cuenta de Azure Media Services
2. Cree e inicie un canal de **paso a través**
3. Configure el codificador para insertar una transmisión en directo con múltiples velocidades de bits
4. Cree un evento en directo publicado
5. Ejecute el codificador en directo durante aproximadamente diez minutos
6. Detenga el evento en directo
7. Anote el id. del activo, la dirección URL del streaming publicado del archivo en directo y la configuración y la versión que se usó desde el codificador en directo
8. Restablezca el estado del canal después de crear cada ejemplo
9. Repita del paso 3 al paso 8 para todas las configuraciones compatibles con el codificador (con y sin señalización de anuncios, leyendas o velocidades de codificación distintas)

Comprobación del canal de codificación en directo
1. Cree o visite la cuenta de Azure Media Services
2. Cree e inicie un canal de **codificación en directo**
3. Configure el codificador para insertar una transmisión en directo con una velocidad de bits única
4. Cree un evento en directo publicado
5. Ejecute el codificador en directo durante aproximadamente diez minutos
6. Detenga el evento en directo
7. Anote el id. del activo, la dirección URL del streaming publicado del archivo en directo y la configuración y la versión que se usó desde el codificador en directo
8. Restablezca el estado del canal después de crear cada ejemplo
9. Repita del paso 3 al paso 8 para todas las configuraciones compatibles con el codificador (con y sin señalización de anuncios, leyendas o velocidades de codificación variadas)

Comprobación de duración
1. Cree o visite la cuenta de Azure Media Services
2. Cree e inicie un canal de **paso a través**
3. Configure el codificador para insertar una transmisión en directo con múltiples velocidades de bits
4. Cree un evento en directo publicado
5. Ejecute el codificador en directo durante una semana o más
6. Detenga el evento en directo
7. Anote el id. del activo, la dirección URL del streaming publicado del archivo en directo y la configuración y la versión que se usó desde el codificador en directo

Por último, envíe la configuración que anotó y los parámetros del archivo activo a Media Services por correo electrónico a amsstreaming@microsoft.com. Una vez que reciba esta información, Media Services realiza pruebas de comprobación en los ejemplos del codificador en directo. Puede ponerse en contacto con Media Services si tiene alguna pregunta respecto de este proceso.