---
title: Prueba de una consulta mediante datos de ejemplo en Azure Stream Analytics
description: En este artículo se describe cómo probar una consulta mediante algunos datos de entrada de ejemplo en Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 305b767ee86de6c8b04fed17514a9092afc2d735
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/12/2018
---
# <a name="test-a-query-and-sample-input-in-azure-stream-analytics"></a>Prueba de una consulta mediante datos de ejemplo en Azure Stream Analytics 

Mediante Azure Stream Analytics, puede muestrear eventos de entrada que procedan de un archivo y probar consultas en el portal de sin necesidad de iniciar o detener un trabajo.

## <a name="testing-your-query"></a>Prueba de la consulta

En el panel de detalles de trabajos de Stream Analytics, abra la hoja **Editor de consultas** haciendo clic en el nombre de la consulta en **Consulta**. (En nuestro escenario de ejemplo, puesto que no se ha creado aún ninguna consulta, haga clic en el marcador de posición **< >**).

![Editor de consultas de Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor.png)

La hoja de editor enriquecido para la creación de la consulta se muestra como ocurría en la versión anterior. Ahora se ha actualizado la hoja con un nuevo panel izquierdo que muestra las entradas y salidas que usan la consulta y que se definen para este trabajo.

![El editor de consultas de Stream Analytics introduce o extrae listas](media/stream-analytics-sample-data-input/stream-analytics-query-editor-highlight.png)

También se muestra una entrada o salida adicionales, que no se han definido. Provienen de la nueva plantilla de consulta inicial. Se cambia, o incluso desaparecen por completo, al editar la consulta. Puede ignorar estos errores con seguridad por ahora.

Para probar con datos de entrada de ejemplo, haga clic en cualquiera de las entradas y, a continuación, seleccione **Cargar datos de ejemplo desde un archivo**.

![Los datos de ejemplo de carga del editor de consultas de Stream Analytics desde el comando de archivos](media/stream-analytics-sample-data-input/stream-analytics-query-editor-upload.png)

Una vez finalizada la carga, haga clic en **Probar** para probar esta consulta con los datos de ejemplo que acaba de proporcionar.

![Botón de prueba del editor de consultas de Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-test.png)

Si desea guardar la salida de prueba para un uso posterior, el resultado de la consulta se muestra en el explorador con un vínculo a los resultados de descarga. Ahora puede modificar iterativamente y fácilmente la consulta y probarla repetidamente para ver cómo cambia la salida.

![Salida de ejemplo del editor de consultas de Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-samples-output.png)

En la imagen anterior, se ha agregado un segundo resultado, denominado **HighAvgTempOutput**.

Cuando use varias entradas en una consulta, podrá ver los resultados de cada salida de forma separada y alternar fácilmente entre ellos.

Cuando esté satisfecho con los resultados, puede guardar la consulta, iniciar el trabajo, ponerse cómodo y observar la magia de Stream Analytics.

## <a name="get-help"></a>Obtención de ayuda

Para más ayuda, pruebe el [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
