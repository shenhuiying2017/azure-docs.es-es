---
title: "Creación de un trabajo de procesamiento de análisis de datos para Stream Analytics | Microsoft Docs"
description: "Creación de un trabajo de procesamiento de análisis de datos para Análisis de transmisiones | segmento de ruta de aprendizaje."
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
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 05fdf1e20efd129cdfc27e1d37bc9e124edf5dcd
ms.contentlocale: es-es
ms.lasthandoff: 08/28/2017

---
# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Creación de un trabajo de procesamiento de análisis de datos para Análisis de transmisiones
El recurso de nivel superior en el Análisis de transmisiones de Azure es un trabajo de análisis de transmisiones.  Consta de uno o varios orígenes de datos de entrada, una consulta que expresa la transformación de datos y uno o más destinos de salida en los que se escriben los resultados. Juntos, permiten al usuario realizar procesamiento de análisis de datos para escenarios de streaming de datos.

Para empezar a usar Análisis de transmisiones, cree un trabajo de Análisis de transmisiones.  Tenga en cuenta que esta acción no tiene ninguna implicación de facturación hasta que se inicie el trabajo.

1. Inicie sesión en el [Portal de Azure clásico](http://manage.windowsazure.com) en línea o en [Azure Portal](https://portal.azure.com/).
2. En el portal: **haga clic en Nuevo** y en **Data Services** o **Análisis de datos**, según el portal. Luego, haga clic en **Azure Stream Analytics** o **Stream Analytics** y en **Creación rápida**.
   
   ![Asistente para trabajos de procesamiento de análisis de datos](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  
   
   ![Creación de un trabajo de procesamiento de análisis de datos](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  
3. Especifique la configuración que desee para el trabajo de Análisis de transmisiones.
   
   * En el cuadro **Nombre del trabajo** , escriba un nombre que identifique el trabajo de Análisis de transmisiones. Cuando la información de **Nombre del trabajo** se valida, aparece una marca de verificación verde en el cuadro Nombre de trabajo. **Nombre del trabajo** solo puede contener caracteres alfanuméricos y el carácter "-", y debe tener entre 3 y 63 caracteres.
   * Use **Región** o **Ubicación** en Azure Portal para especificar la ubicación geográfica en la que desea ejecutar el trabajo.
   * Si utiliza el Portal de Azure, seleccione o cree una cuenta de almacenamiento para usarla como **Cuenta de almacenamiento de supervisión regional**. Esta cuenta de almacenamiento se usa para almacenar datos de supervisión para todos los trabajos de Análisis de transmisiones que se ejecutan dentro de esta región.
   * Si usa el Portal de Azure, especifique un **Grupo de recursos** nuevo o existente para hospedar recursos relacionados para la aplicación.
4. Cuando se configuren nuevas opciones del trabajo de Análisis de transmisiones, haga clic en **Crear trabajo de Análisis de transmisiones**. La creación del trabajo del Análisis de transmisiones puede tardar unos minutos. Para comprobar el estado, puede supervisar el progreso en el centro de notificaciones.
   
   ![Centro de notificaciones del trabajo de procesamiento de análisis de datos](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  
   
   ![Trabajo de procesamiento de análisis de datos en Azure Portal: creación del trabajo](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  
5. Se mostrará el nuevo trabajo con el estado **Creado**. Tenga en cuenta que el botón **Iniciar** está deshabilitado. Configure la entrada, la salida y la consulta del trabajo para poder iniciar el trabajo.
   
   ![Trabajo de procesamiento de análisis de datos: estado del trabajo](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  
   
   ![Trabajo de procesamiento de análisis de datos en Azure Portal: estado del trabajo](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)


