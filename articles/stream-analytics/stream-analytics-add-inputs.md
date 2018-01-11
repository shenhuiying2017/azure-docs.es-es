---
title: "Incorporación de entradas de datos a trabajos de Stream Analytics | Microsoft Docs"
description: "Obtenga información acerca de cómo enlazar un origen de datos al trabajo de Stream Analytics como entrada de datos de streaming desde Event Hubs, o bien como datos de referencia desde Blob Storage."
keywords: entrada de datos, datos de streaming
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: 
ms.assetid: 9e59bd24-2a80-4ecb-b6b2-309a07c70bcd
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 7a4eb8642a0496e126b79724b4048bae7cc15a68
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Adición de entradas de datos de streaming o datos de referencia a trabajos de Stream Analytics
Aprenda a enlazar un origen de datos al trabajo de Stream Analytics como entrada de datos de streaming desde Event Hubs, o bien como datos de referencia desde Blob Storage.

Los trabajos de Azure Stream Analytics pueden estar conectados a una entrada de datos, donde cada una define una conexión a un origen de datos existente. A medida que los datos se envían a ese origen de datos, el trabajo de Stream Analytics los consume y los procesa en tiempo real como datos de streaming. Stream Analytics cuenta con integración de primera clase con [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) y [Azure Blob Storage](../storage/blobs/storage-dotnet-how-to-use-blobs.md) desde dentro y fuera de la suscripción del trabajo.

Este artículo es un paso de la [ruta de aprendizaje de Stream Analytics](/documentation/learning-paths/stream-analytics/).

## <a name="data-input-streaming-data-and-reference-data"></a>Entrada de datos: datos de streaming y datos de referencia
Hay dos tipos distintos de entradas en Stream Analytics: flujos de datos y datos de referencia.

* **Secuencias de datos**: los trabajos de Stream Analytics deben incluir, al menos, una entrada de secuencia de datos para que el trabajo la consuma y transforme. Azure Blob Storage y Azure Event Hubs se admiten como orígenes de entrada de flujos de datos. Azure Event Hubs se usan para recopilar transmisiones de eventos desde dispositivos conectados, servicios y aplicaciones. El almacenamiento de blobs de Azure puede usarse como origen de entrada para la ingesta de datos en masa como secuencia.  
* **Datos de referencia**: Stream Analytics admite un segundo tipo de entrada auxiliar denominada datos de referencia.  A diferencia de los datos en movimiento, estos datos son estáticos o están desacelerando los cambios.  Normalmente se usa para realizar búsquedas y correlaciones con secuencias de datos para crear un conjunto de datos más amplio.  Almacenamiento de blobs de Azure es el único origen de entrada admitido actualmente para los datos de referencia.  

Para agregar una entrada a su trabajo de Stream Analytics:

1. En Azure Portal, haga clic en **Entradas** y en **Agregar una entrada** en el trabajo de Stream Analytics.
   
    ![Azure Portal: agregar una entrada](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  
   
    En Azure Portal, haga clic en el icono **Entradas** en el trabajo de Stream Analytics.  
   
    ![Portal de Azure: agregar entrada.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  
2. Especifique el tipo de la entrada: **Flujo de datos** o **Datos de referencia**.
   
    ![Adición de la entrada correcta de datos, de streaming o de referencia](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  
   
    ![Adición de la entrada correcta de datos, de streaming o de referencia](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  
3. Si crea una entrada de Flujo de datos, especifique el tipo de origen para la entrada.  Este paso se omite durante la creación de Datos de referencia ya que, en este momento, solo se admite el almacenamiento de blobs.
   
    ![Adición de entrada de datos de flujo de datos](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  
   
    ![Adición de entrada de datos de flujo de datos](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  
4. Asigne un nombre descriptivo para esta entrada en el cuadro Alias de entrada.  Este nombre se usará en la consulta de su trabajo más adelante para hacer referencia a la entrada.
   
    Rellene el resto de las propiedades de conexión necesarias para conectarse a su origen de datos. Estos campos varían según el tipo de entrada y de origen y se definen en detalle [aquí](stream-analytics-create-a-job.md).  
   
    ![Adición de entrada de datos de Centro de eventos](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  
5. Especifique la configuración de serialización para los datos de entrada:
   
   * Para asegurarse de que las consultas funcionan según lo previsto, especifique el **Formato de serialización de eventos** de los datos entrantes.  Los formatos de serialización compatibles son JSON, CSV y Avro.
   * Compruebe el valor de **Codificación** para los datos.  Por el momento, UTF-8 es el único formato de codificación compatible.
     
     ![Configuración de la serialización de datos para la entrada de datos](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  
     
     ![Configuración de la serialización de datos para la entrada de datos](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  
6. Después de completar la creación de la entrada, el Stream Analytics comprobará que se puede conectar al origen de la entrada.  Puede ver el estado de la operación de prueba de conexión en el centro de notificaciones.
   
    ![Prueba de la conexión de la entrada de datos de streaming](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  
   
    ![Prueba de la conexión de la entrada de datos de streaming](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>Obtener ayuda con las entradas de datos de streaming
Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

