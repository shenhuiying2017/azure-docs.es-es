---
title: "Creación de un clúster de Apache Spark en Azure HDInsight | Microsoft Docs"
description: "Tutorial de inicio rápido para HDInsight Spark sobre cómo crear un clúster de Apache Spark en HDInsight."
keywords: "inicio rápido para spark,spark interactivo,consulta interactiva,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 516c48424ef5d1256296240541fb544c1e5d9205
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Creación de un clúster de Apache Spark en Azure HDInsight

Aprenda cómo crear clústeres de Apache Spark en Azure HDInsight y cómo ejecutar consultas SQL de Spark en tablas de Hive. Para obtener información sobre Spark en HDInsight, consulte [Introducción a Apache Spark en HDInsight de Azure](apache-spark-overview.md).

## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Antes de comenzar este tutorial, debe tener una suscripción a Azure. Consulte [Cree su cuenta gratuita de Azure hoy mismo](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>Creación de un clúster de HDInsight Spark

Cree un clúster de Spark en HDInsight mediante una [plantilla de Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md). La plantilla se encuentra en [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Para conocer otros métodos de creación de clústeres, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Haga clic en la imagen siguiente para abrir la plantilla en el Portal de Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Escriba los siguientes valores:

    ![Creación de un clúster de HDInsight Spark mediante una plantilla de Azure Resource Manager](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Creación de un clúster de HDInsight Spark mediante una plantilla de Azure Resource Manager")

    * **Suscripción** : seleccione la suscripción de Azure que se usará para crear este clúster.
    * **Grupo de recursos**: cree un grupo de recursos o seleccione uno existente. El grupo de recursos se usa para administrar los recursos de Azure para sus proyectos.
    * **Ubicación**: seleccione una ubicación para el grupo de recursos. La plantilla utiliza esta ubicación para crear el clúster, así como para el almacenamiento de clúster predeterminado.
    * **Nombre del clúster**: escriba un nombre para el clúster de HDInsight que va a crear.
    * **Nombre de inicio de sesión y contraseña de clúster**: el nombre de inicio de sesión predeterminado es admin.
    * **Nombre de usuario y contraseña de SSH**.

3. Seleccione **I agree to the terms and conditions stated above** (Acepto los términos y condiciones indicados anteriormente) y **Anclar al panel** y haga clic en **Purchase** (Comprar). Puede ver un icono nuevo llamado Envío de implementación para la implementación de plantilla. La creación del clúster tarda aproximadamente 20 minutos.

Si surge un problema con la creación de clústeres de HDInsight, podría deberse a que no tiene los permisos adecuados para hacerlo. Para más información, consulte [Requisitos de control de acceso](../hdinsight-administer-use-portal-linux.md#create-clusters).

> [!NOTE]
> En este artículo se crea un clúster de Spark que usa [blobs de Azure Storage como almacenamiento](../hdinsight-hadoop-use-blob-storage.md). También puede crear un clúster de Spark que use [Azure Data Lake Store](../hdinsight-hadoop-use-data-lake-store.md) como almacenamiento predeterminado. Consulte [Creación de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

## <a name="create-a-jupyter-notebook"></a>Creación de un cuaderno de Jupyter

[Jupyter Notebook](http://jupyter.org) es un entorno de cuadernos interactivo que admite varios lenguajes de programación y que permite interactuar con los datos, combinar código con texto de marcado y realizar visualizaciones simples. Spark en HDInsight también incluye [Zeppelin Notebook](apache-spark-zeppelin-notebook.md). En este tutorial, se usa Jupyter Notebook.

**Creación de un cuaderno de Jupyter Notebook**

1. Abra el [Azure Portal](https://portal.azure.com/).

2. Abra el clúster de Spark que creó. Para obtener instrucciones, consulte [Enumeración y visualización de clústeres](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

3. En **Quick links** (Vínculos rápidos), haga clic en **Cluster dashboards** (Paneles de clúster) y después en **Jupyter Notebook**. Cuando se le pida, escriba las credenciales del clúster.

   ![Abrir un cuaderno de Jupyter Notebook para ejecutar consultas Spark SQL interactivas](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Abrir un cuaderno de Jupyter para ejecutar consultas Spark SQL interactivas")

   > [!NOTE]
   > Para acceder al cuaderno de Jupyter Notebook para el clúster, también puede abrir la siguiente dirección URL en el explorador. Reemplace **CLUSTERNAME** por el nombre del clúster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Haga clic en **Nuevo** y, después, haga clic en **Pyspark** para crear un cuaderno. Los cuadernos de Jupyter Notebook en clústeres de HDInsight también admiten tres kernels: **PySpark**, **PySpark3** y **Spark**. En este tutorial, se usa el kernel **PySpark**. Para más información sobre los kernels y las ventanas del uso de **PySpark**, consulte [Kernels para Jupyter Notebook en clústeres Spark en Azure HDInsight](apache-spark-jupyter-notebook-kernels.md).

   ![Crear un cuaderno de Jupyter Notebook para ejecutar consultas Spark SQL interactivas](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Crear un cuaderno de Jupyter Notebook para ejecutar consultas Spark SQL interactivas")

   Se crea y se abre un nuevo Notebook con el nombre Untitled(Untitled.pynb).

4. Haga clic en el nombre del Notebook en la parte superior y escriba un nombre descriptivo.

    ![Proporcione un nombre para el cuaderno de Jupyter Notebook para ejecutar consultas Spark interactivas](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Proporcione un nombre para el cuaderno de Jupyter Notebook para ejecutar consultas Spark interactivas")

## <a name="run-spark-sql-statements-on-a-hive-table"></a>Ejecución de instrucciones Spark SQL en una tabla Hive

SQL (Lenguaje de consulta estructurado) es el lenguaje más común y ampliamente utilizado en la consulta y definición de datos. Spark SQL funciona como una extensión de Apache Spark para procesar datos estructurados, mediante la conocida sintaxis de SQL.

Spark SQL es compatible tanto con SQL como con HiveQL como lenguajes de consulta. Sus funcionalidades incluyen un enlace en Java, Python y Scala. Con él, puede consultar los datos almacenados en varias ubicaciones, como las bases de datos externas, los archivos de datos estructurados (ejemplo: JSON) y las tablas Hive.

Para ver un ejemplo de cómo leer datos desde un archivo csv en lugar de una tabla de Hive, consulte [Ejecución de consultas interactivas en clústeres de Spark en HDInsight](apache-spark-load-data-run-query.md).

**Para ejecutar Spark SQL**

1.  Pegue el código siguiente del cuaderno en una celda vacía y presione **MAYÚS + ENTRAR** para ejecutar el código. 

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    ![Consulta de Hive en HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Consulta de Hive en HDInsight Spark")

    Cuando se utiliza un cuaderno de Jupyter Notebook con el clúster de Spark en HDInsight, obtendrá un valor de `sqlContext` preestablecido que puede usar para ejecutar consultas de Hive con Spark SQL. `%%sql` indica a Jupyter Notebook que use el valor de `sqlContext` preestablecido para ejecutar la consulta de Hive. La consulta recupera las 10 primeras filas de una tabla de Hive (**hivesampletable**) que se incluye de forma predeterminada en todos los clústeres de HDInsight. Para más información sobre las `%%sql` instrucciones mágicas y los contextos preestablecidos, consulte [Kernels para Jupyter Notebook en clústeres Spark en Azure HDInsight](apache-spark-jupyter-notebook-kernels.md).

    Cada vez que se ejecuta una consulta en Jupyter, el título de la ventana del explorador web muestra el estado **(Busy)** (Ocupado) junto con el título del Notebook. También verá un círculo sólido junto al texto **PySpark** en la esquina superior derecha. Cuando finaliza el trabajo, cambia a un círculo vacío.
    
    Debe actualizar la pantalla para mostrar la salida de la consulta.

    ![Consulta de Hive en HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Consulta de Hive en HDInsight Spark")

2. En el menú **Archivo** del cuaderno, haga clic en **Cerrar y detener**. Al cerrar el cuaderno, se liberan los recursos de clúster.

3. Si tiene previsto completar los pasos siguientes en otro momento, asegúrese de eliminar el clúster de HDInsight que creó en este artículo. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-step"></a>Paso siguiente 

En este artículo ha aprendido a crear un clúster de HDInsight Spark y a ejecutar una consulta básica de Spark SQL. Continúe hasta el siguiente artículo para obtener información sobre cómo usar un clúster de HDInsight Spark para ejecutar consultas interactivas en datos de ejemplo.

> [!div class="nextstepaction"]
>[Ejecución de consultas interactivas en un clúster de HDInsight Spark](apache-spark-load-data-run-query.md)



