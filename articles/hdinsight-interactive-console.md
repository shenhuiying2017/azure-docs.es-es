<properties linkid="manage-services-hdinsight-howto-work-with-the-interactive-console" urlDisplayName="Interactive Console" pageTitle="How to work with the interactive console in HDInsight | Azure" metaKeywords="" description="In this guide, you'll learn how to perform common tasks such as file upload, run jobs, and visualize data using the interactive console for HDInsight Service." metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight Interactive JavaScript and Hive Consoles" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Consolas interactivas de HDInsight para Hive y JavaScript
=========================================================

El servicio HDInsight de Microsoft Azure incluye consolas interactivas tanto para JavaScript como para Hive. Estas consolas proporcionan una experiencia interactiva y simple de bucle de lectura, evaluación e impresión (REPL), donde los usuarios pueden escribir expresiones, evaluarlas y, a continuación, consultar y mostrar inmediatamente los resultados de un trabajo de MapReduce. La consola JavaScript ejecuta instrucciones Pig Latin. La consola Hive evalúa instrucciones de lenguaje de consulta Hive (Hive QL). Ambos tipos de instrucciones se compilan en programas de MapReduce. Administrar trabajos de Hadoop en estas consolas es mucho más sencillo que conectarse de manera remota al nodo principal del clúster de Hadoop y que trabajar directamente con programas de MapReduce.

**La consola JavaScript**: un shell de comandos que proporciona una interfaz fluida al ecosistema de Hadoop. Una interfaz fluida utiliza un método para encadenar instrucciones que transmite el contexto de una llamada en una secuencia a la llamada subsiguiente en esa secuencia. La consola JavaScript proporciona:

-   Acceso al clúster de Hadoop, sus recursos y los comandos del sistema de archivos distribuidos Hadoop (HDFS).
-   Administración y manipulación de datos entrantes y salientes del clúster de Hadoop.
-   Una interfaz fluida que evalúa instrucciones de Pig Latin y JavaScript para definir una serie de programas de MapReduce con el fin de crear flujos de trabajo de procesamiento de datos.

**La consola Hive**: Hive es un marco de trabajo de almacén de datos, basado en Hadoop, que proporciona análisis, consulta y administración de datos. Utiliza HiveQL, un dialecto de SQL, para consultar los datos almacenados en un clúster de Hadoop. La consola Hive proporciona:

-   Acceso al clúster de Hadoop, sus recursos y los comandos de HDFS.
-   Una implementación del marco de trabajo de Hive que puede ejecutar instrucciones de HiveQL en un clúster de Hadoop.
-   Un modelo de base de datos relacional para HDFS que le permite interactuar con los datos almacenados en el sistema de archivos distribuido como si esos datos estuviesen almacenados en tablas. La consola JavaScript utiliza Pig Latin, un lenguaje de flujo de datos, y la consola Hive utiliza HiveQL, un lenguaje de consulta.

Pig (y la consola JavaScript) será la opción de preferencia de quienes están más familiarizados con un enfoque de scripting, donde se utiliza una secuencia de transformaciones encadenadas (o fluidas) para definir un flujo de procesamiento de datos. También es una buena opción si tiene datos seriamente no estructurados.

Hive (y su consola) será la opción de preferencia de quienes están más familiarizados con SQL y un entorno de base de datos relacional. El uso de esquema y una abstracción de tabla en Hive significa que la experiencia es muy cercana a la que se encuentra normalmente en un RDBMS.

Pig y Hive proporciona lenguajes de nivel superior que se compilan en programas de MapReduce escritos en Java y que se ejecutan en el HDFS. Si realmente necesita un control preciso o alto rendimiento, deberá escribir directamente los programas de MapReduce.

Apartados de este tutorial
--------------------------

-   [Uso de la consola JavaScript para ejecutar un trabajo de MapReduce](#runjob)
-   [Uso de la consola JavaScript para mostrar gráficamente los resultados](#displayresults)
-   [Uso de la consola Hive para exportar los resultados a una tabla de Hive](#createhivetable)
-   [Uso de la consola Hive para consultar los datos en la tabla de Hive](#queryhivetable)

Uso de la consola JavaScript para ejecutar un trabajo de MapReduce
------------------------------------------------------------------

En esta sección, utilizará la consola JavaScript para ejecutar el ejemplo de WordCount que se incluye con el servicio HDInsight. Aquí, la ejecución de consultas de JavaScript utiliza la API fluida en niveles sobre Pig que proporciona la consola interactiva. El archivo de texto aquí analizado es la edición de *Los cuadernos de Leonardo Da Vinci* para el libro electrónico del proyecto Gutenberg. Se especifica un filtro para que los resultados del trabajo de MapReduce contengan solo las diez palabras más frecuentes.

1.  Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).
2.  Haga clic en **HDINSIGHT**. Aparecerá una lista de los clústeres de Hadoop implementados.
3.  Haga clic en el nombre del clúster de HDInsight al que desea conectarse.
4.  Haga clic en **Manage Cluster**.
5.  Escriba su credencial y, a continuación, haga clic en **Log On**.
6.  En el portal de HDInsight, haga clic en **Samples**.

    ![HDI.Tiles.Samples](./media/hdinsight-interactive-console/HDI.TileSamples.PNG)

7.  En la página **Hadoop Sample Gallery**, haga clic en el icono **WordCount**.
8.  Haga clic en **WordCount.js** en la esquina superior derecha y guarde el archivo en un directorio local, por ejemplo, en la carpeta .../downloads.

    ![HDI.JsConsole.WordCountDownloads](./media/hdinsight-interactive-console/HDI.JsConsole.WordCountDownloads.PNG)

9.  Haga clic en **Azure HDInsight** en la esquina superior izquierda para volver a la página del panel de clúster.
10. Haga clic en **Interactive Cluster** para iniciar la consola JavaScript.

    ![HDI.Tiles.InteractiveConsole](./media/hdinsight-interactive-console/HDI.TileInteractiveConsole.PNG)
11. Haga clic en **JavaScript** en la esquina superior derecha.
12. Ejecute el siguiente comando:

    fs.put()

13. Escriba los siguientes parámetros en la ventana **Upload a file**:

    -   **Origen:** \_..\\downloads\\Wordcount.js
    -   **Destino:** ./WordCount.js/

    ![HDI.JsConsole.UploadJs](./media/hdinsight-interactive-console/HDI.JsConsole.UploadJs.PNG)

    Explore la ubicación del archivo WordCount.js. Se requerirá la ruta de acceso local completa. El punto que aparece al inicio de la ruta de acceso de destino es necesario como parte de la dirección relativa en HDFS.

14. Haga clic en el botón **Upload**.

15. Ejecute los siguientes comandos para que aparezca el archivo y mostrar el contenido:

        #ls
        #cat WordCount.js

    ![HDI.JsConsole.JsCode](./media/hdinsight-interactive-console/HDI.JsConsole.JsCode.PNG)

    Observe que la función de asignación de JavaScript quita las letras mayúsculas del texto con el método "toLowerCase()" antes de contar el número de repeticiones de una palabra en la función de reducción.

16. Ejecute el siguiente comando para mostrar el archivo de datos que procesará el trabajo de MapReduce de WordCount:

		\#ls /example/data/gutenberg

1.  Ejecute el siguiente comando para ejecutar el programa de MapReduce:

        pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")

    Reemplace "admin" por el nombre de usuario de inicio de sesión actual.

    Observe que las instrucciones se "encadenan" juntas mediante el operador punto y que el archivo de salida tenga el nombre *DaVinciTop10Words*. En la siguiente sección, tendrá acceso al archivo de salida.

    Una vez completado, verá lo siguiente:

        pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")
        2013-04-25 18:54:28,116 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success! (Visualización de registro)

2.  Desplácese a la derecha y haga clic en **View Log** si desea observar el progreso del trabajo. Este registro también proporcionará diagnósticos en caso de que el trabajo no se complete. Cuando el trabajo sí se complete, verá el siguiente mensaje al final del registro:

        [main] INFO org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success! followed by a link to the log file.

3.  Ejecute el siguiente comando para ver el archivo de salida:

        #ls

    Observe que se creó una carpeta DavinciTop10Words.

Uso de la consola JavaScript para mostrar gráficamente los resultados
---------------------------------------------------------------------

En la última sección, ejecutó un trabajo de MapReduce para recuperar las diez palabras principales de un archivo de texto. El archivo de salida es ./DaVinciTop10Words.

1.  Ejecute el siguiente comando para ver los resultados en el directorio DaVinciTop10Words:

         file = fs.read("DaVinciTop10Words")

    El resultado se parecerá al siguiente:

         js> file=fs.read("DaVinciTop10Words")
         the    22966
         of 11228
         and    8428
         in 5737
         to 5296
         a  4791
         is 4261
         it 3073
         that   2903
         which  2544

2.  Ejecute el siguiente comando para analizar el contenido del archivo en un archivo de datos:

         data = parse(file.data, "word, count:long")

3.  Ejecute el siguiente comando para trazar los datos:

         graph.bar(data)

    ![HDI.JsConsole.BarGraphTop10Words](./media/hdinsight-interactive-console/HDI.JsConsole.BarGraphTop10Words.PNG)

Uso de la consola Hive para exportar los resultados a una tabla de Hive
-----------------------------------------------------------------------

En esta sección conocerá la consola interactiva Hive. Creará una tabla de Hive a partir de la salida del trabajo de MapReduce. La siguiente sección muestra cómo consultar los datos de esta tabla.

**Creación de la tabla de Hive**

1.  Haga clic en Hive en la esquina superior derecha para abrir la consola Hive.

2.  Escriba el siguiente comando para crear una tabla de dos columnas llamada *DaVinciWordCountTable* a partir de la salida de ejemplo WordCount que se guardó en la carpeta "DaVinciTop10Words":

         CREATE EXTERNAL TABLE DaVinciWordCountTable     
         (word STRING,
         count INT) 
         ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' 
         STORED AS TEXTFILE LOCATION '/user/admin/DaVinciTop10Words';   

    Reemplace "admin" por el nombre de usuario de inicio de sesión.

    Observe que la tabla se crea como una tabla EXTERNA para mantener la carpeta de destino independiente de la tabla. Además, observe que solo necesita especificar la carpeta en que se ubica el archivo de salida, no el nombre del archivo propiamente dicho.

3.  Haga clic en **EVALUATE** en la esquina inferior izquierda.

4.  Escriba los siguientes comandos para confirmar que se creó la tabla de dos columnas:

         SHOW TABLES;
         DESCRIBE DaVinciWordCountTable;

5.  Haga clic en **EVALUATE**.

    ![HDI.Hive.ShowDescribeTable][hdi-hive-showdescribetable]

Uso de la consola Hive para consultar los datos en la tabla de Hive
-------------------------------------------------------------------

1.  Ejecute el siguiente comando para consultar las diez palabras que se repiten con mayor frecuencia:

         SELECT word, count
         FROM DaVinciWordCountTable
         ORDER BY count DESC LIMIT 10

    Los resultados de esta consulta son:

         SELECT word, count FROM DaVinciWordCountTable ORDER BY count DESC LIMIT 10
         the 22966
         of 11228
         and 8428
         in 5737
         to 5296
         a 4791
         is 4261
         it 3073
         that 2903
         which 2544

Pasos siguientes
----------------

Ha visto cómo ejecutar un trabajo de Hadoop desde la consola JavaScript interactiva y cómo inspeccionar los resultados desde un trabajo con esta consola. También ha visto cómo se puede utilizar la consola interactiva Hive para inspeccionar y procesar los resultados de un trabajo de Hadoop mediante la creación y consulta de una tabla que contiene la salida desde un programa de MapReduce. Ha visto ejemplos de instrucciones de Pig Latin y de Hive QL que se utilizan en las consolas. Finalmente, ha visto cómo la naturaleza interactiva de REPL de las consolas JavaScript y Hive simplifica el uso de un clúster de Hadoop. Para obtener más información, consulte los artículos siguientes:

-   [Uso de Pig con HDInsight](/es-es/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Uso de Hive con HDInsight](/es-es/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Uso de MapReduce con HDInsight](/es-es/manage/services/hdinsight/using-mapreduce-with-hdinsight/)

[hdi-hive-showdescribetable]: ./media/hdinsight-interactive-console/HDI.Hive.ShowDescribeTable.PNG "Hive Table Confirmation")

