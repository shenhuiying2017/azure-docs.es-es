---
title: Panel de información general de Azure Application Insights | Microsoft Docs
description: Supervise las aplicaciones con Azure Application Insights y la funcionalidad del panel de información general.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mbullwin
ms.openlocfilehash: 0be54c47965c6a27c3506fd37a7bf67e4b2b7924
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356093"
---
# <a name="application-insights-overview-dashboard-preview"></a>Panel de información general de Application Insights (versión preliminar)

Application Insights siempre ha proporcionado un panel de información general de resumen para permitir una valoración rápida a simple vista del mantenimiento y el rendimiento de una aplicación. El nuevo panel de información general en versión preliminar proporciona una experiencia más rápida y flexible.

## <a name="how-do-i-test-out-the-new-experience"></a>¿Cómo se prueba la nueva experiencia?

 En Application Insights, en _Introducción_, seleccione _Please try new Overview before it becomes the default experience_ (Pruebe una introducción nueva antes de que se convierta en la predeterminada).

![Información general (versión preliminar)](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-01.png)

Se inicia el nuevo panel de información general predeterminado:

![Panel de información general (versión preliminar)](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-02.png)

## <a name="better-performance"></a>Mejor rendimiento

Se ha simplificado la selección de intervalo de tiempo a favor de una interfaz más sencilla con un solo clic.

![Intervalo de tiempo](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-03.png)

El rendimiento en general ha aumentado enormemente. Cada icono de KPI de actualización dinámica predeterminada está vinculado a la característica correspondiente de Application Insights. Por ejemplo, al seleccionar las solicitudes con error se inicia el panel _Errores_:

![Errores](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Panel de la aplicación

El panel de la aplicación aprovecha la tecnología de paneles existente en Azure para proporcionar una única vista en el panel, totalmente personalizable, del mantenimiento y el rendimiento de la aplicación.

Para acceder al panel predeterminado, seleccione _Panel de la aplicación_ en la esquina superior izquierda.

![Vista de panel](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-05.png)

Si esta es la primera vez que accede al panel, se iniciará una vista predeterminada:

![Vista de panel](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-06.png)

Aunque puede mantener la vista predeterminada si así lo desea, también puede agregar o eliminar vistas del panel para adaptarse mejor a las necesidades de su equipo.

> [!NOTE]
> Todos los usuarios con acceso al recurso de Application Insights comparten el mismo panel de la aplicación. Los cambios que realice cualquiera de ellos modificarán la vista de todos.

Para volver a la experiencia de información general, simplemente seleccione:

![Botón Información general](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-07.png)

## <a name="next-steps"></a>Pasos siguientes

- [Embudos](usage-funnels.md)
- [Retención](app-insights-usage-retention.md)
- [Flujos de usuario](app-insights-usage-flows.md)
- [Paneles](app-insights-dashboards.md)
