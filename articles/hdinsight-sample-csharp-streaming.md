<properties linkid="manage-services-hdinsight-sample-csharp-streaming" urlDisplayName="HDInsight Samples" pageTitle="The HDInsight C# streaming wordcount sample | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to run a sample TBD." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The HDInsight C# streaming wordcount sample" authors="bradsev" />

Muestra WordCount por streaming de C\# para HDInsight
=====================================================

Hadoop proporciona una API de streaming para MapReduce que le permite escribir mapas y reducir funciones en lenguajes distintos de Java. En este tutorial se muestra cómo escribir programas de MapReduce en C \# que usa la interfaz de streaming de Hadoop y cómo ejecutar los programas en HDInsight de Azure con Azure PowerShell.

En el ejemplo, tanto el asignador como el reductor son ejecutables que leen la entrada desde [stdin](http://msdn.microsoft.com/es-es/library/3x292kth(v=vs.110).aspx) (línea a línea) y emiten la salida en [stdout](http://msdn.microsoft.com/es-es/library/3x292kth(v=vs.110).aspx). El programa cuenta todas las palabras del texto.

Cuando se especifica un archivo ejecutable para los **asignadores**, cada tarea del asignador inicia el ejecutable como un proceso aparte al inicializar el asignador. Mientras se ejecuta la tarea del asignador, convierte sus entradas en líneas y alimenta las líneas a [stdin](http://msdn.microsoft.com/es-es/library/3x292kth(v=vs.110).aspx) del proceso. Mientras tanto, el asignador recopila las salidas orientadas por las líneas desde el stdout del proceso y convierte cada línea en un par clave-valor, que se recopila como la salida del asignador. De manera predeterminada, el prefijo de una línea hasta el primer carácter de tabulación es la clave y el resto de la línea (sin incluir el carácter de tabulación) es el valor. Si no hay un carácter de tabulación en la línea, entonces se considera toda la línea como una clave y el valor es nulo.

Cuando se especifica un archivo ejecutable para los **reductores**, cada tarea del reductor inicia el ejecutable como un proceso aparte al inicializar el reductor. Mientras se ejecuta la tarea del reductor, convierte sus pares clave-valor de entrada en líneas y alimenta las líneas a [stdin](http://msdn.microsoft.com/es-es/library/3x292kth(v=vs.110).aspx) del proceso. Mientras tanto, el reductor recopila las salidas orientadas por las líneas desde el [stdout](http://msdn.microsoft.com/es-es/library/3x292kth(v=vs.110).aspx) del proceso y convierte cada línea en un par clave-valor, que se recopila como la salida del reductor. De manera predeterminada, el prefijo de una línea hasta el primer carácter de tabulación es la clave y el resto de la línea (sin incluir el carácter de tabulación) es el valor.

Para obtener más información acerca de la interfaz de streaming de Hadoop, consulte [Hadoop Streaming](http://wiki.apache.org/hadoop/HadoopStreaming).

**Aprenderá a:**

-   Usar Azure PowerShell para ejecutar un programa de streaming en C\# para analizar los datos contenidos en un archivo en HDInsight.
-   Escribir código C\# que usa la interfaz de streaming de Hadoop.

**Requisitos previos**:

-   Debe tener una cuenta de Azure. Para conocer las opciones para obtener una cuenta, consulte la página de la [prueba gratuita de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/).

-   Debe aprovisionar un clúster de HDInsight. Para obtener instrucciones acerca de varias formas de creación de dichos clústeres, consulte [Aprovisionamiento de clústeres de HDInsight](/es-es/manage/services/hdinsight/provision-hdinsight-clusters/).

-   Debe tener instalado Azure PowerShell y haber configurado los clústeres para utilizarlos con su cuenta. Para obtener instrucciones acerca de cómo hacerlo, consulte [Instalación y configuración de Azure PowerShell](/es-es/documentation/articles/install-configure-powershell/).

En este artículo
----------------

En este tema se muestra cómo ejecutar la muestra, presenta el código de Java para el programa de MapReduce, resume lo que ha aprendido y se describen algunos pasos a seguir. Tiene las siguientes secciones.

1.  [Ejecución de la muestra con Azure PowerShell](#run-sample)
2.  [El código de C\# para streaming de Hadoop](#java-code)
3.  [Resumen](#summary)
4.  [Pasos siguientes](#next-steps)

Ejecución de la muestra con Azure PowerShell
--------------------------------------------

**Para ejecutar el trabajo de MapReduce**

1.  Abra **Azure PowerShell**. Para obtener instrucciones acerca de cómo abrir la ventana de la consola de Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](/es-es/documentation/articles/install-configure-powershell/).

2.  Ajuste las dos variables en los comandos siguientes y, a continuación, ejecútelos:

         $subscriptionName = "<SubscriptionName>"       # Nombre de la suscripción a Azure
         $clusterName = "<ClusterName>"             # Nombre del clúster de HDInsight

3.  Ejecute el siguiente comando para definir el trabajo de MapReduce.

         # Crear una definición de trabajo de MapReduce para el trabajo de streaming.
         $streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe" 

    Los parámetros especifican las funciones del asignador y el reductor, además de los archivos de entrada y salida.

4.  Ejecute los siguientes comandos para ejecutar el trabajo de MapReduce, espere a que el trabajo finalice y, a continuación, imprima el error estándar:

         # Ejecutar el trabajo de MapReduce mediante streaming con C#.
         # Esperar a que el trabajo finalice.
         # Imprimir la salida y el archivo de error estándar del trabajo de MapReduce.
         Select-AzureSubscription $subscriptionName
         $streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

5.  Ejecute los siguientes comandos para mostrar los resultados del recuento de palabras.

         $subscriptionName = "<SubscriptionName>"   
         $storageAccountName = "<StorageAccountName>" 
         $containerName = "<ContainerName>"

         # Seleccione la suscripción actual
         Select-AzureSubscription $subscriptionName
                  
         # Nombre de la cuenta y del contenedor de almacenamiento de blobs

    	 $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $\_.Primary } 
		 $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

         # Recuperar la salida
         Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force 

         # El número de palabras del texto es:
         cat ./example/data/StreamingOutput/wc.txt/part-00000

    Tenga en cuenta que los archivos de salida de un trabajo de MapReduce son inmutables. Por lo tanto, si vuelve a ejecutar esta muestra tendrá que cambiar el nombre del archivo de salida.

El código de C\# para streaming de Hadoop
-----------------------------------------

El programa de MapReduce utiliza la aplicación cat.exe como una interfaz de asignación para transmitir el texto en la consola y la aplicación wc.exe como la interfaz de reducción para contar el número de palabras que se transmiten desde un documento. Tanto el asignador como el reductor leen caracteres, línea por línea, a partir de la secuencia de entrada estándar (stdin) y escriben en la secuencia de salida estándar (stdout).

    // El código fuente de cat.exe (Asignador). 
     
    using System;
    using System.IO;

    namespace cat
    {
        class cat
        {
            static void Main(string[] args)
            {
                if (args.Length > 0)
                {
                    Console.SetIn(new StreamReader(args[0])); 
                }

                string line;
                while ((line = Console.ReadLine()) != null) 
                {
                    Console.WriteLine(line);
                }
            }
        }
    }

El código del asignador en el archivo cat.cs usa un objeto StreamReader para leer los caracteres de la transmisión entrante en la consola, la cual a su vez escribe la transmisión en la secuencia de salida estándar con el método Console.Writeline estático.

    // El código fuente de wc.exe (Reductor) es:

    using System;
    using System.IO;
    using System.Linq;

    namespace wc
    {
        class wc
        {
            static void Main(string[] args)
            {
                string line;
                var count = 0;

                if (args.Length > 0){
                    Console.SetIn(new StreamReader(args[0]));
                }

                while ((line = Console.ReadLine()) != null) {
                    count += line.Count(cr => (cr == ' ' || cr == '\n'));
                }
                Console.WriteLine(count);
            }
        }
    }

El código reductor en el archivo wc.cs usa un objeto [StreamReader](http://msdn.microsoft.com/es-es/library/system.io.streamreader.aspx) para leer caracteres de la secuencia de entrada estándar que han sido de salida por el asignador cat.exe. A medida que lee los caracteres con el método [Console.Writeline](http://msdn.microsoft.com/es-es/library/system.console.writeline), cuenta las palabras contando el espacio y los caracteres de fin de línea al final de cada palabra y, a continuación, escribe el total de la secuencia de salida estándar con el método [Console.Writeline](http://msdn.microsoft.com/es-es/library/system.console.writeline).

Resumen
-------

En este tutorial, ha observado la manera de implementar un trabajo de MapReduce en HDInsight con la ayuda del streaming de Hadoop.

Pasos siguientes
----------------

Para ver tutoriales que ejecutan otras muestras y proporcionan instrucciones sobre el uso de Pig, Hive y los trabajos de MapReduce en HDInsight de Azure con Azure PowerShell, consulte los siguientes temas:

-   [Introducción a HDInsight de Azure](/es-es/manage/services/hdinsight/get-started-hdinsight/)
-   [Muestra: estimador de Pi](/es-es/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/)
-   [Muestra: WordCount](/es-es/manage/services/hdinsight/howto-run-samples/sample-wordcount/)
-   [Muestra: GraySort de 10 GB](/es-es/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [Uso de Pig con HDInsight](/es-es/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Uso de Hive con HDInsight](/es-es/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Azure HDInsight SDK documentation](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

