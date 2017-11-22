---
title: "Control del comportamiento del almacenamiento en caché de la red CDN de Azure con cadenas de consulta: Premium | Microsoft Docs"
description: "El almacenamiento en caché de las cadenas de consulta de la red CDN de Azure controla el modo en que se almacenan en caché los archivos cuando contienen cadenas de consulta."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mazha
ms.openlocfilehash: ba9c28f0e6df25b101b45edf836d0b95056cbc6f
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-query-strings---premium"></a>Control del comportamiento del almacenamiento en caché de Azure Content Delivery Network con cadenas de consulta - Premium
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Red CDN premium de Azure de Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Información general
Azure Content Delivery Network (CDN) le permite controlar cómo se almacenan los archivos en caché para una solicitud web que contiene una cadena de consulta. En una solicitud web con una cadena de consulta, la cadena de consulta es la parte de la solicitud que hay después del carácter `?`. Una cadena de consulta puede contener uno o más parámetros, que están separados por un carácter `&`. Por ejemplo: `http://www.domain.com/content.mov?data1=true&data2=false`. Si hay más de un parámetro de cadena de consulta en una solicitud, el orden de los parámetros no importa. 

> [!IMPORTANT]
> Los productos estándar y premium de CDN ofrecen la misma funcionalidad de almacenamiento en caché de cadenas de consulta, pero la interfaz de usuario es distinta.  En este artículo se describe la interfaz de **Azure CDN Premium de Verizon**. Para obtener más información sobre el almacenamiento en caché de cadenas de consulta con la **red CDN estándar de Azure de Akamai** y la **red CDN estándar de Azure de Verizon**, consulte [Control del comportamiento del almacenamiento en caché de las solicitudes de red CDN con cadenas de consultas](cdn-query-string.md).
>

Existen tres modos de cadena de consulta:

- **standard-cache**: este es el modo predeterminado. En este modo, el nodo perimetral de CDN pasa las cadenas de consulta del solicitante al origen en la primera solicitud y almacena en la memoria caché el recurso. Todas las solicitudes posteriores del recurso que se ofrecen desde el nodo perimetral ignoran las cadenas de consulta hasta que expira el recurso en caché.
- **no-cache**: en este modo, las solicitudes con cadenas de consultas no están almacenadas en caché en el nodo perimetral de CDN. El nodo perimetral recupera el activo directamente del origen y lo pasa al solicitante con cada solicitud.
- **unique-cache**: en este modo, cada solicitud con un URL único, incluida la cadena de consulta, se trata como un recurso único con su propia memoria caché. Por ejemplo, la respuesta desde el origen para una solicitud de `example.ashx?q=test1` se almacena en la memoria caché en el nodo perimetral y se devuelve para cachés posteriores con esa misma cadena de consulta. Se almacena en caché una solicitud de `example.ashx?q=test2` como un recurso independiente con su propia configuración de período de vida.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Modificación de la configuración del almacenamiento en caché de cadenas de consultas para perfiles de red CDN premium
1. Abra un perfil de CDN y, después, haga clic en **Administrar**.
   
    ![Botón de administración del perfil de la red CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Se abre el portal de administración de CDN.
2. Desplace el mouse sobre la pestaña **HTTP Large** (HTTP grande) y, luego, mantenga el mouse sobre el menú flotante **Configuración de caché**. Haga clic en **Query-String Caching** (Almacenamiento en caché de cadenas de consulta).
   
    Aparecen las opciones del almacenamiento en caché de cadenas de consultas.
   
    ![Opciones del almacenamiento en caché de cadenas de consultas de CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Seleccione un modo de cadena de consulta y, después, haga clic en **Actualizar**.

> [!IMPORTANT]
> Es posible que los cambios en la configuración de la cadena de caché no sean visibles de forma inmediata, ya que el registro puede tardar en propagarse a través de CDN. Para los perfiles de la **Azure CDN Premium de Verizon**, la propagación normalmente se completa en 90 minutos, pero en algunos casos puede tardar más tiempo.
 

