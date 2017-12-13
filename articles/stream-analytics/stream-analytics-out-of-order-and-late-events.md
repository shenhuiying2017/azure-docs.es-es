---
title: Control del orden y la tardanza de los eventos con Azure Stream Analytics | Microsoft Docs
description: "Aprenda cómo funciona Stream Analytics con eventos desordenados o atrasados en flujos de datos."
keywords: desordenados, atrasados, eventos
documentationcenter: 
services: stream-analytics
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 208dfa14d5d18e106d654539cd80bafdeb90cdf8
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2017
---
# <a name="azure-stream-analytics-event-order-consideration"></a>Consideraciones sobre el orden de eventos de Azure Stream Analytics

## <a name="understand-arrival-time-and-application-time"></a>Es necesario comprender la hora de llegada y el tiempo de aplicación.

En un flujo de eventos de datos temporales, cada evento tiene asignada una marca de tiempo. Azure Stream Analytics asigna marcas de tiempo a cada evento según la hora de llegada o en el tiempo de aplicación. La columna "System.Timestamp" contiene la marca de tiempo asignada al evento. La hora de llegada se asigna en el origen de entrada, cuando el evento llega al origen. La hora de llegada es EventEnqueuedTime en la entrada de Event Hubs y [blob last modified time](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) en la entrada de Blob. El tiempo de aplicación se asigna cuando el evento se genera y forma parte de la carga. Para procesar eventos según el tiempo de aplicación, use la cláusula "Timestamp by" en la consulta de selección. Si la cláusula "Timestamp by" no está presente, los eventos se procesarán según la hora de llegada. Puede tener acceso a la hora de llegada con la propiedad EventEnqueuedTime en Event Hub y la propiedad BlobLastModified en el caso de la entrada de blob. Azure Stream Analytics genera resultados en el orden de las marcas de tiempo y proporciona algunas opciones para tratar los datos desordenados.


## <a name="azure-stream-analytics-handling-of-multiple-streams"></a>Administración de varias flujos de Azure Stream Analytics

Los trabajos de Azure Stream Analytics combinan eventos de varias escalas de tiempo en unos cuantos casos, como por ejemplo:

* Generación de una salida desde varias particiones. Las consultas que no tienen una cláusula explícita "Partition by PartitionId" tendrían que combinar eventos de todas las particiones.
* Unión de dos o más orígenes de entrada diferentes.
* Unión a orígenes de entrada.

En escenarios donde se combinan varias escalas de tiempo, Azure Stream Analytics genera una salida para una marca de tiempo *t1* solo después de que todos los orígenes que se combinan lleguen al menos a la hora *t1*.
Por ejemplo, si la consulta lee una partición de un *centro de eventos* que tiene dos particiones y una de ellas, *P1*, tiene eventos hasta la hora *t1* y la otra, *P2*, hasta la hora *t1 + x*, la salida se genera hasta la hora *t1*.
Sin embargo, si hubo una cláusula explícita *"Partition by PartitionId"*, ambas particiones progresan de manera independiente.
La configuración de tolerancia de llegada tardía se usa para hacer frente a la ausencia de datos en algunas particiones.

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>Configuración de tolerancia de llegada tardía y tolerancia de desorden
Los flujos de entrada que no están en orden:
* Se ordenan (y, por tanto, se **retrasan**).
* Los ajusta el sistema, según la directiva especificada por el usuario.

Stream Analytics admite eventos tardíos y desordenados cuando se procesan por **tiempo de aplicación**. Los siguientes valores de configuración están disponibles en la opción **Ordenación de eventos** de Azure Portal: 

![Control de eventos de Stream Analytics](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

**Tolerancia de llegada tardía**
* Esta configuración solo es válida cuando el procesamiento se realiza por tiempo de aplicación. Si no es así, se omite.
* Es la diferencia máxima entre la hora de llegada y el tiempo de aplicación. Si el tiempo de aplicación es anterior a (Hora de llegada - Período de tolerancia de llegada tardía), se establece en (Hora de llegada - Período de tolerancia de llegada tardía).
* Cuando hay varias particiones del mismo flujo de entrada o se combinan varios flujos de entrada entre sí, la tolerancia de llegada tardía es la cantidad máxima de tiempo que cada partición espera nuevos datos. 

En pocas palabras, el período de llegada tardía es el retraso máximo entre la generación de eventos y la recepción del evento en el origen de entrada.
Primero tiene lugar el ajuste basado en la tolerancia de llegada tardía y, luego, se abordan los elementos desordenados. La columna **System.Timestamp** contendrá la marca de tiempo final asignada al evento.

**Tolerancia de desorden**
* Los eventos que llegan desordenados, pero dentro del "período de tolerancia de desorden" establecido, se **reordenan por marca de tiempo**. 
* Los eventos que llegan más tarde que la tolerancia se **descartan o ajustan**.
    * **Ajustados**: se ajustan para parecer que han llegado a la última hora aceptable. 
    * **Descartados**: los eventos descartados.

Para reordenar los eventos recibidos dentro del "período de tolerancia de desorden", el resultado de la consulta es **delayed by out of order tolerance window**.

**Ejemplo**

* Tolerancia de llegada tardía = 10 minutos<br/>
* Tolerancia de desorden = 3 minutos<br/>
* Procesamiento por tiempo de aplicación<br/>
* Eventos:
   * Evento 1 _Tiempo de aplicación_ = 00:00:00, _Hora de llegada_ = 00:10:01, _System.Timestamp_ = 00:00:01, ajustado porque (_Hora de llegada_ - _Tiempo de aplicación_) es mayor que la tolerancia de llegada tardía.
   * Evento 2 _Tiempo de aplicación_ = 00:00:01 _Hora de llegada_ = 00:10:01, _System.Timestamp_ = 00:00:01, no se ajusta porque el tiempo de aplicación está dentro del período de tolerancia de llegada tardía.
   * Evento 3 _Tiempo de aplicación_ = 00:10:00 _Hora de llegada_ = 00:10:02, _System.Timestamp_ = 00:10:00, no se ajusta porque el tiempo de aplicación está dentro del período de tolerancia de llegada tardía.
   * Evento 4 _Tiempo de aplicación_ = 00:09:00, _Hora de llegada_ = 00:10:03 _System.Timestamp_ = 00:09:00, se acepta con la marca de tiempo original, porque el tiempo de aplicación está dentro de la tolerancia de desorden.
   * Evento 5 _Tiempo de aplicación_ = 00:06:00, _Hora de llegada_ = 00:10:04 _System.Timestamp_ = 00:07:00, se ajusta porque el tiempo de aplicación es anterior a la tolerancia de desorden.

## <a name="practical-considerations"></a>Consideraciones prácticas
Como se ha mencionado anteriormente, la *tolerancia de llegada tardía* es la diferencia máxima entre la hora de llegada y el tiempo de aplicación.
Asimismo, cuando el procesamiento se realiza por tiempo de aplicación, los eventos que son posteriores a la *tolerancia de llegada tardía* configurada se ajustan antes de que se aplique el valor de *tolerancia de desorden*. Por lo tanto, el desorden efectivo es la cantidad mínima de tolerancia de llegada tardía y de tolerancia de desorden.

El desorden de los eventos dentro de un flujo se produce por alguna de estas razones:
* Desplazamiento del reloj entre remitentes.
* Latencia variable entre el remitente y el origen del evento de entrada.

La llegada tardía se produce por algunas de estas razones:
* Los remitentes procesan por lotes los eventos durante un intervalo más tarde y los envían después del intervalo.
* La latencia entre el envío del evento por parte del remitente y la recepción del evento en el origen de entrada.

Al configurar la *tolerancia de llegada tardía* y la *tolerancia de desorden* para un trabajo concreto, es necesario tener en cuenta el grado de corrección, los requisitos de latencia y los factores mencionados anteriormente.

Estos son algunos ejemplos:

### <a name="example-1"></a>Ejemplo 1: 
La consulta tiene la cláusula "Partition by PartitionId" y, dentro de una única partición, los eventos se envían mediante métodos de envío sincrónicos. Los métodos de envío sincrónicos se bloquean hasta que se envían los eventos.
En este caso, el desorden es cero porque los eventos se envían en orden con confirmación explícita antes de enviar el evento siguiente. La llegada tardía es el retraso máximo entre que se genera el evento y se envía, sumado a la latencia máxima entre el remitente y el origen de entrada.

### <a name="example-2"></a>Ejemplo 2:
La consulta tiene la cláusula "Partition by PartitionId" y, dentro de una única partición, los eventos se envían mediante métodos de envío asincrónicos. Los métodos de envío asincrónicos pueden iniciar varios envíos a la vez, lo que puede provocar que los eventos se desordenen.
En este caso, el desorden y la llegada tardía son como mínimo el retraso máximo entre que se genera el evento y se envía, más la latencia máxima entre el remitente y el origen de entrada.

### <a name="example-3"></a>Ejemplo 3:
La consulta no tiene la cláusula "Partition by PartitionId" y hay al menos dos particiones.
La configuración es la misma que en el ejemplo 2. Sin embargo, la ausencia de datos en una de las particiones puede retrasar la salida en una ventana adicional de *tolerancia de llegada tardía".

## <a name="to-summarize"></a>Resumiendo:
* La ventana de tolerancia de llegada tardía y de desorden se debe configurar en función del grado de corrección, los requisitos de latencia y las consideraciones sobre cómo se envían los eventos.
* Se recomienda que la tolerancia de desorden sea más pequeña que la tolerancia de llegada tardía.
* Al combinar varias escalas de tiempo, la falta de datos en uno de los orígenes o particiones puede retrasar la salida en una ventana adicional de tolerancia de llegada tardía.

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [¿Qué es Stream Analytics?](stream-analytics-introduction.md)
* [Introducción a Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalado de trabajos de Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia de lenguaje de consulta de Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
