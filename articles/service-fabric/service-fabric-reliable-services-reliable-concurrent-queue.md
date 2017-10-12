---
title: ReliableConcurrentQueue en Azure Service Fabric
description: ReliableConcurrentQueue es una cola de alto rendimiento que permite puestas en cola y eliminaciones de la cola paralelas.
services: service-fabric
documentationcenter: .net
author: sangarg
manager: timlt
editor: raja,tyadam,masnider,vturecek
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: sangarg
ms.openlocfilehash: 122cb48149477f295a65b8ee623c647b6db10a86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Introducción a ReliableConcurrentQueue en Azure Service Fabric
La cola simultánea confiable es una cola asincrónica, transaccional y replicada que presenta una alta simultaneidad para las operaciones de puesta en cola y eliminación de la cola. Está diseñada para ofrecer un alto rendimiento y una baja latencia al relajar la ordenación FIFO estricta que proporciona la [cola confiable](https://msdn.microsoft.com/library/azure/dn971527.aspx) y, en su lugar, proporciona la ordenación de mejor esfuerzo.

## <a name="apis"></a>API existentes

|Cola simultánea                |Cola simultánea confiable                                         |
|--------------------------------|------------------------------------------------------------------|
| void Enqueue(T item)           | Task EnqueueAsync(ITransaction tx, T item)                       |
| bool TryDequeue(out T result)  | Task< ConditionalValue < T > > TryDequeueAsync(ITransaction tx)  |
| int Count()                    | long Count()                                                     |

## <a name="comparison-with-reliable-queuehttpsmsdnmicrosoftcomlibraryazuredn971527aspx"></a>Comparación con la [cola confiable](https://msdn.microsoft.com/library/azure/dn971527.aspx)

La cola simultánea confiable se ofrece como una alternativa a la [cola confiable](https://msdn.microsoft.com/library/azure/dn971527.aspx). Debe usarse en aquellos casos en que no se requiere la ordenación FIFO estricta, ya que para garantizar FIFO se requiere un compromiso con la simultaneidad.  [La cola confiable](https://msdn.microsoft.com/library/azure/dn971527.aspx) usa bloqueos para aplicar la ordenación FIFO, y como máximo permite poner en cola una transacción y quitar de cola una transacción a la vez. En comparación, la cola simultánea confiable relaja la restricción de ordenación y permite que cualquier número de transacciones simultáneas intercalen las operaciones de puesta en cola y eliminación de la cola. Aunque se ofrece la ordenación de mejor esfuerzo, la ordenación relativa de dos valores nunca se puede garantizar en una cola simultánea confiable.

La cola simultánea confiable proporciona un rendimiento mayor y menor latencia que la [cola confiable](https://msdn.microsoft.com/library/azure/dn971527.aspx) siempre que existen varias transacciones simultáneas que realizan puestas en cola y/o eliminaciones de la cola.

Un ejemplo de caso de uso de ReliableConcurrentQueue es el escenario de la [cola de mensajes](https://en.wikipedia.org/wiki/Message_queue). En este escenario, uno o más productores de mensajes crean elementos y los agregan a la cola, y uno o más consumidores de mensajes extraen mensajes de la cola y los procesan. Varios productores y consumidores pueden trabajar de manera independiente usando transacciones simultáneas para procesar la cola.

## <a name="usage-guidelines"></a>Directrices de uso
* La cola espera que los elementos de la cola tengan un período de retención baja. Es decir, los elementos no permanecen en la cola durante mucho tiempo.
* La cola no garantiza la ordenación FIFO estricta.
* La cola no lee sus propias escrituras. Si un elemento se pone en cola dentro de una transacción, no será visible para un operador de eliminación de la cola dentro de la misma transacción.
* Las eliminaciones de la cola no están aisladas entre sí. Si el elemento *A* se quita de la cola en la transacción *txnA*, aunque la transacción *txnA* no esté confirmada, el elemento *A* no será visible en una transacción simultánea *txnB*.  Si *txnA* se anula, *A* pasará a ser visible para *txnB* inmediatamente.
* Para implementar el comportamiento de *TryPeekAsync*, se puede usar un método *TryDequeueAsync* y, a continuación, anular la transacción. Un ejemplo de esto se puede encontrar en la sección Modelos de programación.
* El recuento es no transaccional. Se puede usar para hacerse una idea del número de elementos en la cola, pero representa un punto en el tiempo y no es confiable.
* No puede debe realizar un procesamiento costoso en los elementos quitados de la cola mientras la transacción esté activa, a fin de evitar transacciones de ejecución larga que podrían afectar al rendimiento del sistema.

## <a name="code-snippets"></a>Fragmentos de código
Echemos un vistazo a algunos fragmentos de código y a sus resultados esperados. El control de excepciones se omite en esta sección.

### <a name="enqueueasync"></a>EnqueueAsync
A continuación, se muestran algunos fragmentos de código para usar EnqueueAsync, seguido de los resultados previstos.

- *Caso 1: Tarea de puesta en cola única*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Supongamos que la tarea se completó correctamente y que no hubo ninguna transacción simultánea que modificara la cola. El usuario puede esperar que la cola contenga los elementos en cualquiera de los órdenes siguientes:

> 10, 20

> 20, 10


- *Caso 2: Tarea de puesta en cola paralela*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

Supongamos que las tareas se completaron correctamente, que se ejecutaron en paralelo y que no hubo ninguna otra transacción simultánea que modificara la cola. No se puede realizar ninguna inferencia sobre el orden de los elementos de la cola. Para este fragmento de código, los elementos pueden aparecer en cualquiera de los 4 órdenes posibles.  La cola intentará mantener los elementos en el orden original (en cola), pero es posible que deba reordenarlos debido a las operaciones simultáneas o errores.


### <a name="dequeueasync"></a>DequeueAsync
A continuación, se muestran algunos fragmentos de código para usar TryDequeueAsync, seguido de los resultados previstos. Supongamos que la cola ya se rellenó con los siguientes elementos:
> 10, 20, 30, 40, 50, 60

- *Caso 1: Tarea de quitar de la cola única*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Supongamos que la tarea se completó correctamente y que no hubo ninguna transacción simultánea que modificara la cola. Puesto que no puede realizarse ninguna inferencia acerca del orden de los elementos de la cola, cualquiera de los tres elementos se puede quitar de la cola, en cualquier orden. La cola intentará mantener los elementos en el orden original (en cola), pero es posible que deba reordenarlos debido a las operaciones simultáneas o errores.  

- *Caso 2: Tarea de eliminación de la cola paralela*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

Supongamos que las tareas se completaron correctamente, que se ejecutaron en paralelo y que no hubo ninguna otra transacción simultánea que modificara la cola. Dado que no se puede realizar ninguna inferencia sobre el orden de los elementos de la cola, las listas *dequeue1* y *dequeue2* contendrán cada una dos elementos cualesquiera, en cualquier orden.

El mismo elemento *no* aparecerá en ambas listas. Por lo tanto, si dequeue1 tiene *10*, *30*, dequeue2 tendrá *20*, *40*.

- *Caso 3: Ordenación de eliminación de la cola con anulación de transacción*

Al anular una transacción con eliminaciones de la cola en marcha, se vuelven a colocar los elementos al principio de la cola. No se garantiza el orden en el que los elementos se vuelven a colocar al principio de la cola. Veamos el código siguiente:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Supongamos que los elementos se quitaron de la cola en el orden siguiente:
> 10, 20

Al anular la transacción, los elementos deberían volver a agregarse al principio de la cola en cualquiera de los órdenes siguientes:
> 10, 20

> 20, 10

Lo mismo puede decirse en todos los casos en que la transacción no se *confirmó* correctamente.

## <a name="programming-patterns"></a>Modelos de programación
En esta sección, vamos a echar un vistazo a algunos modelos de programación que podrían resultar útiles para usar ReliableConcurrentQueue.

### <a name="batch-dequeues"></a>Eliminaciones de la cola por lotes
Un modelo de programación recomendado para la tarea de consumidor es realizar eliminaciones de la cola por lotes en lugar de realizarlas de una en una. El usuario puede elegir limitar los retrasos entre los lotes o el tamaño del lote. El siguiente fragmento de código muestra este modelo de programación.  Tenga en cuenta que, en este ejemplo, el procesamiento se realiza tras confirmarse la transacción. Por tanto, si se produce un error durante el procesamiento, los elementos no procesados se perderán sin procesarse.  Como alternativa, el procesamiento puede realizarse en el ámbito de la transacción, aunque esto puede tener un impacto negativo en el rendimiento y requiere que la administración de los elementos ya se haya procesado.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>Procesamiento basado en notificaciones de mejor esfuerzo
Otro modelo de programación interesante usa Count API. En este caso, podemos implementar el procesamiento basado en notificaciones de mejor esfuerzo para la cola. El recuento de cola puede usarse para limitar una tarea de puesta en cola y eliminación de la cola.  Tenga en cuenta que, como se muestra en el ejemplo anterior, puesto que el procesamiento se produce fuera de la transacción, los elementos no procesados pueden perderse si se produce un error durante el procesamiento.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>Descarga de mejor esfuerzo
No se puede garantizar una descarga de la cola debido a la naturaleza simultánea de la estructura de datos.  Es posible que, aún sin haber operaciones de usuario en marcha en la cola, una llamada concreta a TryDequeueAsync no pueda devolver un elemento que se puso en cola y confirmó anteriormente.  Se garantiza que el elemento en cola *finalmente* será visible para quitar de la cola. Sin embargo, sin un mecanismo de comunicación fuera de banda, un consumidor independiente no puede saber que la cola alcanzó un estado estable aunque todos los productores estén detenidos y no se permita ninguna nueva operación de puesta en cola. Por lo tanto, la operación de descarga es de mejor esfuerzo, como se muestra en la siguiente implementación.

El usuario debe detener toda la demás tareas de productor y consumidor, y esperar a que las transacciones en curso se confirmen o anulen, antes de intentar descargar la cola.  Si el usuario conoce el número previsto de elementos en la cola, puede configurar una notificación que indique que todos los elementos se quitaron de la cola.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>Peek
ReliableConcurrentQueue no proporciona la API de *TryPeekAsync*. Los usuarios pueden obtener la semántica de peek con un método *TryDequeueAsync* y, a continuación, anular la transacción. En este ejemplo, las eliminaciones de la cola solo se procesan si el valor del elemento es mayor que *10*.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>Lecturas obligatorias
* [Inicio rápido de Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Trabajo con Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Notificaciones de Reliable Services](service-fabric-reliable-services-notifications.md)
* [Copia de seguridad y restauración de Reliable Services (recuperación ante desastres)](service-fabric-reliable-services-backup-restore.md)
* [Configuración del administrador de estado confiable](service-fabric-reliable-services-configuration.md)
* [Introducción a los servicios de la API web de Microsoft Azure Service Fabric](service-fabric-reliable-services-communication-webapi.md)
* [Uso avanzado del modelo de programación de Reliable Services](service-fabric-reliable-services-advanced-usage.md)
* [Referencia para desarrolladores de colecciones confiables](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
