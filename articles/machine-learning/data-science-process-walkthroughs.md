---
title: Tutoriales del proceso de ciencia de los datos en equipos | Microsoft Docs
description: "En los tutoriales se muestra cómo combinar servicios y herramientas en la nube y locales en un flujo de trabajo o una canalización con el fin de crear una aplicación inteligente."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aa63d5a5-25ee-4c4b-9a4c-7553b98d7f6e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: bradsev
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 6df3fc533b0c375cd85743a971c778071629bb7e
ms.contentlocale: es-es
ms.lasthandoff: 07/01/2017


---
# <a name="team-data-science-process-walkthroughs"></a>Tutoriales del proceso de ciencia de datos en equipos
Los **tutoriales completos** detallados aquí demuestran los pasos del proceso de ciencia de datos en equipos para **escenarios específicos**. Se ilustra cómo combinar servicios y herramientas en la nube y locales en un flujo de trabajo o una canalización con el fin de crear una **aplicación inteligente**. Los tutoriales se agrupan por la **plataforma** que usan: Spark, HDInsight (Hadoop), Azure Data Lake y SQL Server.


## <a name="hdinsight-spark-using-pyspark-and-scala"></a>HDInsight Spark con PySpark y Scala

### <a name="data-science-using-python-with-spark-on-azure"></a>Ciencia de datos mediante Python con Spark en Azure
En el tutorial [Ciencia de datos mediante Spark en HDInsight de Azure](machine-learning-data-science-spark-overview.md) se usa el proceso de ciencia de datos en equipos en un escenario con un [clúster de Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) para almacenar y explorar los datos del conjunto de datos de carreras y tarifas de taxi de la ciudad de Nueva York disponible públicamente, así como para diseñar sus características. 

### <a name="data-science-using-scala-with-spark-on-azure"></a>Ciencia de datos mediante Scala con Spark en Azure
En el tutorial [Ciencia de datos mediante Scala con Spark en Azure](machine-learning-data-science-process-scala-walkthrough.md) se muestra cómo usar Scala para realizar tareas supervisadas de aprendizaje automático con la biblioteca de aprendizaje automático de Spark (MLlib) y los paquetes de SparkML en un clúster Azure HDInsight Spark. Además, explicaremos cuáles son las tareas que constituyen el [proceso de ciencia de datos](http://aka.ms/datascienceprocess): exploración e ingesta de datos, visualización, ingeniería de características, modelado y consumo de modelos. Los modelos creados incluyen regresión logística y lineal, bosques aleatorios y árboles impulsados por gradiente:


## <a name="hdinsight-hadoop"></a>HDInsight Hadoop 

### <a name="use-hdinsight-hadoop-clusters"></a>Uso de clústeres de Hadoop en HDInsight
En el tutorial sobre el [proceso de ciencia de datos en equipo en acción mediante clústeres de Hadoop en HDInsight](machine-learning-data-science-process-hive-walkthrough.md), usamos un [clúster de HDInsight de Azure](https://azure.microsoft.com/services/hdinsight/) para almacenar, explorar y estudiar las características de los datos desde el punto de vista de los ingenieros del conjunto de datos NYC Taxi Trips.

### <a name="use-azure-hdinsight-hadoop-clusters-on-a-1-tb-dataset"></a>Uso de clústeres de Hadoop en Azure HDInsight en un conjunto de datos de 1 TB
En el tutorial sobre el [proceso de ciencia de datos en equipos en acción mediante clústeres de Hadoop en HDInsight de Azure en un conjunto de datos de 1 TB](machine-learning-data-science-process-hive-criteo-walkthrough.md) se presenta un escenario que usa un [clúster de Hadoop de HDInsight de Azure](https://azure.microsoft.com/services/hdinsight/) para almacenar, explorar, diseñar las características y reducir la muestra de datos de uno de los conjuntos de datos de [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) disponibles públicamente.


## <a name="azure-data-lake"></a>Azure Data Lake

### <a name="use-azure-data-lake-store-and-analytics"></a>Usar Azure Data Lake Store y Analytics
En el tutorial [Ciencia de datos escalables en Azure Data Lake: tutorial completo](machine-learning-data-science-process-data-lake-walkthrough.md) se muestra cómo usar Azure Data Lake para realizar las tareas de exploración de datos y clasificación binaria en un ejemplo del conjunto de datos NYC taxi para predecir si se dará una propina por tarifa. 


## <a name="sql-server-and-sql-data-warehouse"></a>SQL Server y SQL Data Warehouse 

### <a name="use-sql-data-warehouse"></a>Uso de Almacenamiento de datos SQL
En el tutorial sobre el [proceso de ciencia de datos en equipo en acción mediante Almacenamiento de datos SQL](machine-learning-data-science-process-sqldw-walkthrough.md) se muestra cómo crear e implementar modelos de clasificación y regresión de aprendizaje automático mediante Almacenamiento de datos SQL (SQL DW) para un conjunto de datos de carreras y tarifas de taxi de la ciudad de Nueva York públicamente disponible.

### <a name="use-sql-server"></a>Uso de SQL Server
En el tutorial sobre el [proceso de ciencia de datos en equipo en acción mediante SQL Server](machine-learning-data-science-process-sql-walkthrough.md) se muestra cómo crear e implementar modelos de clasificación y regresión de aprendizaje automático mediante SQL Server y un conjunto de datos de carreras y tarifas de taxi de la ciudad de Nueva York públicamente disponible.

### <a name="use-r-with-sql-server-r-services"></a>Uso de R con R Services de SQL Server
En el [Tutorial completo de ciencia de datos con SQL Server R Services](https://msdn.microsoft.com/library/mt612857.aspx) se proporciona a los científicos de datos una combinación de código R, datos de SQL Server y funciones SQL personalizadas para compilar e implementar un modelo R en SQL Server.

### <a name="use-t-sql-with-sql-server-r-services"></a>Uso de T-SQL con R Services de SQL Server
En el tutorial [Análisis avanzados en bases de datos para desarrolladores de SQL](https://msdn.microsoft.com/library/mt683480.aspx) se proporciona a los programadores de SQL experiencia para la creación de una solución de análisis avanzada con Transact-SQL mediante R Services de SQL Server para poner en operación una solución de R.


### <a name="use-t-sql-with-sql-server-python-services"></a>Usar T-SQL con servicios de Python de SQL Server
En el tutorial sobre [análisis de Python en bases de datos para desarrolladores de SQL](https://docs.microsoft.com/en-us/sql/advanced-analytics/tutorials/sqldev-in-database-python-for-sql-developers) se proporciona a los programadores de SQL la experiencia de crear una solución de aprendizaje automático en SQL Server. Se muestra cómo incorporar Python a una aplicación mediante la adición de código Python a los procedimientos almacenados.

## <a name="whats-next"></a>Pasos siguientes
Para ver una introducción a los temas que lo guiarán por las tareas que componen el proceso de ciencia de datos en Azure, consulte [Proceso de ciencia de los datos en equipos (TDSP)](http://aka.ms/datascienceprocess). 


