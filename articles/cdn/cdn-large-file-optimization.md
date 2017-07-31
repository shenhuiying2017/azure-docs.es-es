---
title: "Optimización de descarga de archivos grandes mediante Azure CDN"
description: "Optimización profunda de las descargas de archivos grandes"
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
ms.openlocfilehash: 27e202b05f86eeee7071f3fae145caeba3d66827
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017

---
# <a name="large-file-download-optimization-via-azure-cdn"></a>Optimización de descarga de archivos grandes mediante Azure CDN

El tamaño de los archivos del contenido enviado por Internet ha ido aumentando constantemente debido a una funcionalidad mejorada, a gráficos mejorados y a un contenido multimedia enriquecido. Esto se controla por diversos factores, incluido el acceso de banda ancha, a dispositivos de almacenamiento de bajo costo, proliferación de vídeo de alta definición, dispositivos conectados a Internet (IoT) etc.  Proporcionar un mecanismo de entrega más rápido y eficiente para esos archivos grandes es fundamental para garantizar una experiencia sin complicaciones y divertida para el consumidor. 

Existen varios desafíos inherentes al enviar archivos grandes. En primer lugar, el tiempo medio para descargar un archivo grande puede ser importante y es posible que muchas aplicaciones no descarguen todos los datos de forma secuencial. En algunos casos, las aplicaciones pueden descargar la última parte de un archivo antes de la primera. Por lo tanto, cuando se solicita solo una pequeña cantidad de un archivo o un usuario pone en pausa una descarga, el resultado puede ser un error en la descarga o que se retrase hasta después de que la red CDN haya recuperado todo el archivo desde el origen. 

En segundo lugar, con la proliferación de archivos grandes en Internet, los usuarios a menudo observan que la latencia entre el usuario y el archivo determina en última instancia el rendimiento o la velocidad a la que los usuarios pueden ver el contenido. Además, los problemas de capacidad y de congestión de red afectan aún más al rendimiento y estos problemas, junto con la mayor distancia entre el servidor y el usuario final, dan lugar a más oportunidades de pérdida de paquetes, con lo que se reduce aún más la calidad. La reducción de la calidad causada por un rendimiento limitado y una mayor pérdida de paquetes puede manifestarse en un tiempo de espera significativamente mayor para que se complete la descarga de un archivo. 

Por último, muchos archivos grandes no se envían en su totalidad. Los usuarios pueden cancelar una descarga a la mitad o ver solo los primeros minutos de un vídeo largo MP4. Por lo tanto, resulta útil para muchas empresas de software y de elementos multimedia proporcionar solo la parte del archivo que solicita el usuario final. De este modo, solo las partes solicitadas se distribuirán de manera eficaz hasta el acceso más alejado de Internet, lo que reducirá el tráfico de salida desde el origen y, por tanto, la presión de memoria y de E/S en el servidor de origen. 

Azure CDN de Akamai ahora ofrece una característica que se ocupa de entregar archivos grandes de manera eficiente a los usuarios finales en todo el mundo a escala y con latencias reducidas, reduciendo al mismo tiempo la carga en los servidores de origen. Esta característica está disponible a través de la característica “Optimizado para” del punto de conexión de Azure CDN creado en un perfil de Azure CDN con el plan de tarifa “Akamai Estándar”.

## <a name="configuring-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Configuración del punto de conexión de la red CDN para optimizar la entrega de archivos grandes

Puede configurar el punto de conexión de la red CDN para optimizar la entrega de archivos grandes a través de Azure Portal simplemente seleccionando la opción “Descarga de archivos grandes” bajo la selección de la propiedad “Optimizado para” durante la creación del punto de conexión. También puede utilizar nuestras API de REST o cualquiera de los SDK de cliente para hacer esto. Las capturas de pantalla siguientes ilustran el proceso a través de Azure Portal.

![Nuevo punto de conexión de CDN](./media/cdn-large-file-optimization/01_Adding.png)  
 
*Figura 1: Adición de un nuevo punto de conexión de CDN a partir de la opción Perfil de CDN*
 
![LFO seleccionado](./media/cdn-large-file-optimization/02_Creating.png)

*Figura 2: Creación de un punto de conexión de la red CDN con la opción de optimización de descarga de archivos grande seleccionada*

Una vez creado el punto de conexión de la red CDN, aplicará la optimización de archivos grandes para todos los archivos que coincidan con ciertos criterios. En la sección siguiente se describe esto en detalle.

## <a name="optimizing-for-delivery-of-large-files-with-azure-cdn-from-akamai"></a>Optimización de entrega de archivos grandes con Azure CDN de Akamai

Para Azure CDN de Akamai, puede utilizar la característica de tipo de optimización de archivos grandes para activar las optimizaciones y configuraciones de red que permiten una entrega de archivos grandes más rápida y eficaz. La entrega web general con Akamai solo puede almacenar en caché archivos inferiores a 1,8 GB y puede tunelizar (no almacenar en caché) archivos de hasta 150 GB, aunque la optimización de archivos grandes permite que se almacenen en caché archivos de hasta 150 GB.

La optimización de archivos grandes es efectiva cuando se cumplen determinadas condiciones con respecto a cómo funciona el servidor de origen, los tipos de archivos que se solicitan y el tamaño de los archivos solicitados. Antes de entrar en detalles sobre cada uno de ellos, es importante comprender una descripción general del funcionamiento de la optimización. 

### <a name="object-chunking"></a>Fragmentación de objetos 

Azure CDN de Akamai emplea una técnica denominada fragmentación de objetos, según la cual la red CDN recupera las partes más pequeñas del archivo del origen cuando se solicita un archivo grande. Cuando el servidor perimetral/POP de CDN recibe una solicitud completa o de archivos de intervalo de bytes de un usuario final, en primer lugar comprueba si el tipo de archivo pertenece a la lista de tipos de archivo admitidos en esta optimización y si cumple los requisitos de tamaño de archivo. Si el tamaño del archivo es mayor que 10 MB, el servidor perimetral de la red CDN solicita el archivo del servidor de origen en fragmentos de 2 MB. Cuando llega el fragmento a la red perimetral CDN, se almacena en caché e inmediatamente se envía al usuario final, mientras que la red CDN "captura anticipadamente" el fragmento siguiente en paralelo. Esta "captura previa" garantiza que el contenido está disponible antes al llevar un fragmento por delante de los usuarios y al reducir la latencia para el usuario final. Este proceso continúa hasta que se descarga el archivo completo (si el usuario final ha solicitado todo el archivo), hasta que estén disponibles todos los intervalos de bytes solicitados (si el usuario final solicita intervalos de bytes), o el cliente finaliza la conexión. 

Los detalles de la solicitud de intervalo de bytes pueden encontrarse en [7233 RFC](https://tools.ietf.org/html/rfc7233).

La red CDN almacenará en caché los fragmentos cuando se reciben y no requiere que todo el archivo se almacene en caché en la red CDN. Las solicitudes posteriores para los intervalos de bytes o archivos se enviarán desde la caché de la red CDN y usará "la captura previa" para solicitar fragmentos desde el origen si no se han almacenado en caché todos los fragmentos en la red CDN. Como puede ver, esta optimización se basa en las solicitudes de intervalo de bytes que respaldan el servidor de origen. _Si el servidor de origen no admite solicitudes de intervalo de bytes, esta optimización no será efectiva._ 

### <a name="caching"></a>Almacenamiento en caché
Los archivos grandes utilizan diferentes tiempos de expiración del almacenamiento en caché para la entrega general. Esto marca la diferencia entre el almacenamiento en caché positivo y el negativo, en función de códigos de respuesta HTTP. Si el origen especifica un tiempo con un tiempo de expiración a través el encabezado Cache-Control o Expires en la respuesta, la red CDN respetará siempre ese valor. Si no especifica el origen y el archivo coincide con el tipo de archivo y con la lista de condiciones de tamaño de archivo para este tipo de optimización, la red CDN usará los valores predeterminados para la optimización de archivos grandes. En caso contrario, la red CDN usará los valores predeterminados para la entrega web general.

 
|    | Web general | Optimización de archivos grandes 
--- | --- | --- 
Almacenamiento en caché: positivo <br> HTTP 200, 203, 300, <br> 301, 302 y 410 | 7 días |1 día  
Almacenamiento en caché: negativo <br> HTTP 204, 305, 404, <br> y 405 | Ninguna | 1 segundo 

### <a name="dealing-with-origin-failure"></a>Tratamiento del error de origen

En el tipo de optimización de archivos grandes, la longitud de tiempo de espera de lectura del origen aumenta de 2 segundos en la entrega web general a 2 minutos para explicar que los tamaños de archivo más grandes no agoten prematuramente el tiempo de espera de una conexión.

Al igual que con la entrega de web general, cuando se agota el tiempo de espera de una conexión, volvemos a intentarlo un número determinado de veces antes de enviar un error de tiempo de espera de puerta de enlace 504 al cliente. 

### <a name="conditions-for-large-file-optimization"></a>Condiciones para la optimización de archivos grandes

En la tabla siguiente se enumera el conjunto de criterios que se debe cumplir para la optimización de archivos grandes:

Condición | Valores 
--- | --- 
Tipos de archivo admitidos | 3g2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> mpeg, mpg, mts, pkg, qt, rm, swf, tar, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Tamaño de archivo mínimo | 10 MB 
Tamaño de archivo máximo | 150 GB 
Características del servidor de origen | Debe admitir solicitudes de intervalo de bytes 

## <a name="optimizing-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optimización de entrega de archivos grandes con Azure CDN de Verizon

Azure CDN de Verizon puede enviar archivos grandes sin un límite de tamaño y tiene varias características que agilizan la entrega de archivos grandes activados de forma predeterminada.

### <a name="complete-cache-fill"></a>Relleno de la memoria caché completa

Azure CDN de Verizon tiene una característica de predeterminada denominada relleno de caché completa en la cual la red CDN extrae un archivo en la memoria caché cuando se abandona o se pierde la solicitud inicial. 

Esta característica resulta especialmente útil para los recursos de gran tamaño que los usuarios no suelen descargar de principio a fin (por ejemplo, vídeos de descarga progresiva). Por lo tanto, esta característica está habilitada de forma predeterminada en Azure CDN de Verizon. Este comportamiento predeterminado consiste en forzar al servidor perimetral para que inicie una captura de fondo del recurso desde el servidor de origen. Después, el recurso estará en la caché local del servidor perimetral. Cuando el objeto completo se encuentra en caché, el servidor perimetral puede aceptar las solicitudes de intervalo de bytes en la red CDN para el objeto almacenado en caché.

El comportamiento predeterminado de relleno de caché completa puede deshabilitarse a través del motor de reglas en el nivel Verizon Premium.

### <a name="peer-cache-fill-hotfiling"></a>Creación de archivos activos de relleno de caché del mismo nivel

Se trata de una característica predeterminada de Azure CDN de Verizon, en el que un algoritmo propietario avanzado puede aprovechar los servidores de almacenamiento en caché perimetrales basados en métricas como el ancho de banda y las solicitudes de agregado para responder a solicitudes de cliente de objetos grandes y muy populares. Esto evita una situación en la cual se podría enviar un gran número de solicitudes adicionales a un servidor de origen del cliente. 

### <a name="conditions-for-large-file-optimization"></a>Condiciones para la optimización de archivos grandes

Las características de optimización de Verizon están activadas de forma predeterminada y no hay ningún límite en el tamaño máximo del archivo. 

## <a name="additional-considerations"></a>Consideraciones adicionales

Hay algunos aspectos adicionales a tener en cuenta durante el uso de este tipo de optimización.
 
### <a name="azure-cdn-from-akamai"></a>Azure CDN de Akamai

- El proceso de fragmentación da como resultado solicitudes adicionales en el servidor de origen, pero el volumen global de los datos enviados desde el origen será mucho menor, ya que la fragmentación resulta en mejores características de almacenamiento en caché en la red CDN.
- También habrá una ventaja adicional en la memoria reducida y presión de E/S en el origen porque se envían piezas más pequeñas del archivo. 
- En el caso de los fragmentos que se almacenan en caché en la red CDN, no habrá solicitudes adicionales para el origen hasta que expire el contenido de la memoria caché o se expulse de la caché por otras razones. 
- El usuario puede realizar solicitudes de intervalo en la red CDN y solo se tratarán como cualquier archivo normal. La optimización solo se aplicará si es un tipo de archivo válido y el intervalo de bytes se encuentra entre 10 MB y 150 GB. Si el tamaño medio de los archivos solicitado es menor que 10 MB, puede utilizar la entrega web general en su lugar.

### <a name="azure-cdn-from-verizon"></a>Azure CDN de Verizon

La optimización de entrega web general puede enviar archivos grandes.

