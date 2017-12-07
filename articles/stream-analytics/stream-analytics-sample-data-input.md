---
title: Prueba de consulta de Azure Stream Analytics | Microsoft Docs
description: "Identifique los problemas cuando realice la solución de problemas de trabajos de Stream Analytics."
keywords: "entrada de solución de problemas, muestreo de entrada"
documentationcenter: 
services: stream-analytics
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: e2636b8b89b86bbb2a2991972386462535d5a10f
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="azure-stream-analytics-query-testing-and-input-stream-sampling"></a>Muestreo de flujo de entrada y prueba de consulta de Azure Stream Analytics

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

## <a name="get-help"></a>Obtener ayuda

Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
