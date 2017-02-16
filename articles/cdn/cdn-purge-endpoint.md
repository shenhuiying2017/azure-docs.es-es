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
translationtype: Human Translation
ms.sourcegitcommit: 57d00f2192fed7a2e89ac94e110ebb7e84c83b72
ms.openlocfilehash: e19c08d7a47255eb6a6db631cb88ecaa053ee183


---
# <a name="purge-an-azure-cdn-endpoint"></a>Purgar un punto de conexión de red CDN de Azure
## <a name="overview"></a>Información general
Los nodos perimetrales de la red CDN almacenarán recursos en caché hasta que el período de vida de dichos recursos (TTL) expire.  Tras la expiración del TTL del activo, cuando un cliente solicite el activo desde el nodo perimetral, este nodo recuperará una nueva copia actualizada del activo para atender la solicitud de cliente y almacenar actualizada la memoria caché.

A veces puede que quiera purgar contenido almacenado en caché de todos los nodos perimetrales y forzarlos todos para recuperar nuevos activos actualizados.  Esto puede deberse a actualizaciones de la aplicación web o a actualizaciones rápidas de los activos de actualización que contienen información incorrecta.

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
4. Seleccione los activos que quiera purgar de los nodos perimetrales.  Si quiere borrar todos los recursos, haga clic en la casilla **Purgar todo** .  De lo contrario, escriba la ruta de acceso completa de cada recurso que quiera purgar (por ejemplo, `/pictures/kitten.png`) en el cuadro de texto **Ruta de acceso** .
   
   > [!TIP]
   > Aparecerán más cuadros de texto de **Ruta de acceso** después de escribir texto para permitirle crear una lista de varios activos.  Puede eliminar activos en la lista haciendo clic en el botón de puntos suspensivos (...).
   > 
   > Las rutas de acceso tienen que ser direcciones URL relativas que se ajusten a la siguiente [expresión regular](https://msdn.microsoft.com/library/az24scfc.aspx): `^\/(?:[a-zA-Z0-9-_.\u0020]+\/)*\*$";`.  Para la **red CDN de Azure de Verizon** (estándar y premium), el asterisco (\*) se puede usar como un carácter comodín (por ejemplo, `/music/*`).  Los caracteres comodín y **Purgar todo** no se admiten con la **red CDN de Azure de Akamai**.
   > 
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




<!--HONumber=Jan17_HO4-->


