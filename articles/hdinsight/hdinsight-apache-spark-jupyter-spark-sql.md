---
title: "Creación de un clúster de Apache Spark en Azure HDInsight | Microsoft Docs"
description: "Tutorial de inicio rápido para HDInsight Spark sobre cómo crear un clúster de Apache Spark en HDInsight."
keywords: "inicio rápido para spark,spark interactivo,consulta interactiva,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/21/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: ad4330a1fc7f8de154d9aaa8df3acc2ab59b9dc1
ms.contentlocale: es-es
ms.lasthandoff: 07/24/2017

---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Creación de un clúster de Apache Spark en Azure HDInsight

En este artículo, aprenderá a crear un clúster de Apache Spark en Azure HDInsight. Para obtener información sobre Spark en HDInsight, consulte [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md).

   ![Diagrama de inicio rápido donde se describen los pasos para crear un clúster de Apache Spark en Azure HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-quickstart-interactive-spark-query-flow.png "Inicio rápido para Spark mediante Apache Spark en HDInsight. Pasos que ilustran cómo crear un clúster y ejecutar una consulta Spark interactiva")

## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Antes de comenzar este tutorial, debe tener una suscripción a Azure. Consulte la página [Cree su cuenta gratuita de Azure hoy mismo](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>Creación de un clúster de HDInsight Spark

En esta sección, creará un clúster de HDInsight Spark mediante una [plantilla de Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Para conocer otros métodos de creación de clústeres, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Haga clic en la imagen siguiente para abrir la plantilla en el Portal de Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Escriba los siguientes valores:

    ![Creación de un clúster de HDInsight Spark mediante una plantilla de Azure Resource Manager](./media/hdinsight-apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Creación de un clúster de HDInsight Spark mediante una plantilla de Azure Resource Manager")

    * **Suscripción**: seleccione su suscripción de Azure para este clúster.
    * **Grupo de recursos**: cree un grupo de recursos o seleccione uno existente. El grupo de recursos se usa para administrar los recursos de Azure para sus proyectos.
    * **Ubicación**: seleccione una ubicación para el grupo de recursos. La plantilla utiliza esta ubicación para crear el clúster, así como para el almacenamiento de clúster predeterminado.
    * **Nombre del clúster**: escriba un nombre para el clúster de HDInsight que va a crear.
    * **Versión de Spark**: seleccione **2.0** como la versión de Spark que quiere instalar en el clúster.
    * **Nombre de inicio de sesión y contraseña de clúster**: el nombre de inicio de sesión predeterminado es admin.
    * **Nombre de usuario y contraseña de SSH**.

   Anote estos valores.  Los necesitará más adelante en el tutorial.

3. Seleccione **I agree to the terms and conditions stated above** (Acepto los términos y condiciones indicados anteriormente) y **Anclar al panel** y haga clic en **Purchase** (Comprar). Puede ver un icono nuevo llamado Envío de implementación para la implementación de plantilla. La creación del clúster tarda aproximadamente 20 minutos.

Si surge un problema con la creación de clústeres de HDInsight, podría deberse a que no tiene los permisos adecuados para hacerlo. Consulte [Requisitos de control de acceso](hdinsight-administer-use-portal-linux.md#create-clusters) para más información.

> [!NOTE]
> En este artículo se crea un clúster de Spark que usa [blobs de Azure Storage como almacenamiento](hdinsight-hadoop-use-blob-storage.md). También puede crear un clúster de Spark que use [Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md) como almacenamiento predeterminado. Consulte [Creación de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

## <a name="run-a-hive-query-using-spark-sql"></a>Ejecución de una consulta de Hive con Spark SQL

Cuando se utiliza un cuaderno de Jupyter Notebook configurado para el clúster de HDInsight Spark, obtendrá un valor `sqlContext` preestablecido que puede usar para ejecutar consultas de Hive con Spark SQL. En esta sección, aprenderá a iniciar un cuaderno de Jupyter Notebook y después a ejecutar una consulta de Hive básica.

1. Abra el [Azure Portal](https://portal.azure.com/).

2. Si ha elegido anclar el clúster al panel, haga clic en el icono de clúster desde el panel para abrir la hoja del clúster.

    Si no ancló el clúster al panel, en el panel izquierdo, haga clic en **Clústeres de HDInsight** y luego haga clic en el clúster que ha creado.

3. En **Quick links** (Vínculos rápidos), haga clic en **Cluster dashboards** (Paneles de clúster) y después en **Jupyter Notebook**. Cuando se le pida, escriba las credenciales del clúster.

   ![Abrir un cuaderno de Jupyter para ejecutar consultas Spark SQL interactivas](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Abrir un cuaderno de Jupyter para ejecutar consultas Spark SQL interactivas")

   > [!NOTE]
   > También puede acceder al cuaderno de Jupyter en el clúster si abre la siguiente dirección URL en el explorador. Reemplace **CLUSTERNAME** por el nombre del clúster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Cree un cuaderno. Haga clic en **Nuevo** y, luego, en **PySpark**.

   ![Crear un cuaderno de Jupyter para ejecutar consultas Spark SQL interactivas](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "Crear un cuaderno de Jupyter para ejecutar consultas Spark SQL interactivas")

   Se crea y se abre un nuevo Notebook con el nombre Untitled(Untitled.pynb).

4. Haga clic en el nombre del Notebook en la parte superior y escriba un nombre descriptivo.

    ![Proporcionar un nombre para el cuaderno de Jupyter para ejecutar consultas Spark interactivas](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Proporcionar un nombre para el cuaderno de Jupyter ejecutar consultas Spark interactivas")

5.  Pegue el código siguiente en una celda vacía y presione **MAYÚS + ENTRAR** para ejecutar el código. En el código siguiente `%%sql` (denominado instrucción mágica de SQL) indica a un cuaderno de Jupyter Notebook que utilice el valor `sqlContext` predeterminado para ejecutar la consulta de Hive. La consulta recupera las 10 primeras filas de una tabla de Hive (**hivesampletable**) que está disponible de forma predeterminada en todos los clústeres de HDInsight.

        %%sql
        SELECT * FROM hivesampletable LIMIT 10

    ![Consulta de Hive en HDInsight Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Consulta de Hive en HDInsight Spark")

    Para más información sobre las `%%sql` instrucciones mágicas y los contextos preestablecidos, consulte [Kernels para Jupyter Notebook en clústeres Spark en Azure HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

    > [!NOTE]
    > Cada vez que se ejecuta una consulta en Jupyter, el título de la ventana del explorador web muestra el estado **(Busy)** (Ocupado) junto con el título del Notebook. También verá un círculo sólido junto al texto **PySpark** en la esquina superior derecha. Cuando finaliza el trabajo, cambia a un círculo vacío.
    >
    >
    
6. Debe actualizar la pantalla para mostrar el resultado de la consulta.

    ![Consulta de Hive en HDInsight Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Consulta de Hive en HDInsight Spark")

7. Cuando haya terminado de ejecutar la aplicación, cierre el Notebook para liberar los recursos del clúster. Para ello, en el menú **Archivo** del cuaderno, haga clic en **Cerrar y detener**.

8. Si tiene previsto completar los pasos siguientes en otro momento, asegúrese de eliminar el clúster de HDInsight que creó en este artículo. 

    [!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-step"></a>Paso siguiente 

En este artículo ha aprendido a crear un clúster de HDInsight Spark y a ejecutar una consulta básica de Spark SQL. Continúe hasta el siguiente artículo para obtener información sobre cómo usar un clúster de HDInsight Spark para ejecutar consultas interactivas en datos de ejemplo.

> [!div class="nextstepaction"]
>[Ejecución de consultas interactivas en un clúster de HDInsight Spark](hdinsight-apache-spark-load-data-run-query.md)




