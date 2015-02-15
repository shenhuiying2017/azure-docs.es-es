<properties 
	pageTitle="Introducción al emulador de HDInsight | Azure" 
	description="Aprenda a usar el emulador de HDInsight para Azure." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	author="nitinme" 
	authors="nitinme" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/06/2015" 
	ms.author="nitinme"/>

# Introducción al emulador de HDInsight 

Este tutorial es una introducción a los clústeres de Hadoop en el emulador de Microsoft HDInsight para Azure (anteriormente HDInsight Server Developer Preview). El emulador de HDInsight viene con los mismos componentes del ecosistema Hadoop como HDInsight de Azure. Para conocer los detalles, incluida la información sobre las versiones implementadas, consulte [¿Qué versión de Hadoop tiene HDInsight de Azure?][hdinsight-versions].

>[AZURE.NOTE] El emulador de HDInsight solo incluye un clúster de Hadoop. No incluye HBase.

El emulador de HDInsight proporciona un entorno de desarrollo local para HDInsight de Azure. Si ya conoce Hadoop, puede comenzar con el emulador usando HDFS. En HDInsight, el sistema de archivos predeterminado es el almacenamiento de blobs de Azure (WASB, alias Azure Storage - Blobs). Por lo que, finalmente, querrá desarrollar sus trabajos con WASB. Para usar WASB con el emulador de HDInsight, debe realizar cambios a la configuración del emulador de HDInsight. 

> [AZURE.NOTE] El emulador de HDInsight solo puede usar una implementación de un nodo. 


**Requisitos previos**	
Antes de empezar este tutorial, debe contar con lo siguiente:

- El emulador de HDInsight requiere una versión de Windows de 64 bits. Se debe cumplir uno de los siguientes requisitos:

	- Windows 7 Service Pack 1
	- Windows Server 2008 R2 Service Pack1
	- Windows 8 
	- Windows Server 2012

- Instale y configure Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure]. 

## Apartados de este tutorial

* [Instalación del emulador de HDInsight](#install)
* [Ejecución de la muestra de recuento de palabras](#runwordcount)
* [Ejecución de los ejemplos de introducción](#rungetstartedsamples)
* [Conexión con el almacenamiento de blobs de Azure](#blobstorage)
* [Ejecución de HDInsight PowerShell](#powershell)
* [Eliminación del emulador de HDInsight](#remove)
* [Pasos siguientes](#nextsteps)

##<a name="install"></a>Instalación del emulador de HDInsight

El emulador de Microsoft HDInsight se puede instalar a través del instalador de plataforma web de Microsoft.  

> [AZURE.NOTE] El emulador de HDInsight solo es compatible actualmente con un sistema operativo en idioma inglés. Si tiene instalada una versión anterior del emulador, debe desinstalar los dos componentes siguientes desde Panel de control/Programas y características antes de instalar la última versión del emulador.
><ul>
<li>Emulador de Microsoft HDInsight para Microsoft Azure o HDInsight Developer Preview, el que esté instalado.</li>
<li>Hortonworks Data Platform</li>
</ul>


**Para instalar el emulador de HDInsight**

1. Abra Internet Explorer y diríjase a la [página de instalación del emulador de Microsoft HDInsight para Azure][hdinsight-emulator-install].
2. Haga clic en **Instalar ahora**. 
3. Haga clic en **Ejecutar** cuando se le pregunte por la instalación de HDINSIGHT.exe en la parte inferior de la página. 
4. Haga clic en el botón **Sí** en la ventana **Control de cuentas de usuario** que aparece para completar la instalación. Verá el cuadro de diálogo Instalador de plataforma web.
6. Haga clic en **Instalar** en la parte inferior de la página.
7. Haga clic en **Acepto** para aceptar los términos de la licencia.
8. Verifique que el Instalador de plataforma web muestre **Los siguientes productos se instalaron correctamente** y, a continuación, haga clic en **Finalizar**.
9. Haga clic en **Salir** para cerrar la ventana del Instalador de plataforma web.

**Para comprobar la instalación del emulador de HDInsight**
	
La instalación debería haber instalado tres iconos en el escritorio. Los tres iconos están vinculados como se muestra a continuación: 
	
- **Línea de comandos de Hadoop**: El símbolo del sistema de Hadoop desde el que se ejecutan trabajos de MapReduce, Pig y Hive en el emulador de HDInsight.

- **Estado del nodo de nombre de Hadoop**: El NameNode mantiene el directorio de árbol para todos los archivos en HDFS. Realiza también un seguimiento de dónde se mantienen los datos de todos los archivos en un clúster de Hadoop. Los clientes se comunican con el NameNode para resolver los nodos de datos de todos los archivos que están almacenados.
	
- **Estado de Hadoop Yarn**: El seguimiento de trabajo que asigna tareas de MapReduce a los nodos en un clúster.

La instalación debería haber instalado también varios servicios locales. La siguiente es una captura de pantalla de la ventana de servicios:

![HDI.Emulator.Services][image-hdi-emulator-services]

De manera predeterminada, los servicios relacionados con el emulador de HDInsight no se inician. Para iniciar los servicios, desde la línea de comandos de Hadoop, ejecute **start\_local\_hdp_services.cmd** en <system drive\>\hdp. Para iniciar automáticamente los servicios después de reiniciar el equipo, ejecute **set-onebox-autostart.cmd**.  

Para obtener información acerca de los problemas conocidos con la instalación y ejecución de HDInsight Server, consulte las [Notas de la versión del emulador de HDInsight][hdinsight-emulator-release-notes]. El registro de instalación se encuentra en **C:\HadoopFeaturePackSetup\HadoopFeaturePackSetupTools\gettingStarted.winpkg.install.log**.


##<a name="runwordcount"></a>Ejecución de un trabajo de MapReduce de recuento de palabras

Ahora que el emulador de HDInsight está configurado en su estación de trabajo,  puede ejecutar un trabajo de MapReduce para probar la instalación. Primero, cargará algunos archivos de datos en HDFS y, posteriormente, ejecutará un trabajo de recuento de palabras de MapReduce para contar la frecuencia de palabras específicas de esos archivos. 

El programa de recuento de palabras de MapReduce se empaquetó en  *hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar*.  El archivo jar está ubicado en la carpeta  *C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce*.

El trabajo de MapReduce para contar palabras adopta dos argumentos:

- Una carpeta de entrada. Utilizará *hdfs://localhost/user/HDIUser* como la carpeta de entrada.
- Una carpeta de salida. Utilizará *hdfs://localhost/user/HDIUser/WordCount_Output* como el directorio de salida. La carpeta de salida no puede estar en una carpeta existente, de lo contrario, el trabajo de MapReduce producirá un error. Si desea ejecutar el trabajo de MapReduce por segunda vez, debe especificar una carpeta de salida diferente o eliminar la carpeta de salida existente. 

**Para ejecutar el trabajo de MapReduce de recuento de palabras**

1. En el escritorio, haga doble clic en la **línea de comandos de Hadoop** para abrir la ventana de línea de comandos de Hadoop.  La carpeta actual debe ser:

		c:\hdp\hadoop-2.4.0.2.1.3.0-1981

	De lo contrario, ejecute el siguiente comando:

		cd %hadoop_home%

2. Ejecute los siguientes comandos de Hadoop para crear una carpeta de HDFS para el almacenamiento de los archivos de entrada y salida:

		hadoop fs -mkdir /user
		hadoop fs -mkdir /user/HDIUser
	
3. Ejecute el siguiente comando de Hadoop para copiar algunos archivos de texto locales en HDFS:

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common\*.txt /user/HDIUser

4. Ejecute el siguiente comando para ver en una lista de los archivos de la carpeta /user/HDIUser:

		hadoop fs -ls /user/HDIUser

	Debería ver los siguientes archivos:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -ls /user/HDIUser
		Found 4 items
		-rw-r--r--   1 username hdfs     574261 2014-09-08 12:56 /user/HDIUser/CHANGES.txt
		-rw-r--r--   1 username hdfs      15748 2014-09-08 12:56 /user/HDIUser/LICENSE.txt
		-rw-r--r--   1 username hdfs        103 2014-09-08 12:56 /user/HDIUser/NOTICE.txt
		-rw-r--r--   1 username hdfs       1397 2014-09-08 12:56 /user/HDIUser/README.txt

5. Ejecute el siguiente comando para ejecutar el trabajo de MapReduce de recuento de palabras:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981> hadoop jar C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6. Ejecute el siguiente comando para mostrar en una lista el número de palabras que incluyen "ventanas" desde el archivo de salida:

		hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

	La salida debe ser:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"
		windows 4
		windows.        2
		windows/cygwin. 1

Para obtener más información sobre los comandos de Hadoop, consulte el [manual de comandos de Hadoop][hadoop-commands-manual] (en inglés).

##<a name="rungetstartedsamples"></a> Ejecución de los ejemplos de introducción

La instalación del emulador de HDInsight ofrece algunos ejemplos para que los usuarios inicien el aprendizaje de los servicios basados en Apache Hadoop en Windows. En estos ejemplos se incluyen algunas tareas que suelen ser necesarias cuando se procesa un conjunto de grandes volúmenes de datos. El repaso de los ejemplos le ayudará a familiarizarse con los conceptos asociados al modelo de programación de MapReduce y su ecosistema.

Los ejemplos se organizan en torno al procesamiento de los escenarios de datos del registro de IIS W3C. Se proporciona una herramienta de generación de datos para crear e importar los conjuntos de datos de diferentes tamaños a HDFS o WASB (almacenamiento de blobs de Azure). Consulte [Uso del almacenamiento de blobs de Azure para HDInsight][hdinsight-storage] para obtener más información. Luego, los trabajos de MapReduce, Pig o Hive se pueden ejecutar en las páginas de datos generadas por el script de PowerShell. Tenga en cuenta que los scripts de Pig y Hive constituyen una capa de abstracción sobre MapReduce que, al final, se compilan en programas de MapReduce. Los usuarios pueden ejecutar una serie de trabajos para observar los efectos de usar estas distintas tecnologías y la forma en que el tamaño de los datos afecta a la ejecución de las tareas de procesamiento. 

### En esta sección

- [Los escenarios de datos del registro de IIS w3c](#scenarios)
- [Carga de los datos de ejemplo del registro de w3c](#loaddata)
- [Ejecución de trabajos de MapReduce de Java](#javamapreduce)
- [Ejecución de trabajos de Hive](#hive)
- [Ejecución de trabajos de Pig](#pig)
- [Recompilación de los ejemplos](#rebuild)

###<a name="scenarios"></a>Los escenarios de datos del registro de IIS w3c

El escenario de w3c genera e importa datos de registro de IIS W3C en tres tamaños a HDFS o WASB: 1 MB (pequeño), 500 MB (mediando) y 2 GB (grande). Proporciona tres tipos de trabajos y los implementa en C#, Java, Pig y Hive.

- **totalhits**: Calcula la cantidad total de solicitudes de una página determinada. 
- **avgtime**: Calcula el tiempo promedio utilizado (en segundos) para una solicitud por página. 
- **errors**: Calcula la cantidad de errores por página y por hora para las solicitudes cuyo estado fue 404 o 500. 

Estos ejemplos y su documentación no proporcionan un estudio en profundidad ni una implementación completa de las tecnologías clave de Hadoop. El clúster usado tiene un solo nodo y, por lo tanto, no se puede observar el efecto de agregar más nodos en esta versión. 

###<a name="loaddata"></a>Carga de los datos de ejemplo del registro de w3c

La generación e importación de los datos a HDFS se hace usando el script importdata.ps1 de PowerShell.

**Para importar los datos de ejemplo del registro de w3c:**

1. Abra la línea de comandos de Hadoop desde el escritorio.
2. Cambie el directorio a **C:\hdp\GettingStarted**.
3. Ejecute el siguiente comando para generar e importar datos a HDFS:

		powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

	Si, en cambio, desea cargar datos en WASB, consulte [Conexión con el almacenamiento de blobs de Azure](#blobstorage).

4. Ejecute el siguiente comando desde la línea de comandos de Hadoop para mostrar la lista de los archivos importados en el HDFS:

		hadoop fs -ls -R /w3c

	La salida debe ser similar a la siguiente: 

		C:\hdp\GettingStarted>hadoop fs -ls -R /w3c
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:41 /w3c/input/large
		-rw-r--r--   1 username hdfs  543683503 2014-09-08 15:41 /w3c/input/large/data_w3c_large.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input/medium
		-rw-r--r--   1 username hdfs  272435159 2014-09-08 15:40 /w3c/input/medium/data_w3c_medium.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:39 /w3c/input/small
		-rw-r--r--   1 username hdfs    1058423 2014-09-08 15:39 /w3c/input/small/data_w3c_small.txt

5. Si desea comprobar el contenido del archivo, ejecute el siguiente comando para mostrar uno de los archivos de datos en la ventana de la consola:

		hadoop fs -cat /w3c/input/small/data_w3c_small.txt

Ahora, los archivos de datos se han creado e importado en HDFS. Puede empezar a ejecutar diferentes trabajos de Hadoop.

###<a name="javamapreduce"></a> Ejecución de trabajos de MapReduce de Java

MapReduce es el motor de cálculo básico de Hadoop. De manera predeterminada, está implementado en Java, pero también hay ejemplos que aprovechan el streaming de .NET y Hadoop que usan C#. La sintaxis para ejecutar un trabajo de MapReduce es:

	hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

El archivo jar y los archivos de origen se encuentran en la carpeta C:\Hadoop\GettingStarted\Java.

**Para ejecutar un trabajo de MapReduce y así calcular las visitas de la página web**

1. Abra la línea de comandos de Hadoop.
2. Cambie el directorio a **C:\hdp\GettingStarted**.
3. Ejecute el siguiente comando para quitar el directorio de salida en caso de que exista la carpeta.  El trabajo de MapReduce producirá un error si la carpeta de salida ya existe.

		hadoop fs -rm -r /w3c/output

3. Ejecute el siguiente comando:

		hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

	En la siguiente tabla se describen los elementos del comando:
	<table border="1">
	<tr><td>Parámetro</td><td>Nota:</td></tr>
	<tr><td>w3c_scenarios.jar</td><td>El archivo jar se encuentra en la carpeta C:\hdp\GettingStarted\Java.</td></tr>
	<tr><td>microsoft.hadoop.w3c.TotalHitsForPage</td><td>El tipo se puede sustituir por una de las siguientes opciones: 
	<ul>
	<li>microsoft.hadoop.w3c.AverageTimeTaken</li>
	<li>microsoft.hadoop.w3c.ErrorsByPage</li>
	</ul></td></tr>
	<tr><td>/w3c/input/small/data_w3c_small.txt</td><td>El archivo de entrada se puede sustituir por lo siguiente:
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
	<li>/w3c/input/large/data_w3c_large.txt</li>
	</ul></td></tr>
	<tr><td>/w3c/output</td><td>Este es el nombre de la carpeta de salida.</td></tr>
	</table>

4. Ejecute el siguiente comando para ver el archivo de salida:

		hadoop fs -cat /w3c/output/part-00000

	La salida debe ser similar a:

		c:\Hadoop\GettingStarted\Java>hadoop fs -cat /w3c/output/part-00000
		/Default.aspx   3360
		/Info.aspx      1156
		/UserService    1137

	La página Default.aspx recibe 3360 visitas y así sucesivamente. Intente ejecutar de nuevo los comandos reemplazando los valores tal como se sugiere en la tabla anterior y observe cómo cambia el resultado en función del tipo de trabajo y el tamaño de los datos.

### <a name="hive"></a>Ejecución de trabajos de Hive
Para los analistas con profundos conocimientos de SQL el motor de consulta de Hive les resultará familiar. Proporciona una interfaz similar a SQL y un modelo de datos relacionales para HDFS. Hive usa un lenguaje denominado HiveQL, muy similar a SQL. Hive proporciona una capa de abstracción sobre el marco de MapReduce basado en Java y las consultas de Hive se compilan en MapReduce en tiempo de ejecución.

**Para ejecutar un trabajo de Hive**

1. Abra la línea de comandos de Hadoop.
2. Cambie el directorio a **C:\hdp\GettingStarted**.
3. Ejecute el siguiente comando para quitar la carpeta **/w3c/hive/input** en caso de que exista.  El trabajo de hive producirá un error si la carpeta existe.

		hadoop fs -rmr /w3c/hive/input

4. Ejecute el siguiente comando para crear las carpetas **/w3c/hive/input** y copie los archivos de datos en la carpeta /hive/input:

        hadoop fs -mkdir /w3c/hive
		hadoop fs -mkdir /w3c/hive/input
        
		hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5. Ejecute el siguiente comando para ejecutar el archivo de script **w3ccreate.hql**.  El script crea una tabla de Hive y carga datos en la tabla de Hive:
        
		C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

	La salida debe ser similar a la siguiente:

		Logging initialized using configuration in file:/C:/hdp/hive-0.13.0.2.1.3.0-1981	/conf/hive-log4j.properties
		OK
		Time taken: 1.137 seconds
		OK
		Time taken: 4.403 seconds
		Loading data to table default.w3c
		Moved: 'hdfs://HDINSIGHT02:8020/hive/warehouse/w3c' to trash at: hdfs://HDINSIGHT02:8020/user/<username>/.Trash/Current
		Table default.w3c stats: [numFiles=1, numRows=0, totalSize=1058423, rawDataSize=0]
		OK
		Time taken: 2.881 seconds

6. Ejecute el siguiente comando para ejecutar el archivo de script de HiveQL **w3ctotalhitsbypage.hql**.  

        C:\hdp\hive-0.13.0\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

	En la siguiente tabla se describen los elementos del comando:
	<table border="1">
	<tr><td>Archivo</td><td>Descripción</td></tr>
	<tr><td>C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd</td><td>El script de comando de Hive.</td></tr>
	<tr><td>C:\hdp\GettingStarted\Hive\w3c\w3ctotalhitsbypage.hql</td><td> Puede sustituir el archivo de script de Hive por una de las siguientes opciones:
	<ul>
	<li>C:\hdp\GettingStarted\Hive\w3c\w3caveragetimetaken.hql</li>
	<li>C:\hdp\GettingStarted\Hive\w3c\w3cerrorsbypage.hql</li>
	</ul>
	</td></tr>

	</table>

	El script de HiveQL w3ctotalhitsbypage.hql es:

		SELECT filtered.cs_uri_stem,COUNT(*) 
		FROM (
		  SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
		) filtered
		GROUP BY (filtered.cs_uri_stem);

	El final de la salida debe ser similar a la siguiente:
		
		MapReduce Total cumulative CPU time: 5 seconds 391 msec
		Ended Job = job_1410201800143_0008
		MapReduce Jobs Launched:
		Job 0: Map: 1  Reduce: 1   Cumulative CPU: 5.391 sec   HDFS Read: 1058638 HDFS Write: 53 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 391 msec
		OK
		/Default.aspx   3360
		/Info.aspx      1156
		/UserService    1137
		Time taken: 49.304 seconds, Fetched: 3 row(s)

Tenga en cuenta que como primer paso en cada uno de los trabajos, se creará una tabla y los datos se cargarán en la tabla desde el archivo que se creó anteriormente. Puede examinar el archivo que se creó buscando bajo el nodo /Hive en HDFS con el siguiente comando:

	hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Ejecución de trabajos de Pig

El procesamiento de Pig usa un lenguaje de flujo de datos llamado *Pig Latin*. Las abstracciones de Pig Latin proporcionan estructuras de datos más enriquecidas que MapReduce y realizan para Hadoop lo que SQL realiza para los sistemas RDBMS. 


**Para ejecutar trabajos de pig:**

1. Abra la línea de comandos de Hadoop.
2. Cambie el directorio a la carpeta **C:\hdp\GettingStarted**.
3. Ejecute el siguiente comando para enviar un trabajo de Pig:

		C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

	En la siguiente tabla se muestran los elementos del comando:
	<table border="1">
	<tr><td>Archivo</td><td>Descripción</td></tr>
	<tr><td>C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd</td><td>El script de comando de Pig.</td></tr>
	<tr><td>C:\hdp\GettingStarted\Pig\w3c\TotalHitsForPage.pig</td><td> Puede sustituir el archivo de script de Pig Latin por una de las siguientes opciones:
	<ul>
	<li>C:\hdp\GettingStarted\Pig\w3c\AverageTimeTaken.pig</li>
	<li>C:\hdp\GettingStarted\Pig\w3c\ErrorsByPage.pig</li>
	</ul>
	</td></tr>
	<tr><td>/w3c/input/small/data_w3c_small.txt</td><td> Puede sustituir el parámetro por un archivo más grande:
	
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
	<li>/w3c/input/large/data_w3c_large.txt</li>
	</ul>
	
	</td></tr>
	</table>

	La salida debe ser similar a la siguiente:

		(/Info.aspx,1156)
		(/UserService,1137)
		(/Default.aspx,3360)
		
Tenga en cuenta que debido a que los scripts de Pig se compilan en trabajos de MapReduce y posiblemente en más de uno de tales trabajos, puede que se vean varios trabajos de MapReduce que se ejecuten durante el procesamiento de un trabajo de Pig.


### <a name="rebuild"></a>Recompilación de los ejemplos
Los ejemplos contienen actualmente todos los binarios necesarios, por lo que no se requiere una compilación. Si desea realizar cambios en los ejemplos de Java o .NET, puede volver a compilarlos usando msbuild o el script de PowerShell que se incluye.

**Para recompilar los ejemplos**

1. Abra la línea de comandos de Hadoop.
2. Ejecute el siguiente comando:

		powershell -F buildsamples.ps1


##<a name="blobstorage"></a>Conexión con el almacenamiento de blobs de Azure
El emulador de HDInsight utiliza HDFS como sistema de archivos predeterminado. Sin embargo, HDInsight de Azure usa el almacenamiento de blobs de Azure como sistema de archivos predeterminado. Se puede configurar el emulador de HDInsight para que use el almacenamiento de blobs de Azure Blob en lugar el almacenamiento local. Siga las instrucciones que se ofrecen a continuación para crear un contenedor de almacenamiento en Azure y conectarlo al emulador de HDInsight.

>[AZURE.NOTE] Para obtener más información sobre cómo usa HDInsight el almacenamiento de blobs de Azure, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage].

Antes de comenzar con las instrucciones dadas a continuación, debe haber creado una cuenta de almacenamiento. Para obtener instrucciones, consulte [Creación de una cuenta de almacenamiento][azure-create-storage-account].

**Para crear un contenedor**

1. Inicie sesión en el [Portal de administración][azure-management-portal].
2. Haga clic en **ALMACENAMIENTO** a la izquierda. Debe ver una lista de cuentas de almacenamiento debajo de su suscripción.
3. En la lista, haga clic en la cuenta de almacenamiento donde desea crear el contenedor.
4. Haga clic en **CONTENEDORES** en la parte superior de la página.
5. Haga clic en **AGREGAR** en la parte inferior de la página.
6. Escriba **NOMBRE** y seleccione **ACCESO**. Puede usar cualquiera de los tres niveles de acceso.  El valor predeterminado es **Privado**.
7. Haga clic en **Aceptar** para guardar los cambios. Podrá ver que el contenedor nuevo se muestra en el portal.

Para que pueda tener acceso a una cuenta de almacenamiento de Azure, debe agregar el nombre y la clave de la cuenta al archivo de configuración.

**Para configurar la conexión a una cuenta de almacenamiento de Azure**

1. Abra **C:\hdp\hadoop-2.4.0.2.1.3.0-1981\etc\hadoop\core-site.xml** en el Bloc de notas.
2. Agregue la siguiente etiqueta <property\> junto a las demás etiquetas <property\>:

		<property>
		    <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
		    <value><StorageAccountKey></value>
		</property>

	Debe sustituir <StorageAccountName\> y <StorageAccountKey\> por los valores que coinciden con la información de su cuenta de almacenamiento.

3. Guarde el cambio.  No es necesario reiniciar los servicios de Hadoop.

Use la siguiente sintaxis para tener acceso a la cuenta de almacenamiento:

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

Por ejemplo:

	hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/


##<a name="powershell"></a> Ejecución de HDInsight PowerShell
Algunos de los cmdlets de HDInsight de Azure PowerShell también son compatibles con el emulador de HDInsight. Estos cmdlets incluyen:

- Cmdlets de definición del trabajo de HDInsight
	
	- New-AzureHDInsightSqoopJobDefinition
	- New-AzureHDInsightStreamingMapReduceJobDefinition
	- New-AzureHDInsightPigJobDefinition                                                                                          
	- New-AzureHDInsightHiveJobDefinition                                                                                           
	- New-AzureHDInsightMapReduceJobDefinition
- Start-AzureHDInsightJob
- Get-AzureHDInsightJob
- Wait-AzureHDInsightJob

La siguiente es un ejemplo para enviar un trabajo de Hadoop:

	$creds = Get-Credential (hadoop as username, password can be anything)
	$hdinsightJob = <JobDefinition>
	Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

Recibirá una indicación cuando llame a Get-Credential. Debe usar **hadoop** como nombre de usuario. La contraseña puede ser cualquier cadena. El nombre del clúster siempre es **http://localhost:50111**.

Para obtener más información sobre el envío de trabajos de Hadoop, consulte [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs]. Para obtener más información sobre los cmdlets de HDInsight PowerShell, consulte [Documentación de referencia de los cmdlets de HDInsight][hdinsight-powershell-reference].


##<a name="remove"></a> Eliminación del emulador de HDInsight
En el equipo en el que tiene el emulador instalado, abra el Panel de Control y en **programas**, haga clic en **Desinstalar un programa**. En la lista de programas instalados, haga clic con el botón secundario en **Emulador de Microsoft HDInsight para Azure** y, a continuación, haga clic en **Desinstalar**. 


##<a name="nextsteps"></a> Pasos siguientes
Con este tutorial, tiene instalado un emulador de HDInsight y ha ejecutado algunos trabajos de Hadoop. Para obtener más información, consulte los artículos siguientes:

- [Introducción al uso de HDInsight de Azure][hdinsight-get-started]
- [Desarrollo de programas MapReduce de Java para HDInsight][hdinsight-develop-mapreduce]
- [Desarrollo de programas de MapReduce de streaming de Hadoop C# para HDInsight][hdinsight-develop-deploy-streaming]
- [Notas de la versión del emulador de HDInsight][hdinsight-emulator-release-notes]
- [Foro de MSDN para el análisis de HDInsight](http://social.msdn.microsoft.com/Forums/es-es/hdinsight)



[azure-sdk]: http://azure.microsoft.com/es-es/downloads/
[azure-create-storage-account]: ../storage-create-storage-account/
[azure-management-portal]: https://manage.windowsazure.com/
[netstat-url]: http://technet.microsoft.com/es-es/library/ff961504.aspx

[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/

[hdinsight-emulator-install]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
[hdinsight-emulator-release-notes]: ../hdinsight-emulator-release-notes/

[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/es-es/library/windowsazure/dn479228.aspx
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-develop-deploy-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-versions]: ../hdinsight-component-versioning/

[Powershell-install-configure]: ../install-configure-powershell/

[hadoop-commands-manual]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html

[image-hdi-emulator-services]: ./media/hdinsight-get-started-emulator/HDI.Emulator.Services.png 

<!--HONumber=42-->
