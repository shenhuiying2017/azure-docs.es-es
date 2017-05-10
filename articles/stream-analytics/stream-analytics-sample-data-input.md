---
title: Entradas de ejemplo en Azure Stream Analytics | Microsoft Docs
description: "Identifique los problemas cuando realice la solución de problemas de trabajos de Stream Analytics."
keywords: "entrada de solución de problemas, muestreo de entrada"
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 8a8664cec88f2d04a3dff55df0e6ccea1ea79422
ms.contentlocale: es-es
ms.lasthandoff: 05/01/2017


---
# <a name="azure-stream-analytics-input-stream-sampling"></a>Muestreo de flujo de entrada de Azure Stream Analytics

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

Para obtener ayuda adicional, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

