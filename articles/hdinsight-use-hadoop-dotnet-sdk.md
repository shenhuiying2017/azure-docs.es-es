<properties linkid="manage-services-hdinsight-howto-sdk" urlDisplayName="HDInsight SDK" pageTitle="How to use the HDInsight .NET libraries | Azure" metaKeywords="" description="Learn how to get the HDInsight NuGet packages and use them from your .NET application." metaCanonical="" services="hdinsight" documentationCenter="" title="Use the Hadoop .NET SDK with HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Uso del SDK .NET de Hadoop con HDInsight
========================================

El SDK .NET de Hadoop proporciona bibliotecas de cliente .NET que facilitan el trabajo con Hadoop en .NET. En este tutorial aprenderá a obtener el SDK .NET de Hadoop y a utilizarlo para crear una sencilla aplicación basada en .NET que ejecute consultas de Hive mediante el uso del servicio HDInsight de Azure. A partir de un archivo actors.txt, escribirá una aplicación para encontrar el actor/la actriz que haya obtenido la mayor cantidad de premios.

Para habilitar HDInsight, haga clic [aquí](https://account.windowsazure.com/PreviewFeatures).

En este artículo
----------------

-   [Descarga e instalación del SDK .NET de Hadoop](#install)
-   [Preparación para el tutorial](#prepare)
-   [Creación de la aplicación](#create)
-   [Ejecución de la aplicación](#run)
-   [Pasos siguientes](#nextsteps)

Descarga e instalación del SDK .NET de Hadoop
---------------------------------------------

Puede instalar la compilación publicada más reciente del SDK en [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). El SDK incluye los siguientes componentes:

-   **Biblioteca de MapReduce**: Simplifica el proceso de escribir trabajos de MapReduce en lenguajes .NET con la interfaz de transmisión por secuencias de Hadoop.

-   **Biblioteca de clientes de LINQ a Hive**: Traduce consultas LINQ de C\# o F\# a consultas HiveQL y las ejecuta en el clúster de Hadoop. Esta biblioteca también puede ejecutar consultas arbitrarias de HiveQL desde una aplicación .NET.

-   **Biblioteca de WebClient**: Contiene bibliotecas de clientes para *WebHDFS* y *WebHCat*.

    -   **Biblioteca de clientes de WebHDFS**: Trabaja con archivos en HDFS y el almacenamiento de blobs de Azure.

    -   **Biblioteca de clientes de WebHCat**: Administra la programación y ejecución de trabajos en el clúster de HDInsight.

Sintaxis de NuGet para instalar las bibliotecas:

    install-package Microsoft.Hadoop.MapReduce
    install-package Microsoft.Hadoop.Hive 
    install-package Microsoft.Hadoop.WebClient 

Estos comandos agregan las bibliotecas y referencias al proyecto de Visual Studio actual.

Preparación para el tutorial
----------------------------

Debe tener una [suscripción de Azure](http://www.windowsazure.com/es-es/pricing/free-trial/) y una [cuenta de almacenamiento de Azure](http://www.windowsazure.com/es-es/manage/services/storage/how-to-create-a-storage-account/) antes de continuar. También debe conocer el nombre de su cuenta de almacenamiento de Azure y la clave de la cuenta. Para obtener instrucciones sobre cómo obtener esta información, consulte la sección *Vista, copia y regeneración de claves de acceso al almacenamiento* de [Administración de cuentas de almacenamiento](/es-es/manage/services/storage/how-to-manage-a-storage-account/).

También debe descargar el archivo actors.txt que se utiliza en este tutorial. Realice los siguientes pasos para descargar este archivo en su entorno de desarrollo:

1.  Cree una carpeta C:\\Tutorials en el equipo local.

2.  Descargue el archivo [actors.txt](http://www.microsoft.com/en-us/download/details.aspx?id=37003) y guárdelo en la carpeta C:\\Tutorials.

Creación de la aplicación
-------------------------

En esta sección aprenderá a cargar archivos en el clúster de Hadoop mediante programación y a ejecutar trabajos de Hive con LINQ a Hive.

1.  Abra Visual Studio 2012.

2.  En el menú Archivo, haga clic en **Nuevo** y, a continuación, haga clic en **Proyecto**.

3.  En Nuevo proyecto, escriba o seleccione los siguientes valores:

  <table data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
 <tr data-morhtml="true">
 <th data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propiedad</th>
 <th data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valor</th></tr>
 <tr data-morhtml="true">
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categor&iacute;a</td>
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Plantillas/Visual C#/Windows</td></tr>
 <tr data-morhtml="true">
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Plantilla</td>
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Aplicaci&oacute;n de consola</td></tr>
 <tr data-morhtml="true">
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre</td>
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Trabajo de Hive sencillo</td></tr>
 </table>

4.  Haga clic en **Aceptar** para crear el proyecto.

5.  En el menú **Herramientas**, haga clic en **Library Package Manager** y, a continuación, en **Package Manager Console**.

6.  Ejecute los siguientes comandos en la consola para instalar los paquetes.

         install-package Microsoft.Hadoop.Hive 
         install-package Microsoft.Hadoop.WebClient 

    Estos comandos agregan las bibliotecas y referencias .NET al proyecto de Visual Studio actual.

7.  En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo.

8.  Agregue la siguiente instrucción en la parte superior del archivo:

         using Microsoft.Hadoop.WebHDFS.Adapters;
         using Microsoft.Hadoop.WebHDFS;
         using Microsoft.Hadoop.Hive;

9.  En la función Main(), copie y pegue el siguiente código:

         // Cargar actors.txt en el almacenamiento de blobs
         var asvAccount = [Storage-account-name.blob.core.windows.net];
         var asvKey = [Storage account key];
         var asvContainer = [Container name];
         var localFile = "C:/Tutorials/Actors.txt";
         var hadoopUser = [Hadoop user name]; // El usuario del clúster de HDInsight
         var hadoopUserPassword = [Hadoop user password]; // La contraseña del usuario del clúster de HDInsight
         var clusterURI = [HDInsight cluster URL]; //"https://HDInsightCluster Name.azurehdinsight.net:563";
            
         var storageAdapter = new BlobStorageAdapter(asvAccount, asvKey, asvContainer, true);
         var HDFSClient = new WebHDFSClient(hadoopUser, storageAdapter);
            
         Console.WriteLine("Creating MovieData directory and uploading actors.txt...");
         HDFSClient.CreateDirectory("/user/hadoop/MovieData").Wait();
         HDFSClient.CreateFile(localFile, "/user/hadoop/MovieData/Actors.txt").Wait();
            
         // Crear conexión de Hive
         var hiveConnection = new HiveConnection(
           new System.Uri(clusterURI),
           hadoopUser, 
           hadoopUserPassword,
           asvAccount, 
           asvKey);
            
         // Anular cualquier tabla existente llamada Actors
         // Solo se necesita si desea volver a ejecutar esta aplicación
         // y anular una tabla Actors anterior.
         //hiveConnection.GetTable<HiveRow>("Actors").Drop();

         Console.WriteLine("Creating a Hive table named 'Actors'...");
         string command =
           @"CREATE TABLE Actors(
                     MovieId string, 
                     ActorId string,
                     Name string, 
                     AwardsCount int) 
                     row format delimited fields 
                 terminated by ',';";
         hiveConnection.ExecuteHiveQuery(command).Wait();
            
         Console.WriteLine("Load data from Actors.txt into the 'Actors' table in Hive...");
         command =
           @"LOAD DATA INPATH 
                 '/user/hadoop/MovieData/Actors.txt'
             OVERWRITE INTO TABLE Actors;";
         hiveConnection.ExecuteHiveQuery(command).Wait();
            
         Console.WriteLine("Performing Hive query...");
         var result = hiveConnection.ExecuteQuery(@"select name, awardscount
                   from actors sort by awardscount desc
                   limit 1");
         result.Wait();

         Console.WriteLine("The results are: {0}", result.Result.ReadToEnd());
         Console.WriteLine("\nPress any key to continue.");
         Console.ReadKey();

10. Actualice las constantes de la aplicación. El servicio HDInsight de Azure usa el almacenamiento de blobs de Azure como sistema de archivos predeterminado. Durante el proceso de aprovisionamiento de HDInsight, se designa un blob como sistema de archivos predeterminado. Tiene la opción de utilizar el contenedor de sistema de archivos predeterminado o un contenedor en un almacenamiento de blobs distinto. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](/es-es/manage/services/hdinsight/howto-blob-store/).

    Si elige utilizar el contenedor de sistema de archivos predeterminado, puede encontrar el nombre de la cuenta de almacenamiento, la clave de almacenamiento y el nombre del contenedor en el archivo de configuración *c:\\apps\\dist\\hadoop-1.1.0-SNAPSHOT\\conf\>core-site.xml* si se conecta de manera remota al clúster. Puede encontrar el contenedor que se utiliza como sistema de archivos predeterminado si busca en *fs.default.name*; el nombre de la cuenta de almacenamiento y la clave de cuenta puede encontrarlos si busca en *fs.azure.account.key*.

Ejecución de la aplicación
--------------------------

Mientras la aplicación está abierta en Visual Studio, presione **F5** para ejecutarla. Se debe abrir una ventana de la consola y mostrar los pasos que ejecuta la aplicación a medida que los datos se cargan, se almacenan en una tabla de Hive y, finalmente, se consultan. Una vez que se completa la aplicación y que se han devuelto los resultados de la consulta, presione cualquier tecla para terminar la aplicación.

![HDI.HadoopSDKOutput](./media/hdinsight-use-hadoop-dotnet-sdk/HDI.HadoopSDKOutput.PNG "Aplicación de consola")

**Nota:**

Cada paso que realiza la aplicación puede tardar segundos, o incluso minutos, en completarse. El tiempo para cargar el archivo actors.txt variará según su conexión de Internet al centro de datos de Azure, mientras que los demás pasos dependen del tamaño del clúster.

**Nota:**

La operación LOAD DATA INPATH es una operación de desplazamiento que mueve los datos del archivo actors.txt al espacio de nombres del sistema de archivos controlado por Hive. Con esto se elimina realmente el archivo actors.txt de la ubicación de carga. Debido a esto, el archivo actors.txt se debe cargar cada vez que ejecuta esta aplicación.

Para obtener más información sobre cómo cargar datos en Hive, consulte [Introducción a Hive](https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-DMLOperations).

Pasos siguientes
----------------

Ahora sabe crear una aplicación .NET con el SDK .NET de Hadoop. Para obtener más información, consulte los artículos siguientes:

-   [Introducción a HDInsight de Azure](/es-es/manage/services/hdinsight/get-started-hdinsight/)
-   [Uso de Pig con HDInsight](/es-es/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Uso de MapReduce con HDInsight](/es-es/manage/services/hdinsight/using-mapreduce-with-hdinsight/)
-   [Uso de Hive con HDInsight](/es-es/manage/services/hdinsight/using-hive-with-hdinsight/)

