---
title: Prueba de consulta de Azure Stream Analytics | Microsoft Docs
description: "Cómo probar consultas en trabajos de Stream Analytics."
keywords: "probar consulta, consulta de solución de problemas"
documentation center: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: 2898e3404dcfa3d75e3920f9c83e4efa7201998e
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="test-azure-stream-analytics-queries-in-the-azure-portal"></a>Prueba de consultas de Azure Stream Analytics en Azure Portal

Con Azure Stream Analytics, puede probar consultas en Azure Portal sin tener que iniciar o detener un trabajo.

## <a name="test-the-input"></a>Prueba de la entrada

1. Para probar con datos de entrada de ejemplo, haga clic en cualquiera de las entradas y, a continuación, seleccione **Cargar datos de ejemplo desde un archivo**. Actualmente, solo puede cargar datos con formato JSON. Si los datos tienen un formato distinto, como CSV, deberá convertirlos al formato JSON antes de cargarlos. Puede usar cualquier herramienta de conversión de código abierto, como este [convertidor de CSV a JSON](http://www.convertcsv.com/csv-to-json.htm), para convertir los datos al formato JSON.

    ![consulta de prueba del editor de consultas de Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

2. Una vez finalizada la carga, haga clic en **Probar** para probar esta consulta con los datos de ejemplo que ha proporcionado.

    ![datos de prueba de ejemplo del editor de consultas de Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

El resultado de la consulta se muestra en el explorador, con el vínculo Descargar resultados por si desea guardar el resultado de prueba para un uso posterior. Ahora puede modificar iterativamente y fácilmente la consulta y probarla repetidamente para ver cómo cambia la salida.

![Salida de ejemplo del editor de consultas de Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

Con varias salidas usadas en una consulta, podrá ver los resultados de ambas salidas de forma separada y alternar fácilmente entre ellas.

Cuando esté satisfecho con los resultados mostrados en el explorador, podrá guardar la consulta, iniciar el trabajo y permitir que se procesen eventos sin errores.

## <a name="get-help"></a>Obtención de ayuda

Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
