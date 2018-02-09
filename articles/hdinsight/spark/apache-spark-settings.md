---
title: "Configuración de las opciones de Spark: Azure HDInsight | Microsoft Docs"
description: "Cómo configurar Spark para un clúster de HDInsight."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: maxluk
ms.openlocfilehash: 1dd0ff26cdb39feacec697d7900ad7abaa5f1996
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="configure-spark-settings"></a>Configuración de opciones de Spark

Un clúster de HDInsight Spark incluye una instalación de la biblioteca de Apache Spark.  Cada clúster de HDInsight incluye parámetros de configuración predeterminados para todos sus servicios instalados, incluido Spark.  Un aspecto clave de la administración de un clúster de HDInsight Hadoop es la supervisión de la carga de trabajo, incluido los trabajos de Spark, para asegurarse de que están ejecutándose de una manera predecible. Para ejecutar mejor los trabajos de Spark, considere la configuración física del clúster al determinar cómo optimizar la configuración lógica del clúster.

El clúster de HDInsight Apache Spark predeterminado incluye los siguientes nodos: tres nodos de ZooKeeper, dos nodos principales y uno o más nodos de trabajo:

![Arquitectura de HDInsight Spark](./media/apache-spark-settings/spark-hdinsight-arch.png)

El número de máquinas virtuales y el tamaño de las máquinas virtuales, para los nodos del clúster de HDInsight, también pueden afectar a la configuración de Spark. Los valores de la configuración HDInsight no predeterminados a menudo requieren valores de configuración de Spark no predeterminados. Al crear un clúster de HDInsight Spark, se muestran los tamaños sugeridos de la máquina virtual para cada uno de los componentes. Actualmente los [tamaños de la máquina virtual Linux optimizado para memoria](../../virtual-machines/linux/sizes-memory.md) para Azure son D12 v2 o posterior.

## <a name="spark-versions"></a>Versiones de Spark

También debería considerar la mejor versión de Spark para el clúster.  Spark 2.x puede ejecutar mucho mejor que Spark 1.x. Spark 2.x tiene un número de optimizaciones de rendimiento, como Tungsten, optimización de consulta de Catalyst y mucho más.  El servicio HDInsight incluye varias versiones tanto de Spark como del mismo HDInsight.  Cada versión de Spark incluye un conjunto de configuración de clúster predeterminadas.  Al crear un nuevo clúster, estas son las versiones actuales de Spark para elegir:

![Versiones de Spark](./media/apache-spark-settings/spark-version.png)

> [!NOTE]
> La versión predeterminada de Apache Spark en el servicio HDInsight puede cambiar sin previo aviso. Microsoft recomienda especificar la versión particular al crear clústeres con el .NET SDK, Azure PowerShell y la CLI de Azure, si tiene una dependencia de versiones.

Apache Spark cuenta con tres ubicaciones de configuración del sistema:

* Las propiedades de Spark controlan la mayoría de los parámetros de la aplicación y se pueden establecer mediante el uso de un objeto `SparkConf` o a través de las propiedades del sistema de Java.
* Se pueden utilizar variables de entorno para establecer la configuración por máquina, como la dirección IP, a través del script `conf/spark-env.sh` en cada nodo.
* Puede configurar el registro mediante `log4j.properties`.

Al seleccionar una versión concreta de Spark, el clúster incluye las opciones de configuración predeterminadas.  Puede cambiar los valores de configuración predeterminados de Spark proporcionando un archivo de configuración personalizado de Spark.  A continuación se muestra un ejemplo.

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

El ejemplo mostrado antes reemplaza varios valores predeterminados en cinco parámetros de configuración de Spark.  Estos son el códec de compresión, el tamaño mínimo de división de Hadoop MapReduce y los tamaños de bloques de Parquet, así como la partición Spar SQL y los valores predeterminados de los tamaños de archivos abiertos.  Estos cambios de configuración se eligen porque los datos y trabajos asociados (en este ejemplo, datos genómicos) tienen características particulares que se ejecutarán mejor con estos ajustes de configuración personalizados.

---

## <a name="view-cluster-configuration-settings"></a>Visualización de las opciones de configuración del clúster

Compruebe las opciones de configuración actuales del clúster de HDInsight antes de realizar la optimización del rendimiento en el clúster. Para iniciar el panel de HDInsight desde Azure Portal, haga clic en el vínculo **Panel** en el panel del clúster de Spark. Inicie sesión en el nombre de usuario y la contraseña del administrador del clúster.

Aparece la interfaz de usuario de Ambari Web, con una vista del panel de las principales métricas de utilización de los recursos del clúster.  El panel de Ambari muestra la configuración de Apache Spark, así como los otros servicios que haya instalado. El panel incluye una pestaña con el **historial de configuración**, donde puede ver información de configuración de todos los servicios instalados, incluido Spark.

Para ver los valores de configuración de Apache Spark, seleccione **Config History** (Historial de configuración) y, después, seleccione **Spark2**.  Seleccione la pestaña **Configs** (Configuraciones) y, después, seleccione el vínculo `Spark` (o `Spark2`, dependiendo de la versión) en la lista de servicios.  Verá una lista de valores de configuración para el clúster:

![Configuraciones de Spark](./media/apache-spark-settings/spark-config.png)

Para ver y cambiar los valores de configuración individuales de Spark, seleccione cualquier vínculo con la palabra "spark" en el título del vínculo.  Las configuraciones para Spark incluyen tanto valores de configuración personalizados como avanzados en estas categorías:

* Custom Spark2-defaults
* Custom Spark2-metrics-properties
* Advanced Spark2-defaults
* Advanced Spark2-env
* Advanced spark2-hive-site-override

Si crea un conjunto de valores de configuración no predeterminados, también puede ver el historial de las actualizaciones de configuración.  Este historial de configuración puede ser útil para ver qué configuración no predeterminada tiene un rendimiento óptimo.

> [!NOTE]
> Para ver, pero no cambiar, los valores comunes de configuración del clúster de Spark, seleccione la pestaña **Environment** (Entorno) en la interfaz **Spark Job UI** de nivel superior.

## <a name="configuring-spark-executors"></a>Configuración de los ejecutores de Spark

El diagrama siguiente muestra los principales objetos de Spark: el programa del controlador y su contexto de Spark asociado, y el administrador del clúster y sus nodos de trabajo *n*.  Cada nodo de trabajo incluye un ejecutor, una caché y las instancias de tareas *n*.

![Objetos de clúster](./media/apache-spark-settings/spark-arch.png)

Los trabajos de Spark utilizan los recursos del nodo de trabajo, en particular la memoria, por lo que es común ajustar los valores de configuración de Spark para los ejecutores de los nodos de trabajo.

Tres parámetros clave que a menudo se ajustan para optimizar las configuraciones de Spark para mejorar los requisitos de la aplicación son `spark.executor.instances`, `spark.executor.cores` y `spark.executor.memory`. Un ejecutor es un proceso que se inicia para una aplicación Spark. Un ejecutor se ejecuta en el nodo de trabajo y es responsable de realizar las tareas de la aplicación. Para cada clúster, el número predeterminado de ejecutores y el tamaño de estos se calcula en función del número de nodos de trabajo y el tamaño de estos. Estos se almacenan en `spark-defaults.conf` en los nodos principales del clúster.  Puede modificar estos valores en un clúster en ejecución mediante la selección del vínculo **Custom spark-defaults** en la interfaz de usuario de Ambari Web.  Después de realizar cambios, la interfaz de usuario le solicitará que **reinicie** todos los servicios afectados.

> [!NOTE]
> Estos tres parámetros de configuración se pueden configurar en el nivel de clúster (para todas las aplicaciones que se ejecutan en el clúster) y especificar también para cada aplicación individual.

Otra fuente de información sobre los recursos que están utilizando los ejecutores de Spark es la interfaz de usuario de la aplicación de Spark.  En la interfaz de usuario de Spark, seleccione la pestaña **Executors** (Ejecutores) para mostrar vistas de resumen y de detalle de la configuración, así como los recursos utilizados por el ejecutor.  Estas vistas pueden ayudarle a determinar si desea cambiar los valores predeterminados de los ejecutores de Spark para todo el clúster o un conjunto particular de ejecuciones de trabajos.

![Ejecutores de Spark](./media/apache-spark-settings/spark-executors.png)

Como alternativa, puede usar la API de REST de Ambari para comprobar mediante programación los valores de configuración del clúster de HDInsight y Spark.  Hay disponible más información en la [referencia de la API de Ambari en GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

En función de la carga de trabajo de Spark, puede determinar que una configuración de Spark no predeterminada proporcione ejecuciones de trabajos de Spark más optimizadas.  Debe realizar pruebas comparativas con cargas de trabajo de ejemplo para validar las configuraciones de clúster no predeterminadas.  Algunos de los parámetros comunes que puede considerar ajustar son:

* `--num-executors` establece el número de ejecutores.
* `--executor-cores` establece el número de núcleos para cada ejecutor. Le recomendamos usar ejecutores de tamaño medio, ya que otros procesos consumen también parte de la memoria disponible.
* `--executor-memory` controla el tamaño de la memoria (tamaño del montón) de cada ejecutor en YARN, y deberá dejar algo de memoria para la sobrecarga de ejecución.

Este es un ejemplo de dos nodos de trabajo con diferentes valores de configuración:

![Configuraciones de dos nodos](./media/apache-spark-settings/executor-config.png)

La siguiente lista muestra los parámetros clave de la memoria del ejecutor de Spark.

* `spark.executor.memory` define la cantidad total de memoria disponible para un ejecutor.
* `spark.storage.memoryFraction` (valor predeterminado de aproximadamente 60 %) define la cantidad de memoria disponible para almacenar RDD persistentes.
* `spark.shuffle.memoryFraction` (valor predeterminado de aproximadamente 20 %) define la cantidad de memoria reservada para el orden aleatorio.
* `spark.storage.unrollFraction` y `spark.storage.safetyFraction` (suman un total de aproximadamente el 30 % del total de memoria); estos valores los utiliza internamente Spark y no deben cambiarse.

YARN controla la suma máxima de memoria que usan los contenedores en cada nodo de Spark. En el siguiente diagrama se muestran las relaciones por nodos entre objetos de configuración de YARN y objetos de Spark.

![Administración de memoria de Spark para YARN](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Cambio de los parámetros de una aplicación que se ejecuta en un cuaderno de Jupyter Notebook

Los clústeres de Spark en HDInsight incluyen un número de componentes de forma predeterminada. Cada uno de estos componentes incluyen los valores de configuración predeterminados que se pueden reemplazar según sea necesario.

* Spark Core: Spark Core, Spark SQL, API de Spark Streaming, GraphX y MLlib
* Anaconda: un administrador de paquetes de Python
* Livy: API de REST de Apache Spark que se usa para enviar trabajos remotos a un clúster Spark de HDInsight
* Cuadernos de Jupyter y Zeppelin: interfaz de usuario interactiva basada en explorador para interactuar con el clúster de Spark
* Controlador ODBC: conecta clústeres de Spark en HDInsight con herramientas de inteligencia empresarial (BI), como Microsoft Power BI y Tableau

Para las aplicaciones que se ejecutan en un cuaderno de Jupyter Notebook, puede utilizar el comando `%%configure` para realizar los cambios de configuración dentro del mismo cuaderno. Estos cambios de configuración se aplicarán a la ejecución de trabajos de Spark desde la instancia del cuaderno. Debe realizar estos cambios al comienzo de la aplicación, antes de ejecutar la primera celda de código. La configuración modificada se aplica a la sesión de Livy cuando esta se cree.

> [!NOTE]
> Para cambiar la configuración en una fase posterior de la aplicación, debe utilizar el parámetro `-f` (force). Sin embargo, se perderá todo el progreso en la aplicación.

El código siguiente muestra cómo cambiar la configuración de una aplicación que se ejecuta en un cuaderno de Jupyter Notebook.

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Conclusión

Hay una serie de valores de configuración básicos que necesita supervisar y ajustar para garantizar que los trabajos de Spark se ejecuten de forma predecible y eficaz. Estas opciones ayudan a determinar la mejor configuración del clúster de Spark para las cargas de trabajo determinadas.  También debe supervisar la ejecución de los trabajos de Spark de larga duración o que consumen recursos.  La mayor dificultad se centra en la presión de memoria, debido a las configuraciones inadecuadas (en especial, al tamaño incorrecto de los ejecutores), las operaciones de ejecución prolongada y las tareas que dan lugar a operaciones cartesianas.

## <a name="next-steps"></a>pasos siguientes

* [¿Cuáles son los componentes y versiones de Hadoop disponibles con HDInsight?](../hdinsight-component-versioning.md)
* [Administración de recursos de un clúster Spark en HDInsight](apache-spark-resource-manager.md)
* [Configuración de clústeres en HDInsight con Hadoop, Spark, Kafka, etc.](../hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Spark Configuration](https://spark.apache.org/docs/latest/configuration.html) (Configuración de Apache Spark)
* [Running Spark on YARN](https://spark.apache.org/docs/latest/running-on-yarn.html) (Ejecución de Spark en YARN)
