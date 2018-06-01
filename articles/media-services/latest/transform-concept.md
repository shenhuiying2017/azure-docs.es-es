---
title: Transformaciones y trabajos en Azure Media Services | Microsoft Docs
description: Al usar Media Services, debe crear una transformación para describir las reglas o especificaciones para procesar los vídeos. En este artículo se proporciona información general sobre qué es un transformación y cómo se usa.
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
ms.openlocfilehash: b755e0573098d3dbed1bea18a40af634be609f76
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34272087"
---
# <a name="transforms-and-jobs"></a>Transformaciones y trabajos

## <a name="overview"></a>Información general 

En la última versión de la API de REST de Azure Media Services (versión 3) se presenta un nuevo recurso de flujo de trabajo con plantilla para codificar o analizar vídeos, que se conoce como una **transformación**. Las **transformaciones** se pueden usar para configurar tareas comunes para codificar o analizar vídeos. Cada **transformación** describe un flujo de trabajo simple de tareas para procesar los archivos de vídeo o audio. 

El objeto **Transformación** es la receta y un **trabajo** es la solicitud real a Azure Media Services para aplicar que dicha **transformación** a un determinado contenido de audio o vídeo de entrada. El **trabajo** especifica información como la ubicación del vídeo de entrada y la ubicación de la salida. Puede especificar la ubicación del vídeo con direcciones URL HTTP(s), direcciones URL de SAS o una ruta de acceso a los archivos ubicados en el entorno local o en Azure Blob Storage. Puede tener hasta 100 transformaciones en la cuenta de Azure Media Services y ejecutar trabajos en esas transformaciones. Luego puede suscribirse a eventos como cambios de estado del trabajo, mediante notificaciones, que se integran directamente con el sistema de notificaciones de Azure Event Grid. 

Como Azure Resource Manager controla esta API, puede usar plantillas de Resource Manager para crear e implementar las transformaciones en su cuenta de Media Services. El control de acceso basado en rol también se puede establecer en el nivel de recursos de esta API, lo que le permite bloquear el acceso a recursos específicos, como las transformaciones.

## <a name="transform-definition"></a>Definición de transformación

En la tabla siguiente se muestran las propiedades de la transformación y se proporcionan sus definiciones.

|NOMBRE|Escriba|DESCRIPCIÓN|
|---|---|---|
|Id|string|Identificador de recurso completo del recurso.|
|Nombre|string|Nombre del recurso.|
|properties.created |string|La fecha y hora UTC cuando se creó la transformación, en formato "AAAA-MM-DDThh:mm:ssZ".|
|properties.description |string|Una descripción detallada opcional de la transformación.|
|properties.lastModified |string|La fecha y hora UTC cuando se actualizó la transformación por última vez, en formato "AAAA-MM-DDThh:mm:ssZ".|
|Properties.Outputs |TransformOutput[]|Una matriz de uno o más elementos TransformOutputs que debe generar la transformación.|
|Tipo|string|Tipo de recurso.|

Para conocer la definición completa, consulte [Transformaciones](https://docs.microsoft.com/rest/api/media/transforms).

## <a name="job-definition"></a>Definición del trabajo

En la tabla siguiente se muestran las propiedades del trabajo y se proporcionan sus definiciones.

|NOMBRE|Escriba|DESCRIPCIÓN|
|---|---|---|
|Id|string|Identificador de recurso completo del recurso.|
|Nombre|string|Nombre del recurso.|
|properties.created |string|La fecha y hora UTC cuando se creó la transformación, en formato "AAAA-MM-DDThh:mm:ssZ".|
|properties.description |string|Una descripción detallada opcional del trabajo.|
|properties.lastModified |string|La fecha y hora UTC cuando se actualizó la transformación por última vez, en formato "AAAA-MM-DDThh:mm:ssZ".|
|Properties.Outputs |JobOutput[]:JobOutputAsset[] |Las salidas del trabajo.|
|properties.priority |Prioridad |Prioridad con la que se debe procesar el trabajo. Los trabajos con prioridad superior se procesan antes que los trabajos con prioridad inferior. Si no se establece, el valor predeterminado es normal.
|properties.state |JobState |Estado actual del trabajo.
|Tipo|string|Tipo de recurso.|

Para conocer la definición completa, consulte [Trabajos](https://docs.microsoft.com/rest/api/media/jobs).

## <a name="typical-workflow-and-example"></a>Flujo de trabajo típico y ejemplo

1. Cree una transformación 
2. Enviar trabajos en esa transformación 
3. Enumerar las transformaciones 
4. Eliminar una transformación si no se planea usar esta "receta" en el futuro. 

Imagine que desea extraer el primer fotograma de todos los vídeos como una imagen en miniatura; los pasos que debería seguir son: 

1. Definir la regla para el procesamiento, por ejemplo, use el primer fotograma del vídeo como la miniatura. 
2. Luego, para cada vídeo que tiene como entrada al servicio, debería indicarle al servicio: 
    1. Dónde encontrar ese vídeo y 
    2. Dónde escribir la salida, por ejemplo, la imagen en miniatura 

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Streaming de archivos de vídeo](stream-files-dotnet-quickstart.md)
