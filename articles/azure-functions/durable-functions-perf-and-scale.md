---
title: 'Rendimiento y escalado horizontal en Durable Functions: Azure'
description: Introducción a la extensión Durable Functions de Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 18a3f3080c58e01117e0fc73adad76d28c298536
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Rendimiento y escalado horizontal en Durable Functions (Azure Functions)

Para optimizar el rendimiento y la escalabilidad, es importante comprender las características únicas de escalado de [Durable Functions](durable-functions-overview.md).

Para entender el comportamiento de escalado, tendrá que comprender algunos de los detalles del proveedor de Azure Storage subyacente.

## <a name="history-table"></a>Tabla del historial

La tabla **History** es una tabla de Azure Storage que contiene los eventos del historial de todas las instancias de orquestación dentro de una central de tareas. El nombre de esta tabla está en formato *TaskHubName*History. Según se ejecutan las instancias, se van agregando nuevas filas a esta tabla. La clave de partición de esta tabla proviene del identificador de instancia de la orquestación. El identificador de instancia es aleatorio en la mayoría de los casos, lo que garantiza una distribución óptima de las particiones internas en Azure Storage.

Cuando sea necesario ejecutar una instancia de orquestación, las filas correspondientes de la tabla History se cargan en memoria. Estos *eventos del historial* se reproducen luego en el código de función de orquestación para regresar a su estado de punto de control previo. El uso del historial de ejecución para reconstruir el estado de esta manera se ve influenciado por el [patrón Event Sourcing](https://docs.microsoft.com/en-us/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabla Instances

La tabla **Instances** es otra tabla de Azure Storage que contiene los estados de todas las instancias de orquestación dentro de una central de tareas. Según se crean las instancias, se van agregando nuevas filas a esta tabla. La clave de partición de esta tabla es el identificador de instancia de orquestación y la clave de fila es una constante fija. Hay una fila por cada instancia de orquestación.

Esta tabla se utiliza para satisfacer las solicitudes de consulta de instancia de [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) API, así como de [API HTTP de consulta de estado](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-http-api#get-instance-status). Se mantiene coherente en última instancia con el contenido de la tabla **History** mencionada anteriormente. El uso de una tabla de Azure Storage independiente para satisfacer con eficiencia operaciones de consulta de instancias de esta manera se ve influenciada por el [patrón Command and Query Responsibility Segregation (CQRS)](https://docs.microsoft.com/en-us/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Desencadenadores de cola interna

Las funciones de orquestador y de actividad se desencadenan mediante colas internas de la central de tareas de la aplicación de función. El uso de colas de este modo proporciona garantías de entrega confiable de mensajes "al menos una vez". Hay dos tipos de colas en Durable Functions: **de control** y **de elementos de trabajo**.

### <a name="the-work-item-queue"></a>Cola de elementos de trabajo

En Durable Functions hay una cola de elementos de trabajo por central de tareas. Se trata de una cola básica y se comporta del mismo modo que cualquier otra cola `queueTrigger` en Azure Functions. Esta cola se usa para desencadenar *funciones de actividad* sin estado al eliminar de la cola un único mensaje a la vez. Cada uno de estos mensajes contiene entradas de la función de actividad y metadatos adicionales, como qué función se debe ejecutar. Cuando una aplicación de Durable Functions admita el escalado horizontal de varias máquinas virtuales, estas compiten para adquirir el trabajo de la cola de elementos de trabajo.

### <a name="control-queues"></a>Colas de control

Hay varias *colas de control* por central de tareas en Durable Functions. Una *cola de control* es más sofisticada que una cola de elementos de trabajo, más sencilla. Las colas de control se utilizan para desencadenar las funciones de orquestador con estado. Dado que las instancias de las funciones de orquestador son las únicas con estado, no es posible utilizar un modelo de consumidor de competencia para distribuir la carga entre máquinas virtuales. En su lugar, se equilibra la carga de los mensajes del orquestador entre las colas de control. Encontrará más detalles sobre este comportamiento en las secciones siguientes.

Las colas de control contienen una gran variedad de tipos de mensajes del ciclo de vida de la orquestación. Algunos ejemplos son los [mensajes de control del orquestador](durable-functions-instance-management.md), los mensajes de *respuesta* de la función de actividad y los del temporizador. Como máximo, se pueden eliminar 32 mensajes de una cola de control en un único sondeo. Estos mensajes contienen datos de carga, así como metadatos, incluido para qué instancia de orquestación están previstos. Si hay varios mensajes eliminados de la cola previstos para la misma instancia de orquestación, se procesarán como lote.

## <a name="storage-account-selection"></a>Selección de una cuenta de almacenamiento

Las colas, tablas y blobs usados por Durable Functions se crean en una cuenta de Azure Storage. Se puede especificar la cuenta que se va a usar mediante el valor `durableTask/azureStorageConnectionStringName` del archivo **host.json**.

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
  }
}
```

Si no se especifica, se utiliza la cuenta de almacenamiento `AzureWebJobsStorage` predeterminada. Para las cargas de trabajo sensibles al rendimiento, se recomienda, sin embargo, configurar una cuenta de almacenamiento que no sea la predeterminada. Durable Functions utiliza Azure Storage de forma intensiva y el uso de una cuenta de almacenamiento dedicada aísla el uso del almacenamiento de Durable Functions del uso interno por parte del host de Azure Functions.

## <a name="orchestrator-scale-out"></a>Escalabilidad horizontal del orquestador

Las funciones de actividad no tienen estado y se escalan horizontalmente de manera automática con la incorporación de máquinas virtuales. En cambio, las funciones de orquestador,tienen *particiones* en una o más colas de control. El número de colas de control se define en el archivo **host.json**. El siguiente fragmento de código de host.json de ejemplo establece la propiedad `durableTask/partitionCount` en `3`.

```json
{
  "durableTask": {
    "partitionCount": 3
  }
}
```
Una central de tareas se puede configurar con entre 1 y 16 particiones. Si no se especifica, el número de participaciones predeterminado es **4**.

Mientras se escala horizontalmente a varias instancias de host de función (normalmente en máquinas virtuales diferentes), cada instancia adquiere un bloqueo en una de las colas de control. Estos bloqueos se implementan internamente como concesiones de almacenamiento de blobs y aseguran que solo se ejecuta una instancia de orquestación en una única instancia de host a la vez. Si una central de tareas está configurada con tres colas de control, se puede equilibrar la carga de las instancias de orquestación en tres máquinas virtuales como máximo. Se pueden agregar máquinas virtuales adicionales para aumentar la capacidad de ejecución de la función de actividad,

El siguiente diagrama ilustra cómo interactúa el host de Azure Functions con las entidades de almacenamiento en un entorno de escalado horizontal.

![Diagrama de escalado](media/durable-functions-perf-and-scale/scale-diagram.png)

Tal como se muestra en el diagrama anterior, todas las máquinas virtuales compiten por los mensajes en la cola de elementos de trabajo. Sin embargo, solo tres de ellas pueden adquirir mensajes de las colas de control y cada una bloquea una sola cola de control.

Las instancias de orquestación se distribuyen entre todas las instancias de la cola de control. La distribución se realiza aplicando un algoritmo hash al identificador de instancia de la orquestación. Los identificadores de instancia son, de forma predeterminada, GUID aleatorios, que aseguran que las instancias se distribuyen equitativamente entre todas las colas de control.

En general, las funciones de orquestador se han diseñado para que sean ligeras y no necesiten grandes capacidades de computación. Por lo tanto, no es necesario crear un gran número de particiones de cola de control para obtener un rendimiento excelente. La mayor parte del trabajo pesado se realiza en las funciones de actividad sin estado, que se pueden escalar en horizontal infinitamente.

## <a name="auto-scale"></a>Escalado automático

Al igual que todas las instancias de Azure Functions que se ejecutan en el plan de consumo, Durable Functions admite el escalado automático a través del [controlador de escalado de Azure Functions](functions-scale.md#runtime-scaling). El controlador de escalado supervisa la latencia de todas las colas emitiendo periódicamente comandos _peek_. En función de las latencias de los mensajes inspeccionados, el controlador de escalado decidirá si desea agregar o quitar máquinas virtuales.

Si el controlador de escalado determina que las latencias de mensaje de la cola de control son demasiado altas, agregará instancias de máquina virtual hasta que la latencia de mensajes se reduzca a un nivel aceptable o alcance el número de particiones de la cola de control. De igual forma, el controlador de escalado va a agregar continuamente instancias de máquina virtual si las latencias de la cola de elementos de trabajo son altas, independientemente del número de particiones.

## <a name="thread-usage"></a>Uso de subprocesos

Las funciones de orquestador se ejecutan en un solo subproceso para asegurarse de que la ejecución pueda ser determinista entre muchas de las reproducciones. Debido a esta ejecución en un único subproceso, es importante que los subprocesos de las funciones de orquestador no realicen tareas de uso intensivo de CPU, ni operaciones de E/S ni bloqueos por ningún motivo. Cualquier trabajo que requiera operaciones de E/S, bloqueos o varios subprocesos debe derivarse a las funciones de actividad.

Las funciones de actividad tienen el mismo comportamiento que las funciones normales desencadenadas por colas. Pueden realizar operaciones de E/S, ejecutar operaciones de uso intensivo de CPU y utilizar varios subprocesos con seguridad. Dado que los desencadenadores de actividad no tienen estado, se escalan horizontalmente a un número ilimitado de máquinas virtuales sin problema.

## <a name="concurrency-throttles"></a>Limitaciones de simultaneidad

Azure Functions admite la ejecución de varias funciones simultáneamente dentro de una instancia de aplicación única. Esta ejecución simultánea ayuda a aumentar el paralelismo y minimiza el número de "arranques en frío" que experimentará una aplicación típica a lo largo del tiempo. Sin embargo, una elevada simultaneidad puede dar lugar a un gran uso de memoria por máquina virtual. Según las necesidades de la aplicación de función, puede ser necesario limitar la simultaneidad por instancia para evitar la posibilidad de quedarse sin memoria en situaciones de carga alta.

Tanto el límite de simultaneidad de la función de actividad como de la función de orquestador se pueden configurar en el archivo **host.json**. La configuración pertinente es `durableTask/maxConcurrentActivityFunctions` y `durableTask/maxConcurrentOrchestratorFunctions` respectivamente.

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
  }
}
```

En el ejemplo anterior, se pueden ejecutar simultáneamente un máximo de 10 funciones de orquestador y 10 funciones de actividad en una única máquina virtual. Si no se especifica, el número de ejecuciones simultáneas de funciones de actividad y de orquestador está limitado a 10 veces el número de núcleos de la máquina virtual.

> [!NOTE]
> Esta configuración resulta útil para ayudar a administrar el uso de memoria y de CPU en una única máquina virtual. Sin embargo, al escalar horizontalmente a través de varias máquinas virtuales, cada máquina virtual tendrá su propio conjunto de límites. Esta configuración no se puede usar para controlar la simultaneidad a nivel global.

## <a name="orchestrator-function-replay"></a>Reproducción de una función de orquestador
Tal como se ha mencionado anteriormente, las funciones de orquestador se reproducen con el contenido de la tabla **History**. De forma predeterminada, el código de la función de orquestador se reproduce cada vez que se elimina un lote de mensajes de una cola de control.

Se puede deshabilitar este comportamiento agresivo de reproducción habilitando **sesiones extendidas**. Cuando se habilitan sesiones extendidas, las instancias de la función de orquestador se mantienen en la memoria más tiempo y se pueden procesar mensajes nuevos sin una reproducción completa. Las sesiones extendidas se habilitan al establecer `durableTask/extendedSessionsEnabled` en `true` en el archivo **host.json**. El valor `durableTask/extendedSessionIdleTimeoutInSeconds` se utiliza para controlar cuánto tiempo se retiene una sesión inactiva en la memoria:

```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

El efecto típico de habilitar sesiones extendidas es un número de operaciones de E/S reducido en lo que respecta a la cuenta de Azure Storage y un rendimiento general mejorado.

Sin embargo, uno de los posibles inconvenientes de esta característica es que las instancias de la función de orquestador inactivas permanecerán en la memoria más tiempo. Hay dos efectos que se deben tener en cuenta:

1. Aumento general del uso de memoria en la aplicación de función.
2. Reducción general del rendimiento si hay varias ejecuciones de la función de orquestador simultáneas y de corta duración.

Por ejemplo, si `durableTask/extendedSessionIdleTimeoutInSeconds` se establece en 30 segundos, un episodio de la función de orquestador de corta duración que se ejecuta en menos de 1 segundo seguirá aún ocupando memoria durante 30 segundos. También se descontará de la cuota `durableTask/maxConcurrentOrchestratorFunctions` que se ha mencionado antes, lo que impide potencialmente que se ejecuten otras funciones de orquestador.

> [!NOTE]
> Esta configuración solo se debe usar una vez que haya sido desarrollada y probada totalmente una función de orquestador. El comportamiento agresivo de reproducción predeterminado es útil para detectar errores de idempotencia en funciones de orquestador en el tiempo de desarrollo.

## <a name="performance-targets"></a>Destinos del rendimiento

Si prevé utilizar Durable Functions para una aplicación de producción, es importante tener en cuenta los requisitos de rendimiento en una fase temprana del proceso de planeamiento. En esta sección se describen algunos escenarios de uso básico y las cifras de rendimiento máximo que se esperan.

* **Ejecución de actividad secuencial**: este escenario describe una función de orquestador que ejecuta una serie de funciones de actividad una tras otra. Se parece en gran medida al ejemplo de [encadenamiento de funciones](durable-functions-sequence.md).
* **Ejecución de actividad en paralelo**: este escenario describe una función de orquestador que ejecuta numerosas funciones de actividad en paralelo mediante el patrón de [distribución ramificada de entrada/salida](durable-functions-cloud-backup.md).
* **Procesamiento de respuestas en paralelo**: este escenario es la segunda mitad del patrón de [distribución ramificada de entrada/salida](durable-functions-cloud-backup.md). Se centra en el rendimiento de la distribución ramificada de entrada. Es importante tener en cuenta que, a diferencia de la distribución ramificada de salida, la distribución ramificada de entrada se realiza mediante una única instancia de la función de orquestador y, por lo tanto, solo se puede ejecutar en una sola máquina virtual.
* **Procesamiento de eventos externos**: este escenario representa una única instancia de la función de orquestador que espera en [eventos externos](durable-functions-external-events.md), uno a uno.

> [!TIP]
> A diferencia de la distribución ramificada de salida, las operaciones de la distribución ramificada de entrada están limitadas a una sola máquina virtual. Si la aplicación utiliza el patrón de distribución ramificada de entrada/salida, y le preocupa el rendimiento de la distribución ramificada de entrada, considere la posibilidad de subdividir la distribución ramificada de salida de la función de actividad entre varias [suborquestaciones](durable-functions-sub-orchestrations.md).

En la tabla siguiente se muestran las cifras previstas del rendimiento *máximo* para los escenarios descritos anteriormente. "Instancia" hace referencia a una sola instancia de una función de orquestador que se ejecuta en una única y pequeña máquina virtual ([A1](../virtual-machines/windows/sizes-general.md#a-series)) de Azure App Service. En todos los casos, se supone que las [sesiones extendidas](#orchestrator-function-replay) están habilitadas. Los resultados reales pueden variar según el trabajo de CPU o de E/S realizado por el código de la función.

| Escenario | Rendimiento máximo |
|-|-|
| Ejecución de actividad secuencial | 5 actividades por segundo y por instancia |
| Ejecución de actividad en paralelo (distribución ramificada de salida) | 100 actividades por segundo y por instancia |
| Procesamiento de respuestas en paralelo (distribución ramificada de entrada) | 150 respuestas por segundo y por instancia |
| Procesamiento de eventos externos | 50 eventos por segundo y por instancia |

> [!NOTE]
> Estos números están actualizados a partir de la versión v1.4.0 (GA) de la extensión de Durable Functions. Estos números pueden cambiar a lo largo del tiempo, ya que la característica evoluciona y se realizan optimizaciones.

Si no ve las cifras de rendimiento que espera, y el uso de CPU y de memoria parece correcto, compruebe si la causa está relacionada con el [mantenimiento de la cuenta de almacenamiento](../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). La extensión de Durable Functions puede colocar una carga importante en una cuenta de Azure Storage y unas cargas suficientemente altas pueden provocar limitaciones en la cuenta de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Instalación de la extensión Durable Functions y ejemplos](durable-functions-install.md)
