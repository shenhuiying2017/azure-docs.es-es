---
title: Control del orden y la tardanza de los eventos con Azure Stream Analytics | Microsoft Docs
description: "Obtenga información sobre cómo funciona Azure Stream Analytics con eventos fuera de servicio o atrasados en los flujos de datos"
keywords: desordenados, atrasados, eventos
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: cf9a43fbb82a32c92d66f25809916d3ccde1a20d
ms.contentlocale: es-es
ms.lasthandoff: 09/14/2017

---
# <a name="azure-stream-analytics-event-order-handling"></a>Control del orden de los eventos de Azure Stream Analytics

En un flujo de eventos de datos temporales, cada evento tiene asignada una marca de tiempo. Azure Stream Analytics asigna marcas de tiempo a cada evento basadas en la hora de llegada o en el tiempo de aplicación. 

La columna **System.Timestamp** contiene la marca de tiempo asignada al evento. La hora de llegada se asigna en el origen de entrada, cuando el evento llega al origen. La hora de llegada es **EventEnqueuedTime** en la entrada de Event Hubs y [blob last modified time](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) en la entrada de Blob. El tiempo de aplicación se asigna cuando el evento se genera y forma parte de la carga. 

Para procesar eventos según el tiempo de aplicación, use la cláusula TIMESTAMP BY en la consulta de selección. Si la cláusula TIMESTAMP BY no está presente, los eventos se procesarán según la hora de llegada. Puede tener acceso a la hora de llegada si usa la propiedad **EventEnqueuedTime** en Event Hubs y la propiedad **BlobLastModified** en el caso de la entrada de Blob. 

Azure Stream Analytics genera resultados en el orden de las marcas de tiempo y proporciona algunas opciones para tratar los datos desordenados.

![Control de eventos de Stream Analytics](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

Los flujos de entrada que no están en orden:
* Se ordenan (y, por tanto, se *retrasan*).
* Los ajusta el sistema, según la directiva especificada por el usuario.

Stream Analytics admite eventos tardíos y desordenados cuando se procesan por tiempo de aplicación.

**Tolerancia de llegada tardía**

* La opción de tolerancia de llegada tardía solo es válida cuando el procesamiento se realiza por tiempo de aplicación. De lo contrario, se omite.
* La tolerancia de llegada tardía es la diferencia máxima entre la hora de llegada y el tiempo de aplicación. Si el tiempo de aplicación es anterior a *(Hora de llegada - Período de tolerancia de llegada tardía)*, se establece en *(Hora de llegada - Período de tolerancia de llegada tardía)*.
* Cuando hay varias particiones del mismo flujo de entrada o se combinan varios flujos de entrada entre sí, la tolerancia de llegada tardía es la cantidad máxima de tiempo que cada partición espera nuevos datos. 

En pocas palabras, el período de llegada tardía es el retraso máximo entre la generación de eventos y la recepción del evento en el origen de entrada.
Primero tiene lugar el ajuste basado en la tolerancia de llegada tardía y, luego, se abordan los elementos desordenados. La columna **System.Timestamp** contiene la marca de tiempo final asignada al evento.

**Tolerancia de desorden**

* Los eventos que llegan desordenados, pero dentro del "período de tolerancia de desorden", se *reordenan por marca de tiempo*. 
* Los eventos que llegan más tarde que la tolerancia se *descartan* o *ajustan*.
    * **Ajustados**: se ajustan para parecer que han llegado a la última hora aceptable. 
    * **Descartados**: los eventos descartados.

Para reordenar los eventos recibidos dentro del "período de tolerancia de desorden", el resultado de la consulta es *delayed by out of order tolerance window*.

**Ejemplo**

Tolerancia de llegada tardía = 10 minutos<br/>
Tolerancia de desorden = 3 minutos<br/>
Procesamiento por tiempo de aplicación<br/>

Eventos:

Evento 1 _Tiempo de aplicación_ = 00:00:00, _Hora de llegada_ = 00:10:01, _System.Timestamp_ = 00:00:01, ajustado porque (_Hora de llegada_ - _Tiempo de aplicación_) es mayor que la tolerancia de llegada tardía.

Evento 2 _Tiempo de aplicación_ = 00:00:01 _Hora de llegada_ = 00:10:01, _System.Timestamp_ = 00:00:01, no se ajusta porque el tiempo de aplicación está dentro del período de tolerancia de llegada tardía.

Evento 3 _Tiempo de aplicación_ = 00:10:00, _Hora de llegada_ = 00:10:02 _System.Timestamp_ = 00:10:00, no se ajusta porque el tiempo de aplicación está dentro del período de tolerancia de llegada tardía.

Evento 4 _Tiempo de aplicación_ = 00:09:00, _Hora de llegada_ = 00:10:03 _System.Timestamp_ = 00:09:00, se acepta con la marca de tiempo original, porque el tiempo de aplicación está dentro de la tolerancia de desorden.

Evento 5 _Tiempo de aplicación_ = 00:06:00, _Hora de llegada_ = 00:10:04 _System.Timestamp_ = 00:07:00, se ajusta porque el tiempo de aplicación es anterior a la tolerancia de desorden.



## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

