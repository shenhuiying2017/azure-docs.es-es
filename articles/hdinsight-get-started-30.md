<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started using Hadoop 2.2 clusters with HDInsight | Azure" metaKeywords="" description="Get started using Hadoop 2.2 clusters with HDInsight, a big data solution. Learn how to provision clusters, run MapReduce jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Azure HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Introducción al uso de clústeres de Hadoop 2.2 con HDInsight
============================================================

HDInsight pone [Apache Hadoop](http://hadoop.apache.org/) a disposición de los usuarios como servicio en la nube. De esta forma, el marco de software de MapReduce está disponible en un entorno de Azure más sencillo, escalable y rentable. HDInsight también ofrece una solución rentable para la administración y el almacenamiento de datos mediante el almacenamiento de blobs de Azure.

En este tutorial se aprovisionará un clúster de HDInsight a través del Portal de administración de Azure, se enviará un trabajo de MapReduce de Hadoop mediante PowerShell y, después, se importarán los datos resultantes del trabajo de MapReduce en Excel para su análisis.

> [WACOM.NOTE] En este tutorial se trata el uso de los clústeres de Hadoop 2.2 en HDInsight. Para ver el tutorial acerca del uso de clústeres de Hadoop 1.2 en HDInsight, consulte [Introducción al uso de HDInsight de Azure](/en-us/documentation/articles/hdinsight-get-started/).

> [WACOM.NOTE] La sintaxis *asv://* no es compatible con la versión 3.0 de los clústeres de HDInsight y tampoco será compatible con las versiones futuras. Debería usarse la sintaxis *wasb://* en lugar de la anterior.

Además de poner HDInsight de Azure a disposición de los usuarios, Microsoft también ha lanzado el emulador de HDInsight para Azure, anteriormente conocido como Microsoft HDInsight Developer Preview. Este producto está destinado a los desarrolladores y, como tal, solo admite implementaciones de un solo nodo. Para usar el emulador de HDInsight, consulte [Introducción al emulador de HDInsight](/en-us/documentation/articles/hdinsight-get-started-emulator/).

**Requisitos previos:**

Antes de empezar este tutorial, debe tener lo siguiente:

-   Una suscripción de Azure. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra](https://www.windowsazure.com/en-us/pricing/purchase-options/), [Ofertas para miembros](https://www.windowsazure.com/en-us/pricing/member-offers/) o [Evaluación gratuita](https://www.windowsazure.com/en-us/pricing/free-trial/).
-   Un equipo que ejecute Windows 8, Windows 7, Windows Server 2012 o Windows Server 2008 R2. Este equipo se usará para enviar trabajos de MapReduce.
-   Office Professional Plus 2013, Office 365 Pro Plus, Excel 2013 Standalone u Office Professional Plus 2010.

**Duración aproximada:** 30 minutos

Apartados de este tutorial
--------------------------

-   [Configuración de un entorno local para ejecutar PowerShell](#setup)
-   [Aprovisionamiento de un clúster de HDInsight](#provision)
-   [Ejecución de un trabajo WordCount de MapReduce](#sample)
-   [Conexión a las herramientas de inteligencia empresarial de Microsoft](#powerquery)
-   [Pasos siguientes](#nextsteps)

Configuración de un entorno local para ejecutar PowerShell
----------------------------------------------------------

Existen varias formas de enviar trabajos de MapReduce a HDInsight. En este tutorial se usará Azure PowerShell. Para instalar Azure PowerShell, ejecute el [instalador de plataforma web de Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Cuando se le solicite, haga clic en **Ejecutar**, a continuación, en **Instalar** y después siga las instrucciones. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

Los cmdlets de PowerShell requieren información de la suscripción para utilizarse a fin de administrar los servicios.

**Para conectarse a su suscripción con Azure AD**

1.  Abra la consola de Azure PowerShell siguiendo las instrucciones del apartado [Instalación de Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/#install).
2.  Ejecute el siguiente comando:

         Add-AzureAccount

3.  En la ventana, escriba la dirección de correo electrónico y la contraseña asociadas a su cuenta. Azure autentica y guarda las credenciales y, a continuación, cierra la ventana.

Como alternativa para conectarse a la suscripción, puede usar el método del certificado. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

Aprovisionamiento de un clúster de HDInsight
--------------------------------------------

El proceso de aprovisionamiento de HDInsight requiere el uso de una cuenta de almacenamiento de Azure como sistema de archivos predeterminado. Dicha cuenta debe estar ubicada en el mismo centro de datos que los recursos de proceso de HDInsight. En la actualidad, solo pueden aprovisionarse clústeres de HDInsight en los siguientes centros de datos:

-   Sudeste asiático
-   Europa del Norte
-   Europa occidental
-   Este de EE. UU.
-   Oeste de EE. UU.

Debe elegir uno de estos cinco centros de datos para la cuenta de almacenamiento de Azure.

**Para crear una cuenta de Almacenamiento de Azure**

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).
2.  Haga clic en **NEW** en la esquina inferior izquierda, seleccione **DATA SERVICES**, **STORAGE** y, a continuación, haga clic en **QUICK CREATE**.

    ![HDI.StorageAccount.QuickCreate](./media/hdinsight-get-started-3.0/HDI.StorageAccount.QuickCreate.png)

3.  Escriba los detalles de **URL**, **LOCATION** y **REPLICATION** y, a continuación, haga clic en **CREATE STORAGE ACCOUNT**. No se admiten grupos de afinidad. La nueva cuenta de almacenamiento aparecerá en la lista de almacenamiento.
4.  Espere hasta que la característica **STATUS** de la nueva cuenta de almacenamiento cambie a **Online**.
5.  Haga clic en la nueva cuenta de almacenamiento en la lista para seleccionarla.
6.  Haga clic en **MANAGE ACCESS KEYS** en la parte inferior de la página.
7.  Tome nota de los valores de los campos **STORAGE ACCOUNT NAME** y **PRIMARY ACCESS KEY**. Los necesitará más adelante en el tutorial.

Para obtener instrucciones detalladas, consulte [Creación de una cuenta de almacenamiento](/en-us/documentation/articles/storage-create-storage-account/) y [Uso del almacenamiento de blobs de Azure con HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/).

El aprovisionamiento de clústeres de HDInsight 3.0 es solo compatible actualmente mediante la opción de creación personalizada.

**Para aprovisionar un clúster de HDInsight**

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).

2.  Haga clic en **HDINSIGHT** en la izquierda para ver los clústeres de HDInsight en la cuenta. En la captura de pantalla siguiente no hay ningún clúster de HDInsight.

    ![HDI.ClusterStatus](./media/hdinsight-get-started-3.0/HDI.ClusterStatus.png)

3.  Haga clic en **New** en la esquina inferior izquierda y, después, en **Servicios de datos**, **HDInsight** y en **Creación personalizada**.

    ![ClústerCreaciónPersonalizada.HDI](./media/hdinsight-get-started-3.0/HDI.CustomCreateCluster.png)

4.  En la pestaña de detalles del clúster, escriba o seleccione los valores siguientes:

  <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><th data-morhtml="true">Nombre</th><th data-morhtml="true">Valor</th></tr>
 <tr data-morhtml="true"><td data-morhtml="true"><strong data-morhtml="true">Cluster Name</strong></td><td data-morhtml="true">Nombre del cl&uacute;ster.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true"><strong data-morhtml="true">Nodos de datos</strong></td><td data-morhtml="true">N&uacute;mero de nodos de datos que desea implementar. Para prop&oacute;sitos de prueba, cree un cl&uacute;ster de un solo nodo. <br data-morhtml="true" />El l&iacute;mite del tama&ntilde;o del cl&uacute;ster var&iacute;a seg&uacute;n las suscripciones a Azure. P&oacute;ngase en contacto con el servicio de soporte relacionado con la facturaci&oacute;n de Azure para aumentar el l&iacute;mite.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true"><strong data-morhtml="true">Versi&oacute;n de HDInsight</strong></td><td data-morhtml="true">Seleccione <strong data-morhtml="true">3.0</strong> para crear un cl&uacute;ster de Hadoop 2.2 en HDInsight.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true"><strong data-morhtml="true">Region</strong></td><td data-morhtml="true">Seleccione la misma regi&oacute;n que la cuenta de almacenamiento que cre&oacute; en el &uacute;ltimo procedimiento. HDInsight requiere que la cuenta de almacenamiento se encuentre en la misma regi&oacute;n. Posteriormente, en la configuraci&oacute;n, puede seleccionar solo una cuenta de almacenamiento que se encuentre en la misma regi&oacute;n que especific&oacute; aqu&iacute;.
 </td></tr>
 </table>

5.  Haga clic en la flecha derecha en la esquina inferior derecha para configurar el usuario del clúster.
6.  En la pestaña para configurar usuario de clúster, especifique el **nombre de usuario** y la **contraseña** para la cuenta del usuario del clúster de HDInsight. Además de esta cuenta, puede crear una cuenta de usuario de RDP después de que se haya realizado el aprovisionamiento del clúster, por lo que puede disponer de un escritorio remoto en el clúster. Para ver las instrucciones, consulte [Administración de HDInsight mediante el portal de administración](/en-us/documentation/articles/hdinsight-administer-use-management-portal/).
7.  Haga clic en la flecha derecha en la esquina inferior derecha para configurar la cuenta de almacenamiento.
8.  En la etiqueta de cuenta de almacenamiento, escriba o seleccione los valores siguientes:

    <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><th data-morhtml="true">Nombre</th><th data-morhtml="true">Valor</th></tr>
 <tr data-morhtml="true"><td data-morhtml="true">STORAGE ACCOUNT</td><td data-morhtml="true">Seleccione <strong data-morhtml="true">Usar almacenamiento existente</strong>. Tambi&eacute;n tiene la opci&oacute;n de usar el portal de administraci&oacute;n para crear una nueva cuenta de almacenamiento si no ha creado una.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">ACCOUNT NAME</td><td data-morhtml="true">Especifique la cuenta de almacenamiento que cre&oacute; en el &uacute;ltimo procedimiento de este tutorial. Tenga en cuenta que solo se muestran las cuentas de almacenamiento de la misma regi&oacute;n en el cuadro de lista.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">DEFAULT CONTAINER</td><td data-morhtml="true">Seleccione <strong data-morhtml="true">Crear contenedor predeterminado</strong>. Cuando se seleccione esta opci&oacute;n, el nombre del contenedor predeterminado tendr&aacute; el mismo nombre que el cl&uacute;ster.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">ADDITIONAL STORAGE ACCOUNT</td><td data-morhtml="true">Seleccione <strong data-morhtml="true">0</strong>. Tiene la opci&oacute;n de conectar el cl&uacute;ster a hasta 7 cuentas de almacenamiento adicionales.</td></tr>
 </table>

9.  Haga clic en el icono de verificación de la esquina inferior derecha para crear el clúster. Una vez completado el proceso de aprovisionamiento, la columna de estado mostrará **Running**.

Ejecución de un trabajo WordCount de MapReduce
----------------------------------------------

Ahora ya se ha aprovisionado un clúster de HDInsight. El paso siguiente consiste en ejecutar un trabajo de MapReduce para contar las palabras de un archivo de texto.

Para ejecutar un trabajo de MapReduce se requieren los siguientes elementos:

-   Un programa de MapReduce. En este tutorial se usará el ejemplo WordCount incluido con la distribución del clúster de HDInsight, por lo que no tendrá que escribir uno propio. Se encuentra en */example/jars/hadoop-mapreduce-examples.jar*. Para obtener instrucciones acerca de cómo escribir su propio trabajo de MapReduce, consulte [Desarrollo de programas MapReduce de Java para HDInsight](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/).

-   Un archivo de entrada. Se usará */example/data/gutenberg/davinci.txt* como este tipo de archivo. Para obtener información acerca de cómo cargar archivos, consulte [Carga de datos en HDInsight](/en-us/documentation/articles/hdinsight-upload-data/).
-   Una carpeta de archivo de salida. Se usará */example/data/WordCountOutput* como la carpeta mencionada. El sistema creará la carpeta en caso de que esta no exista.

El esquema URI para obtener acceso a los archivos del almacenamiento de blobs es:

    wasb[s]://<containername>@<storageaccountname>.blob.core.windows.net/<path>

> [WACOM.NOTE] De forma predeterminada, el contenedor de blobs usado para el sistema de archivos predeterminado tiene el mismo nombre que el clúster de HDInsight.

El esquema de URI proporciona tanto acceso no cifrado con el prefijo *wasb:* como acceso SSL cifrado con wasbs. Se recomienda usar wasbs siempre que sea posible, incluso al obtener acceso a los datos que residen en el mismo centro de datos de Azure.

Dado que HDInsight usa un contenedor de almacenamiento de blobs como sistema de archivos predeterminado, puede consultar los archivos y directorios de dicho sistema de archivos mediante rutas de acceso relativas o absolutas.

Por ejemplo, para obtener acceso al archivo hadoop-mapreduce-examples.jar, puede usar una de las siguientes opciones:

	● wasb://<containername>@<storageaccountname>.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	● wasb:///example/jars/hadoop-mapreduce-examples.jar
	● /example/jars/hadoop-mapreduce-examples.jar

El uso del prefijo *wasb://* en las rutas de acceso de estos archivos es necesario para indicar que el almacenamiento de blobs de Azure se está usando para los archivos de entrada y salida. El directorio de salida asume una ruta de acceso relativa predeterminada a la carpeta *wasb:///user/&lt;nombreusuario\>*.

Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/).

**Para ejecutar el ejemplo WordCount**

1.  Abra **Azure PowerShell**. Para obtener instrucciones acerca de cómo abrir la ventana de la consola de Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

2.  Ejecute los siguientes comandos para establecer las variables:

         $subscriptionName = "<SubscriptionName>" 
         $clusterName = "<HDInsightClusterName>"        

3.  Ejecute los siguientes comandos para crear una definición del trabajo de MapReduce:

         # Defina el trabajo de MapReduce
         $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    El archivo hadoop-mapreduce-examples.jar se incluye con la distribución del clúster de HDInsight. Existen dos argumentos para el trabajo de MapReduce. El primero es el nombre del archivo de origen y, el segundo, la ruta de acceso del archivo de salida. El archivo de origen se incluye con la distribución del clúster de HDInsight y la ruta de acceso del archivo de salida se creará en tiempo de ejecución.

4.  Ejecute el siguiente comando para enviar el trabajo de MapReduce:

         # Envíe el trabajo
         Select-AzureSubscription $subscriptionName
         $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

    Además de la definición de trabajo de MapReduce, también debe proporcionarse el nombre del clúster de HDInsight en el que se desea ejecutar el trabajo de MapReduce.

    *Start-AzureHDInsightJob* es una llamada no sincronizada. Para comprobar la finalización del trabajo, use el cmdlet *Wait-AzureHDInsightJob*.

5.  Ejecute el siguiente comando para comprobar la finalización del trabajo de MapReduce:

         Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

6.  Ejecute el siguiente comando para comprobar posibles errores al ejecutar el trabajo de MapReduce:

         # Obtenga la salida del trabajo
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

    En la captura de pantalla siguiente se muestra el resultado de una ejecución correcta. En caso contrario, aparecerán mensajes de error.

    ![HDI.GettingStarted.RunMRJob](./media/hdinsight-get-started-3.0/HDI.GettingStarted.RunMRJob.png)

**Para recuperar los resultados del trabajo de MapReduce**

1.  Abra **Azure PowerShell**.
2.  Ejecute los siguientes comandos para crear una carpeta C:\\Tutorials y cambie el directorio a la carpeta:

         mkdir \Tutorials
         cd \Tutorials

    El directorio predeterminado de Azure Powershell es *C:\\Windows\\System32\\WindowsPowerShell\\v1.0*. De forma predeterminada, no tiene permiso de escritura en esta carpeta. Debe cambiar el directorio a una carpeta en la que tenga permiso de escritura.

3.  Establezca tres variables en los comandos siguientes y, a continuación, ejecútelos:

         $subscriptionName = "<SubscriptionName>"       
         $storageAccountName = "<StorageAccountName>"   
         $containerName = "<ContainerName>"              

    La cuenta de almacenamiento de Azure es la misma que se creó anteriormente en el tutorial. Esta se usa para hospedar el contenedor de blobs utilizado como sistema de archivos predeterminado del clúster de HDInsight. El nombre del contenedor de almacenamiento de blobs suele coincidir con el del clúster de HDInsight, a menos que se especifique un nombre distinto durante el aprovisionamiento del clúster.

4.  Ejecute los siguientes comandos para crear un objeto de contexto de almacenamiento de Azure:

         # Crear el objeto de contexto de la cuenta de almacenamiento
         Select-AzureSubscription $subscriptionName
         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    *Select-AzureSubscription* se usa para establecer la suscripción actual en caso de tener varias y no usar la suscripción predeterminada.

5.  Ejecute el siguiente comando para descargar el resultado del trabajo de MapReduce del contenedor de blobs a la estación de trabajo:

         # Descargar el resultado del trabajo en la estación de trabajo
         Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    *example/data/WordCountOutput* es la carpeta de salida especificada al ejecutar el trabajo de MapReduce. *part-r-00000* es el nombre de archivo predeterminado para el resultado del trabajo de MapReduce. El archivo se descargará a la misma estructura de carpetas de la carpeta local. Por ejemplo, en la captura de pantalla siguiente, la carpeta actual es la carpeta raíz C. El archivo se descargará en la carpeta *C:\\example\\data\\WordCountOutput\\*.

6.  Ejecute el siguiente comando para imprimir el archivo de salida del trabajo de MapReduce:

         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    ![HDI.GettingStarted.MRJobOutput](./media/hdinsight-get-started-3.0/HDI.GettingStarted.MRJobOutput.png)

    El trabajo de MapReduce genera un archivo denominado *part-r-00000* con las palabras y los recuentos. El script usa el comando findstr para enumerar todas las palabras que contienen *"there"*.

> [WACOM.NOTE] Si se abre en el Bloc de notas *./example/data/WordCountOutput/part-r-00000*, resultado de varias líneas de un trabajo de MapReduce, observará que los saltos de línea no se representan correctamente. Se espera que esto sea así.

Conexión a las herramientas de inteligencia empresarial de Microsoft
--------------------------------------------------------------------

El complemento Power Query de Excel se puede usar para exportar los resultados de HDInsight a Excel y aplicarles las herramientas de Microsoft Business Intelligence (BI) para seguir procesándolos o mostrar los resultados. Al crear un clúster de HDInsight, se creó un contenedor predeterminado con el mismo nombre que dicho clúster en la cuenta de almacenamiento asociada durante la creación. Este se rellena automáticamente con un conjunto de archivos. Uno de estos archivos es una tabla de Hive de ejemplo. En esta sección se mostrará cómo importar en Excel los datos incluidos en dicha tabla para poder verlos y procesarlos aún más.

Para completar esta parte del tutorial, debe tener instalado Excel 2010 o 2013. Aquí importaremos la tabla de Hive que se incluye con HDInsight.

**Para descargar Microsoft Power Query para Excel**

-   Descargue Microsoft Power Query para Excel en el [Centro de descarga de Microsoft](http://www.microsoft.com/en-us/download/details.aspx?id=39379) e instálelo.

**Para importar datos de HDInsight**

1.  Abra Excel y cree un libro en blanco.
2.  Haga clic en el menú **Power Query**, en **From Other Sources** y, a continuación, en **From Azure HDInsight**.

    ![HDI.GettingStarted.PowerQuery.ImportData](./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData.png)

3.  En **Account Name**, escriba el nombre de la cuenta de almacenamiento de blobs de Azure asociada con su clúster y, a continuación, haga clic en **Aceptar**. Esta es la cuenta de almacenamiento creada previamente en el tutorial.
4.  En **Account Key**, escriba la clave de la cuenta de almacenamiento de blobs de Azure y, a continuación, haga clic en **Guardar**.
5.  En el panel de navegación de la derecha, haga doble clic en el nombre del contenedor de almacenamiento de blobs. De forma predeterminada, el nombre del contenedor es el mismo que el del clúster.

6.  Busque **part-r-00000** en la columna **Name** (la ruta de acceso es *.../example/data/WordCountOutput*) y, a continuación, haga clic en **Binary** a la izquierda de **part-r-00000**.

    ![HDI.GettingStarted.PowerQuery.ImportData2](./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData2.png)

7.  Haga clic con el botón secundario en **Column1.1** y seleccione **Rename**.
8.  Cambie el nombre a **Word**.
9.  Repita el proceso para cambiar el nombre de **Column1.2** a **Count**.

    ![HDI.GettingStarted.PowerQuery.ImportData3](./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData3.png)

10. Haga clic en la opción de aplicar y cerrar& en la esquina superior izquierda. La consulta importa la tabla de Hive en Excel.

Pasos siguientes
----------------

En este tutorial ha aprendido cómo aprovisionar un clúster con HDInsight, cómo ejecutar un trabajo de MapReduce en este y cómo importar los resultados en Excel para procesarlos mejor y representarlos gráficamente mediante las herramientas de BI. Para obtener más información, consulte los artículos siguientes:

-   [Introducción a HDInsight](/en-us/documentation/articles/hdinsight-get-started/)
-   [Introducción al emulador de HDInsight](/en-us/documentation/articles/hdinsight-get-started-emulator/)
-   [Uso del almacenamiento de blobs de Azure con HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/)
-   [Administración de HDInsight con PowerShell](/en-us/documentation/articles/hdinsight-administer-use-powershell/)
-   [Carga de datos en HDInsight](/en-us/documentation/articles/hdinsight-upload-data/)
-   [Uso de Hive con HDInsight](/en-us/documentation/articles/hdinsight-use-hive/)
-   [Uso de Pig con HDInsight](/en-us/documentation/articles/hdinsight-use-pig/)
-   [Uso de Oozie con HDInsight](/en-us/documentation/articles/hdinsight-use-oozie/)
-   [Desarrollo de programas de MapReduce de streaming de Hadoop C\# para HDInsight](/en-us/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [Desarrollo de programas MapReduce de Java para HDInsight](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)

