---
title: 'Administración de clústeres de R Server en HDInsight: Azure | Microsoft Docs'
description: Obtenga información sobre cómo administrar un clúster de R Server en Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 827bcb7bb20f1def9acec8cb2043ea295801583a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31414932"
---
# <a name="manage-r-server-cluster-on-azure-hdinsight"></a>Administración de clústeres de R Server en Azure HDInsight

En este artículo, aprenderá a administrar un clúster de R Server en Azure HDInsight para realizar tareas como agregar varios usuarios simultáneos, conectarse de forma remota a un cliente o un clúster de R Server (Microsoft ML Server), cambiar el contexto de proceso, etc.

## <a name="prerequisites"></a>requisitos previos

* **Un clúster de R Server en HDInsight**: para obtener instrucciones, consulte [Introducción al uso de R Server en HDInsight](r-server-get-started.md).

* **Un cliente de Secure Shell (SSH)**: el cliente de SSH se usa para conectarse al clúster de HDInsight de forma remota y ejecutar comandos directamente desde el clúster. Para obtener más información, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="enable-multiple-concurrent-users"></a>Habilitación de varios usuarios simultáneos

Para habilitar varios usuarios simultáneos para un clúster de R Server en HDInsight agregando más usuarios al nodo perimetral en el que se ejecuta la versión de comunidad de RStudio. Al crear un clúster de HDInsight, es preciso especificar dos usuarios, un usuario HTTP y un usuario SSH:

![Usuario simultáneo 1](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **Nombre de usuario de inicio de sesión del clúster**: un usuario HTTP para la autenticación a través de la puerta de enlace de HDInsight que se usa para proteger los clústeres de HDInsight que ha creado. Este usuario HTTP se utiliza para acceder a la interfaz de usuario de Ambari, a la interfaz de usuario de YARN y a otros componentes de la interfaz de usuario.
- **Nombre de usuario de Secure Shell (SSH)**: un usuario SSH para acceder al clúster a través de Secure Shell. Este es un usuario del sistema Linux para todos los nodos principales, nodos de trabajo y nodos perimetrales. Por consiguiente, puede usar Secure Shell para acceder a cualquiera de los nodos de un clúster remoto.

La versión de comunidad de R Studio Server que se usa en el clúster de R Server en HDInsight acepta únicamente el nombre de usuario y la contraseña de Linux como mecanismo de inicio de sesión. No admite tokens de paso. Por lo tanto, al tratar de obtener acceso a R Studio por primera vez en un clúster de R Server, debe iniciar sesión dos veces.

- Primero inicie sesión con las credenciales de usuario HTTP a través de la puerta de enlace de HDInsight. 

- Luego, use las credenciales de usuario SSH para iniciar sesión en RStudio.
  
Actualmente, una cuenta de usuario SSH solo se puede crear al aprovisionar un clúster de HDInsight. Por tanto, para que varios usuarios puedan acceder al clúster de R Server en HDInsight, debe crear usuarios adicionales en el sistema Linux.

Dado que RStudio se ejecuta en el nodo perimetral del clúster, hay que dar varios pasos:

1. Uso del usuario de SSH creado para iniciar sesión en el nodo perimetral
2. Agregar más usuarios de Linux al nodo perimetral.
3. Utilizar la versión de comunidad de RStudio con el usuario creado.

### <a name="step-1-use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>Paso 1: Uso del usuario SSH creado para iniciar sesión en el nodo perimetral

Siga las instrucciones que se proporcionan en [Conexión a través de SSH con HDInsight (Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md) para acceder al nodo perimetral. La dirección del nodo perimetral para el clúster de R Server en HDInsight es `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Paso 2: Adición de más usuarios de Linux al nodo perimetral

Para agregar un usuario al nodo perimetral, ejecute los comandos:

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user 
    sudo passwd <yournewusername>

En la captura de pantalla siguiente se muestra el resultado.

![Usuario simultáneo 3](./media/r-server-hdinsight-manage/concurrent-users-2.png)

Cuando se le pida la "contraseña actual de Kerberos", presione **Entrar** para ignorarlo. La opción `-m` del comando `useradd` indica que el sistema creará una carpeta particular para el usuario, que se requiere para la versión de comunidad de RStudio.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Paso 3: Uso de la versión de comunidad de RStudio con el usuario creado

Acceda a RStudio desde https://CLUSTERNAME.azurehdinsight.net/rstudio/. Si va a iniciar sesión por primera vez después de crear el clúster, escriba las credenciales de administrador del clúster seguidas de las credenciales del usuario SSH que acaba de crear. Si no se trata del primer inicio de sesión, escriba solo las credenciales del usuario SSH que ha creado.

También puede iniciar sesión con las credenciales originales (de forma predeterminada, es *sshuser*) simultáneamente desde otra ventana del explorador.

Tenga en cuenta también que los usuarios recién agregados no tienen privilegios raíz en el sistema Linux, pero tienen el mismo acceso a todos los archivos en el almacenamiento HDFS y WASB remoto.

## <a name="connect-remotely-to-microsoft-ml-server-or-client"></a>Conexión de forma remota a Microsoft ML Server o Microsoft ML Client

Puede configurar el acceso al contexto de proceso de HDInsight Hadoop Spark desde una instancia remota de Microsoft ML Server o Microsoft ML Client que se ejecute en un equipo de escritorio. Para ello, debe especificar las opciones (hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches y sshProfileScript) al definir el contexto de proceso RxSpark en el equipo de escritorio. Por ejemplo:

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

Para obtener más información, consulte la sección "Using Microsoft R Server as a Hadoop Client" (Uso de Microsoft R Server como cliente de Hadoop) de [Creating a Compute Context for Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios) (Creación de un contexto de proceso para Spark).

## <a name="use-a-compute-context"></a>Usar un contexto de proceso

Un contexto de proceso le permite controlar si un cálculo se realizará de forma local en el nodo perimetral o se distribuirá en los nodos del clúster de HDInsight.

1. Desde RStudio Server o la consola de R (en una sesión SSH), use el siguiente código para cargar datos de ejemplo en el almacenamiento predeterminado de HDInsight:

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
        remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
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

3. Ejecute una regresión lógica a través de los datos con el contexto de proceso local.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    Debería ver una salida que finalice con líneas similares a las siguientes:

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

4. Ejecute la misma regresión lógica usando el contexto de Spark. El contexto de Spark distribuirá el procesamiento en todos los nodos de trabajo del clúster de HDInsight.

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
   > También puede usar MapReduce para distribuir el cálculo en los nodos del clúster. Para más información sobre el contexto de proceso, consulte [Opciones de contexto de proceso para R Server en HDInsight](r-server-compute-contexts.md).


## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuir el código de R a varios nodos

Con R Server en HDInsight puede seleccionar el código de R existente y ejecutarlo en varios nodos del clúster mediante `rxExec`. Esta función es útil cuando se realiza un barrido de parámetros o simulaciones. El siguiente código es un ejemplo de cómo usar `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Si aún usa el contexto de Spark o MapReduce, este comando devuelve el valor nodename de los nodos de trabajo en los que se ejecuta el código `(Sys.info()["nodename"])`. Por ejemplo, en un clúster de cuatro nodos, lo esperable es recibir un resultado similar al siguiente:

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


## <a name="access-data-in-hive-and-parquet"></a>Acceso a datos en Hive y Parquet

Una característica disponible en R Server 9.1 permite el acceso directo a los datos en Hive y Parquet para usarlos en funciones ScaleR en el contexto de proceso de Spark. Estas funcionalidades están disponibles por medio de las nuevas funciones de origen de datos ScaleR llamadas RxHiveData y RxParquetData. Estas emplean Spark SQL para cargar datos directamente en un elemento DataFrame de Spark para analizarlos con ScaleR.  

El siguiente código proporciona código de ejemplos relativo al uso de las nuevas funciones:

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
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

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


Para más información sobre el uso de estas nuevas funciones, consulte la ayuda en línea de ML Server mediante los comandos `?RxHivedata` y `?RxParquetData`.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Instalación de paquetes de R adicionales en el clúster

### <a name="to-install-r-packages-on-the-edge-node"></a>Procedimiento para instalar paquetes de R adicionales en el nodo perimetral

Si quiere instalar paquetes de R adicionales en el nodo perimetral, puede usar `install.packages()` directamente desde la consola de R cuando se conecte al nodo perimetral a través de SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Procedimiento para instalar paquetes de R en el nodo de trabajo

Para instalar paquetes de R en los nodos de trabajo del clúster, debe usar una acción de script. Las acciones de script son scripts de Bash que se usan para realizar cambios en la configuración del clúster de HDInsight o para instalar software adicional, como paquetes de R adicionales. 

> [!IMPORTANT]
> Usar Acciones de script para instalar paquetes de R adicionales solo se permite una vez creado el clúster. No use este procedimiento durante la creación del clúster, ya que el script cuenta con que el servidor de R esté completamente configurado e instalado.
>
>

1. Siga los pasos de [Personalización de clústeres mediante la acción de script](../hdinsight-hadoop-customize-cluster-linux.md).

3. En **Enviar acción de script**, proporcione la siguiente información:

   * En **Tipo de script**, seleccione **Personalizada**.

   * En **Nombre**, especifique un nombre para la acción de script.

    * En **URI de script de Bash**, escriba `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Se trata del script que instala los paquetes de R adicionales en el nodo de trabajo.

   * Active solo la casilla de verificación de **Trabajo**.

   * **Parámetros**: paquetes de R que se van a instalar. Por ejemplo: `bitops stringr arules`

   * Active la casilla de verificación de **Persist this script action** (Continuar con esta acción de script).  

   > [!NOTE]
   > 1. De forma predeterminada, todos los paquetes de R se instalan desde una instantánea del repositorio Microsoft MRAN coherente con la versión del servidor R que se ha instalado. Si desea instalar las versiones más recientes de los paquetes, corre el riesgo de que se produzca incompatibilidad. Sin embargo, para realizar este tipo de instalación es preciso especificar `useCRAN` como primer elemento de la lista de paquetes, por ejemplo `useCRAN bitops, stringr, arules`.  
   > 2. Algunos paquetes de R requieren otras bibliotecas de sistema de Linux. Por comodidad, hemos instalado previamente las dependencias que necesitan los 100 paquetes de R más populares. Sin embargo, si los paquetes de R que instale necesitan otras bibliotecas, debe descargar el script base usado aquí y realizar los pasos para instalar las bibliotecas del sistema. A continuación, debe cargar el script modificado a un contenedor de blobs público en Azure Storage y usar el script modificado para instalar los paquetes.
   >    Para más información sobre cómo desarrollar acciones de script, consulte [Desarrollo de acciones de script](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Agregar una Acción de script](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. Seleccione **Crear** para ejecutar el script. Una vez que finalice el script, los paquetes de R estarán disponibles en todos los nodos de trabajo.

## <a name="next-steps"></a>Pasos siguientes

* [Operacionalización de un clúster de R Server en HDInsight](r-server-operationalize.md)
* [Opciones de contexto de proceso para un clúster de R Server en HDInsight](r-server-compute-contexts.md)
* [Opciones de Azure Storage para un clúster de R Server en HDInsight](r-server-storage.md)
