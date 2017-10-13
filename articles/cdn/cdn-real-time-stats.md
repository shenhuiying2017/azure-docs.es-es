---
title: "Estadísticas en tiempo real en la red CDN de Azure | Microsoft Docs"
description: "Las estadísticas en tiempo real proporcionan datos en tiempo real sobre el rendimiento de la red CDN de Azure al entregar contenido a los clientes."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e9b9522de6b2c54dc794b00100ffe358296ecfdd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Estadísticas en tiempo real en la CDN de Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Información general
En este documento se describe la funcionalidad de Estadísticas en tiempo real en la CDN de Microsoft Azure.  Esta funcionalidad proporciona datos en tiempo real, como el ancho de banda, los estados de la memoria caché y las conexiones simultáneas a su perfil de red CDN cuando se entrega el contenido a los clientes. Esto permite la supervisión continua del estado de su servicio en cualquier momento, incluidos los eventos de puesta en marcha.

Los gráficos siguientes están disponibles:

* [Ancho de banda](#bandwidth)
* [Códigos de estado](#status-codes)
* [Estados de la memoria caché](#cache-statuses)
* [Conexiones](#connections)

## <a name="accessing-real-time-stats"></a>Acceso a estadísticas en tiempo real
1. En el [Portal de Azure](https://portal.azure.com), vaya a su perfil de la red CDN.
   
    ![Hoja del perfil de red CDN](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. En la hoja de perfil de CDN, haga clic en el botón **Administrar** .
   
    ![Botón de administración de hoja de perfil de red CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    Se abre el portal de administración de CDN.
3. Mantenga el mouse sobre la pestaña **Análisis** y, después, sobre el control flotante **Estadísticas en tiempo real**.  Haga clic en **Objeto grande HTTP**.
   
    ![Portal de administración de la red CDN](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Se muestran los gráficos de estadísticas en tiempo real.

Cada uno de los gráficos muestra estadísticas en tiempo real para el intervalo de tiempo seleccionado, a partir del momento en que se carga la página.  Los gráficos se actualizan automáticamente cada pocos segundos.  El botón **Actualizar gráfico** , si está presente, borrará el gráfico y solo se mostrarán los datos seleccionados.

## <a name="bandwidth"></a>Ancho de banda
![Gráfico Ancho de banda](./media/cdn-real-time-stats/cdn-bandwidth.png)

El gráfico **Ancho de banda** muestra la cantidad de ancho de banda que se usa en la plataforma actual durante el intervalo de tiempo especificado. La parte sombreada del gráfico indica el uso de ancho de banda. La cantidad exacta del ancho de banda que se usa actualmente se muestra directamente debajo del gráfico de líneas.

## <a name="status-codes"></a>Códigos de estado
![Gráfico Código de estado](./media/cdn-real-time-stats/cdn-status-codes.png)

El gráfico **Códigos de estado** indica con qué frecuencia se producen ciertos códigos de respuesta HTTP durante el intervalo de tiempo especificado.

> [!TIP]
> Para obtener una descripción de cada opción de código de estado de HTTP, consulte [Azure CDN HTTP Status Codes](https://msdn.microsoft.com/library/mt759238.aspx)(Códigos de estado HTTP de la red CDN de Azure).
> 
> 

Justo encima del gráfico se muestra una lista de códigos de estado HTTP. En esta lista se indica cada código de estado que se puede incluir en el gráfico de líneas y el número actual de repeticiones por segundo para ese código de estado. De forma predeterminada, se muestra una línea para cada uno de estos códigos de estado del gráfico. Pero se puede optar por supervisar solo los códigos de estado que tienen un significado especial para la configuración de la red CDN. Para ello, active los códigos de estado que desee, desactive todas las demás opciones y haga clic en **Actualizar gráfico**. 

También puede ocultar temporalmente los datos registrados para un determinado código de estado.  En la leyenda, directamente debajo del gráfico, haga clic en el código de estado que desee ocultar. El código de estado se ocultará inmediatamente en el gráfico. Al volver a hacer clic en ese código de estado volverá a mostrarse esa opción.

## <a name="cache-statuses"></a>Estados de la memoria caché
![Gráfico Estado de caché](./media/cdn-real-time-stats/cdn-cache-status.png)

El gráfico **Estado de caché** indica con qué frecuencia se producen determinados tipos de estados de la memoria caché durante el intervalo de tiempo especificado. 

> [!TIP]
> Para ver una descripción de cada opción de código de estado de caché, consulte [Azure CDN Cache Status Codes](https://msdn.microsoft.com/library/mt759237.aspx)(Códigos de estado de caché de la red CDN de Azure).
> 
> 

Justo encima del gráfico se muestra una lista de códigos de estado de la memoria caché. En esta lista se indica cada código de estado que se puede incluir en el gráfico de líneas y el número actual de repeticiones por segundo para ese código de estado. De forma predeterminada, se muestra una línea para cada uno de estos códigos de estado del gráfico. Pero se puede optar por supervisar solo los códigos de estado que tienen un significado especial para la configuración de la red CDN. Para ello, active los códigos de estado que desee, desactive todas las demás opciones y haga clic en **Actualizar gráfico**. 

También puede ocultar temporalmente los datos registrados para un determinado código de estado.  En la leyenda, directamente debajo del gráfico, haga clic en el código de estado que desee ocultar. El código de estado se ocultará inmediatamente en el gráfico. Al volver a hacer clic en ese código de estado volverá a mostrarse esa opción.

## <a name="connections"></a>Conexiones
![Gráfico Conexiones](./media/cdn-real-time-stats/cdn-connections.png)

Este gráfico indica cuántas conexiones se han establecido para los servidores perimetrales. Cada solicitud de un recurso que pasa por la red CDN genera una conexión.

## <a name="next-steps"></a>Pasos siguientes
* Recibir notificaciones con [alertas en tiempo real en la CDN de Azure](cdn-real-time-alerts.md)
* Profundizar más con [informes de HTTP avanzados](cdn-advanced-http-reports.md)
* Analizar [patrones de uso](cdn-analyze-usage-patterns.md)

