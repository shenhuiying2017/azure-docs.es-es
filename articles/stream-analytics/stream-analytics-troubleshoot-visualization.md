---
title: "Visualización y solución de problemas de trabajos de Stream Analytics | Microsoft Docs"
description: "Aprenda a visualizar una canalización de trabajo de Análisis de transmisiones para el autoservicio de solución de problemas mediante la característica de diagrama de diagnóstico."
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: d87841cd-c59f-4a46-b46e-8b904fdc12e9
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 820b73a5dbf9bb108e189313cf6ee2b924ab04c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Visualización y solución de problemas de trabajos de Análisis de transmisiones
En Análisis de transmisiones, al igual que con otras tecnologías basadas en la nube, en ocasiones se necesita la solución de problemas para profundizar en los motivos de que un trabajo no produzca el resultado esperado (o ningún resultado para ese propósito). Con este concepto en mente, Stream Analytics ofrece la funcionalidad de visualizar un trabajo en streaming. Esto también resulta útil como herramienta de modelado y tiene la ventaja adicional, para aquellos que lo necesitan, de documentar su trabajo.

En el panel de visualización se pueden ver las entradas, así como la consulta que se ejecuta y luego todas las salidas configuradas. Los problemas de conectividad o configuración se hacen más evidentes y también ayuda a tener una representación visual de su configuración.

## <a name="using-the-diagnosis-diagram-tool"></a>Uso de la herramienta de diagrama de diagnóstico
Para acceder a este visualizador, simplemente haga clic en el botón "Diagrama de diagnóstico" en el área "Configuración" del trabajo de Stream Analytics.

![stream-analytics-troubleshoot-visualization-diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Cada entrada y salida está codificada con colores para indicar el estado actual de cada componente, como se muestra a continuación.

![stream-analytics-troubleshoot-visualization-input-map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Cuando el usuario quiera examinar los pasos de consulta intermedios para comprender los patrones de flujo de datos dentro de un trabajo, la herramienta de visualización ofrece una vista desglosada de la consulta en los pasos que la componen y la secuencia de flujo. Al hacer clic en cada paso de consulta se muestra la sección correspondiente en un panel de edición de consultas, tal y como se indica. 

![stream-analytics-troubleshoot-visualization-intermediate-steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

