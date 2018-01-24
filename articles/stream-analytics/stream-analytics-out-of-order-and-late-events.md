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
ms.openlocfilehash: 71929b449f2a0fa55327fd3f9741208506859e85
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="azure-stream-analytics-event-order-considerations"></a>Puntos a tener en cuenta sobre el orden de eventos de Azure Stream Analytics

## <a name="arrival-time-and-application-time"></a>Hora de llegada y tiempo de aplicación

En un flujo de eventos de datos temporales, cada evento tiene asignada una marca de tiempo. Azure Stream Analytics asigna marcas de tiempo a cada evento según la hora de llegada o el tiempo de aplicación. La columna **System.Timestamp** contiene la marca de tiempo asignada al evento. 

La hora de llegada se asigna en el origen de entrada, cuando el evento llega al origen. Puede obtener acceso a la hora de llegada si usa la propiedad **EventEnqueuedTime** para el centro de eventos y la propiedad [BlobProperties.LastModified](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) para la entrada de blobs. 

El tiempo de aplicación se asigna cuando el evento se genera y forma parte de la carga. Para procesar eventos según el tiempo de aplicación, use la cláusula **Timestamp by** en la consulta de selección. Si la cláusula **Timestamp by** no está presente, los eventos se procesarán según la hora de llegada. 

Azure Stream Analytics genera resultados en el orden de las marcas de tiempo y proporciona varias opciones para tratar los datos desordenados.


## <a name="handling-of-multiple-streams"></a>Control de varios flujos

Un trabajo de Azure Stream Analytics combina eventos de varias escalas de tiempo en casos como los siguientes:

* Generación de una salida desde varias particiones. Las consultas que no tienen una cláusula explícita **Partition by PartitionId** tienen que combinar eventos de todas las particiones.
* Unión de dos o más orígenes de entrada diferentes.
* Unión a orígenes de entrada.

En escenarios donde se combinan varias escalas de tiempo, Azure Stream Analytics genera una salida para una marca de tiempo *t1* solo después de que todos los orígenes que se combinan lleguen al menos a la hora *t1*. Por ejemplo, supongamos que la consulta proviene de una partición de un centro de eventos que a su vez tiene dos particiones. Una de las particiones, *P1*, tiene eventos hasta la hora *t1*. La otra partición, *P2*, tiene eventos hasta la hora *t1 + x*. A continuación, se generan los resultados hasta la hora *t1*. Sin embargo, si existe una cláusula **Partition by PartitionId** explícita, ambas particiones progresan de manera independiente.

La configuración de tolerancia de llegada tardía se usa para hacer frente a la ausencia de datos en algunas particiones.

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>Configuración de tolerancia de llegada tardía y tolerancia de desorden
Los flujos de entrada que no están en orden:
* Se ordenan (y, por tanto, se retrasan).
* Los ajusta el sistema, según la directiva que especificó el usuario.

Stream Analytics admite eventos tardíos y desordenados cuando se procesan según el tiempo de aplicación. Los siguientes valores de configuración están disponibles en la opción **Ordenación de eventos** de Azure Portal: 

![Control de eventos de Stream Analytics](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

### <a name="late-arrival-tolerance"></a>Tolerancia de llegada tardía
La opción de tolerancia de llegada tardía solo es válida cuando el procesamiento se realiza según el tiempo de aplicación. En caso contrario, se omite la opción de configuración.

La tolerancia de llegada tardía es la diferencia máxima entre la hora de llegada y el tiempo de aplicación. Si un evento llega más tarde que la tolerancia de llegada tardía (por ejemplo, el tiempo de aplicación *app_t* < hora de llegada *arr_t* -tolerancia de la directiva de llegada tardía *late_t*), el evento se ajusta al máximo de la tolerancia de llegada tardía (*arr_t* - *late_t*). Esto es, el período de llegada tardía es el retraso máximo entre la generación de eventos y la recepción del evento en el origen de entrada. 

Cuando hay varias particiones del mismo flujo de entrada o se combinan varios flujos de entrada entre sí, la tolerancia de llegada tardía es la cantidad máxima de tiempo que cada partición dedica a esperar nuevos datos. 

El ajuste basado en la tolerancia de llegada tardía se produce en primer lugar. El ajuste basado en la tolerancia de desorden se produce a continuación. La columna **System.Timestamp** contiene la marca de tiempo final asignada al evento.

### <a name="out-of-order-tolerance"></a>Tolerancia de desorden
Los eventos que llegan desordenados, pero dentro del período de tolerancia de desorden establecido, se reordenan según la marca de tiempo. En cuanto a los eventos que llegan más tarde que el período de tolerancia, pueden tener el siguiente estado:
* **Ajustados**: se ajustan para parecer que han llegado a la última hora aceptable. 
* **Descartados**: los eventos descartados.

Cuando Stream Analytics reordena los eventos recibidos dentro del período de tolerancia de desorden, el resultado de la consulta es "delayed by out of order tolerance window" (retraso debido al período de tolerancia de desorden).

### <a name="example"></a>Ejemplo

* Tolerancia de llegada tardía = 10 minutos<br/>
* Tolerancia de desorden = 3 minutos<br/>
* Procesamiento por tiempo de aplicación<br/>
* Eventos:
   1. **Tiempo de aplicación** = 00:00:00, **Hora de llegada** = 00:10:01, **System.Timestamp** = 00:00:01, ajustado porque (**Hora de llegada - Tiempo de aplicación**) es mayor que la tolerancia de llegada tardía.
   2. **Tiempo de aplicación** = 00:00:01 **Hora de llegada** = 00:10:01, **System.Timestamp** = 00:00:01, no se ajusta porque el tiempo de aplicación está dentro del período de tolerancia de llegada tardía.
   3. **Tiempo de aplicación** = 00:10:00 **Hora de llegada** = 00:10:02, **System.Timestamp** = 00:10:00, no se ajusta porque el tiempo de aplicación está dentro del período de tolerancia de llegada tardía.
   4. **Tiempo de aplicación** = 00:09:00, **Hora de llegada** = 00:10:03 **System.Timestamp** = 00:09:00, se acepta con la marca de tiempo original, porque el tiempo de aplicación está dentro de la tolerancia de desorden.
   5. **Tiempo de aplicación** = 00:06:00, **Hora de llegada** = 00:10:04 **System.Timestamp** = 00:07:00, se ajusta porque el tiempo de aplicación es anterior a la tolerancia de desorden.

### <a name="practical-considerations"></a>Consideraciones prácticas
Como se ha mencionado anteriormente, la tolerancia de llegada tardía es la diferencia máxima entre la hora de llegada y el tiempo de aplicación. Asimismo, cuando el procesamiento se realiza por tiempo de aplicación, los eventos que son posteriores a la tolerancia de llegada tardía configurada, se ajustan antes de que se aplique el valor de tolerancia de desorden. Por lo tanto, el desorden efectivo es la cantidad mínima de tolerancia de llegada tardía y de tolerancia de desorden.

El desorden de los eventos dentro de un flujo se produce por alguna de estas razones:
* Distorsión del reloj entre remitentes.
* Latencia variable entre el remitente y el origen del evento de entrada.

La llegada tardía se produce debido a alguna de estas razones:
* Los remitentes procesan por lotes los eventos durante un intervalo posterior y los envían después del intervalo.
* La latencia entre el envío del evento por parte del remitente y la recepción del evento en el origen de entrada.

Al configurar la tolerancia de llegada tardía y la tolerancia de desorden para un trabajo concreto, es necesario tener en cuenta el grado de corrección, los requisitos de latencia y los factores mencionados anteriormente.

Estos son algunos ejemplos.

#### <a name="example-1"></a>Ejemplo 1 
La consulta tiene una cláusula **Partition by PartitionId**. Dentro de una sola partición, los eventos se envían a través de métodos de envío sincrónicos. Los métodos de envío sincrónicos se bloquean hasta que se envían los eventos.

En este caso, el desorden es cero porque los eventos se envían en orden con confirmación explícita antes de enviar el evento siguiente. La llegada tardía es el retraso máximo entre que se genera el evento y se envía, sumado a la latencia máxima entre el remitente y el origen de entrada.

#### <a name="example-2"></a>Ejemplo 2
La consulta tiene una cláusula **Partition by PartitionId**. Dentro de una sola partición, los eventos se envían a través de métodos de envío asincrónicos. Los métodos de envío asincrónicos pueden iniciar varios envíos a la vez, lo que puede provocar que los eventos se desordenen.

En este caso, el desorden y la llegada tardía son como mínimo el retraso máximo entre que se genera el evento y se envía, más la latencia máxima entre el remitente y el origen de entrada.

#### <a name="example-3"></a>Ejemplo 3
La consulta no tiene la cláusula **Partition by PartitionId** y hay al menos dos particiones.

La configuración es la misma que en el ejemplo 2. Sin embargo, la ausencia de datos en una de las particiones puede retrasar la salida en un período adicional de tolerancia de llegada tardía.

## <a name="handling-event-producers-with-differing-timelines"></a>Controlar productores de eventos con escalas de tiempo diferentes
Un flujo de eventos de entrada único a menudo contiene eventos que se originan en varios productores de eventos, como los dispositivos individuales. Estos eventos pueden llegar desordenados debido a las razones que se analizaron anteriormente. En estos casos, aunque el desorden de los productores de eventos puede ser grande, el desorden dentro de los eventos de un solo productor es pequeño o puede que no exista.

Azure Stream Analytics proporciona mecanismos generales para trabajar con eventos desordenados. Tales mecanismos devuelven retrasos de procesamiento (mientras se espera para que los eventos rezagados lleguen al sistema),eventos que se han quitado o ajustado o ambos casos.

Aún así, en muchos escenarios la consulta que se espera es el procesamiento independiente de eventos de diferentes productores de eventos. Por ejemplo, se podrían agregar eventos por período y dispositivo. En estos casos, no hay ninguna necesidad de retrasar la salida que corresponde a un productor de eventos mientras se espera a que los demás productores de eventos se pongan al día. En otras palabras, no hay ninguna necesidad de trabajar con el sesgo horario entre productores. Puede pasarla por alto.

Por supuesto, esto significa que los eventos de salida ya están desordenados de por sí con respecto a sus marcas de tiempo. El consumidor descendente debe ser capaz de trabajar con este comportamiento. De todos modos, todos los eventos del resultado son correctos.

Azure Stream Analytics implementa esta funcionalidad mediante la cláusula [TIMESTAMP BY OVER](https://msdn.microsoft.com/library/azure/mt573293.aspx).

## <a name="summary"></a>Resumen
* Configure el período de tolerancia de llegada tardía y de desorden en función de los requisitos de corrección y latencia. Asimismo, también debe tener en cuenta la manera de enviar los eventos.
* Se recomienda que la tolerancia de desorden sea más pequeña que la tolerancia de llegada tardía.
* Al combinar varias escalas de tiempo, la falta de datos en uno de los orígenes o particiones puede retrasar la salida debido a un período adicional de tolerancia de llegada tardía.

## <a name="get-help"></a>Obtención de ayuda
Para obtener más ayuda, pruebe el [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>pasos siguientes
* [¿Qué es Stream Analytics?](stream-analytics-introduction.md)
* [Introducción a Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalado de trabajos de Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia de lenguaje de consulta de Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
