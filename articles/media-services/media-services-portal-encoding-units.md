<properties
	pageTitle="Escalación del procesamiento multimedia mediante el Portal de Azure clásico"
	description="Aprenda a escalar Servicios multimedia mediante la especificación del número de unidades reservadas de streaming a petición y unidades reservadas de codificación con las que desea aprovisionar la cuenta."
	services="media-services"
	documentationCenter=""
	authors="juliako,milangada"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/29/2016"
	ms.author="juliako"/>


# Escalación del procesamiento multimedia mediante el Portal de Azure clásico

## Llevar a cabo esta tarea con otras tecnologías  

Esta página ofrece una visión general de cómo escalar el procesamiento de contenido multimedia y muestra cómo hacerlo mediante el Portal de Azure clásico. También puede llevar a cabo esta tarea con otras tecnologías:

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [Portal](media-services-portal-encoding-units.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

## Información general

Una cuenta de Servicios multimedia está asociada con un tipo de unidad reservada que determina la rapidez con la que se procesan las tareas de procesamiento multimedia. Puede elegir uno de los siguientes tipos de unidad reservada: **S1**, **S2** o **S3**. Por ejemplo, el mismo trabajo de codificación se ejecuta más rápido cuando se usa el tipo de unidad reservada **S2** en comparación con el tipo **S1**. Para obtener más información, consulte el blog [Encoding Reserved Unit Types](https://azure.microsoft.com/blog/author/milanga/) (Codificación de tipos de unidad reservada).

Además de especificar el tipo de unidad reservada, puede especificar el aprovisionamiento de su cuenta con unidades reservadas de codificación. El número de unidades reservadas de codificación aprovisionadas determina el número de tareas de medios que se pueden procesar de forma simultánea en una cuenta determinada. Por ejemplo, si la cuenta tiene cinco unidades reservadas, se ejecutarán simultáneamente cinco tareas multimedia siempre que haya tareas para procesar. Las tareas restantes esperarán en la cola y se elegirán para el procesamiento secuencialmente tan pronto como finalice la tarea en ejecución. Si una cuenta no tiene ninguna unidad reservada aprovisionada, las tareas se elegirán de manera secuencial. En este caso, el tiempo de espera entre la finalización de una tarea y el inicio de la siguiente dependerá de la disponibilidad de los recursos del sistema.

## Selección de los distintos tipos de unidad reservada

La siguiente tabla sirve de ayuda para tomar la decisión de elegir entre distintas velocidades de codificación. También incluye algunos casos de prueba comparativa y direcciones URL de SAS que puede usar para descargar vídeos en los que puede realizar sus propias pruebas:

 |**S1**|**S2**|**S3**|
----------|------------|----------|------------
Caso de uso previsto| Codificación con velocidad de bits sencilla. <br/>Archivos con resolución SD o menor, no sujetos a limitación temporal y de bajo costo.|Codificación con velocidad de bits sencilla y múltiple.<br/>Uso normal para codificación SD y HD. |Codificación con velocidad de bits sencilla y múltiple.<br/>Vídeos con resolución Full HD y 4K. Codificación con respuesta más rápida, sujeta a limitación temporal. 
Prueba comparativa|[Archivo de entrada: 5 minutos de duración, 640 x 360 p a 29,97 fotogramas/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_360p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D).<br/><br/>La codificación de un archivo MP4 con velocidad de bits sencilla con la misma resolución tarda aproximadamente 11 minutos.|[Archivo de entrada: 5 minutos de duración, 1280 x720 p a 29,97 fotogramas/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_720p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D)<br/><br/>La codificación con el valor predeterminado "H264 Single Bitrate 720p" tardará aproximadamente 5 minutos.<br/><br/>La codificación con el valor predeterminado "H264 Multiple Bitrate 720p" tardará aproximadamente 11,5 minutos.|[Archivo de entrada: 5 minutos de duración, 1920 x 1080 p a 29,97 fotogramas/segundo](https://wamspartners.blob.core.windows.net/for-long-term-share/Whistler_5min_1080p30.mp4?sr=c&si=AzureDotComReadOnly&sig=OY0TZ%2BP2jLK7vmcQsCTAWl33GIVCu67I02pgarkCTNw%3D). <br/><br/>La codificación con el valor predeterminado "H264 Single Bitrate 1080p" tarda aproximadamente 2,7 minutos.<br/><br/>La codificación con el valor predeterminado "H264 Multiple Bitrate 1080p" tarda aproximadamente 5,7 minutos.

##Consideraciones

>[AZURE.IMPORTANT] Se aplican las siguientes consideraciones:

- Las unidades reservadas sirven para establecer paralelismos en todo el procesamiento multimedia, incluida la indexación de trabajos mediante Azure Media Indexer. Sin embargo, a diferencia de la codificación, la indexación de los trabajos no se procesará más rápido con unidades reservadas de mayor rapidez.

- Si utiliza el grupo compartido, es decir, sin ninguna unidad reservada, las tareas de codificación tendrán el mismo rendimiento que con las RU S1. Sin embargo, no existe ningún límite superior para el tiempo que las tareas pueden estar en el estado en cola, y en un momento determinado, se estará ejecutando a lo sumo una tarea.

- Los siguientes centros de datos no ofrecen el tipo de unidad reservada **S3**: Sur de Brasil, India occidental, India central y Sur de la India.

- Se utiliza el número más elevado de unidades especificadas durante el período de 24 horas al calcular el coste.

## Cambio del tipo de unidad reservada

Para cambiar el tipo de unidad reservada y el número de unidades reservadas, haga lo siguiente:

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios multimedia**. A continuación, haga clic en el nombre del servicio multimedia.

2. Seleccione la página **CODIFICACIÓN**.

	Para cambiar el **TIPO DE UNIDAD RESERVADA**, presione S1, S2 o S3.

	Para cambiar el número de unidades reservadas para el tipo de unidad reservada seleccionada, use el control deslizante **CODIFICACIÓN**.


	![Página de procesadores](./media/media-services-portal-encoding-units/media-services-encoding-scale.png)

3. Presione el botón GUARDAR para guardar los cambios.

	Las nuevas unidades reservadas se asignan en cuanto presiona GUARDAR.
 

##Cuotas y limitaciones

Para obtener información sobre las cuotas y limitaciones y sobre cómo abrir una incidencia de soporte técnico, consulte [Cuotas y limitaciones](media-services-quotas-and-limitations.md).



##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0420_2016-->