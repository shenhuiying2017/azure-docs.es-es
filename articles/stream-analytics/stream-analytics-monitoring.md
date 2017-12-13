---
title: "Descripción de la supervisión del trabajo de Stream Analytics y cómo supervisar consultas | Microsoft Docs"
description: "Descripción de la supervisión del trabajo de Stream Analytics"
keywords: "supervisión de consultas"
services: stream-analytics
documentationcenter: 
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 5f5cc00f-4a7b-491e-89e1-dbafea46d399
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 7474f45494c6190ffcac354e75458b18f5777fb9
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Descripción de la supervisión del trabajo de Stream Analytics y cómo supervisar consultas

## <a name="introduction-the-monitor-page"></a>Introducción: Página de supervisión
Azure Portal muestra las métricas de rendimiento clave que se pueden usar para supervisar y solucionar problemas de rendimiento del trabajo y consultas. Para ver estas métricas, vaya al trabajo de Stream Analytics para el que le interesa ver las métricas y consulte la sección **Supervisión** de la página de información general.  

![Vínculo de supervisión](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Se mostrará la siguiente ventana:

![Panel de supervisión de trabajos](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Métricas disponibles para Stream Analytics
| Métrica                 | Definición                               |
| ---------------------- | ---------------------------------------- |
| SU % uso       | El uso de las unidades de streaming asignadas a un trabajo en la pestaña Escala del trabajo. Si este indicador llega o supera el 80 %, existe una gran probabilidad de que el procesamiento de eventos se retrase o deje de avanzar. |
| Eventos de entrada           | Cantidad de datos recibidos por el trabajo de Stream Analytics, en términos de recuento de eventos. Puede usarse para validar que los eventos que se envían al origen de entrada. |
| Eventos de salida          | Cantidad de datos enviados por el trabajo de Stream Analytics al destino de salida, en términos de recuento de eventos. |
| Eventos que no funcionan    | Número de eventos recibidos fuera de orden que se eliminan o se les asigna una marca de tiempo ajustada, según la Directiva de ordenación de eventos. Puede verse afectado por la configuración del ajuste de Período de tolerancia de fuera de servicio. |
| Errores de conversión de datos | Número de errores de conversión de datos que produce un trabajo de Stream Analytics. |
| Errores de tiempo de ejecución         | El número total de errores relacionados con el procesamiento de consultas (sin incluir los errores encontrados durante la ingesta de eventos o los resultados de salida) |
| Eventos de entrada retrasada      | Número de eventos que llegan tarde del origen y que se han eliminado o cuya marca de tiempo se ha ajustado, en función de la configuración de la Directiva de ordenación de eventos del ajuste del Período de tolerancia de fuera de servicio. |
| Solicitudes de función      | Número de llamadas a la función Azure Machine Learning (si corresponde). |
| Solicitudes de función con errores | Número de llamadas a la función Azure Machine Learning con error (si corresponde). |
| Eventos de función        | Número de eventos enviados a la función Azure Machine Learning (si corresponde). |
| Bytes del evento de entrada      | Cantidad de datos recibidos por el trabajo de Stream Analytics, en bytes. Puede usarse para validar que los eventos que se envían al origen de entrada. |


## <a name="customizing-monitoring-in-the-azure-portal"></a>Personalización de la supervisión en Azure Portal
Puede ajustar el tipo de gráfico, las métricas que se muestran y el intervalo de tiempo en la configuración de Editar gráfico. Para obtener detalles, vea [Personalización de la supervisión](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Escala de tiempo de supervisión de consultas](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Salida más reciente
Otro punto de datos interesante para supervisar el trabajo es la hora de la salida más reciente, que se muestra en la página Introducción.
La hora es el tiempo de aplicación (es decir, el tiempo usando la marca de tiempo desde los datos de eventos) de la salida más reciente de su trabajo.

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

