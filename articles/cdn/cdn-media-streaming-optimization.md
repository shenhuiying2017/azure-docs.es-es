---
title: "Optimización del streaming multimedia mediante la red CDN de Azure"
description: "Optimización del streaming de archivos multimedia para entrega sin problemas"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: v-semcev
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 582eb69ccd2830643644760753cca1c96d72128d
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017

---
# <a name="media-streaming-optimization-via-azure-cdn"></a>Optimización del streaming multimedia mediante la red CDN de Azure 
 
El vídeo de alta definición cada vez está más presente en Internet, lo que crea varios problemas en la entrega eficiente de estos archivos tan grandes a través de Internet. Los clientes esperan una reproducción fluida de VoD o de recursos de vídeo en vivo en una variedad de redes y clientes en todo el mundo. Proporcionar un mecanismo de entrega más rápido y eficiente para archivos de streaming multimedia es fundamental para garantizar una experiencia sin complicaciones y divertida para el consumidor.  

El streaming en vivo de contenido multimedia es especialmente difícil de entregar, no solo debido a los grandes tamaños y al número de usuarios simultáneos, sino también porque los retardos son un punto conflictivo para dichos usuarios. Las transmisiones en vivo no se pueden almacenar en caché por adelantado y las latencias de gran tamaño no son aceptables para el público, por lo que los fragmentos de vídeo se deben entregar de una manera oportuna. 

Los patrones de solicitud de streaming también ofrecen algunos desafíos nuevos. Para una transmisión en vivo popular, o cuando se lanza una nueva serie para vídeo bajo demanda (VoD), puede haber miles de millones de espectadores intentando solicitar la transmisión al mismo tiempo. En este caso, la consolidación de solicitud inteligente es vital para no sobrecargar los servidores de origen cuando los recursos todavía no están almacenados en caché.
 
La red CDN de Azure de Akamai ahora ofrece una característica que se ocupa de entregar recursos multimedia de transmisión de manera eficiente a los usuarios finales en todo el mundo a escala y con latencias reducidas, reduciendo al mismo tiempo la carga en los servidores de origen. Esta característica está disponible a través de la característica “Optimizado para” del punto de conexión de la red CDN de Azure creado en un perfil de la red CDN de Azure con plan de tarifa “Akamai estándar”. Debe utilizar la optimización “Streaming multimedia de vídeo a petición” para los recursos de VoD y el tipo de optimización “Streaming multimedia general” si realiza una combinación de transmisión en vivo y VoD.

La red CDN de Azure de Verizon puede ofrecer streaming multimedia directamente en el tipo de optimización “Entrega web general”.
 
## <a name="configuring-cdn-endpoint-to-optimize-delivery-of-media-streaming-in-azure-cdn-from-akamai"></a>Configuración del punto de conexión de la red CDN para optimizar la entrega de streaming multimedia en la red CDN de Azure de Akamai
 
Puede configurar el punto de conexión de la red CDN para optimizar la entrega de archivos grandes a través de Azure Portal simplemente seleccionando la opción “Streaming multimedia general” o “Streaming multimedia de vídeo a petición” bajo la selección de la propiedad “Optimizado para” durante la creación del punto de conexión. También puede utilizar nuestras API de REST o cualquiera de los SDK de cliente para hacer esto. Las capturas de pantalla siguientes ilustran el proceso a través de Azure Portal. 
  
![Incorporación de un nuevo punto de conexión](./media/cdn-media-streaming-optimization/01_Adding.png)

*Figura 1: Incorporación de un nuevo punto de conexión de la red CDN a partir de la opción Perfil de CDN*

![Streaming seleccionado](./media/cdn-media-streaming-optimization/02_Creating.png) 
  
*Figura 2: Creación de un punto de conexión de la red CDN con la opción Streaming multimedia de vídeo a petición seleccionada* 
 
Una vez creado el punto de conexión de la red CDN, aplicará la optimización para todos los archivos que coincidan con ciertos criterios. La siguiente sección describe esto en detalle. 
 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Optimizaciones de streaming multimedia para la red CDN de Azure de Akamai
 
La optimización de streaming multimedia de Akamai es efectiva para streaming multimedia en vivo o VoD donde se utilizan fragmentos multimedia individuales para la entrega, en contraposición a un único recurso grande transferido mediante descarga progresiva o utilizando solicitudes de intervalo de bytes. Para ese estilo de entrega multimedia, consulte la [optimización de archivos grandes ](cdn-large-file-optimization.md).

El uso del tipo de optimización de entrega multimedia general o de entrega multimedia de VoD utilizará una red CDN con optimizaciones de back-end para entregar recursos más rápido, así como configuraciones para recursos multimedia basadas en procedimientos recomendados aprendidos a lo largo del tiempo.

### <a name="caching"></a>Almacenamiento en caché

Si la red CDN de Azure de Akamai detecta que el recurso es un fragmento o manifiesto de streaming (vea la lista completa a continuación), la red CDN utilizará diferentes tiempos de expiración de almacenamiento en caché de la entrega general. Como siempre, se aceptarán los encabezados de control de caché o de expiración enviados desde el origen y, si el recurso no es un recurso multimedia, se almacenará en caché utilizando los tiempos de expiración para la entrega web general.

El tiempo de almacenamiento en caché negativo corto es útil para la descarga de origen cuando muchos usuarios solicitan un fragmento que aún no existe, como en una transmisión en vivo en la que los paquetes no están disponibles en origen ese segundo. Un intervalo de almacenamiento en caché más largo también ayuda a descargar solicitudes del origen, ya que el contenido de vídeo no suele modificarse.
 

|    | General<br> web<br>entrega | General<br> medios<br> streaming | VoD<br>medios<br> streaming  
--- | --- | --- | ---
Almacenamiento en caché: positivo <br> HTTP 200, 203, 300, <br> 301, 302 y 410 | 7 días |365 días | 365 días   
Almacenamiento en caché: negativo <br> HTTP 204, 305, 404, <br> y 405 | Ninguna | 1 segundo | 1 segundo
 
### <a name="dealing-with-origin-failure"></a>Tratamiento del error de origen  

La entrega multimedia general y la entrega multimedia de VoD también tienen tiempo de espera de origen y registro de reintentos según procedimientos recomendados para patrones de solicitud típicos. Por ejemplo, dado que la entrega multimedia general está pensada tanto para la entrega multimedia en vivo como para la entrega multimedia de VoD, usa un tiempo de espera de conexión mucho más corto debido a la importancia intrínseca del tiempo del streaming en vivo.

Cuando se agota el tiempo de espera de una conexión, la red CDN vuelve a intentarlo un número determinado de veces antes de enviar un error de tiempo de espera de puerta de enlace 504 al cliente. 

Cuando un archivo cumple la lista de condiciones de tamaño y tipo de archivo, la red CDN usa el comportamiento de streaming multimedia; de lo contrario, se usa la entrega web general. 
   
### <a name="conditions-for-media-streaming-optimization"></a>Condiciones para la optimización de streaming multimedia 

En la tabla siguiente se enumera el conjunto de criterios que se debe cumplir para esta optimización: 
 
Tipos de streaming admitidos | Extensiones de archivo  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, clave, ts y aac
Adobe HDS | f4m, f4x, drmmeta, arranque, f4f,<br>Estructura de dirección URL Seg-Frag <br> (expresión regular de coincidencia: ^(/.*)Seq(\d+)-Frag(\d+))
DASH | mpd, guión, divx, ismv, m4s, m4v, mp4,. mp4v, <br> sidx, webm, mp4a, m4a y isma
Smooth Streaming | /manifest/,/QualityLevels/Fragments/
  

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Optimizaciones de streaming multimedia para la red CDN de Azure de Verizon

La red CDN de Azure de Verizon puede entregar recursos multimedia de streaming directamente utilizando el tipo de optimización “Entrega web general”. También hay algunas las características en la red CDN de forma predeterminada que ayudan directamente en recursos multimedia de entrega.

### <a name="partial-cache-sharing"></a>Uso compartido de caché parcial

Esto permite que el contenido parcialmente almacenado en caché se sirva desde la red CDN a las nuevas solicitudes. Esto significa que, por ejemplo, la primera solicitud a la red CDN dé un error de caché y la solicitud se envíe al origen. Mientras este contenido siga estando cargado en la memoria caché de la red CDN (aunque todavía incompleto), las demás solicitudes a la red CDN pueden empezar a obtener estos datos. 

### <a name="cache-fill-wait-time"></a>Tiempo de espera de relleno de caché

Útil junto con el uso compartido de la memoria caché parcial, la característica del tiempo de espera de relleno de caché exige al servidor perimetral mantener todas las solicitudes posteriores para el mismo recurso hasta que los encabezados de respuesta HTTP lleguen desde el servidor de origen. Si los encabezados de respuesta HTTP del servidor de origen llegan antes de que el temporizador expire, todas las solicitudes colocadas en espera se servirán desde la memoria caché creciente (al mismo tiempo que se rellena con datos del origen). De forma predeterminada, el tiempo de espera de relleno de caché se establece en 3000 milisegundos. 


