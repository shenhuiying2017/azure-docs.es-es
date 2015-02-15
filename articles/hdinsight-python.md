<properties 
	pageTitle="Uso de Python con Hive y Pig en HDInsight de Azure" 
	description="Vea cómo utilizar funciones definidas por el usuario (UDF) de Python desde Hive y Pig en HDInsight de Azure." 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>

#Uso de Python con Hive y Pig en HDInsight

Hive y Pig resultan excelentes para trabajar con datos en HDInsight, pero en ocasiones se necesita un lenguaje con una finalidad más general. Tanto Hive como Pig le permiten crear funciones definidas por el usuario (UDF) mediante diversos lenguajes de programación. En este artículo, aprenderá a usar una UDF de Python desde Hive y Pig.

> [AZURE.NOTE] Los pasos de este artículo se aplican a las versiones de clúster de HDInsight 2.1, 3.0 y 3.1.

##Tabla de contenido

* [Python en HDInsight](#python)
*   [Hive y Python](#hivepython)
*   [Pig y Python](#pigpython)
* [Ejecución de los ejemplos](#running)
* [Solución de problemas](#troubleshooting)
* [Pasos siguientes](#next)

##<a name="python"></a>Python en HDInsight

Python2.7 se instala de forma predeterminada en los clústeres de HDInsight 3.0. La instalación se puede encontrar en la carpeta D:\Python. Hive se puede usar con esta versión de Python para el procesamiento por secuencias (los datos se pasan entre Hive y Python mediante STDOUT/STDIN).

HDInsight incluye también Jython, que es una implementación de Python escrita en Java. Pig comprende cómo hablar con Jython sin tener que recurrir a la transmisión por secuencias, de modo que es preferible cuando se usa Pig.

###<a name="hivepython"></a>Hive y Python

Python se puede usar como UDF desde Hive a través de la instrucción **TRANSFORM** de HiveQL. Por ejemplo, el siguiente HiveQL invoca un script de Python almacenado en el archivo **streaming.py**.

	add file wasb:///streaming.py;
	
	SELECT TRANSFORM (clientid, devicemake, devicemodel)
	  USING 'D:\Python27\python.exe streaming.py' AS
	  (clientid string, phoneLable string, phoneHash string)
	FROM hivesampletable
	ORDER BY clientid LIMIT 50;

A continuación se muestra lo que hace este ejemplo:

1. La instrucción **add file** al comienzo del archivo agrega el archivo **streaming.py** a la caché distribuida, de modo que todos los nodos del clúster pueden acceder a él.

2. La instrucción  **SELECT TRANSFORM ... USING 'D:\Python27\python.exe streaming.py'** selecciona datos de **hivesampletable** y pasa clientid, devicemake y devicemodel al script **streaming.py**.

	> [AZURE.NOTE] La cláusula **USING** especifica la ruta de acceso completa a python.exe, dado que no está en la ruta.

3. La cláusula **AS** describe los campos devueltos por **streaming.py**.


<a name="streamingpy"></a>
Este es el archivo **streaming.py** usado en el ejemplo de HiveQL.

	import sys
	import string
	import hashlib
	
	while True:
	  line = sys.stdin.readline()
	  if not line:
	    break
	
	  line = string.strip(line, "\n ")
	  clientid, devicemake, devicemodel = string.split(line, "\t")
	  phone_label = devicemake + ' ' + devicemodel
	  print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])

Dado que usamos la transmisión por secuencias, este script debe hacer lo siguiente:

1. Leer datos de STDIN. Para ello se usa `sys.stdin.readline()` en este ejemplo.

2. El carácter de nueva línea final se elimina con `string.strip(line, "\n ")`, dado que solo queremos los datos de texto y no el final del indicador de línea.

2. Al realizar el procesamiento por secuencias, una sola línea contiene todos los valores con un carácter de tabulación entre cada uno. Por tanto `string.split(line, "\t")` se puede usar para dividir la entrada en cada tabulación y que solo se devuelvan los campos.

3. Cuando el procesamiento haya finalizado, la salida se debe escribir en STDOUT como una sola línea, con una tabulación entre cada campo. Para ello se usa `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.

4. Todo esto tiene lugar dentro de un bucle `while`, que se repetirá hasta que no se lea ninguna `line`, momento en el cual `break` sale del bucle y termina el script.

Aparte de eso, el script simplemente concatena los valores de entrada de `devicemake` y `devicemodel` y calcula un hash del valor concatenado. Aunque simple, describe los aspectos básicos de cómo se invoca cualquier script de Python desde la función should de Hive: repetir, leer entradas hasta que no haya más, separar cada línea de entrada en las tabulaciones, procesar, escribir una sola línea de salida delimitada por tabuladores.

Consulte [Ejecución de los ejemplos](#running) para obtener información sobre cómo ejecutar este ejemplo en el clúster de HDInsight.

###<a name="pigpython"></a>Pig y Python

Un script de Python se puede usar como UDF desde Pig a través de la instrucción **GENERATE**. Por ejemplo, el siguiente ejemplo usa un script de Python almacenado en el archivo **jython.py**.

	Register 'wasb:///jython.py' using jython as myfuncs;
    LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

A continuación se muestra cómo funciona este ejemplo:

1. Registra el archivo que contiene el script de Python (**jython.py**) mediante **Jython** y lo expone a Pig como **myfuncs**. Jython es una implementación de Python en Java, y se ejecuta en la misma máquina virtual Java que Pig. Esto nos permite tratar el script de Python como una llamada a función tradicional frente al enfoque de transmisión por secuencias usado con Hive.

2. La siguiente línea carga el archivo de datos de ejemplo **sample.log** en **LOGS**. Como este archivo de registro no tiene un esquema coherente, también define cada registro (en este caso **LINE**) como **chararray**. Chararray es básicamente una cadena.

3. La tercera línea filtra los valores nulos y almacena el resultado de la operación en **LOG**.

4. A continuación, recorre en iteración los registros de **LOG** y usa **GENERATE** para invocar el método **create_structure** contenido en el script **jython.py** cargado como **myfuncs**.  **LINE** se usa para pasar el registro actual a la función.

5. Por último, los resultados se vuelcan en STDOUT con el comando **DUMP**. Esta acción se realiza para mostrar inmediatamente los resultados una vez que la operación ha finalizado; en un script real los datos se almacenarían (**STORE**) en un nuevo archivo.

<a name="jythonpy"></a>
Este es el archivo **jython.py** usado en el ejemplo de Pig:

	@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
	def create_structure(input):
	  if (input.startswith('java.lang.Exception')):
	    input = input[21:len(input)] + ' - java.lang.Exception'
	  date, time, classname, level, detail = input.split(' ', 4)
	  return date, time, classname, level, detail

Recuerde que anteriormente hemos definido la entrada **LINE** como chararray porque no hay un esquema coherente para la entrada. Lo que **jython.py** hace es transformar los datos en un esquema coherente para la salida. Funciona de la siguiente manera:

1. La instrucción **@outputSchema** define el formato de los datos que se devolverán a Pig. En este caso es un **contenedor de datos**, que es un tipo de datos de Pig. El contenedor contiene los siguientes campos, los cuales son todos chararray (cadenas):

	* date: la fecha en la que se creó la entrada del registro
	* time: la hora a la que se creó la entrada del registro
	* classname: el nombre de la clase para la que se creó la entrada
	* level: el nivel de registro
	* detail: los detalles de la entrada del registro

2. A continuación, **def create_structure(input)** define la función a la que Pig pasará elementos de línea.

3. Los datos de ejemplo, **sample.log**, se ajustan principalmente al esquema de date, time, classname, level y detail que queremos devolver. Pero también contiene unas cuantas líneas que comienzan por la cadena '*java.lang.Exception*' y que han de modificarse para que coincidan con el esquema. La instrucción **if** las busca, y luego modifica los datos de entrada para mover la cadena '*java.lang.Exception*' al final, de forma que pone los datos en línea con nuestro esquema de salida esperado.

4. A continuación, se usa el comando **split** para dividir los datos en los primeros cuatro caracteres de espacio. El resultado son cinco valores, que se asignan a **date**, **time**, **classname**, **level** y **detail**.

5. Finalmente, los valores se devuelven a Pig.

Es entonces cuando tendremos un esquema coherente tal y como se define en la instrucción **@outputSchema**.

Consulte [Ejecución de los ejemplos](#running) para obtener información sobre cómo ejecutar este ejemplo en el clúster de HDInsight.

##<a name="running"></a>Ejecución de los ejemplos

En los pasos que se indican a continuación se usa Microsoft Azure PowerShell. Si aún no está instalado y configurado en su máquina de desarrollo, consulte [Instalación y configuración de Azure PowerShell](http://azure.microsoft.com/es-es/documentation/articles/install-configure-powershell/) antes de realizar los siguientes pasos.


1. Mediante los ejemplos de Python [streaming.py](#streamingpy) y [jython.py](#jythonpy), cree copias locales de los archivos en su máquina de desarrollo.

2. Use  el siguiente script de PowerShell para cargar los archivos **streaming.py** y **jython.py** en el servidor. Sustituya el nombre del clúster de HDInsight de Azure y la ruta de acceso a los archivos **streaming.py** y **jython.py** en las tres primeras líneas del script.

		$clusterName = YourHDIClusterName
		$pathToStreamingFile = "C:\path\to\streaming.py"
		$pathToJythonFile = "C:\path\to\jython.py"

		$hdiStore = get-azurehdinsightcluster -name $clusterName
		$storageAccountName = $hdiStore.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
		$storageAccountKey = $hdiStore.defaultstorageaccount.storageaccountkey
		$defaultContainer = $hdiStore.DefaultStorageAccount.StorageContainerName
		
		$destContext = new-azurestoragecontext -storageaccountname $storageAccountName -storageaccountkey $storageAccountKey
		set-azurestorageblobcontent -file $pathToStreamingFile -Container $defaultContainer -Blob "streaming.py" -context $destContext
		set-azurestorageblobcontent -file $pathToJythonFile -Container $defaultContainer -Blob "jython.py" -context $destContext

	Este script recupera información del clúster de HDInsight, luego extrae la cuenta y la clave de la cuenta de almacenamiento predeterminada y seguidamente carga los archivos en la raíz del contenedor.

	> [AZURE.NOTE] En el documento [Carga de datos para trabajos de Hadoop en HDInsigh](/es-es/documentation/articles/hdinsight-upload-data/) se pueden encontrar otros métodos para cargar los scripts.

###Uso del panel de Hive (solo ejemplo de Hive)

1. Después de cargar el archivo, abra un explorador y vaya a https://YourClusterName.azurehdinsight.net/. Cuando se le pidan las credenciales, escriba el nombre de usuario y la contraseña de administración del clúster.

	> [AZURE.NOTE] También puede usar el vínculo **Administrar clúster** de la parte inferior del **Panel** de HDInsight en el Portal de administración de Azure para iniciar el panel de Hive.

2. Mediante el **Editor de Hive**, sustituya la línea `select * from hivesampletable` por el siguiente HiveQL.

		add file wasb:///streaming.py;
		SELECT TRANSFORM (clientid, devicemake, devicemodel)
		  USING 'D:\Python27\python.exe streaming.py' AS
		  (clientid string, phoneLable string, phoneHash string)
		FROM hivesampletable
		ORDER BY clientid LIMIT 50;

3. Haga clic en el botón **Enviar** para enviar el trabajo. En función de la versión del clúster de HDInsight, puede ser redirigido a la página Detalles del trabajo. En caso contrario, seleccione **Ver detalles** en el área **Sesión de trabajo** en la parte inferior de la página.

4. La página **Detalles del trabajo** se actualizará hasta que se complete el trabajo. A continuación se mostrará información sobre el trabajo, así como la salida.

###Uso de PowerShell (ejemplos de Hive y Pig)

Después de cargar los archivos, use los siguientes scripts de PowerShell para iniciar los trabajos. Cuando finalice el trabajo, la salida se debe escribir en la consola de PowerShell.

**Para ejecutar el trabajo de Hive,siga estos pasos:**
    
    # Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

	$HiveQuery = "add file wasb:///streaming.py;" +
	             "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
	               "USING 'D:\Python27\python.exe streaming.py' AS " +
	               "(clientid string, phoneLable string, phoneHash string) " +
	             "FROM hivesampletable " +
	             "ORDER BY clientid LIMIT 50;"
	
	$jobDefinition = New-AzureHDInsightHiveJobDefinition -Query $HiveQuery -StatusFolder '/hivepython'
	
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
	Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

La salida del trabajo de **Hive** debe parecerse a la siguiente:

	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9

**Para ejecutar el trabajo de Pig, siga estos pasos:**

	# Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

	$PigQuery = "Register wasb:///jython.py using jython as myfuncs;" +
	            "LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);" +
	            "LOG = FILTER LOGS by LINE is not null;" +
	            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
	            "DUMP DETAILS;"
	
	$jobDefinition = New-AzureHDInsightPigJobDefinition -Query $PigQuery -StatusFolder '/pigpython'
	
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
	Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

La salida del trabajo de **Pig** debe parecerse a la siguiente:

	((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
	((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
	((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
	((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
	((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

##<a name="troubleshooting"></a>Solución de problemas

Ambos scripts de ejemplo de PowerShell usados para ejecutar los ejemplos contienen una línea comentada que mostrará una salida con error para el trabajo. Si no ve la salida esperada del trabajo, quite los comentarios de la siguiente línea y observe si la información de error indica un problema.

	# Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName

La información de error (STDERR) y el resultado del trabajo (STDOUT) también se registran en el contenedor de blobs predeterminado de los clústeres en las siguientes ubicaciones.

<table>
<tr>
<td>Para este trabajo:</td><td>Examine estos archivos en el contenedor de blobs.</td>
</tr>
<tr>
<td>Hive</td><td>/HivePython/stderr<br />/HivePython/stdout</td>
</tr>
<td>Pig</td><td>/PigPython/stderr<br />/PigPython/stdout</td>
</tr>
</table>

##<a name="next"></a>Pasos siguientes

Si necesita cargar módulos de Python que no se proporcionan de forma predeterminada, consulte [Implementación de un módulo en HDInsight de Azure](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) para ver un ejemplo de cómo hacerlo.

Si desea ejecutar trabajos en HDInsight de forma remota sin usar PowerShell, consulte [Uso de HDInsight de Azure desde Linux](http://blogs.msdn.com/b/benjguin/archive/2014/02/18/how-to-use-hdinsight-from-linux.aspx) para ver un ejemplo del uso de Python para ejecutar trabajos a través de la API REST de WebHCat.

<!--HONumber=42-->
