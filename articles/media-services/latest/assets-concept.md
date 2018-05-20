---
title: Recursos en Azure Media Services | Microsoft Docs
description: En este artículo se explica qué son los artículos y cómo los usa Azure Media Services.
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
ms.openlocfilehash: 928d88d51503350abe7df847ce58ff066b987c8e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="assets"></a>Recursos

Un **recurso** contiene archivos digitales (como vídeos, audio, imágenes, colecciones de miniaturas, pistas de texto y subtítulos) y metadatos de estos archivos. Una vez que los archivos digitales se cargan en un recurso, se pueden utilizar en los flujos de trabajo de codificación y streaming en Media Services.

Un recurso se asigna a un contenedor de blobs en la [cuenta de Azure Storage](storage-account-concept.md) y los archivos del recurso se almacenan como blobs en bloques en ese contenedor. Puede interactuar con los archivos del recurso en los contenedores mediante los clientes del SDK de Storage.

Azure Media Services admite los niveles de blob cuando la cuenta usa el almacenamiento de uso general v2 (GPv2). Con GPv2, puede mover los archivos al almacenamiento de acceso esporádico o al almacenamiento en frío. El almacenamiento en frío es adecuado para guardar archivos intermedios cuando ya no son necesarios (por ejemplo, una vez codificados).

En Media Services v3, la entrada de trabajo se puede crear a partir de recursos o desde direcciones URL de HTTP(s). Para crear un recurso que se puede usar como una entrada para el trabajo, consulte [Creación de una entrada de trabajo a partir de un archivo local](job-input-from-local-file-how-to.md).

Además, lea sobre las [cuentas de almacenamiento en Media Services](storage-account-concept.md) y sobre las [transformaciones y trabajos](transform-concept.md).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Streaming de un archivo](stream-files-dotnet-quickstart.md)
