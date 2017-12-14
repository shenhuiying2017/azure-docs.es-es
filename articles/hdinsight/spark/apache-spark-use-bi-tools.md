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
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 18f495864befafd26e7adafb5c01612222d2cfdf
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI mediante herramientas de visualización de datos con Azure HDInsight

Obtenga información sobre cómo utilizar [Microsoft Power BI](http://powerbi.microsoft.com) y [Tableau](http://www.tableau.com) para visualizar datos en un clúster de Apache Spark en Azure HDInsight.

## <a name="prerequisites"></a>Requisitos previos

* **Complete [Ejecución de consultas interactivas en un clúster Spark de HDInsight](./apache-spark-load-data-run-query.md)**.
* **Power BI**: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) y [suscripción de prueba de Power BI](https://app.powerbi.com/signupredirect?pbi_source=web) (opcional).
* **Tableau**: [Tableau Desktop](http://www.tableau.com/products/desktop) y [controlador ODBC de Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229).


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

    Power BI Desktop tiene toda la información necesaria para conectarse a los datos de carga y al clúster de Spark desde la tabla `hvac`. La tabla y las columnas que la forman se muestran en el panel **Campos**.  Vea la siguiente captura de pantalla.

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

## <a name="tableau"></a>Uso de Tableau Desktop 

> [!NOTE]
> Esta sección solo es aplicable a los clústeres de Spark 1.5.2 creados en Azure HDInsight.
>
>

1. Instale [Tableau Desktop](http://www.tableau.com/products/desktop) en el equipo donde vaya a ejecutar este tutorial de Apache Spark BI.

2. Asegúrese de que el equipo también tenga instalado el controlador ODBC de Microsoft Spark. Puede instalar el controlador desde [aquí](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Inicie Tableau Desktop. En el panel izquierdo, en la lista del servidor al que va a conectarse, haga clic en **Spark SQL**. Si Spark SQL no aparece de forma predeterminada en el panel izquierdo, puede hacer clic en **More Servers**(Más servidores) para buscarlo.
2. En el cuadro de diálogo de conexión de Spark SQL, proporcione los valores como se muestra en la captura de pantalla y luego haga clic en **OK**(Aceptar).

    ![Conexión con un clúster de Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Conexión con un clúster de Apache Spark BI")

    La lista desplegable de autenticación solo muestra **Servicio Microsoft Azure HDInsight** como opción si ha instalado el [controlador ODBC de Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229) en el equipo.
3. En la siguiente pantalla, en la lista desplegable **Schema** (Esquema), haga clic en el icono **Find** (Buscar) y luego en **default** (predeterminado).

    ![Búsqueda del esquema de Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Búsqueda del esquema de Apache Spark BI")
4. En el campo **Table** (Tabla), vuelva a hacer clic en el icono **Find** (Buscar) para ver todas las tablas de Hive disponibles en el clúster. Debería ver la tabla **hvac** que creó antes con el cuaderno.

    ![Búsqueda de la tabla de Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Búsqueda de la tabla de Apache Spark BI")
5. Arrastre y coloque la tabla en el cuadro superior a la derecha. Tableau importa los datos y muestra el esquema como se resalta con el cuadro rojo.

    ![Incorporación de tablas a Tableau para Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Incorporación de tablas a Tableau para Apache Spark BI")
6. Haga clic en la pestaña **Sheet1** (Hoja1) en la parte inferior izquierda. Realice una visualización que muestre el promedio de temperaturas objetivo y reales de todos los edificios para cada fecha. Arrastre **Date** (Fecha) y **Building ID** (Id. de edificio) a **Columns** (Columnas), y **Actual Temp**/**Target Temp** (Temperatura real/Temperatura objetivo) a **Rows** (Filas). En **Marks** (Marcas), seleccione **Area** (Área) para usar un mapa de áreas para visualización de datos de Spark.

     ![Incorporación de campos para la visualización de datos de Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Incorporación de campos para la visualización de datos de Spark")
7. De manera predeterminada, se muestran los campos de temperatura como agregado. Si desea mostrar el promedio de las temperaturas en su lugar, puede hacerlo en la lista desplegable, como se muestra en la siguiente captura de pantalla.

    ![Realización del promedio de temperatura para la visualización de datos de Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Realización del promedio de temperatura para la visualización de datos de Spark")

8. También puede superponer un mapa de temperatura al otro para hacerse una idea más clara de la diferencia entre las temperaturas reales y objetivo. Mueva el mouse a la esquina del gráfico de área inferior hasta que vea la forma del controlador resaltada en un círculo rojo. Arrastre el gráfico al otro gráfico de encima y suelte el mouse cuando vea la forma resaltada en el rectángulo rojo.

    ![Combinación de mapas para la visualización de datos de Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Combinación de mapas para la visualización de datos de Spark")

     La visualización de los datos cambia, como se muestra en la captura de pantalla:

    ![Salida de Tableau para la visualización de datos de Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Salida de Tableau para la visualización de datos de Spark")
9. Haga clic en **Save** (Guardar) para guardar la hoja de cálculo. Puede crear paneles y agregarles una o varias hojas.

## <a name="next-steps"></a>Pasos siguientes

Hasta ahora ha aprendido a crear un clúster, a crear tramas de datos de Spark para consultar los datos y luego a acceder a esos datos desde herramientas de BI. Ahora puede ver instrucciones sobre cómo administrar los recursos de clúster y depurar los trabajos que se ejecutan en un clúster de HDInsight Spark.

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](apache-spark-job-debugging.md)

