---
title: "Configuración de salidas de datos para trabajos de Stream Analytics | Microsoft Docs"
description: "Configuración de salidas de datos para trabajos de Análisis de transmisiones | segmento de ruta de aprendizaje."
keywords: salida de datos, movimiento de datos
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 3bbea3da-bfce-4af1-a15e-d4b23874034f
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/26/2017
ms.author: samacha
ms.openlocfilehash: 1ffa517469da1a8d79917b9747abc97ca3bef463
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>Configuración de salidas de datos para trabajos de Análisis de transmisiones

Los trabajos de Análisis de transmisiones de Azure pueden estar conectados a una o más salidas de datos, que definen una conexión a un receptor de datos existente. A medida que el trabajo de Stream Analytics procesa y transforma los datos entrantes, se escribe una transmisión de eventos de salida de datos en la salida del trabajo.

Las salidas de datos de Stream Analytics se pueden usar para generar alertas o paneles en tiempo real, desencadenar flujos de trabajo de movimiento de datos o simplemente archivar datos para el procesamiento por lotes más adelante. El Análisis de transmisiones tiene una integración de primera clase con varios servicios de Azure, que se documentan aquí con detalle.

Para agregar una salida a su trabajo del Análisis de transmisiones:

1. En [Azure Portal](https://portal.azure.com), abra el trabajo y haga clic en **Salidas** y, a continuación, haga clic en **Agregar** en la hoja Salidas que aparece.
   
    ![Agregar salidas](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  
   
2. Asigne un nombre descriptivo para esta salida en el cuadro **Alias de salida** . Este nombre se puede usar en la consulta de su trabajo más adelante para hacer referencia a la salida.  
   
    Rellene el resto de las propiedades de conexión necesarias para conectarse a su salida.  Estos campos varían según el tipo de salida y se definen en detalle aquí.  
   
    ![Elección del tipo de movimiento de datos](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  
   
3. Según el tipo de salida, puede que necesite especificar cómo se serializan o se formatean los datos. La configuración de serialización específica para cada tipo de salida se documenta aquí.
   
    Rellene el resto de las propiedades de conexión necesarias para conectarse a su origen de datos. Estos campos varían según el tipo de entrada y de origen y se definen en detalle en el [artículo Crear un trabajo](stream-analytics-create-a-job.md).  

> [!Note]
>
> Todos los elementos de salida que se agreguen al trabajo deben existir antes de que se inicie el trabajo y los eventos empiecen a fluir. Por ejemplo, si usa el almacenamiento de blobs como salida, el trabajo no creará una cuenta de almacenamiento automáticamente. Debe crearla el usuario antes de que se inicie el trabajo ASA.
> 
 

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

