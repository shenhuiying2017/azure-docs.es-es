<properties 
	pageTitle="Kernels disponibles para cuadernos de Jupyter con clústeres de HDInsight Spark en Linux | Microsoft Azure" 
	description="Obtenga información sobre los kernels del cuaderno de Jupyter adicionales disponibles con el clúster de HDInsight Spark en Linux." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/06/2016" 
	ms.author="nitinme"/>


# Kernels disponibles para cuadernos de Jupyter con clústeres de Apache Spark en HDInsight Linux

El clúster Apache Spark en HDInsight (Linux) incluye cuadernos de Jupyter que puede usar para probar las aplicaciones. Un kernel es un programa que ejecuta e interpreta el código. Los clústeres Spark en HDInsight proporcionan dos kernels que puede utilizar con el cuaderno de Jupyter. Dichos componentes son:

1. **PySpark** (para aplicaciones escritas con Python)
2. **Spark** (para aplicaciones escritas con Scala)

En este artículo, obtendrá información sobre cómo usar estos kernels y cuáles son los beneficios que obtiene de uso.

**Requisitos previos:**

Debe tener lo siguiente:

- Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster Apache Spark en HDInsight Linux. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

## ¿Cómo puedo usar los kernels? 

1. Desde el [Portal de Azure](https://portal.azure.com/), en el panel de inicio, haga clic en el icono del clúster Spark (si lo ancló al panel de inicio). También puede navegar hasta el clúster en **Examinar todo** > **Clústeres de HDInsight**.   

2. En la hoja del clúster Spark, haga clic en **Vínculos rápidos** y, luego, en la hoja **Panel de clúster**, haga clic en **Jupyter Notebook**. Cuando se le pida, escriba las credenciales del clúster.

	> [AZURE.NOTE] También puede comunicarse con el equipo Jupyter Notebook en el clúster si abre la siguiente dirección URL en el explorador. Reemplace __CLUSTERNAME__ por el nombre del clúster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Cree un nuevo cuaderno con los kernels nuevos. Haga clic en **New** (Nuevo) y, luego, en **Pyspark** o **Spark**. Debe utilizar el kernel de Spark para aplicaciones de Scala y kernel PySpark para aplicaciones de Python.

	![Crear un nuevo cuaderno de Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "Crear un nuevo cuaderno de Jupyter")

3. Esto debería abrir un nuevo cuaderno con el kernel seleccionado.

## ¿Por qué debo usar los kernels de PySpark o Spark?

Utilizar los nuevos kernels aporta un par de beneficios.

1. **Contextos preestablecidos**. Con los kernels de **PySpark** or **Spark** que se proporcionan con los cuadernos de notas de Jupyter, no necesita establecer de forma explícita los contextos de Spark o Hive para poder empezar a trabajar con la aplicación que está desarrollando, ya que están disponibles de forma predeterminada. Estos contextos son:

	* **sc**: para el contexto Spark
	* **sqlContext**: para el contexto Hive


	Por tanto, no tiene que ejecutar instrucciones como la siguiente para definir los contextos:

		###################################################
		# YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
		###################################################
		sc = SparkContext('yarn-client')
		sqlContext = HiveContext(sc)

	En su lugar, puede utilizar directamente los contextos preestablecidos en la aplicación.
	
2. **Instrucciones mágicas de celda**. El kernel PySpark proporciona algunas "instrucciones mágicas" predefinidas, que son comandos especiales que se pueden llamar con `%%` (por ejemplo, `%%MAGIC` <args>). El comando mágico debe ser la primera palabra de una celda de código y permitir varias líneas de contenido. La palabra mágica debe ser la primera palabra en la celda. Si se agrega algo antes de la palabra mágica, incluso comentarios, se producirá un error. Para obtener más información sobre instrucciones mágicas, vaya [aquí](http://ipython.readthedocs.org/en/stable/interactive/magics.html).

	La tabla siguiente muestra las diferentes instrucciones mágicas disponibles a través de los kernels.

	| Instrucción mágica | Ejemplo | Descripción |
	|-----------|---------------------------------|--------------|
	| help | `%%help` | Genera una tabla de todas las instrucciones mágicas disponibles con el ejemplo y la descripción |
	| info | `%%info` | Produce información de sesión del punto de conexión actual de Livy. |
	| configurar | `%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} | Configura los parámetros para crear una sesión. La marca force (-f) es obligatoria si ya se ha creado una sesión, que se quitará y se vuelve a crear. Consulte [Livy's POST /sessions Request Body (Cuerpo de la solicitud de sesiones o POST de Livy)](https://github.com/cloudera/livy#request-body) para obtener una lista de parámetros válidos. Los parámetros deben pasarse como una cadena JSON y deben estar en la siguiente línea después de la instrucción mágica, como se muestra en la columna de ejemplo. |
	| sql | `%%sql -o <variable name>`<br> `SHOW TABLES` | Ejecuta una consulta de Hive en el sqlContext. Si se pasa el parámetro `-o`, el resultado de la consulta se conserva en el contexto %%local de Python como trama de datos [Pandas](http://pandas.pydata.org/). |
	| local | `%%local`<br>`a=1` | Todo el código de las líneas siguientes se ejecutará localmente. El código debe ser un código de Python válido. |
	| logs | `%%logs` | Genera los registros de la sesión actual de Livy. |
	| delete | `%%delete -f -s <session number>` | Elimina una sesión específica del punto de conexión actual de Livy. Tenga en cuenta que no se puede eliminar la sesión iniciada en el propio kernel. |
	| cleanup | `%%cleanup -f` | Elimina todas las sesiones del punto de conexión actual de Livy, incluida la sesión de este cuaderno. La marca force -f es obligatoria. |

3. **Visualización automática**. El kernel **Pyspark** visualiza automáticamente el resultado de las consultas de Hive y SQL. Tiene la posibilidad de elegir entre diferentes tipos de visualizaciones como tabla, circular, línea, área, barra.

## Parámetros compatibles con la instrucción mágica %%sql

La instrucción mágica %%sql es compatible con distintos parámetros que se pueden usar para controlar el tipo de resultado que se obtiene al ejecutar consultas. En la tabla siguiente se muestra el resultado.

| Parámetro | Ejemplo | Descripción |
|-----------|---------------------------------|--------------|
| -o | `-o <VARIABLE NAME>` | Use este parámetro para conservar el resultado de la consulta en el contexto %%local de Python como trama de datos [Pandas](http://pandas.pydata.org/). El nombre de la variable de la trama de datos es el nombre de variable que especifique. |
| -q | `-q` | Úselo para desactivar visualizaciones de la celda. Si no quiere visualizar de forma automática el contenido de una celda y simplemente quiere capturarlo como una trama de datos, use `-q -o <VARIABLE>`. Si quiere desactivar visualizaciones sin capturar los resultados (por ejemplo, para ejecutar una consulta de SQL con efectos secundarios, como una instrucción `CREATE TABLE`), simplemente use `-q` sin especificar un argumento `-o`. |
| -m | `-m <METHOD>` | Donde **METHOD** es **take** o **sample** (el valor predeterminado es **take**). Si el método es **take**, el kernel toma elementos de la parte superior del conjunto de datos de resultados especificado por MAXROWS (se describe más adelante en esta tabla). Si el método es **sample**, el kernel tomará como muestra de forma aleatoria elementos del conjunto de datos según el parámetro `-r`, que se describe a continuación en esta tabla. |
| -r | `-r <FRACTION>` | Aquí **FRACTION** es un número de punto flotante entre 0,0 y 1,0. Si el método de ejemplo de la consulta de SQL es `sample`, el kernel toma como muestra de forma aleatoria la fracción especificada de los elementos del conjunto de resultados; por ejemplo, si ejecuta una consulta de SQL con los argumentos `-m sample -r 0.01`, se tomará como muestra de forma aleatoria el 1 % de las filas de resultados. |
| -n | `-n <MAXROWS>` | **MAXROWS** es un valor entero. El kernel limitará el número de filas de resultados a **MAXROWS**. Si **MAXROWS** es un número negativo como **-1**, no se limitará el número de filas del conjunto de resultados. |

**Ejemplo:**

	%%sql -q -m sample -r 0.1 -n 500 -o query2 
	SELECT * FROM hivesampletable

La instrucción anterior hace lo siguiente:

* Selecciona todos los registros de **hivesampletable**.
* Dado que se usa -q, desactiva la visualización automática.
* Dado que se usa `-m sample -r 0.1 -n 500`, toma como muestra de forma aleatoria el 10 % de las filas de hivesampletable y limita el tamaño del conjunto de resultados a 500 filas.
* Por último, dado que se ha usado `-o query2`, también guarda el resultado en una trama de datos denominada **query2**.
	

## Consideraciones al utilizar los kernels nuevos

Sea cual sea el kernel usado (PySpark o Spark), dejar los cuadernos en ejecución consumirá los recursos del clúster. Con estos kernels, dado que los contextos están preestablecidos, salir simplemente de los cuadernos no elimina el contexto y, por tanto, los recursos de clúster seguirán estando en uso. Una buena práctica con los kernels PySpark y Spark sería utilizar la opción **Cerrar y detener** del menú **Archivo** del cuaderno. Esto termina el contexto y luego sale del cuaderno.


## Estos son algunos ejemplos:

Al abrir un cuaderno de Jupyter, verá dos carpetas disponibles en el nivel raíz.

* La carpeta **PySpark** tiene cuadernos de ejemplo que usan el nuevo kernel **Python**.
* La carpeta **Scala** tiene cuadernos de ejemplo que usan el kernel **Spark** nuevo.

Puede abrir el cuaderno **00 - [READ ME FIRST] Spark Magic Kernel Features** de la carpeta **PySpark** o **Spark** para obtener información sobre las distintas instrucciones mágicas disponibles. Puede usar los demás cuadernos de ejemplo disponibles en las dos carpetas para aprender a lograr distintos escenarios con cuadernos de Jupyter con clústeres de HDInsight Spark.

## Almacenamiento de los cuadernos

Los cuadernos de Jupyter se guardan en la cuenta de almacenamiento asociada al clúster en la carpeta **/HdiNotebooks**. Es posible acceder a los cuadernos, los archivos de texto y las carpetas que se crean en Jupyter desde WASB. Por ejemplo, si usa Jupyter para crear una carpeta **myfolder** y un cuaderno **myfolder/mynotebook.ipynb**, puede acceder a ese cuaderno en `wasb:///HdiNotebooks/myfolder/mynotebook.ipynb`. También ocurre lo contrario, es decir, si carga un cuaderno directamente en la cuenta de almacenamiento en `/HdiNotebooks/mynotebook1.ipynb`, será visible también desde Jupyter. Los cuadernos permanecen en la cuenta de almacenamiento incluso después de que se elimine el clúster.

La forma de guardar los cuadernos en la cuenta de almacenamiento es compatible con HDFS. Por lo tanto, si se usa SSH en el clúster, puede usar comandos de administración de archivos como los siguientes:

	hdfs dfs -ls /HdiNotebooks             				  # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
	hdfs dfs –copyToLocal /HdiNotebooks    				# Download the contents of the HdiNotebooks folder
	hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter


En caso de que haya problemas para acceder a la cuenta de almacenamiento del clúster, los cuadernos también se guardan en el nodo principal `/var/lib/jupyter`.

## Explorador compatible
Los cuadernos de Jupyter que se ejecutan en clústeres de HDInsight Spark solo son compatibles con Google Chrome.

## Comentarios

El nuevo kernel está en la fase de evolución y se desarrollará con el tiempo. También podría significar que las API podrían cambiar a medida que estos kernels maduran. Agradecemos cualquier comentario que tenga al utilizar estos nuevos kernels. Esto será muy útil para dar forma a la versión final de estos kernels. Puede dejar sus comentarios la sección **Comentarios** al final de este artículo.


## <a name="seealso"></a>Otras referencias


* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### Escenarios

* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Creación y ejecución de aplicaciones

* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (Linux)](hdinsight-apache-spark-livy-rest-interface.md)

### Herramientas y extensiones

* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applications (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones Scala Spark)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)

* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Administración de recursos

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)

* [Track and debug jobs running on an Apache Spark cluster in HDInsight](hdinsight-apache-spark-job-debugging.md) (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)

<!---HONumber=AcomDC_0608_2016-->