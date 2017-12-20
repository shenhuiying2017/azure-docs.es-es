---
title: "Control del comportamiento del almacenamiento en caché de Azure Content Delivery Network con cadenas de consulta | Microsoft Docs"
description: "El almacenamiento en caché de las cadenas de consulta de Azure CDN controla el modo en que los archivos se almacenan en caché cuando contienen cadenas de consulta."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: 9ffd05a0eb4d976dc40a1c5d45fd22ebf9bd4db1
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings"></a>Control del comportamiento del almacenamiento en caché de Azure Content Delivery Network con cadenas de consulta
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Red CDN premium de Azure de Verizon](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Información general
Azure Content Delivery Network (CDN) le permite controlar cómo se almacenan los archivos en caché para una solicitud web que contiene una cadena de consulta. En una solicitud web con una cadena de consulta, esta última es la parte de la solicitud que hay después del signo de interrogación (?). Una cadena de consulta puede contener uno o más pares clave-valor, en los cuales el nombre de campo y su valor están separados por un signo igual (=). Los pares clave-valor están separados entre ellos por una Y comercial (&). Por ejemplo: `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Si hay más de un par clave-valor en una cadena de consulta de una solicitud, no importa el orden en el que se especifiquen. 

> [!IMPORTANT]
> Los productos estándar y premium de CDN ofrecen la misma funcionalidad de almacenamiento en caché de cadenas de consulta, pero la interfaz de usuario es distinta.  En este artículo se describe la interfaz de **Azure CDN Standard de Akamai** y **Azure CDN Standard de Verizon**. Para más información sobre el almacenamiento en caché de cadenas de consulta con la **red CDN premium de Azure de Verizon**, consulte [Control del comportamiento del almacenamiento en caché de las solicitudes de CDN con cadenas de consultas - Premium](cdn-query-string-premium.md).

Existen tres modos de cadena de consulta:

- **Ignorar cadenas de consulta**: este es el modo predeterminado. En este modo, el nodo perimetral de CDN pasa las cadenas de consulta del solicitante al origen en la primera solicitud y almacena en la memoria caché el recurso. Todas las solicitudes posteriores del recurso que se ofrecen desde el nodo perimetral ignoran las cadenas de consulta hasta que expira el recurso en caché.
- **Omitir almacenamiento en caché de cadenas de consulta**: en este modo, las solicitudes con cadenas de consulta no se almacenan en caché en el nodo perimetral de CDN. El nodo perimetral recupera el activo directamente del origen y lo pasa al solicitante con cada solicitud.
- **Almacenar en caché todas las URL únicas**: en este modo, cada solicitud con un URL único, incluida la cadena de consulta, se trata como un recurso único con su propia memoria caché. Por ejemplo, la respuesta desde el origen para una solicitud de `example.ashx?q=test1` se almacena en la memoria caché en el nodo perimetral y se devuelve para cachés posteriores con esa misma cadena de consulta. Se almacena en caché una solicitud de `example.ashx?q=test2` como un recurso independiente con su propia configuración de período de vida.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Modificación de la configuración del almacenamiento en caché de cadenas de consultas para perfiles de red CDN estándar
1. Abra un perfil de CDN y, después, seleccione el punto de conexión de CDN que quiere administrar.
   
   ![Puntos de conexión de perfil de la red CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. En el panel izquierdo, en Configuración, haga clic en **Reglas de caché**.
   
    ![Botón Reglas de caché de CDN](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. En la lista **Comportamiento del almacenamiento en caché de cadenas de consulta**, seleccione un modo de cadena de consulta y, después, haga clic en **Guardar**.
   
   ![Opciones del almacenamiento en caché de cadenas de consultas de CDN](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Es posible que los cambios en la configuración de la cadena de caché no sean visibles de forma inmediata, ya que el registro puede tardar en propagarse a través de CDN. Para los perfiles de la **red CDN de Azure de Akamai** , la propagación normalmente se completa en un minuto. Para los perfiles de la **red CDN de Azure de Verizon**, la propagación normalmente se completa en 90 minutos, pero en algunos casos puede tardar más tiempo.


