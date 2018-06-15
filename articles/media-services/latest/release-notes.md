---
title: Notas de la versión de Azure Media Services v3 | Microsoft Docs
description: Para mantenerse al día con los últimos desarrollos, en este artículo se proporcionan las actualizaciones más reciente en Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: fc6c5ba6cd97c261dd44eade33bf21e8d1b74bf0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782644"
---
# <a name="azure-media-services-v3-preview-release-notes"></a>Notas de la versión de Azure Media Services v3 (versión preliminar) 

Para mantenerse al día con los avances más recientes, este artículo proporciona información acerca de los elementos siguientes:

* Versiones más recientes
* Problemas conocidos
* Corrección de errores
* Funciones obsoletas
* Planes de cambios

## <a name="may-07-2018"></a>07 de mayo de 2018

### <a name="net-sdk"></a>SDK de .NET

Las características siguiente están disponibles en el SDK de .NET:

1. **Transformaciones** y **trabajos** para codificar o analizar el contenido multimedia. Para ver ejemplos, consulte los artículos sobre [streaming de archivos](stream-files-tutorial-with-api.md) y [análisis](analyze-videos-tutorial-with-api.md).
2. **StreamingLocators** para publicar y transmitir contenido a los dispositivos de usuarios finales.
3. **StreamingPolicies** y **ContentKeyPolicies** para configurar la entrega de claves y la protección de contenido (DRM) al entregar el contenido.
4. **LiveEvents** y **LiveOutputs** para configurar la ingesta y el archivo de contenido de streaming en vivo.
5. **Recursos** para almacenar y publicado contenido multimedia en Azure Storage. 
6. **StreamingEndpoints** para configurar y escalar el empaquetado dinámico, cifrado y streaming tanto de contenido multimedia en vivo como a petición.

### <a name="known-issues"></a>Problemas conocidos

Problema conocido:

Cuando envíe un trabajo con una dirección URL de HTTPS (JobInputHttp) apuntando al contenido de origen, asegúrese de que el servidor HTTP admite la solicitud "HEAD". De lo contrario, se rechazará el trabajo.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Información general](media-services-overview.md)
