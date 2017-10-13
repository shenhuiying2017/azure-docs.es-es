---
title: "Información general sobre las métricas en Microsoft Azure | Microsoft Docs"
description: "Obtenga información acerca de cómo personalizar los gráficos de supervisión en Azure."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c36031eb-4df5-4cd5-9479-311d493a40d2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: robb
ms.openlocfilehash: 3f9ebb0f5737714dd685f0dcc1ff4b1c0c89528f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Información general sobre las métricas en Microsoft Azure
Todos los servicios de Azure realizan un seguimiento de las métricas clave que le permiten supervisar el estado, el rendimiento, la disponibilidad y el uso de los servicios. Puede ver estas métricas en Azure Portal y, además, puede utilizar la [API de REST](https://msdn.microsoft.com/library/azure/dn931930.aspx) o el [SDK de .NET](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) para tener acceso mediante programación a todo el conjunto de métricas.

Para algunos servicios, puede que necesite activar diagnósticos para ver las métricas. Para otros, como las máquinas virtuales, obtendrá un conjunto básico de métricas, pero necesita habilitar el conjunto completo de métricas de alta frecuencia. Consulte [Habilitación de supervisión y diagnóstico](insights-how-to-use-diagnostics.md) para obtener más información.

## <a name="using-monitoring-charts"></a>Uso de gráficos de supervisión
Puede representar en gráficos cualquier métrica durante cualquier período de tiempo que elija.

1. En el [Portal de Azure](https://portal.azure.com/), haga clic en **Examinar**y luego en el recurso que le interese supervisar.
2. La sección **Supervisión** contiene las métricas más importantes para cada recurso de Azure. Por ejemplo, una aplicación web tiene **Solicitudes y errores** donde, como máquina virtual, tendría **Porcentaje de CPU** y **Lectura y escritura de disco**: ![modo Supervisión](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. Al hacer clic en cualquier gráfico aparecerá la hoja **Métrica** . En el cuadro, además del gráfico, hay una tabla que muestra las agregaciones de las métricas (como promedio, mínimo y máximo, durante el intervalo de tiempo que ha elegido). A continuación se muestran las reglas de alerta para el recurso.
    ![Hoja Métrica](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. Para personalizar las líneas que aparecen, haga clic en el botón **Editar** del gráfico o en el comando **Editar gráfico** de la hoja Métrica.
5. En la hoja Editar consulta, puede hacer tres cosas:
   
   * Cambiar el intervalo de tiempo
   * Cambiar la apariencia entre líneas y barras
   * Elegir otras métricas ![Editar consulta](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. Cambiar el intervalo de tiempo es tan fácil como seleccionar un intervalo diferente (como **Hora pasada**) y hacer clic en **Guardar** en la parte inferior de la hoja. También puede elegir **Personalizado**, que le permite elegir cualquier período de tiempo durante las últimas dos semanas. Por ejemplo, puede ver las dos semanas completas o, simplemente, una hora de ayer. Para especificar una hora diferente, escríbala en el cuadro de texto.
    ![Intervalo de tiempo personalizado](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. Debajo del intervalo de tiempo, puede elegir el número de métricas que desea mostrar en el gráfico.
8. Al hacer clic en Guardar, los cambios se guardarán para ese recurso concreto. Por ejemplo, si tiene dos máquinas virtuales y cambiar un gráfico en una de ellas, la otra no resultará afectada.

## <a name="creating-side-by-side-charts"></a>Creación de gráficos paralelos
Con las potentes opciones de personalización del portal, puede agregar tantos gráficos como desee.

1. En el menú **...** de la parte superior de la hoja, haga clic en **Agregar iconos**:  
    ![Adición de menú](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. A continuación, se puede seleccionar un gráfico desde la **Galería** en el lado derecho de la pantalla: ![Galería](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Si no ve la métrica que desea, siempre puede agregar una de las métricas presentes y **Editar** el gráfico para que aparezca la métrica que necesita.

## <a name="monitoring-usage-quotas"></a>Supervisión de las cuotas de uso
La mayoría de las métricas muestran tendencias a lo largo del tiempo, pero determinados datos, como las cuotas de uso, suponen información puntual con un umbral.

También puede ver las cuotas de uso en la hoja de los recursos que tienen cuotas:

![Uso](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Al igual que con las métricas, puede utilizar la [API de REST](https://msdn.microsoft.com/library/azure/dn931963.aspx) o el [SDK de .NET](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) para tener acceso a todo el conjunto de cuotas de uso mediante programación.

## <a name="next-steps"></a>Pasos siguientes
* [Reciba notificaciones de alerta](insights-receive-alert-notifications.md) cada vez que una métrica traspase un umbral.
* [Habilite la supervisión y el diagnóstico](insights-how-to-use-diagnostics.md) para recopilar métricas detalladas de alta frecuencia en su servicio.
* [Escale el número de instancias automáticamente](insights-how-to-scale.md) para asegurarse de que el servicio está disponible y tiene capacidad de respuesta.
* [Supervise el rendimiento de la aplicación](../application-insights/app-insights-azure-web-apps.md) si desea comprender exactamente cómo está actuando su código en la nube.
* Utilice [aplicaciones y páginas web de Application Insights para JavaScript](../application-insights/app-insights-web-track-usage.md) para obtener el análisis del cliente acerca de los exploradores que visitan una página web.
* [Supervise la disponibilidad y la capacidad de respuesta de cualquier página web](../application-insights/app-insights-monitor-web-app-availability.md) con Application Insights, para poder averiguar si su página está inactiva.

