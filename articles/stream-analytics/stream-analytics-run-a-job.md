---
title: Inicio de trabajos de streaming en Stream Analytics | Microsoft Docs
description: "Ejecución de un trabajo de streaming en Análisis de transmisiones de Azure | segmento de ruta de acceso de aprendizaje."
keywords: Trabajos de streaming
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 9d46950f-2b69-49ce-a567-df558c5dd820
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 03d43476c1946221538acccd1251c64ed3f02e13
ms.contentlocale: es-es
ms.lasthandoff: 08/29/2017

---
# <a name="how-to-run-a-streaming-job-in-azure-stream-analytics"></a>Ejecución de un trabajo de streaming en Análisis de transmisiones de Azure
Cuando se hayan especificado una entrada, una consulta y una salida de trabajo, podrá iniciar el trabajo de Análisis de transmisiones.

Para iniciar su trabajo:

1. En el Portal de Azure clásico, desde el panel del trabajo, haga clic en **Iniciar** en la parte inferior de la página.
   
   ![Botón Iniciar trabajo](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)  
   
   En Azure Portal, haga clic en **Iniciar** en la parte superior de la página de trabajo.
   
   ![Botón Iniciar trabajo de Azure Portal](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)  
2. Especifique un valor en **Iniciar salida** para determinar cuándo comenzará este trabajo a generar una salida. La configuración predeterminada para los trabajos que no se han iniciado anteriormente es **Hora de inicio del trabajo**, lo que significa que el trabajo iniciará inmediatamente el procesamiento de datos. También puede especificar un tiempo **Personalizado** en el pasado (para consumir datos históricos) o en el futuro (para retrasar el procesamiento hasta una fecha futura). En los casos en que un trabajo se ha iniciado y detenido anteriormente, la opción **Hora de la última detención** está disponible para reanudar el trabajo desde la última hora de salida y evitar la pérdida de datos.  
   
   ![Hora de inicio del trabajo de streaming](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)  
   
   ![Hora de inicio del trabajo de streaming de Azure Portal](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)  
3. Confirme la selección. El estado del trabajo cambiará a *Iniciando* y, en cuanto se inicie el trabajo, pasará a *En ejecución*. Puede supervisar el progreso de la operación **Iniciar** en el **Centro de notificaciones**:
   
   ![progreso del trabajo de streaming](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)  
   
   ![Progreso del trabajo de streaming de Azure Portal](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)  

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


