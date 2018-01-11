---
title: "Creación de un trabajo de procesamiento de análisis de datos para Stream Analytics | Microsoft Docs"
description: "Creación de un trabajo de procesamiento de análisis de datos para Stream Analytics | segmento de ruta de aprendizaje."
keywords: "procesamiento de análisis de datos"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: e825fbcf-69e9-443f-b402-3b7a4568f415
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 82b9c861fee820cdb0ca1891e0e62436f422d838
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Creación de un trabajo de procesamiento de análisis de datos para Stream Analytics
El recurso de nivel superior en Azure Stream Analytics es un trabajo de Stream Analytics.  Consta de uno o varios orígenes de datos de entrada, una consulta que expresa la transformación de datos y uno o más destinos de salida en los que se escriben los resultados. Juntos, permiten al usuario realizar procesamiento de análisis de datos para escenarios de streaming de datos.

Para empezar a usar Stream Analytics, cree un trabajo de Stream Analytics.  Tenga en cuenta que esta acción no tiene ninguna implicación de facturación hasta que se inicie el trabajo.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Seleccione **Nuevo** > **Data + Analytics** (Datos y análisis)  > **Stream Analytics job** (Trabajo de Stream Analytics).
3. Seleccione **Crear**.
   
3. Especifique la configuración que desee para el trabajo de Stream Analytics.
   
   * En el cuadro **Nombre del trabajo** , escriba un nombre que identifique el trabajo de Stream Analytics. Cuando la información de **Nombre del trabajo** se valida, aparece una marca de verificación verde en el cuadro Nombre de trabajo. **Nombre del trabajo** solo puede contener caracteres alfanuméricos y el carácter "-", y debe tener entre 3 y 63 caracteres.
   * Use **Ubicación** para especificar la ubicación geográfica en la que quiere que se ejecute el trabajo.
   * Especifique un **Grupo de recursos** nuevo o existente para hospedar recursos relacionados para la aplicación.
4. Seleccione **Crear**.
La creación del trabajo del Stream Analytics puede tardar unos minutos. Para comprobar el estado, puede supervisar el progreso en el centro de notificaciones.
    
   ![Trabajo de procesamiento de análisis de datos en Azure Portal: creación del trabajo](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  
5. Se mostrará el nuevo trabajo con el estado **Creado**. Tenga en cuenta que el botón **Iniciar** está deshabilitado. Configure la entrada, la salida y la consulta del trabajo para poder iniciar el trabajo.

   
   ![Trabajo de procesamiento de análisis de datos en Azure Portal: estado del trabajo](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

