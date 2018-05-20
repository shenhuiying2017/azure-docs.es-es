---
title: 'Guía de inicio rápido: Creación de un clúster de Spark en HDInsight con una plantilla'
description: Esta guía de inicio rápido muestra cómo usar la plantilla de Resource Manager para crear un clúster de Apache Spark en Azure HDInsight y ejecutar una consulta SQL de Spark simple.
services: azure-hdinsight
author: mumian
manager: cgronlun
editor: cgronlun
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.devlang: na
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: jgao
ms.custom: mvc
ms.openlocfilehash: ca44354b6aa51cae9218594ba5120d9c4a6f5ece
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-create-a-spark-cluster-in-hdinsight-using-template"></a>Guía de inicio rápido: Creación de un clúster de Spark en HDInsight con una plantilla

Aprenda cómo crear clústeres de Apache Spark en Azure HDInsight y cómo ejecutar consultas SQL de Spark en tablas de Hive. Apache Spark permite el análisis de datos y procesamiento de clústeres rápidos con el procesamiento en memoria. Para obtener información sobre Spark en HDInsight, consulte [Introducción a Apache Spark en HDInsight de Azure](apache-spark-overview.md).

En esta guía de inicio rápido, usará una plantilla de Resource Manager para crear un clúster de HDInsight Spark. El clúster usa Azure Storage Blob como almacenamiento de clústeres.

> [!IMPORTANT]
> La facturación de los clústeres de HDInsight se prorratea por minuto, tanto si se usan como si no. Por consiguiente, es aconsejable eliminar el clúster al terminar de usarlo. Para más información, consulte la sección [Limpieza de recursos](#clean-up-resources) de este artículo.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="create-an-hdinsight-spark-cluster"></a>Creación de un clúster de Spark en HDInsight

Cree un clúster de Spark en HDInsight mediante una plantilla de Azure Resource Manager. La plantilla se encuentra en [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). 

1. Seleccione el vínculo siguiente para abrir la plantilla en Azure Portal en una nueva pestaña del explorador:         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank">Implementación en Azure</a>

2. Escriba los siguientes valores:

    | Propiedad | Valor |
    |---|---|
    |**Suscripción**|Seleccione la suscripción de Azure que se usará para crear este clúster. La suscripción que se usa en esta guía de inicio rápido es **&lt;nombre de suscripción de Azure>**. |
    | **Grupos de recursos**|Cree un grupo de recursos o seleccione uno existente. El grupo de recursos se usa para administrar los recursos de Azure para sus proyectos. El nuevo nombre del grupo de recursos usado para esta guía de inicio rápido es **myspark20180403rg**.|
    | **Ubicación**|Seleccione una ubicación para el grupo de recursos. La plantilla utiliza esta ubicación para crear el clúster, así como para el almacenamiento de clúster predeterminado. La ubicación utilizada para esta guía de inicio rápido es **Este de EE. UU. 2**.|
    | **ClusterName**|Escriba un nombre para el clúster de HDInsight que va a crear. El nombre del nuevo clúster usado para esta guía de inicio rápido es **myspark20180403**.|
    | **Nombre de inicio de sesión y contraseña del clúster**|El nombre de inicio de sesión predeterminado es admin. Elija una contraseña el inicio de sesión del clúster. El nombre de inicio de sesión utilizado para esta guía de inicio rápido es **admin**.|
    | **Nombre de usuario y contraseña de SSH**|Elija una contraseña para el usuario de SSH. El nombre de usuario de SSH utilizado para esta guía de inicio rápido es **admin**.|

    ![Creación de un clúster de HDInsight Spark mediante una plantilla de Azure Resource Manager](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Creación de un clúster de HDInsight Spark mediante una plantilla de Azure Resource Manager")

3. Seleccione **Acepto los términos y condiciones indicados anteriormente** y **Anclar al panel** y, a continuación, seleccione **Comprar**. Verá un icono nuevo llamado **Deploying Template deployment** (Implementación de la plantilla de implementación). La creación del clúster tarda aproximadamente 20 minutos. El clúster debe crearse antes de poder pasar a la siguiente sesión.

Si surge un problema con la creación de clústeres de HDInsight, podría deberse a que no tiene los permisos adecuados para hacerlo. Para más información, consulte [Requisitos de control de acceso](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="create-a-jupyter-notebook"></a>Creación de un cuaderno de Jupyter

Jupyter Notebook es un entorno de cuaderno interactivo que admite varios lenguajes de programación. El cuaderno le permite interactuar con los datos, combinar código con el texto de marcado y realizar visualizaciones simples. 

1. Abra el [Azure Portal](https://portal.azure.com).
2. Seleccione **clústeres de HDInsight** y, a continuación, seleccione el clúster que creó.

    ![Abrir un clúster de HDInsight en Azure Portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. En el portal, seleccione **Paneles de clúster** y después **Jupyter Notebook**. Cuando se le pida, escriba las credenciales de inicio de sesión del clúster.

   ![Abrir un cuaderno de Jupyter Notebook para ejecutar consultas Spark SQL interactivas](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Abrir un cuaderno de Jupyter para ejecutar consultas Spark SQL interactivas")

4. Seleccione **Nuevo** > **PySpark** para crear un cuaderno. 

   ![Crear un cuaderno de Jupyter Notebook para ejecutar consultas Spark SQL interactivas](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Crear un cuaderno de Jupyter Notebook para ejecutar consultas Spark SQL interactivas")

   Se crea y se abre un nuevo Notebook con el nombre Untitled(Untitled.pynb).


## <a name="run-spark-sql-statements"></a>Ejecución de instrucciones de Spark SQL

SQL (Lenguaje de consulta estructurado) es el lenguaje más común y ampliamente utilizado en la consulta y definición de datos. Spark SQL funciona como una extensión de Apache Spark para procesar datos estructurados, mediante la conocida sintaxis de SQL.

1. Compruebe que el kernel esté listo. El kernel está listo cuando aparece un círculo vacío junto al nombre del kernel en el cuaderno. Un círculo sólido indica que el kernel está ocupado.

    ![Consulta de Hive en HDInsight Spark](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Consulta de Hive en HDInsight Spark")

    Al iniciar el cuaderno por primera vez, el kernel realiza tareas en segundo plano. Espere a que el kernel esté listo. 
2. Pegue el código siguiente en una celda vacía y presione **MAYÚS + ENTRAR** para ejecutar el código. El comando muestra las tablas Hive del clúster:

    ```PySpark
    %%sql
    SHOW TABLES
    ```
    Cuando se utiliza un cuaderno de Jupyter Notebook con el clúster de Spark en HDInsight, obtendrá un valor de `sqlContext` preestablecido que puede usar para ejecutar consultas de Hive con Spark SQL. `%%sql` indica a Jupyter Notebook que use el valor de `sqlContext` preestablecido para ejecutar la consulta de Hive. La consulta recupera las 10 primeras filas de una tabla de Hive (**hivesampletable**) que se incluye de forma predeterminada en todos los clústeres de HDInsight. Se tardan unos 30 segundos en obtener los resultados. El resultado tendrá un aspecto similar al siguiente: 

    ![Consulta de Hive en HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Consulta de Hive en HDInsight Spark")

    Cada vez que se ejecuta una consulta en Jupyter, el título de la ventana del explorador web muestra el estado **(Busy)** (Ocupado) junto con el título del Notebook. También verá un círculo sólido junto al texto **PySpark** en la esquina superior derecha.
    
2. Ejecute otra consulta para ver los datos en `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```
    
    Debe actualizar la pantalla para mostrar la salida de la consulta.

    ![Consulta de Hive en HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Consulta de Hive en HDInsight Spark")

2. En el menú **File** (Archivo) del cuaderno, seleccione **Close and Halt** (Cerrar y detener). Al cerrar el cuaderno, se liberan los recursos de clúster.

## <a name="clean-up-resources"></a>Limpieza de recursos
HDInsight guarda los datos en Azure Storage o Azure Data Lake Store, por lo que puede eliminar de manera segura un clúster si no está en uso. También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso. Si tiene previsto pasar inmediatamente al tutorial en [Pasos siguientes](#next-steps), es aconsejable que no elimine el clúster.

Vuelva a Azure Portal y seleccione **Eliminar**.

![Eliminar un clúster de HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Eliminar un clúster de HDInsight")

También puede seleccionar el nombre del grupo de recursos para abrir la página del grupo de recursos y, a continuación, seleccionar **Eliminar grupo de recursos**. Al eliminar el grupo de recursos, se eliminan tanto el clúster de HDInsight Spark como la cuenta de almacenamiento predeterminada.

## <a name="next-steps"></a>Pasos siguientes 

En esta guía de inicio rápido, ha aprendido a crear un clúster de HDInsight Spark y a ejecutar una consulta SQL básica de Spark. Continúe hasta el siguiente tutorial para obtener información sobre cómo usar un clúster de HDInsight Spark para ejecutar consultas interactivas en datos de ejemplo.

> [!div class="nextstepaction"]
>[Ejecución de consultas interactivas en Spark](./apache-spark-load-data-run-query.md)


