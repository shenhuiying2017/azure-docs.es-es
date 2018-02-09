---
title: Optimizar la entrega de contenido de Azure para su escenario
description: "Cómo optimizar la entrega del contenido para escenarios específicos"
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: rli
ms.openlocfilehash: fa4cf306eeb1a8372da5b2a86ac73fbba2832666
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2018
---
# <a name="optimize-azure-content-delivery-for-your-scenario"></a>Optimizar la entrega de contenido de Azure para su escenario

Cuando realice la entrega de contenido a una audiencia global de gran tamaño, es fundamental garantizar la entrega optimizada del contenido. Azure Content Delivery Network puede optimizar la experiencia de entrega según el tipo de contenido que tenga. El contenido puede ser un sitio web, una transmisión en directo, un vídeo o un archivo grande para su descarga. Cuando cree un punto de conexión de la red de entrega de contenido (CDN), especifique un escenario en la opción **Optimizado para**. Su elección determina qué optimización se aplica al contenido que se va a entregar desde el punto de conexión de la red CDN.

Las opciones de optimización están diseñadas para usar comportamientos de procedimientos recomendados para mejorar el rendimiento de la entrega de contenido y un mejor origen de descarga. Las opciones del escenario afectan el rendimiento mediante la modificación de las configuraciones de almacenamiento parcial en caché, la fragmentación de objetos y la directiva de reintentos de error de origen. 

En este artículo se proporciona una introducción a las diversas características de optimización y cuándo se deben usar. Para más información sobre las características y limitaciones, vea los artículos respectivos en cada tipo de optimización individual.

> [!NOTE]
> Las opciones de **Optimizado para** pueden variar según el proveedor seleccionado. Los proveedores de la red CDN aplican la mejora de maneras diferentes, según el escenario. 

## <a name="provider-options"></a>Opciones de proveedor

**Azure Content Delivery Network de Akamai** admite las siguientes optimizaciones:

* [Entrega web general](#general-web-delivery) 

* [Streaming multimedia general](#general-media-streaming)

* [Streaming multimedia de vídeo a petición](#video-on-demand-media-streaming)

* [Descarga de archivos de gran tamaño](#large-file-download)

* [Aceleración de sitios dinámicos](#dynamic-site-acceleration) 

**Azure Content Delivery Network de Verizon** admite las siguientes optimizaciones:

* [Entrega web general](#general-web-delivery) (también se puede utilizar para streaming multimedia y contenido de descarga de archivos grandes)

* [Aceleración de sitios dinámicos](#dynamic-site-acceleration) 

Se recomienda encarecidamente que pruebe las variaciones en el rendimiento entre diferentes proveedores para seleccionar el proveedor óptimo para su entrega.

## <a name="select-and-configure-optimization-types"></a>Seleccionar y configurar los tipos de optimización

Para crear un nuevo punto de conexión, seleccione el tipo de optimización que mejor coincida con el escenario y el tipo de contenido que quiere que el punto de conexión entregue. **Entrega web general** es la selección predeterminada. Para los puntos de conexión existentes de **Azure Content Delivery Network de Akamai**, puede actualizar la opción de optimización en cualquier momento. Este cambio no interrumpe la entrega desde la red CDN. 

1. Dentro de un perfil de **Azure Content Delivery Network de Akamai**, seleccione un punto de conexión.

    ![Selección de punto de conexión ](./media/cdn-optimization-overview/01_Akamai.png)

2. En **CONFIGURACIÓN**, seleccione **Optimización**. Después, seleccione un tipo en la lista desplegable **Optimizado para**.

    ![Selección de la optimización y el tipo.](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>Optimización de escenarios específicos

Puede optimizar el punto de conexión de la red CDN para uno de estos escenarios. 

### <a name="general-web-delivery"></a>Entrega web general

Entrega web general es la opción de optimización más común. Está diseñada para la optimización de contenido web general, como páginas web y aplicaciones web. Esta optimización puede usarse también para la descarga de archivos y vídeos.

Un sitio web típico incluye contenido estático y dinámico. El contenido estático incluye las imágenes, las bibliotecas de JavaScript y las hojas de estilos, que pueden almacenarse en la memoria caché y se entregan a distintos usuarios. El contenido dinámico es personalizado para un usuario específico, como elementos de noticias adaptados a un perfil de usuario. El contenido dinámico no se almacena en la memoria caché porque es único para cada usuario, como el contenido de los carros de la compra. La entrega web general puede optimizar todo el sitio web. 

> [!NOTE]
> Si utiliza **Azure Content Delivery Network de Akamai**, es posible que quiera usar esta optimización cuando el tamaño promedio de los archivos sea inferior a 10 MB. Si el tamaño promedio de los archivos es superior a 10 MB, seleccione **Descarga de archivos grandes** en la lista desplegable **Optimizado para**.

### <a name="general-media-streaming"></a>Streaming multimedia general

Si tiene que usar el punto de conexión para streaming en vivo y de vídeo bajo demanda, se recomienda la optimización de streaming multimedia general.

El streaming multimedia depende del tiempo, porque los paquetes que llegan tarde al cliente pueden provocar una experiencia de visualización deteriorada, como el almacenamiento en búfer frecuente del contenido de vídeo. La optimización del streaming multimedia reduce la latencia de entrega de contenido multimedia y proporciona una experiencia de streaming con velocidad de transmisión adaptable para los usuarios. 

Es un escenario común para los clientes de Azure Media Services. Cuando usa Azure Media Services, obtiene un punto de conexión de streaming que puede usarse para streaming en vivo y bajo demanda. Con este escenario, los clientes no tienen que cambiar a otro punto de conexión cuando pasan de streaming en vivo a streaming bajo demanda. La optimización de streaming multimedia general admite este tipo de escenario.

**Azure Content Delivery Network de Verizon** usa el tipo de optimización de entrega web general para ofrecer streaming multimedia.

Para más información acerca de la optimización del streaming multimedia, consulte [Optimización del streaming multimedia](cdn-media-streaming-optimization.md).

### <a name="video-on-demand-media-streaming"></a>Streaming multimedia de vídeo a petición

La optimización del streaming multimedia de vídeo a petición mejora el contenido que se transmite a petición. Se recomienda usar esta opción si usa un punto de conexión para el streaming de vídeo bajo demanda.

**Azure Content Delivery Network de Verizon** usa el tipo de optimización de entrega web general para ofrecer streaming multimedia.

Para más información acerca de la optimización del streaming multimedia, consulte [Optimización del streaming multimedia](cdn-media-streaming-optimization.md).

> [!NOTE]
> Si el punto de conexión sirve principalmente contenido de vídeo bajo demanda, use este tipo de optimización. La principal diferencia entre esta optimización y la optimización de streaming multimedia general es el tiempo de expiración del reintento de conexión. El tiempo de expiración es mucho más breve para trabajar con escenarios de streaming en vivo.

### <a name="large-file-download"></a>Descarga de archivos de gran tamaño

Si utiliza **Azure Content Delivery Network de Akamai**, debe usar la descarga de archivos grandes para entregar archivos de más de 1,8 GB. **Azure Content Delivery Network de Verizon** no tiene un límite para el tamaño de descarga de archivos en su optimización para entrega web general.

Si usa **Azure Content Delivery Network de Akamai**, las descargas de archivos grandes se optimizan para el contenido de más de 10 MB. Si el tamaño promedio de los archivos es menor que 10 MB, se recomienda usar la entrega web general. Si los tamaños promedios de los archivos son constantemente superiores a 10 MB, es posible que sea más eficaz crear un punto de conexión independiente para los archivos grandes. Por ejemplo, las actualizaciones de firmware o software normalmente son archivos grandes.

**Azure Content Delivery Network de Verizon** usa el tipo de optimización de entrega web general para ofrecer contenido de descarga de archivos grandes.

Para más información acerca de la optimización de archivos grandes, consulte [Optimización de archivos grandes](cdn-large-file-optimization.md).

### <a name="dynamic-site-acceleration"></a>Aceleración de sitios dinámicos

 La aceleración de sitios dinámicos está disponible tanto en los perfiles de **Azure Content Delivery Network de Akamai** como de **Azure Content Delivery Network de Verizon**. El uso de esta optimización implica el pago de una cuota adicional; para más información, consulte [Precios de Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

La aceleración de sitios dinámicos incluye diversas técnicas que se benefician de la latencia y el rendimiento del contenido dinámico. Las técnicas incluyen la optimización de rutas y redes, la optimización de TCP y mucho más. 

Esta optimización se puede usar para acelerar una aplicación web que incluye muchas respuestas que no se pueden almacenar en caché. Algunos ejemplos son los resultados de búsquedas, transacciones de finalización de la compra o datos en tiempo real. Puede seguir usando las funciones de almacenamiento en caché principales de CDN para los datos estáticos. 

Para más información acerca de la aceleración de sitios dinámicos, consulte [Aceleración de sitios dinámicos](cdn-dynamic-site-acceleration.md).



