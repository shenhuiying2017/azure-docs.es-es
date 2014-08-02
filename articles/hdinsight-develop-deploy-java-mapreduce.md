<properties linkid="manage-services-hdinsight-develop-Java-MapReduce-programs-for-HDInsight" urlDisplayName="HDInsight Tutorials" pageTitle="Develop Java MapReduce programs for HDInsight | Azure" metaKeywords="hdinsight, hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce, Java" description="Learn how to develop Java MapReduce programs on HDInsight emulator, how to deploy them to HDInsight." services="hdinsight" title="Develop Java MapReduce programs for HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="jgao" />

Desarrollo de programas MapReduce de Java para HDInsight
========================================================

Este tutorial le guiará por un escenario integral, desde el desarrollo y la prueba de un trabajo de MapReduce para el recuento de palabras en el emulador de HDInsight hasta su implementación y ejecución en HDInsight de Azure.

**Requisitos previos:**

Antes de empezar este tutorial, debe realizar lo siguiente:

-   Instale el emulador de HDInsight de Azure. Para obtener más información, consulte [Introducción al emulador de HDInsight](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/).
-   Instale Azure PowerShell en el equipo emulador. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).
-   Obtenga una suscripción de Azure. Para obtener más información, consulte [Opciones de compra](https://www.windowsazure.com/en-us/pricing/purchase-options/), [Ofertas para miembros](https://www.windowsazure.com/en-us/pricing/member-offers/) o [Evaluación gratuita](https://www.windowsazure.com/en-us/pricing/free-trial/).

En este artículo
----------------

-   [Desarrollo de un programa de MapReduce para el recuento de palabras en Java](#develop)
-   [Prueba del programa en el emulador](#test)
-   [Carga de los archivos de datos y la aplicación al almacenamiento de blobs de Azure](#upload)
-   [Ejecución del programa de MapReduce en HDInsight de Azure](#run)
-   [Recuperación de los resultados de MapReduce](#retrieve)
-   [Pasos siguientes](#nextsteps)

Desarrollo de un programa de MapReduce para el recuento de palabras en Java
---------------------------------------------------------------------------

El recuento de palabras es una aplicación sencilla que cuenta las repeticiones de cada palabra en un conjunto de entrada especificado.

**Para escribir el trabajo de MapReduce para el recuento de palabras en Java**

1.  Abra el Bloc de notas.
2.  Copie y pegue el siguiente programa en el Bloc de notas.

         package org.apache.hadoop.examples;
            
         import java.io.IOException;
         import java.util.StringTokenizer;
         import org.apache.hadoop.conf.Configuration;
         import org.apache.hadoop.fs.Path;
         import org.apache.hadoop.io.IntWritable;
         import org.apache.hadoop.io.Text;
         import org.apache.hadoop.mapreduce.Job;
         import org.apache.hadoop.mapreduce.Mapper;
         import org.apache.hadoop.mapreduce.Reducer;
         import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
         import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
         import org.apache.hadoop.util.GenericOptionsParser;
            
         public class WordCount {
            
           public static class TokenizerMapper 
                extends Mapper<Object, Text, Text, IntWritable>{
                
             private final static IntWritable one = new IntWritable(1);
             private Text word = new Text();
                  
             public void map(Object key, Text value, Context context
                             ) throws IOException, InterruptedException {
               StringTokenizer itr = new StringTokenizer(value.toString());
               while (itr.hasMoreTokens()) {
                 word.set(itr.nextToken());
                 context.write(word, one);
               }
             }
           }
              
           public static class IntSumReducer 
                extends Reducer<Text,IntWritable,Text,IntWritable> {
             private IntWritable result = new IntWritable();
            
             public void reduce(Text key, Iterable<IntWritable> values, 
                                Context context
                                ) throws IOException, InterruptedException {
               int sum = 0;
               for (IntWritable val : values) {
                 sum += val.get();
               }
               result.set(sum);
               context.write(key, result);
             }
           }
            
           public static void main(String[] args) throws Exception {
             Configuration conf = new Configuration();
             String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
             if (otherArgs.length != 2) {
               System.err.println("Usage: wordcount <in> <out>");
               System.exit(2);
             }
             Job job = new Job(conf, "word count");
             job.setJarByClass(WordCount.class);
             job.setMapperClass(TokenizerMapper.class);
             job.setCombinerClass(IntSumReducer.class);
             job.setReducerClass(IntSumReducer.class);
             job.setOutputKeyClass(Text.class);
             job.setOutputValueClass(IntWritable.class);
             FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
             FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
             System.exit(job.waitForCompletion(true) ? 0 : 1);
           }
         }

    Tenga en cuenta que el nombre del paquete es **org.apache.hadoop.examples** y el nombre de clase es **WordCount**. Utilizará los nombres cuando envíe el trabajo de MapReduce.

3.  Guarde el archivo como **c:\\Tutorials\\WordCountJava\\WordCount.java**. Cree la estructura de carpeta si no existe.

El emulador de HDInsight incluye el compilador *javac*.

**Para compilar el programa de MapReduce**

1.  Abra el símbolo del sistema.
2.  Cambie el directorio a **c:\\Tutorials\\WordCountJava**. Esta es la carpeta para el programa de MapReduce para el recuento de palabras.
3.  Ejecute el comando siguiente para comprobar la existencia de los dos archivos jar:

         dir %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar
         dir %hadoop_home%\lib\commons-cli-1.2.jar

4.  Ejecute el siguiente comando para compilar el programa:

         C:\Hadoop\java\bin\javac -classpath %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar;%hadoop_home%\lib\commons-cli-1.2.jar WordCount.java

    javac está ubicado en la carpeta C:\\Hadoop\\java\\bin. El último parámetro es el programa java que se encuentra en la carpeta actual. El compilador crear tres archivos de clase en la carpeta actual.

5.  Ejecute el siguiente comando para crear un archivo jar:

         C:\Hadoop\java\bin\jar -cvf WordCount.jar *.class

    El comando crea un archivo WordCount.jar en la carpeta actual.

    ![HDI.EMulator.WordCount.Compile](./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png)

Prueba del programa en el emulador
----------------------------------

La prueba del trabajo de MapReduce en el emulador incluye los procedimientos siguientes:

1.  cargar los archivos de datos en el HDFS en el emulador
2.  enviar un trabajo de MapReduce para el recuento de palabras
3.  comprobar el estado del trabajo
4.  recuperar los resultados del trabajo

De manera predeterminada, el emulador de HDInsight utiliza HDFS como sistema de archivos predeterminado. Opcionalmente, puede configurar el emulador de HDInsight para utilizar el almacenamiento de blobs de Azure. Para obtener más información, consulte [Introducción al emulador de HDInsight](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/#blobstorage).

En este tutorial, utilizará el comando *copyFromLocal* de HDFS para cargar los archivos de datos a HDFS. La siguiente sección muestra cómo cargar archivos con Azure PowerShell en el almacenamiento de blobs de Azure. Para obtener información acerca de otros métodos para cargar archivos en el almacenamiento de blobs de Azure, consulte [Carga de datos en HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/).

Este tutorial utiliza la siguiente estructura de carpetas de HDFS:

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><td data-morhtml="true">Carpeta</td><td data-morhtml="true">Nota:</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">/WordCount</td><td data-morhtml="true">La carpeta ra&iacute;z para el proyecto para el recuento de palabras. </td></tr>
<tr data-morhtml="true"><td data-morhtml="true">/WordCount/Apps</td><td data-morhtml="true">La carpeta para los ejecutables de los programas asignador y reductor.</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">/WordCount/Input</td><td data-morhtml="true">La carpeta de los archivos de origen de MapReduce.</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">/WordCount/Output</td><td data-morhtml="true">La carpeta de los archivos de resultados de MapReduce.</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">/WordCount/MRStatusOutput</td><td data-morhtml="true">La carpeta de resultados del trabajo.</td></tr>
</table>

Este tutorial utiliza los archivos .txt ubicados en el directorio %hadoop\_home% como archivos de datos.

> [WACOM.NOTE] Los comandos HDFS de Hadoop distinguen mayúsculas de minúsculas.

**Para copiar los archivos de datos al HDFS del emulador**

1.  Abra la línea de comandos de Hadoop desde el escritorio. La línea de comandos de Hadoop la instala el instalador del emulador.
2.  Desde la ventana de la línea de comandos de Hadoop, ejecute el comando siguiente para crear un directorio para los archivos de entrada:

         hadoop fs -mkdir /WordCount/Input

    La ruta de acceso utilizada aquí es la ruta de acceso relativa. Es equivalente a la siguiente:

         hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

3.  Ejecute el comando siguiente para copiar algunos archivos de texto en la carpeta de entrada en HDFS:

         hadoop fs -copyFromLocal %hadoop_home%\*.txt /WordCount/Input

    El trabajo de MapReduce contará las palabras de estos archivos.

4.  Ejecute el comando siguiente para enumerar y comprobar los archivos cargados:

         hadoop fs -ls /WordCount/Input

    Verá unos ocho archivos .txt.

**Para ejecutar el trabajo de MapReduce mediante la línea de comandos de Hadoop**

1.  Abra la línea de comandos de Hadoop desde el escritorio.
2.  Ejecute el comando siguiente para eliminar la estructura de carpetas /WordCount/Output de HDFS. /WordCount/Output es la carpeta de resultados del trabajo de MapReduce para el recuento de palabras. El trabajo de MapReduce producirá un error si la carpeta ya existe. Este paso es necesario si es la segunda vez que ejecuta el trabajo.

         hadoop fs -rmr /WordCount/Output

3.  Ejecute el siguiente comando:

         hadoop jar c:\Tutorials\WordCountJava\wordcount\target\wordcount-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

    Si el trabajo se completa satisfactoriamente, debería obtener un resultado similar al de la captura de pantalla siguiente:

    ![HDI.EMulator.WordCount.Run](./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png)

    En la captura de pantalla, puede ver tanto la asignación como la reducción completadas al 100%. También muestra el identificador del trabajo, job\_201312092021\_0002. El mismo informe se puede recuperar abriendo el acceso directo **Hadoop MapReduce status** en el escritorio y buscando el identificador del trabajo.

La otra opción para ejecutar un trabajo de MapReduce es utilizar Azure PowerShell. Para obtener más información, consulte [Introducción al emulador de HDInsight](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/).

**Para mostrar el resultado de HDFS**

1.  Abra la línea de comandos de Hadoop.
2.  Ejecute los comandos siguientes para ver el resultado:

         hadoop fs -ls /WordCount/Output/
         hadoop fs -cat /WordCount/Output/part-00000

    Puede anexar "|more" al final del comando para obtener la vista de la página. O bien, utilice el comando findstr para encontrar un patrón de cadena:

         hadoop fs -cat /WordCount/Output/part-00000 | findstr "there"

Hasta ahora, ha desarrollado un trabajo de MapReduce para el recuento de palabras y lo ha probado correctamente en el emulador. El paso siguiente es implementarlo y ejecutarlo en HDInsight de Azure.

Carga de archivos de datos al almacenamiento de blobs de Azure
--------------------------------------------------------------

HDInsight de Azure utiliza el almacenamiento de blobs de Azure para almacenar datos. Cuando se aprovisiona un clúster de HDInsight, se utiliza un contenedor de almacenamiento de blobs de Azure para almacenar archivos del sistema. Puede utilizar este contenedor predeterminado u otro diferente (ya sea en la misma cuenta de almacenamiento de Azure o en otra cuenta de almacenamiento ubicada en el mismo centro de datos que el clúster) para almacenar los archivos de datos.

En este tutorial, creará un contenedor en una cuenta de almacenamiento independiente para los archivos de datos y la aplicación de MapReduce. Los archivos de datos son los archivos de texto del directorio %hadoop\_home% en su estación de trabajo.

**Para crear un almacenamiento de blobs y un contenedor**

1.  Abra Azure PowerShell.
2.  Configure las variables y, a continuación, ejecute los siguientes comandos:

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName_Data = "<AzureStorageAccountName>"  
         $containerName_Data = "<ContainerName>"
         $location = "<MicrosoftDataCenter>"  # Por ejemplo, "East US"

    El valor **$subscripionName** está asociado a la suscripción de Azure. Debe asignar un nombre a los valores **$storageAccountName_Data** y **$containerName_Data**. Para las restricciones de nomenclatura, consulte [Asignación de nombres y referencias a contenedores, blobs y metadatos](http://msdn.microsoft.com/en-us/library/windowsazure/dd135715.aspx).

3.  Ejecute el comando siguiente para crear una cuenta de almacenamiento y un contenedor de almacenamiento de blobs en la cuenta:

         # Seleccionar suscripción de Azure
         Select-AzureSubscription $subscriptionName
            
         # Crear una cuenta de almacenamiento
         New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location
                    
         # Cree un contenedor de almacenamiento de blobs.
         $storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
         $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  
         New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4.  Ejecute los comandos siguientes para comprobar la cuenta de almacenamiento y el contenedor:

         Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
         Get-AzureStorageContainer -Context $destContext

**Para cargar los archivos de datos**

1.  Abra Azure PowerShell.
2.  Configure las tres primeras variables y, a continuación, ejecute los siguientes comandos:

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName_Data = "<AzureStorageAccountName>"  
         $containerName_Data = "<ContainerName>"

         $localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
         $destFolder = "WordCount/Input"

    Los valores de **$storageAccountName_Data** y **$containerName_Data** son iguales a los definidos en el último procedimiento.

    Tenga en cuenta que la carpeta de los archivos de origen es **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT** y la carpeta de destino es **WordCount/Input**.

3.  Ejecute los comandos siguientes para obtener una lista de los archivos txt en la carpeta de archivos de origen:

         # Obtener una lista de los archivos txt
         $filesAll = Get-ChildItem $localFolder
         $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4.  Ejecute los comandos siguientes para crear un objeto de contexto de almacenamiento:

         # Crear un objeto de contexto de almacenamiento
         Select-AzureSubscription $subscriptionName
         $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5.  Ejecute los comandos siguientes para copiar los archivos:

         # Copiar el archivo desde la estación de trabajo local al contenedor de blobs        
         foreach ($file in $filesTxt){
             
             $fileName = "$localFolder\$file"
             $blobName = "$destFolder/$file"
            
             write-host "Copying $fileName to $blobName"
            
             Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
         }

6.  Ejecute el comando siguiente para incluir los archivos cargados:

         # Enumerar los archivos cargados en el contenedor de almacenamiento de blobs
         Write-Host "The Uploaded data files:" -BackgroundColor Green
         Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

    Debería ver unos ocho archivos de datos de texto.

**Para cargar la aplicación para el recuento de palabras**

1.  Abra Azure PowerShell.
2.  Configure las tres primeras variables y, a continuación, ejecute los siguientes comandos:

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName_Data = "<AzureStorageAccountName>"  
         $containerName_Data = "<ContainerName>"

         $jarFile = "C:\Tutorials\WordCountJava\WordCount.jar"
         $blobFolder = "WordCount/jars"

    Los valores de **$storageAccountName_Data** y **$containerName_Data** son los mismos que ha definido en el último procedimiento, lo que significa que cargará tanto el archivo de datos como la aplicación en el mismo contenedor y en la misma cuenta de almacenamiento.

    Tenga en cuenta que la carpeta de destino es **WordCount/jars**.

3.  Ejecute los comandos siguientes para crear un objeto de contexto de almacenamiento:

         # Crear un objeto de contexto de almacenamiento
         Select-AzureSubscription $subscriptionName
         $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

4.  Ejecute los comandos siguientes para copiar las aplicaciones:

         Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

5.  Ejecute el comando siguiente para incluir los archivos cargados:

         # Enumerar los archivos cargados en el contenedor de almacenamiento de blobs
         Write-Host "The Uploaded application file:" -BackgroundColor Green
         Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

    Debería ver aquí el archivo jar.

Ejecución del programa de MapReduce en HDInsight de Azure
---------------------------------------------------------

El siguiente script de PowerShell ejecuta las tareas siguientes:

1.  aprovisionar un clúster de HDInsight

    1.  crear una cuenta de almacenamiento que se utilizará como el sistema de archivos predeterminado del clúster de HDInsight
    2.  crear un contenedor de almacenamiento de blobs
    3.  crear un clúster de HDInsight

2.  enviar el trabajo de MapReduce

    1.  crear una definición de trabajo de MapReduce
    2.  enviar un trabajo de MapReduce
    3.  esperar hasta que se haya completado el trabajo
    4.  mostrar el error estándar
    5.  mostrar el resultado estándar

3.  eliminar el clúster

    1.  eliminar el clúster de HDInsight
    2.  eliminar la cuenta de almacenamiento utilizada como sistema de archivos predeterminado del clúster de HDInsight

**Para ejecutar el script de PowerShell**

1.  Abra el Bloc de notas.
2.  Copie y pegue el código siguiente:

         # La cuenta de almacenamiento y las variables del clúster de HDInsight
         $subscriptionName = "<WindowsAzureSubscriptionName>"
         $serviceNameToken = "<ServiceNameTokenString>"
         $location = "<MicrosoftDataCenter>"     ### coincidir con la ubicación de la cuenta de almacenamiento de datos
         $clusterNodes = <NumberOFNodesInTheCluster>

         $storageAccountName_Data = "<TheDataStorageAccountName>"
         $containerName_Data = "<TheDataBlobStorageContainerName>"
            
         $clusterName = $serviceNameToken + "hdicluster"
            
         $storageAccountName_Default = $serviceNameToken + "hdistore"
         $containerName_Default =  $serviceNameToken + "hdicluster"

         # Las variables de trabajo de MapReduce
         $jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
         $className = "org.apache.hadoop.examples.WordCount"
         $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
         $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
         $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
            
         # Crear un objeto PSCredential. El nombre de usuario y la contraseña se codifican aquí de forma rígida.  Puede cambiarlos si lo desea.
         $password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
         $creds = New-Object System.Management.Automation.PSCredential ("Admin", $password) 
            
         Select-AzureSubscription $subscriptionName
            
         #=============================
         # Crear una cuenta de almacenamiento utilizada como sistema de archivos predeterminado
         Write-Host "Create a storage account" -ForegroundColor Green
         New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
            
         #=============================
         # Crear un contenedor de almacenamiento de blobs utilizado como sistema de archivos predeterminado
         Write-Host "Create a Blob storage container" -ForegroundColor Green
         $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
         $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default
            
         New-AzureStorageContainer -Name $containerName_Default -Context $destContext
            
         #=============================
         # Crear un clúster de HDInsight
         Write-Host "Create an HDInsight cluster" -ForegroundColor Green
         $storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
            
         $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
             Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
             Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data
            
         New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $creds -Config $config
            
         #=============================
         # Crear una definición de trabajo de MapReduce
         Write-Host "Create a MapReduce job definition" -ForegroundColor Green
         $mrJobDef = New-AzureHDInsightMapReduceJobDefinition -JobName mrWordCountJob  -JarFile $jarFile -ClassName $className -Arguments $mrInput, $mrOutput -StatusFolder /WordCountStatus
            
         #=============================
         # Ejecutar el trabajo de MapReduce
         Write-Host "Run the MapReduce job" -ForegroundColor Green
         $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
         Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 
            
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput
                    
         #=============================
         # Eliminar el clúster de HDInsight
         Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
         Remove-AzureHDInsightCluster -Name $clusterName  
            
         # Eliminar la cuenta de almacenamiento del sistema de archivos predeterminado
         Write-Host "Delete the storage account" -ForegroundColor Green
         Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3.  Establezca las seis primeras variables en el script. **$serviceNameToken** se utilizará para el nombre del clúster de HDInsight, el nombre de la cuenta de almacenamiento predeterminado y el nombre del contenedor de almacenamiento de blobs predeterminado. Como el nombre del servicio debe tener de 3 a 24 caracteres y el script anexa una cadena de hasta 10 caracteres a los nombres, debe limitar la cadena a 14 caracteres o menos. Y $serviceNameToken debe utilizar minúsculas. **$storageAccountName_Data** y **$containerName_Data** constituyen la cuenta de almacenamiento y el contenedor que se utilizan para almacenar los archivos de datos y la aplicación. **$location** debe coincidir con la ubicación de la cuenta de almacenamiento de datos.
4.  Revise el resto de las variables.
5.  Guarde el archivo de script.
6.  Abra Azure PowerShell.
7.  Ejecute el comando siguiente para establecer la directiva de ejecución en remotesigned:

         PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  Cuando se le solicite, escriba el nombre de usuario y la contraseña para el clúster de HDInsight. Como eliminará el clúster al final del script y no necesitará más el nombre de usuario y la contraseña, estos pueden ser cualquier cadena. Si no desea que se le soliciten las credenciales, consulte [Working with Passwords, Secure Strings and Credentials in Windows PowerShell](http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx)

Recuperación del resultado del trabajo de MapReduce
---------------------------------------------------

En esta sección se muestra cómo descargar y mostrar los resultados. Para obtener más información sobre cómo mostrar los resultados en Excel, consulte [Conexión de Excel a HDInsight con Microsoft Hive ODBC Driver](/en-us/manage/services/hdinsight/connect-excel-with-hive-ODBC/) y [Conexión de Excel a HDInsight con Power Query](/en-us/manage/services/hdinsight/connect-excel-with-power-query/).

**Para recuperar los resultados**

1.  Abra la ventana de Azure PowerShell.
2.  Cambie el directorio a **C:\\Tutorials\\WordCountJava**. La carpeta predeterminada de Azure PowerShell es **C:\\Windows\\System32\\WindowsPowerShell\\v1.0**. Los cmdlets que ejecutará descargarán el archivo de resultados a la carpeta actual. No tiene permiso para descargar los archivos en las carpetas del sistema.
3.  Ejecute los siguientes comandos para establecer los valores:

         $subscriptionName = "<WindowsAzureSubscriptionName>"
         $storageAccountName_Data = "<TheDataStorageAccountName>"
         $containerName_Data = "<TheDataBlobStorageContainerName>"
         $blobName = "WordCount/Output/part-r-00000"

4.  Ejecute los siguientes comandos para crear un objeto de contexto de almacenamiento de Azure:

         Select-AzureSubscription $subscriptionName
         $storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
         $storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

5.  Ejecute los comandos siguientes para descargar y mostrar el resultado:

         Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
         cat "./$blobName" | findstr "there"

Después de finalizar el trabajo, puede exportar los datos a una base de datos SQL Server o Azure SQL usando [Sqoop](../hdinsight-use-sqoop/) o exportar los datos a Excel.

Pasos siguientes
----------------

En este tutorial, ha aprendido a desarrollar un trabajo de MapReduce de Java, a probar la aplicación en el emulador de HDInsight y a escribir un script PowerShell para aprovisionar un clúster de HDInsight y ejecutar un MapReduce en el clúster. Para obtener más información, consulte los artículos siguientes:

-   [Desarrollo de programas de MapReduce de streaming de Hadoop C\# para HDInsight](/en-us/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [Introducción a HDInsight de Azure](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Introducción al emulador de HDInsight](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/)
-   [Uso del almacenamiento de blobs de Azure con HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/)
-   [Administración de HDInsight con PowerShell](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/)
-   [Carga de datos en HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/)
-   [Uso de Hive con HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Uso de Pig con HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Conexión de Excel a HDInsight con Power Query](/en-us/documentation/articles/hdinsight-connect-excel-power-query/)
-   [Conexión de Excel a HDInsight con Microsoft Hive ODBC Driver](../hdinsight-connect-excel-hive-ODBC-driver/)

