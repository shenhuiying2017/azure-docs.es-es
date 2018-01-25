---
title: "Análisis de datos de Twitter con Apache Hive en Azure HDInsight | Microsoft Docs"
description: "Obtenga información sobre cómo usar Hive y Hadoop en HDInsight para transformar datos sin procesar de Twitter en una tabla de Hive que permite realizar búsquedas."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e1e249ed-5f57-40d6-b3bc-a1b4d9a871d3
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: b6e540576bc4a5876bc8546262a181bd82ad9727
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="analyze-twitter-data-using-hive-and-hadoop-on-hdinsight"></a>Análisis de datos de Twitter con Hive y Hadoop en HDInsight

Obtenga información sobre cómo utilizar Apache Hive para procesar los datos de Twitter. El resultado es una lista de usuarios de Twitter que enviaron la mayoría de los tweets que contienen una palabra determinada.

> [!IMPORTANT]
> Los pasos de este documento se probaron en HDInsight 3.6.
>
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="get-the-data"></a>Obtener los datos

Twitter permite recuperar los [datos de cada tweet](https://dev.twitter.com/docs/platform-objects/tweets) como documento de JavaScript Object Notation (JSON) a través de una API de REST. [OAuth](http://oauth.net) para autenticación en la API.

### <a name="create-a-twitter-application"></a>Crear una aplicación de Twitter

1. Desde un explorador web, inicie sesión en [https://apps.twitter.com/](https://apps.twitter.com/). Haga clic en el vínculo **Regístrate ahora** si no tiene una cuenta de Twitter.

2. Haga clic en **Crear nueva aplicación**.

3. Especifique los campos **Name** (Nombre), **Description** (Descripción), **Website** (Sitio web). Puede conformar una dirección URL para el campo **Website** (Sitio web). La siguiente tabla muestra algunos valores de ejemplo para utilizar:

   | Campo | Valor |
   |:--- |:--- |
   | NOMBRE |MyHDInsightApp |
   | DESCRIPCIÓN |MyHDInsightApp |
   | Website |http://www.myhdinsightapp.com |

4. Active **Yes, I agree** (Acepto) y, a continuación, haga clic en **Create your Twitter application** (Crear la aplicación de Twitter).

5. Haga clic en la pestaña **Permissions** (Permisos). El permiso predeterminado es **Read only**(Solo lectura).

6. Haga clic en la pestaña **Keys and Access Tokens** (Claves y tokens de acceso).

7. Haga clic en **Create my access token**(Crear mi token de acceso).

8. Haga clic en **Prueba de OAuth** en la esquina superior derecha de la página.

9. Rellene los campos **consumer key** (clave del consumidor), **Consumer secret** (Secreto del consumidor), **Access token** (Token de acceso) y **Access token secret** (Secreto del token de acceso).

### <a name="download-tweets"></a>Descarga de tweets

El siguiente código Python descarga 10 000 tweets de Twitter y los guarda en un archivo denominado **tweets.txt**.

> [!NOTE]
> Los siguientes pasos se realizan en el clúster de HDInsight, puesto que Python ya está instalada.

1. Conéctese al clúster de HDInsight con SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Utilice los comandos siguientes para instalar [Tweepy](http://www.tweepy.org/), [Progressbar](https://pypi.python.org/pypi/progressbar/2.2) y otros paquetes requeridos:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

4. Use el comando siguiente para crear un archivo denominado **gettweets.py**:

   ```bash
   nano gettweets.py
   ```

5. Use el texto siguiente como contenido del archivo **gettweets.py**:

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=10000

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!IMPORTANT]
    > Con la información de la aplicación de twitter, reemplace el texto del marcador de posición para los elementos siguientes:
    >
    > * `consumer_secret`
    > * `consumer_key`
    > * `access_token`
    > * `access_token_secret`

    > [!TIP]
    > Ajuste el filtro de temas en la última línea para realizar un seguimiento de las palabras clave populares. El uso de palabras clave populares en el momento de ejecutar el script permite la captura más rápida de los datos.

6. Use **Ctrl+X** y luego **Y** para guardar el archivo.

7. Use el siguiente comando para ejecutar el archivo y descargar tweets:

    ```bash
    python gettweets.py
    ```

    Aparece un indicador de progreso. Cuenta hasta el 100% a medida que se descargan los tweets.

   > [!NOTE]
   > Si la barra de progreso tarda mucho en avanzar, debería cambiar el filtro de seguimiento de las tendencias. Cuando hay muchos tweets sobre un tema en el filtro, puede obtener rápidamente los 10 000 tweets necesarios.

### <a name="upload-the-data"></a>Carga de los datos

Para cargar los datos de almacenamiento para HDInsight, use los comandos siguientes:

   ```bash
   hdfs dfs -mkdir -p /tutorials/twitter/data
   hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Estos comandos almacenan los datos en una ubicación a la que pueden tener acceso todos los nodos del clúster.

## <a name="run-the-hiveql-job"></a>Ejecución del trabajo de HiveQL

1. Use el siguiente comando para crear un archivo que contenga instrucciones de HiveQL:

   ```bash
   nano twitter.hql
   ```

    Use el texto siguiente como contenido del archivo:

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
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
       cast (get_json_object(json_response, '$.retweet_count') as INT),
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
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

2. Presione **Ctrl+X** y luego **Y** para guardar el archivo.
3. Use el siguiente comando para ejecutar el HiveQL incluido en el archivo:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Este comando ejecuta el archivo **twitter.hql**. Una vez que se completa la consulta, verá el aviso `jdbc:hive2//localhost:10001/>`.

4. Desde el símbolo del sistema de beeline, use la siguiente consulta para comprobar que se importaron datos:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Esta consulta devolverá un máximo de 10 tweets que contienen la palabra **Azure** en el texto del mensaje.

    > [!NOTE]
    > Si cambió el filtro en el script `gettweets.py`, reemplace **Azure** por uno de los filtros que usó.

## <a name="next-steps"></a>pasos siguientes

Ha aprendido cómo transformar un conjunto de datos JSON no estructurado en una tabla de Hive estructurada. Para obtener más información sobre Hive en HDInsight, consulte los siguientes documentos:

* [Introducción a HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Análisis de la información de retraso de vuelos con HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter
