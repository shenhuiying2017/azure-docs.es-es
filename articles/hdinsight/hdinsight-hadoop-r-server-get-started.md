---
title: "Introducción a R Server en HDInsight | Microsoft Docs"
description: "Obtenga información sobre cómo crear un motor Apache Spark en un clúster de HDInsight que incluya R Server y después enviar un script de R al clúster."
services: HDInsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/09/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 888a818bc0ea1804366dcb3932ac0e3f7b5802c6
ms.openlocfilehash: 51341d933525ef77d1f9ab604b212854b447ebc5


---
# <a name="get-started-using-r-server-on-hdinsight"></a>Introducción al uso del servidor de R en HDInsight
HDInsight incluye una opción de R Server que se integra en el clúster de HDInsight. Esto permite que los scripts de R usen Spark y MapReduce para ejecutar cálculos distribuidos. En este documento, obtendrá información sobre cómo crear un nuevo R Server en el clúster de HDInsight y después ejecutar un script de R que demuestre el uso de Spark para cálculos de R distribuidos.

## <a name="prerequisites"></a>Requisitos previos
* **Una suscripción a Azure**: antes de comenzar este tutorial, debe tener una suscripción a Azure. Consulte [Obtener una evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) para obtener más información.
* **Un cliente de Secure Shell (SSH)**: el cliente de SSH se usa para conectarse al clúster de HDInsight de forma remota y ejecutar comandos directamente desde el clúster. Los sistemas Linux, Unix y OS X proporcionan un cliente de SSH a través del comando `ssh` . Para sistemas Windows, se recomienda [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

  * **Claves SSH (opcional)**: puede proteger la cuenta de SSH usada para conectarse al clúster mediante una contraseña o una clave pública. Usar una contraseña es más sencillo y le permite empezar sin tener que crear un par de clave pública o privada. Sin embargo, el uso de una clave es más seguro.

      Los pasos de este documento asumen que está usando una contraseña. Para obtener información sobre cómo crear y usar claves de SSH con HDInsight, consulte los siguientes documentos:

    * [Use SSH with HDInsight from Linux, Unix, or OS X clients (Uso de SSH con HDInsight desde clientes de Linux, Unix u OS X)](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Uso de SSH con HDInsight desde clientes de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

### <a name="access-control-requirements"></a>Requisitos de control de acceso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-the-cluster"></a>Creación de clústeres
> [!NOTE]
> Los pasos de este documento le ofrecerán información sobre cómo crear un R Server en el clúster de HDInsight con la información de configuración básica. Para obtener otras opciones de configuración del clúster (como agregar cuentas de almacenamiento adicional, usar una red virtual de Azure o crear una tienda de metadatos para Hive), consulte [Creación de clústeres de HDInsight basados en Linux](hdinsight-hadoop-provision-linux-clusters.md).
>
> 

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Seleccione **NUEVO**, **Inteligencia y análisis** y luego **HDInsight**.

    ![Imagen de la creación de un nuevo clúster](./media/hdinsight-getting-started-with-r/newcluster.png)

3. Escriba un nombre para el clúster en el campo **Nombre del clúster**. Si tiene varias suscripciones a Azure, use la entrada **Suscripción** para seleccionar la que quiera usar.

    ![Opciones de nombre de clúster y suscripción](./media/hdinsight-getting-started-with-r/clustername.png)

4. Seleccione **Configuración de clúster**. En la hoja **Configuración de clúster**, seleccione las opciones siguientes:

   * **Tipo de clúster**: R Server
   * **Versión**: seleccione la versión de R Server que se va a instalar en el clúster. Seleccione la última versión para obtener las funcionalidades más recientes. Las demás versiones están disponibles por si son necesarias para compatibilidad. Puede consultar las notas de la versión de cada una de las versiones disponibles [aquí](https://msdn.microsoft.com/en-us/microsoft-r/notes/r-server-notes).
   * **R Studio community edition for R Server**: este IDE basado en el explorador se instala de forma predeterminada en el nodo perimetral.  Si prefiere que no se instale, desactive la casilla. Si desea instalarlo, encontrará la dirección URL para acceder al inicio de sesión de RStudio Server en una hoja de aplicación del portal para el clúster, una vez que se haya creado.
     Deje las demás opciones con los valores predeterminados y use el botón **Seleccionar** para guardar el tipo de clúster.

     ![Captura de pantalla de la hoja del tipo de clúster](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)

   

5. Seleccione **Credenciales** y escriba un valor en **Nombre de usuario de inicio de sesión del clúster** y **Contraseña de inicio de sesión de clúster**.

    Especifique un valor en **Nombre de usuario de SSH**.  SSH se usa para conectarse de forma remota al clúster con un cliente de **Secure Shell (SSH)**. Puede especificar el usuario SSH en este cuadro de diálogo o una vez creado el clúster (pestaña de configuración del clúster). R Server está configurado para esperar el **nombre de usuario de SSH** "remoteuser".  Si utiliza un nombre de usuario diferente, tendrá que realizar un paso adicional después de crear el clúster.

    ![Hoja Credenciales](./media/hdinsight-getting-started-with-r/clustercredentials.png)

    **Tipo de autenticación SSH**: seleccione **CONTRASEÑA** como tipo de autenticación a menos que prefiera usar una clave pública.  Si desea acceder a R Server en el clúster a través de un cliente remoto como, por ejemplo, RTV, RStudio u otro IDE de escritorio, necesitará un par de claves pública y privada. Tenga en cuenta que debe elegir una contraseña SSH si instala RStudio Server community edition.     

    Para crear y usar un par de claves pública y privada seleccione "CLAVE PÚBLICA" y haga lo que se explica a continuación.  Estas instrucciones asumen que dispone de Cygwin con ssh-keygen o equivalente instalado.

   * Genere un par de claves pública y privada desde el símbolo del sistema en el equipo portátil:

        ssh-keygen -t rsa -b 2048 –f <nombreDeArchivoDeClavePrivada>

   * Esto creará un archivo de clave privada y un archivo de clave pública con el nombre <nombreDeArchivoDeClavePrivada>.pub, por ejemplo, davec y davec.pub.  A continuación, especifique el archivo de clave pública (*. pub) al asignar credenciales de clúster HDI:

     ![Hoja Credenciales](./media/hdinsight-getting-started-with-r/publickeyfile.png)  

   * Cambie los permisos en el archivo de clave privada en el equipo portátil

        chmod 600 <nombreDeArchivoDeClavePrivada>

   * Utilice el archivo de clave privada con SSH para el inicio de sesión remoto, por ejemplo.

        ssh –i <nombreDeArchivoDeClavePrivada> remoteuser@<hostname public ip>

     O como parte de la definición del contexto de proceso de Hadoop Spark para R Server en el cliente (consulte Using Microsoft R Server as a Hadoop Client [Uso de Microsoft R Server como cliente de Hadoop] en la sección [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) [Creación de un contexto de proceso para Spark] del [documento de introducción a SacaleR en Apache Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)).

6. Seleccione **Origen de datos** para seleccionar un origen de datos como la ubicación principal del sistema de archivos HDFS utilizado por el clúster. Seleccione una cuenta de Azure Storage nueva o existente o una cuenta de almacenamiento de Data Lake existente.

   1. Si selecciona una cuenta de Azure Storage, seleccione una cuenta de almacenamiento existente mediante **Seleccionar cuenta de almacenamiento** y seleccione la cuenta o cree una con el vínculo **Crear nueva** en la sección **Seleccionar cuenta de almacenamiento**.

      > [!NOTE]
      > Si selecciona **Nuevo**, debe escribir un nombre para la nueva cuenta de almacenamiento. Si el nombre está aceptado, aparecerá una marca de comprobación verde.

      Se usará el nombre del clúster de forma predeterminada en **Contenedor predeterminado**. Déjelo como valor.

      Si se ha seleccionado una nueva opción de cuenta de almacenamiento, seleccione **Ubicación** para seleccionar la región en la que crear la cuenta de almacenamiento.

      > [!IMPORTANT]
      > Seleccionar la ubicación del origen de datos predeterminado también establecerá la ubicación del clúster de HDInsight. El origen de datos predeterminado y el clúster deben estar en la misma región.

   2. Si selecciona el uso de un Data Lake Store existente, seleccione la cuenta de almacenamiento ADLS que usar y agregue la identidad de ADD de clúster al clúster para permitir el acceso al almacén.  Para más información sobre este proceso, consulte [Creación de un clúster de HDInsight con Data Lake Store mediante Azure Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal).

      Use el botón **Seleccionar** para guardar la configuración del origen de datos.

       ![Hoja Origen de datos](./media/hdinsight-getting-started-with-r/datastore.png)

7. Seleccione **Planes de tarifa de nodo** para mostrar información sobre los nodos que se crearán para este clúster. A menos que sepa que necesitará un clúster de mayor tamaño, deje el número de nodos de trabajo en el valor predeterminado de `4`. El costo estimado del clúster se mostrará en la hoja.

   > [!NOTE]
   > Si es necesario, puede volver a cambiar el tamaño del clúster más adelante mediante el Portal (Clúster -> Configuración -> Escalar clúster) para aumentar o disminuir el número de nodos de trabajo.  Esto puede ser útil para desactivar el clúster cuando no esté en uso o para agregar más capacidad para satisfacer las necesidades de las tareas más grandes.
   >
   > 

    Para cambiar el tamaño del clúster, los nodos de datos y el nodo perimetral hay que tener en cuenta los siguientes factores:  

   * El rendimiento de los análisis distribuidos de R Server en Spark es proporcional al número de nodos de trabajo si el volumen de datos es elevado.  

   * El rendimiento de los análisis de R Server es lineal según el tamaño de los datos analizados. Por ejemplo:  

     * Para datos de tamaño reducido y medio, el rendimiento será mayor si se analizan en un contexto de proceso local del nodo perimetral.  Para más información sobre los escenarios en los que los contextos de proceso local y de Spark funcionan mejor, consulte Opciones de contexto de proceso para R Server en HDInsight.<br>
     * Si inicia sesión en el nodo perimetral y ejecuta el script de R allí, se ejecutarán todas las funciones rx de ScaleR <strong>localmente</strong> en el nodo perimetral, por lo que la memoria y el número de núcleos de este nodo deben ajustarse según corresponda. Lo mismo se aplica si utiliza R Server en HDI como contexto de proceso remoto desde el equipo portátil.

     ![Hoja Niveles de precios de nodo](./media/hdinsight-getting-started-with-r/pricingtier.png)

     Use el botón **Seleccionar** para guardar la configuración de precios del nodo.

8. Seleccione el **grupo de recursos** que usar seleccionando un grupo de recursos existente o creando uno nuevo. Si selecciona **Crear nuevo**, escriba el nombre del nuevo grupo de recursos. Aparecerá una marca de comprobación verde para indicar que el nuevo nombre de grupo está disponible.

   ![Hoja Niveles de precios de nodo](./media/hdinsight-getting-started-with-r/useexistingrg.png)

   

9. Después de revisar las selecciones, es el momento de crear el clúster. Seleccione **Anclar a Panel de inicio** y, luego, **Crear**. Esto creará el clúster y agregará un icono para él en el panel de inicio de su Portal de Azure.

   Podrá observar que hay también un vínculo para **Opciones de automatización**. Al hacer clic en este vínculo se mostrarán los scripts que pueden utilizarse para automatizar la creación de un clúster con la configuración seleccionada. Estos scripts también están disponibles en la entrada de Azure Portal para el clúster una vez que se ha creado.

   | Al crear                           | Creación completa                        |
   | ---------------------------------------- | ---------------------------------------- |
   | ![Indicador de creación en el Panel de inicio](./media/hdinsight-getting-started-with-r/provisioning.png) | ![Icono de clúster creado](./media/hdinsight-getting-started-with-r/provisioned.png) |

   > [!NOTE]
   > El clúster tardará algo de tiempo en crearse, normalmente unos 20 minutos. Use el icono del Panel de inicio o la entrada **Notificaciones** de la izquierda de la página para comprobar el proceso de creación.
   >
   > 

## <a name="connect-to-rstudio-server"></a>Conexión a RStudio Server

Si ha elegido incluir RStudio Server community edition en la instalación, puede acceder al inicio de sesión de RStudio por dos métodos diferentes.

1. Puede ir a la dirección URL siguiente (donde **CLUSTERNAME** es el nombre del clúster que creó): 

    https://**CLUSTERNAME**.azurehdinsight.net/rstudio/

2. O puede abrir la entrada para el clúster en Azure Portal, seleccionar el vínculo rápido R Server Dashboards (Paneles de R Server) y seleccionar el panel de R Studio:

     ![Acceso al panel de R Studio](./media/hdinsight-getting-started-with-r/rstudiodashboard.png)

   > [!IMPORTANT]
   > Con independencia del método, la primera vez que inicie sesión deberá autenticarse dos veces.  Con la primera autenticación, proporcione el identificador de usuario de administrador del clúster y la contraseña. Con la segunda, proporcione el identificador de usuario de SSH y la contraseña. Posteriormente, solo serán necesarios el identificador de usuario y la contraseña SSH. 

## <a name="connect-to-the-r-server-edge-node"></a>Conectarse al nodo perimetral del servidor de R
Conectarse al nodo perimetral del servidor de R del clúster de HDInsight con SSH:

    ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

> [!NOTE]
> También puede encontrar la dirección `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` en Azure Portal al seleccionar su clúster y después **Todas las configuraciones**, **Aplicaciones** y **RServer**. Se mostrará la información del punto de conexión de SSH para el nodo perimetral.
>
> ![Imagen del punto de conexión SSH del nodo perimetral](./media/hdinsight-getting-started-with-r/sshendpoint.png)
>
> 

Si usó una contraseña para proteger la cuenta de usuario SSH, se le pedirá la contraseña. Si usa una clave pública, tal vez tenga que usar el parámetro `-i` para especificar la ruta de acceso a la correspondiente clave privada. Por ejemplo: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`.

Para obtener más información sobre el uso de SSH con HDInsight basado en Linux, vea los siguientes artículos:

* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

Una vez conectado, llegará a un símbolo de sistema similar al siguiente.

    username@ed00-myrser:~$

## <a name="use-the-r-console"></a>Usar la consola de R
1. En la sesión SSH, use el siguiente comando para iniciar la consola de R.

        R

    Verá un resultado similar al siguiente.

        R version 3.2.2 (2015-08-14) -- "Fire Safety"
        Copyright (C) 2015 The R Foundation for Statistical Computing
        Platform: x86_64-pc-linux-gnu (64-bit)
       
        R is free software and comes with ABSOLUTELY NO WARRANTY.
        You are welcome to redistribute it under certain conditions.
        Type 'license()' or 'licence()' for distribution details.
       
        Natural language support but running in an English locale
       
        R is a collaborative project with many contributors.
        Type 'contributors()' for more information and
        'citation()' on how to cite R or R packages in publications.
       
        Type 'demo()' for some demos, 'help()' for on-line help, or
        'help.start()' for an HTML browser interface to help.
        Type 'q()' to quit R.
       
        Microsoft R Server version 8.0: an enhanced distribution of R
        Microsoft packages Copyright (C) 2016 Microsoft Corporation
       
        Type 'readme()' for release notes.
       
        >
2. Desde el símbolo de sistema de `>` , puede escribir el código de R. El servidor de R incluye paquetes que le permiten interactuar con Hadoop y ejecutar cálculos distribuidos fácilmente. Por ejemplo, use el siguiente comando para ver la raíz del sistema de archivos predeterminado del clúster de HDInsight.

        rxHadoopListFiles("/")

    También puede usar el direccionamiento de estilo WASB.

        rxHadoopListFiles("wasbs:///")

## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Uso de R Server en HDI desde una instancia remota de Microsoft R Server o Microsoft R Client
Según la sección anterior sobre el uso de pares de claves públicas y privadas para acceder al clúster, se puede configurar el acceso al contexto de proceso de HDI Hadoop Spark desde una instancia remota de Microsoft R Server o Microsoft R Client que se ejecuta en un escritorio o equipo portátil. Consulte Using Microsoft R Server as a Hadoop Client (Uso de Microsoft R Server como cliente de Hadoop) en la sección [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Creación de un contexto de proceso para Spark) de la [guía de introducción en línea de RevoScaleR Hadoop Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started).  Para ello, debe especificar las siguientes opciones al definir el contexto de proceso RxSpark en el equipo portátil: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches y sshProfileScript. Por ejemplo:

    myNameNode <- "default"
    myPort <- 0 
    
    mySshHostname  <- 'rkrrehdi1-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key
    
    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")
    
    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )



## <a name="use-a-compute-context"></a>Usar un contexto de proceso
Un contexto de proceso le permite controlar si un cálculo se realizará de forma local en el nodo perimetral, o si se distribuirá en los nodos del clúster de HDInsight.

1. Desde la consola de R o R Server Studio (en una sesión SSH), use las opciones siguientes para cargar datos de ejemplo en el almacenamiento predeterminado de HDInsight.

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"
        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)
        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
        download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
        download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
        download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
        download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
        download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
        download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
        download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
        download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
        download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
        download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
        download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
        download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012") 
        # Make the directory
        rxHadoopMakeDir(inputDir)
        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. Tras esto, vamos a crear información de datos y a definir dos orígenes de datos, de modo que podamos trabajar con los datos.

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()
        # Create info list for the airline data
        airlineColInfo <- list(
            DAY_OF_WEEK = list(type = "factor"),
            ORIGIN = list(type = "factor"),
            DEST = list(type = "factor"),
            DEP_TIME = list(type = "integer"),
            ARR_DEL15 = list(type = "logical"))
       
        # get all the column names
        varNames <- names(airlineColInfo)
       
        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)
       
        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. Vamos a ejecutar una regresión lógica a través de los datos con el contexto de proceso local.

        # Set a local compute context
        rxSetComputeContext("local")
        # Run a logistic regression
        system.time(
            modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )
        # Display a summary 
        summary(modelLocal)

    Debería ver una salida que finalice con líneas similares a las siguientes.

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)
       
        Coefficients:
                        Estimate Std. Error z value Pr(>|z|)
        (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
        ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
        ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
        ......
        DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
        DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
        DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
        DEST=BPT         Dropped    Dropped Dropped  Dropped
        ---
        Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
       
        Condition number of final variance-covariance matrix: 11904202
        Number of iterations: 7

4. Después, ejecutaremos la misma regresión lógica usando el contexto de Spark. El contexto de Spark distribuirá el procesamiento en todos los nodos de trabajo del clúster de HDInsight.

        # Define the Spark compute context 
        mySparkCluster <- RxSpark()
        # Set the compute context 
        rxSetComputeContext(mySparkCluster)
        # Run a logistic regression 
        system.time(  
            modelSpark <- rxLogit(formula, data = airOnTimeData)
        )
        # Display a summary
        summary(modelSpark)

   > [!NOTE]
   > También puede usar MapReduce para distribuir el cálculo en los nodos del clúster. Para más información sobre el contexto de proceso, consulte [Opciones de contexto de proceso para R Server en HDInsight](hdinsight-hadoop-r-server-compute-contexts.md).
   >
   > 

## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuir el código de R a varios nodos
Con R Server puede seleccionar fácilmente el código de R existente y ejecutarlo en varios nodos del clúster mediante `rxExec`. Esto es útil cuando se realiza un barrido de parámetros o simulaciones. Aquí se ,muestra un ejemplo de cómo usar `rxExec`.

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Si sigue usando el contexto de Spark o MapReduce, se devolverá el valor nodename de los nodos de trabajo en los que se ejecuta el código `(Sys.info()["nodename"])`. Por ejemplo, en un clúster de cuatro nodos, puede recibir un resultado similar al siguiente.

    $rxElem1
        nodename
    "wn3-myrser"
    
    $rxElem2
        nodename
    "wn0-myrser"
    
    $rxElem3
        nodename
    "wn3-myrser"
    
    $rxElem4
        nodename
    "wn3-myrser"

## <a name="accessing-data-in-hive-and-parquet"></a>Acceso a datos en Hive y Parquet
Una nueva característica disponible en R Server 9.0 y versiones posteriores permite el acceso directo a los datos en Hive y Parquet para usarlos en funciones ScaleR en el contexto de proceso de Spark. Estas funcionalidades están disponibles por medio de las nuevas funciones de origen de datos ScaleR llamadas RxHiveData y RxParquetData. Estas emplean Spark SQL para cargar datos directamente en un elemento DataFrame de Spark para analizarlos con ScaleR.  

En los siguientes ejemplos de código, se muestra el uso de las nuevas funciones: 



```
#..create a Spark compute context

myHadoopCluster <- rxSparkConnect(reset = TRUE)
```


```
#..retrieve some sample data from Hive and run a model 

hiveData <- RxHiveData("select * from hivesampletable", 
                 colInfo = list(devicemake = list(type = "factor")))
rxGetInfo(hiveData, getVarInfo = TRUE)

rxLinMod(querydwelltime ~ devicemake, data=hiveData)
```


```
#..retrieve some sample data from Parquet and run a model 

rxHadoopMakeDir('/share')
rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
pqData <- RxParquetData('/share/claimsParquet',
                 colInfo = list(
            age    = list(type = "factor"),
           car.age = list(type = "factor"),
              type = list(type = "factor")
         ) )
rxGetInfo(pqData, getVarInfo = TRUE)

rxNaiveBayes(type ~ age + cost, data = pqData)
```


``` 
#..check on Spark data objects, cleanup, and close the Spark session 

lsObj <- rxSparkListData() # two data objs are cached
lsObj
rxSparkRemoveData(lsObj)
rxSparkListData() # it should show empty list
rxSparkDisconnect(myHadoopCluster)
```

Para más información sobre el uso de estas nuevas funciones, consulte la Ayuda en pantalla de R Server mediante los comandos ?RxHivedata y ?RxParquetData.  


## <a name="install-r-packages"></a>Instalar paquetes de R
Si quiere instalar más paquetes de R en el nodo perimetral, puede usar `install.packages()` directamente desde la consola de R cuando se conecte al nodo perimetral a través de SSH. Sin embargo, si necesita instalar paquetes de R en los nodos de trabajo del clúster, debe usar Acción de script.

Acciones de script son scripts de Bash que se usan para realizar cambios en la configuración del clúster de HDInsight o para instalar software adicional. En este caso, para instalar paquetes de R adicionales. Para instalar paquetes adicionales con una Acción de script, use los pasos siguientes.

> [!IMPORTANT]
> Usar Acciones de script para instalar paquetes de R adicionales solo se permite una vez creado el clúster. No debe usarse durante la creación del clúster, ya que el script cuenta con que el servidor de R esté completamente configurado e instalado.
>
> 

1. En el [Portal de Azure](https://portal.azure.com), seleccione R Server en el clúster de HDInsight.
2. En la hoja **Configuración**, seleccione **Acciones de script** y, luego, **Enviar nuevo** para enviar una nueva acción de script.

    ![Imagen de la hoja Acciones de script](./media/hdinsight-getting-started-with-r/newscriptaction.png)
3. En la hoja **Enviar acción de script**, proporcione la siguiente información.

   * **Nombre**: nombre descriptivo para identificar este script.

   * **URI de script de Bash**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Principal**: debe estar **desactivado**.

   * **Trabajador**: debe estar **activado**.

   * **Nodos perimetrales**: debe estar **desactivado**.

   * **Zookeeper**: debe estar **desactivado**.

   * **Parámetros**: paquetes de R que se van a instalar. Por ejemplo, `bitops stringr arules`

   * **Continuar con esta acción de script…**: debe estar **activado**.  

     > [!NOTE]
     > 1. De forma predeterminada, todos los paquetes de R se instalan desde una instantánea del repositorio Microsoft MRAN coherente con la versión del servidor R que se ha instalado.  Si desea instalar las versiones más recientes de los paquetes, existen algunos riesgos de incompatibilidad; no obstante, es posible hacerlo especificando `useCRAN` como primer elemento de la lista de paquetes, por ejemplo, `useCRAN bitops, stringr, arules`.  
     > 2. Algunos paquetes de R requieren otras bibliotecas de sistema de Linux. Por comodidad, hemos instalado previamente las dependencias que necesitan los 100 paquetes de R más populares. Sin embargo, si los paquetes de R que instale necesitan otras bibliotecas, debe descargar el script base usado aquí y realizar los pasos para instalar las bibliotecas del sistema. A continuación, debe cargar el script modificado a un contenedor de blobs público en el almacenamiento de Azure y usar el script modificado para instalar los paquetes.
     >    Para más información sobre cómo desarrollar acciones de script, consulte [Desarrollo de acciones de script](hdinsight-hadoop-script-actions-linux.md).  
     >
     >

     ![Agregar una Acción de script](./media/hdinsight-getting-started-with-r/scriptaction.png)
     
4. Seleccione **Crear** para ejecutar el script. Una vez que finalice el script, los paquetes de R estarán disponibles en todos los nodos de trabajo.

## <a name="next-steps"></a>Pasos siguientes
Ahora que sabe cómo crear un nuevo clúster de HDInsight que incluya un servidor de R y los aspectos básicos del uso de la consola de R desde una sesión de SSH, use las siguientes opciones para descubrir otras formas de trabajar con el servidor de R en HDInsight.

* [Agregar R Server Studio a HDInsight (si no está instalado durante la creación del clúster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Opciones de contexto de proceso para R Server en HDInsight (versión preliminar)](hdinsight-hadoop-r-server-compute-contexts.md)
* [Opciones de Azure Storage para R Server en HDInsight](hdinsight-hadoop-r-server-storage.md)




<!--HONumber=Dec16_HO2-->


