---
title: "Optimización del streaming multimedia mediante Azure Content Delivery Network"
description: Optimizar el streaming de archivos multimedia para una entrega sin problemas
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
ms.openlocfilehash: 02cd0fe30a2a14f42a16ed12f714d496bbb23b36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="media-streaming-optimization-via-the-azure-content-delivery-network"></a>Optimización del streaming multimedia mediante Azure Content Delivery Network 
 
El uso de vídeo de alta definición está aumentando en Internet, lo que crea problemas de entrega eficaz de archivos grandes. Los clientes esperan una reproducción fluida de vídeo bajo demanda o de recursos de vídeo en vivo en una variedad de redes y clientes en todo el mundo. Un mecanismo de entrega rápido y eficiente de los archivos de streaming multimedia es fundamental para garantizar una experiencia sin complicaciones y divertida para el consumidor.  

El streaming en vivo de contenido multimedia es especialmente difícil de entregar, debido a los grandes tamaños y al número de usuarios simultáneos. Los retardos prolongados hacen que los usuarios se marchen. Como las transmisiones en vivo no se pueden almacenar en caché por adelantado y las latencias de gran tamaño no son aceptables para el público, los fragmentos de vídeo se deben entregar de una manera oportuna. 

Los patrones de solicitud de streaming también ofrecen algunos desafíos nuevos. Cuando se publica para vídeo bajo demanda una transmisión en vivo popular o una nueva serie, es posible que miles o millones de espectadores soliciten la transmisión al mismo tiempo. En este caso, la consolidación de solicitud inteligente es vital para no sobrecargar los servidores de origen cuando los recursos todavía no están almacenados en caché.
 
Azure Content Delivery Network de Akamai ahora proporciona una característica que entrega recursos de elementos multimedia en streaming de manera eficaz a usuarios en todo el mundo a escala. La característica reduce las latencias porque reduce la carga en los servidores de origen. Esta característica está disponible con el plan de tarifa Standard de Akamai. 

Azure Content Delivery Network de Verizon puede ofrecer streaming multimedia directamente en el tipo de optimización de entrega web general.
 
## <a name="configure-an-endpoint-to-optimize-media-streaming-in-the-azure-content-delivery-network-from-akamai"></a>Configurar un punto de conexión para optimizar el streaming multimedia en Azure Content Delivery Network de Akamai
 
Puede configurar el punto de conexión de la red de entrega de contenido (CDN) para optimizar la entrega de archivos grandes a través de Azure Portal. También puede utilizar nuestras API de REST o cualquiera de los SDK de cliente para hacer esto. En los pasos siguientes se muestra el proceso a través de Azure Portal:

1. Para agregar un nuevo punto de conexión, en la página **Perfil de CDN**, seleccione **Punto de conexión**.
  
    ![Nuevo punto de conexión](./media/cdn-media-streaming-optimization/01_Adding.png)

2. En la lista desplegable **Optimizado para**, seleccione **Streaming multimedia de vídeo a petición** para los recursos de vídeo bajo demanda. Si realiza una combinación de streaming en vivo y bajo demanda, seleccione **Streaming multimedia general**.

    ![Streaming seleccionado](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Después de crear el punto de conexión, aplica la optimización para todos los archivos que coincidan con determinados criterios. En la sección siguiente se describe este proceso. 
 
## <a name="media-streaming-optimizations-for-the-azure-content-delivery-network-from-akamai"></a>Optimizaciones del streaming multimedia para Azure Content Delivery Network de Akamai
 
La optimización del streaming multimedia de Akamai es eficaz para el streaming multimedia en vivo o de vídeo bajo demanda que usa fragmentos multimedia individuales para la entrega. Este proceso es diferente a la transferencia de un solo recurso de gran tamaño a través de descarga progresiva o mediante solicitudes de intervalo de bytes. Para obtener información sobre ese estilo de entrega multimedia, vea [Optimización de archivos grandes](cdn-large-file-optimization.md).


Los tipos de optimización de entrega multimedia general o de vídeo bajo demanda usan una red CDN con optimizaciones de back-end para entregar los recursos multimedia más rápidamente. También usan configuraciones para recursos multimedia en función de procedimientos recomendados aprendidos con el tiempo.

### <a name="caching"></a>Almacenamiento en caché

Si Azure Content Delivery Network de Akamai detecta que el recurso es un fragmento o manifiesto de streaming, usa diferentes tiempos de expiración de almacenamiento en caché que la entrega web general. (Vea la lista completa en la tabla siguiente). Como siempre, se respetan los encabezados Cache-Control o Expires enviados desde el origen. Si el recurso no es un recurso multimedia, almacena en caché mediante los tiempos de expiración para la entrega web general.

El tiempo de almacenamiento en caché negativo corto es útil para la descarga de origen cuando muchos usuarios solicitan un fragmento que no existe todavía. Un ejemplo es una transmisión en directo en la que los paquetes no están disponibles desde el origen en ese segundo. Un intervalo de almacenamiento en caché más largo también ayuda a descargar solicitudes del origen, ya que el contenido de vídeo no suele modificarse.
 

|   | Entrega web general | Streaming multimedia general | Streaming multimedia de vídeo a petición  
--- | --- | --- | ---
Almacenamiento en caché: positivo <br> HTTP 200, 203, 300, <br> 301, 302 y 410 | 7 días |365 días | 365 días   
Almacenamiento en caché: negativo <br> HTTP 204, 305, 404, <br> y 405 | None | 1 segundo | 1 segundo
 
### <a name="deal-with-origin-failure"></a>Tratamiento del error de origen  

La entrega multimedia general y la entrega multimedia de vídeo bajo demanda también tienen tiempo de espera de origen y un registro de reintentos basado en los procedimientos recomendados para patrones de solicitud típicos. Por ejemplo, dado que la entrega multimedia general es para la entrega multimedia en vivo y de vídeo bajo demanda, usa un tiempo de espera de conexión más corto debido a la importancia intrínseca del tiempo del streaming en vivo.

Cuando se agota el tiempo de espera de una conexión, la red CDN lo intenta un número de veces antes de enviar al cliente un error de "504 - Tiempo de espera agotado para la puerta de enlace". 

Cuando un archivo cumple la lista de condiciones de tamaño y tipo de archivo, la red CDN usa el comportamiento de streaming multimedia. De lo contrario, se usa la entrega web general.
   
### <a name="conditions-for-media-streaming-optimization"></a>Condiciones para la optimización de streaming multimedia 

En la tabla siguiente se enumera el conjunto de criterios que se deben cumplir para la optimización de streaming multimedia: 
 
Tipos de streaming admitidos | Extensiones de archivo  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, clave, ts y aac
Adobe HDS | f4m, f4x, drmmeta, arranque, f4f,<br>Estructura de dirección URL Seg-Frag <br> (expresión regular de coincidencia: ^(/.*)Seq(\d+)-Frag(\d+))
DASH | mpd, guión, divx, ismv, m4s, m4v, mp4,. mp4v, <br> sidx, webm, mp4a, m4a y isma
Streaming con velocidad de transmisión adaptable | /manifest/,/QualityLevels/Fragments/
  

 
## <a name="media-streaming-optimizations-for-the-azure-content-delivery-network-from-verizon"></a>Optimizaciones del streaming multimedia para Azure Content Delivery Network de Verizon

Azure Content Delivery Network de Verizon entrega recursos de streaming multimedia directamente mediante el tipo de optimización de entrega web general. Algunas características en la red CDN ayudan directamente en la entrega de recursos multimedia de forma predeterminada.

### <a name="partial-cache-sharing"></a>Uso compartido de caché parcial

El uso compartido de caché parcial permite que la red CDN entregue contenido parcialmente almacenado en caché a las nuevas solicitudes. Por ejemplo, si la primera solicitud a la red CDN produce un error de caché, la solicitud se envía al origen. Aunque este contenido incompleto se cargue en la memoria caché de la red CDN, las demás solicitudes a la red CDN pueden empezar a obtener estos datos. 

### <a name="cache-fill-wait-time"></a>Tiempo de espera de relleno de caché

 La característica del tiempo de espera de relleno de caché exige al servidor perimetral mantener todas las solicitudes posteriores para el mismo recurso hasta que los encabezados de respuesta HTTP lleguen desde el servidor de origen. Si los encabezados de respuesta HTTP del servidor de origen llegan antes de que el temporizador expire, todas las solicitudes colocadas en espera se sirven desde la memoria caché creciente. Al mismo tiempo, la memoria caché se rellena con datos del origen. De forma predeterminada, el tiempo de espera de relleno de caché se establece en 3.000 milisegundos. 

