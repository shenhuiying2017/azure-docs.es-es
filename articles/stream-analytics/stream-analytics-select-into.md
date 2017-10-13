---
title: "Depuración de consultas de Azure Stream Analytics mediante SELECT INTO | Microsoft Docs"
description: Datos de ejemplo de consulta intermedia mediante instrucciones SELECT INTO en Stream Analytics
keywords: 
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: 6ffa756eef0cfa44d7dd397e43afbf054ac2df7a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="debug-queries-by-using-select-into-statements"></a>Depuración de consultas mediante instrucciones SELECT INTO

En el procesamiento de datos en tiempo real, puede ser útil conocer qué aspecto tendrán los datos en el medio de la consulta. Como las entradas o pasos de un trabajo de Azure Stream Analytics se pueden leer muchas veces, puede escribir instrucciones SELECT INTO adicionales. Si lo hace, se generan daros intermedios en el almacenamiento y puede inspeccionar la integridad de los datos, igual que hacen las *variables de inspección* cuando se depura un programa.

## <a name="use-select-into-to-check-the-data-stream"></a>Uso de SELECT INTO para comprobar el flujo de datos

La siguiente consulta de ejemplo en un trabajo de Azure Stream Analytics tiene una entrada de secuencias, dos entradas de datos de referencia y una salida a Azure Table Storage. La consulta combina datos del centro de eventos y dos blobs de referencia para obtener la información de nombre y categoría:

![Ejemplo de consulta SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Tenga en cuenta que el trabajo está en ejecución, pero no se genera ningún evento en la salida. En el icono **Supervisión**, que se muestra aquí, puede ver que la entrada genera datos, pero no sabe qué paso de la cláusula **JOIN** ha hecho que se descarten todos los eventos.

![Icono de Supervisión](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
En esta situación, puede agregar unas cuantas instrucciones SELECT INTO adicionales para "registrar" los resultados intermedios de JOIN y los datos que se leen de la entrada.

En este ejemplo, hemos agregado dos nuevas "salidas temporales". Estas pueden ser cualquier receptor que desee. Aquí usamos Azure Storage como ejemplo:

![Adición de instrucciones SELECT INTO adicionales](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

A continuación, puede volver a escribir la consulta del modo siguiente:

![Consulta SELECT INTO reescrita](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Ahora, inicie de nuevo el trabajo y deje que se ejecute durante unos minutos. A continuación, consulte temp1 y temp2 con Visual Studio Cloud Explorer para generar las siguientes tablas:

**tabla temp1**
![SELECT INTO tabla temp1](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**tabla temp2**
![SELECT INTO tabla temp2](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Como puede ver, temp1 y temp2 tienen ambas datos, y la columna de nombre está rellenada correctamente en temp2. Sin embargo, dado que todavía no hay ningún dato en la salida, algo sucede:

![Tabla SELECT INTO output1 sin datos](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Mediante el muestreo de los datos, puede estar casi seguro de que el problema está relacionado con la segunda cláusula JOIN. Puede descargar los datos de referencia del blob y echar un vistazo:

![Tabla de referencia de SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Como puede ver, el formato del GUID en los datos de esta referencia es distinto del formato de la columna [from] en temp2. Por eso los datos no llegan a output1 como estaba previsto.

Puede corregir el formato de datos, cargarlo para que haga referencia al blob e intentarlo de nuevo:

![Tabla temporal de SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Esta vez, los datos en la salida tienen el formato adecuado y se han rellenado según lo previsto.

![Tabla final de SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)


## <a name="get-help"></a>Obtener ayuda

Para obtener ayuda adicional, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes

* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

