---
title: "Carga previa de recursos en un punto de conexión de Azure CDN | Microsoft Docs"
description: "Aprenda a precargar el contenido almacenado en caché en un punto de conexión de la red CDN de Azure."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 1f2dcd9a91bb6e883cbef06373c1acd98bf8d45f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Carga previa de activos en un punto de conexión de CDN de Azure
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

De forma predeterminada, los recursos primero se almacenan en caché conforme se solicitan. Esto significa que la primera solicitud de cada región puede tardar más tiempo, ya que los servidores perimetrales no tendrán el contenido almacenado en caché y deberán reenviar la solicitud al servidor de origen. La precarga del contenido evita esta latencia de primera visita.

Además de proporcionar una mejor experiencia de cliente, la precarga de los recursos almacenados en caché puede reducir el tráfico de red en el servidor de origen.

> [!NOTE]
> La precarga de recursos es útil para grandes eventos o contenido que se pone a disposición de un gran número de usuarios simultáneamente, como el lanzamiento de una nueva película o una actualización de software.
> 
> 

Este tutorial le guiará a través de la precarga de contenido almacenado en la caché en todos los nodos perimetrales de CDN de Azure.

## <a name="walkthrough"></a>Tutorial
1. En el [Portal de Azure](https://portal.azure.com), examine el perfil de CDN que contiene el punto de conexión que quiere precargar.  Se abre la hoja del perfil.
2. Haga clic en el punto de conexión de la lista.  Se abre la hoja del punto de conexión.
3. En la hoja del punto de conexión de CDN, haga clic en el botón Cargar.
   
    ![Hoja Punto de conexión de CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    Se abre la hoja Cargar.
   
    ![Hoja Carga de CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Escriba la ruta de acceso completa de cada recurso que quiera cargar (por ejemplo, `/pictures/kitten.png`) en el cuadro de texto **Ruta de acceso** .
   
   > [!TIP]
   > Aparecerán más cuadros de texto de **Ruta de acceso** después de escribir texto para permitirle crear una lista de varios activos.  Puede eliminar activos en la lista haciendo clic en el botón de puntos suspensivos (...).
   > 
   > Las rutas de acceso deben ser una dirección URL relativa que se ajuste a la siguiente [expresión regular](https://msdn.microsoft.com/library/az24scfc.aspx):  
   > >Carga de un solo archivo `@"^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$"`;  
   > >Carga de un único archivo con cadena de consulta `@"^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$";`  
   > 
   > Cada recurso debe tener su propia ruta de acceso.  No hay ninguna funcionalidad comodín para la carga previa de recursos.
   > 
   > 
   
    ![Botón Cargar](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Haga clic en el botón **Cargar** .
   
    ![Botón Cargar](./media/cdn-preload-endpoint/cdn-load-button.png)

> [!NOTE]
> Hay una limitación de 10 solicitudes de carga por minuto por perfil de red CDN. Se admiten 50 rutas por solicitud. Cada ruta de acceso tiene un límite de longitud de 1024 caracteres.
> 
> 

## <a name="see-also"></a>Otras referencias
* [Purgar un punto de conexión de red CDN de Azure](cdn-purge-endpoint.md)
* [Referencia de la API de REST de red de CDN de Azure - purgar o cargar previamente un punto de conexión](https://msdn.microsoft.com/library/mt634451.aspx)

