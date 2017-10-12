---
title: "Ejemplo de aprendizaje automático con Spark MLlib en HDInsight - Azure | Microsoft Docs"
description: "Aprenda a usar Spark MLlib para crear una aplicación de aprendizaje automático que analice un conjunto de datos usando la clasificación mediante una regresión logística."
keywords: "aprendizaje automático con spark, ejemplo de aprendizaje automático con spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c0fd4baa-946d-4e03-ad2c-a03491bd90c8
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: fef4a48c6356addad0dc12a2df5799e1acb4ddc3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Usar Spark MLlib para compilar una aplicación de aprendizaje automático y analizar un conjunto de datos

Aprenda a usar Spark **MLlib** para crear una aplicación de aprendizaje automático para efectuar análisis predictivos simples en un conjunto de datos abierto. De las bibliotecas de aprendizaje automático integradas de Spark, en este ejemplo se usa una *clasificación* mediante una regresión logística. 

> [!TIP]
> Este ejemplo también está disponible en formato de cuaderno de Jupyter en un clúster Spark (Linux) que se crea en HDInsight. La experiencia del cuaderno le permite ejecutar los fragmentos de código de Python desde el propio Bloc de notas. Para seguir el tutorial desde un cuaderno, cree un clúster de Spark y abra un cuaderno de Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`). Luego, ejecute el cuaderno **Spark Machine Learning - Predictive analysis on food inspection data using MLlib.ipynb** en la carpeta **Python**.
>
>

MLlib es una biblioteca básica de Spark que proporciona numerosas utilidades que resultan prácticas para las tareas de aprendizaje automático, como utilidades adecuadas para:

* clasificación
* Regresión
* Agrupación en clústeres
* Modelado de tema
* Descomposición en valores singulares (SVD) y Análisis de los componentes principales (PCA)
* Comprobación de hipótesis y cálculo de estadísticas de ejemplo

## <a name="what-are-classification-and-logistic-regression"></a>¿Qué entendemos por clasificación y por regresión logística?
*Clasificación*, una tarea habitual en el aprendizaje automático, es el proceso de ordenación de datos de entrada en categorías. El trabajo de averiguar cómo asignar "etiquetas" a las entradas de datos que se proporcionen lo realiza un algoritmo de clasificación. Por ejemplo, imagínese un algoritmo de aprendizaje automático que acepta información bursátil como entrada y divide las existencias en dos categorías: acciones que se deberían vender y acciones que se deberían conservar.

La regresión logística es el algoritmo que se usa para la clasificación. La API de regresión logística de Spark es útil para la *clasificación binaria*, o para la clasificación de datos de entrada en uno de los dos grupos. Para obtener más información acerca de la regresión logística, consulte [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

En resumen, el proceso de regresión logística genera una *función logística* que se puede usar para predecir la probabilidad de que un vector de entrada pertenezca a un grupo o al otro.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Ejemplo de análisis predictivo en datos de inspección alimentaria
En este ejemplo usará Spark para llevar a cabo un análisis predictivo sobre unos datos de inspección alimentaria (**Food_Inspections1.csv**) que se han adquirido a través del [portal de datos de la ciudad de Chicago](https://data.cityofchicago.org/). Este conjunto de datos contiene información sobre las inspecciones alimentarias que se llevaron a cabo en establecimientos de Chicago (información sobre cada establecimiento, las infracciones que se detectaron (en caso de detectarse alguna) y los resultados de la inspección). El archivo de datos CSV ya está disponible en la cuenta de almacenamiento asociada al clúster, en **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

En los pasos siguientes, va a desarrollar un modelo para ver lo que es necesario para superar o no una inspección de alimentos.

## <a name="start-building-a-spark-mmlib-machine-learning-app"></a>Empezar a compilar una aplicación de aprendizaje automático de Spark MLlib
1. Desde [Azure Portal](https://portal.azure.com/), en el panel de inicio, haga clic en el icono del clúster Spark (si lo ancló al panel de inicio). También puede navegar hasta el clúster en **Examinar todo** > **Clústeres de HDInsight**.   
1. En la hoja del clúster Spark, haga clic en **Panel de clúster** y, luego, en **Jupyter Notebook**. Cuando se le pida, escriba las credenciales del clúster.

   > [!NOTE]
   > También puede comunicarse con el equipo Jupyter Notebook en el clúster si abre la siguiente dirección URL en el explorador. Reemplace **CLUSTERNAME** por el nombre del clúster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
1. Cree un cuaderno. Haga clic en **Nuevo** y, luego, en **PySpark**.

    ![Crear un cuaderno de Jupyter](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/spark-machine-learning-create-jupyter.png "Crear un nuevo cuaderno de Jupyter")
1. Se crea y se abre un nuevo cuaderno con el nombre Untitled.pynb. Haga clic en el nombre del cuaderno en la parte superior y escriba un nombre descriptivo.

    ![Proporcionar un nombre para el cuaderno](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/spark-machine-learning-name-jupyter.png "Proporcionar un nombre para el cuaderno")
1. Dado que creó un cuaderno con el kernel PySpark, no necesitará crear ningún contexto explícitamente. Los contextos de Spark y Hive se crean automáticamente al ejecutar la primera celda de código. Puede empezar a crear la aplicación de aprendizaje automático importando los tipos necesarios para este escenario. Para ello, coloque el cursor en la celda y presione **MAYÚS + ENTRAR**.

        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
        from pyspark.sql.functions import UserDefinedFunction
        from pyspark.sql.types import *

## <a name="construct-an-input-dataframe"></a>Construcción de una trama de datos de entrada
Podemos usar `sqlContext` para realizar las transformaciones de datos estructurados. La primera tarea consiste en cargar los datos de ejemplo (**[Food_Inspections1.csv**]) en una *trama de datos* de Spark SQL.

1. Dado que los datos sin procesar están en formato CSV, tiene que usar el contexto de Spark para extraer cada línea del archivo en memoria como texto no estructurado; a continuación, utilice la biblioteca CSV de Python para analizar cada línea individualmente.

        def csvParse(s):
            import csv
            from StringIO import StringIO
            sio = StringIO(s)
            value = csv.reader(sio).next()
            sio.close()
            return value

        inspections = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                        .map(csvParse)
1. Ahora tenemos el archivo CSV como un conjunto RDD.  Vamos a recuperar una fila del RDD para comprender el esquema de los datos.

        inspections.take(1)

    Debe ver algo parecido a lo siguiente:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [['413707',
          'LUNA PARK INC',
          'LUNA PARK  DAY CARE',
          '2049789',
          "Children's Services Facility",
          'Risk 1 (High)',
          '3250 W FOSTER AVE ',
          'CHICAGO',
          'IL',
          '60625',
          '09/21/2010',
          'License-Task Force',
          'Fail',
          '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
          '41.97583445690982',
          '-87.7107455232781',
          '(41.97583445690982, -87.7107455232781)']]
1. La salida anterior nos da una idea del esquema del archivo de entrada. Incluye el nombre de cada establecimiento, el tipo de establecimiento, la dirección, los datos de las inspecciones y la ubicación, entre otras cosas. Seleccionemos algunas columnas que nos van a ser útiles para nuestro análisis predictivo y agrupemos los resultados como una trama de datos, que luego usaremos para crear una tabla temporal.

        schema = StructType([
        StructField("id", IntegerType(), False),
        StructField("name", StringType(), False),
        StructField("results", StringType(), False),
        StructField("violations", StringType(), True)])

        df = sqlContext.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
        df.registerTempTable('CountResults')
1. Ahora tenemos una *trama de datos*, `df`, en la que podemos realizar el análisis. También contamos con una llamada a la tabla temporal **CountResults**. Hemos incluido cuatro columnas de interés en la trama de datos: **id** (identificador), **name** (nombre), **results** (resultados) y **violations** (infracciones).

    Vamos a obtener una pequeña muestra de los datos:

        df.show(5)

    Debe ver algo parecido a lo siguiente:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        +------+--------------------+-------+--------------------+
        |    id|                name|results|          violations|
        +------+--------------------+-------+--------------------+
        |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
        |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
        |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
        |413708|BENCHMARK HOSPITA...|   Pass|                    |
        |413722|           JJ BURGER|   Pass|                    |
        +------+--------------------+-------+--------------------+

## <a name="understand-the-data"></a>Comprensión de los datos
1. Vamos a empezar a hacernos una idea de lo que contiene el conjunto de datos. Por ejemplo, ¿cuáles son los diferentes valores en la columna **results** ?

        df.select('results').distinct().show()

    Debe ver algo parecido a lo siguiente:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        +--------------------+
        |             results|
        +--------------------+
        |                Fail|
        |Business Not Located|
        |                Pass|
        |  Pass w/ Conditions|
        |     Out of Business|
        +--------------------+
1. Una visualización rápida puede ayudarnos a razonar sobre la distribución de estos resultados. Ya tenemos los datos en una tabla temporal **CountResults**. Puede ejecutar la siguiente consulta SQL en la tabla para una mejor descripción de cómo se distribuyen los resultados.

        %%sql -o countResultsdf
        SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results

    La instrucción mágica `%%sql` seguida de `-o countResultsdf` garantiza que el resultado de la consulta persista localmente en el servidor de Jupyter (normalmente, el nodo principal del clúster). El resultado se conserva como una trama de datos [Pandas](http://pandas.pydata.org/) con el nombre especificado **countResultsdf**.

    Debe ver algo parecido a lo siguiente:

    ![Salida de la consulta SQL](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "Salida de la consulta SQL")

    Para más información sobre la instrucción mágica `%%sql`, así como otras instrucciones mágicas disponibles con el kernel de PySpark, consulte [Kernels disponibles en Jupyter Notebook con clústeres de Spark en HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).
1. También puede utilizar Matplotlib, una biblioteca que se usa para construir la visualización de datos, para crear un gráfico. Dado que el gráfico debe crearse a partir de la trama de datos localmente persistente **countResultsdf**, el fragmento de código debe comenzar con la instrucción mágica `%%local`. Esto garantiza que el código se ejecuta localmente en el servidor de Jupyter.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt

        labels = countResultsdf['results']
        sizes = countResultsdf['cnt']
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')

    Debe ver algo parecido a lo siguiente:

    ![Salida de la aplicación de aprendizaje automático de Spark: gráfico circular con cinco resultados de inspección distintos](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Salida de resultados de aprendizaje automático de Spark")
1. Vemos que una inspección puede tener cinco resultados diferentes:

   * Business not located (no se encontró el negocio)
   * Fail (no superado)
   * Pass (pasado)
   * Pss w/ conditions (superado con condiciones)
   * Out of Business (negocio cerrado)

     Vamos a desarrollar un modelo que pueda predecir el resultado de una inspección de alimentos, teniendo en cuenta las infracciones (violations). Puesto que la regresión logística es un método de clasificación binaria, tiene sentido agrupar los datos en dos categorías: **Fail** y **Pass**. "Pass w/ Conditions" sigue siendo una categoría Pass (superado), por lo que cuando se entrena el modelo, consideraremos los dos resultados como equivalentes. Los datos que tienen los otros resultados ("Business Not Located" o "Out of Business") no son útiles, por lo que los eliminaremos de nuestro conjunto de aprendizaje. Esta eliminación no tiene importancia ya que estas dos categorías constituyen un porcentaje muy pequeño de los resultados.
1. Vamos a seguir y convertir nuestra trama de datos existente (`df`) en una trama de datos nueva, donde cada inspección se representa como un par de etiquetas de infracción. En nuestro caso, una etiqueta de `0.0` representa un resultado de "no superado", una etiqueta de `1.0` representa un resultado de "pasado" y una etiqueta de `-1.0` representa resultados que no sean los dos anteriores. Filtraremos esos otros resultados al calcular la nueva trama de datos.

        def labelForResults(s):
            if s == 'Fail':
                return 0.0
            elif s == 'Pass w/ Conditions' or s == 'Pass':
                return 1.0
            else:
                return -1.0
        label = UserDefinedFunction(labelForResults, DoubleType())
        labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')

    Vamos a recuperar una fila para ver el aspecto de los datos etiquetados.

        labeledData.take(1)

    Debe ver algo parecido a lo siguiente:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Creación de un modelo de regresión logística a partir de la trama de datos de entrada
Nuestra última tarea consiste en convertir los datos etiquetados a un formato que se pueda analizar con regresión logística. La entrada a un algoritmo de regresión logística debe ser un conjunto de *pares de vector de característica de etiqueta*, donde el "vector de característica" es un vector de números que representa el punto de entrada. Por lo tanto, tenemos que convertir la columna "violations", que está semiestructurada y contiene numerosos comentarios de texto sin formato, en una matriz de números reales que una máquina pueda comprender.

Un enfoque estándar en el aprendizaje automático para procesar el lenguaje natural consiste en asignar a cada palabra diferente un "índice" y, a continuación, pasar un vector al algoritmo de aprendizaje automático, de forma que el valor de cada índice contenga la frecuencia relativa de esa palabra en la cadena de texto.

MLlib proporciona una forma sencilla de efectuar esta operación. En primer lugar, vamos a tratar como tokens todas las cadenas de infracciones para obtener las palabras de cada cadena. Luego, use un `HashingTF` para convertir cada conjunto de tokens en un vector de característica que se pueda pasar al algoritmo de regresión logística para construir un modelo. Llevaremos a cabo todos estos pasos en secuencia mediante una "canalización".

    tokenizer = Tokenizer(inputCol="violations", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

    model = pipeline.fit(labeledData)

## <a name="evaluate-the-model-on-a-separate-test-dataset"></a>Evaluación del modelo en un conjunto de datos de prueba independiente
Podemos usar el modelo que hemos creado anteriormente para *predecir* cuáles serán los resultados de las nuevas inspecciones, basándose en las infracciones que se han observado. Hemos probado este modelo en el conjunto de datos **Food_Inspections1.csv**. Ahora, vamos a usar un segundo conjunto, **Food_Inspections2.csv**, para *evaluar* la solidez de este modelo con nuevos datos. Este segundo conjunto de datos (**Food_Inspections2.csv**) debe estar ya en el contenedor de almacenamiento predeterminado asociado al clúster.

1. El siguiente fragmento de código crea una trama de datos, **predictionsDf**, que contiene la predicción generada por el modelo. El fragmento de código también crea una tabla temporal, llamada **Predictions**, basada en la trama de datos.

        testData = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                 .map(csvParse) \
                 .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
        testDf = sqlContext.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
        predictionsDf = model.transform(testDf)
        predictionsDf.registerTempTable('Predictions')
        predictionsDf.columns

    Debe ver algo parecido a lo siguiente:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        ['id',
         'name',
         'results',
         'violations',
         'words',
         'features',
         'rawPrediction',
         'probability',
         'prediction']
1. Examine una de las predicciones. Ejecute este fragmento de código:

        predictionsDf.take(1)

   Hay una predicción para la primera entrada en el conjunto de datos de prueba.
1. El método `model.transform()` aplica la misma transformación a todos los datos nuevos que tengan el mismo esquema y llega a una predicción sobre cómo clasificar los datos. Podemos hacer algunas estadísticas muy sencillas para hacernos una idea del grado de precisión alcanzado por nuestras predicciones:

        numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                              (prediction = 1 AND (results = 'Pass' OR
                                                                   results = 'Pass w/ Conditions'))""").count()
        numInspections = predictionsDf.count()

        print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
        print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"

    El resultado tendrá un aspecto similar al siguiente:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        There were 9315 inspections and there were 8087 successful predictions
        This is a 86.8169618894% success rate

    El uso de la regresión logística con Spark nos ofrece un modelo preciso de la relación entre las descripciones de las infracciones en inglés y el hecho de si un negocio determinado superará o no una inspección de alimentos.

## <a name="create-a-visual-representation-of-the-prediction"></a>Creación de una representación visual de la predicción
Ahora podemos crear una visualización final que nos ayude a analizar los resultados de esta prueba.

1. Empezaremos por extraer los distintos resultados y predicciones de la tabla temporal **Predictions** creada anteriormente. Las siguientes consultas separan la salida en distintos grupos: *true_positive*, *false_positive*, *true_negative* y *false_negative*. En las consultas siguientes, hay que desactivar la visualización mediante el uso de `-q` y guardar también la salida (usando `-o`) como tramas de datos que pueden usarse con la instrucción mágica `%%local`.

        %%sql -q -o true_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'

        %%sql -q -o false_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')

        %%sql -q -o true_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'

        %%sql -q -o false_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
1. Por último, utilice el siguiente fragmento de código para generar el gráfico mediante **Matplotlib**.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt

        labels = ['True positive', 'False positive', 'True negative', 'False negative']
        sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')

    Debería ver la siguiente salida:

    ![Salida de la aplicación de aprendizaje automático de Spark: gráfico circular con porcentajes de inspecciones alimentarias no superadas](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Salida de resultados de aprendizaje automático de Spark")

    En este gráfico, un resultado "positivo" hace referencia a la inspección de alimentos no superada, mientras que un resultado negativo hace referencia a una inspección pasada.

## <a name="shut-down-the-notebook"></a>Cierre del cuaderno
Cuando haya terminado de ejecutar la aplicación, deberá cerrar el cuaderno para liberar los recursos. Para ello, en el menú **Archivo** del cuaderno, haga clic en **Cerrar y detener**. Con esta acción se cerrará el cuaderno.

## <a name="seealso"></a>Otras referencias
* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark Streaming: Use Spark in HDInsight for building real-time streaming applications](hdinsight-apache-spark-eventhub-streaming.md)
* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones de Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)
