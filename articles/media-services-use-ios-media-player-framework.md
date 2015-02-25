<properties pageTitle="Uso de iOS Media Player Framework con Servicios multimedia de Azure" description="Aprenda a usar la biblioteca iOS Media Player Framework de Servicios multimedia para crear aplicaciones enriquecidas y dinámicas." services="media-services" documentationCenter="" authors="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="mobile-ios" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako"/>



# Uso de iOS Media Player Framework de Servicios Multimedia de Azure

La biblioteca iOS Media Player Framework de Servicios Multimedia de Azure facilita a los desarrolladores de iPod, iPhone y iPad la creación de aplicaciones de cliente enriquecidas y dinámicas que crean y combinan transmisiones de vídeo y audio de manera instantánea. Por ejemplo, las aplicaciones que muestran contenido deportivo pueden insertar fácilmente anuncios en el lugar que elijan y controlar la frecuencia con que aparecen esos anuncios incluso cuando el contenido principal se rebobine. Las aplicaciones educativas pueden usar la misma funcionalidad, por ejemplo, para crear contenido en donde las conferencias principales tengan puntos aparte o barras laterales antes de volver al contenido principal.

Suele ser relativamente complicado trabajar para generar una aplicación que pueda crear transmisiones de contenido que provoquen la interacción entre la aplicación y el usuario. Normalmente, debe crear la transmisión completa desde el principio y almacenarla, por adelantado, en el servidor. Use iOS Media Player Framework para generar aplicaciones de cliente que puedan hacer todo esto sin tener control de la transmisión de contenido principal ni modificarla. puede:

- Programar transmisiones de contenido por adelantado en el dispositivo cliente.
- Programar inserciones o anuncios de procesamiento previo.
- Programar inserciones o anuncios de procesamiento posterior.
- Programar inserciones o anuncios de procesamiento intermedio y crear sucesiones de anuncios.
- Controlar si la inserción o el anuncio de procesamiento intermedio se reproduce cada vez que la escala de tiempo de contenido se rebobina o si solo se reproduce una vez y, a continuación, se quita de la escala de tiempo.
- Insertar dinámicamente el contenido directamente en la escala de tiempo como resultado de cualquier evento, si el usuario presionó un botón o la aplicación recibió una notificación de un servicio, por ejemplo, un programa de contenido de noticias podría enviar notificaciones de noticias de última hora y la aplicación podría "pausar" el contenido principal para cargar dinámicamente una transmisión de noticias de última hora. 

La combinación de estas características con características de reproducción multimedia de dispositivos iOS hace posible la creación de experiencias multimedia enriquecidas en un breve período con un número menor de recursos.

El SDK contiene una aplicación SamplePlayer que muestra cómo generar una aplicación iOS que usa la mayor parte de estas características para crear una transmisión de contenido de manera instantánea y permitir al usuario activar e insertar contenido de forma dinámica con solo pulsar un botón. Este tutorial muestra los componentes principales de la aplicación SamplePlayer y cómo puede usarlos como punto de partida para la aplicación.

## Introducción a la aplicación SamplePlayer
Los siguientes pasos muestran cómo obtener la aplicación y proporcionan un recorrido por las áreas de la aplicación que usan el marco. 

1. Clone el repositorio git. 

    `git clone https://github.com/WindowsAzure/azure-media-player-framework`

2. Abra el proyecto que se encuentra en `azure-media-player-framework/src/iOS/HLSClient/`: **SamplePlayer.xcodeproj**.

 
3. Aquí tiene la estructura del reproductor de ejemplo:

![HLS Sample Code structure](http://mingfeiy.com/wp-content/uploads/2013/01/HLS-Structure.png)

4. En la carpeta iPad, hay dos archivos .xib: **SeekbarViewController** y **SamplePlayerViewController**. Se ocupan de crear el diseño de la interfaz de usuario de la aplicación para iPad. De la misma forma, existen dos archivos .xib en la carpeta iPhone que definen el controlador y la barra de búsqueda. 

6. La lógica de la aplicación principal se encuentra en **SamplePlayerViewController.m** debajo de la carpeta  `Shared`. La mayoría de los fragmentos de código descritos a continuación se encuentran ubicados en ese archivo. 

## Descripción del diseño de la interfaz de usuario
Existen dos archivos .xib que definen la interfaz del reproductor. En el ejemplo se usa el diseño de iPad como ejemplo, pero el diseño de iPhone es muy parecido y los principios son los mismos.
### SamplePlayerViewController_iPad.xib
![Sample Player Address Bar](http://mingfeiy.com/wp-content/uploads/2013/01/addressbar.png)

* **La dirección URL multimedia** es la dirección URL usada para cargar una transmisión multimedia. La aplicación cuenta con una lista previamente completada de direcciones URL multimedia que puede usar mediante los botones de selección de la dirección URL. También puede especificar su propia dirección URL de contenido de HTTP Live Streaming (HLS). Este contenido multimedia se usará como el primer contenido principal. 
**Nota: no deje esta dirección URL vacía.**

* Los botones de **selección de dirección URL** le permiten seleccionar direcciones URL alternativas de la lista de direcciones URL multimedia.

### SeekbarViewController_iPad.xib
![Seek Bar Controller](http://mingfeiy.com/wp-content/uploads/2013/01/controller.png)
* Use el botón **Reproducir** para ejecutar y pausar la reproducción multimedia.

* La **barra de búsqueda** muestra toda la escala de tiempo de reproducción. Cuando realice la búsqueda, mantenga presionado, arrastre a la posición que desee y libere el botón de búsqueda en la barra de búsqueda. 

**Nota**: cuando el visor busque en un anuncio, aparecerá una nueva barra de búsqueda con la duración del anuncio. La barra de búsqueda principal solo presenta la duración del contenido principal (es decir, un anuncio tiene una duración de 0 en la barra de búsqueda principal).

* El control **Tiempo de reproducción** muestra dos tiempos (`Label:playerTime`), como por ejemplo 00:23/02:10. En este caso, 00:23 sería el tiempo de reproducción actual y 02:10 la duración total del contenido multimedia. 

* Los botones **Saltar hacia adelante y Saltar hacia atrás**  no funcionan actualmente según lo esperado; se publicará una versión actualizada pronto.

* Si presiona el botón **Programar ahora** cuando el contenido principal se esté reproduciendo, se inserta un anuncio (puede definir la dirección URL del origen del anuncio en el código subyacente). Nota: en la versión actual, no puede programar un anuncio cuando el otro anuncio se esté reproduciendo. 

### Programación del contenido principal
Clip de contenido programado de 0 a 80 segundos:

    //Schedule the main content
    MediaTime *mediaTime = [[[MediaTime alloc] init] autorelease];
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
        
    int clipId = 0;
    if (![framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

En el código de ejemplo anterior:

* El objeto **MediaTime** controla el clip de vídeo que desea programar como contenido principal. En el ejemplo anterior, el clip de vídeo se programará para que tenga una duración de 80 segundos (de 0 segundos a 80 segundos);
* **clipBeginMediaTime** representa el tiempo de inicio para que un vídeo comience a reproducirse. Por ejemplo, si **clipBeginMediaTime** = 5, este clip de vídeo se inicia durante 5 segundos en el clip de vídeo.
* **clipEndMediaTime** representa el tiempo final para que se reproduzca un vídeo. Si **clipEndMediaTime**=100, el reproductor de vídeo finaliza en el segundo 100 del clip de vídeo.
*A continuación se programa **MediaTime** solicitando al marco que realice la operación de **appendContentClip**. En el ejemplo anterior, la dirección URL del contenido principal se proporciona en `[NSURL URLWithString:url]` y la programación para ese recurso multimedia se establece mediante **withMedia**:
 `[framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId]])`.

**Nota:** programe siempre el contenido principal antes de programar cualquier anuncio (incluido el anuncio de procesamiento previo). 

### Variación: si dispone de dos clips de contenido principal en reproducción, también puede programar el segundo clip después del primero con el siguiente código:

    //Schedule second content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 30;
    mediaTime.clipEndMediaTime = 80;
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

Si hace esto después del código anterior, se programan dos transmisiones de contenido en la escala de tiempo de contenido principal. La primera se programa basándose en  `URLWithString:url` y el segundo contenido se programa basándose en  `URLWithString:secondContent`. Para el segundo contenido, el contenido comienza desde un punto de 30 segundos de la transmisión del recurso multimedia y finaliza en los 80 segundos. 

## Programación de anuncios 
En la versión actual, solo es compatible un anuncio **pauseTimeline=false**, lo que significa que después de que finalice el anuncio, el reproductor comenzará desde donde se quedó el contenido principal. 

He aquí algunos puntos clave:
<ul><li> **LinearTime.duration** debe estar en 0 cuando se programe un anuncio.</li>
<li> Cuando **clipEndMediaTime** sea superior a la duración del anuncio, este finaliza después que haya acabado y no se muestra ninguna excepción. Se recomienda que compruebe si la duración natural del anuncio se encuentra dentro del tiempo de representación (**clipEndMediaTime**). De esa forma, no perderá una oportunidad de anuncio.</li> 
<li> Los anuncios de procesamiento previo, intermedio y posterior son compatibles. El procesamiento previo solo puede programarse al principio de todo el contenido. Por ejemplo, no puede programar un procesamiento previo para el segundo contenido en un escenario de edición tentativa (RCE). </li>
<li> Los anuncios persistentes y los que se reproducen una vez son compatibles y pueden usarse junto a los anuncios de procesamiento previo, intermedio y posterior.</li>
<li> El formato del anuncio puede ser .Mp4 o HLS.</li>
</ul>
### Programación de sucesiones de anuncios y anuncios de procesamiento previo, intermedio y posterior

#### Programación de anuncios de procesamiento previo

    LinearTime *adLinearTime = [[[LinearTime alloc] init] autorelease];
    NSString *adURLString = @"http://smoothstreamingdemo.blob.core.windows.net/videoasset/WA-BumpShort_120530-1.mp4";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.clipBeginMediaTime = 0;
    adInfo.mediaTime.clipEndMediaTime = 5;
    adInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

El objeto **AdInfo** representa toda la información sobre el clip del anuncio:
* **ClipURL** es la dirección URL para el origen del clip.
* La propiedad **mediaTime** indica la duración que tarda el anuncio en reproducirse. (**clipBeginMediaTime** es la hora de inicio de un anuncio y **clipEndMediaTime** define el final del anuncio). En el código de ejemplo anterior, programamos un anuncio durante 5 segundos, comenzando por 0 hasta el segundo 5 de la duración del anuncio.
* El marco no usa actualmente el objeto **Policy**.
* Debe establecer el valor **appendTo** en -1 si no es una sucesión de anuncios. 
* El valor **type** puede ser una sucesión de anuncios o un procesamiento previo, intermedio o posterior. Especifique el tipo para el procesamiento previo o el posterior, ya que no existen intervalos asociados. 

#### Programación de anuncios de procesamiento intermedio

Si agrega `adLinearTime.startTime = 23;` al ejemplo de código anterior, el anuncio empieza a reproducirse a los 23 segundos en la escala de tiempo del contenido principal.

#### Programación de anuncios de procesamiento posterior

    //Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    postAdInfo.mediaTime.clipEndMediaTime = 5;
    postAdInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

La única diferencia respecto a la programación de anuncios de procesamiento previo es `postAdInfo.type = AdType_Postroll;`. El código anterior programó un anuncio de 5 segundos como procesamiento posterior. 

#### Programación de sucesiones de anuncios
Las sucesiones de anuncios representan una interrupción de anuncios con varios de ellos reproduciéndose opuesto con opuesto. A continuación se muestra el código para la programación de dos anuncios en una sucesión. 

    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    adpodInfo1.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.mediaTime = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    int32_t adIndex = 0;
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
        
    NSString *adpodSt2=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-532531b8-fca4-4c15-86f6-45f9f45ec980/Windows%208_%20Sign%20in%20with%20a%20Smile.mp4?st=2012-11-28T16%3A35%3A26Z&se=2014-11-28T16%3A35%3A26Z&sr=c&si=c6ede35c-f212-4ccd-84da-805c4ebf64be&sig=zcWsj1JOHJB6TsiQL5ZbRmCSsEIsOJOcPDRvFVI0zwA%3D";
    AdInfo *adpodInfo2 = [[[AdInfo alloc] init] autorelease];
    adpodInfo2.clipURL = [NSURL URLWithString:adpodSt2];
    adpodInfo2.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo2.mediaTime.clipBeginMediaTime = 0;
    adpodInfo2.mediaTime.clipEndMediaTime = 17;
    adpodInfo2.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo2.type = AdType_Pod;
    adpodInfo2.appendTo = adIndex;
    if (![framework scheduleClip:adpodInfo2 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

Existen algunos aspectos que debe tener en cuenta aquí:
* Para el primer clip, **appendTo** es -1. Cuando llamamos a `[framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex]`,  `adIndex` recibe un valor exclusivo que indica el final del primer clip en la sucesión de anuncios. A continuación, para el segundo clip de la sucesión, alinee el principio del segundo anuncio con el final del primero estableciendo **appendTo** as `adpodInfo2.appendTo = adIndex;`, que especifica la posición final del primero como la ubicación en la que iniciar el segundo clip. 
* A continuación, debe establecer el tipo como  `AdType_Pod` para indicar que se trata de una sucesión de anuncios. 

### Programación de un anuncio persistente o que se reproduce una vez
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    oneTimeInfo.deleteAfterPlay = YES;

Como se muestra en el ejemplo del código anterior, si establece **deleteAfterPlay** en **YES**, este anuncio se reproduce solo una vez. Si estableció **deleteAfterPlay** en **NO**, este anuncio sigue reproduciéndose. Es lo que llamamos "anuncio persistente".
### Consulte la página [wiki Azure Media Player Framework](https://github.com/WindowsAzure/azure-media-player-framework/wiki) para obtener más información.

<!--HONumber=42-->
