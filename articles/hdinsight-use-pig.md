<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="Use Hadoop Pig in HDInsight" pageTitle="Use Hadoop Pig in HDInsight | Azure" metaKeywords="" description="Learn how to use Pig with HDInsight. Write Pig Latin statements to analyze an application log file, and run queries on the data to generate output for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Pig in HDInsight" authors="jgao" solutions="big data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Uso de Pig con Hadoop en HDInsight

Obtenga más información para ejecutar trabajos de [Apache Pig][] en HDInsight para analizar un archivo de registro log4j de Apache.

**Duración aproximada:** 30 minutos

## En este artículo

-   [Caso de uso de Pig][]
-   [Requisitos previos][]
-   [Descripción de Pig Latin][]
-   [Envío de trabajos de Pig mediante PowerShellPig][]
-   [Envío de trabajos de Pig mediante el SDK .NET de HDInsight][]
-   [Pasos siguientes][]

## <span id="usage"></span></a>Caso de uso de Pig

Las bases de datos son ideales para pequeños conjuntos de datos y consultas de baja latencia. Sin embargo, cuando nos enfrentamos a grandes conjuntos que contienen terabytes de datos, las bases de datos SQL tradicionales no son la solución ideal. Desde siempre, los administradores de bases de datos han tenido que adquirir hardware de grandes dimensiones a medida que la carga de las bases de datos se incrementaba y el rendimiento disminuía.

Por lo general, todas las aplicaciones guardan errores, excepciones y otros problemas codificados en un archivo de registro para que los administradores puedan revisarlos o generar determinadas métricas a partir de los datos de dicho archivo de registro. Estos archivos de registro normalmente adquieren grandes tamaños y contienen una gran cantidad de datos que deben procesarse y extraerse.

Por lo tanto, los archivos de registro son buenos ejemplos de datos de gran tamaño. Trabajar con datos de gran tamaño es difícil si se usan bases de datos relacionales y paquetes de estadísticas/visualización. Debido a las grandes cantidades de datos y el cálculo de los mismos, a menudo es necesario tener software que se ejecute en paralelo en decenas, cientos o incluso miles de servidores para calcular estos datos en un plazo razonable. Hadoop proporciona un marco de MapReduce para escribir aplicaciones que procesan grandes cantidades de datos estructurados y sin estructurar en paralelo en grandes clústeres de máquinas de forma muy fiable y con tolerancia a errores.

[Apache *Pig*][Apache Pig] proporciona un lenguaje de scripting para ejecutar trabajos de *MapReduce* como alternativa a la escritura de código Java. El lenguaje de scripting de Pig se llama *Pig Latin*. Las instrucciones de Pig Latin siguen este flujo general:

-   **Carga**: Datos leídos que se van a manipular desde el sistema de archivos.
-   **Transformación**: Manipulación de los datos.
-   **Volcado o almacenamiento**: Datos de salida en la pantalla o almacenamiento para su procesamiento.

Usar Pig reduce el tiempo necesario para escribir programas de asignador y reductor. Es decir, no es necesario usar Java ni código reutilizable. Además, tiene la flexibilidad de combinar el código Java con Pig. Se pueden escribir muchos algoritmos complejos en menos de cinco líneas de código Pig legible para el ojo humano.

La representación visual de lo que conseguirá en este artículo se muestra en las dos ilustraciones siguientes. Estas ilustraciones muestran un ejemplo representativo del conjunto de datos para ilustrar el flujo y la transformación de los datos a medida que revisa las líneas de código Pig del script. La primera ilustración muestra un ejemplo del archivo log4j:

![Ejemplo del archivo completo][]

La segunda ilustración muestra la transformación de los datos:

![HDI.PIG.Data.Transformation][]

Para obtener más información acerca de Pig Latin, consulte el [manual de referencia de Pig Latin 1][] (en inglés) y el [manual de referencia de Pig Latin 2][] (en inglés).

## <span id="prerequisites"></span></a>Requisitos previos

Tenga en cuenta los siguientes requisitos antes de empezar este artículo:

-   Un clúster de HDInsight de Azure. Para obtener instrucciones, consulte [Introducción a HDInsight de Azure][] o [Aprovisionamiento de clústeres de HDInsight][]. Para completar el tutorial, necesitará los datos siguientes:

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Propiedad del clúster</th>
    <th align="left">Nombre de variable de PowerShell</th>
    <th align="left">Valor</th>
    <th align="left">Descripción</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Nombre del clúster de HDInsight</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">El clúster de HDInsight al que aplicará este tutorial.</td>
    </tr>
    </tbody>
    </table>

-   Instale y configure Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][].

**Descripción del almacenamiento de HDInsight**

HDInsight usa el almacenamiento de blobs de Azure para el almacenamiento de datos. Se llama *WASB* o *Almacenamiento de Azure - Blob*. WASB es la implementación del sistema de archivos distribuido de Hadoop (HDFS) de Microsoft en el almacenamiento de blobs de Azure. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][].

Cuando se aprovisiona un clúster de HDInsight, se designan una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs específico de dicha cuenta como sistema de archivos predeterminado, de la misma forma que en HDFS. Además de esta cuenta de almacenamiento, se pueden agregar más cuentas de almacenamiento desde la misma suscripción de Azure o desde otras diferentes durante el proceso de aprovisionamiento. Para obtener instrucciones acerca de cómo agregar más cuentas de almacenamiento, consulte [Aprovisionamiento de clústeres de HDInsight][]. Para simplificar el script de PowerShell que se utiliza en este tutorial, todos los archivos se almacenan en el contenedor del sistema de archivos predeterminado, ubicado en */tutorials/usepig*. De forma predeterminada, este contenedor tiene el mismo nombre que el del clúster de HDInsight.
La sintaxis de WASB es la siguiente:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] La sintaxis *wasb://* es la única compatible con la versión 3.0 del clúster de HDInsight. La sintaxis *asv://* anterior es compatible con los clústeres de las versiones 2.1 y 1.6 de HDInsight, pero no con los de la versión 3.0, y no será compatible con versiones posteriores.

> [WACOM.NOTE] La ruta WASB es una ruta de acceso virtual. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][].

Para tener acceso a un archivo almacenado en el contenedor del sistema de archivos predeterminado desde HDInsight se puede usar cualquiera de los URI siguientes (use sample.log como ejemplo. Este archivo es el archivo de datos usado en este tutorial):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

Si desea obtener acceso al archivo directamente desde la cuenta de almacenamiento, el nombre de blob del archivo es:

    example/data/sample.log

En este artículo, usará un archivo log4j de muestra incluido con clústeres de HDInsight almacenados en *\\example\\data\\sample.log*. Para obtener información sobre la carga de sus propios archivos de datos, consulte [Carga de datos en HDInsight][].

## <span id="understand"></span></a> Descripción de Pig Latin

En esta sesión repasará algunas de las instrucciones de Pig Latin y los resultados de ejecutar las instrucciones. Y en la siguiente, utilizará PowerShell para ejecutar las instrucciones de Pig.

1.  Carga de datos desde el sistema de archivos y muestra de los resultados

        LOGS = LOAD 'wasb:///example/data/sample.log';
        DUMP LOGS;

    La salida será similar a la siguiente:

        (2012-02-05 19:23:50 SampleClass5 [TRACE] verbose detail for id 313393809)
        (2012-02-05 19:23:50 SampleClass6 [DEBUG] detail for id 536603383)
        (2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail for id 564842645)
        (2012-02-05 19:23:50 SampleClass8 [TRACE] verbose detail for id 1929822199)
        (2012-02-05 19:23:50 SampleClass5 [DEBUG] detail for id 1599724386)
        (2012-02-05 19:23:50 SampleClass0 [INFO] everything normal for id 2047808796)
        (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1774407365)
        (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 2099982986)
        (2012-02-05 19:23:50 SampleClass4 [DEBUG] detail for id 180683124)
        (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1072988373)
        (2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail)
        ...

2.  Revise todas las línea del archivo de datos para buscar una coincidencia en los seis niveles de registro:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

3.  Filtre las filas que no tengan una coincidencia. Por ejemplo, las filas vacías.

        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        DUMP FILTEREDLEVELS;

    La salida será similar a la siguiente:

        (DEBUG)
        (TRACE)
        (TRACE)
        (DEBUG)
        (TRACE)
        (TRACE)
        (DEBUG)
        (TRACE)
        (TRACE)
        (DEBUG)
        (TRACE)
        ...

4.  Agrupe todos los niveles de registro en su fila correspondiente:

        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        DUMP GROUPEDLEVELS;

    La salida será similar a la siguiente:

        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
        (TRACE), ...

5.  En cada grupo, cuente las repeticiones de los niveles de registro. Esta será la frecuencia de cada nivel de registro:

        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        DUMP FREQUENCIES;

    La salida será similar a la siguiente:

        (INFO,3355)
        (WARN,361)
        (DEBUG,15608)
        (ERROR,181)
        (FATAL,37)
        (TRACE,29950)

6.  Ordene las frecuencias en orden descendente:

        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;   

    La salida será similar a la siguiente:

        (TRACE,29950)
        (DEBUG,15608)
        (INFO,3355)
        (WARN,361)
        (ERROR,181)
        (FATAL,37)

## <span id="powershell"></span></a>Envío de trabajos de Pig mediante PowerShellPig

En esta sección se proporcionan las instrucciones para usar los cmdlets de PowerShell. Antes de pasar a esta sección, debe configurar el entorno local y la conexión con Azure. Para obtener información, consulte [Introducción a HDInsight de Azure][] o [Administración de HDInsight con PowerShell][].

**Para ejecutar Pig Latin usando PowerShell**

1.  Abra Windows PowerShell ISE (en la pantalla Inicio de Windows 8, escriba **PowerShell\_ISE** y, a continuación, haga clic en **Windows PowerShell ISE**. Consulte [Start Windows PowerShell on Windows 8 and Windows][]).
2.  En el panel inferior, ejecute el comando siguiente para conectarse a su suscripción de Azure:

        Add-AzureAccount

    Se le pedirá que escriba las credenciales de la cuenta de Azure. Este método de agregar una conexión de suscripción expira y, transcurridas 12 horas, tendrá que volver a ejecutar el cmdlet.

    > [WACOM.NOTE] Si tiene varias suscripciones de Azure y no desea usar la predeterminada, utilice el cmdlet **Select-AzureSubscription** para seleccionar la suscripción actual.

3.  En el panel de scripts, copie y pegue las siguientes líneas:

        $clusterName = "<HDInsightClusterName>" 
        $statusFolder = "/tutorials/usepig/status"

4.  Establezca la variable $clusterName.

5.  Anexe las líneas siguientes en el panel de scripts. Estas líneas definen la cadena de consulta de Pig Latin y crean una definición de trabajo de Pig:

        # Create the Pig job definition
        $0 = '$0';
        $QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                        "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                        "RESULT = order FREQUENCIES by COUNT desc;" +
                        "DUMP RESULT;" 

        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString -StatusFolder $statusFolder 

    También usará el modificador -File para especificar un archivo de script de Pig en HDFS. El modificador -StatusFolder pone el registro de errores estándar y el archivo de salida estándar en la carpeta.

6.  Anexe las siguientes líneas para enviar el trabajo de Pig:

        # Submit the Pig job
        Write-Host "Submit the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

7.  Anexe las siguientes líneas para esperar a que el trabajo de Pig termine:

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8.  Anexe las siguientes líneas para imprimir la salida del trabajo de Pig:

        # Print the standard error and the standard output of the Pig job.
        #Write-Host "Display the standard error log ..." -ForegroundColor Green
        #Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardError

        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    > [WACOM.NOTE] Uno de los cmdlets de Get-AzureHDInsightJobOut está comentado para acortar la salida en la siguiente captura de pantalla.

9.  Presione **F5** para ejecutar el script:
    ![HDI.Pig.PowerShell][]

    El trabajo de Pig calcula las frecuencias de los diferentes tipos de registro.

## <span id="sdk"></span></a>Envío de trabajos de Pig mediante el SDK .NET de HDInsight

A continuación figura una muestra de envío de un trabajo de Pig mediante el SDK .NET de HDInsight. Para obtener instrucciones para crear una aplicación en C# para enviar trabajos de Hadoop, consulte [Envío de trabajos de Hadoop mediante programación][].

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    using System.IO;
    using System.Threading;
    using System.Security.Cryptography.X509Certificates;

    using Microsoft.WindowsAzure.Management.HDInsight;
    using Microsoft.Hadoop.Client;

    namespace SubmitPigJobs
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Set the variables
                string subscriptionID = "<Azure subscription ID>";
                string certFriendlyName = "<certificate friendly name>";
        
                string clusterName = "<HDInsight cluster name>";
                string statusFolderName = @"/tutorials/usepig/status";

                string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                    "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                    "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                    "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                    "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                    "RESULT = order FREQUENCIES by COUNT desc;" +
                    "DUMP RESULT;";

                // Define the Pig job
                PigJobCreateParameters myJobDefinition = new PigJobCreateParameters()
                {
                    Query = queryString,
                    StatusFolder = statusFolderName
                };

                // Get the certificate object from certificate store using the friendly name to identify it
                X509Store store = new X509Store();
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);

                JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

                // Create a hadoop client to connect to HDInsight
                var jobClient = JobSubmissionClientFactory.Connect(creds);

                // Run the MapReduce job
                Console.WriteLine("----- Submit the Pig job ...");
                JobCreationResults mrJobResults = jobClient.CreatePigJob(myJobDefinition);

                // Wait for the job to complete
                Console.WriteLine("----- Wait for the Pig job to complete ...");
                WaitForJobCompletion(mrJobResults, jobClient);

                // Display the error log
                Console.WriteLine("----- The Pig job error log.");
                using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
                {
                    var reader = new StreamReader(stream);
                    Console.WriteLine(reader.ReadToEnd());
                }

                // Display the output log
                Console.WriteLine("----- The Pig job output log.");
                using (Stream stream = jobClient.GetJobOutput(mrJobResults.JobId))
                {
                    var reader = new StreamReader(stream);
                    Console.WriteLine(reader.ReadToEnd());
                }

                Console.WriteLine("----- Press ENTER to continue.");
                Console.ReadLine();
            }

            private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
            {
                JobDetails jobInProgress = client.GetJob(jobResults.JobId);
                while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
                {
                    jobInProgress = client.GetJob(jobInProgress.JobId);
                    Thread.Sleep(TimeSpan.FromSeconds(10));
                }
            }
        }
    }

## <span id="nextsteps"></span></a>Pasos siguientes

Aunque Pig le permite realizar análisis de datos, también hay otros lenguajes incluidos con HDInsight que podrían resultarle interesantes. Hive proporciona un lenguaje de consulta tipo SQL que le permite realizar consultas fácilmente en los datos almacenados en HDInsight, mientras que los trabajos de MapReduce escritos en Java le permiten realizar análisis de datos complejos. Para obtener más información, consulte los temas siguientes:

-   [Introducción a HDInsight de Azure][]
-   [Carga de datos en HDInsight][]
-   [Envío de trabajos de Hadoop mediante programación][]
-   [Uso de Hive con HDInsight][]

  [Apache Pig]: http://pig.apache.org/
  [Caso de uso de Pig]: #usage
  [Requisitos previos]: #prerequisites
  [Descripción de Pig Latin]: #understand
  [Envío de trabajos de Pig mediante PowerShellPig]: #powershell
  [Envío de trabajos de Pig mediante el SDK .NET de HDInsight]: #sdk
  [Pasos siguientes]: #nextsteps
  [Ejemplo del archivo completo]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
  [HDI.PIG.Data.Transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
  [manual de referencia de Pig Latin 1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
  [manual de referencia de Pig Latin 2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
  [Introducción a HDInsight de Azure]: ../hdinsight-get-started/
  [Aprovisionamiento de clústeres de HDInsight]: ../hdinsight-provision-clusters/
  [Instalación y configuración de Azure PowerShell]: ../install-configure-powershell/
  [Uso del almacenamiento de blobs de Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Carga de datos en HDInsight]: ../hdinsight-upload-data/
  [Administración de HDInsight con PowerShell]: ../hdinsight-administer-use-powershell/
  [Start Windows PowerShell on Windows 8 and Windows]: http://technet.microsoft.com/en-us/library/hh847889.aspx
  [HDI.Pig.PowerShell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
  [Envío de trabajos de Hadoop mediante programación]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk
  [Uso de Hive con HDInsight]: ../hdinsight-use-hive/
