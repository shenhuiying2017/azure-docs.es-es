---
title: 'Opciones de contexto de proceso para R Server en HDInsight: Azure | Microsoft Docs'
description: Conozca las distintas opciones de contexto de proceso disponibles para los usuarios con R Server en HDInsight.
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 0deb0b1c-4094-459b-94fc-ec9b774c1f8a
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 47f4441612be4f363ba82cc22b09786a6f3bfdc3
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="compute-context-options-for-r-server-on-hdinsight"></a>Opciones de contexto de proceso para R Server en HDInsight (versión preliminar)

Microsoft R Server en Azure HDInsight controla cómo se ejecutan las llamadas estableciendo el contexto de proceso. En este artículo se describen las opciones que están disponibles para especificar si la ejecución se realiza o no en paralelo y, en caso afirmativo, cómo se lleva a cabo a través del nodo perimetral o del clúster de HDInsight.

El nodo perimetral de un clúster proporciona un lugar conveniente para conectarse al clúster y ejecutar los scripts de R. Con un nodo perimetral, tiene la opción de ejecutar las funciones distribuidas paralelizadas de ScaleR en los diferentes núcleos del servidor de nodo perimetral. También puede ejecutarlas en los nodos del clúster utilizando los contextos de proceso de Spark o Hadoop MapReduce de ScaleR.

## <a name="microsoft-r-server-on-azure-hdinsight"></a>Microsoft R Server en Azure HDInsight
[Microsoft R Server en Azure HDInsight](hdinsight-hadoop-r-server-overview.md) proporciona las últimas funcionalidades para análisis basado en R. Puede usar datos almacenados en un contenedor HDFS en su cuenta de almacenamiento de [Azure Blob](../storage/common/storage-introduction.md "Azure Blob Storage"), un almacén de Data Lake Store o el sistema de archivos local de Linux. Como R Server se basa en el lenguaje R de código abierto integrado, las aplicaciones basadas en R que se crean aplican cualquiera de los más de 8000 paquetes de R de código abierto. También pueden utilizar las rutinas de [RevoScaleR](https://msdn.microsoft.com/microsoft-r/scaler/scaler), un paquete de análisis de macrodatos de Microsoft que se incluye con R Server.  

## <a name="compute-contexts-for-an-edge-node"></a>Contextos de proceso de un nodo perimetral
En general, el script de R que se ejecuta en el nodo perimetral de R Server lo hace dentro del intérprete de R de ese nodo. Las excepciones son esos pasos que llaman a una función ScaleR. Las llamadas a ScaleR se ejecutan en un entorno de proceso determinado por la manera en que establece el contexto de proceso de ScaleR.  Al ejecutar el script de R desde un nodo perimetral, los posibles valores del contexto de proceso son:

- secuencial local (*‘local’*)
- paralelo local (*‘localpar’*)
- MapReduce
- Spark

Las opciones *‘local’* y *‘localpar’* solo difieren en cómo se ejecutan las llamadas de **rxExec**. Las dos ejecutan otras llamadas a función de rx de manera paralela en todos los núcleos disponibles, a menos que se especifique lo contrario mediante el uso de la opción **numCoresToUse** de ScaleR; por ejemplo, `rxOptions(numCoresToUse=6)`. Las opciones de ejecución en paralelo ofrecen un rendimiento óptimo.

En la tabla siguiente se resumen las distintas opciones de contexto de proceso para establecer cómo se ejecutan las llamadas:

| Contexto de proceso  | Cómo definir                      | Contexto de ejecución                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Secuencial local | rxSetComputeContext(‘local’)    | Ejecución en paralelo en los núcleos del servidor de nodo perimetral, excepto las llamadas de rxExec, que se ejecutan en serie |
| Paralelo local   | rxSetComputeContext(‘localpar’) | Ejecución paralelizada en los núcleos del servidor de nodo perimetral |
| Spark            | RxSpark()                       | Ejecución distribuida paralelizada vía Spark en los nodos del clúster de HDI |
| MapReduce       | RxHadoopMR()                    | Ejecución distribuida paralelizada vía MapReduce en los nodos del clúster de HDI |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Directrices para decidir en un contexto de proceso

¿Cuál de las tres opciones que elija que proporcionan la ejecución en paralelo depende de la naturaleza del trabajo de análisis, el tamaño y la ubicación de los datos? No hay ninguna fórmula sencilla que indique qué contexto de proceso ejecutar. Sin embargo, hay algunos principios fundamentales que pueden ayudarle a tomar la decisión correcta o, al menos, a reducir sus opciones antes de ejecutar una prueba comparativa. Estos principios rectores incluyen:

- El sistema de archivos de Linux local es más rápido que HDFS.
- Los análisis repetidos son más rápidos si los datos son locales y están en XDF.
- Es preferible transmitir pequeñas cantidades de datos desde un origen de datos de texto. Si la cantidad de datos es mayor, conviértala a XDF antes del análisis.
- La sobrecarga de copiar o transmitir los datos al nodo perimetral para su análisis se convierte en algo difícil de manejar en el caso de cantidades de datos muy grandes.
- Spark es más rápido que MapReduce para el análisis de Hadoop.

Con estos principios, las siguientes secciones ofrecen algunas reglas generales para seleccionar un contexto de proceso.

### <a name="local"></a>Local
* Si la cantidad de datos que se va a analizar es pequeña y no requiere un análisis repetido, transmítalos directamente a la rutina de análisis utilizando *'local'* o *'localpar'*.
* Si la cantidad de datos que se va a analizar es pequeña o mediana y requiere análisis repetido, cópielos en el sistema de archivos local, impórtelos a XDF y analícelos mediante *'local'* o *'localpar'*.

### <a name="hadoop-spark"></a>Hadoop Spark
* Si la cantidad de datos que se va a analizar es grande, impórtelos a Spark DataFrame mediante **RxHiveData** o **RxParquetData**, o a XDF en HDFS (a no ser que el almacenamiento sea un problema), y analícelos mediante el contexto de proceso de Spark.

### <a name="hadoop-map-reduce"></a>Hadoop MapReduce
* Utilice el contexto de proceso de MadReduce solo si detecta un problema insuperable con el contexto de procesos de Spark, ya que generalmente es más lento.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Ayuda insertada en rxSetComputeContext
Para más información sobre los contextos de proceso de ScaleR y ver algunos ejemplos, consulte la ayuda insertada en R en el método rxSetComputeContext, por ejemplo:

    > ?rxSetComputeContext

También puede consultar el documento "[ScaleR Distributed Computing Guide](https://msdn.microsoft.com/microsoft-r/scaler-distributed-computing)" (Guía de informática distribuida de ScaleR) que se encuentra disponible en la biblioteca [R Server MSDN](https://msdn.microsoft.com/library/mt674634.aspx "R Server en MSDN").

## <a name="next-steps"></a>Pasos siguientes
En este artículo ha aprendido las opciones que están disponibles para especificar si la ejecución se realiza o no en paralelo y, en caso afirmativo, cómo se lleva a cabo a través del nodo perimetral o del clúster de HDInsight. Para más información sobre cómo usar R Server con clústeres de HDInsight, consulte los temas siguientes:

* [Información general sobre R Server en Hadoop](hdinsight-hadoop-r-server-overview.md)
* [Introducción a R Server en Hadoop](hdinsight-hadoop-r-server-get-started.md)
* [Instalación de RStudio con R Server en HDInsight (si no está instalado durante la creación del clúster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Opciones de Azure Storage para R Server en HDInsight](hdinsight-hadoop-r-server-storage.md)

