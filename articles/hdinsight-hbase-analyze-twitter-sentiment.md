<properties urlDisplayName="Analyze realt-time Twitter sentiment with Hbase in HDInsight" pageTitle="Análisis de opinión en Twitter en tiempo real con HBase en HDInsight | Azure" metaKeywords="" description="Learn how to do real-time analysis of big data using HBase in an HDInsight (Hadoop) cluster." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze real-time Twitter sentiment with HBase in HDInsight" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao" />

# Realizar análisis de opinión en Twitter en tiempo real con HBase en HDInsight

Obtenga información acerca de cómo realizar [análisis de opinión](http://en.wikipedia.org/wiki/Sentiment_analysis) en tiempo real de grandes volúmenes de datos con HBase en un clúster de HDInsight (Hadoop).


Los sitios web de las redes sociales constituyen una de las principales fuerzas motrices para la adopción de grandes datos. Las API públicas proporcionadas por sitios como Twitter constituyen un origen de datos muy útil para analizar y comprender las tendencias populares. En este tutorial, aprenderá a desarrollar una aplicación de servicio de streaming de consola y una aplicación web ASP.NET para hacer lo siguiente:

![][img-app-arch]

- Obtener tweets con geoetiqueta en tiempo real a través de la API de streaming de Twitter.
- Evaluar la opinión de dichos tweets.
- Almacenar la información de opinión en HBase con el SDK de HBase de Microsoft.
- Trazar los resultados estadísticos en tiempo real en mapas de Bing con una aplicación web ASP.NET. La visualización de estos tweets resultará similar a lo siguiente:

	![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
	
	Puede consultar los tweets con ciertas palabras clave para hacerse una idea acerca de si las opiniones que se expresan son positivas, negativas o neutras.

Encontrará una solución completa de Visual Studio de ejemplo en [https://github.com/maxluk/tweet-sentiment](https://github.com/maxluk/tweet-sentiment).






























##En este artículo

- [Requisitos previos](#prerequisites)
- [Crear una aplicación de Twitter](#twitter)
- [Crear un servicio simple de streaming de Twitter](#streaming)
- [Crear un sitio web de Azure para visualizar la opinión de Twitter](#web)
- [Pasos siguientes](#nextsteps)

##<a id="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

- **Un clúster de HBase en HDInsight**. Para obtener instrucciones sobre el aprovisionamiento de clústeres, consulte [Introducción al uso de HBase con Hadoop en HDInsight][hBase-get-started]. Para completar el tutorial, necesitará los datos siguientes:

	<table border="1">
	<tr><th>Propiedad del clúster</th><th>Descripción</th></tr>
	<tr><td>Nombre del clúster de HBase</td><td>El nombre del clúster de HBase de HDInsight. Por ejemplo: https://myhbase.azurehdinsight.net/</td></tr>
	<tr><td>Nombre de usuario de clúster</td><td>El nombre de la cuenta de usuario de Hadoop. El nombre de usuario de Hadoop predeterminado es <strong>admin</strong>.</td></tr>
	<tr><td>Contraseña de usuario de clúster</td><td>La contraseña de usuario del clúster de Hadoop.</td></tr>
	</table>

- **Una estación de trabajo** con Visual Studio 2013 instalado. Para obtener instrucciones, consulte [Instalar Visual Studio](http://msdn.microsoft.com/es-es/library/e2h7fzkw.aspx).





##<a id="twitter"></a>Crear secretos y un identificador de aplicación de Twitter

Las API de streaming de Twitter autorizan las solicitudes con [OAuth](http://oauth.net/). 

El primer paso es utilizar OAuth para crear una aplicación nueva en el sitio de desarrolladores de Twitter.

**Para crear secretos y un identificador de aplicación de Twitter:**

1. Inicie sesión en [https://apps.twitter.com/](https://apps.twitter.com/). Haga clic en el vínculo **Regístrese ahora** si no tiene una cuenta de Twitter.
2. Haga clic en **Create New App**.
3. Escriba el **nombre**, la **descripción** y el **sitio web**. El campo Sitio web no se usa en realidad. No es necesario escribir una URL válida. La siguiente tabla muestra algunos valores de ejemplo para utilizar:

	<table border="1">
	<tr><th>Campo</th><th>Valor</th></tr>
	<tr><td>Nombre</td><td>MyHDInsightHBaseApp</td></tr>
	<tr><td>Descripción</td><td>MyHDInsightHBaseApp</td></tr>
	<tr><td>Sitio web</td><td>http://www.myhdinsighthbaseapp.com</td></tr>
	</table>

4. Haga clic en **Yes, I agree** y, a continuación, en **Create your Twitter application**.
5. Haga clic en la pestaña **Permisos**. El permiso predeterminado es **solo lectura**. Esto es suficiente para este tutorial. 
6. Haga clic en la pestaña **Claves de API**.
7. Haga clic en **Create my access token**.
8. Haga clic en **Prueba de OAuth** en la esquina superior derecha de la página.
9. Anote los valores de **Clave de API**, **Secreto de API**, **Token de acceso** y **Secreto de token de acceso**. Los necesitará más adelante en el tutorial.

	![hdi.hbase.twitter.sentiment.twitter.app][img-twitter-app]






























##<a id="streaming"></a> Crear un servicio simple de streaming de Twitter

Cree una aplicación de consola para obtener tweets, calcule la puntuación de opinión de los tweets y envíe las palabras procesadas de estos a HBase.

**Para crear la  solución de Visual Studio:**

1. Abra **Visual Studio**.
2. En el menú **Archivo**, vaya a **Nuevo** y, a continuación, haga clic en **Proyecto**.
3. Escriba o seleccione los valores siguientes:

	- Plantillas: **Visual C#**
	- Plantilla: **Aplicación de consola**
	- Nombre: **TweetSentimentStreaming** 
	- Ubicación: **C:\Tutorials**
	- Nombre de la solución: **TweetSentimentStreaming**

4. Haga clic en **Aceptar** para continuar.
 


**Para instalar paquetes de NuGet y agregar referencias de SDK:**

1. En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y, a continuación, en **Consola del Administrador de paquetes**. El panel de la consola se abrirá en la parte inferior de la página.
2. Con los comandos siguientes, instale los paquetes [Tweetinvi](https://www.nuget.org/packages/TweetinviAPI/) (para obtener acceso a la API de Twitter) y [Protobuf-net](https://www.nuget.org/packages/protobuf-net/) (para serializar y deserializar objetos).

		Install-Package TweetinviAPI
		Install-Package protobuf-net 

	> [WACOM.NOTE] El paquete NuGet del SDK de Hbase de Microsoft no está disponible desde el 26 de agosto de 2014. El repositorio Github es [https://github.com/hdinsight/hbase-sdk-for-net](https://github.com/hdinsight/hbase-sdk-for-net). Hasta que el SDK esté disponible, deberá compilar la dll por su cuenta. Para obtener instrucciones, consulte [Introducción al uso de HBase con Hadoop en HDInsight][hdinsight-hbase-get-started].

3. En el **Explorador de soluciones**, haga clic con el botón secundario en **Referencias** y, a continuación, haga clic en **Agregar referencia**.
4. En el panel izquierdo, expanda **Ensamblados** y, a continuación, haga clic en **Marco**.
5. En el panel derecho, active la casilla frente a **System.Configuration** y, a continuación, haga clic en **Aceptar**.



**Para definir la clase de servicio de streaming de Twitter:**

1. En el **Explorador de soluciones**, haga clic con el botón secundario en **TweetSentimentStreaming**, seleccione **Agregar** y, a continuación, haga clic en **Clase**.
2. En **Nombre**, escriba **HBaseWriter** y, a continuación, haga clic en **Agregar**.
3. En **HBaseWriter.cs**, agregue lo siguiente mediante instrucciones en la parte superior del archivo:

		using System.IO;		
		using System.Threading;
		using System.Globalization;
		using Microsoft.HBase.Client;
		using Tweetinvi.Core.Interfaces;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

4. En **HbaseWriter.cs**, agregue una clase nueva denominada **DictionaryItem**:

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

5. En la clase **HBaseWriter**, defina las variables y constantes siguientes:

        // Clúster de HBase en HDinsight e información de la tabla de HBase
        const string CLUSTERNAME = "https://<HBaseClusterName>.azurehdinsight.net/";
        const string HADOOPUSERNAME = "<HadoopUserName>"; //el nombre predeterminado es "admin"
        const string HADOOPUSERPASSWORD = "<HaddopUserPassword>";
        const string HBASETABLENAME = "tweets_by_words";

        // Archivo de diccionario de opinión y caracteres de puntuación
        const string DICTIONARYFILENAME = @"..\..\data\dictionary\dictionary.tsv";
        private static char[] _punctuationChars = new[] { 
            ' ', '!', '\"', '#', '$', '%', '&', '\'', '(', ')', '*', '+', ',', '-', '.', '/',   //ascii 23--47
            ':', ';', '<', '=', '>', '?', '@', '[', ']', '^', '_', '`', '{', '|', '}', '~' };   //ascii 58--64 + misc.

        // Para escribir en HBase
        HBaseClient client;

        // un diccionario de opinión para estimar la opinión. Se carga desde un archivo físico.
        Dictionary<string, DictionaryItem> dictionary;
        
        // usar escritura multiproceso
        Thread writerThread;
        Queue<ITweet> queue = new Queue<ITweet>();
        bool threadRunning = true;

6. Establezca los valores de constante, incluidos **&lt;HBaseClusterName>**, **&lt;HadoopUserName>** y **&lt;HaddopUserPassword>**. Si desea cambiar el nombre de tabla de HBase, deberá cambiarlo también en la aplicación web.

	Más adelante en este tutorial, descargará y moverá el archivo dictionary.tsv a una carpeta específica.

7. Defina las funciones siguientes en la clase **HBaseWriter**:

		// Esta función conecta a HBase, carga el diccionario de opinión e inicia el subproceso para escribir.
        public HBaseWriter()
        {
            ClusterCredentials credentials = new ClusterCredentials(new Uri(CLUSTERNAME), HADOOPUSERNAME, HADOOPUSERPASSWORD);
            client = new HBaseClient(credentials);

            // crear la tabla de HBase si aún no existe
            if (!client.ListTables().name.Contains(HBASETABLENAME))
            {
                TableSchema tableSchema = new TableSchema();
                tableSchema.name = HBASETABLENAME;
                tableSchema.columns.Add(new ColumnSchema { name = "d" });
                client.CreateTable(tableSchema);
                Console.WriteLine("Table \"{0}\" is created.", HBASETABLENAME);
            }

            // Cargar diccionario de opinión desde un archivo
            LoadDictionary();

			// Iniciar un subproceso para escribir en HBase
            writerThread = new Thread(new ThreadStart(WriterThreadFunction));
            writerThread.Start();
        }

        ~HBaseWriter()
        {
            threadRunning = false;
        }

        // Poner en cola los tweets recibidos
        public void WriteTweet(ITweet tweet)
        {
            lock (queue)
            {
                queue.Enqueue(tweet);
            }
        }

        // Cargar diccionario de opinión desde un archivo
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

        // Calcular puntuación de opinión
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

		// Valorar un objeto CellSet para escribirlo en HBase
        private void CreateTweetByWordsCells(CellSet set, ITweet tweet)
        {
            // Dividir el tweet en palabras
            string[] words = tweet.Text.ToLower().Split(_punctuationChars);

            // Calcular la puntuación de opinión según las palabras
            int sentimentScore = CalcSentimentScore(words);
            var word_pairs = words.Take(words.Length - 1)
                                  .Select((word, idx) => string.Format("{0} {1}", word, words[idx + 1]));
            var all_words = words.Concat(word_pairs).ToList();

            // Para cada palabra del tweet, agregar una fila a la tabla de HBase
            foreach (string word in all_words)
            {
                string time_index = (ulong.MaxValue - (ulong)tweet.CreatedAt.ToBinary()).ToString().PadLeft(20) + tweet.IdStr;
                string key = word + "_" + time_index;

                // Crear una fila
                var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };

                // Agregar columnas a la fila, entre otras, el identificador, el idioma, el coordinador (si está disponible) y la opinión del tweet 
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

        // Escribir un tweet (CellSet) en HBase
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

                        // Escribir el tweet mediante la celda de palabras establecida en la tabla de HBase
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

	- **Conectarse a Hbase [ HBaseWriter() ]**: Use el SDK de HBase para crear un objeto *ClusterCredentials* con la URL del clúster y las credenciales de usuario de Hadoop y, a continuación, cree un objeto *HBaseClient* con el objeto ClusterCredentials.
	- **Creación de la tabla de Hbase [ HBaseWriter() ]**: La llamada de método es *HBaseClient.CreateTable()*.
	- **Escritura en la tabla de HBase [ WriterThreadFunction() ]**: La llamada de método es *HBaseClient.StoreCells()*.

**Para completar Program.cs:**

1. En el **Explorador de soluciones**, haga doble clic en **Program.cs** para abrirlo.
2. Al principio del archivo, agregue las instrucciones using siguientes:

		using System.Configuration;
		using System.Diagnostics;
		using Tweetinvi;

3. En la clase **Program**, defina las constantes siguientes:

        const string TWITTERAPPACCESSTOKEN = "<TwitterApplicationAccessToken";
        const string TWITTERAPPACCESSTOKENSECRET = "TwitterApplicationAccessTokenSecret";
        const string TWITTERAPPAPIKEY = "TwitterApplicationAPIKey";
        const string TWITTERAPPAPISECRET = "TwitterApplicationAPISecret";

4. Establezca los valores de constante para que coincidan con los valores de aplicación de Twitter.

3. Modifique la función **Main()** para que quede así:

		static void Main(string[] args)
		{
            TwitterCredentials.SetCredentials(TWITTERAPPACCESSTOKEN, TWITTERAPPACCESSTOKENSECRET, TWITTERAPPAPIKEY, TWITTERAPPAPISECRET);

            Stream_FilteredStreamExample();
		}

4. Agregue la función siguiente a la clase:

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

                        // Escribir tweets en HBase
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

1. Vaya a [https://github.com/maxluk/tweet-sentiment](https://github.com/maxluk/tweet-sentiment).
2. Haga clic en **Descargar ZIP**.
3. Extraiga el archivo en una ubicación local.
4. Copie el archivo de **../tweet-sentiment/SimpleStreamingService/data/dictionary/dictionary.tsv**.
5. 	Pegue el archivo en la solución, debajo de **TweetSentimentStreaming/TweetSentimentStreaming/data/dictionary/dictionary.tsv**.

**Para ejecutar el servicio de streaming:**

1. En Visual Studio, presione **F5**. Esta es una captura de pantalla de la aplicación de consola:

	![hdinsight.hbase.twitter.sentiment.streaming.service][img-streaming-service]
2. Mientras desarrolla la aplicación web, mantenga en ejecución la aplicación de consola de streaming para disponer de más datos.



























##<a id="web"></a> Crear un sitio web de Azure para visualizar la opinión de Twitter

En esta sección, creará una aplicación web ASP.NET MVC para leer los datos de opinión en tiempo real desde HBase y trazarlos en mapas de Bing.

**Para crear una aplicación web ASP.NET MVC:**

1. Abra Visual Studio.
2. Haga clic en **Archivo**, en **Nuevo** y en **Proyecto**.
3. Escriba los valores siguientes:

	- Categoría de plantilla: **Visual C#/Web**
	- Plantilla: **Aplicación web ASP.NET**
	- Nombre: **TweetSentimentWeb**
	- Ubicación: **C:\Tutorials** 
4. Haga clic en **Aceptar**.
5. En **Seleccione una plantilla**, haga clic en **MVC**. 
6. En **Windows Azure**, haga clic en **Administrar suscripciones**.
7. En **Administrar suscripciones de Windows Azure**, haga clic en **Iniciar sesión**.
8. Escriba sus credenciales de Azure. En la pestaña Cuentas, aparece la información de suscripción de Azure.
9. Haga clic en **Cerrar** para cerrar la ventana Administrar suscripciones de Windows Azure.
10. En **Nuevo proyecto ASP.NET - TweetSentimentWeb**, haga clic en **Aceptar**.
11. En **Parámetros de configuración del sitio de Windows Azure**, seleccione la **región** más cercana a usted. No es necesario especificar un servidor de bases de datos. 
12. Haga clic en **Aceptar**.

**Para instalar los paquetes NuGet:**

1. En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y, a continuación, en **Consola del Administrador de paquetes**. El panel de la consola se abre en la parte inferior de la página.
2. Con los comandos siguientes instale el paquete [Protobuf-net](https://www.nuget.org/packages/protobuf-net/), que se usa para serializar y deserializar objetos.

		Install-Package protobuf-net 

	> [WACOM.NOTE] El paquete NuGet del SDK de Hbase de Microsoft no está disponible desde el 20 de agosto de 2014. El repositorio Github es [https://github.com/hdinsight/hbase-sdk-for-net](https://github.com/hdinsight/hbase-sdk-for-net). Hasta que el SDK esté disponible, deberá compilar la dll por su cuenta. Para obtener instrucciones, consulte [Introducción al uso de HBase con Hadoop en HDInsight][hdinsight-hbase-get-started].

**Para agregar la clase HBaseReader:**

1. En el **Explorador de soluciones**, expanda **TweetSentiment**.
2. Haga clic con el botón secundario en **Modelos** y, a continuación, haga clic en **Agregar** y en **Clase**.
3. En Nombre, escriba **HBaseReader.cs**, y, a continuación, haga clic en **Agregar**.
4. Reemplace el código por lo siguiente:

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
		        // Para leer datos de opinión de tweets en HBase de HDInsight
		        HBaseClient client;
		
		        // Clúster de HBase en HDinsight e información de la tabla de HBase
		        const string CLUSTERNAME = "<HBaseClusterName>";
		        const string HADOOPUSERNAME = "<HBaseClusterHadoopUserName>"
		        const string HADOOPUSERPASSWORD = "<HBaseCluserUserPassword>";
		        const string HBASETABLENAME = "tweets_by_words";
		
		        // El constructor
		        public HBaseReader()
		        {
		            ClusterCredentials creds = new ClusterCredentials(
		                            new Uri(CLUSTERNAME),
		                            HADOOPUSERNAME,
		                            HADOOPUSERPASSWORD);
		            client = new HBaseClient(creds);
		        }
		
		        // Consultar datos de opinión de tweets en la tabla de HBase de manera asincrónica 
		        public async Task<IEnumerable<Tweet>> QueryTweetsByKeywordAsync(string keyword)
		        {
		            List<Tweet> list = new List<Tweet>();
		
		            // Demostrar el filtrado de datos de las últimas seis horas con la clave de fila
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
		
		            // Realizar llamada de detección asincrónica
		            ScannerInformation scannerInfo =
		                await client.CreateScannerAsync(HBASETABLENAME, scanSettings);
		
		            CellSet next;
		
		            while ((next = await client.ScannerGetNextAsync(scannerInfo)) != null)
		            {
		                foreach (CellSet.Row row in next.rows)
		                {
		                    // encontrar la celda con el patrón de cadena "d:coor" 
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

4. En la clase **HBaseReader**, cambie los valores de constante:

	- **CLUSTERNAME**: el nombre del clúster de HBase. Por ejemplo, *https://<HBaseClusterName>.azurehdinsight.net/*. 
    - **HADOOPUSERNAME**: el nombre de usuario de Hadoop del clúster de HBase. El nombre predeterminado es *admin*.
    - **HADOOPUSERPASSWORD**: La contraseña de usuario de Hadoop del clúster de HBase.
    - **HBASETABLENAME** = "tweets_by_words";

	El nombre de tabla de HBase es "tweets_by_words". Para que la aplicación web pueda leer los datos de la misma tabla de HBase, los valores deben coincidir con los que se enviaron en el servicio de streaming.




**Para agregar el controlador TweetsController:**

1. En el **Explorador de soluciones**, expanda **TweetSentimentWeb**.
2. Haga clic con el botón secundario en **Controladores** y, a continuación, haga clic en **Agregar** y en **Controlador**.
3. Haga clic en **Controlador Web API 2 - Vacío** y, a continuación, en **Agregar**.
4. En Nombre del controlador, escriba **TweetsController**, y, a continuación, haga clic en **Agregar**.
5. En el **Explorador de soluciones**, haga doble clic en TweetsController.cs para abrir el archivo.
5. Modifique el archivo para que quede así:

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

1. En el **Explorador de soluciones**, expanda **TweetSentimentWeb**.
2. Haga clic con el botón secundario en **Scripts** y, a continuación, haga clic en **Agregar** y en **Archivo JavaScript**.
3. En Nombre del elemento, escriba **heatmap.js**.
4. Copie y pegue el código siguiente en el archivo. El código lo escribió Alastair Aitchison. Para obtener más información, consulte [http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/](http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/).

		/*******************************************************************************
		* Autor: Alastair Aitchison
		* Sitio web: http://alastaira.wordpress.com
		* Fecha: 15 de abril de 2011
		* 
		* Description: 
		* Este archivo JavaScript ofrece un algoritmo que se puede usar para agregar un mapa de calor
		* superponer en un control de Mapas de Bing v7. La intensidad y la paleta de temperatura
		* del mapa de calor están diseñadas para personalizarse con facilidad.
		*
		* Requisitos:
		* La capa del mapa de calor la crea el cliente de forma dinámica con el
		* elemento HTML5 <canvas> y, por tanto, requiere un navegador que admita
		* este elemento. Se ha probado en los navegadores IE9, Firefox 3.6/4 y 
		* Chrome 10. Si confirma que funciona con otros navegadores,
		* me gustaría que me informara.
		
		* Uso:
		* El constructor HeatMapLayer requiere:
		* - Una referencia a un objeto de mapa
		* - Una matriz o elementos de Microsoft.Maps.Location
		* - Parámetros opcionales para personalizar la apariencia de la capa
		*  (radio, unidad, intensidad y degradado del color) y una función de devolución de llamada
		*
		*/
		
		var HeatMapLayer = function (map, locations, options) {
		
		    /* Propiedades privadas */
		    var _map = map,
		      _canvas,
		      _temperaturemap,
		      _locations = [],
		      _viewchangestarthandler,
		      _viewchangeendhandler;
		
		    // Definir opciones predeterminadas
		    var _options = {
		        // Opacidad en el centro de cada punto de calor
		        intensidad: 0.5,
		
		        // Radio afectado de cada punto de calor
		        radio: 1000,
		
		        // Si el radio se presenta en metros o como un valor de píxel absoluto
		        unidad: 'metros',
		
		        // Degradado de la temperatura del color en el mapa
		        colourgradient: {
		            "0.00": 'rgba(255,0,255,20)',  // Magenta
		            "0.25": 'rgba(0,0,255,40)',    // Azul
		            "0.50": 'rgba(0,255,0,80)',    // Verde
		            "0.75": 'rgba(255,255,0,120)', // Amarillo
		            "1.00": 'rgba(255,0,0,150)'    // Rojo
		        },
		
		        // Función de devolución de llamada que se activa después de redibujar la capa del mapa de calor 
		        callback: null
		    };
		
		    /* Métodos privados */
		    function _init() {
		        var _mapDiv = _map.getRootElement();
		
		        if (_mapDiv.childNodes.length >= 3 && _mapDiv.childNodes[2].childNodes.length >= 2) {
		            // Crear el elemento canvas
		            _canvas = document.createElement('canvas');
		            _canvas.style.position = 'relative';
		
		            var container = document.createElement('div');
		            container.style.position = 'absolute';
		            container.style.left = '0px';
		            container.style.top = '0px';
		            container.appendChild(_canvas);
		
		            _mapDiv.childNodes[2].childNodes[1].appendChild(container);
		
		            // Reemplazar los valores predeterminados con opciones transferidas al constructor
		            _setOptions(options);
		
		            // Cargar matriz de los datos de ubicación
		            _setPoints(locations);
		
		            // Crear un degradado de color a partir de los puntos de color suministrados
		            _temperaturemap = _createColourGradient(_options.colourgradient);
		
		            // Activar el controlador de eventos para volver a dibujar el canvas del mapa de calor
		            _viewchangestarthandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangestart', _clearHeatMap);
		            _viewchangeendhandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangeend', _createHeatMap);
		
		            _createHeatMap();
		
		            delete _init;
		        } else {
		            setTimeout(_init, 100);
		        }
		    }
		
		    // Restablece el mapa de calor
		    function _clearHeatMap() {
		        var ctx = _canvas.getContext("2d");
		        ctx.clearRect(0, 0, _canvas.width, _canvas.height);
		    }
		
		    // Crea un degradado del color a partir de los puntos de color suministrados durante la inicialización
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
		
		    // Aplica un degradado de color al mapa de intensidad
		    function _colouriseHeatMap() {
		        var ctx = _canvas.getContext("2d");
		        var dat = ctx.getImageData(0, 0, _canvas.width, _canvas.height);
		        var pix = dat.data; // pix es una matriz de píxeles de canvas CanvasPixelArray que contiene la altura x la anchura x 4 bytes de datos (RGBA)
		        for (var p = 0, len = pix.length; p < len;) {
		            var a = pix[p + 3] * 4; // obtener el valor alfa de este píxel
		            if (a != 0) { // Si hay algún dato para trazar
		                pix[p] = _temperaturemap[a]; // establecer el valor rojo del degradado correspondiente a este valor alfa
		                pix[p + 1] = _temperaturemap[a + 1]; //establecer el valor verde basado en el valor alfa
		                pix[p + 2] = _temperaturemap[a + 2]; //establecer el valor azul basado en el alfa
		            }
		            p += 4; // Pasar al píxel siguiente
		        }
		        ctx.putImageData(dat, 0, 0);
		    }
		
		    // Establece las opciones transferidas
		    function _setOptions(options) {
		        for (attrname in options) {
		            _options[attrname] = options[attrname];
		        }
		    }
		
		    // Establece los puntos del mapa de calor a partir de una matriz de Microsoft.Maps.Locations  
		    function _setPoints(locations) {
		        _locations = locations;
		    }
		
		    // Método principal para trazar el mapa de calor
		    function _createHeatMap() {
		        // Garantizar que el canvas coincide con las dimensiones actuales del mapa
		        // También tiene el efecto de restablecer el canvas
		        _canvas.height = _map.getHeight();
		        _canvas.width = _map.getWidth();
		
		        _canvas.style.top = -_canvas.height / 2 + 'px';
		        _canvas.style.left = -_canvas.width / 2 + 'px';
		
		        // Calcular el radio del píxel de cada p unto de calor en el zoom del mapa actual
		        if (_options.unit == "pixels") {
		            radiusInPixel = _options.radius;
		        } else {
		            radiusInPixel = _options.radius / _map.getMetersPerPixel();
		        }
		
		        var ctx = _canvas.getContext("2d");
		
		        // Convertir lat/long en la ubicación del píxel
		        var pixlocs = _map.tryLocationToPixel(_locations, Microsoft.Maps.PixelReference.control);
		        var shadow = 'rgba(0, 0, 0, ' + _options.intensity + ')';
		        var mapWidth = 256 * Math.pow(2, _map.getZoom());
		
		        // Crear el mapa de intensidad haciendo un traspaso a través de cada ubicación
		        for (var i = 0, len = pixlocs.length; i < len; i++) {
		            var x = pixlocs[i].x;
		            var y = pixlocs[i].y;
		
		            if (x < 0) {
		                x += mapWidth * Math.ceil(Math.abs(x / mapWidth));
		            }
		
		            // Crear degradado radial centrado en este punto
		            var grd = ctx.createRadialGradient(x, y, 0, x, y, radiusInPixel);
		            grd.addColorStop(0.0, shadow);
		            grd.addColorStop(1.0, 'transparent');
		
		            // Trazar el punto de calor en el canvas
		            ctx.fillStyle = grd;
		            ctx.fillRect(x - radiusInPixel, y - radiusInPixel, 2 * radiusInPixel, 2 * radiusInPixel);
		        }
		
		        // Aplicar un degradado de color específico al mapa de intensidad
		        _colouriseHeatMap();
		
		        // Llamar a la función de devolución de llamada, si se especifica
		        if (_options.callback) {
		            _options.callback();
		        }
		    }
		
		    /* Métodos públicos */
		
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
		
		    // Define las opciones de intensidad, radio, degradado de color, etc.
		    this.SetOptions = function (options) {
		        _setOptions(options);
		    }
		
		    // Define una matriz de Microsoft.Maps.Locations a partir de la cual se crea el mapa de calor
		    this.SetPoints = function (locations) {
		        // Restablecer la capa del mapa de calor actual
		        _clearHeatMap();
		        // Transferir el nuevo conjunto de ubicaciones
		        _setPoints(locations);
		        // Volver a crear la capa
		        _createHeatMap();
		    }
		
		    // Elimina del DOM la capa del mapa de calor
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
		
		    // Llama a la rutina de inicialización
		    _init();
		};
		
		// Llama al método de módulo cargado
		Microsoft.Maps.moduleLoaded('HeatMapModule');


**Para agregar tweetStream.js:**

1. En el **Explorador de soluciones**, expanda **TweetSentimentWeb**.
2. Haga clic con el botón secundario en **Scripts** y, a continuación, haga clic en **Agregar** y en **Archivo JavaScript**.
3. En Nombre del elemento, escriba **twitterStream.js**.
4. Copie y pegue el código siguiente en el archivo:

		var liveTweetsPos = [];
		var liveTweets = [];
		var liveTweetsNeg = [];
		var map;
		var heatmap;
		var heatmapNeg;
		var heatmapPos;
		
		function initialize() {
		    // Inicializar el mapa
		    var options = {
		        credenciales: "AvFJTZPZv8l3gF8VC3Y7BPBd0r7LKo8dqKG02EAlqg9WAi0M7la6zSIT-HwkMQbx",
		        centro: new Microsoft.Maps.Location(23.0, 8.0),
		        mapTypeId: Microsoft.Maps.MapTypeId.ordnanceSurvey,
		        labelOverlay: Microsoft.Maps.LabelOverlay.hidden,
		        zoom: 2.5
		    };
		    var map = new Microsoft.Maps.Map(document.getElementById('map_canvas'), options);
		
		    // Opciones del mapa de calor para capas positivas, neutrales y negativas
		
		    var heatmapOptions = {
		        // Opacidad en el centro de cada punto de calor
		        intensidad: 0.5,
		
		        // Radio afectado de cada punto de calor
		        radio: 15,
		
		        // Si el radio se presenta en metros o como un valor de píxel absoluto
		        unidad: 'pixels'
		    };
		
		    var heatmapPosOptions = {
		        // Opacidad en el centro de cada punto de calor
		        intensidad: 0.5,
		
		        // Radio afectado de cada punto de calor
		        radio: 15,
		
		        // Si el radio se presenta en metros o como un valor de píxel absoluto
		        unidad: 'pixels',
		
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
		        // Opacidad en el centro de cada punto de calor
		        intensidad: 0.5,
		
		        // Radio afectado de cada punto de calor
		        radio: 15,
		
		        // Si el radio se presenta en metros o como un valor de píxel absoluto
		        unidad: 'pixels',
		
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
		
		    // Registrar y cargar el módulo del mapa de calor del cliente
		    Microsoft.Maps.registerModule("HeatMapModule", "scripts/heatmap.js");
		    Microsoft.Maps.loadModule("HeatMapModule", {
		        callback: function () {
		            // Crear capas del mapa de calor para tweets positivos, neutrales y negativos
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
		
		            // Si es correcto, 'data' contiene una lista de tweets.
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
		    //Agregar tweet a las matrices el mapa de calor.
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

1. En el **Explorador de soluciones**, expanda **TweetSentimentWeb**, **Views** y **Shared** y, a continuación, haga doble clic en _**Layout.cshtml**.
2. Reemplace el contenido por lo siguiente:

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

1. En el **Explorador de soluciones**, expanda **TweetSentimentWeb**, **Views** y **Home** y, a continuación, haga doble clic en **Index.cshtml**.
2. Reemplace el contenido por lo siguiente:

		@{
		    ViewBag.Title = "Tweet Sentiment";
		}
		
		<div class="map_container">
		    <div id="map_canvas"/>
		</div>

**Para modificar el archivo site.css:**

1. En el **Explorador de soluciones**, expanda **TweetSentimentWeb**, **Content** y, a continuación, haga doble clic en **Site.css**.
2. Anexe el código siguiente al archivo.
		
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

1. En el **Explorador de soluciones**, expanda **TweetSentimentWeb** y, a continuación, haga doble clic **Global.asax**.
2. Agregue la siguiente instrucción using:

		using System.Web.Http;

2. Agregue las líneas siguientes a la función **Application_Start()**:

		// Registrar rutas API
		GlobalConfiguration.Configure(WebApiConfig.Register);
  
	Modifique el registro de las rutas de la API para que el controlador de la API web funcione dentro de la aplicación MVC.

**Para ejecutar la aplicación web:**

1. Compruebe que la aplicación de consola de servicio de streaming sigue en ejecución. Así puede ver los cambios en tiempo real.
2. Presione **F5** para ejecutar la aplicación web:

	![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
2. En el cuadro de texto, escriba una palabra clave y haga clic en **Go**.  Según los datos recopilados en la tabla de HBase, es posible que algunas palabras clave no se encuentren. Inténtelo con algunas palabras clave comunes, como "amor", "xbox", "playstation", etc. 
3. Alterne entre **Positivo**, **Neutral** y **Negativo** para comparar la opinión sobre el asunto.
4. Deje el servicio de streaming en ejecución durante otra hora, busque la misma palabra clave y compare los resultados.

 
De forma opcional, puede implementar la aplicación en un sitio web de Azure.  Para obtener instrucciones, consulte [Introducción a Azure y ASP.NET][website-get-started].
 
##<a id="nextsteps"></a>Pasos siguientes

En este tutorial, hemos visto cómo obtener tweets, analizar la opinión de estos, guardar los datos de opinión en HBase y presentar los datos de opinión de Twitter en tiempo real en mapas de Bing. Para obtener más información, consulte:

- [Introducción a HDInsight][hdinsight-get-started]
- [Análisis de datos de Twitter con Hadoop en HDInsight][hdinsight-analyze-twitter-data]
- [Análisis de la información de retraso de vuelos usando HDInsight][hdinsight-analyze-flight-delay-data]
- [Desarrollo de programas de streaming de Hadoop C# para HDInsight][hdinsight-develop-streaming]
- [Desarrollo de programas MapReduce de Java para HDInsight][hdinsight-develop-mapreduce]


[hbase-get-started]: ../hdinsight-hbase-get-started/
[website-get-started]: ../web-sites-dotnet-get-started/



[img-app-arch]: ./media/hdinsight-hbase-analyze-twitter-sentiment/AppArchitecture.png
[img-twitter-app]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterApp.png
[img-streaming-service]: ./media/hdinsight-hbase-analyze-twitter-sentiment/StreamingService.png
[img-bing-map]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterSentimentBingMap.png



[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hdinsight-analyze-twitter-data]: ../hdinsight-analyze-twitter-data/
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started/




[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/es-es/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell
[powershell-script]: http://technet.microsoft.com/es-es/library/ee176949.aspx

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/

