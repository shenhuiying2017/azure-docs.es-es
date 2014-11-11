<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started with the HDInsight Emulator | Azure" metaKeywords="hdinsight, Azure hdinsight, hdinsight azure, get started hdinsight, emulator, hdinsight emulator" description="Learn how to use HDInsight Emulator for Azure." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" title="Get started with the HDInsight Emulator" author="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="" />

# Introducción al emulador de HDInsight

Este tutorial es una introducción al uso de clústeres de Hadoop en Microsoft HDInsight para Azure (anteriormente HDInsight Server Developer Preview). El emulador de HDInsight viene con los mismos componentes del ecosistema Hadoop como HDInsight de Azure. Para conocer los detalles, incluida la información sobre las versiones implementadas, consulte [¿Qué versión de Hadoop tiene HDInsight de Azure??][¿Qué versión de Hadoop tiene HDInsight de Azure??].

El emulador de HDInsight proporciona un entorno de desarrollo local para HDInsight de Azure. Si ya conoce Hadoop, puede comenzar con el emulador usando HDFS. No obstante, en HDInsight, el sistema de archivos predeterminado es el almacenamiento de blobs de Azure (WASB, alias Azure Storage - Blobs), por lo que, finalmente, querrá desarrollar sus trabajos con WASB. Puede comenzar a desarrollar contra WASB usando el emulador de almacenamiento de Azure; probablemente solo desee usar un pequeño subconjunto de los datos (no se necesitan cambios en la configuración en el emulador de HDInsight, solo un nombre de cuenta de almacenamiento diferente). Posteriormente, prueba sus trabajos localmente contra el almacenamiento de Azure, nuevamente usando solamente un subconjunto de los datos (requiere un cambio en la configuración del emulador de HDInsight). Finalmente, está listo para cambiar la parte de cálculo de su trabajo HDInsight y ejecutar un trabajo contra los datos de producción.

> [WACOM.NOTE] El emulador de HDInsight solo puede usar una implementación de un nodo.

Para ver un tutorial sobre el uso de HDInsight, consulte [Introducción al uso de HDInsight de Azure][Introducción al uso de HDInsight de Azure].

**Requisitos previos**
 Antes de comenzar este tutorial, debe cumplir los siguientes requisitos previos:

-   El emulador de HDInsight requiere una versión de Windows de 64 bits. Se debe cumplir uno de los siguientes requisitos:

    -   Windows 7 Service Pack 1
    -   Windows Server 2008 R2 Service Pack1
    -   Windows 8
    -   Windows Server 2012
-   Instale y configure Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell].

## Apartados de este tutorial

-   [Instalación del emulador de HDInsight][Instalación del emulador de HDInsight]
-   [Ejecución de la muestra de recuento de palabras][Ejecución de la muestra de recuento de palabras]
-   [Ejecución de los ejemplos de introducción][Ejecución de los ejemplos de introducción]
-   [Conexión con el almacenamiento de blobs de Azure][Conexión con el almacenamiento de blobs de Azure]
-   [Ejecución de HDInsight PowerShell][Ejecución de HDInsight PowerShell]
-   [Pasos siguientes][Pasos siguientes]

## <a name="install"></a>Instalación del emulador de HDInsight

El emulador de Microsoft HDInsight se puede instalar a través del instalador de plataforma web de Microsoft.

> [WACOM.NOTE] El emulador de HDInsight solo es compatible actualmente con un sistema operativo en idioma inglés.

> [WACOM.NOTE] Si ya ha tenido instalado Microsoft HDInsight Developer Preview, deberá primero desinstalar los siguientes dos componentes desde el Panel de control o Programas y características.
>
> -   HDInsight Developer Preview
> -   Hortonworks Data Platform Developer Preview
>
> </p>

**Para instalar el emulador de HDInsight**

1.  Abra Internet Explorer y diríjase a la [página de instalación del emulador de Microsoft HDInsight para Azure][página de instalación del emulador de Microsoft HDInsight para Azure].
2.  Haga clic en **Install Now**.
3.  Haga clic en **Run** cuando se le pregunte por la instalación de HDINSIGHT.exe en la parte inferior de la página.
4.  Haga clic en el botón **Yes** en la ventana **User Account Control** que aparece para completar la instalación. Verá la ventana Instalador de plataforma web 4.6.
5.  Haga clic en **Install** en la parte inferior de la página.
6.  Haga clic en **I Accept** para aceptar los términos de la licencia.
7.  Verifique que el Instalador de plataforma web muestre **the following products were successfully installed** y, a continuación, haga clic en **Finish**.
8.  Haga clic en **Exit** para cerrar la ventana del Instalador de plataforma web 4.6.

    La instalación debería haber instalado tres iconos en el escritorio. Los tres iconos están vinculados como se muestra a continuación:

    -   **Hadoop Command Line**: El símbolo del sistema de Hadoop desde el que se ejecutan trabajos de MapReduce, Pig y Hive en el emulador de HDInsight.

    -   **Hadoop Name Node Status**: El NameNode mantiene el directorio de árbol para todos los archivos en HDFS. Realiza también un seguimiento de dónde se mantienen los datos de todos los archivos en un clúster de Hadoop. Los clientes se comunican con el NameNode para resolver los nodos de datos de todos los archivos que están almacenados.

    -   **Hadoop MapReduce Status**: El seguimiento de trabajo que asigna tareas de MapReduce a los nodos en un clúster.

    La instalación debería haber instalado también varios servicios locales. La siguiente es una captura de pantalla de la ventana de servicios:

    ![HDI.Emulator.Services][HDI.Emulator.Services]

    Para obtener información acerca de los problemas conocidos con la instalación y ejecución de HDInsight Server, consulte las [Notas de la versión del emulador de HDInsight][Notas de la versión del emulador de HDInsight]. El registro de instalación se encuentra en **C:\\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\\gettingStarted.winpkg.install.log**.

## <a name="runwordcount"></a>Ejecución de un trabajo de MapReduce de recuento de palabras

Ahora el emulador de HDInsight está configurado en su estación de trabajo. Puede ejecutar un trabajo de MapReduce para probar la instalación. Primero, cargará algunos archivos de texto en HDFS y, posteriormente, ejecutará un trabajo de MapReduce de recuento de palabras para contar las frecuencias de palabras de esos archivos.

El programa MapReduce de recuento de palabras se empaquetó en *hadoop-examples.jar*. El archivo jar está ubicado en la carpeta *C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT*.

La sintaxis del comando jar es:

    hadoop jar <jar> [mainClass] args...

Utilizará también algunos comandos fs. Para obtener más información sobre los comandos de Hadoop, consulte el [manual de comandos de Hadoop][manual de comandos de Hadoop] (en inglés).

El trabajo de MapReduce de recuento de palabras utiliza dos argumentos: una carpeta de entrada y una carpeta de salida. Usará *hdfs://localhost/user/HDIUser* como carpeta de entrada y *hdfs://localhost/user/HDIUser/WordCount\_Output* como directorio de salida. La carpeta de salida no puede estar en una carpeta existente, de lo contrario, el trabajo de MapReduce producirá un error. Si desea ejecutar el trabajo de MapReduce por segunda vez, debe especificar una carpeta de salida diferente o eliminar la carpeta de salida existente.

**Para ejecutar el trabajo de MapReduce de recuento de palabras**

1.  En el escritorio, haga doble clic en **Hadoop Command Line** para abrir la ventana de línea de comandos de Hadoop. La carpeta actual debe ser:

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>

    De lo contrario, ejecute el siguiente comando:

        cd %hadoop_home%

2.  Ejecute el siguiente comando de Hadoop para crear una carpeta de HDFS para el almacenamiento de los archivos de entrada y salida:

        hadoop fs -mkdir /user/HDIUser

3.  Ejecute el siguiente comando de Hadoop para copiar algunos archivos locales en HDFS:

        hadoop fs -copyFromLocal *.txt /user/HDIUser/

4.  Ejecute el siguiente comando para ver en una lista de los archivos de la carpeta /user/HDIUser:

        hadoop fs -ls /user/HDIUser

    Debería ver los siguientes archivos:

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -ls /user/HDIUser
        Found 8 items
        -rw-r--r--   1 username supergroup      16372 2013-10-30 12:07 /user/HDIUser/CHANGES.branch-1-win.txt
        -rw-r--r--   1 username supergroup     463978 2013-10-30 12:07 /user/HDIUser/CHANGES.txt
        -rw-r--r--   1 username supergroup       6631 2013-10-30 12:07 /user/HDIUser/Jira-Analysis.txt
        -rw-r--r--   1 username supergroup      13610 2013-10-30 12:07 /user/HDIUser/LICENSE.txt
        -rw-r--r--   1 username supergroup       1663 2013-10-30 12:07 /user/HDIUser/Monarch-CHANGES.txt
        -rw-r--r--   1 username supergroup        103 2013-10-30 12:07 /user/HDIUser/NOTICE.txt
        -rw-r--r--   1 username supergroup       2295 2013-10-30 12:07 /user/HDIUser/README.Monarch.txt
        -rw-r--r--   1 username supergroup       1397 2013-10-30 12:07 /user/HDIUser/README.txt

5.  Ejecute el siguiente comando para ejecutar el trabajo de MapReduce de recuento de palabras:

        hadoop jar hadoop-examples.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6.  Ejecute el siguiente comando para mostrar en una lista las palabras que tienen "ventanas" desde el archivo de salida:

        hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

    La salida debe ser:

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -cat /user/HDIUser/WordCount_Output/pa
        rt-r-00000 | findstr "windows"
        windows 12
        windows+java6.  1
        windows.        3

## <a name="rungetstartedsamples"></a> Ejecución de los ejemplos de introducción

La instalación del emulador de HDInsight proporciona algunos ejemplos para que los nuevos usuarios aprendan rápidamente los servicios basados en Apache Hadoop en Windows. Estos ejemplos cubren algunas tareas que generalmente se necesitan cuando se procesa un conjunto de datos grande. El repaso de los ejemplos puede familiarizarlo con los conceptos asociados con el modelo de programación de MapReduce y su ecosistema.

Los ejemplos se organizan en torno a los escenarios de datos del registro de IIS W3C de procesamiento. Se proporciona una herramienta de generación de datos para crear e importar los conjuntos de datos de diferentes tamaños a HDFS o WASB (almacenamiento de blobs de Azure). Consulte [Use Azure Blob storage for HDInsight][Use Azure Blob storage for HDInsight] para obtener más información). Luego, los trabajos de MapReduce, Pig o Hive se pueden ejecutar en las páginas de datos generadas por el script de PowerShell. Tenga en cuenta que los scripts de Pig y Hive usados se compilan ambos para programas de MapReduce. Los usuarios pueden ejecutar una serie de trabajos para que observen, por su cuenta, los efectos de usar estas diferentes tecnologías y los efectos del tamaño de los datos en la ejecución de las tareas de procesamiento.

### En esta sección

-   [Los escenarios de datos del registro de IIS w3c][Los escenarios de datos del registro de IIS w3c]
-   [Carga de los datos de ejemplo del registro de w3c][Carga de los datos de ejemplo del registro de w3c]
-   [Ejecución de trabajos de MapReduce de Java][Ejecución de trabajos de MapReduce de Java]
-   [Ejecución de trabajos de Hive][Ejecución de trabajos de Hive]
-   [Ejecución de trabajos de Pig][Ejecución de trabajos de Pig]
-   [Recompilación de los ejemplos][Recompilación de los ejemplos]

### <a name="scenarios"></a>Los escenarios de datos del registro de IIS w3c

El escenario de w3c genera e importa datos de registro de IIS W3C en tres tamaños a HDFS o WASB: 1 MB, 500 MB y 2 GB. Proporciona tres tipos de trabajos y los implementa en C#, Java, Pig y Hive.

-   **totalhits**: Calcula la cantidad total de solicitudes de una página determinada.
-   **avgtime**: Calcula el tiempo promedio utilizado (en segundos) para una solicitud por página.
-   **errors**: Calcula la cantidad de errores por página y por hora para las solicitudes cuyo estado fue 404 o 500.

Estos ejemplos y su documentación no proporcionan un estudio en profundidad ni una implementación completa de las tecnologías clave de Hadoop. El clúster usado tiene un solo nodo y, por lo tanto, no se puede observar el efecto de agregar más nodos en esta versión.

### <a name="loaddata"></a>Carga de los datos de ejemplo del registro de w3c

La generación e importación de los datos a HDFS se hace usando el script importdata.ps1 de PowerShell.

**Para importar los datos de ejemplo del registro de w3c:**

1.  Abra la línea de comandos de Hadoop desde el escritorio.
2.  Ejecute el siguiente comando para cambiar el directorio a **C:\\Hadoop\\GettingStarted**:

        cd \Hadoop\GettingStarted

3.  Ejecute el siguiente comando para generar e importar datos a HDFS:

        powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

    Si, en cambio, desea cargar datos en WASB, consulte [Conexión con el almacenamiento de blobs de Azure][Conexión con el almacenamiento de blobs de Azure].

4.  Ejecute el siguiente comando desde la línea de comandos de Hadoop para mostrar los archivos importados en el HDFS:

        hadoop fs -lsr /w3c

    La salida debe ser similar a la siguiente:

        c:\Hadoop\GettingStarted\w3c>hadoop fs -lsr /w3c
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input/large
        -rw-r--r--   1 username supergroup  543692369 2013-10-30 13:29 /w3c/input/large/data_w3c_large.txt
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/medium
        -rw-r--r--   1 username supergroup  272394671 2013-10-30 13:28 /w3c/input/medium/data_w3c_medium.txt
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/small
        -rw-r--r--   1 username supergroup    1058328 2013-10-30 13:28 /w3c/input/small/data_w3c_small.txt

5.  Ejecute el siguiente comando para mostrar uno de los archivos de datos en la ventana de la consola:

        hadoop fs -cat /w3c/input/small/data_w3c_small.txt

Ahora, el archivo de datos se ha creado e importado en HDFS. Puede ejecutar diferentes trabajos de Hadoop.

### <a name="javamapreduce"></a> Ejecución de trabajos de MapReduce de Java

MapReduce es el motor de cálculo básico de Hadoop. De manera predeterminada, está implementado en Java, pero también hay ejemplos que aprovechan el streaming de .NET y Hadoop que usan C#. La sintaxis para ejecutar un trabajo de MapReduce es:

    hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

El archivo jar y los archivos de origen se encuentran en la carpeta C:\\Hadoop\\GettingStarted\\Java.

**Para ejecutar un trabajo de MapReduce y así calcular las visitas de la página web**

1.  Abra la línea de comandos de Hadoop.
2.  Ejecute el siguiente comando para cambiar el directorio a **C:\\Hadoop\\GettingStarted**:

        cd \Hadoop\GettingStarted

3.  Ejecute el siguiente comando para quitar el directorio de salida en caso de que exista la carpeta. El trabajo de MapReduce producirá un error si la carpeta de salida ya existe.

        hadoop fs -rmr /w3c/output

4.  Ejecute el siguiente comando:

        hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

    En la siguiente tabla se describen los elementos del comando:

	<table border="1">
	<tr><td>Parámetro</td><td>Nota:</td></tr>
	<tr><td>w3c_scenarios.jar</td><td>El archivo jar está ubicado en la carpeta C:\\Hadoop\\GettingStarted\\Java.</td></tr>
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

5.  Ejecute el siguiente comando para ver el archivo de salida:

		hadoop fs -cat /w3c/output/part-00000

    La salida debe ser similar a:

        c:\Hadoop\GettingStarted\Java>hadoop fs -cat /w3c/output/part-00000
        /Default.aspx   3409
        /Info.aspx      1115
        /UserService    1130

    De esta manera, la página Default.aspx recibe 3409 visitas y así sucesivamente.

### <a name="hive"></a>Ejecución de trabajos de Hive

Para los analistas con profundos conocimientos de SQL el motor de consulta de Hive les será familiar. Proporciona una interfaz similar a SQL y un modelo de datos relacionales para HDFS. Hive usa un lenguaje llamado HiveQL (o HQL), que es un dialecto de SQL.

**Para ejecutar un trabajo de Hive**

1.  Abra la línea de comandos de Hadoop.
2.  Cambie el directorio a la carpeta **C:\\Hadoop\\GettingStarted**.
3.  Ejecute el siguiente comando para quitar la carpeta **/w3c/hive/input** en caso de que exista. El trabajo de hive producirá un error si la carpeta existe.

        hadoop fs -rmr /w3c/hive/input

4.  Ejecute el siguiente comando para crear la carpeta **/w3c/hive/input** y copie el archivo de datos de la estación de trabajo en HDFS:

        hadoop fs -mkdir /w3c/hive/input
        hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5.  Ejecute el siguiente comando para ejecutar el archivo de script **w3ccreate.hql**. El script crea una tabla de Hive y carga datos en la tabla de Hive:

        C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

    El script de HiveQL es:

        DROP TABLE w3c;

        CREATE TABLE w3c(
         logdate string,
         logtime string,
         c_ip string,
         cs_username string,
         s_ip string,
         s_port string,
         cs_method string,
         cs_uri_stem string,
         cs_uri_query string,
         sc_status int,
         sc_bytes int,
         cs_bytes int,
         time_taken int,
         cs_agent string, 
         cs_Referrer string)
        ROW FORMAT delimited
        FIELDS TERMINATED BY ' ';

        LOAD DATA INPATH '${hiveconf:input}' OVERWRITE INTO TABLE w3c;

    La salida debe ser similar a la siguiente:

        c:\Hadoop\GettingStarted>C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccrea    te.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"
        Hive history file=c:\hadoop\hive-0.9.0\logs\history/hive_job_log_username_201310311452_1053491002.txt
        Logging initialized using configuration in file:/C:/Hadoop/hive-0.9.0/conf/hive-log4j.properties
        OK
        Time taken: 0.616 seconds
        OK
        Time taken: 0.139 seconds
        Loading data to table default.w3c
        Moved to trash: hdfs://localhost:8020/apps/hive/warehouse/w3c
        OK
        Time taken: 0.573 seconds

6.  Ejecute el siguiente comando para ejecutar el archivo de script de HiveQL **w3ctotalhitsbypate.hql**.

        C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

    En la siguiente tabla se describen los elementos del comando:

	<table border="1">
	<tr><td>Archivo</td><td>Descripción</td></tr>
	<tr><td>C:\Hadoop\hive-0.9.0\bin\hive.cmd</td><td>El script de comando de Hive.</td></tr>
	<tr><td>C:\Hadoop\GettingStarted\Hive\w3c\w3ctotalhitsbypage.hql</td><td>Puede sustituir el archivo de script de Hive por una de las siguientes opciones:
	<ul>
	<li>C:\Hadoop\GettingStarted\Hive\w3c\w3caveragetimetaken.hql</li>
	<li>C:\Hadoop\GettingStarted\Hive\w3c\w3cerrorsbypage.hql</li>
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

        MapReduce Total cumulative CPU time: 3 seconds 47 msec
        Ended Job = job_201310291309_0006
        MapReduce Jobs Launched:
        Job 0: Map: 1  Reduce: 1   Cumulative CPU: 3.047 sec   HDFS Read: 1058546 HDFS W
        rite: 53 SUCCESS
        Total MapReduce CPU Time Spent: 3 seconds 47 msec
        OK
        /Default.aspx   3409
        /Info.aspx      1115
        /UserService    1130
        Time taken: 34.68 seconds

Tenga en cuenta que como primer paso en cada uno de los trabajos, se creará una tabla y los datos se cargarán en la tabla desde el archivo que se creó anteriormente. Puede examinar el archivo que se creó buscando bajo el nodo /Hive en HDFS con el siguiente comando:

    hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Ejecución de trabajos de Pig

El procesamiento de Pig usa un lenguaje de flujo de datos llamado *Pig Latin*. Las abstracciones de Pig Latin proporcionan estructuras de datos más enriquecidas que MapReduce y realizan para Hadoop lo que SQL realiza para los sistemas RDBMS.

**Para ejecutar trabajos de pig:**

1.  Abra la línea de comandos de Hadoop.
2.  Cambie el directorio a la carpeta C:\\Hadoop\\GettingStarted.
3.  Ejecute el siguiente comando para enviar un trabajo de Pig:

        C:\Hadoop\pig-0.9.3-SNAPSHOT\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

    En la siguiente tabla se muestran los elementos del comando:

	<table border="1">
	<tr><td>Archivo</td><td>Descripción</td></tr>
	<tr><td>C:\Hadoop\pig-0.9.3-SNAPSHOT\bin\pig.cmd</td><td>El script de comando de Pig.</td></tr>
	<tr><td>C:\Hadoop\GettingStarted\Pig\w3c\TotalHitsForPage.pig</td><td>Puede sustituir el archivo de script de Pig Latin por una de las siguientes opciones:
	<ul>
	<li>C:\Hadoop\GettingStarted\Pig\w3c\AverageTimeTaken.pig</li>
	<li>C:\Hadoop\GettingStarted\Pig\w3c\ErrorsByPage.pig</li>
	</ul>
	</td></tr>
	<tr><td>/w3c/input/small/data_w3c_small.txt</td><td>Puede sustituir el parámetro por un archivo más grande:
	
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
	<li>/w3c/input/large/data_w3c_large.txt</li>
	</ul>
	
	</td></tr>
	</table>

    La salida debe ser similar a la siguiente:

        (/Info.aspx,1115)
        (/UserService,1130)
        (/Default.aspx,3409)

Tenga en cuenta que debido a que los scripts de Pig se compilan en trabajos de MapReduce y posiblemente en más de uno de tales trabajos, los usuarios pueden ver varios trabajos de MapReduce que se ejecutan en el curso del procesamiento de un trabajo de Pig.

### <a name="rebuild"></a>Recompilación de los ejemplos

Los ejemplos contienen actualmente todos los binarios necesarios, por lo que no se requiere una compilación. Si desea realizar cambios en los ejemplos de Java o .NET, puede volver a compilarlos usando msbuild o el script de PowerShell que se incluye.

**Para recompilar los ejemplos**

1.  Abra la línea de comandos de Hadoop.
2.  Ejecute el siguiente comando:

        powershell -F buildsamples.ps1






## <a name="blobstorage"></a>Conexión con el almacenamiento de blobs de Azure

HDInsight de Azure usa el almacenamiento de blobs de Azure como sistema de archivos predeterminado. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][Use Azure Blob storage for HDInsight].

Se puede configurar un clúster local en el emulador de HDInsight para usar el almacenamiento de blobs de Azure en vez del almacenamiento local. Esta sección cubre los siguientes temas:

-   conexión con el emulador de almacenamiento
-   conexión con el almacenamiento de blobs de Azure
-   configuración de un almacenamiento de blobs de Azure como el sistema de archivos predeterminado para el emulador de HDInsight

### Conexión con el emulador de almacenamiento

El emulador de almacenamiento de Azure viene con el [SDK de Azure para .NET][SDK de Azure para .NET]. El emulador de almacenamiento no se inicia automáticamente. Debe iniciarlo manualmente. El nombre de la aplicación es *Emulador de almacenamiento de Azure*. Para iniciar/detener los emuladores, haga clic con el botón secundario en el icono azul de Azure en la bandeja del sistema de Windows y, a continuación, haga clic en Show Storage Emulator UI.

> [WACOM.NOTE] Es posible que reciba el siguiente mensaje de error al iniciar el emulador de almacenamiento:

>     The process cannot access the file because it is being used by another process.

> Esto se debe a que uno de los servicios Hive de Hadoop también usa el puerto 10000. Para solucionar el problema, use el siguiente procedimiento:

> 1.  Detenga los dos servicios Hive de Hadoop con services.msc: Apache Hadoop hiveserver y Apache Hadoop Hiveserver2.
> 2.  Inicie el emulador de almacenamiento de blobs.
> 3.  Reinicie los dos servicios Hive de Hadoop.

La sintaxis para tener acceso al emulador de almacenamiento es:

    wasb://<ContainerName>@storageemulator

Por ejemplo:

    hadoop fs -ls wasb://myContainer@storageemulator

> [WACOM.NOTE] Si recibe el siguiente mensaje de error:

>     ls: No FileSystem for scheme: wasb

> Se debe a que todavía usa la versión Developer Preview. Siga las instrucciones que se encuentran en la sección Instalación del emulador de HDInsight de este artículo para desinstalar la versión preliminar del desarrollador y luego vuelva a instalar la aplicación.

### Conexión con el almacenamiento de blobs de Azure

Para obtener instrucciones sobre la creación de una cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento][Creación de una cuenta de almacenamiento].

**Para crear un contenedor**

1.  Inicie sesión en el [Portal de administración][Portal de administración].
2.  Haga clic en **ALMACENAMIENTO** a la izquierda. Debe ver una lista de cuentas de almacenamiento debajo de su suscripción.
3.  En la lista, haga clic en la cuenta de almacenamiento donde desea crear el contenedor.
4.  Haga clic en **CONTAINERS** en la parte superior de la página.
5.  Haga clic en **ADD** en la parte inferior de la página.
6.  Escriba **NAME** y seleccione **ACCESS**. Puede usar cualquiera de los tres niveles de acceso. El valor predeterminado es **Private**.
7.  Haga clic en **OK** para guardar los cambios. Podrá ver que el contenedor nuevo se muestra en el portal.

Para que pueda tener acceso a una cuenta de almacenamiento de Azure, debe agregar el nombre y la clave de la cuenta al archivo de configuración.

**Para configurar la conexión a una cuenta de almacenamiento de Azure**

1.  Abra **C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf\\core-site.xml** en el Bloc de notas.
2.  Agregue la siguiente etiqueta \<property\> junto a las demás etiquetas \<property\>:

        <property>
            <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
            <value><StorageAccountKey></value>
        </property>

    Debe sustituir \<StorageAccountName\> y \<StorageAccountKey\> por los valores que coinciden con la información de su cuenta de almacenamiento.

3.  Guarde el cambio. No es necesario reiniciar los servicios de Hadoop.

Use la siguiente sintaxis para tener acceso a la cuenta de almacenamiento:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

Por ejemplo:

    hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/

### Uso de un contenedor de almacenamiento de blobs de Azure como sistema de archivos predeterminado

También es posible usar un contenedor de almacenamiento de blobs de Azure como sistema de archivos predeterminado, como ocurre con HDInsight de Azure.

**Para configurar el sistema de archivos predeterminado con un contenedor de almacenamiento de blobs de Azure**

1.  Abra **C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf\\core-site.xml** en el Bloc de notas.
2.  Busque la siguiente etiqueta \<property\>:

        <property>
          <name>fs.default.name</name>
          <!-- cluster variant -->
          <value>hdfs://localhost:8020</value>
          <description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
          <final>true</final>
        </property>

3.  Reemplácela por las siguientes dos etiquetas \<property\>:

        <property>
          <name>fs.default.name</name>
          <!-- cluster variant -->
          <!--<value>hdfs://localhost:8020</value>-->
          <value>wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net</value>
          <description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
          <final>true</final>
        </property>

        <property>
          <name>dfs.namenode.rpc-address</name>
          <value>hdfs://localhost:8020</value>
          <description>A base for other temporary directories.</description>
        </property>

    Debe sustituir \<StorageAccountName\> y \<StorageAccountKey\> por los valores que coinciden con la información de su cuenta de almacenamiento.

4.  Guarde los cambios.
5.  Abra la línea de comandos de Hadoop en su escritorio en modo elevado (Ejecutar como administrador)
6.  Ejecute los siguientes comandos para reiniciar los servicios de Hadoop:

        C:\Hadoop\stop-onebox.cmd
        C:\Hadoop\start-onebox.cmd

7.  Ejecute el siguiente comando para probar la conexión con el sistema de archivos predeterminado:

        hadoop fs -ls /

    Los siguientes comandos muestran el contenido en la misma carpeta:

        hadoop fs -ls wasb:///
        hadoop fs -ls wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/
        hadoop fs -ls wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

    Para tener acceso a HDFS, use el siguiente comando:

        hadoop fs -ls hdfs://localhost:8020/

## <a name="powershell"></a> Ejecución de HDInsight PowerShell

Algunos de los cmdlets de HDInsight PowerShell son compatibles con el emulador de HDInsight. Estos cmdlets incluyen:

-   Cmdlets de definición del trabajo de HDInsight

    -   New-AzureHDInsightSqoopJobDefinition
    -   New-AzureHDInsightStreamingMapReduceJobDefinition
    -   New-AzureHDInsightPigJobDefinition
    -   New-AzureHDInsightHiveJobDefinition
    -   New-AzureHDInsightMapReduceJobDefinition
-   Start-AzureHDInsightJob
-   Get-AzureHDInsightJob
-   Wait-AzureHDInsightJob

La siguiente es un ejemplo para enviar un trabajo de Hadoop:

    $creds = Get-Credential (hadoop as username, password can be anything)
    $hdinsightJob = <JobDefinition>
    Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

Recibirá una indicación cuando llame a Get-Credential. Debe usar **hadoop** como nombre de usuario. La contraseña puede ser cualquier cadena. El nombre del clúster siempre es **http://localhost:50111**.

Para obtener más información sobre el envío de trabajos de Hadoop, consulte [Envío de trabajos de Hadoop mediante programación][Envío de trabajos de Hadoop mediante programación]. Para obtener más información sobre los cmdlets de HDInsight PowerShell, consulte [HDInsight cmdlet reference][HDInsight cmdlet reference].

## <a name="nextsteps"></a>Pasos siguientes

Con este tutorial, tiene instalado un emulador de HDInsight y ha ejecutado algunos trabajos de Hadoop. Para obtener más información, consulte los artículos siguientes:

-   [Introducción al uso de HDInsight de Azure][Introducción al uso de HDInsight de Azure]
-   [Desarrollo de programas MapReduce de Java para HDInsight][Desarrollo de programas MapReduce de Java para HDInsight]
-   [Desarrollo de programas de MapReduce de streaming de Hadoop C# para HDInsight][Desarrollo de programas de MapReduce de streaming de Hadoop C# para HDInsight]
-   [Notas de la versión del emulador de HDInsight][Notas de la versión del emulador de HDInsight]
-   [Foro de MSDN para el análisis de HDInsight (en inglés)][Foro de MSDN para el análisis de HDInsight (en inglés)]

  [Introducción al uso de HDInsight de Azure]: ../hdinsight-get-started/
  [Instalación y configuración de Azure PowerShell]: ../install-configure-powershell/
  [Instalación del emulador de HDInsight]: #install
  [Ejecución de la muestra de recuento de palabras]: #runwordcount
  [Ejecución de los ejemplos de introducción]: #rungetstartedsamples
  [Conexión con el almacenamiento de blobs de Azure]: #blobstorage
  [Ejecución de HDInsight PowerShell]: #powershell
  [Pasos siguientes]: #nextsteps
  [página de instalación del emulador de Microsoft HDInsight para Azure]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
  [HDI.Emulator.Services]: ./media/hdinsight-get-started-emulator/HDI.Emulator.Services.png
  [Notas de la versión del emulador de HDInsight]: ../hdinsight-emulator-release-notes/
  [manual de comandos de Hadoop]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html
  [Use Azure Blob storage for HDInsight]: ../hdinsight-use-blob-storage/
  [Los escenarios de datos del registro de IIS w3c]: #scenarios
  [Carga de los datos de ejemplo del registro de w3c]: #loaddata
  [Ejecución de trabajos de MapReduce de Java]: #javamapreduce
  [Ejecución de trabajos de Hive]: #hive
  [Ejecución de trabajos de Pig]: #pig
  [Recompilación de los ejemplos]: #rebuild
  [SDK de Azure para .NET]: http://azure.microsoft.com/es-es/downloads/
  [Creación de una cuenta de almacenamiento]: ../storage-create-storage-account/
  [Portal de administración]: https://manage.windowsazure.com/
  [Envío de trabajos de Hadoop mediante programación]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [HDInsight cmdlet reference]: http://msdn.microsoft.com/es-es/library/windowsazure/dn479228.aspx
  [Desarrollo de programas MapReduce de Java para HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Desarrollo de programas de MapReduce de streaming de Hadoop C# para HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
