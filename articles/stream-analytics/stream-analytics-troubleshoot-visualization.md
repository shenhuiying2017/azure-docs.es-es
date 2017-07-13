---
title: "Visualización y solución de problemas de trabajos de Stream Analytics | Microsoft Docs"
description: "Aprenda a visualizar una canalización de trabajo de Análisis de transmisiones para el autoservicio de solución de problemas mediante la característica de diagrama de diagnóstico."
keywords: 
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: d87841cd-c59f-4a46-b46e-8b904fdc12e9
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 18c39a025f750cf5a17c535ab40923b7cafe413d
ms.contentlocale: es-es
ms.lasthandoff: 07/04/2017


---
<a id="visualize-and-troubleshoot-stream-analytics-jobs" class="xliff"></a>

# Visualización y solución de problemas de trabajos de Análisis de transmisiones
En Análisis de transmisiones, al igual que con otras tecnologías basadas en la nube, en ocasiones se necesita la solución de problemas para profundizar en los motivos de que un trabajo no produzca el resultado esperado (o ningún resultado para ese propósito). A este respecto, Análisis de transmisiones ofrece la posibilidad de visualizar un trabajo en streaming. Esto también resulta útil como herramienta de modelado y tiene la ventaja adicional, para aquellos que lo necesitan, de documentar su trabajo.

En el panel de visualización se pueden ver las entradas, así como la consulta que se ejecuta y luego todas las salidas configuradas. Los problemas de conectividad o configuración se hacen más evidentes y también ayuda a tener una representación visual de su configuración.

<a id="using-the-diagnosis-diagram-tool" class="xliff"></a>

## Uso de la herramienta de diagrama de diagnóstico
Para tener acceso a este visualizador, simplemente haga clic en el botón "Diagrama de diagnóstico" en la hoja "Configuración" del trabajo de Análisis de transmisiones.

![stream-analytics-troubleshoot-visualization-diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Cada entrada y salida está codificada con colores para indicar el estado actual de cada componente, como se muestra a continuación.

![stream-analytics-troubleshoot-visualization-input-map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Cuando el usuario quiera examinar los pasos de consulta intermedios para comprender los patrones de flujo de datos dentro de un trabajo, la herramienta de visualización ofrece una vista desglosada de la consulta en los pasos que la componen y la secuencia de flujo. Al hacer clic en cada paso de consulta se muestra la sección correspondiente en un panel de edición de consultas, tal y como se ilustra. 

![stream-analytics-troubleshoot-visualization-intermediate-steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)

<a id="next-steps" class="xliff"></a>

## Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


