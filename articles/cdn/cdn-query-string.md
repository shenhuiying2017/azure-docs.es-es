---
title: "Control del comportamiento del almacenamiento en caché de la red CDN de Azure con cadenas de consulta | Microsoft Docs"
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
ms.openlocfilehash: 28e724f34c32edb0d5641b24f9ffedb7dc5f9680
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings"></a>Control del comportamiento del almacenamiento en caché de Azure Content Delivery Network con cadenas de consulta
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Red CDN premium de Azure de Verizon](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Información general
Azure Content Delivery Network (CDN) le permite controlar cómo se almacenan los archivos en caché para una solicitud web que contiene una cadena de consulta. En una solicitud web con una cadena de consulta, la cadena de consulta es la parte de la solicitud que se produce después del carácter `?`. Una cadena de consulta puede contener uno o más parámetros, que están separados por un carácter `&`. Por ejemplo: `http://www.domain.com/content.mov?data1=true&data2=false`. Si hay más de un parámetro de cadena de consulta en una solicitud, el orden de los parámetros no importa. 

> [!IMPORTANT]
> Los productos estándar y premium de CDN ofrecen la misma funcionalidad de almacenamiento en caché de cadenas de consulta, pero la interfaz de usuario es distinta.  En este artículo se describe la interfaz de **Azure CDN Standard de Akamai** y **Azure CDN Standard de Verizon**. Para más información sobre el almacenamiento en caché de cadenas de consulta con la **red CDN premium de Azure de Verizon**, consulte [Control del comportamiento del almacenamiento en caché de las solicitudes de CDN con cadenas de consultas - Premium](cdn-query-string-premium.md).

Existen tres modos de cadena de consulta:

- **Ignorar cadenas de consulta**: este es el modo predeterminado. En este modo, el nodo perimetral de CDN pasa las cadenas de consulta del solicitante al origen en la primera solicitud y almacena en la memoria caché el recurso. Todas las solicitudes posteriores del recurso que se ofrecen desde el nodo perimetral ignoran las cadenas de consulta hasta que expira el recurso en caché.
- **Omitir almacenamiento en caché de cadenas de consulta**: en este modo, las solicitudes con cadenas de consulta no se almacenan en caché en el nodo perimetral de CDN. El nodo perimetral recupera el activo directamente del origen y lo pasa al solicitante con cada solicitud.
- **Almacenar en caché todas las URL únicas**: en este modo, cada solicitud con un URL único, incluida la cadena de consulta, se trata como un recurso único con su propia memoria caché. Por ejemplo, la respuesta desde el origen para una solicitud de `example.ashx?q=test1` se almacena en la memoria caché en el nodo perimetral y se devuelve para cachés posteriores con esa misma cadena de consulta. Se almacena en caché una solicitud de `example.ashx?q=test2` como un recurso independiente con su propia configuración de período de vida.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Modificación de la configuración del almacenamiento en caché de cadenas de consultas para perfiles de red CDN estándar
1. Abra un perfil de CDN y, después, seleccione el punto de conexión de CDN que quiere administrar.
   
   ![Puntos de conexión de perfil de la red CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. En Configuración, haga clic en **Caché**.
   
    ![Botón Caché de perfil de CDN](./media/cdn-query-string/cdn-cache-btn.png)
   
3. En la lista **Comportamiento del almacenamiento en caché de cadenas de consulta**, seleccione un modo de cadena de consulta y, después, haga clic en **Guardar**.
   
  <!--- Replace screen shot after general caching goes live ![CDN query string caching options](./media/cdn-query-string/cdn-query-string.png) --->

> [!IMPORTANT]
> Es posible que los cambios en la configuración de la cadena de caché no sean visibles de forma inmediata, ya que el registro puede tardar en propagarse a través de CDN. Para los perfiles de la **red CDN de Azure de Akamai** , la propagación normalmente se completa en un minuto. Para los perfiles de la **red CDN de Azure de Verizon**, la propagación normalmente se completa en 90 minutos, pero en algunos casos puede tardar más tiempo.


