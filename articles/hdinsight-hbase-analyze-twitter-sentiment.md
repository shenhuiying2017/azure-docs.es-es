<properties linkid="manage-services-hdinsight-howto-social-data" urlDisplayName="Analyze realt-time Twitter sentiment with Hbase in HDInsight" pageTitle="Analyze real-time Twitter sentiment with HBase in HDInsight | Azure" metaKeywords="" description="Learn how to do real-time analysis of big data using HBase in an HDInsight (Hadoop) cluster." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze real-time Twitter sentiment with HBase in HDInsight" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao"></tags>

# Realizar análisis de opinión en Twitter en tiempo real con HBase en HDInsight

Descubra cómo realizar [análisis de opinión][análisis de opinión] en tiempo real de grandes volúmenes de datos con HBase en un clúster de HDInsight (Hadoop).

Los sitios web de las redes sociales constituyen una de las principales fuerzas motrices para la adopción de grandes datos. Las API públicas proporcionadas por sitios como Twitter constituyen un origen de datos muy útil para analizar y comprender las tendencias populares. En este tutorial, aprenderá a desarrollar una aplicación de servicio de streaming de consola y una aplicación web ASP.NET para hacer lo siguiente:

![][]

-   Obtener tweets con geoetiqueta en tiempo real a través de la API de streaming de Twitter.
-   Evaluar la opinión de dichos tweets.
-   Almacenar la información de opinión en HBase con el SDK de HBase de Microsoft.
-   Trazar los resultados estadísticos en tiempo real en mapas de Bing con una aplicación web ASP.NET. La visualización de estos tweets resultará similar a lo siguiente:

    ![hdinsight.hbase.twitter.sentiment.bing.map][hdinsight.hbase.twitter.sentiment.bing.map]

    Puede consultar los tweets con ciertas palabras clave para hacerse una idea acerca de si las opiniones que se expresan son positivas, negativas o neutras.

Encontrará una solución completa de Visual Studio de ejemplo en [][]<https://github.com/maxluk/tweet-sentiment></a>.

## En este artículo

-   [Requisitos previos][Requisitos previos]
-   [Crear una aplicación de Twitter][Crear una aplicación de Twitter]
-   [Crear un servicio simple de streaming de Twitter][Crear un servicio simple de streaming de Twitter]
-   [Crear un sitio web de Azure para visualizar la opinión de Twitter][Crear un sitio web de Azure para visualizar la opinión de Twitter]
-   [Pasos siguientes][Pasos siguientes]

## <span id="prerequisites"></span></a>Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

-   **Un clúster de HBase en HDInsight**. Para obtener instrucciones sobre el aprovisionamiento de clústeres, consulte [Primeros pasos para HBase con Hadoop en HDInsight][Primeros pasos para HBase con Hadoop en HDInsight]. Para completar el tutorial, necesitará los datos siguientes:

    | Propiedad del clúster            | Descripción                                                                                                      |
    |----------------------------------|------------------------------------------------------------------------------------------------------------------|
    | Nombre de clúster de HBase       | El nombre del clúster de HBase de HDInsight. Por ejemplo: https://myhbase.azurehdinsight.net/                    |
    | Nombre de usuario de clúster     | El nombre de la cuenta de usuario de Hadoop. El nombre de usuario de Hadoop predeterminado es **administrador**. |
    | Contraseña de usuario de clúster | La contraseña de usuario del clúster de Hadoop.                                                                  |

-   **Una estación de trabajo** con Visual Studio 2013 instalado. Para obtener instrucciones, consulte [Instalar Visual Studio][Instalar Visual Studio].

## <span id="twitter"></span></a>Crear secretos y un identificador de aplicación de Twitter

Las API de streaming de Twitter autorizan las solicitudes con [OAuth][OAuth].

El primer paso es utilizar OAuth para crear una aplicación nueva en el sitio de desarrolladores de Twitter.

**Para crear secretos y un identificador de aplicación de Twitter:**

1.  Inicie sesión en [][1]<https://apps.twitter.com/></a>. Haga clic en el vínculo **Regístrese ahora** si no tiene una cuenta de Twitter.
2.  Haga clic en **Create New App**.
3.  Escriba **Nombre**, **Descripción**, **Sitio web**. El campo Sitio web no se usa en realidad. No es necesario escribir una URL válida. La siguiente tabla muestra algunos valores de ejemplo para utilizar:

    | Campo       | Valor                              |
    |-------------|------------------------------------|
    | Nombre      | MyHDInsightHBaseApp                |
    | Descripción | MyHDInsightHBaseApp                |
    | Sitio web   | http://www.myhdinsighthbaseapp.com |

4.  Haga clic en **Yes, I agree** y, a continuación, en **Create your Twitter application**.
5.  Haga clic en la pestaña **Permisos**. El permiso predeterminado es **solo lectura**. Esto es suficiente para este tutorial.
6.  Haga clic en la pestaña **Claves de API**.
7.  Haga clic en **Create my access token**.
8.  Haga clic en **Prueba de OAuth** en la esquina superior derecha de la página.
9.  Anote los valores de **Clave de API**, **Secreto de API**, **Token de acceso** y **Secreto de token de acceso**. Los necesitará más adelante en el tutorial.

    ![hdi.hbase.twitter.sentiment.twitter.app][hdi.hbase.twitter.sentiment.twitter.app]

## <span id="streaming"></span></a> Crear un servicio simple de streaming de Twitter

Cree una aplicación de consola para obtener tweets, calcule la puntuación de opinión de los tweets y envíe las palabras procesadas de estos a HBase.

**Para crear la solución de Visual Studio:**

1.  Abra **Visual Studio**.
2.  En el menú **Archivo**, apunte a **Nuevo** y haga clic en **Proyecto**.
3.  Escriba o seleccione los valores siguientes:

    -   Plantillas: **Visual C#**
    -   Plantilla: **Aplicación de consola**
    -   Nombre: **TweetSentimentStreaming**
    -   Location : **C:\\Tutorials**
    -   Nombre de la solución: **TweetSentimentStreaming**

4.  Haga clic en **Aceptar** para continuar.

**Para instalar paquetes de NuGet y agregar referencias de SDK:**

1.  En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y, a continuación, en **Consola del Administrador de paquetes**. El panel de la consola se abrirá en la parte inferior de la página.
2.  Con los comandos siguientes, instale los paquetes [Tweetinvi][Tweetinvi] (para acceder a la API de Twitter) y [Protobuf-net][Protobuf-net] (para serializar y deserializar objetos).

        Install-Package TweetinviAPI
        Install-Package protobuf-net 

    > [WACOM.NOTE] El paquete NuGet del SDK de Hbase de Microsoft no está disponible desde el 26 de agosto de 2014. El repositorio Github es [][2]<https://github.com/hdinsight/hbase-sdk-for-net></a>. Hasta que el SDK esté disponible, deberá compilar la dll por su cuenta. Para obtener instrucciones, consulte [Primeros pasos para HBase con Hadoop en HDInsight][Primeros pasos para HBase con Hadoop en HDInsight].

3.  En el **Explorador de soluciones**, haga clic con el botón secundario en **Referencias** y después haga clic en **Agregar referencia**.
4.  En el panel izquierdo, expanda **Ensamblados** y haga clic en **Marco**.
5.  En el panel derecho, active la casilla frente a **System.Configuration** y haga clic en **Aceptar**.

**Para definir la clase de servicio de streaming de Twitter:**

1.  En el **Explorador de soluciones**, haga clic con el botón secundario en **TweetSentimentStreaming**, elija **Agregar** y haga clic en **Clase**.
2.  En **Nombre**, escriba **HBaseWriter** y haga clic en **Agregar**.
3.  En **HBaseWriter.cs**, agregue lo siguiente mediante instrucciones en la parte superior del archivo:

        using System.IO;        
        using System.Threading;
        using System.Globalization;
        using Microsoft.HBase.Client;
        using Tweetinvi.Core.Interfaces;
        using org.apache.hadoop.hbase.rest.protobuf.generated;

4.  En **HbaseWriter.cs**, agregue una clase nueva denominada **DictionaryItem**:

        public class DictionaryItem
        {
            public string Type { get; set; }
            public int Length { get; set; }
            public string Word { get; set; }
            public string Pos { get; set; }
            public string Stemmed { get; set; }
            public string Polarity { get; set; }
        }

    Esta estructura de clase se usa para analizar el archivo de diccionario de opinión. Con los datos se calcula la puntuación de opinión de cada tweet.

5.  En la clase **HBaseWriter**, defina las variables y constantes siguientes:

        // HDinsight HBase cluster and HBase table information
        const string CLUSTERNAME = "https://<HBaseClusterName>.azurehdinsight.net/";
        const string HADOOPUSERNAME = "<HadoopUserName>"; //the default name is "admin"
        const string HADOOPUSERPASSWORD = "<HaddopUserPassword>";
        const string HBASETABLENAME = "tweets_by_words";

        // Sentiment dictionary file and the punctuation characters
        const string DICTIONARYFILENAME = @"..\..\data\dictionary\dictionary.tsv";
        private static char[] _punctuationChars = new[] { 
            ' ', '!', '\"', '#', '$', '%', '&', '\'', '(', ')', '*', '+', ',', '-', '.', '/',   //ascii 23--47
            ':', ';', '<', '=', '>', '?', '@', '[', ']', '^', '_', '`', '{', '|', '}', '~' };   //ascii 58--64 + misc.

        // For writting to HBase
        HBaseClient client;

        // a sentiment dictionary for estimate sentiment. It is loaded from a physical file.
        Dictionary<string, DictionaryItem> dictionary;

        // use multithread write
        Thread writerThread;
        Queue<ITweet> queue = new Queue<ITweet>();
        bool threadRunning = true;

6.  Establezca los valores de constante, incluidos **\<HBaseClusterName\>**, **\<HadoopUserName\>** y **\<HaddopUserPassword\>**. Si desea cambiar el nombre de tabla de HBase, deberá cambiarlo también en la aplicación web.

    Más adelante en este tutorial, descargará y moverá el archivo dictionary.tsv a una carpeta específica.

7.  Defina las funciones siguientes en la clase **HBaseWriter**:

        // This function connects to HBase, loads the sentiment dictionary, and starts the thread for writting.
        public HBaseWriter()
        {
            ClusterCredentials credentials = new ClusterCredentials(new Uri(CLUSTERNAME), HADOOPUSERNAME, HADOOPUSERPASSWORD);
            client = new HBaseClient(credentials);

            // create the HBase table if it doesn't exist
            if (!client.ListTables().name.Contains(HBASETABLENAME))
            {
                TableSchema tableSchema = new TableSchema();
                tableSchema.name = HBASETABLENAME;
                tableSchema.columns.Add(new ColumnSchema { name = "d" });
                client.CreateTable(tableSchema);
                Console.WriteLine("Table \"{0}\" is created.", HBASETABLENAME);
            }

            // Load sentiment dictionary from a file
            LoadDictionary();

            // Start a thread for writting to HBase
            writerThread = new Thread(new ThreadStart(WriterThreadFunction));
            writerThread.Start();
        }

        ~HBaseWriter()
        {
            threadRunning = false;
        }

        // Enqueue the Tweets received
        public void WriteTweet(ITweet tweet)
        {
            lock (queue)
            {
                queue.Enqueue(tweet);
            }
        }

        // Load sentiment dictionary from a file
        private void LoadDictionary()
        {
            List<string> lines = File.ReadAllLines(DICTIONARYFILENAME).ToList();
            var items = lines.Select(line =>
            {
                var fields = line.Split('\t');
                var pos = 0;
                return new DictionaryItem
                {
                    Type = fields[pos++],
                    Length = Convert.ToInt32(fields[pos++]),
                    Word = fields[pos++],
                    Pos = fields[pos++],
                    Stemmed = fields[pos++],
                    Polarity = fields[pos++]
                };
            });

            dictionary = new Dictionary<string, DictionaryItem>();
            foreach (var item in items)
            {
                if (!dictionary.Keys.Contains(item.Word))
                {
                    dictionary.Add(item.Word, item);
                }
            }
        }

        // Calculate sentiment score
        private int CalcSentimentScore(string[] words)
        {
            Int32 total = 0;
            foreach (string word in words)
            {
                if (dictionary.Keys.Contains(word))
                {
                    switch (dictionary[word].Polarity)
                    {
                        case "negative": total -= 1; break;
                        case "positive": total += 1; break;
                    }
                }
            }
            if (total > 0)
            {
                return 1;
            }
            else if (total < 0)
            {
                return -1;
            }
            else
            {
                return 0;
            }
        }

        // Popular a CellSet object to be written into HBase
        private void CreateTweetByWordsCells(CellSet set, ITweet tweet)
        {
            // Split the Tweet into words
            string[] words = tweet.Text.ToLower().Split(_punctuationChars);

            // Calculate sentiment score base on the words
            int sentimentScore = CalcSentimentScore(words);
            var word_pairs = words.Take(words.Length - 1)
                                  .Select((word, idx) => string.Format("{0} {1}", word, words[idx + 1]));
            var all_words = words.Concat(word_pairs).ToList();

            // For each word in the Tweet add a row to the HBase table
            foreach (string word in all_words)
            {
                string time_index = (ulong.MaxValue - (ulong)tweet.CreatedAt.ToBinary()).ToString().PadLeft(20) + tweet.IdStr;
                string key = word + "_" + time_index;

                // Create a row
                var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };

                // Add columns to the row, including Tweet identifier, language, coordinator(if available), and sentiment 
                var value = new Cell { column = Encoding.UTF8.GetBytes("d:id_str"), data = Encoding.UTF8.GetBytes(tweet.IdStr) };
                row.values.Add(value);

                value = new Cell { column = Encoding.UTF8.GetBytes("d:lang"), data = Encoding.UTF8.GetBytes(tweet.Language.ToString()) };
                row.values.Add(value);

                if (tweet.Coordinates != null)
                {
                    var str = tweet.Coordinates.Longitude.ToString() + "," + tweet.Coordinates.Latitude.ToString();
                    value = new Cell { column = Encoding.UTF8.GetBytes("d:coor"), data = Encoding.UTF8.GetBytes(str) };
                    row.values.Add(value);
                }

                value = new Cell { column = Encoding.UTF8.GetBytes("d:sentiment"), data = Encoding.UTF8.GetBytes(sentimentScore.ToString()) };
                row.values.Add(value);

                set.rows.Add(row);
            }
        }

        // Write a Tweet (CellSet) to HBase
        public void WriterThreadFunction()
        {
            try
            {
                while (threadRunning)
                {
                    if (queue.Count > 0)
                    {
                        CellSet set = new CellSet();
                        lock (queue)
                        {
                            do
                            {
                                ITweet tweet = queue.Dequeue();
                                CreateTweetByWordsCells(set, tweet);
                            } while (queue.Count > 0);
                        }

                        // Write the Tweet by words cell set to the HBase table
                        client.StoreCells(HBASETABLENAME, set);
                        Console.WriteLine("\tRows written: {0}", set.rows.Count);
                    }
                    Thread.Sleep(100);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Exception: " + ex.Message);
            }
        }

    El código proporciona las siguientes funciones:

    -   **Conexión con Hbase [ HBaseWriter() ]**: con el SDK de HBase, cree un objeto *ClusterCredentials* (con la URL del clúster y las credenciales de usuario de Hadoop) y un objeto *HBaseClient* (con el objeto ClusterCredentials).
    -   **Creación de la tabla de Hbase [ HBaseWriter() ]**: la llamada de método es *HBaseClient.CreateTable()*.
    -   **Escritura en la tabla de HBase [ WriterThreadFunction() ]**: la llamada de método es *HBaseClient.StoreCells()*.

**Para completar Program.cs:**

1.  En el **Explorador de soluciones**, haga doble clic en **Program.cs** para abrirlo.
2.  Al principio del archivo, agregue las instrucciones using siguientes:

        using System.Configuration;
        using System.Diagnostics;
        using Tweetinvi;

3.  En la clase **Program**, defina las constantes siguientes:

        const string TWITTERAPPACCESSTOKEN = "<TwitterApplicationAccessToken";
        const string TWITTERAPPACCESSTOKENSECRET = "TwitterApplicationAccessTokenSecret";
        const string TWITTERAPPAPIKEY = "TwitterApplicationAPIKey";
        const string TWITTERAPPAPISECRET = "TwitterApplicationAPISecret";

4.  Establezca los valores de constante para que coincidan con los valores de aplicación de Twitter.

5.  Modifique la función **Main()** para que quede así:

        static void Main(string[] args)
        {
            TwitterCredentials.SetCredentials(TWITTERAPPACCESSTOKEN, TWITTERAPPACCESSTOKENSECRET, TWITTERAPPAPIKEY, TWITTERAPPAPISECRET);

            Stream_FilteredStreamExample();
        }

6.  Agregue la función siguiente a la clase:

        private static void Stream_FilteredStreamExample()
        {
            for (; ; )
            {
                try
                {
                    HBaseWriter hbase = new HBaseWriter();
                    var stream = Stream.CreateFilteredStream();
                    stream.AddLocation(Geo.GenerateLocation(-180, -90, 180, 90));

                    var tweetCount = 0;
                    var timer = Stopwatch.StartNew();

                    stream.MatchingTweetReceived += (sender, args) =>
                    {
                        tweetCount++;
                        var tweet = args.Tweet;

                        // Write Tweets to HBase
                        hbase.WriteTweet(tweet);

                        if (timer.ElapsedMilliseconds > 1000)
                        {
                            if (tweet.Coordinates != null)
                            {
                                Console.ForegroundColor = ConsoleColor.Green;
                                Console.WriteLine("\n{0}: {1} {2}", tweet.Id, tweet.Language.ToString(), tweet.Text);
                                Console.ForegroundColor = ConsoleColor.White;
                                Console.WriteLine("\tLocation: {0}, {1}", tweet.Coordinates.Longitude, tweet.Coordinates.Latitude);
                            }

                            timer.Restart();
                            Console.WriteLine("\tTweets/sec: {0}", tweetCount);
                            tweetCount = 0;
                        }
                    };

                    stream.StartStreamMatchingAllConditions();
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Exception: {0}", ex.Message);
                }
            }
        }

**Para descargar el archivo de diccionario de opinión:**

1.  Vaya a [https://github.com/maxluk/tweet-sentiment](https://github.com/maxluk/tweet-sentiment).
2.  Haga clic en **Descargar ZIP**.
3.  Extraiga el archivo en una ubicación local.
4.  Copie el archivo de **../tweet-sentiment/SimpleStreamingService/data/dictionary/dictionary.tsv**.
5.  Pegue el archivo en la solución, debajo de **TweetSentimentStreaming/TweetSentimentStreaming/data/dictionary/dictionary.tsv**.

**Para ejecutar el servicio de streaming:**

1.  En Visual Studio, presione **F5**. Esta es una captura de pantalla de la aplicación de consola:

    ![hdinsight.hbase.twitter.sentiment.streaming.service][hdinsight.hbase.twitter.sentiment.streaming.service]

2.  Mientras desarrolla la aplicación web, mantenga en ejecución la aplicación de consola de streaming para disponer de más datos.

## <span id="web"></span></a> Crear un sitio web de Azure para visualizar la opinión de Twitter

En esta sección, creará una aplicación web ASP.NET MVC para leer los datos de opinión en tiempo real desde HBase y trazarlos en mapas de Bing.

**Para crear una aplicación web ASP.NET MVC:**

1.  Abra Visual Studio.
2.  Haga clic en **Archivo**, en **Nuevo** y en **Proyecto**.
3.  Escriba los valores siguientes:

    -   Categoría de plantilla: **Visual C#/Web**
    -   Plantilla: **Aplicación web ASP.NET**
    -   Nombre: **TweetSentimentWeb**
    -   Location : **C:\\Tutorials**
4.  Haga clic en **OK**.
5.  En **Seleccione una plantilla**, haga clic en **MVC**.
6.  En **Windows Azure**, haga clic en **Administrar suscripciones**.
7.  En **Administrar suscripciones de Windows Azure**, haga clic en **Iniciar sesión**.
8.  Escriba sus credenciales de Azure. En la pestaña Cuentas, aparece la información de suscripción de Azure.
9.  Haga clic en **Cerrar** para cerrar la ventana Administrar suscripciones de Windows Azure.
10. En **Nuevo proyecto ASP.NET - TweetSentimentWeb**, haga clic en **Aceptar**.
11. En **Parámetros de configuración del sitio de Windows Azure**, seleccione la **región** más cercana a usted. No es necesario especificar un servidor de bases de datos.
12. Haga clic en **OK**.

**Para instalar los paquetes NuGet:**

1.  En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y, a continuación, en **Consola del Administrador de paquetes**. El panel de la consola se abre en la parte inferior de la página.
2.  Con los comandos siguientes instale el paquete [Protobuf-net][Protobuf-net], que se usa para serializar y deserializar objetos.

        Install-Package protobuf-net 

    > [WACOM.NOTE] El paquete NuGet del SDK de Hbase de Microsoft no está disponible desde el 20 de agosto de 2014. El repositorio Github es [][2]<https://github.com/hdinsight/hbase-sdk-for-net></a>. Hasta que el SDK esté disponible, deberá compilar la dll por su cuenta. Para obtener instrucciones, consulte [Primeros pasos para HBase con Hadoop en HDInsight][Primeros pasos para HBase con Hadoop en HDInsight].

**Para agregar la clase HBaseReader:**

1.  En el **Explorador de soluciones**, expanda **TweetSentiment**.
2.  Haga clic con el botón secundario en **Modelos** y con el botón primario en **Agregar** y **Clase**.
3.  En Nombre, escriba **HBaseReader.cs** y haga clic en **Agregar**.
4.  Reemplace el código por lo siguiente:

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;

        using System.Configuration;
        using System.Threading.Tasks;
        using System.Text;
        using Microsoft.HBase.Client;
        using org.apache.hadoop.hbase.rest.protobuf.generated;

        namespace TweetSentimentWeb.Models
        {
            public class HBaseReader
            {
                // For reading Tweet sentiment data from HDInsight HBase
                HBaseClient client;

                // HDinsight HBase cluster and HBase table information
                const string CLUSTERNAME = "<HBaseClusterName>";
                const string HADOOPUSERNAME = "<HBaseClusterHadoopUserName>"
                const string HADOOPUSERPASSWORD = "<HBaseCluserUserPassword>";
                const string HBASETABLENAME = "tweets_by_words";

                // The constructor
                public HBaseReader()
                {
                    ClusterCredentials creds = new ClusterCredentials(
                                    new Uri(CLUSTERNAME),
                                    HADOOPUSERNAME,
                                    HADOOPUSERPASSWORD);
                    client = new HBaseClient(creds);
                }

                // Query Tweets sentiment data from the HBase table asynchronously 
                public async Task<IEnumerable<Tweet>> QueryTweetsByKeywordAsync(string keyword)
                {
                    List<Tweet> list = new List<Tweet>();

                    // Demonstrate Filtering the data from the past 6 hours the row key
                    string timeIndex = (ulong.MaxValue -
                        (ulong)DateTime.UtcNow.Subtract(new TimeSpan(6, 0, 0)).ToBinary()).ToString().PadLeft(20);
                    string startRow = keyword + "_" + timeIndex;
                    string endRow = keyword + "|";
                    Scanner scanSettings = new Scanner
                    {
                        batch = 100000,
                        startRow = Encoding.UTF8.GetBytes(startRow),
                        endRow = Encoding.UTF8.GetBytes(endRow)
                    };

                    // Make async scan call
                    ScannerInformation scannerInfo =
                        await client.CreateScannerAsync(HBASETABLENAME, scanSettings);

                    CellSet next;

                    while ((next = await client.ScannerGetNextAsync(scannerInfo)) != null)
                    {
                        foreach (CellSet.Row row in next.rows)
                        {
                            // find the cell with string pattern "d:coor" 
                            var coordinates =
                                row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:coor");

                            if (coordinates != null)
                            {
                                string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');

                                var sentimentField =
                                    row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:sentiment");
                                Int32 sentiment = 0;
                                if (sentimentField != null)
                                {
                                    sentiment = Convert.ToInt32(Encoding.UTF8.GetString(sentimentField.data));
                                }

                                list.Add(new Tweet
                                {
                                    Longtitude = Convert.ToDouble(lonlat[0]),
                                    Latitude = Convert.ToDouble(lonlat[1]),
                                    Sentiment = sentiment
                                });
                            }

                            if (coordinates != null)
                            {
                                string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
                            }
                        }
                    }

                    return list;
                }
            }

            public class Tweet
            {
                public string IdStr { get; set; }
                public string Text { get; set; }
                public string Lang { get; set; }
                public double Longtitude { get; set; }
                public double Latitude { get; set; }
                public int Sentiment { get; set; }
            }
        }

5.  En la clase **HBaseReader**, cambie los valores de constante:

    -   **CLUSTERNAME**: el nombre del clúster de HBase. Por ejemplo, *<https://.azurehdinsight.net/>*.
    -   **HADOOPUSERNAME**: el nombre de usuario de Hadoop del clúster de HBase. El nombre predeterminado es *admin*.
    -   **HADOOPUSERPASSWORD**: la contraseña de usuario de Hadoop del clúster de HBase.
    -   **HBASETABLENAME** = "tweets\_by\_words";

    El nombre de tabla de HBase es "tweets\_by\_words". Para que la aplicación web pueda leer los datos de la misma tabla de HBase, los valores deben coincidir con los que se enviaron en el servicio de streaming.

**Para agregar el controlador TweetsController:**

1.  En el **Explorador de soluciones**, expanda **TweetSentimentWeb**.
2.  Haga clic con el botón secundario en **Controladores** y con el botón primario en **Agregar** y **Controlador**.
3.  Haga clic en **Controlador Web API 2 - Vacío** y en **Agregar**.
4.  En Nombre del controlador, escriba **TweetsController** y haga clic en **Agregar**.
5.  En el **Explorador de soluciones**, haga doble clic en TweetsController.cs para abrir el archivo.
6.  Modifique el archivo para que quede así:

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Net;
        using System.Net.Http;
        using System.Web.Http;

        using System.Threading.Tasks;
        using TweetSentimentWeb.Models;

        namespace TweetSentimentWeb.Controllers
        {
            public class TweetsController : ApiController
            {
                HBaseReader hbase = new HBaseReader();

                public async Task<IEnumerable<Tweet>> GetTweetsByQuery(string query)
                {
                    return await hbase.QueryTweetsByKeywordAsync(query);
                }
            }
        }

**Para agregar heatmap.js:**

1.  En el **Explorador de soluciones**, expanda **TweetSentimentWeb**.
2.  Haga clic con el botón secundario en **Scripts** y con el botón primario en **Agregar** y **Archivo JavaScript**.
3.  En Nombre del elemento, escriba **heatmap.js**.
4.  Copie y pegue el código siguiente en el archivo. El código lo escribió Alastair Aitchison. Para obtener más información, consulte [][3]<http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/></a>.

        /*******************************************************************************
        * Author: Alastair Aitchison
        * Website: http://alastaira.wordpress.com
        * Date: 15th April 2011
        * 
        * Description: 
        * This JavaScript file provides an algorithm that can be used to add a heatmap
        * overlay on a Bing Maps v7 control. The intensity and temperature palette
        * of the heatmap are designed to be easily customisable.
        *
        * Requirements:
        * The heatmap layer itself is created dynamically on the client-side using
        * the HTML5 <canvas> element, and therefore requires a browser that supports
        * this element. It has been tested on IE9, Firefox 3.6/4 and 
        * Chrome 10 browsers. If you can confirm whether it works on other browsers or
        * not, I'd love to hear from you!

        * Usage:
        * The HeatMapLayer constructor requires:
        * - A reference to a map object
        * - An array or Microsoft.Maps.Location items
        * - Optional parameters to customise the appearance of the layer
        *  (Radius,, Unit, Intensity, and ColourGradient), and a callback function
        *
        */

        var HeatMapLayer = function (map, locations, options) {

            /* Private Properties */
            var _map = map,
              _canvas,
              _temperaturemap,
              _locations = [],
              _viewchangestarthandler,
              _viewchangeendhandler;

            // Set default options
            var _options = {
                // Opacity at the centre of each heat point
                intensity: 0.5,

                // Affected radius of each heat point
                radius: 1000,

                // Whether the radius is an absolute pixel value or meters
                unit: 'meters',

                // Colour temperature gradient of the map
                colourgradient: {
                    "0.00": 'rgba(255,0,255,20)',  // Magenta
                    "0.25": 'rgba(0,0,255,40)',    // Blue
                    "0.50": 'rgba(0,255,0,80)',    // Green
                    "0.75": 'rgba(255,255,0,120)', // Yellow
                    "1.00": 'rgba(255,0,0,150)'    // Red
                },

                // Callback function to be fired after heatmap layer has been redrawn 
                callback: null
            };

            /* Private Methods */
            function _init() {
                var _mapDiv = _map.getRootElement();

                if (_mapDiv.childNodes.length >= 3 && _mapDiv.childNodes[2].childNodes.length >= 2) {
                    // Create the canvas element
                    _canvas = document.createElement('canvas');
                    _canvas.style.position = 'relative';

                    var container = document.createElement('div');
                    container.style.position = 'absolute';
                    container.style.left = '0px';
                    container.style.top = '0px';
                    container.appendChild(_canvas);

                    _mapDiv.childNodes[2].childNodes[1].appendChild(container);

                    // Override defaults with any options passed in the constructor
                    _setOptions(options);

                    // Load array of location data
                    _setPoints(locations);

                    // Create a colour gradient from the suppied colourstops
                    _temperaturemap = _createColourGradient(_options.colourgradient);

                    // Wire up the event handler to redraw heatmap canvas
                    _viewchangestarthandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangestart', _clearHeatMap);
                    _viewchangeendhandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangeend', _createHeatMap);

                    _createHeatMap();

                    delete _init;
                } else {
                    setTimeout(_init, 100);
                }
            }

            // Resets the heat map
            function _clearHeatMap() {
                var ctx = _canvas.getContext("2d");
                ctx.clearRect(0, 0, _canvas.width, _canvas.height);
            }

            // Creates a colour gradient from supplied colour stops on initialisation
            function _createColourGradient(colourstops) {
                var ctx = document.createElement('canvas').getContext('2d');
                var grd = ctx.createLinearGradient(0, 0, 256, 0);
                for (var c in colourstops) {
                    grd.addColorStop(c, colourstops[c]);
                }
                ctx.fillStyle = grd;
                ctx.fillRect(0, 0, 256, 1);
                return ctx.getImageData(0, 0, 256, 1).data;
            }

            // Applies a colour gradient to the intensity map
            function _colouriseHeatMap() {
                var ctx = _canvas.getContext("2d");
                var dat = ctx.getImageData(0, 0, _canvas.width, _canvas.height);
                var pix = dat.data; // pix is a CanvasPixelArray containing height x width x 4 bytes of data (RGBA)
                for (var p = 0, len = pix.length; p < len;) {
                    var a = pix[p + 3] * 4; // get the alpha of this pixel
                    if (a != 0) { // If there is any data to plot
                        pix[p] = _temperaturemap[a]; // set the red value of the gradient that corresponds to this alpha
                        pix[p + 1] = _temperaturemap[a + 1]; //set the green value based on alpha
                        pix[p + 2] = _temperaturemap[a + 2]; //set the blue value based on alpha
                    }
                    p += 4; // Move on to the next pixel
                }
                ctx.putImageData(dat, 0, 0);
            }

            // Sets any options passed in
            function _setOptions(options) {
                for (attrname in options) {
                    _options[attrname] = options[attrname];
                }
            }

            // Sets the heatmap points from an array of Microsoft.Maps.Locations  
            function _setPoints(locations) {
                _locations = locations;
            }

            // Main method to draw the heatmap
            function _createHeatMap() {
                // Ensure the canvas matches the current dimensions of the map
                // This also has the effect of resetting the canvas
                _canvas.height = _map.getHeight();
                _canvas.width = _map.getWidth();

                _canvas.style.top = -_canvas.height / 2 + 'px';
                _canvas.style.left = -_canvas.width / 2 + 'px';

                // Calculate the pixel radius of each heatpoint at the current map zoom
                if (_options.unit == "pixels") {
                    radiusInPixel = _options.radius;
                } else {
                    radiusInPixel = _options.radius / _map.getMetersPerPixel();
                }

                var ctx = _canvas.getContext("2d");

                // Convert lat/long to pixel location
                var pixlocs = _map.tryLocationToPixel(_locations, Microsoft.Maps.PixelReference.control);
                var shadow = 'rgba(0, 0, 0, ' + _options.intensity + ')';
                var mapWidth = 256 * Math.pow(2, _map.getZoom());

                // Create the Intensity Map by looping through each location
                for (var i = 0, len = pixlocs.length; i < len; i++) {
                    var x = pixlocs[i].x;
                    var y = pixlocs[i].y;

                    if (x < 0) {
                        x += mapWidth * Math.ceil(Math.abs(x / mapWidth));
                    }

                    // Create radial gradient centred on this point
                    var grd = ctx.createRadialGradient(x, y, 0, x, y, radiusInPixel);
                    grd.addColorStop(0.0, shadow);
                    grd.addColorStop(1.0, 'transparent');

                    // Draw the heatpoint onto the canvas
                    ctx.fillStyle = grd;
                    ctx.fillRect(x - radiusInPixel, y - radiusInPixel, 2 * radiusInPixel, 2 * radiusInPixel);
                }

                // Apply the specified colour gradient to the intensity map
                _colouriseHeatMap();

                // Call the callback function, if specified
                if (_options.callback) {
                    _options.callback();
                }
            }

            /* Public Methods */

            this.Show = function () {
                if (_canvas) {
                    _canvas.style.display = '';
                }
            };

            this.Hide = function () {
                if (_canvas) {
                    _canvas.style.display = 'none';
                }
            };

            // Sets options for intensity, radius, colourgradient etc.
            this.SetOptions = function (options) {
                _setOptions(options);
            }

            // Sets an array of Microsoft.Maps.Locations from which the heatmap is created
            this.SetPoints = function (locations) {
                // Reset the existing heatmap layer
                _clearHeatMap();
                // Pass in the new set of locations
                _setPoints(locations);
                // Recreate the layer
                _createHeatMap();
            }

            // Removes the heatmap layer from the DOM
            this.Remove = function () {
                _canvas.parentNode.parentNode.removeChild(_canvas.parentNode);

                if (_viewchangestarthandler) { Microsoft.Maps.Events.removeHandler(_viewchangestarthandler); }
                if (_viewchangeendhandler) { Microsoft.Maps.Events.removeHandler(_viewchangeendhandler); }

                _locations = null;
                _temperaturemap = null;
                _canvas = null;
                _options = null;
                _viewchangestarthandler = null;
                _viewchangeendhandler = null;
            }

            // Call the initialisation routine
            _init();
        };

        // Call the Module Loaded method
        Microsoft.Maps.moduleLoaded('HeatMapModule');

**Para agregar tweetStream.js:**

1.  En el **Explorador de soluciones**, expanda **TweetSentimentWeb**.
2.  Haga clic con el botón secundario en **Scripts** y con el botón primario en **Agregar** y **Archivo JavaScript**.
3.  En Nombre del elemento, escriba **twitterStream.js**.
4.  Copie y pegue el código siguiente en el archivo:

        var liveTweetsPos = [];
        var liveTweets = [];
        var liveTweetsNeg = [];
        var map;
        var heatmap;
        var heatmapNeg;
        var heatmapPos;

        function initialize() {
            // Initialize the map
            var options = {
                credentials: "AvFJTZPZv8l3gF8VC3Y7BPBd0r7LKo8dqKG02EAlqg9WAi0M7la6zSIT-HwkMQbx",
                center: new Microsoft.Maps.Location(23.0, 8.0),
                mapTypeId: Microsoft.Maps.MapTypeId.ordnanceSurvey,
                labelOverlay: Microsoft.Maps.LabelOverlay.hidden,
                zoom: 2.5
            };
            var map = new Microsoft.Maps.Map(document.getElementById('map_canvas'), options);

            // Heatmap options for positive, neutral and negative layers

            var heatmapOptions = {
                // Opacity at the centre of each heat point
                intensity: 0.5,

                // Affected radius of each heat point
                radius: 15,

                // Whether the radius is an absolute pixel value or meters
                unit: 'pixels'
            };

            var heatmapPosOptions = {
                // Opacity at the centre of each heat point
                intensity: 0.5,

                // Affected radius of each heat point
                radius: 15,

                // Whether the radius is an absolute pixel value or meters
                unit: 'pixels',

                colourgradient: {
                    0.0: 'rgba(0, 255, 255, 0)',
                    0.1: 'rgba(0, 255, 255, 1)',
                    0.2: 'rgba(0, 255, 191, 1)',
                    0.3: 'rgba(0, 255, 127, 1)',
                    0.4: 'rgba(0, 255, 63, 1)',
                    0.5: 'rgba(0, 127, 0, 1)',
                    0.7: 'rgba(0, 159, 0, 1)',
                    0.8: 'rgba(0, 191, 0, 1)',
                    0.9: 'rgba(0, 223, 0, 1)',
                    1.0: 'rgba(0, 255, 0, 1)'
                }
            };

            var heatmapNegOptions = {
                // Opacity at the centre of each heat point
                intensity: 0.5,

                // Affected radius of each heat point
                radius: 15,

                // Whether the radius is an absolute pixel value or meters
                unit: 'pixels',

                colourgradient: {
                    0.0: 'rgba(0, 255, 255, 0)',
                    0.1: 'rgba(0, 255, 255, 1)',
                    0.2: 'rgba(0, 191, 255, 1)',
                    0.3: 'rgba(0, 127, 255, 1)',
                    0.4: 'rgba(0, 63, 255, 1)',
                    0.5: 'rgba(0, 0, 127, 1)',
                    0.7: 'rgba(0, 0, 159, 1)',
                    0.8: 'rgba(0, 0, 191, 1)',
                    0.9: 'rgba(0, 0, 223, 1)',
                    1.0: 'rgba(0, 0, 255, 1)'
                }
            };

            // Register and load the Client Side HeatMap Module
            Microsoft.Maps.registerModule("HeatMapModule", "scripts/heatmap.js");
            Microsoft.Maps.loadModule("HeatMapModule", {
                callback: function () {
                    // Create heatmap layers for positive, neutral and negative tweets
                    heatmapPos = new HeatMapLayer(map, liveTweetsPos, heatmapPosOptions);
                    heatmap = new HeatMapLayer(map, liveTweets, heatmapOptions);
                    heatmapNeg = new HeatMapLayer(map, liveTweetsNeg, heatmapNegOptions);
                }
            });

            $("#searchbox").val("xbox");
            $("#searchBtn").click(onsearch);
            $("#positiveBtn").click(onPositiveBtn);
            $("#negativeBtn").click(onNegativeBtn);
            $("#neutralBtn").click(onNeutralBtn);
            $("#neutralBtn").button("toggle");
        }

        function onsearch() {
            var uri = 'api/tweets?query=';
            var query = $('#searchbox').val();
            $.getJSON(uri + query)
                .done(function (data) {
                    liveTweetsPos = [];
                    liveTweets = [];
                    liveTweetsNeg = [];

                    // On success, 'data' contains a list of tweets.
                    $.each(data, function (key, item) {
                        addTweet(item);
                    });

                    if (!$("#neutralBtn").hasClass('active')) {
                        $("#neutralBtn").button("toggle");
                    }
                    onNeutralBtn();
                })
                .fail(function (jqXHR, textStatus, err) {
                    $('#statustext').text('Error: ' + err);
                });
        }

        function addTweet(item) {
            //Add tweet to the heat map arrays.
            var tweetLocation = new Microsoft.Maps.Location(item.Latitude, item.Longtitude);
            if (item.Sentiment > 0) {
                liveTweetsPos.push(tweetLocation);
            } else if (item.Sentiment < 0) {
                liveTweetsNeg.push(tweetLocation);
            } else {
                liveTweets.push(tweetLocation);
            }
        }

        function onPositiveBtn() {
            if ($("#neutralBtn").hasClass('active')) {
                $("#neutralBtn").button("toggle");
            }
            if ($("#negativeBtn").hasClass('active')) {
                $("#negativeBtn").button("toggle");
            }

            heatmapPos.SetPoints(liveTweetsPos);
            heatmapPos.Show();
            heatmapNeg.Hide();
            heatmap.Hide();

            $('#statustext').text('Tweets: ' + liveTweetsPos.length + "   " + getPosNegRatio());
        }

        function onNeutralBtn() {
            if ($("#positiveBtn").hasClass('active')) {
                $("#positiveBtn").button("toggle");
            }
            if ($("#negativeBtn").hasClass('active')) {
                $("#negativeBtn").button("toggle");
            }

            heatmap.SetPoints(liveTweets);
            heatmap.Show();
            heatmapNeg.Hide();
            heatmapPos.Hide();

            $('#statustext').text('Tweets: ' + liveTweets.length + "   " + getPosNegRatio());
        }

        function onNegativeBtn() {
            if ($("#positiveBtn").hasClass('active')) {
                $("#positiveBtn").button("toggle");
            }
            if ($("#neutralBtn").hasClass('active')) {
                $("#neutralBtn").button("toggle");
            }

            heatmapNeg.SetPoints(liveTweetsNeg);
            heatmapNeg.Show();
            heatmap.Hide();;
            heatmapPos.Hide();;

            $('#statustext').text('Tweets: ' + liveTweetsNeg.length + "\t" + getPosNegRatio());
        }

        function getPosNegRatio() {
            if (liveTweetsNeg.length == 0) {
                return "";
            }
            else {
                var ratio = liveTweetsPos.length / liveTweetsNeg.length;
                var str = parseFloat(Math.round(ratio * 10) / 10).toFixed(1);
                return "Positive/Negative Ratio: " + str;
            }
        }

**Para modificar layout.cshtml:**

1.  En el **Explorador de soluciones**, expanda **TweetSentimentWeb**, **Views** y **Shared**, y haga doble clic en \_**Layout.cshtml**.
2.  Reemplace el contenido por lo siguiente:

        <!DOCTYPE html>
        <html>
        <head>
            <meta charset="utf-8" />
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>@ViewBag.Title</title>
            @Styles.Render("~/Content/css")
            @Scripts.Render("~/bundles/modernizr")
            <!-- Bing Maps -->
            <script type="text/javascript" src="http://ecn.dev.virtualearth.net/mapcontrol/mapcontrol.ashx?v=7.0&mkt=en-gb"></script>
            <!-- Spatial Dashboard JavaScript -->
            <script src="~/Scripts/twitterStream.js" type="text/javascript"></script>
        </head>
        <body onload="initialize()">
            <div class="navbar navbar-inverse navbar-fixed-top">
                <div class="container">
                    <div class="navbar-header">
                        <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                            <span class="icon-bar"></span>
                            <span class="icon-bar"></span>
                            <span class="icon-bar"></span>
                        </button>
                    </div>
                    <div class="navbar-collapse collapse">
                        <div class="row">
                            <ul class="nav navbar-nav col-lg-5">
                                <li class="col-lg-12">
                                    <div class="navbar-form">
                                        <input id="searchbox" type="search" class="form-control">
                                        <button type="button" id="searchBtn" class="btn btn-primary">Go</button>
                                    </div>
                                </li>
                            </ul>
                            <ul class="nav navbar-nav col-lg-7">
                                <li>
                                    <div class="navbar-form">
                                        <div class="btn-group" data-toggle="buttons-radio">
                                            <button type="button" id="positiveBtn" class="btn btn-primary">Positive</button>
                                            <button type="button" id="neutralBtn" class="btn btn-primary">Neutral</button>
                                            <button type="button" id="negativeBtn" class="btn btn-primary">Negative</button>
                                        </div>
                                    </div>
                                </li>
                                <li><span id="statustext" class="navbar-text"></span></li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>
            <div class="map_container">
                @RenderBody()
            </div>
            @Scripts.Render("~/bundles/jquery")
            @Scripts.Render("~/bundles/bootstrap")
            @RenderSection("scripts", required: false)
        </body>
        </html>

**Para modificar Index.cshtml:**

1.  En el **Explorador de soluciones**, expanda **TweetSentimentWeb**, **Views** y **Home**, y haga doble clic en **Index.cshtml**.
2.  Reemplace el contenido por lo siguiente:

        @{
            ViewBag.Title = "Tweet Sentiment";
        }

        <div class="map_container">
            <div id="map_canvas"/>
        </div>

**Para modificar el archivo site.css:**

1.  En el **Explorador de soluciones**, expanda **TweetSentimentWeb** y **Content**, y haga doble clic en **Site.css**.
2.  Anexe el código siguiente al archivo.

        /* make container, and thus map, 100% width */
        .map_container {
            width: 100%;
            height: 100%;
        }

        #map_canvas{
          height:100%;
        }

        #tweets{
          position: absolute;
          top: 60px;
          left: 75px;
          z-index:1000;
          font-size: 30px;
        }

**Para modificar el archivo global.asax:**

1.  En el **Explorador de soluciones**, expanda **TweetSentimentWeb** y haga doble clic en **Global.asax**.
2.  Agregue la siguiente instrucción using:

        using System.Web.Http;

3.  Agregue las líneas siguientes a la función **Application\_Start()**:

        // Register API routes
        GlobalConfiguration.Configure(WebApiConfig.Register);

    Modifique el registro de las rutas de la API para que el controlador de la API web funcione dentro de la aplicación MVC.

**Para ejecutar la aplicación web:**

1.  Compruebe que la aplicación de consola de servicio de streaming sigue en ejecución. Así puede ver los cambios en tiempo real.
2.  Presione **F5** para ejecutar la aplicación web:

    ![hdinsight.hbase.twitter.sentiment.bing.map][hdinsight.hbase.twitter.sentiment.bing.map]
3.  En el cuadro de texto, escriba una palabra clave y haga clic en **Ir**. Según los datos recopilados en la tabla de HBase, es posible que algunas palabras clave no se encuentren. Inténtelo con algunas palabras clave comunes, como "amor", "xbox", "playstation", etc.
4.  Alterne entre **Positivo**, **Neutral** y **Negativo** para comparar las opiniones sobre el tema.
5.  Deje el servicio de streaming en ejecución durante otra hora, busque la misma palabra clave y compare los resultados.

De forma opcional, puede implementar la aplicación en un sitio web de Azure. Para obtener instrucciones, consulte [Introducción a Azure y ASP.NET][Introducción a Azure y ASP.NET].

## <span id="nextsteps"></span></a>Pasos siguientes

En este tutorial, hemos visto cómo obtener tweets, analizar la opinión de estos, guardar los datos de opinión en HBase y presentar los datos de opinión de Twitter en tiempo real en mapas de Bing. Para obtener más información, consulte:

-   [Introducción a HDInsight][Introducción a HDInsight]
-   [Análisis de datos de Twitter con Hadoop en HDInsight][Análisis de datos de Twitter con Hadoop en HDInsight]
-   [Análisis de la información de retraso de vuelos usando HDInsight][Análisis de la información de retraso de vuelos usando HDInsight]
-   [Desarrollo de programas de streaming de Hadoop C# para HDInsight][Desarrollo de programas de streaming de Hadoop C# para HDInsight]
-   [Desarrollo de programas MapReduce de Java para HDInsight][Desarrollo de programas MapReduce de Java para HDInsight]

  [análisis de opinión]: http://en.wikipedia.org/wiki/Sentiment_analysis
  []: ./media/hdinsight-hbase-analyze-twitter-sentiment/AppArchitecture.png
  [hdinsight.hbase.twitter.sentiment.bing.map]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterSentimentBingMap.png
  []: https://github.com/maxluk/tweet-sentiment
  [Requisitos previos]: #prerequisites
  [Crear una aplicación de Twitter]: #twitter
  [Crear un servicio simple de streaming de Twitter]: #streaming
  [Crear un sitio web de Azure para visualizar la opinión de Twitter]: #web
  [Pasos siguientes]: #nextsteps
  [Primeros pasos para HBase con Hadoop en HDInsight]: ../hdinsight-hbase-get-started/
  [Instalar Visual Studio]: http://msdn.microsoft.com/en-us/library/e2h7fzkw.aspx
  [OAuth]: http://oauth.net/
  [1]: https://apps.twitter.com/
  [hdi.hbase.twitter.sentiment.twitter.app]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterApp.png
  [Tweetinvi]: https://www.nuget.org/packages/TweetinviAPI/
  [Protobuf-net]: https://www.nuget.org/packages/protobuf-net/
  [2]: https://github.com/hdinsight/hbase-sdk-for-net
  [hdinsight.hbase.twitter.sentiment.streaming.service]: ./media/hdinsight-hbase-analyze-twitter-sentiment/StreamingService.png
  [3]: http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/
  [Introducción a Azure y ASP.NET]: ../web-sites-dotnet-get-started/
  [Introducción a HDInsight]: ../hdinsight-get-started/
  [Análisis de datos de Twitter con Hadoop en HDInsight]: ../hdinsight-analyze-twitter-data/
  [Análisis de la información de retraso de vuelos usando HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Desarrollo de programas de streaming de Hadoop C# para HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Desarrollo de programas MapReduce de Java para HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
