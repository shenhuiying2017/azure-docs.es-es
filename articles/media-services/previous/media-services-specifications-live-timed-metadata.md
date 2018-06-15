---
title: 'Azure Media Services: Señalización de metadatos con tiempo en streaming en vivo | Microsoft Docs'
description: Esta especificación describe los dos modos que Media Services admite para señalizar metadatos con tiempo en streaming en vivo. Esto incluye compatibilidad con las señales genéricas de metadatos con tiempo, así como con la señalización SCTE-35 para una inserción de anuncios.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2018
ms.author: johndeu;
ms.openlocfilehash: 2e736872dc3e471af7c5b3f758516910a02067fe
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786084"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Señalización de metadatos con tiempo en streaming en vivo


## <a name="1-introduction"></a>1 Introducción 
Para facilitar la inserción de anuncios o eventos personalizados en un reproductor de cliente, los medios de difusión a menudo usan metadatos con tiempo insertados en el vídeo. Para habilitar estos escenarios, Media Services proporciona compatibilidad con el transporte de metadatos con tiempo junto con los medios, desde el punto de ingesta del canal de streaming en vivo a la aplicación cliente.
Esta especificación describe dos modos que admite Media Services para metadatos con tiempo en señales de streaming en vivo:

1. Señalización [SCTE-35] que sigue los procedimientos recomendados descritos en [SCTE-67]

2. Un modo de señalización genérico de metadatos con tiempo, para mensajes que no son [SCTE-35]

### <a name="12-conformance-notation"></a>1.2 Anotación de conformidad
Las palabras clave "DEBE", "NO DEBE", "SE REQUIERE", "TENDRÁ QUE", "NO DEBERÁ", "DEBERÍA", "NO DEBERÍA", "RECOMENDADO", "PUEDE" y "OPCIONAL" de este documento se deben interpretar como se describe en RFC 2119

### <a name="13-terms-used"></a>1.3 Términos empleados

| Término              | Definición                                                                                                                                                                                                                       |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tiempo de presentación | Hora en que se presenta un evento en un visor. La hora representa el momento en la escala de tiempo de los elementos multimedia en que un visor verá el evento. Por ejemplo, el tiempo de presentación de un mensaje de comando SCTE-35 splice_info() es splice_time(). |
| Hora de llegada      | La hora a la que llega un mensaje de evento. Este tiempo normalmente es distinto del tiempo de presentación del evento, ya que los mensajes de eventos se envían antes del tiempo de presentación del evento.                                     |
| Pista dispersa      | pista de elementos multimedia que no es continua y que está sincronizada con una pista principal o de control.                                                                                                                                    |
| Origen            | El servicio Azure Media Streaming                                                                                                                                                                                                |
| Receptor del canal      | El servicio Azure Media Live Streaming Service                                                                                                                                                                                           |
| HLS               | Protocolo Apple HTTP Live Streaming                                                                                                                                                                                               |
| DASH              | Streaming adaptable dinámico a través de HTTP                                                                                                                                                                                             |
| Smooth            | Protocolo de Smooth Streaming                                                                                                                                                                                                        |
| MPEG2-TS          | Secuencias de transporte MPEG-2                                                                                                                                                                                                         |
| RTMP              | Protocolo multimedia en tiempo real                                                                                                                                                                                                    |
| uimsbf            | Entero sin signo, el bit más significativo aparece en primer lugar.                                                                                                                                                                                    |

-----------------------

## <a name="2-timed-metadata-ingest"></a>2 Ingesta de metadatos con tiempo
## <a name="21-rtmp-ingest"></a>2.1 Ingesta de RTMP
RTMP admite señales de metadatos con tiempo enviadas como mensajes de pila de AMF insertados en la secuencia RTMP. Los mensajes de pila se pueden enviar algo antes del evento real o antes de que se produzca una inserción de anuncio. Para que este escenario sea viable, la hora real de la inserción o segmentación se envía en el mensaje de pila. Para más información, consulte [AMF0].

En la siguiente tabla se describe el formato de la carga de mensajes de AMF que Media Services ingerirá.

El nombre del mensaje de AMF se puede utilizar para diferenciar varios flujos de eventos del mismo tipo.  En el caso de los mensajes [SCTE-35], el nombre del mensaje de AMF DEBE ser “onAdCue” como se recomienda en [SCTE-67].  Se DEBEN ignorar todos los campos que no aparezcan en la siguiente lista, para que la innovación de este diseño no se vea comprometida en el futuro.

### <a name="signal-syntax"></a>Sintaxis de señal
Para el modo simple de RTMP, Media Services admite un único mensaje de pila de AMF denominado "onAdCue" con el formato siguiente:

### <a name="simple-mode"></a>Modo simple

| Nombre del campo | Tipo de campo | ¿Necesario? | Descripciones                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| cue        | string     | Obligatorio | El mensaje de evento.  Debe ser "SpliceOut" para designar una inserción de modo sencillo.                                              |
| id         | string     | Obligatorio | Un identificador único que describe la inserción o el segmento. Identifica la instancia del mensaje                            |
| duration   | Number     | Obligatorio | Duración de la inserción. Las unidades son fracciones de segundos.                                                                |
| elapsed    | Number     | Opcional | Cuando se repite la señal para que sea compatible con la sintonización, este campo indicará la cantidad de tiempo de la presentación que ha transcurrido desde que comenzó la inserción. Las unidades son fracciones de segundos. Cuando se usa el modo simple, este valor no debe superar la duración original de la inserción.                                                  |
| Twitter en tiempo       | Number     | Obligatorio | Debe ser el tiempo de la inserción, en el tiempo de presentación. Las unidades son fracciones de segundos.                                     |

---------------------------

### <a name="scte-35-mode"></a>Modo SCTE-35

| Nombre del campo | Tipo de campo | ¿Necesario? | Descripciones                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| cue        | string     | Obligatorio | El mensaje de evento.  En el caso de los mensajes [SCTE-35], este DEBE ser la sección splice_info_section() de codificación binaria (IETF RFC 4648) en base64, para que los mensajes se envíen a los clientes HLS, Smooth y Dash conforme a [SCTE-67].                                              |
| Tipo       | string     | Obligatorio | Un URN o URL que identifica el esquema del mensaje. Por ejemplo, "urn:example:signaling:1.0".  En el caso de los mensajes [SCTE-35], este DEBE ser "urn:scte:scte35:2013a:bin", para que los mensajes se envíen a los clientes HLS, Smooth y Dash conforme a [SCTE-67].  |
| id         | string     | Obligatorio | Un identificador único que describe la inserción o el segmento. Identifica la instancia del mensaje.  Los mensajes con una semántica equivalente deben tener el mismo valor.|
| duration   | Number     | Obligatorio | La duración del evento o segmento de ad-splice, si se conoce. Si no se conoce, el valor debería ser 0.                                                                 |
| elapsed    | Number     | Opcional | Cuando se repite la señal de anuncio de [SCTE-35] para realizar la sintonización, este campo indicará la cantidad de tiempo de la presentación que ha transcurrido desde que comenzó la inserción. Las unidades son fracciones de segundos. En el modo [SCTE-35], este valor puede superar la duración original especificada de la inserción o segmentación.                                                  |
| Twitter en tiempo       | Number     | Obligatorio | El tiempo de presentación del evento o inserción con ad-splice.  El tiempo de presentación y la duración se DEBEN alinear con los puntos de acceso de secuencias (SAP) del tipo 1 o 2, como se definió en [ISO-14496-12] en el anexo I. En el caso de la salida de HLS, el tiempo y la duración se DEBEN alinear con los límites de los segmentos. El tiempo de presentación y la duración de los diferentes mensajes de eventos del mismo flujo de eventos NO DEBE solaparse. Las unidades son fracciones de segundos.

---------------------------

#### <a name="211-cancelation-and-updates"></a>2.1.1 Cancelación y actualizaciones

Los mensajes se pueden cancelar o actualizar mediante el envío de varios mensajes con el mismo tiempo de presentación y el mismo identificador. El tiempo de presentación y el identificador identifican de forma exclusiva el evento. El último mensaje recibido durante un tiempo de presentación específico que cumple las restricciones de cuña anterior es el mensaje sobre el que se actúa. El evento actualizado reemplaza a todos los mensajes recibidos con anterioridad. La restricción de cuña anterior es de cuatro segundos. Se actuará sobre aquellos mensajes recibidos al menos cuatro segundos antes del tiempo de presentación.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Ingesta de MP4 fragmentado (Smooth Streaming)
Consulte [LIVE-FMP4] para conocer los requisitos sobre la ingesta de secuencias en vivo. Las secciones siguientes proporcionan detalles sobre la ingesta de metadatos de presentación con tiempo.  Los metadatos de presentación con tiempo se ingieren como una pista dispersa, lo cual se define en el cuadro Live Server Manifest Box (consulte MS-SSTR) y en Movie Box (‘moov’).  Cada fragmento disperso consta de un cuadro Movie Fragment Box (‘moof’) y un cuadro Media Data Box (‘mdat’), en el que el cuadro ‘mdat’ es el mensaje binario.

#### <a name="221-live-server-manifest-box"></a>2.2.1 Cuadro Live Server Manifest Box
La pista dispersa se DEBE declarar en el cuadro Live Server Manifest Box con una entrada \<textstream\> y DEBE tener el siguiente conjunto de atributos:

| **Nombre del atributo** | **Tipo de campo** | **¿Necesario?** | **Descripción**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Number         | Obligatorio      | DEBE ser "0", lo cual indica una pista con una velocidad de bits desconocida, variable.                                                                                                                                                                                                 |
| parentTrackName    | string         | Obligatorio      | DEBE ser el nombre de la pista principal, aquella con la que se alinean en la escala de tiempo los códigos de tiempo de las pistas dispersas. La pista principal no puede ser una pista dispersa.                                                                                                                    |
| manifestOutput     | boolean        | Obligatorio      | DEBE ser "true", para indicar que la pista dispersa se insertará en el manifiesto de cliente de Smooth.                                                                                                                                                               |
| Subtype            | string         | Obligatorio      | DEBE ser el código de cuatro caracteres "DATA".                                                                                                                                                                                                                         |
| Esquema             | string         | Obligatorio      | DEBE ser un URN o URL que identifica el esquema del mensaje. Por ejemplo, "urn:example:signaling:1.0". En el caso de los mensajes [SCTE-35], este DEBE ser "urn:scte:scte35:2013a:bin", para que los mensajes se envíen a los clientes HLS, Smooth y Dash conforme a [SCTE-67]. |
| trackName          | string         | Obligatorio      | DEBE ser el nombre de la pista dispersa. trackName se puede utilizar para diferenciar varios flujos de eventos con el mismo esquema. Cada flujo de eventos único debe tener un nombre de pista único.                                                                           |
| timescale          | Number         | Opcional      | DEBE ser la escala de tiempo de la pista principal.                                                                                                                                                                                                                      |

-------------------------------------

### <a name="222-movie-box"></a>2.2.2 Cuadro Movie Box

El cuadro Movie Box (‘moov’) sigue al cuadro Live Server Manifest Box como parte del encabezado de flujo de una pista dispersa.

El cuadro ‘moov’ DEBE contener un cuadro **TrackHeaderBox (‘tkhd’)** tal y como se define en [ISO-14496-12] con las siguientes restricciones:

| **Nombre del campo** | **Tipo de campo**          | **¿Necesario?** | **Descripción**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| duration       | Entero sin signo de 64 bits | Obligatorio      | DEBE ser 0, ya que el cuadro de la pista tiene cero ejemplos y la duración total de los ejemplos del cuadro de la pista es 0. |
-------------------------------------

El cuadro ‘moov’ DEBE contener un cuadro **HandlerBox (‘hdlr’)** tal y como se define en [ISO-14496-12] con las siguientes restricciones:

| **Nombre del campo** | **Tipo de campo**          | **¿Necesario?** | **Descripción**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | Entero sin signo de 32 bits | Obligatorio      | DEBE ser ‘meta’. |
-------------------------------------

El cuadro ‘stsd’ DEBE contener un cuadro MetaDataSampleEntry con un nombre de codificación que se define en [ISO-14496-12].  Por ejemplo, para los mensajes de SCTE-35 el nombre de codificación DEBE ser 'scte'.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Cuadros Movie Fragment Box y Media Data Box

Los fragmentos de pistas dispersas constan de un cuadro Movie Fragment Box (‘moof’) y uno Media Data Box (‘mdat’).

El cuadro MovieFragmentBox ("moof") DEBE contener un cuadro **TrackFragmentExtendedHeaderBox ("uuid")**, tal y como se define en [MS-SSTR] con los siguientes campos:

| **Nombre del campo**         | **Tipo de campo**          | **¿Necesario?** | **Descripción**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | Entero sin signo de 64 bits | Obligatorio      | DEBE ser la hora de llegada del evento. Este valor alinea el mensaje con la pista principal.   |
| fragment_duration      | Entero sin signo de 64 bits | Obligatorio      | DEBE ser la duración del evento. La duración puede ser cero para indicar que es desconocida. |

------------------------------------


El cuadro MediaDataBox ('mdat') DEBE tener el formato siguiente:

| **Nombre del campo**          | **Tipo de campo**                   | **¿Necesario?** | **Descripción**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | Entero sin signo de 32 bits (uimsbf) | Obligatorio      | Determina el formato del contenido del cuadro 'mdat'. Se ignorarán las versiones no reconocidas. Actualmente, la única versión admitida es: 1.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | Entero sin signo de 32 bits (uimsbf) | Obligatorio      | Identifica la instancia del mensaje. Los mensajes con una semántica equivalente deben tener el mismo valor, es decir, será suficiente con el procesamiento de cualquier cuadro de mensaje de evento con el mismo identificador.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | Entero sin signo de 32 bits (uimsbf) | Obligatorio      | La suma del valor de fragment_absolute_time, especificado en TrackFragmentExtendedHeaderBox, y el de presentation_time_delta DEBE ser el tiempo de presentación del evento. El tiempo de presentación y la duración se DEBEN alinear con los puntos de acceso de secuencias (SAP) del tipo 1 o 2, como se definió en [ISO-14496-12] en el anexo I. En el caso de la salida de HLS, el tiempo y la duración se DEBEN alinear con los límites de los segmentos. El tiempo de presentación y la duración de los diferentes mensajes de eventos del mismo flujo de eventos NO DEBE solaparse. |
| Mensaje                 | byte array                       | Obligatorio      | El mensaje de evento. En el caso de los mensajes [SCTE-35], el mensaje es la sección binaria splice_info_section(), aunque [SCTE-67] recomienda otra cosa. En el caso de los mensajes [SCTE-35], este DEBE ser la sección splice_info_section(), para que los mensajes se envíen a los clientes HLS, Smooth y Dash conforme a [SCTE-67]. En el caso de los mensajes [SCTE-35], la sección binaria splice_info_section() es la carga del cuadro ‘mdat’ y NO está codificada en base 64.                                                            |

------------------------------


### <a name="224-cancelation-and-updates"></a>2.2.4 Cancelación y actualizaciones
Los mensajes se pueden cancelar o actualizar mediante el envío de varios mensajes con el mismo tiempo de presentación y el mismo identificador.  El tiempo de presentación y el identificador identifican de forma exclusiva el evento. El último mensaje recibido durante un tiempo de presentación específico que cumple las restricciones de cuña anterior es el mensaje sobre el que se actúa. El mensaje actualizado reemplaza a todos los mensajes recibidos con anterioridad.  La restricción de cuña anterior es de cuatro segundos. Se actuará sobre aquellos mensajes recibidos al menos cuatro segundos antes del tiempo de presentación. 


## <a name="3-timed-metadata-delivery"></a>3 Entrega de metadatos con tiempo

Los datos de los flujos de eventos son opacos para Media Services. Media Services simplemente pasa tres fragmentos de información entre el punto de conexión de ingesta y el punto de conexión del cliente. Las siguientes propiedades se entregan al cliente, conforme a lo que se especifica en [SCTE-67] y en el protocolo de streaming del cliente:

1.  Esquema: un URN o URL que identifica el esquema del mensaje.

2.  Tiempo de presentación: el tiempo de presentación del evento en la escala de tiempo de los elementos multimedia.

3.  Duración: la duración del evento.

4.  ID: el identificador único opcional para el evento.

5.  Mensaje: los datos del evento.


## <a name="31-smooth-streaming-delivery"></a>3.1 Entrega de Smooth Streaming

Consulte los detalles de administración de pistas dispersas [MS-SSTR].

#### <a name="smooth-client-manifest-example"></a>Ejemplo de manifiesto de cliente de Smooth
~~~ xml
<?xml version=”1.0” encoding=”utf-8”?>
<SmoothStreamingMedia MajorVersion=”2” MinorVersion=”0” TimeScale=”10000000” IsLive=”true” Duration=”0”
  LookAheadFragmentCount=”2” DVRWindowLength=”6000000000”>

  <StreamIndex Type=”video” Name=”video” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(video={start time})”>
    <QualityLevel Index=”0” Bitrate=”230000”
      CodecPrivateData=”250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <QualityLevel Index=”1” Bitrate=”305000”
      CodecPrivateData=”250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”audio” Name=”audio” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(audio={start time})”>
    <QualityLevel Index=”0” Bitrate=”96000” CodecPrivateData=”1000030000000000000000000000E00042C0”
      FourCC=”WMAP” AudioTag=”354” Channels=”2” SamplingRate=”44100” BitsPerSample=”16” PacketSize=”4459”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”text” Name=”scte35-event-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013a:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- <f> contains base-64 encoded splice_info_section() message 
    <c t=”600000000” d=”300000000”>
<f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia> 
~~~ 

## <a name="32--apple-hls-delivery"></a>3.2 Entrega de Apple HLS
Los metadatos con tiempo de Apple HTTP Live Streaming (HLS) se pueden insertar en la lista de reproducción de segmentos en una etiqueta M3U personalizada.  El nivel de aplicación puede analizar la lista de reproducción M3U y procesar las etiquetas correspondientes. Azure Media Services insertará los metadatos con tiempo en la etiqueta EXT-X-CUE que se define en [HLS].  DynaMux usa la etiqueta EXT-X-CUE actualmente para los mensajes del tipo ADI3.  Para admitir mensajes de SCTE-35 y los que no son de SCTE-35, establezca los atributos de la etiqueta EXT-X-CUE tal como se definen a continuación:

| **Nombre del atributo** | **Tipo**                      | **¿Necesario?**                             | **Descripción**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CUE                | cadena entrecomillada                 | Obligatorio                                  | El mensaje codificado como una cadena en base 64 tal y como se describe en [IETF RFC 4648](http://tools.ietf.org/html/rfc4648). En el caso de los mensajes [SCTE-35], es la sección splice_info_section() codificada en base 64.                                                                                                |
| TIPO               | cadena entrecomillada                 | Obligatorio                                  | Un URN o URL que identifica el esquema del mensaje. Por ejemplo, "urn:example:signaling:1.0". En el caso de los mensajes [SCTE-35], el tipo tiene el valor especial "scte35".                                                                                                                                |
| ID                 | cadena entrecomillada                 | Obligatorio                                  | Un identificador único para el evento. Si no se especifica el identificador cuando se ingiere el mensaje, Azure Media Services generará un identificador exclusivo.                                                                                                                                          |
| DURATION           | número de punto flotante decimal | Obligatorio                                  | Duración del evento. Si no se conoce, el valor debería ser 0. Las unidades son fracciones de segundos.                                                                                                                                                                                           |
| ELAPSED            | número de punto flotante decimal | Opcional, pero obligatorio para la ventana deslizante | Cuando se repite la señal para que admita una ventana de presentación deslizante, este campo DEBE ser la cantidad de tiempo de la presentación que ha transcurrido desde que comenzó el evento. Las unidades son fracciones de segundos. Este valor puede superar la duración original especificada de la inserción o segmentación. |
| TIME               | número de punto flotante decimal | Obligatorio                                  | El tiempo de presentación del evento. Las unidades son fracciones de segundos.                                                                                                                                                                                                                    |


El nivel de aplicación del reproductor HLS usará TYPE para identificar el formato del mensaje, descodificar el mensaje, aplicar las conversiones de tiempo necesarias y procesar el evento.  Los eventos se sincronizan en la lista de reproducción de segmentos de la pista principal, según la marca de tiempo del evento.  Se insertan antes del segmento más cercano (etiqueta #EXTINF).

#### <a name="hls-segment-playlist-example"></a>Ejemplo de lista de reproducción de segmentos HLS
~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:6
#EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
#EXTINF:6.000000,no-desc
Fragments(video=0,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=60000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
#EXT-X-CUE: ID=”metadata-12.000000”,TYPE=”urn:example:signaling:1.0”,TIME=”12.000000”, DURATION=”18.000000”,CUE=”HrwOi8vYmWVkaWEvhhaWFRlRDa=”
Fragments(video=120000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=180000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=240000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=300000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=360000000,format=m3u8-aapl)
#EXT-X-CUE: ID=”metadata-42.000000”,TYPE=”urn:example:signaling:1.0”,TIME=”42.000000”, DURATION=”60.000000”,CUE=”PD94bWwgdm0iMS4wIiBlbmNvpD4=”
#EXTINF:6.000000,no-desc
Fragments(video=420000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=480000000,format=m3u8-aapl)
…
~~~

#### <a name="hls-message-handling"></a>Administración de mensajes HLS

Los eventos se señalizan en la lista de reproducción de segmentos de cada vídeo y pista de audio. La posición de la etiqueta EXT-X-CUE siempre DEBE estar inmediatamente antes del primer segmento HLS (fuera de la inserción o al inicio del segmento) o inmediatamente después del último segmento HLS (dentro de la inserción o al final del segmento) al que hacen referencia sus atributos TIME y DURATION, como requiera [ HLS].

Cuando se habilita una ventana de presentación deslizante, la etiqueta EXT-X-CUE se DEBE repetir con la suficiente frecuencia para que la inserción o segmento estén siempre descritos por completo en la lista de reproducción de segmentos, y se DEBE usar el atributo ELAPSED para indicar la cantidad de tiempo que la inserción o segmento han estado activos, como requiera [HLS].

Cuando se habilita una ventana de presentación deslizante, se quitan las etiquetas EXT-X-CUE de la lista de reproducción de segmentos si el tiempo de los elementos multimedia al que hacen referencia se ha extendido más allá de la ventana de presentación deslizante.

## <a name="33--dash-delivery"></a>3.3 Entrega DASH
[DASH] proporciona tres maneras de señalizar eventos:

1.  Eventos señalizados en el MPD
2.  Eventos señalizados en banda en la representación (mediante el cuadro de mensaje de evento (‘emsg’)
3.  Una combinación de 1 y 2

Los eventos señalizados en el MPD resultan útiles para el streaming de vídeo bajo demanda ya que los clientes tienen acceso a todos los eventos justo después de descargar el MPD. La solución en banda es útil para el streaming en vivo ya que los clientes no necesitan volver a descargar el MPD. Para la segmentación basada en tiempo, el cliente determina el URL del siguiente segmento agregando la duración y la marca de tiempo del segmento actual. Si esa solicitud produce un error, el cliente lo contempla como una interrupción y descarga el MPD pero, en caso contrario, continuará realizando el streaming sin descargarlo.

Azure Media Services realizará la señalización en el MPD y la señalización en banda mediante el cuadro de mensaje de evento.

#### <a name="mpd-signaling"></a>Señalización de MPD

Los eventos se señalizarán en el MPD mediante el elemento EventStream que aparece en el elemento Period.

El elemento EventStream tiene los siguientes atributos:

| **Nombre del atributo** | **Tipo**                | **¿Necesario?** | **Descripción**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | string                  | Obligatorio      | Identifica el esquema del mensaje. El esquema se establece en el valor del atributo Scheme en el cuadro de Live Server Manifest box. El valor DEBE ser un URN o URL que identifique el esquema del mensaje. Por ejemplo, "urn:example:signaling:1.0".                                                                |
| value              | string                  | Opcional      | Un valor de cadena adicional que usan los propietarios del esquema para personalizar la semántica del mensaje. Para diferenciar entre varios flujos de eventos con el mismo esquema, se DEBE establecer el valor en el nombre del flujo de eventos (trackName para la ingesta con Smooth o el nombre del mensaje de AMF para la ingesta de RTMP). |
| Escala de tiempo          | Entero sin signo de 32 bits | Obligatorio      | La escala de tiempo, en tics por segundo, de los campos de tiempo y duración del cuadro 'emsg'.                                                                                                                                                                                                       |


El elemento EventStream contiene cero o más elementos de eventos y tienen los siguientes atributos:

| **Nombre del atributo**  | **Tipo**                | **¿Necesario?** | **Descripción**                                                                                                                                                                                                             |
|---------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| presentation_time   | Entero sin signo de 64 bits | Opcional      | DEBE ser el tiempo de presentación del elemento multimedia del evento relativo al inicio del período. El tiempo de presentación y la duración se DEBERÍAN alinear con los puntos de acceso de secuencias (SAP) del tipo 1 o 2, tal como se define en [ISO-14496-12], en el Anexo I. |
| duration            | Entero sin signo de 32 bits | Opcional      | Duración del evento. Esta se DEBE omitir si la duración es desconocida.                                                                                                                                                 |
| id                  | Entero sin signo de 32 bits | Opcional      | Identifica la instancia del mensaje. Los mensajes con una semántica equivalente deben tener el mismo valor. Si no se especifica el identificador cuando se ingiere el mensaje, Azure Media Services generará un identificador exclusivo.             |
| Event element value | string                  | Obligatorio      | El mensaje de evento como una cadena en base 64 tal y como se describe en [IETF RFC 4648](http://tools.ietf.org/html/rfc4648).                                                                                                                   |

#### <a name="xml-syntax-and-example-for-dash-manifest-mpd-signaling"></a>Sintaxis XML y ejemplo de señalización de manifiesto de DASH (MPD)

~~~ xml
<!-- XML Syntax -->
<xs:complexType name=”EventStreamType”>
  <xs:sequence>
    <xs:element name=”Event” type=”EventType” minOccurs=”0” maxOccurs=”unbounded”/>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute ref=”xlink:href”/>
  <xs:attribute ref=”xlink:actuate” default=”onRequest”/>
  <xs:attribute name=”schemeIdUri” type=”xs:anyURI” use=”required”/>
  <xs:attribute name=”value” type=”xs:string”/>
  <xs:attribute name=”timescale” type=”xs:unsignedInt”/>
</xs:complexType>
<!-- Event -->
<xs:complexType name=”EventType”>
  <xs:sequence>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute name=”presentationTime” type=”xs:unsignedLong” default=”0”/>
  <xs:attribute name=”duration” type=”xs:unsignedLong”/>
  <xs:attribute name=”id” type=”xs:unsignedInt”/>
  <xs:anyAttribute namespace=”##other” processContents=”lax”/>
</xs:complexType><?xml version=”1.0” encoding=”utf-8”?>


<!-- Example Section in MPD -->

<EventStream schemeIdUri=”urn:example:signaling:1.0” timescale=”1000” value=”player-statistics”>
  <Event presentationTime=”0” duration=”10000” id=”0”> PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</Event>
  <Event presentationTime=”20000” duration=”10000” id=”1”> PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</Event>
</EventStream>
~~~

>[!NOTE]
>Tenga en cuenta que presentationTime es el tiempo de presentación del evento, no la hora de llegada del mensaje.
>

### <a name="431-in-band-event-message-box-signaling"></a>4.3.1 Señalización mediante el cuadro de mensajes de evento en banda
Un flujo de eventos en banda requiere que el MPD tenga el elemento InbandEventStream en el nivel del conjunto de adaptación.  Este elemento tiene un atributo schemeIdUri obligatorio y un atributo opcional de escala de tiempo que también aparecen en el cuadro de mensajes de evento ('emsg').  Los cuadros de mensajes de evento con los identificadores de esquema que no estén definidos en el MPD no deben estar presentes. Si un cliente DASH detecta un cuadro de mensajes de evento con un esquema que no está definido en el MPD, se espera que este lo ignore.
El cuadro de mensajes de evento ('emsg') proporciona señalización para eventos genéricos relacionados con el tiempo de presentación del elemento multimedia. Si existe, cualquier cuadro 'emsg' debe colocarse antes de cualquier cuadro 'moof'.

### <a name="dash-event-message-box-emsg"></a>Cuadro de mensajes de evento de DASH 'emsg'
~~~
Box Type: `emsg’
Container: Segment
Mandatory: No
Quantity: Zero or more
~~~

~~~ c
aligned(8) class DASHEventMessageBox extends FullBox(‘emsg’, version = 0, flags =0) 
{
    string scheme_id_uri;
    string value;
    unsigned int(32) timescale;
    unsigned int(32) presentation_time_delta;
    unsigned int(32) event_duration;
    unsigned int(32) id;
    unsigned int(8) message_data[];
}
~~~

Los campos de DASHEventMessageBox se definen a continuación:

| **Nombre del campo**          | **Tipo de campo**          | **¿Necesario?** | **Descripción**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | string                  | Obligatorio      | Identifica el esquema del mensaje. El esquema se establece en el valor del atributo Scheme en el cuadro de Live Server Manifest box. El valor DEBE ser un URN o URL que identifique el esquema del mensaje. Por ejemplo, "urn:example:signaling:1.0". En el caso de los mensajes [SCTE-35], este adopta el valor especial "urn:scte:scte35:2013a:bin", aunque [SCTE-67] recomienda otra cosa. |
| Valor                   | string                  | Obligatorio      | Un valor de cadena adicional que usan los propietarios del esquema para personalizar la semántica del mensaje. Para diferenciar entre varios flujos de eventos con el mismo esquema, se establecerá el valor en el nombre del flujo de eventos (trackName para la ingesta con Smooth o el nombre del mensaje de AMF para la ingesta de RTMP).                                                                  |
| Escala de tiempo               | Entero sin signo de 32 bits | Obligatorio      | La escala de tiempo, en tics por segundo, de los campos de tiempo y duración del cuadro 'emsg'.                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | Entero sin signo de 32 bits | Obligatorio      | El diferencial de tiempo de presentación del elemento multimedia entre el tiempo de presentación del evento y el tiempo de presentación más temprano de este segmento. El tiempo de presentación y la duración se DEBERÍAN alinear con los puntos de acceso de secuencias (SAP) del tipo 1 o 2, tal como se define en [ISO-14496-12], en el Anexo I.                                                                                            |
| event_duration          | Entero sin signo de 32 bits | Obligatorio      | La duración del evento, o 0xFFFFFFFF para indicar una duración desconocida.                                                                                                                                                                                                                                                                                          |
| Id                      | Entero sin signo de 32 bits | Obligatorio      | Identifica la instancia del mensaje. Los mensajes con una semántica equivalente deben tener el mismo valor. Si no se especifica el identificador cuando se ingiere el mensaje, Azure Media Services generará un identificador exclusivo.                                                                                                                                                    |
| Message_data            | byte array              | Obligatorio      | El mensaje de evento. En el caso de los mensajes [SCTE-35], la información del mensaje es la sección binaria splice_info_section(), aunque [SCTE-67] recomienda otra cosa.                                                                                                                                                                                                                                 |

### <a name="332-dash-message-handling"></a>3.3.2 Administración de mensajes DASH

Los eventos se señalizan en banda, en el cuadro 'emsg', para pistas de audio y de vídeo.  La señalización se produce en todas las solicitudes de segmento en los que presentation_time_delta es de 15 segundos o menos. Cuando se habilita una ventana de presentación deslizante, los mensajes de evento se eliminan del MPD si la suma del tiempo y la duración del mensaje de evento es inferior al tiempo de los datos del elemento multimedia en el manifiesto.  En otras palabras, los mensajes de evento se eliminarán del manifiesto si el tiempo del elemento multimedia al que hacen referencia se ha extendido más allá de la ventana de presentación deslizante.

## <a name="4-scte-35-ingest"></a>4 Ingesta con SCTE-35

Los mensajes [SCTE-35] se ingieren en formato binario mediante el esquema **“urn:scte:scte35:2013a:bin”** para la ingesta con Smooth y el tipo **"scte35"** para la ingesta con RTMP. Para facilitar la conversión de la temporización de [SCTE-35], que se basa en las marcas de tiempo de presentación (PTS) de flujos de transporte MPEG-2, el tiempo de presentación del evento proporciona una asignación entre PTS (pts_time + pts_adjustment de splice_time()) y la escala de tiempo del elemento multimedia (el campo fragment_absolute_time para la ingesta con Smooth y el campo time para la ingesta con RTMP). La asignación es necesaria ya que el valor PTS de 33 bits cambia cada 26,5 horas aproximadamente.

En la ingesta con Smooth Streaming el cuadro Media Data Box ('mdat') DEBE contener el valor de **splice_info_section()** definido en la tabla 8-1 de [SCTE-35]. Para la ingesta con RTMP, se establece el atributo cue del mensaje de AMF en la sección **splice_info_section()** codificada en base 64. Si los mensajes tienen el formato descrito anteriormente, se envían a los clientes HLS, Smooth y Dash conforme a [SCTE-67].


## <a name="5-references"></a>5 Referencias

**[SCTE-35]** ANSI/SCTE 35 2013a – Digital Program Insertion Cueing Message for Cable, 2013a

**[SCTE-67]** ANSI/SCTE 67 2014 –Recommended Practice for SCTE 35: Digital Program Insertion Cueing Message for Cable

**[DASH]** ISO/IEC 23009-1 2014 – Information technology – Dynamic adaptive streaming over HTTP (DASH) – Part 1: Media Presentation description and segment formats, 2nd edition

**[HLS]** [“HTTP Live Streaming”, draft-pantos-http-live-streaming-14, 14 de octubre, 2014,](http://tools.ietf.org/html/draft-pantos-http-live-streaming-14)

**[MS-SSTR]** [“Microsoft Smooth Streaming Protocol”, 15 de mayo, 2014](http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

**[AMF0]** ["Action Message Format AMF0"](http://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)

**[LIVE-FMP4]** [Especificación de la ingesta en directo de MP4 fragmentado de Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)

**[ISO-14496-12]** ISO/IEC 14496-12: Part 12 ISO base media file format, Fourth Edition 2012-07-15.

**[RTMP]** [“Adobe’s Real-Time Messaging Protocol”, 21 de diciembre, 2012](https://www.adobe.com/devnet/rtmp.html) 

------------------------------------------

## <a name="next-steps"></a>Pasos siguientes
Ver las rutas de aprendizaje de Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
