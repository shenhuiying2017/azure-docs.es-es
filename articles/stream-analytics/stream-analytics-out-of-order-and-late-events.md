---
title: Control del orden y la tardanza de los eventos con Azure Stream Analytics | Microsoft Docs
description: "Aprenda cómo funciona Stream Analytics con eventos desordenados o atrasados en flujos de datos."
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
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 5089dda48ea829902663ef9d09fe83177df6f220
ms.contentlocale: es-es
ms.lasthandoff: 08/29/2017

---
# <a name="azure-stream-analytics-event-order-handling"></a>Control del orden de los eventos de Azure Stream Analytics

En un flujo de eventos de datos temporales, cada evento se registra con la hora a la que se recibió. Ciertas condiciones podrían provocar que las secuencias de eventos recibieran ocasionalmente algunos eventos en un orden diferente al que se enviaron. Una retransmisión TCP sencilla, o incluso un sesgo de reloj entre el dispositivo de envío y el centro de eventos receptor podría ser la causa de ello. Los eventos de "puntuación" también se agregan a las secuencias de eventos recibidos, para adelantar el tiempo en ausencia de llegadas de eventos. Estos son necesarios en escenarios como "Avisarme cuando no se produzca ningún inicio de sesión durante 3 minutos".

Los flujos de entrada que no están en orden:
* Se ordenan (y, por tanto, se **retrasan**).
* Los ajusta el sistema, según directiva especificada por el usuario.


## <a name="lateness-tolerance"></a>Tolerancia a la tardanza
Stream Analytics tolera estos tipos de escenarios. Stream Analytics tiene control de eventos "desordenados" y "atrasados". Para controlar estos eventos, actúa del modo siguiente:

* Los eventos que llegan desordenados, pero dentro de la tolerancia establecida, se **reordenan por marca de tiempo**.
* Los eventos que llegan más tarde que la tolerancia se **descartan o ajustan**.
    * **Ajustados**: se ajustan para parecer que han llegado a la última hora aceptable.
    * **Descartados**: los eventos descartados.

![Control de eventos de Stream Analytics](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

## <a name="reduce-the-number-of-out-of-order-events"></a>Reducción del número de eventos desordenados

Como Stream Analytics aplica una transformación temporal cuando procesa los eventos entrantes (por ejemplo, para agregados en ventana o uniones temporales), Stream Analytics ordena los eventos entrantes por orden de marca de tiempo.

Cuando la palabra clave "marca de tiempo por" **no** se usa, se emplea de forma predeterminada la hora de puesta en cola del evento de Azure Event Hubs. Event Hubs garantiza monotonicidad de la marca de tiempo en cada partición del centro de eventos. También garantiza que eventos de todas las particiones se combinan en orden de marca de tiempo. Estas dos garantías de Event Hubs aseguran que no hay eventos desordenados.

A veces, es importante usar la marca de tiempo del remitente. En ese caso, se elige una marca de tiempo de la carga de evento mediante "marca de tiempo por". En estos escenarios, se podrían introducir uno o más orígenes de desórdenes de eventos:

* Los productores de eventos tienen sesgos de reloj. Esto es común cuando los productores provienen de equipos diferentes, por lo que tienen relojes diferentes.
* Hay un retraso de red desde el origen de los eventos hasta el centro de eventos de destino.
* Existen sesgos de reloj entre particiones de centro de eventos. Stream Analytics ordena primero los eventos de todas las particiones del centro de eventos por tiempo de puesta en cola del evento. A continuación, examina el flujo de datos de los eventos desordenados.

En la pestaña Configuración, vera los siguientes valores predeterminados:

![Control de desorden de Stream Analytics](media/stream-analytics-event-handling/stream-analytics-out-of-order-handling.png)

Si usa 0 segundos como ventana de tolerancia al desorden, está afirmando que todos los eventos están en orden todo el tiempo. Dados los tres orígenes de eventos desordenados, no es probable que esto sea cierto. 

Para permitir que Stream Analytics corrija un desorden de eventos, puede especificar una ventana de tolerancia al desorden distinta de cero. Stream Analytics almacena en búfer los eventos hasta esa ventana y luego los reordena mediante la marca de tiempo que haya elegido. Luego aplica la transformación temporal. Puede comenzar con una ventana de tres segundos y ajustar el valor para reducir el número de eventos con ajuste de tiempo. 

Un efecto secundario del almacenamiento en búfer es que la salida se **retrasa la misma cantidad de tiempo**. Puede ajustar el valor para reducir el número de eventos desordenados y mantener la latencia del trabajo baja.

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [¿Qué es Stream Analytics?](stream-analytics-introduction.md)
* [Introducción a Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalado de trabajos de Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia de lenguaje de consulta de Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
