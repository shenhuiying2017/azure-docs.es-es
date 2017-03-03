---
title: "Directrices para la optimización del rendimiento de Azure Data Lake Store | Microsoft Docs"
description: "Directrices para la optimización del rendimiento de Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/02/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: af11866fc812cd8a375557b7bf9df5cdc9bba610
ms.openlocfilehash: f0d0c05c08ce198e2702c76ad35b348107c664c7
ms.lasthandoff: 01/18/2017


---
# <a name="performance-tuning-guidance-for-azure-data-lake-store"></a>Guía para la optimización del rendimiento de Azure Data Lake Store

En este artículo se proporcionan instrucciones sobre cómo obtener el rendimiento más óptimo al escribir o leer datos de rendimiento en Azure Data Lake Store. El objetivo de este artículo es ayudar a los usuarios a comprender los parámetros que normalmente se pueden configurar para las herramientas de carga y descarga de datos usadas más comúnmente y las cargas de trabajo de análisis de datos. En esta guía, la optimización va dirigida principalmente a las cargas de trabajo que consumen muchos recursos donde hay una gran cantidad de datos que se leen o escriben en Data Lake Store.

## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Una cuenta de Almacén de Azure Data Lake**. Para obtener instrucciones sobre cómo crear una, consulte la [introducción al Almacén de Azure Data Lake](data-lake-store-get-started-portal.md)
* **Clúster de HDInsight de Azure** con acceso a una cuenta de Almacén de Data Lake. Consulte [Creación de un clúster de HDInsight con Data Lake Store mediante el Portal de Azure](data-lake-store-hdinsight-hadoop-use-portal.md). Asegúrese de habilitar el Escritorio remoto para el clúster.


## <a name="guidelines-for-data-ingestion-tools"></a>Directrices para las herramientas de ingesta de datos

En esta sección se proporcionan instrucciones generales para mejorar el rendimiento cuando los datos se copian o se mueven en Data Lake Store. Se explican los factores que limitan el rendimiento y las formas de superar estas limitaciones. A continuación se muestran algunos aspectos que se deben tener en cuenta:

* **Datos de origen**: existen muchas restricciones que pueden surgir del lugar del que provengan los datos de origen. La capacidad de proceso puede ser un cuello de botella si los datos de origen están en ejes lentos o almacenamiento remoto con funcionalidades de baja capacidad de proceso. Los SSD, preferiblemente en discos locales, proporcionarán el mejor rendimiento debido a la mayor capacidad de proceso del disco.

* **Red**: si tiene los datos de origen en máquinas virtuales, la conexión de red entre la máquina virtual y Data Lake Store es importante. Use máquinas virtuales con la NIC de mayor disponibilidad para obtener más ancho de banda de red.

* **Copia entre regiones**: hay un gran costo de red inherente a la E/S de datos entre regiones, como puede ser la ejecución de una herramienta de ingesta de datos en una máquina virtual en el Este de EE. UU. - 2 para escribir datos en una cuenta de Data Lake Stor en el Centro de EE. UU. Si va a copiar datos entre regiones, puede que se reduzca el rendimiento. Se recomienda usar trabajos de ingesta de datos en máquinas virtuales de la misma región que la cuenta de Data Lake Store de destino para maximizar el rendimiento de la red.                                                                                                                                        

* **Clúster**: si va a ejecutar trabajos de ingesta de datos mediante un clúster de HDInsight (por ejemplo, para DistCp), se recomienda usar máquinas virtuales de la serie D en el clúster ya que contienen más memoria. Un mayor número de núcleos también ayudará a aumentar el rendimiento.                                                                                                                                                                                                                                                                                                            

* **Simultaneidad de los subprocesos**: si va a usar un clúster de HDInsight para copiar datos desde un contenedor de almacenamiento, existen limitaciones en cuanto al número de subprocesos en paralelo que se pueden usar según el tamaño del clúster, el tamaño del contenedor y la configuración de los subprocesos. Una de las mejores maneras de mejorar el rendimiento en Data Lake Store es aumentar la simultaneidad. Para obtener la mayor simultaneidad a fin de obtener mayor rendimiento, debe ajustar la configuración. En la tabla siguiente están los valores de configuración de cada método de ingesta que se pueden configurar para obtener una mayor simultaneidad. Siga los vínculos de la tabla para ir a los artículos que hablan sobre cómo usar la herramienta para la ingesta de datos en Data Lake Store y cómo optimizar el rendimiento de la herramienta para conseguir la máxima capacidad de proceso.

    | Herramienta               | Configuración de simultaneidad                                                                |
    |--------------------|------------------------------------------------------------------------------------|
    | [PowerShell](data-lake-store-get-started-powershell.md)       | PerFileThreadCount, ConcurrentFileCount |
    | [AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)    | Unidades de Azure Data Lake Analytics         |
    | [DistCp](data-lake-store-copy-data-wasb-distcp.md)            | -m (mapper)                             |
    | [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md)| parallelCopies                          |
    | [Sqoop](data-lake-store-data-transfer-sql-sqoop.md)           | fs.azure.block.size, -m (mapper)        |


## <a name="guidelines-while-working-with-hdinsight-workloads"></a>Guía para trabajar con cargas de trabajo de HDInsight

Mientras se ejecutan cargas de trabajo de análisis para trabajar con datos en Data Lake Store, se recomienda usar versiones del clúster de HDInsight 3.5 para obtener el mejor rendimiento con este componente. Cuando el trabajo hace uso intensivo de operaciones de E/S, se pueden configurar determinados parámetros de cara al rendimiento. Por ejemplo, si el trabajo está compuesto principalmente por lecturas o escrituras, aumentar la simultaneidad de E/S hacia y desde Azure Data Lake Store podría aumentar el rendimiento.

Azure Data Lake Store está mejor optimizado para el rendimiento cuando hay más simultaneidad. Para aumentar la simultaneidad de trabajos de uso intensivo de E/S, existen algunos métodos generales:

1. **Ejecutar un número mayor de contenedores YARN de proceso en lugar de unos pocos contenedores YARN grandes**: tener más contenedores aumentará la simultaneidad para las operaciones de entrada y salida, por lo que se aprovechan las capacidades de Data Lake Store.

    Por ejemplo, supongamos que tiene 2 nodos D3v2 en el clúster de HDInsight. Cada nodo D3v2 tiene 12 GB de memoria YARN, de modo que 2 máquinas D3v2 tendrán 24 GB de memoria YARN. Supongamos también que ha establecido los tamaños del contenedor YARN en 6 GB. Esto significa que puede tener 4 contenedores de 6 GB cada uno. Por lo tanto, puede tener 4 tareas simultáneas ejecutándose en paralelo. Para aumentar la simultaneidad, puede reducir el tamaño de los contenedores a 3 GB, lo que le proporcionará 8 contenedores de 3 GB. Esto le permite tener 8 tareas simultáneas ejecutándose en paralelo. A continuación se muestra una ilustración.

    ![Rendimiento de Data Lake Store](./media/data-lake-store-performance-tuning-guidance/image-1.png)

    Una pregunta frecuente es ¿por qué no reducir el tamaño del contenedor a 1 GB de memoria para poder obtener 24 contenedores y así aumentar más aún la simultaneidad? Depende de si la tarea necesita 3 GB de memoria o es suficiente con 1 GB.  Puede que esté realizando una operación sencilla en el contenedor que solo necesite 1 GB de memoria o una operación compleja que requiera 3 GB de memoria.  Si reduce el tamaño del contenedor demasiado, recibirá una excepción de memoria insuficiente.  Cuando esto sucede, debe aumentar el tamaño de los contenedores.  Además de la memoria, el número de núcleos virtuales también puede afectar al paralelismo.

    ![Rendimiento de Data Lake Store](./media/data-lake-store-performance-tuning-guidance/image-2.png)

2. **Aumentar la memoria en el clúster para obtener mayor simultaneidad**: puede aumentar la memoria en el clúster aumentando el tamaño del clúster o eligiendo un tipo de máquina virtual que tenga más memoria. De esta forma aumentará la cantidad de memoria YARN disponible para que pueda crear más contenedores, lo que aumenta la simultaneidad.  

    Por ejemplo, supongamos que tiene un único nodo D3v2 en el clúster de HDInsight con 12 GB de memoria YARN y contenedores de 3 GB.  Si escala el clúster a 2 D3v2, aumentará la memoria YARN a 24 GB.  Esto aumenta la simultaneidad de 4 a 8.

    ![Rendimiento de Data Lake Store](./media/data-lake-store-performance-tuning-guidance/image-3.png)

3. **Comenzar configurando el número de tareas en el número de simultaneidad que tiene**: hasta ahora, ya ha establecido el tamaño del contenedor adecuadamente para obtener la cantidad máxima de simultaneidad. Ahora debe establecer el número de tareas para usar todos esos contenedores. Hay nombres diferentes para las tareas en cada carga de trabajo.

    También podría considerar el tamaño del trabajo. Si el tamaño del trabajo es grande, cada tarea puede tener una gran cantidad de datos para procesar. Puede que quiera usar más tareas para que cada tarea no vaya a procesar demasiados datos.

    Por ejemplo, supongamos que tiene 6 contenedores. Las tareas se deberían establecer en 6 como punto de partida. Puede experimentar con el uso de un mayor número de tareas para ver si mejora el rendimiento. Establecer un número mayor de tareas no aumenta la simultaneidad. Si las tareas se establecen en un valor superior a 6, la tarea no se ejecutará hasta la próxima ronda. Si las tareas se establecen en un valor por debajo de 6, la simultaneidad no se usará completamente.

    Cada carga de trabajo tiene diferentes parámetros disponibles para establecer el número de tareas. En la tabla siguiente se enumeran algunos de ellos.

    | Carga de trabajo               | Parámetro para establecer tareas                                                         |
    |--------------------|------------------------------------------------------------------------------------|
    | [Spark en HDInisight](data-lake-store-performance-tuning-spark.md)       | <ul><li>Num-executors</li><li>Memoria del ejecutor</li><li>Executor-cores</li></ul> |
    | [Hive en HDInsight](data-lake-store-performance-tuning-hive.md)    | hive.tez.container.size         |
    | [MapReduce en HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
    | [Storm en HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Número de procesos de trabajo</li><li>Número de instancias de ejecutor de spout</li><li>Número de instancias de ejecutor de bolt </li><li>Número de tareas de spout</li><li>Número de tareas de bolt</li></ul>|

## <a name="see-also"></a>Consulte también
* [Información general del Almacén de Azure Data Lake](data-lake-store-overview.md)
* [Tutorial: Introducción a Análisis de Azure Data Lake mediante el Portal de vista previa de Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

