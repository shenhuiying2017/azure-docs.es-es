---
title: "Introducción a las funciones de ventana de Stream Analytics | Microsoft Docs"
description: "Obtenga información sobre las tres funciones de ventana de Análisis de transmisiones (saltos de tamaño constante, salto y deslizante)."
keywords: "ventana de saltos de tamaño constante, ventana deslizante, ventana de salto"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 0d8d8717-5d23-43f0-b475-af078ab4627d
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 2a6559551f608cf435e89997392a6a0ba995c583
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-stream-analytics-window-functions"></a>Introducción a las funciones de ventana de Análisis de transmisiones
En numerosos escenarios de transmisión en tiempo real, es necesario realizar operaciones solamente en los datos contenidos en ventanas temporales. La compatibilidad nativa para las funciones de ventanas es una característica clave de Análisis de transmisiones de Azure que marca la diferencia en la productividad del desarrollador en la creación de trabajos de procesamiento de transmisiones complejos. Stream Analytics permite a los desarrolladores usar ventanas de [**saltos de tamaño constante**](https://msdn.microsoft.com/library/dn835055.aspx), [**salto**](https://msdn.microsoft.com/library/dn835041.aspx) y [**deslizantes**](https://msdn.microsoft.com/library/dn835051.aspx) para realizar operaciones temporales en datos de transmisión. Es importante destacar que todas las operaciones de [ventana](https://msdn.microsoft.com/library/dn835019.aspx) generan resultados al **final** de la ventana. La salida de la ventana será un solo evento basado en la función agregada que se usa. El evento tendrá la marca de tiempo del final de la ventana y todas las funciones de ventana están definidas con una longitud fija. Por último, es importante tener en cuenta que todas las funciones de ventana se deben usar en una cláusula [**GROUP BY**](https://msdn.microsoft.com/library/dn835023.aspx).

![Conceptos de las funciones de ventana de Análisis de transmisiones](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Ventana de saltos de tamaño constante
Las funciones de ventana de saltos de tamaño constante se usan para segmentar una transmisión de datos en segmentos de tiempo distintos y realizar una función con ellos, como en el ejemplo siguiente. Los diferenciadores clave de una ventana de saltos de tamaño constante son que se repiten, no se superponen y un evento no puede pertenecer a más de una ventana de saltos de tamaño constante.

![Introducción al salto de tamaño constante de las funciones de ventana de Análisis de transmisiones](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Ventana de salto
Las funciones de ventana de salto saltan hacia adelante en el tiempo un período fijo. Es fácil pensar en ellas como ventanas de salto que se pueden superponer, por lo que los eventos pueden pertenecer a más de un conjunto de resultados de ventana de salto. Para hacer que una ventana de salto sea igual que un una ventana de saltos de tamaño constante simplemente se debe especificar el tamaño de salto de forma que coincida con el tamaño de la ventana. 

![Introducción al salto de las funciones de ventana de Análisis de transmisiones](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Ventana deslizante
Las funciones de ventana deslizante, a diferencia de las ventanas de saltos de tamaño constante o de salto, producen una salida **solo** cuando se produce un evento. Todas las ventanas tendrán al menos un evento y la ventana aumenta continuamente un valor € (épsilon). Al igual que las ventanas de salto, los eventos pueden pertenecer a más de una ventana deslizante.

![Introducción al deslizamiento de las funciones de ventana de Análisis de transmisiones](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>Obtener ayuda con las funciones de ventana
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

