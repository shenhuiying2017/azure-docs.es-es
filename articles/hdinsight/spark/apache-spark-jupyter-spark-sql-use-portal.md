---
title: 'Inicio rápido: crear un clúster de Spark en HDInsight con Azure Portal'
description: Este inicio rápido muestra cómo usar Azure Portal para crear un clúster de Apache Spark en Azure HDInsight y ejecutar Spark SQL.
services: azure-hdinsight
author: mumian
manager: cgronlun
editor: cgronlun
ms.service: azure-hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: jgao
ms.custom: mvc
ms.openlocfilehash: 292396b35bde63304d4c03bd80b2988b0a8fbba8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33779181"
---
# <a name="quickstart-create-a-spark-cluster-in-hdinsight-using-the-azure-portal"></a>Inicio rápido: crear un clúster de Spark en HDInsight con Azure Portal
Aprenda cómo crear clústeres de Apache Spark en Azure HDInsight y cómo ejecutar consultas de Spark SQL en tablas de Hive. Apache Spark permite el análisis rápido de datos y procesamiento de clústeres con el procesamiento en memoria. Para obtener información sobre Spark en HDInsight, consulte [Introducción a Apache Spark en HDInsight de Azure](apache-spark-overview.md).

En este inicio rápido, usará Azure Portal para crear un clúster de Spark de HDInsight. El clúster usa Azure Storage Blob como almacenamiento de clústeres.

> [!IMPORTANT]
> La facturación de los clústeres de HDInsight se prorratea por minuto, tanto si se usan como si no. Por consiguiente, es aconsejable eliminar el clúster al terminar de usarlo. Para obtener más información, consulte la sección [Limpieza de recursos](#clean-up-resources) de este artículo.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="create-an-hdinsight-spark-cluster"></a>Creación de un clúster de Spark en HDInsight

1. En Azure Portal, seleccione **Crear un recurso** > **Datos y análisis** > **HDInsight**. 

    ![HDInsight en Azure Portal](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster.png "HDInsight en Azure Portal")
2. En **Datos básicos**, proporcione los valores siguientes:
     
    |Propiedad  |DESCRIPCIÓN  |
    |---------|---------|
    |**Nombre del clúster**     | Asigne un nombre al clúster de Spark de HDInsight. El nombre del clúster usado para este inicio rápido es **myspark20180403**.|
    |**Suscripción**     | En el menú desplegable, seleccione una suscripción de Azure usada para este clúster. La suscripción usada para este inicio rápido es **&lt;suscripción de Azure**. |
    |**Tipo de clúster**| Expanda el elemento y, a continuación, seleccione **Spark** como tipo de clúster. Especifique la versión de clúster de Spark. <br/> <img src="./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster-type.png" alt = "Slect HDInsight clsuter type" /> |
    |**Nombre de usuario de inicio de sesión del clúster**| Escriba el nombre de usuario de inicio de sesión del clúster.  El nombre predeterminado es *admin*. Use esta cuenta para iniciar sesión en Jupyter Notebook más adelante en el inicio rápido. |
    |**Contraseña de inicio de sesión del clúster**| Escriba la contraseña de inicio de sesión del clúster. |
    |**Nombre de usuario de Secure Shell (SSH)**| Escriba el nombre de usuario de SSH. El nombre de usuario de SSH usado para este inicio rápido es **sshuser**. De manera predeterminada, esta cuenta comparte la contraseña con la cuenta de *nombre de usuario de inicio de sesión del clúster*. |
    |**Grupos de recursos**     | Especifique si desea crear un nuevo grupo de recursos o utilizar uno existente. Un grupo de recursos es un contenedor que almacena los recursos relacionados con una solución de Azure. El nombre del grupo de recursos usado para este inicio rápido es **myspark20180403rg**. |
    |**Ubicación**     | Seleccione una ubicación para el grupo de recursos. La plantilla utiliza esta ubicación para crear el clúster, así como para el almacenamiento de clúster predeterminado. La ubicación usada para este inicio rápido es **Este de EE. UU. 2**. |

    ![Crear configuraciones básicas de clústeres de Spark de HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster-basic2.png "Crear configuraciones básicas de clústeres de Spark en HDInsight")

    Seleccione **Siguiente** para ir a la página **Almacenamiento**.
3. En **Almacenamiento**, proporcione los valores siguientes:

    - **Seleccionar una cuenta de almacenamiento**: seleccione **Crear nuevo** y, a continuación, asigne un nombre a la nueva cuenta de almacenamiento. El nombre de la cuenta de almacenamiento usada para este inicio rápido es **myspark20180403store**.

    ![Crear configuraciones de almacenamiento de clústeres de Spark de HDInsight](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster-storage.png "Crear configuraciones de almacenamiento de clústeres de Spark en HDInsight")

    > [!NOTE] 
    > En la captura de pantalla, muestra **Seleccionar existente**. El vínculo cambia entre **Crear nuevo** y **Seleccionar existente**.

    El **contenedor predeterminado** tiene un nombre predeterminado.  Si quiere, puede cambiar el nombre.

    Seleccione **Siguiente** para ir a la página **Resumen**. 


3. En **Resumen**, seleccione **Crear**. La creación del clúster tarda aproximadamente 20 minutos. El clúster debe crearse antes de poder pasar a la siguiente sesión.

Si surge un problema con la creación de clústeres de HDInsight, podría deberse a que no tiene los permisos adecuados para hacerlo. Para más información, consulte [Requisitos de control de acceso](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="create-a-jupyter-notebook"></a>Creación de un cuaderno de Jupyter

Jupyter Notebook es un entorno de cuaderno interactivo que admite varios lenguajes de programación. El cuaderno le permite interactuar con los datos, combinar código con el texto de marcado y realizar visualizaciones básicas. 

1. Abra el [Azure Portal](https://portal.azure.com).
2. Seleccione **Clústeres de HDInsight** y, a continuación, seleccione el clúster que creó.

    ![abrir un clúster de HDInsight en Azure Portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. En el portal, seleccione **Paneles de clúster** y, a continuación, **Jupyter Notebook**. Cuando se le solicite, escriba las credenciales de inicio de sesión del clúster.

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
HDInsight guarda los datos en Azure Storage o Azure Data Lake Store, por lo que puede eliminar de manera segura un clúster si no está en uso. También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso. Si tiene previsto pasar inmediatamente al tutorial de [Pasos siguientes](#next-steps), es aconsejable que no elimine el clúster.

Vuelva a Azure Portal y seleccione **Eliminar**.

![Eliminar un clúster de HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Eliminar un clúster de HDInsight")

También puede seleccionar el nombre del grupo de recursos para abrir la página del grupo de recursos y, a continuación, seleccionar **Eliminar grupo de recursos**. Al eliminar el grupo de recursos, se eliminan el clúster de HDInsight Spark y la cuenta de almacenamiento predeterminada.

## <a name="next-steps"></a>Pasos siguientes 

En este inicio rápido, ha aprendido a crear un clúster de HDInsight Spark y a ejecutar una consulta básica de Spark SQL. Continúe hasta el siguiente tutorial para obtener información sobre cómo usar un clúster de Spark de HDInsight para ejecutar consultas interactivas en datos de ejemplo.

> [!div class="nextstepaction"]
>[Ejecución de consultas interactivas en Spark](./apache-spark-load-data-run-query.md)
