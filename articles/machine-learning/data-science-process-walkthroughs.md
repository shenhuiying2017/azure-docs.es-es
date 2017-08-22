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
ms.date: 07/25/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: c0f0ff599909234b2b43f2b8512d77f9053b8a09
ms.contentlocale: es-es
ms.lasthandoff: 07/26/2017

---
# <a name="team-data-science-process-walkthroughs"></a>Tutoriales del proceso de ciencia de datos en equipos
Los **tutoriales completos** detallados aquí demuestran los pasos del proceso de ciencia de datos en equipos para **escenarios específicos**. Se ilustra cómo combinar servicios y herramientas en la nube y locales en un flujo de trabajo o una canalización con el fin de crear una **aplicación inteligente**. Los tutoriales se agrupan por la **plataforma** que usan: 

- Spark con PySpark y Scala
- HDInsight (Hadoop)
- Azure Data Lake 
- SQL Server
- SQL Data Warehouse 


## <a name="hdinsight-spark-using-pyspark-and-scala"></a>HDInsight Spark con PySpark y Scala

- En el tutorial [Usar Spark en Azure HDInsight](machine-learning-data-science-spark-overview.md) se usa el proceso de ciencia de datos en equipos en un escenario con un [clúster de Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) para almacenar y explorar los datos del conjunto de datos de carreras y tarifas de taxi de la ciudad de Nueva York disponible públicamente, así como para diseñar sus características.

- En el tutorial [Usar Scala con Spark en Azure](machine-learning-data-science-process-scala-walkthrough.md) se muestra cómo usar Scala para realizar tareas supervisadas de aprendizaje automático con la biblioteca de aprendizaje automático de Spark (MLlib) y los paquetes de SparkML en un clúster Azure HDInsight Spark. Además, explicaremos cuáles son las tareas que constituyen el [proceso de ciencia de datos](http://aka.ms/datascienceprocess): exploración e ingesta de datos, visualización, ingeniería de características, modelado y consumo de modelos. Los modelos creados incluyen regresión logística y lineal, bosques aleatorios y árboles impulsados por gradiente:


## <a name="hdinsight-hadoop"></a>HDInsight Hadoop 

- En el tutorial [Uso de clústeres de Hadoop de Azure HDInsight](machine-learning-data-science-process-hive-walkthrough.md) se usa un [clúster de Hadoop en Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) para almacenar y explorar los datos del conjunto de datos de carreras y tarifas de taxi de la ciudad de Nueva York disponible públicamente, así como para diseñar sus características.

- En el tutorial [Uso de un clúster de Hadoop de Azure HDInsight en un conjunto de datos de 1 TB](machine-learning-data-science-process-hive-criteo-walkthrough.md) se presenta un escenario en el que se usa un [clúster de Hadoop de Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) para almacenar, explorar, diseñar las características y reducir la muestra de datos de uno de los conjuntos de datos de [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) disponibles públicamente.


## <a name="azure-data-lake"></a>Azure Data Lake

- En el tutorial [Ciencia de datos escalables en Azure Data Lake: tutorial completo](machine-learning-data-science-process-data-lake-walkthrough.md) se muestra cómo usar Azure Data Lake para realizar las tareas de exploración de datos y clasificación binaria en una muestra del conjunto de datos de taxi de la ciudad de Nueva York para predecir si el cliente dejará una propina o no. 


## <a name="sql-server-and-sql-data-warehouse"></a>SQL Server y SQL Data Warehouse 

- En el tutorial sobre [uso de SQL Data Warehouse](machine-learning-data-science-process-sqldw-walkthrough.md) se muestra cómo crear e implementar modelos de clasificación y regresión de aprendizaje automático mediante SQL Data Warehouse (SQL DW) para un conjunto de datos de carreras y tarifas de taxi de la ciudad de Nueva York disponible públicamente.

- En el tutorial sobre [uso de SQL Server](machine-learning-data-science-process-sql-walkthrough.md) se muestra cómo crear e implementar modelos de clasificación y regresión de aprendizaje automático mediante SQL Server y un conjunto de datos de carreras y tarifas de taxi de la ciudad de Nueva York disponible públicamente.

- En el tutorial sobre [uso de SQL Server R Services](https://msdn.microsoft.com/library/mt612857.aspx) se proporciona a los científicos de datos una combinación de código de R, datos de SQL Server y funciones SQL personalizadas para compilar e implementar un modelo de R en SQL Server.

- En el tutorial sobre [uso de T-SQL con SQL Server R Services](https://msdn.microsoft.com/library/mt683480.aspx) se proporciona a los programadores de SQL experiencia para la creación de una solución de análisis avanzada con Transact-SQL mediante SQL Server R Services para hacer operativa una solución de R.

- En el tutorial sobre [uso de T-SQL con servicios de Python de SQL Server](https://docs.microsoft.com/en-us/sql/advanced-analytics/tutorials/sqldev-in-database-python-for-sql-developers) se proporciona a los programadores de SQL la experiencia de crear una solución de aprendizaje automático en SQL Server. Se muestra cómo incorporar Python a una aplicación mediante la adición de código Python a los procedimientos almacenados.

## <a name="whats-next"></a>Pasos siguientes
Para ver una introducción a los temas que lo guiarán por las tareas que componen el proceso de ciencia de datos en Azure, consulte [Proceso de ciencia de los datos en equipos (TDSP)](http://aka.ms/datascienceprocess). 


