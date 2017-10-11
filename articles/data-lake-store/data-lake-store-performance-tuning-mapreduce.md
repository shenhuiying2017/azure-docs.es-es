---
title: "Directrices para la optimización del rendimiento de MapReduce de Azure Data Lake Store | Microsoft Docs"
description: "Directrices para la optimización del rendimiento de MapReduce en Azure Data Lake Store"
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
ms.openlocfilehash: 9528148792f083cb0e48d356e61cf61762ee954f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-store"></a>Guía para optimizar el rendimiento de MapReduce en HDInsight y Azure Data Lake Store


## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Una cuenta de Almacén de Azure Data Lake**. Para obtener instrucciones sobre cómo crear una, consulte la [introducción al Almacén de Azure Data Lake](data-lake-store-get-started-portal.md)
* **Clúster de HDInsight de Azure** con acceso a una cuenta de Almacén de Data Lake. Consulte [Creación de un clúster de HDInsight con Data Lake Store mediante el Portal de Azure](data-lake-store-hdinsight-hadoop-use-portal.md). Asegúrese de habilitar el Escritorio remoto para el clúster.
* **Uso de MapReduce en HDInsight**.  Para más información, consulte [Uso de MapReduce en Hadoop en HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-use-mapreduce).  
* **Directrices para la optimización del rendimiento en ADLS**.  Para conocer los conceptos generales de rendimiento, consulte [Guía para la optimización del rendimiento de Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance).  

## <a name="parameters"></a>parameters

Al ejecutar trabajos de MapReduce, estos son los parámetros más importantes que se pueden configurar para aumentar el rendimiento en ADLS:

* **Mapreduce.map.memory.mb**: la cantidad de memoria que se asignará a cada asignador.
* **MapReduce.job.maps**: el número de tareas de asignación por trabajo.
* **Mapreduce.reduce.memory.mb**: la cantidad de memoria que se asignará a cada reductor.
* **Mapreduce.job.reduces**: el número de tareas de reducción por trabajo.

**Mapreduce.map.memory / Mapreduce.reduce.memory**: este número se debe ajustar según la cantidad de memoria necesaria para la tarea de asignación o reducción.  Los valores predeterminados de mapreduce.map.memory y mapreduce.reduce.memory pueden verse en Ambari a través de la configuración de Yarn.  En Ambari, vaya a YARN y vea la pestaña Configs (Configuraciones).  Se mostrará la memoria YARN.  

**Mapreduce.job.maps / Mapreduce.job.reduces**: determina el número máximo de asignadores o reductores que se crearán.  El número de divisiones determinará cuántos asignadores se crearán para el trabajo de MapReduce.  Por lo tanto, puede que obtenga menos asignadores que los solicitados si hay menos divisiones que el número de asignadores solicitado.       

## <a name="guidance"></a>Guía

**Paso 1: Determinación del número de trabajos que se ejecutan**: de forma predeterminada, MapReduce usará todo el clúster para el trabajo.  Puede usar una cantidad menor del clúster usando menos asignadores que contenedores haya disponibles.  En las instrucciones de este documento se asume que la aplicación es la única aplicación que se ejecuta en el clúster.      

**Paso 2: Establecimiento de mapreduce.map.memory/mapreduce.reduce.memory**: el tamaño de la memoria para las tareas de asignación y reducción dependerá del trabajo específico.  Puede reducir el tamaño de memoria si quiere aumentar la simultaneidad.  El número de tareas en ejecución al mismo tiempo depende del número de contenedores.  Al reducir la cantidad de memoria por asignador o reductor, pueden crearse más contenedores, que permiten que más asignadores o reductores se ejecuten a la vez.  Si se reduce demasiado la cantidad de memoria puede provocar que algunos procesos se ejecuten con memoria insuficiente.  Si recibe un error de heap cuando se ejecuta el trabajo, debe aumentar la memoria por asignador o reductor.  Debe tener en cuenta que agregar más contenedores supondrá una sobrecarga adicional para cada contenedor de más, lo que podría reducir el rendimiento.  Otra alternativa consiste en obtener más memoria mediante un clúster que tenga mayor cantidad de memoria o aumentar el número de nodos en el clúster.  Más memoria permitirá que se usen más contenedores, lo que se traduce en una mayor simultaneidad.  

**Paso 3: Determinación de la memoria de YARN total**: para optimizar mapreduce.job.maps/mapreduce.job.reduces, debería considerar la cantidad de memoria YARN total disponible para su uso.  Esta información está disponible en Ambari.  Vaya a YARN y examine la pestaña de configuración.  En esta ventana se muestra el tamaño de la memoria de YARN.  Para obtener la memoria de YARN total, debe multiplicar la memoria de YARN por el número de nodos que tiene en el clúster.

    Total YARN memory = nodes * YARN memory per node
Si va a usar un clúster vacío, la memoria puede ser la memoria de YARN total para en el clúster.  Si otras aplicaciones usan memoria, puede elegir usar solamente una parte de la memoria del clúster; para ello, reduzca el número de asignadores o reductores al número de contenedores que quiere usar.  

**Paso 4: Cálculo del número de contenedores YARN**: los contenedores YARN dictaminan la cantidad de simultaneidad disponible para el trabajo.  Tome la memoria de YARN total y divídala entre el valor de mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Paso 5: Establecer mapreduce.job.maps/mapreduce.job.reduces** Establece mapreduce.job.maps/mapreduce.job.reduces en al menos el número de contenedores disponibles.  Puede seguir experimentando y aumentar el número de asignadores y reductores para ver si obtiene mejor rendimiento.  Tenga en cuenta que cuantos más asignadores más sobrecarga, por lo que tener demasiados asignadores puede reducir el rendimiento.  

Nota: la programación de CPU y el aislamiento de CPU están desactivadas de forma predeterminada, de modo que el número de contenedores de YARN está restringido por la memoria.

## <a name="example-calculation"></a>Cálculo de ejemplo

Supongamos que tiene actualmente un clúster compuesto de 8 nodos D14 y quiere ejecutar un trabajo de uso intensivo de E/S.  Estos son los cálculos que se debe realizar:

**Paso 1: Determinación del número de trabajos que se ejecutan**: en nuestro ejemplo, suponemos que nuestro trabajo es el único en ejecución.  

**Paso 2: Configuración de mapreduce.map.memory/mapreduce.reduce.memory**: en nuestro ejemplo, está ejecutando un trabajo de uso intensivo de E/S y decide que 3 GB de memoria para las tareas de asignación será suficiente.

    mapreduce.map.memory = 3GB
**Paso 3: Determinación de la memoria de YARN total**

    total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Paso 4: Cálculo del número de contenedores de YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Paso 5: Establecer mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="limitations"></a>Limitaciones

**Limitación de ADLS**

Como servicio multiinquilino, ADLS establece los límites de ancho de banda de nivel de cuenta.  Si se alcanzan estos límites, comenzará a ver errores de tarea. Para identificar esta situación, observe los errores de limitación en los registros de tareas.  Si necesita más ancho de banda para su trabajo, póngase en contacto con nosotros.   

Para comprobar si le están aplicando limitaciones, debe habilitar el registro de depuración en el lado del cliente. Así es cómo debe hacerlo:

1. Coloque la siguiente propiedad en las propiedades log4j en Ambari > YARN > Config (Configuración) > Advanced (Opciones avanzadas) yarn-log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Reinicie todos los nodos/servicios para que la configuración surta efecto.

3. Si se le están aplicando límites, verá el código de error HTTP 429 en el archivo de registro de YARN. El archivo de registro de YARN se encuentra en /tmp/&lt;user&gt;/yarn.log

## <a name="examples-to-run"></a>Ejemplos de ejecución

Para demostrar cómo MapReduce se ejecuta en Azure Data Lake Store, a continuación se muestra código de ejemplo que se ejecutó en un clúster con la siguiente configuración:

* 16 nodos D14v2
* Clúster de Hadoop con HDI 3.6

Como punto de partida, estos son algunos comandos de ejemplo para ejecutar MapReduce: Teragen, Terasort y Teravalidate.  Puede ajustar estos comandos en función de los recursos.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
