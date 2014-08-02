<properties linkid="manage-services-hdinsight-howto-social-data" urlDisplayName="Analyze Data with HDInsight" pageTitle="Analyzing Twitter data with Hive - Windows Azure Services" metaKeywords="" description="In this tutorial you will query, explore, and analyze data from Twitter using the HDInsight Service for Windows Azure and a complex Hive example." metaCanonical="" services="" documentationCenter="" title="Analyzing Twitter Data with Hive" authors="" solutions="" writer="wenming" manager="" editor="" />

Análisis de datos de Twitter con Hive
=====================================

En este tutorial, consultará, explorará y analizará datos de Twitter mediante el servicio HDInsight basado en Apache Hadoop para Azure y un ejemplo de Hive complejo. Los sitios web de las redes sociales constituyen una de las principales fuerzas motrices para la adopción de grandes datos. Las API públicas proporcionadas por sitios como Twitter constituyen un origen de datos muy útil para analizar y comprender las tendencias populares. Este tutorial asume que se ya ha creado un clúster de HDInsight mediante el [Portal de Azure](http://www.windowsazure.com).

Este tutorial se compone de los siguientes segmentos:

1.  [Obtención de una fuente de Twitter mediante la API de streaming de cURL y Twitter](#segment1)
2.  [Procesamiento de datos de Twitter de manera interactiva mediante Hive](#segment2)

## Obtención de una fuente de Twitter mediante la API de streaming de cURL y Twitter

1.  Este paso requiere curl.exe. Descargue el archivo curl apropiado del sistema operativo (como el SSL binario de Win64) en <http://curl.haxx.se/download.html>.

    ![Figura 3](./media/hdinsight-analyze-twitter-date-hive/Figure3.png)

2.  Descomprima **curl.exe** en la ubicación apropiada (como **C:\\twitterdata**)

3.  Con el Bloc de notas, cree estos dos archivos **get\_twitter\_stream.cmd** y **twitter\_params.txt** en la misma carpeta que **curl.exe**, de la forma siguiente:

    ![Figura 4](./media/hdinsight-analyze-twitter-date-hive/Figure4.png)

4.  Edite el archivo **twitter\_params.txt** para realizar el seguimiento de los tweets de la forma siguiente:

         track=weather,Azure,WindowsAzure,cloud

    Puede usar cualquiera de los temas o hashtags que prefiera.

5.  Edite el script de comandos de la ventana **get\_twitter\_stream.cmd**; para ello, agregue el nombre de usuario de Twitter en lugar de **USER** y la contraseña en lugar de **PASSWORD** en la línea siguiente:

         curl -d @twitter_parameters.txt -k https://stream.twitter.com/1/statuses/filter.json -u user:{password} >>twitter_stream_seq2.txt

6.  Ejecute el script get\_twitter\_stream.cmd desde un símbolo del sistema de la forma siguiente:

    ![Figura 5](./media/hdinsight-analyze-twitter-date-hive/Figure5.png)

    Puede ver algo así:

    ![Figura 6](./media/hdinsight-analyze-twitter-date-hive/Figure6.png)

7.  Puede detener el trabajo si presiona **Ctrl+C**. A continuación, puede cambiar el nombre del archivo y después reiniciar el script. Deje que el proceso se ejecute de 10 minutos a horas. Para este tutorial, limite el tamaño de datos a algunos cientos de megabytes.

    Los datos de Twitter se almacenan en formato JSON, que contiene una compleja estructura anidada. En el paso siguiente, en lugar de escribir varias líneas de código con un lenguaje de programación convencional, transformaremos esta estructura anidada en una tabla de Hive, para que se pueda consultar interactivamente mediante un lenguaje parecido a SQL denominado HQL.

## Procesamiento de datos de Twitter de manera interactiva mediante Hive

1.  Mientras se recopilan los tweets, cree una sesión de RDP en el clúster de HDInsight que ha creado mediante el portal de Azure.

    **Desplácese** a HDInsight y **seleccione** el clúster que ha creado. Haga clic en el icono **Connect RDP** en la parte inferior de la pantalla. Inicie sesión en RDP, para lo que debe especificar la contraseña. Cuando haya iniciado sesión, **abra** una ventana del Explorador y desplácese a c:.

    ![Conexión mediante el icono RDP](./media/hdinsight-analyze-twitter-date-hive/twitter-RDPconnect.PNG)

2.  Presione **Ctrl+C** para finalizar el proceso de recopilación de tweets de Curl y utilice el Explorador de Windows para desplazarse hasta donde reside el archivo de Twitter. **Haga clic con el botón secundario** para enviar a carpeta comprimida (en zip). De esta forma se ahorra tiempo de carga.

3.  Ahora, presione **Ctrl-C** en el archivo zip en el equipo local y desplácese a c:\\ en el directorio C: de la sesión de Escritorio remoto. Presione **Ctrl-V** después de hacer clic en la ventana del Explorador (C:) para cargar el archivo zip en la sesión RDP.

    ![Actualización de tweets en la sesión RDP](./media/hdinsight-analyze-twitter-date-hive/twitter-uploadingzip.PNG)

4.  Una vez cargado el archivo, **haga clic con el botón secundario** y **seleccione** Extraer todo en c:\\ para volver a obtener el archivo de texto original. **Abra** una ventana de línea de comandos de Hadoop para comenzar a trabajar con los comandos HIVE y Hadoop.

    ![Figura 26](./media/hdinsight-analyze-twitter-date-hive/Figure26.png)

5.  El primer paso es **escribir** C:\\ y, a continuación, **presionar Entrar**. Le llevará a la carpeta c:\\ donde residen los datos de Twitter.

6.  A continuación, cree una carpeta en el HDFS o en el sistema de archivos distribuido (DFS) de Hadoop y después copie los datos de Twitter en la carpeta que acaba de crear con el modificador -copyFromLocal:

         hadoop fs -mkdir /example/data
         hadoop fs -copyFromLocal c:\twitter_stream_seq2.txt /example/data/

7.  Ahora los datos sin procesar de Twitter se han copiado en HDFS en el clúster de HDInsight. El paso siguiente consiste en crear una estructura de tabla sencilla para los datos que hemos cargado. Esta tabla estructurada temporal de Hive nos permite almacenar los datos y realizar un procesamiento adicional de ETL. Escriba notepad, pegue el código siguiente en el Bloc de notas y guárdelo como: "c:\\load\_twitter\_raw.hql"

         drop table twitter_raw;

         create table twitter_raw (
             json_response string
         ) 
         partitioned by (filesequence int);

         load data inpath '/example/data/twitter_stream_seq2.txt'
         into table twitter_raw
         partition (filesequence = 1);

8.  Después de guardar el archivo, ejecute Hive en la ventana de comandos:

         C:\apps\dist\hive-0.9.0\bin\hive -f load_twitter_raw.hql

9.  El proceso tardará unos segundos en completarse:

    ![Resultados sin procesar de la carga de Twitter](./media/hdinsight-analyze-twitter-date-hive/twitter-load-raw-results.PNG)

10. El gran conjunto de datos de Twitter en formato JSON anidado se ha transformado en una estructura de tabla temporal de Hive.

11. Antes de poder consultar el conjunto de datos de Twitter mediante Hive, hay que ejecutar otro proceso de ETL. Definiremos un esquema de tabla más detallado para los datos que hemos almacenado en la tabla "twitter\_raw". Este proceso ETL más complejo tarda más tiempo. En el símbolo del sistema, vuelva a iniciar el Bloc de notas para pegar el siguiente código de consulta de Hive y, a continuación, guárdelo como "c:\\twitter\_etl.hql.txt".

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode=nonstrict;

        drop table twitter_temp;

        create table twitter_temp
        (
            id bigint,
            created_at string,
            created_at_date string,
            created_at_year string,
            created_at_month string,
            created_at_day string,
            created_at_time string,
            in_reply_to_user_id_str string,
            text string,
            contributors string,
            retweeted string,
            truncated string,
            coordinates string,
            source string,
            retweet_count int,
            url string,
            hashtags array<string>,
            user_mentions array<string>,
            first_hashtag string,
            first_user_mention string,
            screen_name string,
            name string,
            followers_count int,
            listed_count int,
            friends_count int,
            lang string,
            user_location string,
            time_zone string,
            profile_image_url string,
            json_response string
        )
        partitioned by (filesequence int);

        from twitter_raw
        insert overwrite table twitter_temp
        partition (filesequence)
        select
            cast(get_json_object(json_response, '$.id_str') as bigint),

            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
                substr (get_json_object(json_response, '$.created_at'),27,4)),

            substr (get_json_object(json_response, '$.created_at'),27,4),

            case substr (get_json_object(json_response,     '$.created_at'),5,3)
                when "Jan" then "01"
                when "Feb" then "02"
                when "Mar" then "03"
                when "Apr" then "04"
                when "May" then "05"
                when "Jun" then "06"
                when "Jul" then "07"
                when "Aug" then "08"
                when "Sep" then "09"
                when "Oct" then "10"
                when "Nov" then "11"
                when "Dec" then "12" end,

            substr (get_json_object(json_response, '$.created_at'),9,2),

            substr (get_json_object(json_response, '$.created_at'),12,8),

            get_json_object(json_response, '$.in_reply_to_user_id_str'),
            get_json_object(json_response, '$.text'),
            get_json_object(json_response, '$.contributors'),
            get_json_object(json_response, '$.retweeted'),
            get_json_object(json_response, '$.truncated'),
            get_json_object(json_response, '$.coordinates'),
            get_json_object(json_response, '$.source'),
            cast (get_json_object(json_response, '$.retweet_count') as int),
            get_json_object(json_response, '$.entities.display_url'),
            array(  
                trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
                trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
            array(
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
                trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            get_json_object(json_response, '$.user.screen_name'),
            get_json_object(json_response, '$.user.name'),
            cast (get_json_object(json_response, '$.user.followers_count') as int),
            cast (get_json_object(json_response, '$.user.listed_count') as int),
            cast (get_json_object(json_response, '$.user.friends_count') as int),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response,
            filesequence
        where (length(json_response) > 500);

12. Ahora escriba en:

        C:\apps\dist\hive-0.9.0\bin\hive -f twitter_etl.hql.txt

13. Este script de Hive complejo iniciará un gran conjunto de trabajos de MapReduce realizado por el clúster de Hadoop. Dependiendo del conjunto de datos y del tamaño del clúster, este puede tardar unos 10 minutos.

    ![ETL compleja de Twitter](./media/hdinsight-analyze-twitter-date-hive/twitter-etl-complex.PNG)

14. También puede supervisar el progreso del trabajo mediante la página de seguimiento de trabajos en el nodo principal; para ello, haga doble clic en el acceso directo "Hadoop MapReduce Status" en el escritorio.

    ![Supervisión y seguimiento de trabajos](./media/hdinsight-analyze-twitter-date-hive/twitter_longjob_browser.PNG)

15. Cuando finaliza el proceso de ETL, inicie Hive:

        C:\apps\dist\hive-0.9.0\bin\hive

16. Puede experimentar con consultas de Hive sencillas como:

		select name, screen\_name, count(1) as cc from twitter\_temp where text like "%Azure%" group by name,screen\_name order by cc desc limit 10;

1.  Esta consulta devolverá una lista de los usuarios de Twitter que envían más tweets que contienen la palabra "Azure".

    ![Resultado final](./media/hdinsight-analyze-twitter-date-hive/twitter_final_query_result.PNG)

## Resumen

En este tutorial hemos visto cómo transformar un conjunto de datos JSON no estructurado en una tabla de Hive estructurada para consultar, explorar y analizar datos desde Twitter mediante HDInsight en Azure. Si desea obtener información acerca de actualizaciones y archivos de compatibilidad, puede encontrarlos en el repositorio de GitHub [aquí](https://github.com/wenming/BigDataSamples/tree/master/twittersample).

Pasos siguientes
----------------

Si bien este artículo muestra cómo usar la línea de comandos de Hadoop, también puede realizar tareas con la consola interactiva del servicio de HDInsight. Para obtener más información, consulte [Guidance: HDInsight Interactive JavaScript and Hive Consoles][interactive-console].

