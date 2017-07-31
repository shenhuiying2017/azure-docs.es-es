---
title: "Optimización de la entrega del contenido para su escenario"
description: "Cómo optimizar la entrega del contenido para escenarios específicos"
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
ms.openlocfilehash: 7dd497109964c3993742a324aace9c98f4a4050f
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017

---
# <a name="optimize-content-delivery-for-your-scenario"></a>Optimización de la entrega del contenido para su escenario

Cuando realice la entrega de contenido a una audiencia global de gran tamaño, es fundamental garantizar una entrega optimizada de su contenido. La red CDN de Azure puede optimizar la experiencia de entrega según el tipo de contenido que tenga, ya sea un sitio web, una transmisión en directo, un video o un archivo grande para su descarga, con el objetivo de proporcionar una mejora en el rendimiento perceptible. Cuando crea un punto de conexión de la red CDN, especifique un escenario en la opción del menú “optimizado para”. Su elección determina qué optimización se aplicará al contenido que se vaya a entregar desde el punto de conexión de la red CDN.

Las opciones de optimización están diseñadas para utilizar los comportamientos de procedimientos recomendados para mejorar el rendimiento de la entrega de contenido y un mejor origen de descarga. Las opciones de escenario afectarán el rendimiento mediante la modificación de las configuraciones de almacenamiento parcial en caché, la fragmentación del objeto, la directiva de reintentos de error de origen, etc. 

Este documento proporciona una introducción aproximada de diversas características de optimización y cuándo se deben utilizar. Consulte los documentos respectivos en cada tipo de optimización individual para obtener más detalles sobre las características y limitaciones.

> [!NOTE]
> Las opciones del menú "optimizado para" pueden variar según el proveedor seleccionado. Los proveedores de la red CDN aplican la mejora de maneras diferentes, según el escenario. 

> * La red CDN de Azure de Akamai admite la entrega web general, el streaming multimedia general, el streaming multimedia de video bajo demanda y la descarga de archivos de gran tamaño. 

> * La red CDN de Azure de Verizon admite solo la entrega web general, que se puede usar con video bajo demanda y descarga de archivos de gran tamaño sin seleccionar un tipo de optimización.

> * Se recomienda encarecidamente que pruebe las variaciones en el rendimiento entre diferentes proveedores para seleccionar el proveedor óptimo para su entrega.

## <a name="selecting-and-configuring-optimization-types"></a>Selección y configuración de los tipos de optimización

Puede empezar a crear un nuevo punto de conexión de la red CDN al seleccionarlo en el menú desplegable el que mejor coincida con el escenario y el tipo de contenido con el que usa el punto de conexión para entregar. "Entrega web general" es la selección predeterminada. Puede actualizar la opción de optimización para cualquier extremo de Akamai existente en cualquier momento. Este cambio no interrumpirá la entrega de la red CDN.  

1. Seleccione un extremo dentro de un perfil estándar de Akamai.

    ![Seleccione un extremo.](./media/cdn-optimization-overview/01_Akamai.png)

2. Seleccione Optimización en la barra lateral y, a continuación, seleccione un tipo en el menú desplegable.

    ![Seleccione Optimización y Tipo.](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optimización de escenarios específicos

Puede optimizar el punto de conexión de la red CDN para uno de los siguientes escenarios:  

1.  Entrega web general
2.  Streaming multimedia general
3.  Streaming multimedia de video bajo demanda
4.  Descarga de archivos de gran tamaño

Los tipos de configuraciones de la optimización para los cuatro escenarios se explican a continuación. 

### <a name="general-web-delivery-optimization"></a>Optimización para entrega web general

La entrega web general es la opción más común. Está diseñada para la optimización de contenido web general, como páginas web y aplicaciones web. Esta optimización puede usarse también para la descarga de archivos y videos.

Normalmente, un sitio web típico incluye contenido estático y dinámico. El contenido estático incluye las imágenes, las bibliotecas de JavaScript y las hojas de estilos, que pueden almacenarse en la memoria caché y se entregan a distintos usuarios. El contenido dinámico es personalizado para un usuario específico, como elementos de noticias adaptados a un perfil de usuario. El contenido dinámico no necesita almacenarse en la memoria caché porque es único para cada usuario, como el contenido de los carritos de compra. La entrega web general puede optimizar todo el sitio web.  

> [!NOTE]
> ¿Cuándo debería utilizarla?

> Si utiliza la red CDN de Azure de Akamai, le recomendamos usar esta optimización cuando el tamaño promedio de los archivos sea menor que 10 MB. Use "optimizado para: descarga de archivos de gran tamaño" cuando el tamaño promedio de los archivos sea superior a 10 MB.

### <a name="general-media-streaming-optimization"></a>Optimización de streaming multimedia general

El streaming multimedia general se recomienda si tiene que utilizar el punto de conexión para transmisiones en vivo y transmisiones de video bajo demanda.

Por ejemplo, el streaming multimedia es urgente porque los paquetes que llegan tarde al cliente pueden provocar una experiencia de visualización deteriorada, como el almacenamiento en búfer frecuente del contenido de video. La optimización del streaming multimedia reduce la latencia de entrega de contenido multimedia y proporciona una experiencia de streaming con velocidad de transmisión adaptable para los usuarios finales. 

Se trata de un escenario común para los clientes de Azure Media Services. Cuando use Azure Media Services, obtendrá un punto de conexión de streaming que puede usarse para streaming en vivo y bajo demanda. Con este escenario, el cliente no tiene que cambiar a otro punto de conexión cuando pasa de streaming en vivo a streaming bajo demanda. La optimización de streaming multimedia general admite este tipo de escenario.

La red CDN de Azure de Verizon puede usar el tipo de optimización para entrega web general para ofrecer contenido de streaming multimedia.

Más información sobre la optimización de streaming multimedia [aquí](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-streaming-optimization"></a>Optimización de streaming de video bajo demanda

La optimización de streaming multimedia de video bajo demanda (VOD) mejorará el contenido que se transmite bajo demanda. Se recomienda usar esta opción cuando usa un punto de conexión para el streaming de video bajo demanda.

La red CDN de Azure de Verizon puede usar el tipo de optimización para entrega web general para ofrecer contenido de streaming multimedia.

Más información sobre la optimización de streaming multimedia [aquí](cdn-media-streaming-optimization.md).

> [!NOTE]
> ¿Cuándo debería utilizarla?

> Cuando el punto de conexión se usa principalmente para contenido de video bajo demanda. La principal diferencia entre esta optimización y la optimización de streaming multimedia general es que el tiempo de expiración del reintento de conexión es mucho más breve para trabajar con escenarios de streaming en vivo.

### <a name="large-file-download-optimization"></a>Optimización de descarga de archivos de gran tamaño

Para la red CDN de Azure de Akamai, debe usar esta optimización para entregar archivos mayores a 1,8 GB.  La red CDN de Azure de Verizon no tiene límites para el tamaño en el archivo de descarga en la optimización para entrega web general.

Cuando se usa la red CDN de Azure de Akamai, se optimizan las descargas de archivos de gran tamaño para el contenido de más de 10 MB. Cuando el tamaño promedio de los archivos es menor que 10 MB, le recomendamos utilizar la entrega web general. Si los tamaños de archivo promedio son constantemente superiores a 10 MB (por ejemplo, actualizaciones de firmware o software), podría ser más eficaz crear un punto de conexión independiente para archivos de gran tamaño. 

La red CDN de Azure de Verizon puede usar el tipo de optimización para entrega web general para ofrecer contenido de streaming multimedia.

Más información sobre la optimización de archivos de gran tamaño [aquí](cdn-large-file-optimization.md).



