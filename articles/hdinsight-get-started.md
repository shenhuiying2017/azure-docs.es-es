<properties  linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started using HDInsight | Azure" metaKeywords="" description="Get started with HDInsight, a big data solution. Learn how to provision clusters, run MapReduce jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Azure HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

# Introducción al uso de HDInsight de Azure

HDInsight pone [Apache Hadoop][1] a disposición de los usuarios como servicio en la nube. De esta forma, el marco de software de MapReduce está disponible en un entorno de Azure más sencillo, escalable y rentable. HDInsight también ofrece una solución rentable para la administración y el almacenamiento de datos mediante el almacenamiento de blobs de Azure.

En este tutorial se aprovisionará un clúster de HDInsight a través del Portal de administración de Azure, se enviará un trabajo de MapReduce de Hadoop mediante PowerShell y, después, se importarán los datos resultantes del trabajo de MapReduce en Excel para su análisis.

> [WACOM.NOTE] En este tutorial se trata el uso de los clústeres de
> Hadoop 1.2 en HDInsight. Para ver el tutorial acerca del uso de
> clústeres de Hadoop 2.2 en HDInsight, consulte [Introducción al uso de
> clústeres de Hadoop 2.2 con
> HDInsight](/en-us/documentation/articles/hdinsight-get-started-30/).

Además de poner HDInsight de Azure a disposición de los usuarios, Microsoft también ha lanzado el emulador de HDInsight para Azure, anteriormente conocido como Microsoft HDInsight Developer Preview. Este producto está destinado a los desarrolladores y, como tal, solo admite implementaciones de un solo nodo. Para usar el emulador de HDInsight, consulte [Introducción al emulador de HDInsight](/en-us/documentation/articles/hdinsight-get-started-emulator/).

**Requisitos previos:**

Antes de empezar este tutorial, debe tener lo siguiente:

* Una suscripción de Azure. Para obtener más información acerca de cómo
  obtener una suscripción, consulte [Opciones de compra][2], [Ofertas
  para miembros][3] o [Evaluación gratuita][4].
* Un equipo que ejecute Windows 8, Windows 7, Windows Server 2012 o
  Windows Server 2008 R2. Este equipo se usará para enviar trabajos de
  MapReduce.
* Office Professional Plus 2013, Office 365 Pro Plus, Excel 2013
  Standalone u Office Professional Plus 2010.

**Tiempo estimado para completarlo:** 30 minutos

## Apartados de este tutorial

* [Configuración de un entorno local para ejecutar PowerShell](#setup)
* [Aprovisionamiento de un clúster de HDInsight](#provision)
* [Ejecución de un trabajo WordCount de MapReduce](#sample)
* [Conexión a las herramientas de inteligencia empresarial de
  Microsoft](#powerquery)
* [Pasos siguientes](#nextsteps)

## <a  id="setup" ></a> Configuración de un entorno local para ejecutar PowerShell

Existen varias formas de enviar trabajos de MapReduce a HDInsight. En este tutorial se usará Azure PowerShell. Para instalar Azure PowerShell, ejecute el [instalador de plataforma web de Microsoft][5]. Cuando se le solicite, haga clic en **Ejecutar**, a continuación, en **Instalar** y después siga las instrucciones. Para obtener más información, consulte
[Instalación y configuración de Azure
PowerShell](/en-us/documentation/articles/install-configure-powershell/).

Los cmdlets de PowerShell requieren información de la suscripción para utilizarse a fin de administrar los servicios.

**Para conectarse a su suscripción con Azure AD**

1.  Abra la consola de Azure PowerShell siguiendo las instrucciones del
    apartado [Instalación de Azure
    PowerShell](/en-us/documentation/articles/install-configure-powershell/#install).
2.  Ejecute el siguiente comando:
    
         Add-AzureAccount

3.  En la ventana, escriba la dirección de correo electrónico y la contraseña asociadas a su cuenta. Azure autentica y guarda las credenciales y, a continuación, cierra la ventana.

Como alternativa para conectarse a la suscripción, puede usar el método del certificado. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

## <a  name="provision" ></a>Aprovisionamiento de un clúster de HDInsight

El proceso de aprovisionamiento de HDInsight requiere el uso de una cuenta de almacenamiento de Azure como sistema de archivos predeterminado. Dicha cuenta debe estar ubicada en el mismo centro de datos que los recursos de proceso de HDInsight. En la actualidad, solo pueden aprovisionarse clústeres de HDInsight en los siguientes centros de datos:

* Sudeste asiático
* Europa del Norte
* Europa occidental
* Este de EE. UU.
* Oeste de EE. UU.

Debe elegir uno de estos cinco centros de datos para la cuenta de almacenamiento de Azure.

**Para crear una cuenta de Almacenamiento de Azure**

1.  Inicie sesión en el [Portal de administración de Azure][6]. 2.  Haga clic en **NEW** en la esquina inferior izquierda, seleccione **DATA SERVICES**, **STORAGE** y, a continuación, haga clic en **QUICK CREATE**.
    
    ![HDI.StorageAccount.QuickCreate](./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png)

3.  Escriba los detalles de **URL**, **LOCATION** y **REPLICATION** y, a continuación, haga clic en **CREATE STORAGE ACCOUNT**. No se admiten grupos de afinidad. La nueva cuenta de almacenamiento aparecerá en la lista de almacenamiento.
4.  Espere hasta que la característica **STATUS** de la nueva cuenta de
    almacenamiento cambie a **Online**.
5.  Haga clic en la nueva cuenta de almacenamiento en la lista para
    seleccionarla.
6.  Haga clic en **MANAGE ACCESS KEYS** en la parte inferior de la
    página.
7.  Tome nota de los valores de los campos **STORAGE ACCOUNT NAME** y
    **PRIMARY ACCESS KEY**. Los necesitará más adelante en el tutorial.

Para obtener instrucciones detalladas, consulte [Creación de una cuenta de almacenamiento](/en-us/documentation/articles/storage-create-storage-account/) y [Uso del almacenamiento de blobs de Azure con HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/).

**Para aprovisionar un clúster de HDInsight**

1.  Inicie sesión en el [Portal de administración de Azure][6].

2.  Haga clic en la opción **HDInsight**, que aparece a la izquierda, para ver el estado de los clústeres en la cuenta. En la captura de pantalla siguiente no hay ningún clúster de HDInsight.
    
    ![HDI.ClusterStatus](./media/hdinsight-get-started/HDI.ClusterStatus.png)

3.  Haga clic en **NEW** en la esquina inferior izquierda y, después, en **Data Services**, **HDInsight** y en **Quick Create**.
    
    ![HDI.QuickCreateCluster](./media/hdinsight-get-started/HDI.QuickCreateCluster.png)

4.  Escriba o seleccione los siguientes valores:
    
    <table  border="1">
     <tr><th>Nombre</th>
    <th>Valor</th>
    </tr>
    
     <tr><td>Cluster Name</td>
    <td>Nombre del clúster.</td>
    </tr>
    
     <tr><td>Cluster Size</td>
    <td>Número de nodos de datos que desea implementar. El valor predeterminado es 4, aunque también hay disponibles clústeres de nodos de datos de 8, 16 y 32 en el menú desplegable. Al usar la opción <strong>Custom Create</strong>
     puede especificarse un número cualquiera de nodos de datos. Hay disponible información sobre las tarifas de facturación para varios tamaños de clúster. Haga clic en el símbolo <strong>?</strong>
     justo encima del cuadro desplegable y siga el vínculo del elemento emergente.</td>
    </tr>
    
     <tr><td>Password (administrador de clústeres)</td>
    <td>La contraseña del <i>administrador</i>
     de la cuenta. El nombre del usuario del clúster se especifica como "admin" de forma predeterminada al usar la opción Quick Create. Esta solo puede cambiarse mediante el asistente <strong>Custom Create</strong>
    . El campo de contraseña debe tener un mínimo de 10 caracteres y contener una letra mayúscula, una minúscula, un número y un carácter especial.</td>
    </tr>
    
     <tr><td>Storage Account</td>
    <td>Seleccione la cuenta de almacenamiento que ha creado en el cuadro desplegable. <br  />
    
    
     > [WACOM.NOTE] > Una vez elegida una cuenta de almacenamiento, esta no se puede cambiar. En caso de quitarla, el clúster dejará de estar disponible para su uso.
    
     La ubicación del clúster de HDInsight será la misma que la de la cuenta de almacenamiento.
     </td>
    </tr>
    
     </table>

5.  Haga clic en **Create HDInsight Cluster** en la parte inferior derecha. Una vez completado el proceso de aprovisionamiento, la columna de estado mostrará **Running**.

Para obtener información acerca de cómo usar la opción **CUSTOM CREATE**, consulte[Aprovisionamiento de clústeres de HDInsight](/en-us/documentation/articles/hdinsight-provision-clusters/).

## <a  name="sample" ></a>Ejecución de un trabajo WordCount de MapReduce

Ahora ya se ha aprovisionado un clúster de HDInsight. El paso siguiente consiste en ejecutar un trabajo de MapReduce para contar las palabras de un archivo de texto.

Para ejecutar un trabajo de MapReduce se requieren los siguientes elementos:

* Un programa de MapReduce. En este tutorial se usará el ejemplo WordCount incluido con la distribución del clúster de HDInsight, por lo que no tendrá que escribir uno propio. Se encuentra en */example/jars/hadoop-examples.jar*. Para obtener instrucciones acerca de cómo escribir su propio trabajo de MapReduce, consulte [Desarrollo de programas MapReduce de Java para HDInsight](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/).

* Un archivo de entrada. Se usará */example/data/gutenberg/davinci.txt* como este tipo de archivo. Para obtener información acerca de cómo cargar archivos, consulte [Carga de datos en HDInsight](/en-us/documentation/articles/hdinsight-upload-data/).
* Una carpeta de archivo de salida. Se usará */example/data/WordCountOutput* como la carpeta mencionada. El sistema creará la carpeta en caso de que esta no exista.

El esquema URI para obtener acceso a los archivos del almacenamiento de blobs es:

    wasb[s]://<containername>@<storageaccountname>.blob.core.windows.net/<path>

> [WACOM.NOTE] De forma predeterminada, el contenedor de blobs usado
> para el sistema de archivos predeterminado tiene el mismo nombre que
> el clúster de HDInsight.

El esquema URI proporciona tanto acceso no cifrado con el prefijo *wasb:* como acceso SSL cifrado con WASBS. Se recomienda usar wasbs siempre que sea posible, incluso al obtener acceso a los datos que residen en el mismo centro de datos de Azure.

Dado que HDInsight usa un contenedor de almacenamiento de blobs como sistema de archivos predeterminado, puede consultar los archivos y directorios de dicho sistema de archivos mediante rutas de acceso relativas o absolutas.

Por ejemplo, para obtener acceso al archivo hadoop-examples.jar, puede usar una de las siguientes opciones:

	● wasb://<containername>@<storageaccountname>.blob.core.windows.net/example/jars/hadoop-examples.jar
    ● wasb:///example/jars/hadoop-examples.jar
    ● /example/jars/hadoop-examples.jar

El uso del prefijo *wasb://* en las rutas de acceso de estos archivos es necesario para indicar que el almacenamiento de blobs de Azure se está usando para los archivos de entrada y salida. El directorio de salida asume una ruta de acceso relativa predeterminada a la carpeta
*wasb:///user/<nombreusuario\>*.

Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/).

**Para ejecutar el ejemplo WordCount**

1.  Abra **Azure PowerShell**. Para obtener instrucciones acerca de cómo abrir la ventana de la consola de Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

2.  Ejecute los siguientes comandos para establecer las variables:
    
         $subscriptionName = "<SubscriptionName>" 
         $clusterName = "<HDInsightClusterName>"        

3.  Ejecute los siguientes comandos para crear una definición del trabajo de MapReduce:

		# Define the MapReduce job
        $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
    
    El archivo hadoop-examples.jar se incluye con la distribución del clúster de HDInsight. Existen dos argumentos para el trabajo de MapReduce. El primero es el nombre del archivo de origen y, el segundo, la ruta de acceso del archivo de salida. El archivo de origen se incluye con la distribución del clúster de HDInsight y la ruta de acceso del archivo de salida se creará en tiempo de ejecución.

4.  Ejecute el siguiente comando para enviar el trabajo de MapReduce:

		# Submit the job
        Select-AzureSubscription $subscriptionName
        $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 
    
    Además de la definición de trabajo de MapReduce, también debe proporcionarse el nombre del clúster de HDInsight en el que se desea ejecutar el trabajo de MapReduce.
    
    *Start-AzureHDInsightJob* es una llamada no sincronizada. Para comprobar la finalización del trabajo, use el cmdlet *Wait-AzureHDInsightJob*.

5.  Ejecute el siguiente comando para comprobar la finalización del trabajo de MapReduce:
    
         Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

6.  Ejecute el siguiente comando para comprobar posibles errores al ejecutar el trabajo de MapReduce:

		# Get the job output
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError
    
    En la captura de pantalla siguiente se muestra el resultado de una ejecución correcta. En caso contrario, aparecerán mensajes de error.
    
    ![HDI.GettingStarted.RunMRJob](./media/hdinsight-get-started/HDI.GettingStarted.RunMRJob.png)

**Para recuperar los resultados del trabajo de MapReduce**

1.  Abra **Azure PowerShell**. 2.  Ejecute los siguientes comandos para crear una carpeta C:\Tutorials y cambie el directorio a la carpeta:
    
         mkdir \Tutorials
         cd \Tutorials
    
    El directorio predeterminado de Azure Powershell es *C:\Windows\System32\WindowsPowerShell\v1.0*. De forma predeterminada, no tiene permiso de escritura en esta carpeta. Debe cambiar el directorio a una carpeta en la que tenga permiso de  escritura.

3.  Establezca tres variables en los comandos siguientes y, a continuación, ejecútelos:
    
         $subscriptionName = "<SubscriptionName>"       
         $storageAccountName = "<StorageAccountName>"   
         $containerName = "<ContainerName>"			   
    
    La cuenta de almacenamiento de Azure es la misma que se creó anteriormente en el tutorial. Esta se usa para hospedar el contenedor de blobs utilizado como sistema de archivos predeterminado del clúster de HDInsight. El nombre del contenedor de almacenamiento de blobs suele coincidir con el del clúster de HDInsight, a menos que se especifique un nombre distinto durante el aprovisionamiento del clúster.

4.  Ejecute los siguientes comandos para crear un objeto de contexto de almacenamiento de Azure:

		# Create the storage account context object
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    
    *Select-AzureSubscription* se usa para establecer la suscripción actual en caso de tener varias y no usar la suscripción predeterminada.

5.  Ejecute el siguiente comando para descargar el resultado del trabajo de MapReduce del contenedor de blobs a la estación de trabajo:

		# Download the job output to the workstation
        Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
    
    *example/data/WordCountOutput* es la carpeta de salida especificada al ejecutar el trabajo de MapReduce. *part-r-00000* es el nombre de archivo predeterminado para el resultado del trabajo de MapReduce. El archivo se descargará a la misma estructura de carpetas de la carpeta local. Por ejemplo, en la captura de pantalla siguiente, la carpeta actual es la carpeta raíz C. El archivo se descargará en la carpeta *C:\example\data\WordCountOutput\*.

6.  Ejecute el siguiente comando para imprimir el archivo de salida del trabajo de MapReduce:
    
         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"
    
    ![HDI.GettingStarted.MRJobOutput](./media/hdinsight-get-started/HDI.GettingStarted.MRJobOutput.png)
    
    El trabajo de MapReduce genera un archivo denominado *part-r-00000* con las palabras y los recuentos. El script usa el comando findstr para enumerar todas las palabras que contienen *"there"*.

> [WACOM.NOTE] Si se abre en el Bloc de notas
> *./example/data/WordCountOutput/part-r-00000*,
> resultado de varias líneas de un trabajo de MapReduce, observará que
> los saltos de línea no se representan correctamente. Se espera que
> esto sea así.

## <a  name="powerquery" ></a>Conexión a las herramientas de inteligencia empresarial de Microsoft

El complemento Power Query de Excel se puede usar para exportar los resultados de HDInsight a Excel y aplicarles las herramientas de Microsoft Business Intelligence (BI) para seguir procesándolos o mostrar los resultados. Al crear un clúster de HDInsight, se creó un contenedor predeterminado con el mismo nombre que dicho clúster en la cuenta de almacenamiento asociada durante la creación. Este se rellena automáticamente con un conjunto de archivos. Uno de estos archivos es una tabla de Hive de ejemplo. En esta sección se mostrará cómo importar en Excel los datos incluidos en dicha tabla para poder verlos y procesarlos aún más.

Para completar esta parte del tutorial, debe tener instalado Excel 2010 o 2013. Aquí importaremos la tabla de Hive que se incluye con HDInsight.

**Para descargar Microsoft Power Query para Excel**

* Descargue Microsoft Power Query para Excel en el [Centro de descarga
  de Microsoft][7] e instálelo.

**Para importar datos de HDInsight**

1.  Abra Excel y cree un libro en blanco. 2.  Haga clic en el menú **Power Query**, en **From Other Sources** y, a
    continuación, en **From Azure HDInsight**.
    
    ![HDI.GettingStarted.PowerQuery.ImportData](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png)

3.  En **Account Name**, escriba el nombre de la cuenta de almacenamiento de blobs de Azure asociada con su clúster y, a continuación, haga clic en **Aceptar**. Esta es la cuenta de almacenamiento creada previamente en el tutorial.
4.  En **Account Key**, escriba la clave de la cuenta de almacenamiento de blobs de Azure y, a continuación, haga clic en **Guardar**.
5.  En el panel de navegación de la derecha, haga doble clic en el nombre del contenedor de almacenamiento de blobs. De forma predeterminada, el nombre del contenedor es el mismo que el del clúster.

6.  Busque **part-r-00000** en la columna **Name** (la ruta de acceso es *.../example/data/WordCountOutput*) y, a continuación, haga clic en **Binary** a la izquierda de **part-r-00000**.
    
    ![HDI.GettingStarted.PowerQuery.ImportData2](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png)

7.  Haga clic con el botón secundario en **Column1.1** y seleccione **Rename**.
8.  Cambie el nombre a **Word**. 9.  Repita el proceso para cambiar el nombre de **Column1.2** a **Count**.
    
    ![HDI.GettingStarted.PowerQuery.ImportData3](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData3.png)

10. Haga clic en **Apply & Close** en la esquina superior izquierda. La consulta importa la tabla de Hive en Excel.

## <a  name="nextsteps" ></a>Pasos siguientes

En este tutorial ha aprendido cómo aprovisionar un clúster con HDInsight, cómo ejecutar un trabajo de MapReduce en este y cómo importar los resultados en Excel para procesarlos mejor y representarlos gráficamente mediante las herramientas de BI. Para obtener más información, consulte los siguientes artículos:

* [Introducción al uso de clústeres de Hadoop 2.2 con
  HDInsight](/en-us/documentation/articles/hdinsight-get-started-30/)
* [Introducción al emulador de
  HDInsight](/en-us/documentation/articles/hdinsight-get-started-emulator/)
* [Uso del almacenamiento de blobs de Azure con
  HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/)
* [Administración de HDInsight con
  PowerShell](/en-us/documentation/articles/hdinsight-administer-use-powershell/)
* [Carga de datos en
  HDInsight](/en-us/documentation/articles/hdinsight-upload-data/)
* [Uso de MapReduce con
  HDInsight](/en-us/documentation/articles/hdinsight-use-mapreduce)
* [Uso de Hive con
  HDInsight](/en-us/documentation/articles/hdinsight-use-hive/)
* [Uso de Pig con
  HDInsight](/en-us/documentation/articles/hdinsight-use-pig/)
* [Uso de Oozie con
  HDInsight](/en-us/documentation/articles/hdinsight-use-oozie/)
* [Desarrollo de programas de streaming de Hadoop C# para
  HDInsight](/en-us/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
* [Desarrollo de programas MapReduce de Java para
  HDInsight](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)



[1]: http://hadoop.apache.org/
[2]: https://www.windowsazure.com/en-us/pricing/purchase-options/
[3]: https://www.windowsazure.com/en-us/pricing/member-offers/
[4]: https://www.windowsazure.com/en-us/pricing/free-trial/
[5]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[6]: https://manage.windowsazure.com/
[7]: http://www.microsoft.com/en-us/download/details.aspx?id=39379