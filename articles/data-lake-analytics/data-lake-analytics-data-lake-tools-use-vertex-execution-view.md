---
title: "Use the Vertex Execution View in Data Lake Tools for Visual Studio (Usar la vista de ejecución de vértices de Data Lake Tools para Visual Studio) | Microsoft Docs"
description: "Obtenga información acerca de cómo usar la vista de la ejecución de vértices para examinar los trabajos de Data Lake Analytics."
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/13/2016
ms.author: jgao
ms.openlocfilehash: b788e7bc8ded86ebd49cc0be73e5b4e1bcbeaba3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Use the Vertex Execution View in Data Lake Tools for Visual Studio (Usar la vista de ejecución de vértices de Data Lake Tools para Visual Studio)
Obtenga información acerca de cómo usar la vista de la ejecución de vértices para examinar los trabajos de Data Lake Analytics.

## <a name="prerequisites"></a>Requisitos previos

Necesitará conocimientos básicos de uso de Data Lake Tools para Visual Studio para desarrollar el script U-SQL.  Vea [Tutorial: Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

## <a name="open-the-vertex-execution-view"></a>Abrir la vista de ejecución de vértices
Abra un trabajo de U-SQL en Data Lake Tools para Visual Studio. Haga clic en la **vista de ejecución de vértices** en la esquina inferior izquierda. Se le solicitará cargar primero los perfiles, lo que puede tardar algún tiempo según la conectividad de red.

![Vista de ejecución de vértices de Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Comprensión de la vista de ejecución de vértices
La vista de ejecución de vértices consta de tres partes:

![Vista de ejecución de vértices de Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

El **selector de vértices** a la izquierda permite seleccionar vértices por características (como las 10 lecturas de datos más importantes o elegir por fase). Uno de los filtros usados más comúnmente es el de los **vértices en la ruta de acceso crítica**. La **ruta de acceso crítica** es la cadena más larga de vértices de un trabajo de U-SQL. Saber cómo funciona resulta útil para la optimización de los trabajos al comprobar qué vértice tarda más tiempo.
  
![Vista de ejecución de vértices de Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

El panel superior central muestra el **estado de ejecución de todos los vértices**.
  
![Vista de ejecución de vértices de Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

El panel inferior central muestra información sobre cada vértice:
* Nombre del proceso: nombre de la instancia de vértice. Se compone de distintas partes de StageName|VertexName|VertexRunInstance. Por ejemplo, el vértice SV7_Split[62].v1 corresponde a la segunda instancia en ejecución (.v1, índice que comienza desde 0) del número de vértice 62 en Stage SV7_Split.
* Escritura/lectura de datos total: los datos leídos/escritos por este vértice.
* Estado de salida/estado: el estado final cuando finaliza el vértice.
* Código de salida/tipo d error: el error cuando produce un error en el vértice.
* Motivo de la creación: motivo por el que se creó el vértice.
* Latencia de cola PN/latencia de proceso/latencia de recurso: el tiempo que tarda el vértice en esperar a los recursos, procesar los datos y permanecer en la cola.
* GUID del creador/proceso: GUID del vértice de ejecución actual o su creador.
* Versión: la instancia N del vértice de ejecución (el sistema puede programar nuevas instancias de un vértice por diversos motivos, por ejemplo, conmutación por error, redundancia de proceso, etc.).
* Hora de creación de la versión
* Tiempo de inicio de creación de proceso/Tiempo en cola de proceso/Tiempo de inicio de proceso/Tiempo completo de proceso: cuando el proceso de vértice comienza la creación; cuando el proceso de vértice comienza a almacenarse en cola; cuando comienza determinado proceso de vértice; cuando un vértice concreto se completa.

## <a name="next-steps"></a>Pasos siguientes
* Para registrar información de diagnóstico, consulte [Accessing Diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Para ver una consulta más compleja, consulte la página sobre el [análisis de registros de sitio web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para ver los detalles del trabajo, vea [Usar el explorador de trabajos y la vista de trabajos para trabajos de Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md)
