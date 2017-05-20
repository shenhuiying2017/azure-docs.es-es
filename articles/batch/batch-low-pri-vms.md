---
title: "Ejecución de cargas de trabajo de Azure Batch en máquinas virtuales de prioridad baja rentables (versión preliminar) | Microsoft Docs"
description: "Aprenda a aprovisionar máquinas virtuales de prioridad baja para reducir el costo de las cargas de trabajo de Azure Batch."
services: batch
author: mscurrell
manager: timlt
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 05/05/2017
ms.author: markscu
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 69e26eaf4aedaf17b58091420430ca996c01cf69
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---

# <a name="use-low-priority-vms-with-batch-preview"></a>Uso de máquinas virtuales de prioridad baja con Batch (versión preliminar)

Azure Batch ofrece máquinas virtuales (VM) de prioridad baja para reducir el costo de las cargas de trabajo de Batch. Las máquinas virtuales de prioridad baja posibilitan nuevos tipos de cargas de trabajo de Batch al proporcionar elevada capacidad de proceso que también resulta económica.

Las máquinas virtuales de prioridad baja aprovechan la capacidad sobrante en Azure. Al especificar máquinas virtuales de prioridad baja en sus grupos, Azure Batch puede usar automáticamente este excedente cuando esté disponible.

La ventaja en el uso de máquinas virtuales de prioridad baja es que esas máquinas virtuales se pueden reemplazar cuando no se encuentre disponible ninguna capacidad sobrante en Azure. Por este motivo, estas máquinas son las más adecuadas para determinados tipos de cargas de trabajo. Use máquinas virtuales de prioridad baja para cargas de trabajo de procesamiento por lotes y asincrónicas en las que el tiempo de finalización del trabajo sea flexible y el trabajo se distribuya entre muchas máquinas virtuales.

Las máquinas virtuales de prioridad baja son significativamente menos caras que las máquinas virtuales dedicadas. Para más información sobre precios, consulte [Precios de Batch](https://azure.microsoft.com/pricing/details/batch/).

> [!IMPORTANT]
> Las máquinas virtuales de prioridad baja se encuentran actualmente en versión preliminar y solo están disponibles para cargas de trabajo que se ejecutan en Batch. 
>
>

## <a name="use-cases-for-low-priority-vms"></a>Casos de uso de máquinas virtuales de prioridad baja

Teniendo en cuenta las características de las máquinas virtuales de prioridad baja, ¿con qué cargas de trabajo se pueden y no pueden usar? En general, las cargas de trabajo de procesamiento por lotes son una buena opción, ya que los trabajos se dividen en muchas tareas paralelas o hay muchos trabajos que se escalan horizontalmente y que se distribuyen en muchas máquinas virtuales.

-   Para maximizar el uso de la capacidad sobrante en Azure, los trabajos adecuados se pueden escalar horizontalmente.

-   Puede que, en ocasiones, las máquinas virtuales no estén disponibles o se reemplacen; como resultado, la capacidad para los trabajos puede verse mermada y conducir a interrupciones de las tareas o la necesidad de ejecutarlas una y otra vez. Por tanto, los trabajos deben ser flexibles en el tiempo que pueden tardar en ejecutarse.

-   Los trabajos con tareas más largas pueden resultar más afectados si se interrumpen. Si las tareas de larga ejecución implementan puntos de comprobación para guardar el progreso mientras se ejecutan, entonces el impacto de la interrupción será bastante menor. Las tareas con tiempos de ejecución más cortos tienden a funcionar mejor con máquinas virtuales de prioridad baja, ya que el impacto de la interrupción es mucho menor.

-   Los trabajos de MPI de larga ejecución que usan varias máquinas virtuales no son adecuados para usar máquinas virtuales de prioridad baja, ya que una máquina virtual reemplazada probablemente ocasionará que todo el trabajo tenga que ejecutarse de nuevo.

Algunos ejemplos de casos de uso de procesamiento por lotes adecuados para usar máquinas virtuales de prioridad baja son:

-   **Desarrollo y pruebas**: en concreto, si se están desarrollando soluciones a gran escala se pueden obtener ahorros significativos. Aunque todos los tipos de pruebas pueden beneficiarse, será especialmente ventajoso para pruebas de carga y regresión a gran escala.

-   **Capacidad a petición complementaria** : las máquinas virtuales de prioridad baja pueden usarse para complementar las máquinas virtuales dedicadas normales; cuando están disponibles, los trabajos pueden escalarse y, por lo tanto, completarse más rápido con un costo menor; cuando no están disponibles, se puede contar con la línea base de máquinas virtuales dedicadas.

-   **Tiempo de ejecución flexible de los trabajos**: si hay flexibilidad en el tiempo en que tienen que completarse los trabajos, entonces se pueden tolerar posibles bajadas en la capacidad; sin embargo, con la adición de máquinas virtuales de prioridad baja, los trabajos se ejecutarán con frecuencia más rápido y con un costo menor.

Los grupos de Batch se pueden configurar para usar máquinas virtuales de prioridad baja de varias maneras, dependiendo de la flexibilidad en el tiempo de ejecución de los trabajos:

-   Las máquinas virtuales de prioridad baja solo se pueden usar en un grupo; Batch simplemente recuperará cualquier capacidad reemplazada cuando esté disponible. Esta es la manera más barata de ejecutar trabajos, ya que solo se usan máquinas virtuales de prioridad baja.

-   Las máquinas virtuales de prioridad baja se pueden usar conjuntamente con una línea de base fija de máquinas virtuales dedicadas. El número fijo de máquinas virtuales dedicadas garantiza que siempre haya algo de capacidad para mantener el progreso de un trabajo.

-   Puede haber una combinación dinámica de máquinas virtuales dedicadas y de prioridad baja, de modo que las máquinas virtuales de prioridad baja más baratas se usen únicamente cuando estén disponibles, pero las máquinas virtuales dedicadas de precio completo se escalen verticalmente cuando sea necesario, a fin de mantener una cantidad mínima de capacidad disponible para mantener el progreso de los trabajos.

## <a name="batch-support-for-low-priority-vms"></a>Compatibilidad de Batch con máquinas virtuales de prioridad baja

Azure Batch ofrece varias funcionalidades que facilitan el consumo y que se benefician de las máquinas virtuales de prioridad baja:

-   Los grupos de Batch pueden contener tanto máquinas virtuales dedicadas como máquinas virtuales de prioridad baja. El número de cada tipo de máquina virtual se puede especificar cuando se crea un grupo, o puede cambiarse en cualquier momento para un grupo ya existente, mediante la operación explícita de cambio de tamaño o el escalado automático. El envío de trabajos y tareas puede permanecer sin cambios y no necesita preocuparse por los tipos de máquina virtual en el grupo. También se puede tener un grupo donde se usen completamente máquinas virtuales de prioridad baja para ejecutar trabajos lo más barato posible, pero volver a máquinas virtuales dedicadas si la capacidad desciende por debajo de un umbral mínimo, a fin de mantener los trabajos en funcionamiento.

-   Los grupos de Batch buscan automáticamente el número objetivo de máquinas virtuales de prioridad baja. Si las máquinas virtuales se reemplazan, entonces Batch intenta reemplazar la capacidad perdida y volver al objetivo.

-   En el caso de que se interrumpan las tareas, Batch lo detectará y las pondrá automáticamente en cola para que se ejecuten de nuevo.

> [!NOTE]
> Las máquinas virtuales de prioridad baja no se admiten actualmente para cuentas de Batch donde se establece el modo de asignación de grupo en [Suscripción del usuario](batch-account-create-portal.md#user-subscription-mode).
>
>

## <a name="create-and-update-pools"></a>Creación y actualización de grupos

Un grupo de Batch puede contener tanto máquinas virtuales dedicadas como de prioridad baja (también conocidas como nodos de proceso). Puede establecer el número objetivo de nodos de proceso tanto para las máquinas virtuales dedicadas como para las de prioridad baja. El número de nodos objetivo especifica el número de máquinas virtuales que quiere tener en el grupo.

Por ejemplo, para crear un grupo mediante máquinas virtuales de servicio en la nube de Azure con un objetivo de 5 máquinas virtuales dedicadas y 20 máquinas virtuales de prioridad baja:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4") // WS 2012 R2
);
```

Para crear un grupo mediante máquinas virtuales de Azure (en este caso, máquinas virtuales Linux) con un objetivo de 5 máquinas virtuales dedicadas y 20 máquinas virtuales de prioridad baja:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04.0-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard\_D2\_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

Puede obtener el número actual de nodos tanto para las máquinas virtuales dedicadas como para las de prioridad baja:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Los nodos de grupo tienen una propiedad que indica si el nodo es una máquina virtual dedicada o de prioridad baja:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Cuando se reemplazan uno o varios nodos de un grupo, la operación de enumeración de nodos seguirá devolviendo esos nodos, el número actual de nodos de prioridad baja permanecerá sin cambios, pero esos nodos tendrán su estado establecido como **reemplazado**. Batch intentará encontrar máquinas virtuales de reemplazo y, si tiene éxito, los nodos pasarán a los estados **Creando** y luego **Iniciando** antes de estar disponibles para la ejecución de tareas, igual que los nuevos nodos.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Escalado de un grupo que contiene máquinas virtuales de prioridad baja

Al igual que sucede con grupos que se componen únicamente de máquinas virtuales dedicadas, es posible escalar un grupo que contenga máquinas virtuales de prioridad baja mediante la llamada al método Resize o por medio de escalado automático.

La operación de cambio de tamaño de grupo tiene un segundo parámetro opcional que actualiza el valor de **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

La fórmula de escalado automático de grupo admite máquinas virtuales de prioridad baja del siguiente modo:

-   Puede obtener o establecer el valor de la variable definida por el servicio **$TargetLowPriorityNodes**.

-   Puede obtener el valor de la variable definida por el servicio **$CurrentLowPriorityNodes**.

-   Puede obtener el valor de la variable definida por el servicio **$PreemptedNodeCount**. 
    Esta variable devuelve el número de nodos en estado reemplazado y le permite escalar o reducir verticalmente el número de nodos dedicados, en función del número de nodos reemplazados que no estén disponibles.

## <a name="jobs-and-tasks"></a>Trabajos y tareas

Los trabajos y las tareas requieren muy poca compatibilidad con los nodos de prioridad baja; la única compatibilidad es la siguiente:

-   La propiedad JobManagerTask de un trabajo tiene una propiedad nueva, **AllowLowPriorityNode**. 
    Cuando esta propiedad es true, la tarea del administrador de trabajos se puede programar tanto en un nodo dedicado como de prioridad baja. Si esta propiedad es false, la tarea del administrador de trabajos se programará solo para un nodo dedicado.

-   Una [variable de entorno](batch-compute-node-environment-variables.md) está disponible para una aplicación de tareas de modo que sea posible determinar si se ejecuta en un nodo de prioridad baja o dedicado. La variable de entorno es AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Control de reemplazos

En ocasiones las máquinas virtuales se pueden reemplazar; cuando esto sucede, Batch hace lo siguiente:

-   Las máquinas virtuales reemplazadas tienen su estado actualizado a **Reemplazada**.
-   Si las tareas se estuvieran ejecutando en las máquinas virtuales del nodo reemplazado, esas tareas se pondrían de nuevo en cola y se volverían a ejecutar.
-   La máquina virtual se elimina en la práctica, lo que hace que se pierdan los datos almacenados localmente en ella.
-   El grupo intenta continuamente alcanzar el número objetivo de nodos de prioridad baja disponibles. Cuando se encuentra la capacidad de reemplazo, los nodos mantienen sus identificaciones, pero se reinicializan, pasando por los estados**Creando** e **Iniciando** antes de que estén disponibles para la programación de tareas.
-   Los recuentos de reemplazos están disponibles como una métrica en el portal de Azure.

## <a name="next-steps"></a>Pasos siguientes

* Consulte [Información general de las características de Lote para desarrolladores](batch-api-basics.md), donde encontrará información esencial para cualquier persona que vaya a utilizar Lote. El artículo contiene información más detallada acerca de los recursos del servicio de Lote, como grupos, nodos, trabajos y tareas, así como las numerosas características de API que se pueden usar al compilar cualquier aplicación de Lote.
* Obtenga información acerca de las [API y herramientas de Batch](batch-apis-tools.md) disponibles para la creación de soluciones de Batch.

