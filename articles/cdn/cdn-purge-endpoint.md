---
title: "Purga de un punto de conexión de red CDN de Azure| Microsoft Docs"
description: "Aprenda a purgar todo el contenido almacenado en caché en un punto de conexión de la red CDN de Azure."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: b035c232bb58d653960190d4974cc3789d55a51d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="purge-an-azure-cdn-endpoint"></a>Purgar un punto de conexión de red CDN de Azure
## <a name="overview"></a>Información general
Los nodos perimetrales de la red CDN almacenarán recursos en caché hasta que el período de vida de dichos recursos (TTL) expire.  Tras la expiración del TTL del activo, cuando un cliente solicite el activo desde el nodo perimetral, este nodo recuperará una nueva copia actualizada del activo para atender la solicitud de cliente y almacenar actualizada la memoria caché.

El procedimiento recomendado para asegurarse de que los usuarios siempre obtengan la copia más reciente de los recursos es crear versiones correspondientes a cada actualización y publicarlos como nuevas URL.  La red CDN recuperará inmediatamente los nuevos recursos en las siguientes solicitudes de los clientes.  A veces puede que quiera purgar contenido almacenado en caché de todos los nodos perimetrales y forzarlos todos para recuperar nuevos activos actualizados.  Esto puede deberse a actualizaciones de la aplicación web o a actualizaciones rápidas de los activos de actualización que contienen información incorrecta.

> [!TIP]
> Tenga en cuenta que el purgado solo borra el contenido almacenado en caché de los servidores perimetrales de la red CDN.  Es posible que las cachés de nivel inferior, como las cachés de servidores proxy y de explorador local, aún contengan una copia en caché del archivo.  Es importante que recuerde esto cuando defina el período de vida de un archivo.  Para hacer que un cliente de nivel inferior solicite la versión más reciente del archivo, asígnele un nombre único cada vez que lo actualice o use el [almacenamiento en caché de cadenas de consulta](cdn-query-string.md).  
> 
> 

Este tutorial le guiará a través de purga de los recursos de todos los nodos perimetrales de un punto de conexión.

## <a name="walkthrough"></a>Tutorial
1. En el [Portal de Azure](https://portal.azure.com), examine el perfil de red CDN que contiene el punto de conexión que quiera purgar.
2. En la hoja Perfil de CDN, haga clic en el botón para purgar.
   
    ![Hoja del perfil de red CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Abre la hoja para purgar.
   
    ![Hoja de purga de red CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. En la hoja para purgar, seleccione la dirección del servicio que quiera purgar en la lista desplegable de la dirección URL.
   
    ![Formulario para purgar](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > También puede obtener acceso a la hoja para purgar haciendo clic en el botón **Purgar** de la hoja del punto de conexión de red de CDN.  En ese caso, el campo **URL** se rellenará previamente con la dirección del servicio de ese punto de conexión concreto.
   > 
   > 
4. Seleccione los activos que quiera purgar de los nodos perimetrales.  Si quiere borrar todos los recursos, haga clic en la casilla **Purgar todo** .  De lo contrario, escriba la ruta de acceso completa de cada recurso que quiera purgar en el cuadro de texto **Ruta de acceso**. Los siguientes formatos se pueden usar en las rutas de acceso.
    1. **Purga con una sola URL**: purgue recursos concretos especificando la URL completa, con o sin la extensión de archivo; por ejemplo,`/pictures/strasbourg.png`; `/pictures/strasbourg`.
    2. **Purga con carácter comodín**: se puede usar el asterisco (\*) como carácter comodín. Purgue todas las carpetas, subcarpetas y archivos de un punto de conexión con `/*` en la ruta de acceso o todas las subcarpetas y archivos de una carpeta concreta especificando la carpeta seguido de `/*`; por ejemplo, `/pictures/*`.  Tenga en cuenta que, en estos momentos, la purga de carácter comodín no es compatible con la red CDN de Azure de Akamai. 
    3. **Purga de dominio raíz**: purgue la raíz del punto de conexión con "/" en la ruta de acceso.
   
   > [!TIP]
   > Las rutas de acceso que se van a purgar deben especificarse y ser una URL relativa que se ajuste a la siguiente [expresión regular](https://msdn.microsoft.com/library/az24scfc.aspx). **Purgar todo** y la **purga con carácter comodín** no son compatibles en estos momentos con la **red CDN de Azure de Akamai**.
   > > Purga con una sola URL `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Cadena de consulta `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Purga con carácter comodín `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";` 
   > 
   > Aparecerán más cuadros de texto de **Ruta de acceso** después de escribir texto para permitirle crear una lista de varios activos.  Puede eliminar activos en la lista haciendo clic en el botón de puntos suspensivos (...).
   > 
5. Haga clic en el botón **Purgar** .
   
    ![Botón Purgar](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Las solicitudes de purga tardan aproximadamente de 2 a 3 minutos en procesarse con la **red CDN de Azure de Verizon** (estándar y premium) y unos 7 minutos con la **red CDN de Azure de Akamai**.  La red CDN de Azure tiene un límite de 50 solicitudes de purga simultáneas en un momento dado. 
> 
> 

## <a name="see-also"></a>Consulte también
* [Carga previa de activos en un punto de conexión de CDN de Azure](cdn-preload-endpoint.md)
* [Referencia de la API de REST de red de CDN de Azure - purgar o cargar previamente un punto de conexión](https://msdn.microsoft.com/library/mt634451.aspx)

