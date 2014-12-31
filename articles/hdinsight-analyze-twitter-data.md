<properties urlDisplayName="Analyze Twitter data with HDInsight Hadoop" pageTitle="Análisis de datos de Twitter con Hadoop en HDInsight | Azure" metaKeywords="" description="Learn how to use Hive to analyze Twitter data on Hadoop in HDInsight to find the usage frequency of a particular word." metaCanonical="" services="HDInsight" documentationCenter="" title="Analyze Twitter data with Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jgao" />

# Análisis de datos de Twitter con Hadoop en HDInsight

Aprenda a analizar datos de Twitter con Hadoop usando Hive en HDInsight.

Los sitios web de las redes sociales constituyen una de las principales fuerzas motrices para la adopción de Big Data. Las API públicas proporcionadas por sitios como Twitter constituyen un origen de datos muy útil para analizar y comprender las tendencias populares. En este tutorial, se conectará con el servicio web de Twitter para obtener algunos tweets con la API de streaming de Twitter y, más adelante, utilizará Hive para obtener una lista de los usuarios de Twitter que enviaron la mayor cantidad de tweets con una palabra determinada.

**Duración aproximada:** 30 minutos

##En este artículo

- [Requisitos previos](#prerequisites)
- [Obtención de una fuente de Twitter](#feed)
- [Creación de un script de HiveQL](#script)
- [Procesamiento de los datos con Hive](#process)
- [Limpieza del tutorial](#cleanup)
- [Pasos siguientes](#nextsteps)

##<a id="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una estación de trabajo** con Azure PowerShell instalado y configurado. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][powershell-install]. Para ejecutar scripts de PowerShell, debe ejecutar Azure PowerShell como administrador y establecer la directiva de ejecución en *RemoteSigned*. Consulte [Ejecución de scripts de Windows PowerShell][powershell-script].

	Antes de ejecutar scripts de PowerShell, asegúrese de estar conectado a su suscripción de Azure mediante el siguiente cmdlet:

		Add-AzureAccount

	Si tiene varias suscripciones a Azure, utilice el siguiente cmdlet para definir la suscripción actual:

		Select-AzureSubscription <AzureSubscriptionName>



- **Un clúster de HDInsight de Azure**. Para obtener instrucciones acerca del aprovisionamiento del clúster, consulte [Introducción al uso de HDInsight de Azure][hdinsight-get-started] o [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision]. Para completar el tutorial, necesitará los datos siguientes:

	<table border="1">
	<tr><th>Propiedad del clúster</th><th>Nombre de variable de PowerShell</th><th>Valor</th><th>Descripción</th></tr>
	<tr><td>Nombre del clúster de HDInsight</td><td>$clusterName</td><td></td><td>Este es el nombre del clúster de HDInsight.</td></tr>
	<tr><td>Nombre de la cuenta de almacenamiento de Azure</td><td>$storageAccountName</td><td></td><td>Cuenta de almacenamiento de Azure disponible para el clúster de HDInsight. Para este tutorial, use la cuenta de almacenamiento predeterminada especificada durante el proceso de aprovisionamiento del clúster.</td></tr>
	<tr><td>Nombre del contenedor de blobs de Azure</td><td>$containerName</td><td></td><td>Para este ejemplo, use el contenedor de almacenamiento de blobs de Azure utilizado para el sistema de archivos predeterminado del clúster de HDInsight. De manera predeterminada, tiene el mismo nombre que el del clúster de HDInsight.</td></tr>
	</table>

**Descripción del almacenamiento de HDInsight**

HDInsight usa el almacenamiento de blobs de Azure para el almacenamiento de datos.  Se llama *WASB* o *Almacenamiento de Azure - Blob*. WASB es la implementación del sistema de archivos distribuido de Hadoop (HDFS) de Microsoft en el almacenamiento de blobs de Azure. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage]. 

Cuando aprovisiona un clúster de HDInsight, se designa un contenedor de almacenamiento de blobs como sistema de archivos predeterminado, al igual que en HDFS. Además de este contenedor, puede agregar más contenedores desde la misma cuenta de almacenamiento de Azure o desde otras diferentes durante el proceso de aprovisionamiento. Para obtener instrucciones acerca de cómo agregar más cuentas de almacenamiento, consulte [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision]. 

> [WACOM.NOTE] Para simplificar el script de PowerShell que se utiliza en este tutorial, todos los archivos se almacenan en el contenedor del sistema de archivos predeterminado, ubicado en */tutorials/twitter*. De forma predeterminada, este contenedor tiene el mismo nombre que el del clúster de HDInsight. Si decide utilizar un contenedor diferente para almacenar estos archivos, actualice el script en consecuencia.

La sintaxis de WASB es la siguiente:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] La sintaxis *wasb://* es la única compatible con la versión 3.0 del clúster de HDInsight. La antigua sintaxis *asv://* es compatible con los clústeres de HDInsight 2.1 y 1.6, pero no es compatible con los clústeres de HDInsight 3.0 y no será compatible con las versiones posteriores.

> La ruta de acceso WASB es la ruta de acceso virtual.  Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage]. 

Para tener acceso a un archivo almacenado en el contenedor del sistema de archivos predeterminado desde HDInsight se puede usar cualquiera de los URI siguientes (use tweets.txt como ejemplo):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/twitter/tweets.txt
	wasb:///tutorials/twitter/tweets.txt
	/tutorials/twitter/tweets.txt

Si desea obtener acceso al archivo directamente desde la cuenta de almacenamiento, el nombre de blob del archivo es:

	tutorials/twitter/tweets.txt

La siguiente tabla enumera los archivos utilizados en este tutorial:

<table border="1">
<tr><th>Archivos</th><th>Descripción</th></tr>
<tr><td>/tutorials/twitter/data/tweets.txt</td><td>Los datos de origen para el trabajo de Hive.</td></tr>
<tr><td>/tutorials/twitter/output</td><td>La carpeta de salida para el trabajo de Hive. El nombre predeterminado del archivo de salida de trabajo en Hive es <strong>000000_0</strong>. </td></tr>
<tr><td>tutorials/twitter/twitter.hql</td><td>El archivo de script de HiveQL.</td></tr>
<tr><td>/tutorials/twitter/jobstatus</td><td>El estado del trabajo de Hadoop.</td></tr>
</table>

##<a id="feed"></a>Obtención de una fuente de Twitter

En este tutorial, verá las [API de streaming de Twitter][twitter-streaming-api]. La API de streaming de Twitter específica que utilizará es [statuses/filter][twitter-statuses-filter].

[Datos de tweets](https://dev.twitter.com/docs/platform-objects/tweets) se almacenan en formato JSon que contiene una compleja estructura anidada. En lugar de escribir varias líneas de código con un lenguaje de programación convencional, puede transformar esta estructura anidada en una tabla Hive para que se puedan realizar consultas a través de un lenguaje similar a SQL llamado HiveQL. 

Twitter utiliza OAuth para brindar acceso autorizado a su API. OAuth es un protocolo de autenticación que permite que los usuarios permitan que la aplicación pueda actuar en su nombre sin compartir la contraseña. Puede encontrar información sobre la instalación en [oauth.net](http://oauth.net/) o en la excelente [Guía de iniciación a OAuth](http://hueniverse.com/oauth/) de Hueniverse.

El primer paso es utilizar OAuth para crear una aplicación nueva en el sitio de desarrolladores de Twitter.

**Para crear una aplicación de Twitter**

1. Inicie sesión en [https://apps.twitter.com/](https://apps.twitter.com/). Haga clic en el vínculo **Regístrese ahora** si no tiene una cuenta de Twitter.
2. Haga clic en **Crear nueva aplicación**.
3. Especifique el **Nombre**, **Descripción**, **Sitio web**. Puede conformar una dirección URL para el campo de sitio web. La siguiente tabla muestra algunos valores de ejemplo para utilizar:

	<table border="1">
	<tr><th>Campo</th><th>Valor</th></tr>
	<tr><td>Nombre</td><td>MyHDInsightApp</td></tr>
	<tr><td>Descripción</td><td>MyHDInsightApp</td></tr>
	<tr><td>Sitio web</td><td>http://www.myhdinsightapp.com</td></tr>
	</table>
4. Active **Acepto** y, a continuación, haga clic en **Crear su aplicación Twitter**.
5. Haga clic en la pestaña **Permisos**. El permiso predeterminado es **Solo lectura**. Esto es suficiente para este tutorial. 
6. Haga clic en la pestaña **Claves de API**.
7. Haga clic en **Crear mi token de acceso**.
8. Haga clic en **Prueba de OAuth** en la esquina superior derecha de la página.
9. Escriba la **clave del consumidor**, **Secreto de consumidor**, **Token de acceso** y **Secreto de token de acceso**. Los necesitará más adelante en el tutorial.

En este tutorial, pasarán a PowerShell para realizar una llamada de servicio web. La otra herramienta popular para realizar llamadas de servicios web [*Curl*][curl]. Es posible descargar la Curl [aquí][curl-download].

>[WACOM.NOTE] Cuando utilice el comando Curl en Windows, utilice comillas dobles en lugar de comillas simples para los valores de opción.

**Para obtener tweets**

1. Abra Windows PowerShell ISE (en la pantalla Inicio de Windows 8, escriba **PowerShell_ISE** y, a continuación, haga clic en **Windows PowerShell ISE**. Consulte [Iniciar Windows PowerShell en Windows 8 y Windows][powershell-start]).

3. Copie el siguiente script en el panel de scripts:

		Write-Host "Set variables ..." -ForegroundColor Green
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<BlobContainerName>"

		$destBlobName = "tutorials/twitter/data/tweets.txt"
		
		$trackString = "Azure, Cloud, HDInsight"
		$track = [System.Uri]::EscapeDataString($trackString);
		$lineMax = 100  #about 3 minutes every 100 lines
		
		$oauth_consumer_key = "<TwitterAppConsumerKey>";
		$oauth_consumer_secret = "<TwitterAppConsumerSecret>";
		$oauth_token = "<TwitterAppAccessToken>";
		$oauth_token_secret = "<TwitterAppAccessTokenSecret>";
		
		Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		
		Write-Host "Create block blob object ..." -ForegroundColor Green
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
		
		Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		
		Write-Host "Format oauth strings ..." -ForegroundColor Green
		$oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
		$ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
		$oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();
		
		$signature = "POST&";
		$signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
		$signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&"); 
		$signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
		$signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
		$signature += [System.Uri]::EscapeDataString("track=" + $track);
		
		$signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);
		
		$hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
		$hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
		$oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));
		
		$oauth_authorization = 'OAuth ';
		$oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
		$oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
		$oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
		$oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
		$oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
		$oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
		$oauth_authorization += 'oauth_version="1.0"';
		
		$post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track); 
				
		Write-Host "Create HTTP web request ..." -ForegroundColor Green
		[System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
		$request.Method = "POST";
		$request.Headers.Add("Authorization", $oauth_authorization);
		$request.ContentType = "application/x-www-form-urlencoded";
		$body = $request.GetRequestStream();
		
		$body.write($post_body, 0, $post_body.length);
		$body.flush();
		$body.close();
		$response = $request.GetResponse() ;
		
		Write-Host "Start stream reading ..." -ForegroundColor Green
		
		$sReader = New-Object System.IO.StreamReader($response.GetResponseStream())
		
		$inrec = $sReader.ReadLine()
		$count = 0
		while (($inrec -ne $null) -and ($count -le $lineMax))
		{
		    if ($inrec -ne "")
		    {
		        Write-Host $count.ToString() ", " -NoNewline -ForegroundColor Green
		        if ($count%10 -eq 0){
		            write-host " --- " -NoNewline
		            Get-Date -Format hh:mm:sstt
		        }
		
		        $writeStream.WriteLine($inrec)
		        $count ++
		    }
		
		    $inrec=$sReader.ReadLine()
		}
		
		Write-Host "Write to the destination blob ..." -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$destBlob.UploadFromStream($memStream) 
		
		$sReader.close()
		Write-Host "Complete!" -ForegroundColor Green

4. Establezca las primeras nueve variables en el script.	

	<table border="1">
	<tr><th>Variable</th><th>Descripción</th></tr>
	<tr><td>$storageAccountName</td><td>La cuenta de almacenamiento de Azure utilizada como sistema de archivos predeterminado del clúster de HDInsight. Esta es la cuenta de almacenamiento especificada en la provisión. Consulte <a href="#prerequisites">Requisitos previos</a>.</td></tr>
	<tr><td>$containerName</td><td>El contenedor de blobs que se utiliza para el sistema de archivos de clúster de HDInsight predeterminado. Este es el contenedor especificado en la provisión. Consulte <a href="#prerequisites">Requisitos previos</a>.</td></tr>
	<tr><td>$destBlobName</td><td>Este es el nombre de blob de salida.  El valor predeterminado es <strong>tutorials/twitter/data/tweets.txt</strong>. Si cambia el valor predeterminado, deberá actualizar los scripts de PowerShell como corresponda.</td></tr>
	<tr><td>$trackString</td><td>El servicio web devolverá tweets relacionados con estas palabras clave.  El valor predeterminado es <strong>Azure, Cloud, HDInsight</strong>. Si cambia el valor predeterminado, actualizará los scripts de PowerShell como corresponda.</td></tr>
	<tr><td>$lineMax</td><td>El valor determina cuántos tweets leerá el script. Tomará unos tres minutos para leer 100 tweets. Puede definir un número mayor, pero aumentará el tiempo necesario para la descarga.</td></tr>
	<tr><td>$oauth_consumer_key</td><td>Esta es la clave de consumidor <strong>de la aplicación Twitter</strong> que ha escrito antes cuando ha creado la aplicación Twitter.</td></tr>
	<tr><td>$oauth_consumer_secret</td><td>Este es el secreto de consumidor <strong>de la aplicación Twitter</strong> que ha escrito antes.</td></tr>
	<tr><td>$oauth_token</td><td>Este es el token de acceso <strong>de la aplicación Twitter</strong> que ha escrito antes.</td></tr>
	<tr><td>$oauth_token_secret</td><td>Este es el secreto de token de acceso <strong>de la aplicación Twitter</strong> que ha escrito antes.</td></tr>
	</table>

5. Presione **F5** para ejecutar el script. Si encuentra problemas, como solución alternativa, seleccione todas las líneas y, a continuación, presione **F8**.
6. Verá un anuncio de "Complete!" al final de la salida. Los mensajes de error aparecerán en rojo.

Como procedimiento de validación, puede revisar el archivo de salida, **/tutorials/twitter/data/tweets.txt**, en el almacenamiento de blobs de Azure con un explorador de almacenamiento de Azure o Azure PowerShell.  Para obtener un script de ejemplo de PowerShell, consulte [Uso de almacenamiento de blobs con HDInsight][hdinsight-storage-powershell]. 



##<a id="script"></a>Creación de un script de HiveQL

Con Azure PowerShell, puede ejecutar varias instrucciones HiveQL a la vez, o empaquetar la instrucción de HiveQL en un archivo script. En este tutorial, creará un script de HiveQL. El archivo de script debe cargarse en WASB. En la siguiente sección, ejecutará el archivo de script con Azure PowerShell.

El script de HiveQL realizará lo siguiente:

1. **Descartar la tabla tweets_raw** en caso de que ya exista la etiqueta.
2. **Crear la tabla de tweets_raw Hive.**. La tabla estructurada temporal de Hive contiene los datos para un procesamiento adicional de ETL.  Para obtener información sobre la partición, consulte [el tutorial de Hive][apache-hive-tutorial].  
3. **Cargar datos** desde la carpeta de origen /tutorials/twitter/data. El gran conjunto de datos de los tweets en formato JSON anidado se ha transformado en una estructura de tabla temporal de Hive.
3. **Descartar la tabla de tweets** en caso de que ya exista.
4. **Crear la tabla de tweets**. Antes de poder consultar el conjunto de datos de tweets mediante Hive, hay que ejecutar otro proceso de ETL. Este proceso ETL define un esquema de tabla más detallado para los datos que hemos almacenado en la tabla "twitter_raw".  
5. **Insertar tabla de sobrescritura**. Este script de Hive complejo iniciará un gran conjunto de trabajos de MapReduce realizado por el clúster de Hadoop.  Dependiendo del conjunto de datos y del tamaño del clúster, este puede tardar unos 10 minutos.
6. **Insertar directorio de sobrescritura**. Ejecute una consulta y ponga el conjunto de datos en un archivo. Esta consulta devolverá una lista de los usuarios de Twitter que envían más tweets que contienen la palabra "Azure".

**Para crear un script de Hive y actualizarlo en Azure**

1. Abra Windows PowerShell ISE.
2. Copie el siguiente script en el panel de scripts:

		Write-Host "Define variables ..." -ForegroundColor Green
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<BlobContainerName>"
		
		$sourceDataPath = "/tutorials/twitter/data"
		$outputPath = "/tutorials/twitter/output"
		
		$hqlScriptFile = "tutorials/twitter/twitter.hql"
		
		$hqlStatements = @"
		set hive.exec.dynamic.partition = true;
		set hive.exec.dynamic.partition.mode = nonstrict;
		
		DROP TABLE tweets_raw;
		CREATE TABLE tweets_raw (
		    json_response STRING
		) 
		PARTITIONED BY (filesequence INT);
		
		LOAD DATA INPATH '$sourceDataPath'
		INTO TABLE tweets_raw
		PARTITION (filesequence = 1);
		
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
		)
		PARTITIONED BY (filesequence INT);
		
		FROM tweets_raw
		INSERT OVERWRITE TABLE tweets
		PARTITION (filesequence)
		SELECT
		    cast(get_json_object(json_response, '$.id_str') as BIGINT),
		    get_json_object(json_response, '$.created_at'),
			concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
			substr (get_json_object(json_response, '$.created_at'),27,4)),
			substr (get_json_object(json_response, '$.created_at'),27,4),
			case substr (get_json_object(json_response,	'$.created_at'),5,3)
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
			json_response,
			filesequence
		WHERE (length(json_response) > 500);
		
		INSERT OVERWRITE DIRECTORY '$outputPath'
		SELECT name, screen_name, count(1) as cc 
			FROM tweets 
			WHERE text like "%Azure%" 
			GROUP BY name,screen_name 
			ORDER BY cc DESC LIMIT 10;
		"@
		
		Write-Host "Define the connection string ..." -ForegroundColor Green
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		
		Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)
		
		Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		$writeStream.Writeline($hqlStatements)
		
		Write-Host "Write to the destination blob ... " -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$hqlScriptBlob.UploadFromStream($memStream)
		
		Write-Host "Complete!" -ForegroundColor Green


4. Establezca las dos primeras variables en el script	

	<table border="1">
	<tr><th>Variable</th><th>Descripción</th></tr>
	<tr><td>$storageAccountName</td><td>La cuenta de almacenamiento de Azure utilizada como sistema de archivos predeterminado del clúster de HDInsight. Esta es la cuenta de almacenamiento especificada en la provisión. Consulte <a href="#prerequisites">Requisitos previos</a>.</td></tr>
	<tr><td>$containerName</td><td>El contenedor de blobs que se utiliza para el sistema de archivos de clúster de HDInsight predeterminado. Este es el contenedor especificado en la provisión. Consulte <a href="#prerequisites">Requisitos previos</a>.</td></tr>
	<tr><td>$sourceDataPath</td><td>La ubicación de WASB desde donde se leerán los datos de consultas de Hive. No es necesario que cambie esta variable.</td></tr>
	<tr><td>$outputPath</td><td>La ubicación de WASB desde donde se almacenarán los resultados de consultas de Hive. No es necesario que cambie esta variable.</td></tr>
	<tr><td>$hqlScriptFile</td><td>La ubicación y el nombre de archivo del archivo de script de HiveQL.</td></tr>
	</table>

5. Presione **F5** para ejecutar el script. Si encuentra problemas, como solución alternativa, seleccione todas las líneas y, a continuación, presione **F8**.
6. Verá un anuncio de "Complete!" al final de la salida. Los mensajes de error aparecerán en rojo.

Como procedimiento de validación, puede revisar el archivo de salida, /tutorials/twitter/twitter.hql**, en el almacenamiento de blobs de Azure con un explorador de almacenamiento de Azure o Azure PowerShell.  Para obtener un script de ejemplo de PowerShell, consulte [Uso de almacenamiento de blobs con HDInsight][hdinsight-storage-powershell].  


##<a name="process"></a> Procesamiento de datos de Twitter de manera interactiva mediante Hive

Ya ha terminado todo el trabajo de preparación. Ahora, puede invocar el script de Hive y comprobar los resultados.

### Enviar el trabajo de Hive

Use el siguiente script de PowerShell para ejecutar el script de Hive. Tendrá que definir la primera variable.

	Write-Host "Set the variables ... " -ForegroundColor Green
	$clusterName = "<HDInsightClusterName>"
	
	$hqlScriptFile = "/tutorials/twitter/twitter.hql"
	$statusFolder = "/tutorials/twitter/jobstatus"
	
	Write-Host "Invoke Hive ... " -ForegroundColor Green
	Use-AzureHDInsightCluster $clusterName
	$response = Invoke-Hive -file $hqlScriptFile -StatusFolder $statusFolder -OutVariable $outVariable
	
	Write-Host "Display the standard error log ... " -ForegroundColor Green
	$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace '\s*$' -replace '.*\s'
	Get-AzureHDInsightJobOutput -cluster $clusterName -JobId $jobID -StandardError 

### Comprobar los resultados

Use el siguiente script de PowerShell para comprobar la salida del trabajo de Hive. Tendrá que definir las dos primeras variables.

	Write-Host "Set the variables ... " -ForegroundColor Green
	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Download the blob ..." -ForegroundColor Green
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
	
	Write-Host "Display the output ..." -ForegroundColor Green
	cat "./$blob"

> [WACOM.NOTE] La tabla de Hive utiliza \001 como delimitador de campo. El delimitador no es visible en la salida. 

Una vez que los resultados del análisis se hayan colocado en WASB, puede exportar los datos a SQL Server/Base de datos SQL de Azure, exportar los datos a Excel con Power Query, o bien, conectar la aplicación con los datos mediante el uso de Hive ODBC.  Para obtener más información, consulte [Uso de Sqoop con HDInsight][hdinsight-use-sqoop] ,[Análisis de la información de retraso de vuelos usando HDInsight][hdinsight-analyze-flight-delay-data], [Conexión de Excel a HDInsight con Power Query][hdinsight-power-query] y [Conexión de Excel a HDInsight con Microsoft Hive ODBC Driver][hdinsight-hive-odbc].
   

##<a id="cleanup"></a>Limpieza del tutorial

En caso de que desee volver a ejecutar el tutorial, necesitará:

- **Volver a crear el archivo de datos de Tweets**. El trabajo de Hive quitó el archivo de datos de Tweets de origen.  Necesitará generar uno nuevo.  El nombre del archivo es **tutorials/twitter/data/tweets.txt**. Consulte[ Obtención de una fuente de Twitter].(#feed).

##<a id="nextsteps"></a>Pasos siguientes

En este tutorial hemos visto cómo transformar un conjunto de datos JSON no estructurado en una tabla de Hive estructurada para consultar, explorar y analizar datos desde Twitter mediante HDInsight en Azure. Para obtener más información, consulte:

- [Introducción a HDInsight][hdinsight-get-started]
- [Análisis de la información de retraso de vuelos usando HDInsight][hdinsight-analyze-flight-delay-data]
- [Conexión de Excel a HDInsight con Power Query][hdinsight-power-query]
- [Conexión de Excel a HDInsight con Microsoft Hive ODBC Driver][hdinsight-hive-odbc]
- [Uso de Sqoop con HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/en-us/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell
[powershell-script]: http://technet.microsoft.com/en-us/library/ee176961.aspx

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/


<!--HONumber=35_1-->
