---
title: "Directrices para la optimización del rendimiento de Storm en Azure Data Lake Store | Microsoft Docs"
description: "Directrices para la optimización del rendimiento de Storm en Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
translationtype: Human Translation
ms.sourcegitcommit: ebf876f946eceddce9c8c990d8b28fcb969bec23
ms.openlocfilehash: 112226028c053cc91f9fb2bc0e5978f7cb2343ed


---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-store"></a>Guía para la optimización del rendimiento de Storm en HDInsight y Azure Data Lake Store

Hay algunos factores que deben tenerse en cuenta al optimizar el rendimiento de una topología de Storm.  Es importante comprender las características del trabajo realizado por los spouts y los bolts (si el trabajo hace un uso intensivo de la memoria o las E/S).

## <a name="prerequisites"></a>Requisitos previos 

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 
* **Una cuenta de Almacén de Azure Data Lake**. Para obtener instrucciones sobre cómo crear una, consulte la [introducción al Almacén de Azure Data Lake](data-lake-store-get-started-portal.md) 
* **Clúster de HDInsight de Azure** con acceso a una cuenta de Almacén de Data Lake. Consulte [Creación de un clúster de HDInsight con Data Lake Store mediante el Portal de Azure](data-lake-store-hdinsight-hadoop-use-portal.md). Asegúrese de habilitar el Escritorio remoto para el clúster. 
* **Ejecutar el clúster de Storm en Azure Data Lake Store**.  Para obtener más información, consulte [Introduction to Apache Storm on HDInsight: Real-time analytics for Hadoop](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-storm-overview) (Introducción a Apache Storm en HDInsight: análisis en tiempo real para Hadoop). 
* **Directrices para la optimización del rendimiento en ADLS**.  Para conocer los conceptos generales de rendimiento, consulte [Guía para la optimización del rendimiento de Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance).  

**Ajuste del paralelismo de la topología**

Puede alcanzar un mejor rendimiento si aumenta la simultaneidad de la entrada/salida hacia y desde Azure Data Lake Store.  
Una topología de Storm tiene un conjunto de configuraciones que determinan el paralelismo:
* Número de procesos de trabajo: los trabajos se distribuyen uniformemente entre las VM.
* Número de instancias de ejecutor de spout
* Número de instancias de ejecutor de bolt
* Número de tareas de spout
* Número de tareas de bolt

Por ejemplo, en un clúster con 4 VM y 4 procesos de trabajo, 32 ejecutores de spout y 32 tareas de spout, 256 ejecutores de bolt y 512 tareas de bolt:

Cada supervisor, que es un nodo de trabajo, tiene un solo proceso JVM de trabajo que se administra los 4 subprocesos de spout y 64 subprocesos de bolt. Dentro de cada subproceso, las tareas se ejecutan secuencialmente. Con la configuración anterior, cada subproceso spout tendrá una tarea y cada subproceso bolt tendrá 2 tareas.

A continuación se indican los distintos componentes implicados en Storm, y cómo afectan al nivel de paralelismo que tiene:
* El nodo principal (denominado Nimbus en Storm) se utiliza para enviar y administrar los trabajos. Estos nodos afectan al grado de paralelismo.
* Los nodos de supervisor: en HDInsight de Azure, esto corresponde a una VM de Azure de nodo de trabajo.
* Las tareas de trabajo son procesos de Storm que se ejecutan en las VM. Cada tarea de trabajo corresponde a una instancia de java JVM. Storm distribuye el número de procesos de trabajo que especifique a los nodos de trabajo lo más uniformemente posible.
* Instancias de ejecutor de spout y bolt: cada instancia del ejecutor corresponde a un subproceso que se ejecuta en los trabajos (JVM).
* Tareas de Storm: se trata de tareas lógicas que ejecuta cada uno de estos subprocesos. Esto no cambia el nivel de paralelismo, por lo que se debe evaluar si necesita o no varias tareas por ejecutor.

## <a name="guidance"></a>Guía

Cuando trabaje con Azure Data Lake, obtendrá un rendimiento óptimo si lo hace lo siguiente:
* Fusione sus anexos más pequeños en tamaños más grandes (lo ideal es que tenga 4 MB de tamaño).
* Realice tantas solicitudes simultáneas como pueda. Como cada subproceso de bolt está realizando lecturas de bloqueo, desea tener de 8 a 12 subprocesos por núcleo para que la NIC y la CPU bien utilizadas.  Una VM mayor permitirá más solicitudes simultáneas.  

## <a name="example"></a>Ejemplo

Supongamos que tiene un clúster de 8 nodos de trabajo con la VM D13v2 de Azure.  Una VM D13v2 tiene 8 núcleos, por lo que con los 8 nodos de trabajo tiene 64 núcleos en total.

Digamos que hacemos 8 subprocesos de bolt por núcleo. Como hay 64 núcleos, deseamos 512 instancias de ejecutor de bolt en total (es decir, subprocesos). En este caso, supongamos que empezamos con una JVM por VM y utilizamos principalmente la simultaneidad de subprocesos dentro de la JVM para lograr la simultaneidad. Esto significa que necesitamos 8 tareas de trabajo (una por cada VM de Azure) y 512 ejecutores de bolt. Dada esta configuración, Storm intentará distribuir los trabajos uniformemente entre los nodos de trabajo (también conocidos como nodos de supervisor), dando a cada nodo de trabajo una JVM. Ahora en cuanto a los supervisores, Storm intentará distribuir los ejecutores uniformemente entre ellos, dando 8 subprocesos a cada supervisor (es decir, JVM).

## <a name="further-tuning"></a>Optimización adicional
Una vez que tenga la topología básica, puede valorar si desea modificar alguno de los parámetros:
* **Number of JVMs per worker node** (Número de JVM por nodo de trabajo): si tiene una estructura de datos de gran tamaño (por ejemplo, una tabla de búsqueda) hospedada en memoria, cada JVM requerirá una copia independiente, por lo que tener menos JVM le permitirá usar la estructura en muchos subprocesos. Para las E/S del bolt, el número de JVM no supone una gran diferencia con respecto al número de subprocesos agregados en esas JVM. Para simplificar, se recomienda una JVM por trabajo, pero debe evaluar si debe modificar este número dependiendo de lo que está haciendo el bolt o del procesamiento de aplicaciones que necesita.
* **Number of spout executors** (Número de ejecutores de spout): puesto que en este ejemplo se usan bolts para escribir en Azure Data Lake, el número de spouts no afecta directamente al rendimiento de los bolts. Sin embargo, según la cantidad de procesamiento o E/S que se realice en el spout, deseará optimizar los spouts para mejorar el rendimiento. Como mínimo, deberá asegurarse de que tiene suficientes spouts para poder mantener los bolts ocupados: es decir, la velocidad de salida de los spouts debe coincidir con el rendimiento de los bolts. La configuración real dependerá del spout, y queda fuera del ámbito de este documento.
* **Number of tasks** (Número de tareas): cada bolt se ejecuta como un único subproceso. Las tareas adicionales por bolt no proporcionan ninguna simultaneidad adicional. La única vez que resultan útiles es cuando el proceso de confirmación de la tupla usa una gran proporción del tiempo de ejecución de los bolts. Se recomienda agrupar muchas tuplas en un anexo mayor antes de enviar una confirmación desde el bolt por lo que, en que la mayoría de los casos, la existencia de varias tareas no ofrece ninguna ventaja adicional.
* **Local or shuffle grouping** (Agrupación local o de orden aleatorio): cuando esta opción está habilitada, las tuplas se envían a bolts de dentro del mismo proceso de trabajo. Esta opción reduce las llamadas de red y la comunicación entre procesos. Se recomienda su uso para la mayoría de las topologías.

Como un enfoque inicial, es recomendable empezar por escenario básico y probar con sus propios datos para ajustar los parámetros mencionados anteriormente de modo que se logre un rendimiento óptimo.

## <a name="tuning-the-spout"></a>Optimización del spout

**Tiempo de espera de la tupla**

topology.message.timeout.secs: este ajuste determina la cantidad de tiempo que se espera a que un mensaje se complete y reciba confirmación antes de que se considere que se ha producido un error.

**Memoria máxima por proceso de trabajo**

Worker.childopts: este ajuste le permite especificar los parámetros de línea de comandos adicionales a los trabajos de java. El ajuste usado más frecuentemente aquí es XmX, que determina la memoria máxima asignada al montón de una JVM.

**Máximo de spouts pendientes**

Topology.max.spout.pending: esta configuración determina el número de tuplas que pueden estar en vuelo (que aún no se han confirmado en todos los nodos de la topología) por subproceso de spout en un momento concreto.

Un buen cálculo sería estimar el tamaño de cada una de sus tuplas. Después, averigüe cuánta memoria tiene un subproceso de spout. La memoria total asignada a un subproceso dividida por este valor debe proporcionarle el límite superior para el parámetro de máximo de spouts pendientes.

## <a name="tuning-the-bolt"></a>Optimización del bolt
Se recomienda que, cuando escriba en ADLS, establezca una directiva de sincronización de tamaño (el búfer en el lado del cliente) a 4 MiB.  Así, se realizará un vaciado o un hsync() solamente cuando el tamaño del búfer sea el valor anterior.  El controlador ADLS de la VM del trabajo realiza automáticamente este almacenamiento en búfer, a menos que el usuario realice explícitamente un hsync().

El bolt predeterminado de ADLS de Storm tiene un parámetro de directiva de sincronización de tamaño (fileBufferSize) que puede utilizarse para optimizar este parámetro.

En las topologías con uso intensivo de E/S, se recomienda que cada subproceso de bolt escriba en su propio archivo y establezca una directiva de rotación de archivos (fileRotationSize).  Cuando el archivo alcanza un determinado tamaño, la transmisión se vacía automáticamente y se escribe en un nuevo archivo.  El tamaño de archivo recomendado por rotación es de 1 GB.

**Cuándo se debe confirmar:** en Storm, un spout se mantiene en una tupla hasta que el bolt lo confirma de forma explícita.  Si el bolt ha leído una tupla, pero no la ha confirmado aún, no se garantiza que el bolt se haya conservado en el back-end de Azure Data Lake Store.  Una vez que se confirma una tupla, el bolt garantiza la persistencia del spout, por lo que puede eliminar los datos de origen de cualquier origen del que esté leyendo.  

**Para obtener el mejor rendimiento en ADLS:** se recomienda que el bolt almacene en búfer 4 MB de datos de la tupla y, a continuación, escriba en el back-end de ADLS como una escritura de 4 MB. Una vez que los datos se han escrito correctamente en el almacén (mediante hflush()) el bolt puede confirmar los datos al spout. Esto es lo que hace el bolt de ejemplo aquí indicado. También es aceptable conservar un mayor número de tuplas antes de realizar la llamada hflush() y de que se confirmen las tuplas. Sin embargo, esto aumenta el número de tuplas en vuelo que el spout debe conservar y, por lo tanto, aumenta la cantidad de memoria necesaria por cada JVM.

Las aplicaciones pueden necesitar que se confirme con más frecuencia (en tamaños de datos menores que 4 MB) por otros motivos no relacionados con el rendimiento. Sin embargo, puede que esto afecte al rendimiento de E/S del back-end de almacenamiento, por lo que el cliente debe considerar detenidamente si hacerlo le compensa el cambio de rendimiento de E/S del bolt.

Si la velocidad de entrada de tuplas no es suficientemente alta y el búfer de 4 MB tarda mucho tiempo en llenarse, puede considerar su migración de una serie de formas:
* Reducir el número de bolts para que haya menos búferes de 4 MB que completar.
* Tener una directiva basada en recuento o en tiempo, donde se desencadene un hflush() cada X vaciados o cada y milisegundos y se confirmen las tuplas acumuladas hasta entonces.

Tenga en cuenta que el rendimiento en este caso es menor, pero como la tasa de eventos es lenta, el objetivo principal tampoco es obtener un rendimiento máximo de todos modos.  Los procedimientos de mitigación anteriores le permiten reducir el tiempo total que tarda una tupla en atravesar el almacén, lo que puede resultar importante si desea una canalización en tiempo real incluso en caso de una tasa de evento baja.  Tenga en cuenta también que si la tasa de tuplas entrantes es baja, también puede ajustar el parámetro topology.message.timeout_secs, de modo que el tiempo de espera de las tuplas no se agote mientras se estén almacenando en búfer o procesando.

## <a name="interpreting-storm-ui"></a>Interpretación de la UI de Storm  
Mientras se ejecuta la topología, puede supervisarla en la interfaz de usuario de Storm. Cuando examine la interfaz de usuario, estos son los principales parámetros que debe observar:

* **Total process execution latency** (Latencia total de ejecución de procesos): este es el tiempo medio que tarda una tupla en ser emitida por el spout, procesada por el bolt y obtener confirmación.

* **Total bolt process latency** (Latencia total de procesamiento de bolts): este es el tiempo medio que está la tupla en el bolt hasta que recibe una confirmación.

* **Total bolt execute latency** (Latencia total de ejecución de bolts): este es el tiempo medio empleado por el bolt en el método de ejecución.

* **Number of failures** (Número de errores): se refiere al número de tuplas que no se pudieron procesar por completo antes de que se agotara el tiempo.

* **Capacity** (Capacidad): se trata de una medida de lo ocupado que está su sistema. Si este número es 1, los bolts trabajan tan rápido como pueden. Si es menor que 1, aumente el paralelismo. Si es mayor que&1;, reduzca el paralelismo.

## <a name="common-troubleshooting-scenarios"></a>Escenarios habituales de solución de problemas
* **Se agota el tiempo de gran número de tuplas**: examine los nodos de la topología individualmente para determinar dónde se encuentra el cuello de botella. El motivo más común de que ocurra esto es que los bolts no sean capaces de mantenerse al ritmo de los spouts, provocando que las tuplas atasquen los búferes internos mientras esperan a ser procesadas. Considere la posibilidad de aumentar el valor de tiempo de espera o disminuir el máximo de spouts pendientes.

* **Alta latencia de ejecución de proceso total, pero baja latencia de proceso de bolts**: en este caso, puede que las tuplas no se estén procesando lo suficientemente rápido. Compruebe que hay un número suficiente de ackers. Otra posibilidad es que esperen en la cola demasiado tiempo hasta que los bolts empiecen a procesarlas. Reduzca el máximo de spouts pendientes.

* **Alta latencia de ejecución de bolts**: esto significa que el método execute() de su bolt está tardando demasiado. Optimice el código o examine el comportamiento de vaciado/tamaños de escritura.

## <a name="limitation"></a>Limitación 
Limitación de ADLS: si se alcanzan los límites de ancho de banda impuestos por ADLS, empezará a ver errores de tareas. Esto puede identificarse observando los errores de limitación en los registros de tarea.  Puede reducir el paralelismo mediante un aumento del tamaño del contenedor.  Si necesita más simultaneidad para su trabajo, póngase en contacto con nosotros.   
Para comprobar si está sujeto a limitaciones, debe habilitar el registro de depuración en el lado del cliente. Para hacerlo:

1. Cambie lo siguiente en Ambari > Storm > Config > Advanced storm-worker-log4j.  Cambie &lt;root level="info"&gt; a &lt;root level=”debug”&gt;.  Reinicie todos los nodos/servicios para que la configuración surta efecto.
2. Supervise la topología de Storm en los nodos de trabajo (en /var/log/storm/worker-artifacts/&lt;NombreTopología&gt;/&lt;puerto&gt;/worker.log) para controlar las excepciones de limitación de ADLS.

## <a name="additional-tuning"></a>Optimización adicional
Puede consultar este [blog](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/) para saber más sobre la optimización del rendimiento adicional de Storm.

## <a name="examples-to-run"></a>Ejemplos que puede ejecutar
Pruebe este ejemplo ubicado en [github](https://github.com/hdinsight/storm-performance-automation).



<!--HONumber=Jan17_HO2-->


