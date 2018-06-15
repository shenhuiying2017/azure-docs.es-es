---
title: Conceptos de punto de control y reproducción para la recuperación de trabajos en Azure Stream Analytics
description: En este artículo se describen los conceptos de punto de control y reproducción para la recuperación de trabajos en Azure Stream Analytics.
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: 1a7cb6c5d9c3383b127ce38ae21bb2dc811e1f2e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
ms.locfileid: "31529208"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Conceptos de punto de control y reproducción en trabajos de Azure Stream Analytics
En este artículo se describen los conceptos de punto de control y reproducción internos en Azure Stream Analytics y el impacto que tienen en la recuperación de trabajos. Cada vez que se ejecuta un trabajo de Stream Analytics, la información de estado se mantiene internamente. La información de estado se guarda en un punto de control de manera periódica. En algunos escenarios, la información de punto de control se usa para la recuperación del trabajo si se produce algún error o actualización del trabajo. En otros casos, no se puede usar el punto de control para la recuperación y es necesaria la reproducción.

## <a name="stateful-query-logic-in-temporal-elements"></a>Lógica de consulta con estado en elementos temporales
Una funcionalidad única de un trabajo de Azure Stream Analytics es realizar procesamiento con estado, como funciones de análisis temporal, combinaciones temporales y agregados en ventanas. Cada uno de estos operadores conserva información de estado cuando se ejecuta el trabajo. El tamaño máximo de la ventana para estos elementos de consulta es siete días. 

El concepto de ventana temporal aparece en varios elementos de consulta de Stream Analytics:
1. Agregados en ventanas (GROUP BY de ventanas de saltos de tamaño constante, de salto y deslizantes)

2. Combinaciones temporales (JOIN con DATEDIFF)

3. Funciones analíticas temporales (ISFIRST, LAST y LAG con LIMIT DURATION)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Recuperación de trabajos en caso de error de nodo, incluida la actualización del SO
Cada vez que se ejecuta un trabajo de Stream Analytics, se escala horizontalmente de manera interna para trabajar en varios nodos de trabajo. El estado de cada nodo de trabajo se comprueba cada pocos minutos, lo que ayuda a que el sistema se recupere si se produce algún error.

En algunas ocasiones, es posible que un nodo de trabajo determinado presente un error, o bien puede producirse una actualización de sistema operativo para ese nodo de trabajo. Para realizar automáticamente la recuperación, Stream Analytics adquiere un nodo nuevo en buen estado, y el estado del nodo de trabajo anterior se restaura desde el punto de control disponible más reciente. Para reanudar el trabajo, se necesita un poco de reproducción para restaurar el estado a partir del momento en que se crea el punto de control. Por lo general, la brecha en la restauración es de solo unos minutos. Cuando se seleccionan unidades de streaming suficientes para el trabajo, la reproducción debería completarse rápidamente. 

En una consulta totalmente paralela, el tiempo que tarda en actualizarse después de un error en el nodo de trabajo es proporcional a:

[la tasa de eventos de entrada] x [la duración de la brecha] / [el número de particiones de procesamiento]

Si alguna vez observa un retraso importante en el procesamiento debido a un error del nodo o a una actualización del sistema operativo, considere la posibilidad de hacer que la consulta sea totalmente paralela y escale el trabajo para asignar más unidades de streaming. Para más información, consulte [Escalado de un trabajo de Azure Stream Analytics para incrementar el rendimiento](stream-analytics-scale-jobs.md).

Actualmente, Stream Analytics no muestra ningún informe cuando se está realizando este tipo de proceso de recuperación.

## <a name="job-recovery-from-a-service-upgrade"></a>Recuperación de trabajos a partir de una actualización de servicio 
En algunas ocasiones, Microsoft actualiza los archivos binarios que ejecutan los trabajos de Stream Analytics en el servicio de Azure. En estas ocasiones, los trabajos en ejecución de los usuarios se actualizan a versiones nuevas y el trabajo se reinicia automáticamente. 

Actualmente, el formato del punto de control de recuperación no se conserva entre las actualizaciones. Como resultado, el estado de la consulta de streaming se debe restaurar completamente con la técnica de la reproducción. Para permitir que los trabajos de Stream Analytics reproduzcan exactamente la misma entrada de antes, resulta importante establecer la directiva de retención para los datos de origen en al menos los tamaños de intervalo de la consulta. Si no es así, se podrían generar resultados parciales o incorrectos durante la actualización del servicio, porque es posible que los datos de origen no vayan tan atrás como para incluir el tamaño de intervalo completo.

En general, la cantidad de reproducción que se necesita es proporcional al tamaño del intervalo multiplicado por la tasa de eventos promedio. Por ejemplo, para un trabajo con una tasa de entrada de 1000 eventos por segundo, un tamaño de intervalo superior a una hora se considera como un tamaño de reproducción grande. En el caso de consultas con un tamaño de reproducción grande, puede observar retrasos en la salida (sin salida) durante un período prolongado. 

## <a name="estimate-replay-catch-up-time"></a>Estimación del tiempo de puesta al día de reproducción
Para calcular la duración del retraso debido a una actualización de servicio, puede seguir esta técnica:

1. Cargue el centro de eventos de entrada con datos suficientes para abarcar el intervalo mayor en la consulta, con la tasa de eventos esperada. La marca de tiempo de los eventos debe aproximarse al tiempo de reloj durante todo ese período, como si se tratara de una fuente de entrada en vivo. Por ejemplo, si en la consulta tiene un intervalo de tres días, envíe eventos al centro de eventos durante tres días y siga enviando eventos. 

2. Empiece en trabajo con **Ahora** como la hora de inicio. 

3. Mida el tiempo que transcurre entre la hora de inicio y cuando se genera la primera salida. El tiempo es una aproximación del retraso en que podría incurrir en trabajo durante una actualización de servicio.

4. Si el retraso es demasiado, intente particionar el trabajo y aumentar la cantidad de unidades de streaming, para que la carga se distribuya en varios nodos. De manera alternativa, considere la posibilidad de disminuir los tamaños de intervalo en la consulta y realice una agregación adicional u otro procesamiento con estado en la salida que genera el trabajo de Stream Analytics en el receptor de nivel inferior (por ejemplo, con Azure SQL Database).

En caso de que surja un problema con la estabilidad del servicio durante la actualización de trabajos críticos, considere la posibilidad de ejecutar trabajos duplicados en regiones de Azure emparejadas. Para más información, consulte [Garantía de la confiabilidad del trabajo de Stream Analytics durante las actualizaciones del servicio](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Recuperación de trabajos a partir de la detención e inicio iniciados por el usuario
Para editar la sintaxis de la consulta en un trabajo de streaming, o bien para ajustar las entradas y salidas, es necesario detener el trabajo para hacer los cambios y actualizar el diseño del trabajo. En este tipo de escenarios, cuando un usuario detiene el trabajo de streaming y lo vuelve a iniciar, el escenario de recuperación es similar a la actualización de servicio. 

Los datos del punto de control no se pueden usar para el reinicio de un trabajo iniciado por el usuario. Para calcular el retraso de la salida durante este tipo de reinicio, use el mismo procedimiento que se describió en la sección anterior y aplique una mitigación similar si el retraso se prolonga demasiado.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre la confiabilidad y escalabilidad, consulte estos artículos:
- [Configuración de alertas en Azure Stream Analytics](stream-analytics-set-up-alerts.md)
- [Escalado de un trabajo de Azure Stream Analytics para incrementar el rendimiento](stream-analytics-scale-jobs.md)
- [Garantía de la confiabilidad del trabajo de Stream Analytics durante las actualizaciones del servicio](stream-analytics-job-reliability.md)
