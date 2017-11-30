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
ms.date: 10/24/2017
ms.author: jgao
ms.openlocfilehash: fabf48da80cf44e82068d180c896ebbca7078d18
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI mediante herramientas de visualización de datos con Azure HDInsight

Obtenga información sobre cómo utilizar Power BI y Tableau para visualizar datos en un clúster de Apache Spark en Azure HDInsight.

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](apache-spark-jupyter-spark-sql.md).
* Datos de ejemplo en el clúster. Para obtener instrucciones, consulte [Ejecución de consultas interactivas en un clúster de HDInsight Spark](apache-spark-load-data-run-query.md).
* Power BI: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) y [suscripción de prueba de Power BI](https://app.powerbi.com/signupredirect?pbi_source=web) (opcional).
* Tableau: [Tableau Desktop](http://www.tableau.com/products/desktop) y [controlador ODBC de Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229).


## <a name="hivetable"></a>Revisar los datos de ejemplo

El bloc de notas de Jupyter que creó en el [tutorial anterior](apache-spark-load-data-run-query.md) incluye código para crear una tabla `hvac`. Esta tabla se basa en el archivo CSV en todos los clústeres de Spark de HDInsight en **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Revisaremos los datos en el clúster de Spark antes de crear visualizaciones.

1. Compruebe que la tabla esperada existe. En una celda vacía del cuaderno, copie el siguiente fragmento de código y presione **MAYÚS + ENTRAR**.

        %%sql
        SHOW TABLES

    Verá unos resultados como los que se muestran a continuación:

    ![Se muestran tablas en Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Si ha cerrado el bloc de notas antes de iniciar este tutorial, `hvactemptable` se limpia, por lo que no se incluye en los resultados.
    Desde las herramientas de BI, solo se puede acceder a las tablas de Hive almacenadas en Metastore (indicadas como **False** en la columna **isTemporary**). En este tutorial, nos conectamos a la tabla **hvac** que hemos creado.

2. Compruebe que la tabla contenga los datos previstos. En una celda vacía del cuaderno, copie el siguiente fragmento de código y presione **MAYÚS + ENTRAR**.

        %%sql
        SELECT * FROM hvac LIMIT 10

    Verá unos resultados como los que se muestran a continuación:

    ![Se muestran las filas de la tabla hvac en Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. Cierre el cuaderno para liberar los recursos. Para ello, en el menú **Archivo** del cuaderno, haga clic en **Cerrar y detener**.

## <a name="powerbi"></a>Uso de Power BI para la visualización de datos de Spark

Cuando haya verificado que los datos esperados existen, puede usar Power BI para crear visualizaciones, informes y paneles a partir de dichos datos. En este artículo se muestra un ejemplo sencillo con datos estáticos, pero si quiere ver ejemplos de streaming más complejos, comuníquenoslo en los comentarios.

### <a name="create-a-report-in-power-bi-desktop"></a>Creación de un informe en Power BI Desktop
Los primeros pasos para trabajar con Spark pasan por conectarse al clúster de Power BI Desktop, cargar datos del clúster y crear una visualización básica basada en dichos datos.

> [!NOTE]
> En estos momentos el conector que se muestra en este artículo está en una versión preliminar, pero le recomendamos que lo utilice y nos haga llegar los comentarios que tenga a través del sitio de la [Comunidad de Power BI](https://community.powerbi.com/) o de [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. En la pestaña **Inicio** de Power BI Desktop, haga clic en **Obtener datos** y luego en **Más**.

2. Busque `Spark`, seleccione **Azure HDInsight Spark** y después haga clic en **Conectar**.

    ![Obtención de datos en Power BI desde Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Obtención de datos en Power BI desde Apache Spark BI")

3. Escriba la dirección URL de clúster (en la forma `mysparkcluster.azurehdinsight.net`), seleccione **DirectQuery** y después haga clic en **Aceptar**.

    ![Conexión a Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "Conexión a Apache Spark BI")

    > [!NOTE]
    > Puede usar cualquier modo de conectividad con Spark. Si usa DirectQuery, los cambios se reflejan en los informes sin tener que actualizar el conjunto de datos completo. Si importa los datos, deberá actualizar el conjunto de datos para ver los cambios. Para obtener más información sobre cómo y cuándo se debe usar DirectQuery, consulte [Uso de DirectQuery en Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Introduzca la información de la cuenta de inicio de sesión de HDInsight en los campos **Nombre de usuario** y **Contraseña** (la cuenta predeterminada es `admin`) y haga clic en **Conectar**.

    ![Nombre de usuario y contraseña del clúster de Spark](./media/apache-spark-use-bi-tools/user-password.png "Nombre de usuario y contraseña del clúster de Spark")

5. Seleccione la tabla `hvac` y espere para obtener una vista previa de los datos. Después haga clic en **Cargar**.

    ![Nombre de usuario y contraseña del clúster de Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Nombre de usuario y contraseña del clúster de Spark")

    Ahora Power BI Desktop tiene toda la información necesaria para conectarse a los datos de carga y al clúster de Spark desde la tabla `hvac`. La tabla y las columnas que la forman se muestran en el panel **CAMPOS**.

    ![Lista de tablas en el panel de Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "Lista de tablas en el panel de Apache Spark BI")

7. Cree una visualización para mostrar la variación entre la temperatura objetivo y la real para cada edificio: 

    1. En el panel **VISUALIZACIONES**, seleccione **Gráfico de áreas**. Arrastre el campo **BuildingID** a **Eje** y arrastre los campos **ActualTemp** y **TargetTemp** a **Valor**.

        ![Creación de visualizaciones de datos de Spark con Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Creación de visualizaciones de datos de Spark con Apache Spark BI")

    2. De manera predeterminada, la visualización muestra la suma de **ActualTemp** y **TargetTemp**. En el menú desplegable de ambos campos, seleccione **Media** para obtener un promedio de las temperaturas objetivo y reales de ambos edificios.

        ![Creación de visualizaciones de datos de Spark con Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Creación de visualizaciones de datos de Spark con Apache Spark BI")

    3. La visualización de datos debería parecerse a la que se muestra en la captura de pantalla. Mueva el cursor sobre la visualización para obtener información sobre herramientas con datos relevantes.

        ![Creación de visualizaciones de datos de Spark con Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Creación de visualizaciones de datos de Spark con Apache Spark BI")

11. Haga clic en **Archivo** y en **Guardar**, y después ponga el nombre `spark.pbix` al archivo. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publicar el informe en el servicio Power BI (opcional)
Ahora ya tiene un informe totalmente funcional en Power BI Desktop. Aunque puede detenerse aquí, muchos usuarios se sirven de las ventajas del servicio Power BI para facilitar el uso compartido de informes y paneles en su organización. 

En esta sección, puede publicar el conjunto de datos y el informe que se encuentra en el archivo de Power BI Desktop que ha creado. A continuación, ancle la visualización del informe al panel. Normalmente los paneles se usan para centrarse en un subconjunto de datos de un informe. En el informe solo tiene una visualización, pero sigue siendo útil para seguir los pasos.

1. En la pestaña **Inicio** de Power BI Desktop, haga clic en **Publicar**.

    ![Publicar en Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publicar en Power BI Desktop")

2. Seleccione el área de trabajo en la que publicar el conjunto de datos y el informe, y haga clic en **Seleccionar**. En la siguiente imagen, está seleccionado el valor predeterminado **Mi área de trabajo**.

    ![Seleccionar el área de trabajo en la que va a publicar el conjunto de datos y el informe](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Seleccionar el área de trabajo en la que va a publicar el conjunto de datos y el informe") 

3. Cuando aparezca un mensaje de confirmación en Power BI Desktop, haga clic en **Abrir “spark.pbix” en Power BI**.

    ![Publicación correcta; haga clic para introducir las credenciales](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publicación correcta; haga clic para introducir las credenciales") 

4. En el servicio Power BI, haga clic en **Escribir credenciales**.

    ![Escribir credenciales en el servicio Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Escribir credenciales en el servicio Power BI")

5. Haga clic en **Editar credenciales**.

    ![Editar credenciales en el servicio Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Editar credenciales en el servicio Power BI")

6. Escriba la información de la cuenta de inicio de sesión de HDInsight (normalmente se trata de la cuenta `admin` predeterminada utilizada en Power BI Desktop) y haga clic en **Iniciar sesión**.

    ![Iniciar sesión en el clúster de Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Iniciar sesión en el clúster de Spark")

7. En el panel izquierdo, vaya a **Áreas de trabajo** > **Mi área de trabajo** > **INFORMES** y haga clic en **spark**.

    ![Informe que aparece en el panel izquierdo, debajo de Informes](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Informe que aparece en el panel izquierdo, debajo de Informes")

    También verá **spark** en el panel izquierdo, debajo de **CONJUNTOS DE DATOS**.

8. Ahora el objeto visual creado en Power BI Desktop está disponible en el servicio. Para anclar este objeto visual a un panel, mantenga el puntero sobre el objeto visual y haga clic en el icono de anclaje.

    ![Informe del servicio Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Informe del servicio Power BI")

9. Seleccione "Nuevo panel", escriba el nombre `SparkDemo` y después haga clic en **Anclar**.

    ![Anclar al nuevo panel](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Anclar al nuevo panel")

10. En el informe, haga clic en **Ir al panel**. 

    ![Ir al panel](./media/apache-spark-use-bi-tools/apache-spark-bi-open-dashboard.png "Ir al panel")

El objeto visual se ancla al panel. Puede agregar otros elementos visuales al informe y anclarlos al mismo panel. Para obtener más información sobre los informes y los paneles, consulte [Informes de Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) y [Paneles de Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="tableau"></a>Uso de Tableau Desktop para la visualización de datos de Spark

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
7. De manera predeterminada, se muestran los campos de temperatura como agregado. Si desea mostrar el promedio de las temperaturas en su lugar, puede hacerlo en la lista desplegable, como se muestra a continuación.

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

