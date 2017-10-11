---
title: "Escenarios y disponibilidad de características de Microsoft Azure Media Services en centros de datos | Microsoft Docs"
description: "Este tema ofrece información general de los escenarios y la disponibilidad de las características y servicios de Microsoft Azure Media Services en centros de datos."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/10/2017
ms.author: juliako;anilmur
ms.openlocfilehash: d9994dd7bfb6b6bf949a7708c07651d667929ae4
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="scenarios-and-availability-of-media-services-features-across-datacenters"></a>Escenarios y disponibilidad de características de Media Services en centros de datos

Microsoft Azure Media Services (AMS) permite cargar, almacenar, codificar y empaquetar de forma segura contenido de audio o vídeo para su entrega a petición y mediante streaming en vivo a varios clientes (por ejemplo, televisión, PC y dispositivos móviles).

AMS opera en varios centros de datos del mundo. Dichos centros se agrupan en regiones geográficas, lo que proporciona flexibilidad al elegir en lugar en que se van a crear aplicaciones. Puede revisar la [lista de regiones y sus ubicaciones](https://azure.microsoft.com/regions/). 

En este tema se muestran escenarios comunes para la entrega de contenido [en directo](#live_scenarios) o [a petición](#vod_scenarios). El tema también proporciona detalles acerca de la disponibilidad de las características y servicios multimedia en centros de datos.

## <a name="overview"></a>Información general

### <a name="prerequisites"></a>Requisitos previos

Para empezar a usar Azure Media Services, debe tener lo siguiente:

* Una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com).
* Una cuenta de Azure Media Services. Para obtener más información, consulte [Creación de una cuenta](media-services-portal-create-account.md).
* El punto de conexión de streaming desde el que va a transmitir el contenido debe estar en estado **Running** (En ejecución).

    Cuando se crea una cuenta de AMS, se agrega un punto de conexión de streaming **predeterminado** a la cuenta en estado **Stopped** (Detenido). Para iniciar la transmisión del contenido y sacar provecho del empaquetado y cifrado dinámicos, el punto de conexión de streaming debe estar en estado **Running** (En ejecución).

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Objetos utilizados con frecuencia al desarrollar con el modelo OData de AMS

En la ilustración siguiente, se muestran algunos de los objetos que se utilizan más a menudo al desarrollar con el modelo OData de Media Services.

Haga clic en la imagen para verla a tamaño completo.  

<a href="./media/media-services-overview/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-overview/media-services-overview-object-model-small.png"></a> 

Puede ver el modelo completo [aquí](https://media.windows.net/API/$metadata?api-version=2.15).  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Protección del contenido en almacenamiento y entrega de contenido multimedia en streaming sin cifrar

![Flujo de trabajo de VoD](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Cargue un archivo multimedia de alta calidad en un recurso.

    Se recomienda aplicar la opción de cifrado de almacenamiento a su recurso con el fin de proteger su contenido durante la carga y mientras se encuentra en reposo en el almacenamiento.
2. Codifique en conjunto de archivos MP4 de velocidades de bits adaptativas.

    Se recomienda aplicar la opción de cifrado de almacenamiento al recurso de salida con el fin de proteger su contenido mientras se encuentra en reposo.
3. Configure la directiva de entrega de recursos(usada por paquetes dinámicos).

    Si el recurso tiene el almacenamiento cifrado, **asegúrese** de configurar la directiva de entrega de recursos.
4. Publique el recurso mediante la creación de un localizador bajo demanda.
5. Trasmita el contenido publicado.

Para obtener información acerca de la disponibilidad en los centros de datos, consulte la sección [Disponibilidad](#availability).

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Protección del contenido en el almacenamiento, entrega de soportes de streaming cifrados dinámicamente

![Protección con PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Cargue un archivo multimedia de alta calidad en un recurso. Aplique una opción de cifrado de almacenamiento al recurso.
2. Codifique en conjunto de archivos MP4 de velocidades de bits adaptativas. Aplique una opción de cifrado de almacenamiento al recurso de salida.
3. Cree la clave de cifrado de contenido para el recurso que desee que se cifre dinámicamente durante la reproducción.
4. Configure la directiva de autorización de claves de contenido.
5. Configure la directiva de entrega de recursos (usada por el empaquetado y el cifrado dinámicos).
6. Publique el recurso mediante la creación de un localizador bajo demanda.
7. Trasmita el contenido publicado.

Para obtener información acerca de la disponibilidad en los centros de datos, consulte la sección [Disponibilidad](#availability).

## <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Use Análisis multimedia para obtener conocimientos útiles de los vídeos

Análisis multimedia es una colección de componentes de voz y visión que facilita a las empresas y organizaciones obtener conocimiento útil de sus archivos de vídeo. Para más información, consulte [Información general de análisis de Azure Media Services](media-services-analytics-overview.md).

1. Cargue un archivo multimedia de alta calidad en un recurso.
2. Procesar sus vídeos con uno de los servicios de Media Analytics que se describen en la sección [Información general de Media Analytics](media-services-analytics-overview.md).
3. Los procesadores multimedia de Análisis multimedia generan archivos MP4 o JSON. Si un procesador multimedia genera un archivo MP4, puede descargar progresivamente el archivo. Si un procesador multimedia genera un archivo JSON, puede descargar el archivo desde Azure Blob Storage.

Para obtener información acerca de la disponibilidad en los centros de datos, consulte la sección [Disponibilidad](#availability).

## <a name="deliver-progressive-download"></a>Entregar la descarga progresiva

1. Cargue un archivo multimedia de alta calidad en un recurso.
2. Codifique en un solo archivo MP4.
3. Publique el recurso creando un localizador OnDemand o SAS.

    Si utiliza un localizador SAS, el contenido se descarga desde Azure Blob Storage. En este caso, no necesita tener puntos de conexión de streaming en estado iniciado.
4. Descargue contenido de forma progresiva.

## <a id="live_scenarios"></a>Entrega de eventos de streaming en vivo 

1. Ingiera contenido en vivo mediante el uso de varios protocolos de streaming en vivo (por ejemplo, RTMP o Smooth Streaming).
2. (Opcionalmente) Codifique la transmisión en un flujo de datos con velocidad de bits adaptable.
3. Obtenga una vista previa del flujo de datos en vivo.
4. Entregue el contenido mediante protocolos de streaming comunes (por ejemplo, MPEG DASH, Smooth o HLS) directamente a sus clientes o a una red de entrega de contenido (CDN) para ampliar la distribución.

    O bien

    Registre y almacene el contenido recibido para transmitirlo posteriormente (vídeo bajo demanda).

Cuando se realiza streaming en vivo, puede elegir una de las rutas siguientes:

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Uso de canales que reciben transmisión por secuencias en directo con velocidad de bits múltiple de codificadores locales (paso a través)

En el diagrama siguiente se muestran las partes principales de la plataforma AMS que intervienen en el flujo de trabajo de **paso a través** .

![Flujo de trabajo activo](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Para más información, consulte [Uso de canales que reciben streaming en vivo con velocidad de bits múltiple de codificadores locales](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Uso de canales habilitados para realizar la codificación en directo con Azure Media Services

El diagrama siguiente muestra las partes principales de la plataforma AMS que intervienen en el flujo de trabajo de streaming en vivo, en la que se habilita un canal para realizar la codificación en directo con Media Services.

![Flujo de trabajo activo](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Para obtener más información, vea [Uso de canales habilitados para realizar la codificación en directo con Servicios multimedia de Azure](media-services-manage-live-encoder-enabled-channels.md).

Para obtener información acerca de la disponibilidad en los centros de datos, consulte la sección [Disponibilidad](#availability).

## <a name="consuming-content"></a>Consumo de contenido

Azure Media Services proporciona las herramientas que necesita para crear aplicaciones cliente de reproductor enriquecidas y dinámicas para la mayoría de las plataformas, como dispositivos iOS, dispositivos Android, Windows, Windows Phone, Xbox y decodificadores (set-top boxes). El tema siguiente proporciona vínculos a los SDK y Player Framework que puede usar para desarrollar sus propias aplicaciones cliente que pueden consumir contenido multimedia en streaming desde Media Services. Para más información, consulte [Desarrollo de aplicaciones para reproductor de vídeo](media-services-develop-video-players.md)

## <a name="enabling-azure-cdn"></a>Habilitación de CDN de Azure

Media Services admite la integración con Azure CDN. Para obtener información sobre cómo habilitar Azure CDN, consulte [Administración de extremos de streaming en una cuenta de Media Services](media-services-portal-manage-streaming-endpoints.md).

## <a id="scaling"></a>Escalado de una cuenta de Media Services

Los clientes de AMS pueden escalar los puntos de conexión de streaming, el procesamiento multimedia y el almacenamiento en sus cuentas de AMS.

* Los clientes de Media Services pueden elegir un punto de conexión de streaming **estándar** o uno **premium**. Un punto de conexión de streaming **estándar** es adecuado para la mayoría de las cargas de trabajo de streaming. Incluye las mismas características que los puntos de conexión **premium** y escala automáticamente el ancho de banda de salida. 

    Los puntos de conexión de streaming **Premium** son adecuados para cargas de trabajo avanzadas y proporcionan una capacidad de ancho de banda dedicada y escalable. Los clientes que tienen un punto de conexión de streaming **Premium**, de forma predeterminada, obtienen una unidad de streaming (SU). El punto de conexión de streaming puede ampliarse agregando unidades de streaming. Cada unidad de streaming proporciona capacidad de ancho de banda adicional a la aplicación. Para más información acerca de cómo escalar puntos de conexión de streaming **premium**, consulte el tema [Escalado de puntos de conexión de streaming con Azure Portal](media-services-portal-scale-streaming-endpoints.md).

* Una cuenta de Servicios multimedia está asociada con un tipo de unidad reservada que determina la rapidez con la que se procesan las tareas de procesamiento multimedia. Puede elegir uno de los siguientes tipos de unidad reservada: **S1**, **S2** o **S3**. Por ejemplo, el mismo trabajo de codificación se ejecuta más rápido cuando se usa el tipo de unidad reservada **S2** en comparación con el tipo**S1**.

    Además de especificar el tipo de unidad reservada, puede especificar el aprovisionamiento de su cuenta con **unidades reservadas**. El número de unidades reservadas aprovisionadas determina el número de tareas de medios que se pueden procesar de forma simultánea en una cuenta determinada.

    >[!NOTE]
    >Las unidades reservadas sirven para establecer paralelismos en todo el procesamiento multimedia, incluida la indexación de trabajos mediante Azure Media Indexer. Sin embargo, a diferencia de la codificación, la indexación de los trabajos no se procesará más rápido con unidades reservadas de mayor rapidez.

    Para más información, consulte [Escalado del procesamiento de elementos multimedia mediante Azure Portal](media-services-portal-scale-media-processing.md).
* También puede escalar la cuenta de Media Services agregándole cuentas de almacenamiento. Cada cuenta de almacenamiento está limitada a 500 TB. Para ampliar el almacenamiento más allá del límite predeterminado, puede asociar varias cuentas de almacenamiento a una sola cuenta de Media Services. Para más información, consulte [Administración de recursos de Servicios multimedia entre varias cuentas de almacenamiento](meda-services-managing-multiple-storage-accounts.md).

##<a id="availability"></a>Disponibilidad de características de Media Services en centros de datos

En esta sección se proporcionan detalles acerca de la disponibilidad de las características de Media Services en centros de datos.

### <a name="ams-accounts"></a>Cuentas de AMS

#### <a name="availability"></a>Disponibilidad

Se pueden crear cuentas de Media Services en las siguientes regiones: Europa del Norte, Europa Occidental, oeste de EE. UU., este de EE. UU., Sudeste Asiático, Asia Oriental, Japón Occidental, Japón Oriental, Sur de Brasil, India occidental, Sur de India e India central. 

### <a name="streaming-endpoints"></a>Puntos de conexión de streaming 

Los clientes de Media Services pueden elegir un punto de conexión de streaming **estándar** o uno **premium**. Para más información, consulte la sección acerca del [escalado](#scaling).

#### <a name="availability"></a>Disponibilidad

|Nombre|Estado|Centros de datos
|---|---|---|
|Estándar|GA|Todo|
|Premium|GA|Todo|

### <a name="live-encoding"></a>Live Encoding

#### <a name="availability"></a>Disponibilidad

Disponible en todos los centros de datos excepto: Alemania, Sur de Brasil, India occidental, Sur de India e India central. 

### <a name="encoding-media-processors"></a>Codificación de procesadores de multimedia

AMS ofrece dos codificadores a petición **Media Encoder Standard** y **Flujo de trabajo premium de codificación de medios**. Para más información, consulte [Información general y comparación de los codificadores multimedia a petición de Azure](media-services-encode-asset.md). 

#### <a name="availability"></a>Disponibilidad

|Nombre de procesador de multimedia|Estado|Centros de datos
|---|---|---|
|Media Encoder Estándar|GA|Todo|
|Flujo de trabajo del Codificador multimedia|GA|Todos excepto China|

### <a name="analytics-media-processors"></a>Procesadores de multimedia de Analytics

Análisis multimedia es una colección de componentes de voz y visión que facilita a las empresas y organizaciones obtener conocimiento útil de sus archivos de vídeo. Para más información, consulte [Información general de análisis de Azure Media Services](media-services-analytics-overview.md).

#### <a name="availability"></a>Disponibilidad

|Nombre de procesador de multimedia|Estado|Centros de datos
|---|---|---|
|Azure Media Face Detector|Vista previa|Todo|
|Azure Media Hyperlapse|Vista previa|Todo|
|Azure Media Indexer|GA|Todo|
|Azure Media Motion Detector|Vista previa|Todo|
|Azure Media OCR|Vista previa|Todo|
|Azure Media Redactor|Vista previa|Todo|
|Estabilizador de Azure Media|Vista previa|Todo|
|Azure Media Video Thumbnails|Vista previa|Todo|
|Azure Media Indexer 2|Vista previa|Todos excepto China y la región Gobierno Federal|

### <a name="protection"></a>Protección

Servicios multimedia de Microsoft Azure le permite proteger su contenido multimedia desde el momento en que deja el equipo a través de almacenamiento, procesamiento y entrega. Para más información, consulte [Protección de contenido de AMS](media-services-content-protection-overview.md).

#### <a name="availability"></a>Disponibilidad

|Cifrado|Estado|Centros de datos|
|---|---|---| 
|Almacenamiento|GA|Todo|
|Claves AES-128|GA|Todo|
|Fairplay|GA|Todo|
|PlayReady|GA|Todo|
|Widevine|GA|Todos, excepto Alemania, el Gobierno Federal y China.

### <a name="reserved-units-rus"></a>Unidades reservadas (RU)

El número de unidades reservadas de codificación aprovisionadas determina el número de tareas de medios que se pueden procesar de forma simultánea en una cuenta determinada. 

Para más información, consulte la sección acerca del [escalado](#scaling).

#### <a name="availability"></a>Disponibilidad

Está disponible en todos los centros de datos.

### <a name="reserved-unit-ru-type"></a>Tipo de unidad reservada (RU)

Una cuenta de Media Services está asociada con un tipo de unidad reservada, lo que determina la rapidez con la que se procesan las tareas de procesamiento multimedia. Puede elegir uno de los siguientes tipos de unidad reservada: S1, S2 o S3.

Para más información, consulte la sección acerca del [escalado](#scaling).

#### <a name="availability"></a>Disponibilidad

|Nombre de tipo de RU|Estado|Centros de datos
|---|---|---|
|S1|GA|Todo|
|S2|GA|Todos, excepto Sur de Brasil e India occidental|
|S3|GA|Todos, excepto India occidental|

## <a name="next-steps"></a>Pasos siguientes

Consulte las rutas de aprendizaje de Servicios multimedia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

