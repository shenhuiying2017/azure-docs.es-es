<properties 
	pageTitle="Uso de trabajos de Servicios multimedia de Azure" 
	description="En este tema se proporciona información general sobre cómo administrar trabajos de Servicios multimedia de Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/05/2015" 
	ms.author="juliako"/>

#Uso de trabajos de Servicios multimedia de Azure

Un **trabajo** contiene metadatos sobre el procesamiento que se va a realizar. Cada **trabajo** contiene una o más **tareas** que especifican una tarea de procesamiento atómica, sus recursos de entrada, recursos de salida, un procesador de multimedia y su configuración asociada. Para obtener más información sobre la configuración del codificador, vea Manuales del codificador y Esquemas de codificador.

Un trabajo de codificación se combina habitualmente con otros procesos como, por ejemplo, empaquetado o cifrado del recurso o recursos generados por el codificador. Las tareas dentro de un trabajo se pueden encadenar en conjunto, donde el recurso de salida de una de las tareas se indica como el recurso de entrada de la tarea siguiente. De este modo, un trabajo puede contener todos los procesos necesarios para una presentación multimedia.

En esta sección se proporcionan vínculos a tareas comunes que se realizan al trabajar con trabajos/tareas.

>[AZURE.NOTE]Actualmente hay un límite de 30 tareas por trabajo. Si necesita encadenar más de 30 tareas, cree más de un trabajo para incluir las tareas.


##Obtención de procesador multimedia

Obtenga el procesador multimedia con **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../../includes/media-services-selector-get-media-processor.md)]

##Creación de trabajos

Un trabajo es una entidad que contiene metadatos sobre un conjunto de tareas (por ejemplo, codificación o indización). Cada tarea realiza una operación atómica en los recursos de entrada. Para ver un ejemplo de cómo crear trabajos de codificación, consulte:

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

##Indización

[AZURE.INCLUDE [media-services-selector-index-content](../../includes/media-services-selector-index-content.md)]

##Codificación

Codifique con el **Codificador multimedia de Azure** mediante el **Portal de Azure clásico**, **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

##Supervisión del progreso del trabajo

Supervise el progreso del trabajo mediante el **Portal de Azure clásico**, **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-job-progress](../../includes/media-services-selector-job-progress.md)]

##Listado 

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-manage-entities.md/#list-jobs-and-assets)
- [REST](media-services-rest-manage-entities.md/#querying-entities)

##Eliminación de trabajos

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-manage-entities.md/#delete-a-job)
- [REST](media-services-rest-manage-entities.md/##deleting-entities)

##Vínculos relacionados

[Cuotas y limitaciones](media-services-quotas-and-limitations.md): explica las cuotas usadas y las limitaciones del codificador de Servicios multimedia


##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_1210_2015-->