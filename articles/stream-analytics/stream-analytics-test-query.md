---
title: Prueba del trabajo en Azure Stream Analytics con datos de ejemplo | Microsoft Docs
description: Cómo probar consultas en trabajos de Stream Analytics.
keywords: probar un trabajo, muestreo de entrada, carga de los datos de ejemplo
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: stream-analytics
ms.workload: data-services
ms.date: 03/18/2018
ms.author: sngun
ms.openlocfilehash: c026a91fff5b8ef5774993b335f8d61877aa5d39
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="test-your-stream-analytics-query-with-sample-data"></a>Prueba de las consultas de Stream Analytics con datos de ejemplo

Con Azure Stream Analytics puede cargar datos de ejemplo y probar las consultas del portal sin iniciar ni detener ningún trabajo.

## <a name="upload-sample-data-and-test-the-query"></a>Carga de los datos de ejemplo y prueba de la consulta

1. Vaya a uno de los trabajos de Stream Analytics existente > haga clic en **Consultar** para abrir la ventana del editor de consultas. 

2. Para probar la consulta con datos de entrada de ejemplo, haga clic con el botón derecho en cualquiera de las entradas y seleccione **Cargar datos de ejemplo desde un archivo**. Actualmente, solo puede cargar datos con formato JSON. Si los datos tienen un formato distinto, como CSV, deberá convertirlos al formato JSON antes de cargarlos. Puede usar cualquier herramienta de conversión de código abierto, como este [convertidor de CSV a JSON](http://www.convertcsv.com/csv-to-json.htm), para convertir los datos al formato JSON.

    ![consulta de prueba del editor de consultas de Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

3. Una vez finalizada la carga, haga clic en **Probar** para probar esta consulta con los datos de ejemplo que ha proporcionado.

    ![datos de prueba de ejemplo del editor de consultas de Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

4. Si desea guardar la salida de prueba para un uso posterior, el resultado de la consulta se muestra en el explorador con un vínculo a los resultados de descarga. Ahora puede modificar iterativamente y fácilmente la consulta y probarla repetidamente para ver cómo cambia la salida.

   ![Salida de ejemplo del editor de consultas de Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

Cuando use varias entradas en una consulta, podrá ver los resultados de cada salida de forma separada y alternar fácilmente entre ellos. Tras verificar los resultados mostrados en el explorador, podrá guardar la consulta, iniciar el trabajo y permitir que se procesen eventos sin errores.
Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
