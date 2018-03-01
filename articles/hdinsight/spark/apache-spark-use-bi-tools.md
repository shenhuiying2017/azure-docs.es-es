---
title: "Spark BI mediante herramientas de visualización de datos con Azure HDInsight | Microsoft Docs"
description: "Uso de herramientas de visualización de datos para análisis con Apache Spark BI en clústeres de HDInsight"
keywords: "Apache Spark BI, Spark BI, visualización de datos de Spark, inteligencia empresarial de Spark"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: jgao
ms.openlocfilehash: 97305ec6774e89e776653adbcdcf86b1cd63642f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI mediante herramientas de visualización de datos con Azure HDInsight

Obtenga información sobre cómo utilizar [Microsoft Power BI](http://powerbi.microsoft.com) para visualizar datos en un clúster de Apache Spark en Azure HDInsight.

## <a name="prerequisites"></a>requisitos previos

* **Complete el artículo [Ejecución de consultas interactivas en un clúster Spark de HDInsight](./apache-spark-load-data-run-query.md)**.
* **Power BI**: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) y [suscripción de prueba de Power BI](https://app.powerbi.com/signupredirect?pbi_source=web) (opcional).


## <a name="hivetable"></a>Comprobación de los datos

El bloc de notas de Jupyter que creó en el [tutorial anterior](apache-spark-load-data-run-query.md) incluye código para crear una tabla `hvac`. Esta tabla se basa en el archivo CSV en todos los clústeres de Spark de HDInsight en **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Use el siguiente procedimiento para comprobar los datos.

1. Desde el cuaderno de Jupyter, pegue el siguiente código y presione **MAYÚS + ENTRAR**. El código verifica la existencia de las tablas.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    El resultado tendrá un aspecto similar al siguiente:

    ![Se muestran tablas en Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Si ha cerrado el bloc de notas antes de iniciar este tutorial, `hvactemptable` se limpia, por lo que no se incluye en los resultados.
    Desde las herramientas de BI, solo se puede acceder a las tablas de Hive almacenadas en Metastore (indicadas como **False** en la columna **isTemporary**). En este tutorial, se conecta a la tabla **hvac** que ha creado.

2. Pegue el siguiente código en una celda vacía y presione **MAYÚS + ENTRAR**. El código comprueba los datos de la tabla.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    El resultado tendrá un aspecto similar al siguiente:

    ![Se muestran las filas de la tabla hvac en Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. En el menú **Archivo** del cuaderno, haga clic en **Cerrar y detener**. Cierre el cuaderno para liberar los recursos. 















## <a name="powerbi"></a>Uso de Power BI

En esta sección, se usa Power BI para crear visualizaciones, informes y paneles de datos a partir de los datos de clúster de Spark. 

### <a name="create-a-report-in-power-bi-desktop"></a>Creación de un informe en Power BI Desktop
Los primeros pasos para trabajar con Spark pasan por conectarse al clúster de Power BI Desktop, cargar datos del clúster y crear una visualización básica basada en dichos datos.

> [!NOTE]
> El conector que se muestra en este artículo está actualmente en vista previa. Proporcione cualquier comentario que tenga a través del sitio [Comunidad de Power BI](https://community.powerbi.com/) o [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas) (Ideas sobre Power BI).

1. Abra [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).
1. En la pestaña **Inicio**, haga clic en **Obtener datos** y luego en **Más**.

    ![Obtención de datos en Power BI Desktop desde HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Obtención de datos en Power BI desde Apache Spark BI")


2. Escriba `Spark` en el cuadro de búsqueda, seleccione **Azure HDInsight Spark (Beta)** y haga clic en **Conectar**.

    ![Obtención de datos en Power BI desde Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Obtención de datos en Power BI desde Apache Spark BI")

3. Escriba la dirección URL del clúster (con el formato `mysparkcluster.azurehdinsight.net`), seleccione **DirectQuery** y haga clic en **Aceptar**.

    Puede usar cualquier modo de conectividad de datos con Spark. Si usa DirectQuery, los cambios se reflejan en los informes sin tener que actualizar el conjunto de datos completo. Si importa los datos, deberá actualizar el conjunto de datos para ver los cambios. Para obtener más información sobre cómo y cuándo se debe usar DirectQuery, consulte [Uso de DirectQuery en Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Escriba la información de la cuenta de inicio de sesión de HDInsight y haga clic en **conectar**. El nombre de cuenta predeterminado es *admin*.

5. Seleccione la tabla `hvac`, espere para obtener una vista previa de los datos y haga clic en **Cargar**.

    ![Nombre de usuario y contraseña del clúster de Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Nombre de usuario y contraseña del clúster de Spark")

    Power BI Desktop tiene toda la información necesaria para conectarse a los datos de carga y al clúster de Spark desde la tabla `hvac`. La tabla y las columnas que la forman se muestran en el panel **Campos**.  Vea la siguiente captura de pantalla:

6. Visualice la variación entre la temperatura objetivo y la real para cada edificio: 

    1. En el panel **VISUALIZACIONES**, seleccione **Gráfico de áreas**. 
    2. Arrastre el campo **BuildingID** a **Eje** y arrastre los campos **ActualTemp** y **TargetTemp** a **Valor**.

        ![Creación de visualizaciones de datos de Spark con Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Creación de visualizaciones de datos de Spark con Apache Spark BI")

        El diagrama tiene el siguiente aspecto:

        ![Creación de visualizaciones de datos de Spark con Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Creación de visualizaciones de datos de Spark con Apache Spark BI")

        De manera predeterminada, la visualización muestra la suma de **ActualTemp** y **TargetTemp**. Haga clic en la flecha abajo junto a **ActualTemp** y **TragetTemp** en el panel Visualizaciones; puede ver que **Suma** se ha seleccionado.

    3. Haga clic en las flechas abajo junto a **ActualTemp** y **TragetTemp** en el panel Visualizaciones, seleccione **Media** para obtener un promedio de temperaturas reales y objetivo para cada edificio.

        ![Creación de visualizaciones de datos de Spark con Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Creación de visualizaciones de datos de Spark con Apache Spark BI")

        La visualización de datos debe parecerse a la que se muestra en la captura de pantalla. Mueva el cursor sobre la visualización para obtener información sobre herramientas con datos relevantes.

        ![Creación de visualizaciones de datos de Spark con Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Creación de visualizaciones de datos de Spark con Apache Spark BI")

7. Haga clic en **Archivo** y en **Guardar**, y después ponga el nombre `BuildingTemperature.pbix` al archivo. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publicar el informe en el servicio Power BI (opcional)

El servicio Power BI le permite compartir informes y paneles a través de su organización. En esta sección, primero publica el conjunto de datos y el informe. A continuación, puede anclar el informe a un panel. Normalmente los paneles se usan para centrarse en un subconjunto de datos de un informe. En el informe solo tiene una visualización, pero sigue siendo útil para seguir los pasos.

1. Abra Power BI Desktop.
2. Desde la pestaña **Inicio**, haga clic en **Publicar**.

    ![Publicar en Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publicar en Power BI Desktop")

2. Seleccione el área de trabajo en la que publicar el conjunto de datos y el informe, y haga clic en **Seleccionar**. En la siguiente imagen, está seleccionado el valor predeterminado **Mi área de trabajo**.

    ![Seleccionar el área de trabajo en la que va a publicar el conjunto de datos y el informe](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Seleccionar el área de trabajo en la que va a publicar el conjunto de datos y el informe") 

3. Después de que la publicación se haya realizado correctamente, haga clic en **Abrir 'BuildingTemperature.pbix' en Power BI**.

    ![Publicación correcta; haga clic para introducir las credenciales](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publicación correcta; haga clic para introducir las credenciales") 

4. En el servicio Power BI, haga clic en **Escribir credenciales**.

    ![Escribir credenciales en el servicio Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Escribir credenciales en el servicio Power BI")

5. Haga clic en **Editar credenciales**.

    ![Editar credenciales en el servicio Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Editar credenciales en el servicio Power BI")

6. Escriba la información de la cuenta de inicio de sesión de HDInsight y haga clic en **Conectar**. El nombre de cuenta predeterminado es *admin*.

    ![Iniciar sesión en el clúster de Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Iniciar sesión en el clúster de Spark")

7. En el panel izquierdo, vaya a **Áreas de trabajo** > **Mi área de trabajo** > **INFORMES** y haga clic en **BuildingTemperature**.

    ![Informe que aparece en el panel izquierdo, debajo de Informes](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Informe que aparece en el panel izquierdo, debajo de Informes")

    También verá **BuildingTemperature** en el panel izquierdo, debajo de **CONJUNTOS DE DATOS**.

    Ahora el objeto visual creado en Power BI Desktop está disponible en el servicio Power BI. 

8. Mantenga el cursor sobre la visualización y haga clic en el icono de anclaje en la esquina superior derecha.

    ![Informe del servicio Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Informe del servicio Power BI")

9. Seleccione "Nuevo panel", escriba el nombre `Building temperature` y después haga clic en **Anclar**.

    ![Anclar al nuevo panel](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Anclar al nuevo panel")

10. En el informe, haga clic en **Ir al panel**. 

El objeto visual se ancla al panel. Puede agregar otros elementos visuales al informe y anclarlos al mismo panel. Para obtener más información sobre los informes y los paneles, consulte [Informes de Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) y [Paneles de Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

<!--
## <a name="tableau"></a>Use Tableau Desktop 

> [!NOTE]
> This section is applicable only for Spark 1.5.2 clusters created in Azure HDInsight.
>
>

1. Install [Tableau Desktop](http://www.tableau.com/products/desktop) on the computer where you are running this Apache Spark BI tutorial.

2. Make sure that computer also has Microsoft Spark ODBC driver installed. You can install the driver from [here](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Launch Tableau Desktop. In the left pane, from the list of server to connect to, click **Spark SQL**. If Spark SQL is not listed by default in the left pane, you can find it by click **More Servers**.
2. In the Spark SQL connection dialog box, provide the values as shown in the screenshot, and then click **OK**.

    ![Connect to a cluster for Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Connect to a cluster for Apache Spark BI")

    The authentication drop-down lists **Microsoft Azure HDInsight Service** as an option, only if you installed the [Microsoft Spark ODBC Driver](http://go.microsoft.com/fwlink/?LinkId=616229) on the computer.
3. On the next screen, from the **Schema** drop-down, click the **Find** icon, and then click **default**.

    ![Find schema for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Find schema for Apache Spark BI")
4. For the **Table** field, click the **Find** icon again to list all the Hive tables available in the cluster. You should see the **hvac** table you created earlier using the notebook.

    ![Find table for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Find table for Apache Spark BI")
5. Drag and drop the table to the top box on the right. Tableau imports the data and displays the schema as highlighted by the red box.

    ![Add tables to Tableau for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Add tables to Tableau for Apache Spark BI")
6. Click the **Sheet1** tab at the bottom left. Make a visualization that shows the average target and actual temperatures for all buildings for each date. Drag **Date** and **Building ID** to **Columns** and **Actual Temp**/**Target Temp** to **Rows**. Under **Marks**, select **Area** to use an area map for Spark data visualization.

     ![Add fields for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Add fields for Spark data visualization")
7. By default, the temperature fields are shown as aggregate. If you want to show the average temperatures instead, you can do so from the drop-down, as shown in the following screenshot:

    ![Take average of temperature for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Take average of temperature for Spark data visualization")

8. You can also super-impose one temperature map over the other to get a better feel of difference between target and actual temperatures. Move the mouse to the corner of the lower area map until you see the handle shape highlighted in a red circle. Drag the map to the other map on the top and release the mouse when you see the shape highlighted in red rectangle.

    ![Merge maps for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Merge maps for Spark data visualization")

     Your data visualization should change as shown in the screenshot:

    ![Tableau output for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Tableau output for Spark data visualization")
9. Click **Save** to save the worksheet. You can create dashboards and add one or more sheets to it.
-->

## <a name="next-steps"></a>pasos siguientes

Hasta ahora ha aprendido a crear un clúster, a crear tramas de datos de Spark para consultar los datos y luego a acceder a esos datos desde herramientas de BI. Ahora puede ver instrucciones sobre cómo administrar los recursos de clúster y depurar los trabajos que se ejecutan en un clúster de HDInsight Spark.

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](apache-spark-job-debugging.md)

