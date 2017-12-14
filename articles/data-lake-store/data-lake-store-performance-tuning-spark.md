---
title: "Directrices para la optimización del rendimiento de Spark en Azure Data Lake Store | Microsoft Docs"
description: "Directrices para la optimización del rendimiento de Spark en Azure Data Lake Store"
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
ms.openlocfilehash: 3c8ef6fd200f67ebc216c967bb1a6250ddcc15d4
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-store"></a>Guía para la optimización del rendimiento de Spark en HDInsight y Azure Data Lake Store

Cuando ajuste el rendimiento de Spark, necesitará tener en cuenta el número de aplicaciones que se ejecutarán en su clúster.  De forma predeterminada, puede ejecutar 4 aplicaciones simultáneamente en su clúster HDI. (Nota: la configuración predeterminada está sujeta a cambios).  Puede decidir usar menos aplicaciones, por lo que puede sustituir la configuración predeterminada y utilizar más del clúster para esas aplicaciones.  

## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Una cuenta de Almacén de Azure Data Lake**. Para obtener instrucciones sobre cómo crear una, consulte la [introducción al Almacén de Azure Data Lake](data-lake-store-get-started-portal.md)
* **Clúster de HDInsight de Azure** con acceso a una cuenta de Almacén de Data Lake. Consulte [Creación de un clúster de HDInsight con Data Lake Store mediante el Portal de Azure](data-lake-store-hdinsight-hadoop-use-portal.md). Asegúrese de habilitar el Escritorio remoto para el clúster.
* **Ejecutar el clúster de Spark en Azure Data Lake Store**.  Para obtener más información, consulte [Use HDInsight Spark cluster to analyze data in Data Lake Store](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store) (Uso de clúster Spark en HDInsight para analizar datos en Data Lake Store).
* **Directrices para la optimización del rendimiento en ADLS**.  Para conocer los conceptos generales de rendimiento, consulte [Guía para la optimización del rendimiento de Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance). 

## <a name="parameters"></a>parameters

Estos son los valores más importantes que se pueden optimizar para aumentar el rendimiento en ADLS cuando ejecute trabajos de Spark:

* **num-executors**: número de tareas simultáneas que se pueden ejecutar.

* **executor-memory**: cantidad de memoria asignada a cada ejecutor.

* **executor-cores**: número de núcleos asignados a cada ejecutor.                     

**num-executors**. En num-executors se define el número máximo de tareas que se pueden ejecutar en paralelo.  El número real de tareas que se pueden ejecutar en paralelo está limitado por los recursos de CPU y memoria disponibles en el clúster.

**executor-memory**. Es la cantidad de memoria que se asigna a cada ejecutor.  La memoria necesaria para cada ejecutor depende del trabajo.  En las operaciones complejas, la memoria debe ser mayor.  En cambio, para las simples como la lectura y escritura, los requisitos de memoria serán menores.  La cantidad de memoria para cada ejecutor puede verse en Ambari.  En Ambari, vaya a Spark y vea la pestaña Configs (Configuraciones).  

**executor-cores**. Establece la cantidad de núcleos que se usan por ejecutor, lo que determina el número de subprocesos paralelos que se pueden ejecutar por ejecutor.  Por ejemplo, si executor-cores = 2, entonces cada ejecutor puede ejecutar 2 tareas en paralelo.  Los núcleos de ejecutor necesarios dependerán del trabajo.  Los trabajos con muchas E/S no requieren una gran cantidad de memoria por tarea, por lo que cada ejecutor puede administrar más tareas paralelas.

De forma predeterminada, se definen dos núcleos YARN virtuales para cada núcleo físico cuando se ejecuta Spark en HDInsight.  Este número proporciona un buen equilibrio entre simultaneidad y la cantidad de cambios de contexto desde varios subprocesos.  

## <a name="guidance"></a>Guía

Si va a ejecute cargas de trabajo de análisis de Spark para trabajar con datos de Data Lake Store, se recomienda usar la versión más reciente de HDInsight para conseguir el mejor rendimiento con Data Lake Store. Si el trabajo tiene un uso intensivo de E/S, hay determinados parámetros que puede configurar para mejorar el rendimiento.  Azure Data Lake Store es una plataforma de almacenamiento altamente escalable que puede controlar un alto rendimiento.  Si el trabajo está compuesto principalmente por lecturas o escrituras, aumentar la simultaneidad de E/S hacia y desde Azure Data Lake Store podría aumentar el rendimiento.

Hay varias formas de aumentar la simultaneidad de trabajos con uso intensivo de E/S.

**Paso 1: determine cuántas aplicaciones se ejecutan en el clúster**. Debe conocer cuántas aplicaciones se están ejecutando en el clúster, incluida la actual.  Los valores predeterminados para cada ajuste de Spark asume que hay 4 aplicaciones ejecutándose simultáneamente.  Por lo tanto, solo habrá un 25 % del clúster disponible para cada aplicación.  Para obtener un mejor rendimiento, puede sustituir los valores predeterminados cambiando el número de ejecutores.  

**Paso 2: establezca executor-memory**. Lo primero que se debe establecer es executor-memory.  La memoria dependerá el trabajo que va a ejecutar.  Puede aumentar la simultaneidad asignando menos memoria a cada ejecutor.  Si ve excepciones por memoria insuficiente cuando ejecuta el trabajo, aumente el valor para este parámetro.  Otra alternativa consiste en obtener más memoria mediante un clúster que tenga mayor cantidad de la misma o aumentar el número de nodos en el clúster.  Mientras más memoria tenga, más ejecutores podrá utilizar, lo que se traduce en una mayor simultaneidad.

**Paso 3: establezca executor-cores**. En cargas de trabajo con uso intensivo de E/S que no tengan operaciones complejas, es recomendable empezar con un gran número de núcleos de ejecutor para aumentar el número de tareas paralelas por cada ejecutor.  Un buen punto de partida sería establecer executor-cores en 4.   

    executor-cores = 4
Si aumenta el número de núcleos de ejecutor podrá tener mayor paralelismo, con lo que puede experimentar con diferentes valores para executor-cores.  Para los trabajos que tengan operaciones más complejas, reduzca el número de núcleos por ejecutor.  Si executor-cores se ha establecido en un número mayor que 4, puede que la recolección de elementos no utilizados sea ineficaz y el rendimiento se vea degradado.

**Paso 4: determine la cantidad de memoria YARN en el clúster**. Esta información está disponible en Ambari.  Vaya a YARN y vea la pestaña Configs (Configuraciones).  La memoria YARN se muestra en esta ventana.  
Nota: mientras está en la ventana puede ver también el tamaño predeterminado del contenedor YARN.  El tamaño del contenedor YARN es igual que la memoria por cada parámetro de ejecutor.

    Total YARN memory = nodes * YARN memory per node
**Paso 5: calcule num-executors**

**Calcule la restricción de memoria**: el parámetro num-executors está restringido por la memoria o por la CPU.  La restricción de memoria se determina por la cantidad de memoria YARN disponible para la aplicación.  Tome la memoria total de YARN y divídala por la memoria del ejecutor.  La restricción debe adaptarse al número de aplicaciones, por lo que dividiremos entre dicho número.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**Calcule la restricción de CPU**: la restricción de la CPU se calcula como el total de núcleos virtuales divididos por el número de núcleos por ejecutor.  Hay 2 núcleos virtuales para cada núcleo físico.  Al igual que en la restricción de memoria, se divide por el número de aplicaciones.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Establezca num-executors**: el parámetro num-executors se determina con la restricción de memoria y la restricción de CPU mínimas. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
El hecho de establecer un número mayor de ejecutores no tiene por qué aumentar el rendimiento.  Debe tener en cuenta que al agregar más ejecutores pondrá una sobrecarga adicional a cada ejecutor que agregue, lo que podría reducir el rendimiento.  El valor de num-executors está limitado por los recursos del clúster.    

## <a name="example-calculation"></a>Cálculo de ejemplo

Supongamos que tiene actualmente un clúster compuesto de 8 nodos D4v2 que están ejecutando 2 aplicaciones, incluida la que se va a ejecutar.  

**Paso 1: determine las aplicaciones se ejecutan en el clúster**. Sabe que tiene 2 aplicaciones en el clúster, incluida la que se va a ejecutar.  

**Paso 2: establezca executor-memory**. En este ejemplo, determinamos que 6 GB de memoria de ejecutor serán suficientes para trabajos con uso intensivo de E/S.  

    executor-memory = 6GB
**Paso 3: establezca executor-cores**. Puesto que se trata de un trabajo con uso intensivo de E/S, podemos establecer el número de núcleos para cada ejecutor a 4.  Si establece los núcleos por ejecutor en un número mayor que 4 puede causar problemas de colección de elementos no utilizados.  

    executor-cores = 4
**Paso 4: determine la cantidad de memoria YARN en clúster**. Navegamos a Ambari para averiguar que cada D4v2 tiene 25 GB de memoria YARN.  Dado que hay 8 nodos, la memoria YARN disponible se multiplica por 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Paso 5: calcule num-executors**. El parámetro num-executors se determina a partir de la restricción mínima de memoria y de CPU, dividida por el número de aplicaciones que están ejecutando en Spark.    

**Calcule la restricción de memoria**. La restricción de memoria se calcula dividiendo la memoria YARN total entre la memoria por ejecutor.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**Calcule la restricción de CPU**. La restricción de la CPU se calcula dividiendo el total de núcleos YARN entre el número de núcleos por ejecutor.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Establezca num-executors**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

