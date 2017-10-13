---
title: "Explorador de métricas de Azure Monitor | Microsoft Docs"
description: "Descubra las nuevas características en el Explorador de métricas de Azure Monitor"
author: vgorbenko
manager: Victor.Mushkatin
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2397596a-071f-4d49-8893-bec5f735bd7b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: vitaly.gorbenko
ms.openlocfilehash: 537dd6d64fe49093dd73d8040cde5a9153a7bd5c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-monitor-metrics-explorer"></a>Explorador de métricas de Azure Monitor

Este procedimiento describe la experiencia de nueva generación de gráficos de métricas de Azure Monitor que se encuentra actualmente en versión preliminar pública. La nueva experiencia admite la representación de gráficos tanto para métricas multidimensionales como métricas básicas sin ninguna dimensión. Se pueden trazar gráficos que se superpongan a las métricas de diferentes tipos de recursos, varios grupos de recursos y suscripciones. Los gráficos de métricas multidimensionales se pueden personalizar al aplicar los filtros de dimensión y mediante agrupaciones. Los gráficos, incluidos los gráficos personalizados, pueden anclarse a los paneles.

Si quiere obtener información sobre la experiencia anterior, que solo es compatible con las métricas básicas sin dimensiones, consulte la sección titulada “Acceso a métricas a través del portal” en [Información general sobre las métricas en Microsoft Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

## <a name="what-is-azure-monitor-metrics-explorer"></a>¿Qué es el Explorador de métricas de Azure Monitor?

El Explorador de métricas de Azure Monitor es un componente de Microsoft Azure Portal que permite trazar los gráficos, correlacionar visualmente las tendencias e investigar crestas y valles en valores de las métricas. El Explorador de métricas es un punto de partida esencial para investigar diversos problemas de rendimiento y disponibilidad en las aplicaciones e infraestructura hospedadas en Azure o supervisadas por servicios de Azure Monitor. 

## <a name="what-are-metrics-in-azure"></a>¿Qué son las métricas en Azure?

Las métricas en Microsoft Azure son la serie de valores medidos y recuentos que se recopilan y se almacenan con el tiempo. Hay métricas estándar (o de la “plataforma”) y métricas personalizadas. La misma plataforma de Azure le proporciona las métricas estándares. Las métricas estándares reflejan las estadísticas de uso y mantenimiento de los recursos de Azure. Mientras que las métricas personalizadas se envían a Azure desde las aplicaciones mediante la [API de Application Insights para los eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Las métricas personalizadas se almacenan en los recursos de Application Insights junto con otras métricas específicas de las aplicaciones.

## <a name="what-are-multi-dimensional-metrics"></a>¿Qué son las métricas multidimensionales?

Muchos de los recursos de Azure ahora muestran métricas multidimensionales. Estas métricas realizar un seguimiento de varias series de valores para una o varias dimensiones con nombre. Por ejemplo, una métrica “Espacio disponible en disco duro” puede tener una dimensión denominada “Unidad” con los valores “C:” o “D:”, que permitiría ver cualquier espacio disponible en el disco duro en todas las unidades o para cada unidad individual. 

En el ejemplo siguiente se muestran dos conjuntos de datos para una métrica hipotética denominada “Rendimiento de la red”. El primer conjunto de datos no tiene ninguna dimensión. El segundo conjunto de datos muestra los valores con dos dimensiones, “Dirección IP” y “Dirección”:

### <a name="network-throughput"></a>Rendimiento de la red
(Esta métrica no tiene ninguna dimensión).

 |Timestamp        | Valor de métrica | 
   | ------------- |:-------------| 
   | 9/8/2017 8:14 | 1331,8 Kbps | 
   | 9/8/2017 8:15 | 1141,4 Kbps |
   | 9/8/2017 8:16 | 1110,2 Kbps |

Esta métrica no dimensional solo puede responder una pregunta básica, como “¿cuál era el rendimiento de la red en un momento dado?”.

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Rendimiento de la red + dos dimensiones (“IP” y “Dirección”)

| Timestamp          | Dimensión “IP” | Dimensión “Dirección” | Valor de métrica| 
   | ------------- |:-----------------|:------------------- |:-----------|  
   | 9/8/2017 8:14 | IP="192.168.5.2" | Dirección="Envío"    | 646,5 Kbps |
   | 9/8/2017 8:14 | IP="192.168.5.2" | Dirección="Recepción" | 420,1 Kbps |
   | 9/8/2017 8:14 | IP="10.24.2.15"  | Dirección="Envío"    | 150,0 Kbps | 
   | 9/8/2017 8:14 | IP="10.24.2.15"  | Dirección="Recepción" | 115,2 Kbps |
   | 9/8/2017 8:15 | IP="192.168.5.2" | Dirección="Envío"    | 515,2 Kbps |
   | 9/8/2017 8:15 | IP="192.168.5.2" | Dirección="Recepción" | 371,1 Kbps |
   | 9/8/2017 8:15 | IP="10.24.2.15"  | Dirección="Envío"    | 155,0 Kbps |
   | 9/8/2017 8:15 | IP="10.24.2.15"  | Dirección="Recepción" | 100,1 Kbps |

Esta métrica puede responder a preguntas como “¿cuál era el rendimiento de la red para cada dirección IP?” y “¿cuántos datos se enviaron en comparación los datos que se recibieron?”. Las métricas multidimensionales llevan valores analíticos y de diagnóstico adicionales en comparación con las métricas no dimensionales. 

## <a name="how-do-i-create-a-new-chart"></a>¿Cómo se crea un nuevo gráfico?

   > [!NOTE]
   > Algunas de las características de la experiencia anterior de métricas aún no están disponibles en el nuevo Explorador de métricas. Si bien la nueva experiencia se encuentra en versión preliminar, puede continuar usando la antigua vista de métricas (no dimensionales) de Azure Monitor. 

1. Abra Azure Portal.
2. Vaya a la nueva pestaña **Monitor** y seleccione **Métricas (versión preliminar)**.

   ![Imagen de Métricas (versión preliminar)](./media/monitoring-metric-charts/001.png)

3. El **selector de métricas** se abrirá automáticamente. Elija un recurso de la lista para ver las métricas asociadas. Solo los recursos con métricas se muestran en la lista.

   ![Imagen de Métricas (versión preliminar)](./media/monitoring-metric-charts/002.png)

   > [!NOTE]
   >Si tiene más de una suscripción a Azure, el Explorador de métricas extrae los recursos de todas las suscripciones que están seleccionadas en la lista de Configuración del portal -> Filtrar por suscripciones. Para cambiar esto, haga clic en el icono de engranaje de Configuración del portal en la parte superior de la pantalla y seleccione las suscripciones que quiere utilizar.

4. Para algunos tipos de recursos (es decir, cuentas de almacenamiento y máquinas virtuales), antes de seleccionar una métrica debe elegir un **Subservicio**. Cada subservicio lleva su propio conjunto de métricas que son pertinentes para este subservicio únicamente, y no para otros subservicios.

   Por ejemplo, cada Azure Storage tiene métricas para los subservicios “Blobs”, “Files”, “Queues” y “Tables”, que son todas partes de la cuenta de almacenamiento. Sin embargo, la métrica “Queue Message Count” se aplica naturalmente al subservicio “Queue” y no a ningún otro subservicio de la cuenta de almacenamiento.

   ![Imagen de Métricas (versión preliminar)](./media/monitoring-metric-charts/003.png)

5. Seleccione una métrica de la lista. Si conoce un nombre parcial de la métrica que desea, puede empezar a escribirlo para ver una lista filtrada de las métricas disponibles:

   ![Imagen de Métricas (versión preliminar)](./media/monitoring-metric-charts/004.png)

6. Después de seleccionar una métrica, el gráfico se representará con la agregación predeterminada de la métrica seleccionada. En este momento, puede hacer clic fuera del **selector de métricas** para cerrarlo. Como alternativa, también puede cambiar el gráfico a otra agregación. Para algunas métricas, cambiar la agregación le permite elegir qué valor desea ver en el gráfico. Por ejemplo, puede cambiar entre los valores promedio, mínimo y máximo. 

7. Al hacer clic en el icono Agregar métrica ![icono de métrica](./media/monitoring-metric-charts/icon001.png) y repetir los pasos del 3 al 6, puede agregar más métricas en el mismo gráfico.

   > [!NOTE]
   > Normalmente no querrá tener en un gráfico métricas con unidades de medida diferentes (es decir, milisegundos y kilobytes) ni con una escala muy diferente. En su lugar, considere el uso de varios gráficos. Haga clic en el botón Agregar gráfico para crear varios gráficos en el Explorador de métricas.

## <a name="how-do-i-apply-filters-to-the-charts"></a>¿Cómo se aplican los filtros a los gráficos?

Puede aplicar filtros a los gráficos que muestran métricas con dimensiones. Por ejemplo, si la métrica “Transaction count” tiene una dimensión, “Response type”, que indica si la respuesta de las transacciones se realizó correctamente o no, el filtrado en esta dimensión trazaría una línea de gráfico solo para transacciones correctas (o solo con errores). 

### <a name="to-add-a-filter"></a>Para agregar un filtro:

1. Haga clic en el icono Agregar filtro ![icono de filtro](./media/monitoring-metric-charts/icon002.png) sobre el gráfico.

2. Seleccione qué dimensión (propiedad) quiere filtrar.

   ![imagen de métrica](./media/monitoring-metric-charts/006.png)

3. Seleccione qué valores de dimensión quiere incluir al trazar el gráfico (en este ejemplo se muestra el filtrado excluyente de las transacciones de almacenamiento correctas):

   ![imagen de métrica](./media/monitoring-metric-charts/007.png)

4. Después de seleccionar los valores de filtro, haga clic fuera del Selector de filtro para cerrarlo. Ahora el gráfico muestra cuántas transacciones de almacenamiento han tenido error:

   ![imagen de métrica](./media/monitoring-metric-charts/008.png)

5. Puede repetir los pasos del 1 al 4 para aplicar varios filtros a los gráficos mismos.

## <a name="how-do-i-segment-a-chart"></a>¿Cómo se puede segmentar un gráfico?

Puede dividir una métrica por dimensión para visualizar cómo se comparan entre sí los distintos segmentos de la métrica, y para identificar los segmentos no relevantes de una dimensión. 

### <a name="to-segment-a-chart"></a>Para segmentar un gráfico:

1. Haga clic en el icono Agregar agrupación  ![imagen de métrica](./media/monitoring-metric-charts/icon003.png) sobre el gráfico.
 
   > [!NOTE]
   > Puede tener varios filtros, pero solo una agrupación en un único gráfico.

2. Elija una dimensión por la que quiera segmentar el gráfico: 

   ![imagen de métrica](./media/monitoring-metric-charts/010.png)

   Ahora el gráfico muestra varias líneas, una para cada segmento de dimensión:

   ![imagen de métrica](./media/monitoring-metric-charts/012.png)

3. Haga clic fuera del **Selector de agrupación** para cerrarlo.

   > [!NOTE]
   > Use el filtrado y la agrupación en la misma dimensión para ocultar los segmentos que no son pertinentes para su escenario y facilitar la lectura de los gráficos.

## <a name="how-do-i-pin-charts-to-dashboards"></a>¿Cómo puedo anclar gráficos a los paneles?

Después de configurar un gráfico, puede que quiera agregarlo a los paneles para poder volver a verlo, posiblemente en el contexto de otra telemetría de supervisión, o compartirlo con su equipo. 

Para anclar un gráfico configurado a un panel:

Después de configurar el gráfico, haga clic en el menú **Chart Actions** (Acciones del gráfico) en la esquina superior derecha del gráfico y haga clic en **Anclar al panel**.

   ![imagen de métrica](./media/monitoring-metric-charts/013.png)

## <a name="next-steps"></a>Pasos siguientes

  Lea [Creating custom KPI dashboards](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) (Creación de paneles de KPI personalizados) para obtener información sobre las prácticas recomendadas para la creación de paneles accionables con métricas.