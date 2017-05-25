---
title: "Spark BI mediante herramientas de visualización de datos con Azure HDInsight | Microsoft Docs"
description: "Uso de herramientas de visualización de datos para análisis con Apache Spark BI en clústeres de HDInsight"
keywords: "Apache Spark BI, Spark BI, visualización de datos de Spark, inteligencia empresarial de Spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: bc6749f583de752592b0b49548c5a42321cac7b3
ms.contentlocale: es-es
ms.lasthandoff: 05/16/2017


---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI mediante herramientas de visualización de datos con Azure HDInsight

Obtenga información sobre cómo usar herramientas de visualización de datos como Power BI y Tableau para analizar un conjunto de datos de ejemplo sin formato mediante Apache Spark BI en clústeres de HDInsight.

> [!NOTE]
> La conectividad con las herramientas de BI que se describen en este artículo no se admite en la versión 2.1 de Spark de la versión 3.6 preliminar de HDInsight de Azure. Solo las versiones de Spark 1.6 y 2.0 (3.4 y 3.5 de HDInsight respectivamente) son compatibles.
>

Este tutorial también está disponible como un cuaderno de Jupyter Notebook en un clúster Spark de HDInsight. La experiencia del cuaderno le permite ejecutar los fragmentos de código de Python desde el propio Bloc de notas. Para realizar el tutorial desde un cuaderno, cree un clúster Spark, inicie un cuaderno de Jupyter Notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`) y ejecute el cuaderno **Use BI tools with Apache Spark on HDInsight.ipynb** en la carpeta **Python**.

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="hivetable"></a>Preparación de los datos para la visualización de datos de Spark

En esta sección, usamos el cuaderno de [Jupyter](https://jupyter.org) Notebook desde un clúster de Spark en HDInsight para ejecutar trabajos que procesan los datos de ejemplo sin procesar y los guardan como una tabla. Los datos de ejemplo corresponden a un archivo .csv (hvac.csv) que está disponible en todos los clústeres de manera predeterminada.

Una vez que los datos se han guardado como una tabla, en la siguiente sección usamos las herramientas de BI para conectarlos con la tabla y realizar visualizaciones de datos.

1. Desde [Azure Portal](https://portal.azure.com/), en el panel de inicio, haga clic en el icono del clúster Spark (si lo ancló al panel de inicio). También puede navegar hasta el clúster en **Examinar todo** > **Clústeres de HDInsight**.   

2. En la hoja del clúster Spark, haga clic en **Panel de clúster** y, luego, en **Jupyter Notebook**. Cuando se le pida, escriba las credenciales del clúster.

   > [!NOTE]
   > También puede comunicarse con el equipo Jupyter Notebook en el clúster si abre la siguiente dirección URL en el explorador. Reemplace **CLUSTERNAME** por el nombre del clúster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Cree un cuaderno. Haga clic en **Nuevo** y, luego, en **PySpark**.

    ![Creación de un cuaderno de Jupyter Notebook para Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/create-jupyter-notebook-for-spark-bi.png "Creación de un cuaderno de Jupyter Notebook para Apache Spark BI")

4. Se crea y se abre un nuevo cuaderno con el nombre Untitled.pynb. Haga clic en el nombre del cuaderno en la parte superior y escriba un nombre descriptivo.

    ![Proporcione un nombre para el cuaderno de Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/jupyter-notebook-name-for-spark-bi.png "Proporcione un nombre para el cuaderno de Apache Spark BI")

5. Dado que creó un cuaderno con el kernel PySpark, no necesitará crear ningún contexto explícitamente. Los contextos de Spark y Hive se crean automáticamente al ejecutar la primera celda de código. Puede empezar por importar los tipos necesarios para este escenario. Para ello, coloque el cursor en la celda y presione **MAYÚS + ENTRAR**.

        from pyspark.sql import *

6. Cargue los datos de ejemplo en una tabla temporal. Cuando crea un clúster Spark en HDInsight, el archivo de datos de ejemplo, **hvac.csv**, se copia en la cuenta de almacenamiento asociada en **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    En una celda vacía, pegue el siguiente fragmento de código y presione **MAYÚS + ENTRAR**. Este fragmento de código registra los datos en una tabla llamada **hvac**.

        # Create an RDD from sample data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create a schema for our data
        Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')

        # Parse the data and create a schema
        hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
        hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))

        # Infer the schema and create a table       
        hvacTable = sqlContext.createDataFrame(hvac)
        hvacTable.registerTempTable('hvactemptable')
        dfw = DataFrameWriter(hvacTable)
        dfw.saveAsTable('hvac')

7. Compruebe que la tabla se creó correctamente. Puede usar la instrucción mágica `%%sql` para ejecutar directamente consultas de Hive. Para más información sobre la instrucción mágica `%%sql`, así como otras instrucciones mágicas disponibles con el kernel de PySpark, consulte [Kernels disponibles en cuadernos de Jupyter Notebook con clústeres de Spark en HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SHOW TABLES

    Verá una salida como la que se muestra a continuación:

        +---------------+-------------+
        |tableName      |isTemporary  |
        +---------------+-------------+
        |hvactemptable  |true         |
        |hivesampletable|false        |
        |hvac           |false        |
        +---------------+-------------+

    Las tablas que muestran false en la columna **isTemporary** son las únicas que son tablas de Hive que se almacenarán en Metastore y a las que se puede acceder desde las herramientas de BI. En este tutorial, nos conectamos a la tabla **hvac** que hemos creado.

8. Compruebe que la tabla contenga los datos previstos. En una celda vacía del cuaderno, copie el siguiente fragmento de código y presione **MAYÚS + ENTRAR**.

        %%sql
        SELECT * FROM hvac LIMIT 10

9. Cierre el cuaderno para liberar los recursos. Para ello, en el menú **Archivo** del cuaderno, haga clic en **Cerrar y detener**.

## <a name="powerbi"></a>Uso de Power BI para la visualización de datos de Spark

Cuando haya guardado los datos como una tabla, puede usar Power BI para conectar con ellos y visualizarlos para crear informes, paneles, etc.

1. Asegúrese de tener acceso a Power BI. Puede obtener una suscripción de versión preliminar gratuita de Power BI en [http://www.powerbi.com/](http://www.powerbi.com/).

2. Inicie sesión en [Power BI](http://www.powerbi.com/).

3. En la parte inferior del panel izquierdo, haga clic en **Obtener datos**.

4. En la página **Obtener datos**, en **Importar datos o conectarse a ellos**, en **Bases de datos**, haga clic en **Obtener**.

    ![Obtención de datos en Power BI para Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Obtención de datos en Power BI para Apache Spark BI")

5. En la siguiente pantalla, haga clic en **Spark en HDInsight de Azure** y luego en **Conectar**. Cuando se le pida, escriba la dirección URL del clúster (`mysparkcluster.azurehdinsight.net`) y las credenciales para conectarse al clúster.

    ![Conexión a Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "Conexión a Apache Spark BI")

    Una vez establecida la conexión, Power BI inicia la importación de datos desde el clúster Spark hacia HDInsight.

6. Power BI importa los datos y agrega un conjunto de datos de **Spark** en el encabezado **Conjuntos de datos**. Haga clic en el conjunto de datos para abrir una nueva hoja de cálculo para visualizar los datos. También puede guardar la hoja de cálculo como un informe. Para guardar una hoja de cálculo, en el menú **Archivo**, haga clic en **Guardar**.

    ![Icono de Apache Spark BI en el panel de Power BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-tile-dashboard.png "Icono de Apache Spark BI en el panel de Power BI")
7. Tenga en cuenta que la lista **Fields** (Campos) de la derecha incluye la tabla **hvac** que creó anteriormente. Expanda la tabla para ver sus campos, como se definieron antes en el cuaderno.

      ![Lista de tablas en el panel de Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "Lista de tablas en el panel de Apache Spark BI")

8. Cree una visualización para mostrar la variación entre la temperatura objetivo y la real para cada edificio. Para visualizar los datos, seleccione **Gráfico de áreas** (se muestra en un cuadro rojo). Para definir el eje, arrastre y coloque el campo **BuildingID** en **Axis** (Eje) y los campos **ActualTemp**/**TargetTemp** en **Value** (Valor).

    ![Creación de visualizaciones de datos de Spark con Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Creación de visualizaciones de datos de Spark con Apache Spark BI")

9. De manera predeterminada, la visualización muestra la suma de **ActualTemp** y **TargetTemp**. Para ambos campos, en la lista desplegable, seleccione **Average** (Media) para obtener un promedio de las temperaturas objetivo y reales de ambos edificios.

    ![Creación de visualizaciones de datos de Spark con Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Creación de visualizaciones de datos de Spark con Apache Spark BI")

10. La visualización de datos debería parecerse a la que se muestra en la captura de pantalla. Mueva el cursor sobre la visualización para obtener información sobre herramientas con datos relevantes.

    ![Creación de visualizaciones de datos de Spark con Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Creación de visualizaciones de datos de Spark con Apache Spark BI")

11. Haga clic en **Save** (Guardar) en el menú superior y proporcione un nombre para el informe. También puede anclar la visualización. Cuando se ancla una visualización, se almacena en el panel para que pueda seguir el valor más reciente de un vistazo.

   Puede agregar tantas visualizaciones como quiera para el mismo conjunto de datos y anclarlas al panel para ver una instantánea de los datos. Además, los clústeres Spark en HDInsight están conectados a Power BI con conexión directa. Esto garantiza que Power BI siempre tiene los datos más actualizados del clúster, por lo que no es necesario programar actualizaciones del conjunto de datos.

## <a name="tableau"></a>Uso de Tableau Desktop para la visualización de datos de Spark

> [!NOTE]
> Esta sección solo es aplicable a los clústeres de Spark 1.5.2 creados en Azure HDInsight.
>
>

1. Instale [Tableau Desktop](http://www.tableau.com/products/desktop) en el equipo donde vaya a ejecutar este tutorial de Apache Spark BI.

2. Asegúrese de que el equipo también tenga instalado el controlador ODBC de Microsoft Spark. Puede instalar el controlador desde [aquí](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Inicie Tableau Desktop. En el panel izquierdo, en la lista del servidor al que va a conectarse, haga clic en **Spark SQL**. Si Spark SQL no aparece de forma predeterminada en el panel izquierdo, puede hacer clic en **More Servers**(Más servidores) para buscarlo.
2. En el cuadro de diálogo de conexión de Spark SQL, proporcione los valores como se muestra en la captura de pantalla y luego haga clic en **OK**(Aceptar).

    ![Conexión con un clúster de Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Conexión con un clúster de Apache Spark BI")

    La lista desplegable de autenticación solo muestra **Servicio HDInsight de Microsoft Azure** como opción si ha instalado el [controlador ODBC de Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229) en el equipo.
3. En la siguiente pantalla, en la lista desplegable **Schema** (Esquema), haga clic en el icono **Find** (Buscar) y luego en **default** (predeterminado).

    ![Búsqueda del esquema de Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Búsqueda del esquema de Apache Spark BI")
4. En el campo **Table** (Tabla), vuelva a hacer clic en el icono **Find** (Buscar) para ver todas las tablas de Hive disponibles en el clúster. Debería ver la tabla **hvac** que creó antes con el cuaderno.

    ![Búsqueda de la tabla de Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Búsqueda de la tabla de Apache Spark BI")
5. Arrastre y coloque la tabla en el cuadro superior a la derecha. Tableau importa los datos y muestra el esquema como se resalta con el cuadro rojo.

    ![Incorporación de tablas a Tableau para Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Incorporación de tablas a Tableau para Apache Spark BI")
6. Haga clic en la pestaña **Sheet1** (Hoja1) en la parte inferior izquierda. Realice una visualización que muestre el promedio de temperaturas objetivo y reales de todos los edificios para cada fecha. Arrastre **Date** (Fecha) y **Building ID** (Id. de edificio) a **Columns** (Columnas), y **Actual Temp**/**Target Temp** (Temperatura real/Temperatura objetivo) a **Rows** (Filas). En **Marks** (Marcas), seleccione **Area** (Área) para usar un mapa de áreas para visualización de datos de Spark.

     ![Incorporación de campos para la visualización de datos de Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Incorporación de campos para la visualización de datos de Spark")
7. De manera predeterminada, se muestran los campos de temperatura como agregado. Si desea mostrar el promedio de las temperaturas en su lugar, puede hacerlo en la lista desplegable, como se muestra a continuación.

    ![Realización del promedio de temperatura para la visualización de datos de Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Realización del promedio de temperatura para la visualización de datos de Spark")

8. También puede superponer un mapa de temperatura al otro para hacerse una idea más clara de la diferencia entre las temperaturas reales y objetivo. Mueva el mouse a la esquina del gráfico de área inferior hasta que vea la forma del controlador resaltada en un círculo rojo. Arrastre el gráfico al otro gráfico de encima y suelte el mouse cuando vea la forma resaltada en el rectángulo rojo.

    ![Combinación de mapas para la visualización de datos de Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Combinación de mapas para la visualización de datos de Spark")

     La visualización de los datos cambia, como se muestra en la captura de pantalla:

    ![Salida de Tableau para la visualización de datos de Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Salida de Tableau para la visualización de datos de Spark")
9. Haga clic en **Save** (Guardar) para guardar la hoja de cálculo. Puede crear paneles y agregarles una o varias hojas.

## <a name="seealso"></a>Otras referencias
* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios
* [Spark con Aprendizaje automático: uso de Spark en HDInsight para analizar la temperatura de edificios con los de datos del sistema de acondicionamiento de aire](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones de Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

