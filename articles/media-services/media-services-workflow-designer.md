<properties 
	pageTitle="Creación de flujos de trabajo de codificación avanzada con el Diseñador de flujo de trabajo" 
	description="Aprenda a crear flujos de trabajo de codificación avanzada con el Diseñador de flujo de trabajo." 
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
	ms.date="09/07/2015"
	ms.author="juliako"/>


#Creación de flujos de trabajo de codificación avanzada con el Diseñador de flujo de trabajo

##Información general
El **Diseñador de flujo de trabajo** es una herramienta de escritorio de Windows que se usa para diseñar y crear flujos de trabajo personalizados para la codificación con el **Flujo de trabajo premium de codificación de medios**.

Esta herramienta también se puede usar para modificar [flujos de trabajo existentes](media-services-workflow-designer.md#existing_workflows).

>[AZURE.NOTE]Para obtener una copia de la herramienta Diseñador de flujo de trabajo, póngase en contacto con mepd@microsoft.com.


Una vez que creado un archivo de flujo de trabajo, se puede cargar como un activo y, a continuación, usarse para la codificación de archivos multimedia. Para obtener información sobre cómo codificar con el **flujo de trabajo del Codificador multimedia Premium** usando **.NET**, consulte [Codificación avanzada con el flujo de trabajo del Codificador multimedia Premium](media-services-encode-with-premium-workflow.md).

##<a id="existing_workflows"></a>Modificación de flujos de trabajo existentes

Los archivos de flujo de trabajo predeterminados pueden modificarse con la herramienta de diseñador. Puede obtener los archivos de flujo de trabajo predeterminados [aquí](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). La carpeta también contiene la descripción de estos archivos.

Los vídeos siguientes muestran cómo usar el diseñador.

###Día 1 – Introducción

El vídeo del día 1 trata de:

- Información general del diseñador
- Flujos de trabajo básicos: "Hello World"
- Creación de varios archivos MP4 de salida para usarlos con streaming de Servicios multimedia de Azure

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-1]

###Día 2

El vídeo del día 2 trata de:

- Diversos escenarios de archivo de código fuente: control del audio
- Flujos de trabajo con lógica avanzada
- Fases de gráfico

> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-2]

###Día 3

El vídeo del día 3 trata de:

- Scripting dentro de los flujos de trabajo y planos
- Restricciones con el codificador actual
- Preguntas y respuestas
 
> [AZURE.VIDEO azure-premium-encoder-workflow-designer-training-videos-day-3]



##Rutas de aprendizaje de Servicios multimedia

Puede ver las rutas de aprendizaje de Servicios multimedia de Azure aquí:

- [Flujo de trabajo de streaming en vivo de Servicios multimedia de Azure](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [Flujo de trabajo de streaming a petición de Servicios multimedia de Azure](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


##Otras referencias

[Azure Premium Encoder Workflow Designer Training Videos](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/) (Vídeos de aprendizaje del Diseñador de flujo de trabajo del codificador de Azure Premium)

<!---HONumber=Oct15_HO2-->