---
title: "Control del comportamiento del almacenamiento en caché de la red CDN de Azure con cadenas de consulta | Microsoft Docs"
description: "El almacenamiento en caché de las cadenas de consulta de la red CDN de Azure controla el modo en que se almacenan en caché los archivos cuando contienen cadenas de consulta."
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
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 8d79626fa8516f226a82d3dac693c2033904c91d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings"></a>Control del comportamiento del almacenamiento en caché de la red CDN de Azure con cadenas de consulta
> [!div class="op_single_selector"]
> * [Standard](cdn-query-string.md)
> * [Red CDN premium de Azure de Verizon](cdn-query-string-premium.md)
> 
> 

## <a name="overview"></a>Información general
El almacenamiento en caché de las cadenas de consultas controla la manera en que se almacenarán en caché los archivos cuando contienen cadenas de consulta.

> [!IMPORTANT]
> Los productos estándar y premium de CDN ofrecen la misma funcionalidad de almacenamiento en caché de cadenas de consultas, pero la interfaz de usuario es distinta.  En este documento se describe la interfaz de la **red CDN estándar de Azure de Akamai** y la **red CDN estándar de Azure de Verizon**.  Para más información sobre el almacenamiento en caché de cadenas de consulta con la **red CDN premium de Azure de Verizon**, consulte [Control del comportamiento del almacenamiento en caché de las solicitudes de CDN con cadenas de consultas - Premium](cdn-query-string-premium.md).
> 
> 

Hay tres modos disponibles:

* **Ignorar cadenas de consultas**: este es el modo predeterminado.  El nodo perimetral de CDN pasará la cadena de consulta del solicitante al origen en la primera solicitud y almacenará en la memoria caché el activo.  Todas las solicitudes posteriores de ese activo que se ofrecen desde el nodo perimetral ignorarán la cadena de consulta hasta que expira el activo en caché.
* **Omitir el almacenamiento en caché para dirección URL con cadenas de consultas**: en este modo, las solicitudes con cadenas de consultas no se almacenan en caché en el nodo perimetral de red CDN.  El nodo perimetral recupera el activo directamente del origen y lo pasa al solicitante con cada solicitud.
* **Almacenar en caché cada URL única**: este modo trata cada solicitud con una cadena de consulta como un activo único con su propia memoria caché.  Por ejemplo, la respuesta desde el origen para una solicitud de *foo.ashx?q=bar* se almacenaría en la memoria caché en el nodo perimetral y se devolvería para cachés posteriores con esa misma cadena de consulta.  Se almacenaría en caché una solicitud de *foo.ashx?q=somethingelse* como un activo independiente con su propio período de vida.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Modificación de la configuración del almacenamiento en caché de cadenas de consultas para perfiles de red CDN estándar
1. En la hoja de perfil de red CDN, haga clic en el punto de conexión de CDN que desea administrar.
   
    ![Puntos de conexión de hoja del perfil de red CDN](./media/cdn-query-string/cdn-endpoints.png)
   
    Se abre la hoja del punto de conexión de CDN.
2. Elija el botón **Configurar** .
   
    ![Botón de administración de hoja de perfil de red CDN](./media/cdn-query-string/cdn-config-btn.png)
   
    Se abre la hoja de configuración de CDN.
3. Seleccione una opción en la lista desplegable **Comportamiento del almacenamiento en caché de cadenas de consultas** .
   
    ![Opciones del almacenamiento en caché de cadenas de consultas de CDN](./media/cdn-query-string/cdn-query-string.png)
4. Tras efectuar su selección, haga clic en el botón **Guardar** .

> [!IMPORTANT]
> Es posible que los cambios en la configuración no sean visibles de forma inmediata, ya que el registro puede tardar en propagarse a través de la red CDN.  Para <b>red CDN de Azure de Akamai</b> , la propagación normalmente se completará en un minuto.  Para los perfiles de la <b>red CDN de Azure de Verizon</b>, la propagación normalmente se completará en 90 minutos, pero en algunos casos puede tardar más tiempo.
> 
> 

