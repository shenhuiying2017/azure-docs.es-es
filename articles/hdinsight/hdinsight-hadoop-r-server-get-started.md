---
title: "Introducción a R Server en HDInsight - Azure | Microsoft Docs"
description: "Aprenda a crear un motor Apache Spark en un clúster de HDInsight que incluya R Server y a enviar un script de R al clúster."
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/14/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: 89fa80b3e3409b7cd2f600776fffdeb3a5271b5d
ms.contentlocale: es-es
ms.lasthandoff: 08/31/2017

---
# <a name="get-started-using-r-server-on-hdinsight"></a>Introducción al uso del servidor de R en HDInsight

HDInsight incluye una opción de R Server que se integra en el clúster de HDInsight. Esta opción permite que los scripts de R usen Spark y MapReduce para ejecutar cálculos distribuidos. En este documento, se ofrece información sobre cómo crear un R Server en el clúster de HDInsight y después ejecutar un script de R que demuestre cómo usar Spark para cálculos de R distribuidos.


## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción a Azure**: antes de comenzar este tutorial, debe tener una suscripción a Azure. Vaya al artículo [Get a Microsoft Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Obtener una evaluación gratuita de Azure) para más información.
* **Un cliente de Secure Shell (SSH)**: el cliente de SSH se usa para conectarse al clúster de HDInsight de forma remota y ejecutar comandos directamente desde el clúster. Para más información, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
* **Claves SSH (opcional)**: puede proteger la cuenta de SSH usada para conectarse al clúster mediante una contraseña o una clave pública. Usar una contraseña es más sencillo y le permite empezar sin tener que crear un par de clave pública o privada. Sin embargo, es más seguro utilizar una clave.

> [!NOTE]
> Los pasos de este documento asumen que está usando una contraseña.


## <a name="automated-cluster-creation"></a>Creación automatizada del clúster

Puede automatizar la creación de HDInsight R Servers mediante plantillas de Azure Resource Manager, el SDK y también mediante PowerShell.

* Para crear un R Server con una plantilla de Azure Resource Manager, consulte [Deploy an R-server HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/) (Implementación de un clúster HDInsight con R Server).
* Para crear un R Server mediante el SDK de .NET, consulte [Crear clústeres basados en Linux en HDInsight con el SDK de .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Para crear un R Server mediante PowerShell consulte el artículo sobre [creación de un R Server en HDInsight con PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Creación de un clúster mediante Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. Seleccione **NUEVO** -> **Inteligencia y análisis**, -> **HDInsight**.

    ![Imagen de la creación de un nuevo clúster](./media/hdinsight-hadoop-r-server-get-started/newcluster.png)

3. En la experiencia **Creación rápida**, escriba un nombre para el clúster en el campo **Nombre del clúster**. Si tiene varias suscripciones a Azure, use la entrada **Suscripción** para seleccionar la que quiera usar.

    ![Opciones de nombre de clúster y suscripción](./media/hdinsight-hadoop-r-server-get-started/clustername.png)

4. Seleccione **Tipo de clúster** para abrir la hoja **Configuración de clúster**. En la hoja **Configuración de clúster**, seleccione las opciones siguientes:

    * **Tipo de clúster**: R Server
    * **Versión**: seleccione la versión de R Server que se va a instalar en el clúster. La versión disponible actualmente es ***R Server 9.1 (HDI 3.6)***. Puede consultar las notas de las versiones disponibles de R Server [aquí](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes).
    * **R Studio community edition for R Server**: este IDE basado en el explorador se instala de forma predeterminada en el nodo perimetral. Si prefiere que no se instale, desactive la casilla. Si desea instalarlo, la dirección URL para acceder al inicio de sesión de RStudio Server está en una hoja de aplicación del portal para el clúster, una vez que se haya creado.
    * Deje las demás opciones con los valores predeterminados y use el botón **Seleccionar** para guardar el tipo de clúster.

        ![Captura de pantalla de la hoja del tipo de clúster](./media/hdinsight-hadoop-r-server-get-started/clustertypeconfig.png)

5. Escriba un **Nombre de usuario de inicio de sesión del clúster** y una **Contraseña de inicio de sesión del clúster**.

    Especifique un **Nombre de usuario de SSH**. SSH se usa para conectarse de forma remota al clúster con un cliente de **Secure Shell (SSH)**. Puede especificar el usuario SSH en este cuadro de diálogo o una vez creado el clúster (en la pestaña de configuración del clúster). R Server está configurado para esperar un **nombre de usuario de SSH** "remoteuser".  **Si utiliza un nombre de usuario diferente, tendrá que realizar un paso adicional después de crear el clúster.**

    Deje el cuadro **Utilizar la misma contraseña que en el inicio de sesión del clúster** activado para usar la **CONTRASEÑA** como tipo de autenticación, a menos que prefiera usar una clave pública.  Para acceder a R Server en el clúster a través de un cliente remoto como, por ejemplo, RTVS, RStudio u otro IDE de escritorio, necesitará un par de claves pública y privada. Debe elegir una contraseña SSH si instala RStudio Server community edition.     

    Para crear y utilizar un par de claves pública y privada, desactive **Utilizar la misma contraseña que en el inicio de sesión del clúster** y luego seleccione **CLAVE PÚBLICA** y haga lo siguiente. Estas instrucciones asumen que dispone de Cygwin con ssh-keygen o equivalente instalado.

    * Genere un par de claves pública y privada desde el símbolo del sistema en el equipo portátil:

        ssh-keygen -t rsa -b 2048

    * Siga las indicaciones para nombrar un archivo de clave y, después, escriba una frase de contraseña para mayor seguridad. La pantalla debe ser similar a lo que muestra la siguiente imagen:

        ![Línea de comando SSH en Windows](./media/hdinsight-hadoop-r-server-get-started/sshcmdline.png)

    * Este comando crea un archivo de clave privada y un archivo de clave pública con el nombre <nombreDeArchivo-Clave-Publica>.pub, por ejemplo, furiosa y furiosa.pub.

        ![Dir SSH](./media/hdinsight-hadoop-r-server-get-started/dir.png)

    * A continuación, especifique el archivo de clave pública (&#42;.pub) al asignar las credenciales del clúster de HDI y, por último, confirme el grupo de recursos y la región, y seleccione **Siguiente**.

        ![Hoja Credenciales](./media/hdinsight-hadoop-r-server-get-started/publickeyfile.png)  

   * Cambie los permisos en el archivo de clave privada en el equipo portátil:

        chmod 600 <nombreDeArchivoDeClavePrivada>

   * Use el archivo de clave privada con SSH para el inicio de sesión remoto:

        ssh –i <private-key-filename> remoteuser@<hostname public ip>

      O como parte de la definición del contexto de proceso de Hadoop Spark para R Server en el cliente. Consulte la subsección **Using Microsoft R Server como un cliente de Hadoop** (Uso de Microsoft R Server como cliente de Hadoop) de [Create a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Creación de un contexto de proceso para Spark).

6. La creación rápida lo remite a la hoja **Storage** para seleccionar la configuración de la cuenta de almacenamiento que se va a usar para la ubicación principal del sistema de archivos HDFS utilizado por el clúster. Seleccione una cuenta de Azure Storage nueva o existente o una cuenta de almacenamiento de Data Lake existente.

    - Si selecciona una cuenta de Azure Storage, puede seleccionar una cuenta de almacenamiento existente en **Seleccionar cuenta de almacenamiento** y, una vez allí, seleccionar la cuenta pertinente. Cree una cuenta mediante el vínculo **Crear nueva** en la sección **Seleccionar cuenta de almacenamiento**.

      > [!NOTE]
      > Si selecciona **Nueva**, debe escribir un nombre para la nueva cuenta de almacenamiento. Si se acepta el nombre, aparece una marca de verificación verde.

      Se usará el nombre del clúster de forma predeterminada en **Contenedor predeterminado**. Déjelo este valor predeterminado como valor.

      Si se ha seleccionado una nueva opción de cuenta de almacenamiento, aparecerá un aviso para seleccionar la **ubicación**, es decir, la región en la que desea crear la cuenta de almacenamiento.  

         ![Hoja Origen de datos](./media/hdinsight-getting-started-with-r/datastore.png)  

      > [!IMPORTANT]
      > Al seleccionar la ubicación del origen de datos predeterminado también establece la ubicación del clúster de HDInsight. El origen de datos predeterminado y el clúster deben estar en la misma región.

    - Si quiere usar un Data Lake Store existente, seleccione la cuenta de almacenamiento ADLS que desea usar y agregue la identidad de *ADD* de clúster al clúster para permitir el acceso al almacén. Para más información sobre este proceso, consulte [Creación de un clúster de HDInsight con Data Lake Store mediante Azure Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal).

    Use el botón **Seleccionar** para guardar la configuración del origen de datos.


7. Después se abre la hoja **Resumen** para validar toda la configuración. Aquí puede cambiar el **Tamaño del clúster** para modificar el número de servidores del clúster y especificar también las **Acciones de script** que desea ejecutar. A menos que sepa que necesita un clúster de mayor tamaño, deje el número de nodos de trabajo en el valor predeterminado de `4`. El costo estimado del clúster se muestra en la hoja.

    ![Resumen del clúster](./media/hdinsight-hadoop-r-server-get-started/clustersummary.png)

   > [!NOTE]
   > Si es necesario, puede volver a cambiar el tamaño del clúster más adelante mediante el portal (**Clúster** -> **Configuración** -> **Escalar clúster**) para aumentar o reducir el número de nodos de trabajo.  Este cambio de tamaño puede ser útil para desactivar el clúster cuando no esté en uso o para agregar más capacidad para satisfacer las necesidades de las tareas más grandes.
   >
   >

   Para cambiar el tamaño del clúster, los nodos de datos y el nodo perimetral hay que tener en cuenta los siguientes factores:  

   * El rendimiento de los análisis distribuidos de R Server en Spark es proporcional al número de nodos de trabajo si el volumen de datos es elevado.  

   * El rendimiento de los análisis de R Server es lineal según el tamaño de los datos analizados. Por ejemplo:  

     * Para datos de tamaño reducido y medio, el rendimiento será mayor si se analizan en un contexto de proceso local del nodo perimetral.  Para más información sobre los escenarios en los que los contextos de proceso local y de Spark funcionan mejor, consulte Opciones de contexto de proceso para R Server en HDInsight.<br>
     * Si inicia sesión en el nodo perimetral y ejecuta el script de R allí, se ejecutarán todas las funciones rx de ScaleR <strong>localmente</strong> en el nodo perimetral. Por este motivo, la memoria y el número de núcleos de este nodo deben ajustarse según corresponda. Lo mismo se aplica si utiliza R Server en HDI como contexto de proceso remoto desde el equipo portátil.

     ![Hoja Niveles de precios de nodo](./media/hdinsight-hadoop-r-server-get-started/pricingtier.png)

     Use el botón **Seleccionar** para guardar la configuración de precios del nodo.

   Observe que también existe el vínculo **Descargar plantilla y parámetros**. Haga clic en este vínculo para que se muestren los scripts que pueden utilizarse para automatizar la creación de un clúster con la configuración seleccionada. Estos scripts también están disponibles en la entrada de Azure Portal para el clúster una vez que se ha creado.

   > [!NOTE]
   > El clúster tarda algo de tiempo en crearse, normalmente unos 20 minutos. Use el icono del Panel de inicio o la entrada **Notificaciones** de la izquierda de la página para comprobar el proceso de creación.
   >
   >

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Conexión a RStudio Server

Si ha elegido incluir RStudio Server community edition en la instalación, puede acceder al inicio de sesión de RStudio por dos métodos diferentes.

1. Vaya a la dirección URL siguiente (donde **CLUSTERNAME** es el nombre del clúster que ha creado):

    https://**CLUSTERNAME**.azurehdinsight.net/rstudio/

2. Abra la entrada del clúster en Azure Portal, seleccione el vínculo rápido **Paneles de R Server** y, después, seleccione el **Panel de R Studio**:

     ![Acceso al panel de R Studio](./media/hdinsight-getting-started-with-r/rstudiodashboard1.png)

     ![Acceso al panel de R Studio](./media/hdinsight-getting-started-with-r/rstudiodashboard2.png)

   > [!IMPORTANT]
   > Independientemente del método que se use, la primera vez que inicie sesión deberá autenticarse dos veces.  En la primera autenticación, especifique el *identificador de usuario de administrador del clúster* y la *contraseña*. En el segundo aviso, especifique el *identificador de usuario de SSH* y la *contraseña*. En los siguientes inicios de sesión solo se necesitan la *contraseña de SSH* y el *identificador de usuario*.

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-r-server-edge-node"></a>Conectarse al nodo perimetral de R Server

Conéctese al nodo perimetral de R Server del clúster de HDInsight que usa SSH mediante el comando:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> La dirección `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` se puede encontrar en Azure Portal mediante la selección del clúster y, después, **Todas las configuraciones** -> **Aplicaciones** -> **RServer**. Esto hace que aparezca la información del punto de conexión de SSH para el nodo perimetral.
>
> ![Imagen del punto de conexión SSH del nodo perimetral](./media/hdinsight-hadoop-r-server-get-started/sshendpoint.png)
>
>

Si usó una contraseña para proteger la cuenta de usuario SSH, se le pedirá que la escriba. Si usa una clave pública, tal vez tenga que usar el parámetro `-i` para especificar la ruta de acceso a la correspondiente clave privada. Por ejemplo:

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Para más información, consulte [Conexión a través de SSH con HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md).

Una vez conectado, llegará a un símbolo de sistema similar al siguiente:

    sername@ed00-myrser:~$

<a name="enable-concurrent-users"></a>
## <a name="enable-multiple-concurrent-users"></a>Habilitación de varios usuarios simultáneos

Para habilitar varios usuarios simultáneos, agregue más usuarios al nodo perimetral en el que se ejecuta la versión de comunidad de RStudio.

Al crear un clúster de HDInsight, es preciso especificar dos usuarios, un usuario HTTP y un usuario SSH:

![Usuario simultáneo 1](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-1.png)

- **Nombre de usuario de inicio de sesión del clúster**: un usuario HTTP para la autenticación a través de la puerta de enlace de HDInsight que se usa para proteger los clústeres de HDInsight que ha creado. Este usuario HTTP se utiliza para acceder a la interfaz de usuario de Ambari, a la interfaz de usuario de YARN y a otros componentes de la interfaz de usuario.
- **Nombre de usuario de Secure Shell (SSH)**: un usuario SSH para acceder al clúster a través de Secure Shell. Este es un usuario del sistema Linux para todos los nodos principales, nodos de trabajo y nodos perimetrales. Por consiguiente, puede usar Secure Shell para acceder a cualquiera de los nodos de un clúster remoto.

La versión de comunidad de Servidor de R Studio que se usa en Microsoft R Server en un clúster del tipo HDInsight acepta únicamente el nombre de usuario y la contraseña de Linux como mecanismo de inicio de sesión. No admite tokens de paso. Por consiguiente, si ha creado un nuevo clúster y desea usar R Studio para acceder a él, es preciso que inicie sesión dos veces.

- Primero inicie sesión con las credenciales de usuario HTTP a través de la puerta de enlace de HDInsight: 

    ![Usuario simultáneo 2a](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-2a.png)

- Luego, use las credenciales de usuario SSH para iniciar sesión en RStudio:
  
    ![Usuario simultáneo 2b](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-2b.png)

Actualmente, una cuenta de usuario SSH solo se puede crear al aprovisionar un clúster de HDInsight. Por tanto, para que varios usuarios puedan acceder a Microsoft R Server en los clústeres de HDInsight, es preciso crear usuarios adicionales en el sistema Linux.

Dado que la versión de comunidad de RStudio Server se ejecuta en el nodo perimetral del clúster, hay que dar varios pasos:

1. Uso del usuario SSH creado para iniciar sesión en el nodo perimetral.
2. Agregar más usuarios de Linux al nodo perimetral.
3. Utilizar la versión de comunidad de RStudio con el usuario creado.

### <a name="step-1-use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>Paso 1: Uso del usuario SSH creado para iniciar sesión en el nodo perimetral

Descargue todas las herramientas de SSH (como Putty) y utilice el usuario SSH existente para iniciar sesión. Luego, siga las instrucciones que se proporcionan en [Conexión a través de SSH con HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md) para acceder al nodo perimetral. La dirección del nodo perimetral de R Server en el clúster de HDInsight es: *clustername-ed-ssh.azurehdinsight.net*


### <a name="step-2-add-more-linux-users-in-edge-node"></a>Paso 2: Adición de más usuarios de Linux al nodo perimetral

Para agregar un usuario al nodo perimetral, ejecute los comandos:

    sudo useradd yournewusername -m
    sudo passwd yourusername

Debería ver que se devuelven los siguientes elementos: 

![Usuario simultáneo 3](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-3.png)

Cuando se le pida la "contraseña actual de Kerberos", presione **Entrar** para ignorarlo. La opción `-m` del comando `useradd` indica que el sistema creará una carpeta particular para el usuario, que se requiere para la versión de comunidad de RStudio.


### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Paso 3: Uso de la versión de comunidad de RStudio con el usuario creado

Use el usuario creado para iniciar sesión en RStudio:

![Usuario simultáneo 4](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-4.png)

Observe que RStudio indica que usa el nuevo usuario (aquí, por ejemplo, *sshuser6*) para iniciar sesión en el clúster: 

![Usuario simultáneo 5](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-5.png)

También puede iniciar sesión con las credenciales originales (de forma predeterminada, es *sshuser*) simultáneamente desde otra ventana del explorador.

Puede enviar un trabajo mediante las funciones de scaleR. Este es un ejemplo de los comandos que se usan para ejecutar un trabajo:

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
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

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)


Tenga en cuenta que los trabajos enviados se encuentran en varios nombres de usuario en la interfaz de usuario de YARN:

![Usuario simultáneo 6](./media/hdinsight-hadoop-r-server-get-started/concurrent-users-6.png)

Tenga en cuenta también que los usuarios recién agregados no tienen privilegios raíz en el sistema Linux, pero tienen el mismo acceso a todos los archivos en el almacenamiento HDFS y WASB remoto.


<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Usar la consola de R

1. En la sesión SSH, use el siguiente comando para iniciar la consola de R:  

        R

2. Debería ver una salida similar a la siguiente:
    
        R version 3.2.2 (2015-08-14) -- "Fire Safety"
        Copyright (C) 2015 The R Foundation for Statistical Computing
        Platform: x86_64-pc-linux-gnu (64-bit)

        R is free software and comes with ABSOLUTELY NO WARRANTY.
        You are welcome to redistribute it under certain conditions.
        Type 'license()' or 'licence()' for distribution details.

    Compatibilidad con lenguaje natural siempre que se ejecute en una configuración regional en inglés

        R is a collaborative project with many contributors.
        Type 'contributors()' for more information and
        'citation()' on how to cite R or R packages in publications.

        Type 'demo()' for some demos, 'help()' for on-line help, or
        'help.start()' for an HTML browser interface to help.
        Type 'q()' to quit R.

        Microsoft R Server version 8.0: an enhanced distribution of R
        Microsoft packages Copyright (C) 2016 Microsoft Corporation

    Escriba 'readme()' para leer las notas de la versión.
    >

3. Desde el símbolo de sistema de `>` , puede escribir el código de R. El servidor de R incluye paquetes que le permiten interactuar con Hadoop y ejecutar cálculos distribuidos fácilmente. Por ejemplo, use el siguiente comando para ver la raíz del sistema de archivos predeterminado del clúster de HDInsight:

        rxHadoopListFiles("/")

4. También puede usar el direccionamiento de estilo WASB.

        rxHadoopListFiles("wasb:///")


## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Uso de R Server en HDI desde una instancia remota de Microsoft R Server o Microsoft R Client

Es posible configurar el acceso al contexto de proceso de HDI Hadoop Spark desde una instancia remota de Microsoft R Server o Microsoft R Client que se ejecute en un equipo de escritorio o portátil. Consulte la subsección **Using Microsoft R Server como un cliente de Hadoop** (Uso de Microsoft R Server como cliente de Hadoop) de [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started.md) (Creación de un contexto de proceso para Spark). Para ello, debe especificar las siguientes opciones al definir el contexto de proceso RxSpark en el equipo portátil: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches y sshProfileScript. Por ejemplo:


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

Un contexto de proceso le permite controlar si un cálculo se realizará de forma local en el nodo perimetral o se distribuirá en los nodos del clúster de HDInsight.

1. Desde RStudio Server o la consola de R (en una sesión SSH), use el siguiente código para cargar datos de ejemplo en el almacenamiento predeterminado de HDInsight:

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


## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuir el código de R a varios nodos

Con R Server puede seleccionar fácilmente el código de R existente y ejecutarlo en varios nodos del clúster mediante `rxExec`. Esta función es útil cuando se realiza un barrido de parámetros o simulaciones. El siguiente código es un ejemplo de cómo usar `rxExec`:

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


## <a name="accessing-data-in-hive-and-parquet"></a>Acceso a datos en Hive y Parquet

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


Para más información sobre el uso de estas nuevas funciones, consulte la ayuda en línea de R Server mediante los comandos `?RxHivedata` y `?RxParquetData`.  


## <a name="install-additional-r-packages-on-the-edge-node"></a>Instalar paquetes de R adicionales en el nodo perimetral

Si quiere instalar más paquetes de R en el nodo perimetral, puede usar `install.packages()` directamente desde la consola de R cuando se conecte al nodo perimetral a través de SSH. Sin embargo, si necesita instalar paquetes de R en los nodos de trabajo del clúster, debe usar Acción de script.

Las acciones de script son scripts de Bash que se usan para realizar cambios en la configuración del clúster de HDInsight o para instalar software adicional, como paquetes de R adicionales. Para instalar paquetes adicionales con una Acción de script, use los pasos siguientes:

> [!IMPORTANT]
> Usar Acciones de script para instalar paquetes de R adicionales solo se permite una vez creado el clúster. No use este procedimiento durante la creación del clúster, ya que el script cuenta con que el servidor de R esté completamente configurado e instalado.
>
>

1. En [Azure Portal](https://portal.azure.com), seleccione R Server en el clúster de HDInsight.

2. En la hoja **Configuración**, seleccione **Acciones de script** y, luego, **Enviar nuevo** para enviar una nueva acción de script.

   ![Imagen de la hoja Acciones de script](./media/hdinsight-hadoop-r-server-get-started/scriptaction.png)

3. En la hoja **Enviar acción de script**, proporcione la siguiente información:

   * **Nombre**: nombre descriptivo para identificar este script.

   * **URI de script de Bash**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Head** (Principal): este elemento debe estar **desactivado**.

   * **Worker** (De trabajo): este elemento debe estar **activado**.

   * **Edge nodes** (Nodos perimetrales): este elemento debe estar **desactivado**.

   * **Zookeeper**: este elemento debe estar **activado**.

   * **Parámetros**: paquetes de R que se van a instalar. Por ejemplo: `bitops stringr arules`

   * **Persist this script...** (Continuar con este script…): debe estar **activado**.  

   > [!NOTE]
   > 1. De forma predeterminada, todos los paquetes de R se instalan desde una instantánea del repositorio Microsoft MRAN coherente con la versión del servidor R que se ha instalado. Si desea instalar las versiones más recientes de los paquetes, corre el riesgo de que se produzca incompatibilidad. Sin embargo, para realizar este tipo de instalación es preciso especificar `useCRAN` como primer elemento de la lista de paquetes, por ejemplo `useCRAN bitops, stringr, arules`.  
   > 2. Algunos paquetes de R requieren otras bibliotecas de sistema de Linux. Por comodidad, hemos instalado previamente las dependencias que necesitan los 100 paquetes de R más populares. Sin embargo, si los paquetes de R que instale necesitan otras bibliotecas, debe descargar el script base usado aquí y realizar los pasos para instalar las bibliotecas del sistema. A continuación, debe cargar el script modificado a un contenedor de blobs público en Azure Storage y usar el script modificado para instalar los paquetes.
   >    Para más información sobre cómo desarrollar acciones de script, consulte [Desarrollo de acciones de script](hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Agregar una Acción de script](./media/hdinsight-getting-started-with-r/submitscriptaction.png)

4. Seleccione **Crear** para ejecutar el script. Una vez que finalice el script, los paquetes de R estarán disponibles en todos los nodos de trabajo.


## <a name="using-microsoft-r-server-operationalization"></a>Uso de la operacionalización de Microsoft R Server

Cuando se complete el modelado de datos, podrá hacer operativo el modelo para realizar predicciones. Para configurar la operacionalización de Microsoft R Server, siga estos pasos:

En primer lugar, use ssh en el nodo perimetral. Por ejemplo, 

    ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Después de utilizar ssh, cambie el directorio para la versión pertinente y use sudo en la dll de dotnet: 

- Para Microsoft R Server 9.1:

    cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0   sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

- Para Microsoft R Server 9.0:

    cd /usr/lib64/microsoft-deployr/9.0.1   sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

Para configurar la operacionalización de Microsoft R Server con una configuración One-box, haga lo siguiente:

1. Seleccione “Configure R Server for Operationalization” (Configurar R Server para operacionalización)
2. Seleccione “A. One-box (web + compute nodes)” [One-box (web y nodos de proceso)]
3. Escriba una contraseña para el usuario **administrador**

![operacionalización one box](./media/hdinsight-hadoop-r-server-get-started/admin-util-one-box-.png)

Como paso opcional, puede realizar comprobaciones de diagnóstico mediante la ejecución de una prueba de diagnóstico tal y como se muestra a continuación:

1. Seleccione “6. Ejecutar pruebas de diagnóstico”
2. Seleccione “A. Configuración de pruebas”
3. Escriba el nombre de usuario = "admin" y la contraseña del paso de configuración anterior
4. Confirme el estado general = pass
5. Salga de la utilidad de administración
6. Salga de SSH

![Diagnóstico de operacionalización](./media/hdinsight-hadoop-r-server-get-started/admin-util-diagnostics.png)


>[!NOTE]
>**Retrasos prolongados al consumir el servicio web en Spark**
>
>Si se producen retrasos prolongados al intentar consumir un servicio web creado con funciones de mrsdeploy en un contexto de proceso de Spark, puede que necesite agregar carpetas que falten. La aplicación de Spark pertenece a un usuario llamado "*rserve2*" cada vez que se invoque desde un servicio web mediante las funciones de mrsdeploy. Para evitar este problema:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


En esta fase, la configuración de la operacionalización está completa. Ahora puede usar el paquete "mrsdeploy" en RClient para conectarse a la operacionalización en el nodo perimetral y empezar a usar sus características, como la [ejecución remota](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution) y los [servicios web](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette). Dependiendo de si el clúster se configura en una red virtual o no, puede que necesite configurar la tunelización de reenvío del puerto mediante el inicio de sesión SSH. En las secciones siguientes se explica cómo configurar este túnel.

### <a name="rserver-cluster-on-virtual-network"></a>RServer Cluster en una red virtual

Asegúrese de que permite que el tráfico pase a través del puerto 12800 al nodo perimetral. De esa forma, puede usar el nodo perimetral para conectarse a la característica de operacionalización.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Si `remoteLogin()` no se puede conectarse al nodo perimetral, pero se posible usar SSH en dicho nodo, tendrá que comprobar si se ha configurado correctamente la regla para permitir el tráfico en el puerto 12800. Si el problema no desaparece, puede solucionarlo mediante la configuración de la tunelización de reenvío del puerto a través de SSH. Para obtener instrucciones, consulte la siguiente sección.

### <a name="rserver-cluster-not-set-up-on-virtual-network"></a>RServer Cluster no configurado en una red virtual

Si el clúster no está configurado en la red virtual o si tiene problemas con la conectividad a través de la red virtual, puede utilizar la tunelización de reenvío del puerto de SSH:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

En Putty, también se puede configurar.

![conexión ssh de putty](./media/hdinsight-hadoop-r-server-get-started/putty.png)

Una vez que la sesión SSH esté activa, el tráfico del puerto 12800 del equipo se reenviará al puerto 12800 del nodo perimetral mediante la sesión de SSH. Asegúrese de utilizar `127.0.0.1:12800` en el método `remoteLogin()`. Esto iniciará sesión en la operacionalización del nodo perimetral mediante el enrutamiento de puerto.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="how-to-scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Escalado de los nodos de proceso de la operacionalización de Microsoft R Server en nodos de trabajo de HDInsight

### <a name="decommission-the-worker-nodes"></a>Retirada de los nodos de trabajo

Microsoft R Server no se administra actualmente mediante Yarn. Si los nodos de trabajo no se retiran, el administrador de recursos de Yarn no funcionará según lo previsto ya que no reconocerá los recursos que utiliza el servidor. Para evitar esto, se recomienda la retirada de los nodos de trabajo antes de escalar horizontalmente los nodos de proceso.

Pasos para retirar los nodos de trabajo:

* Inicie sesión en la consola de Ambari del clúster de HDI y haga clic en la pestaña "hosts".
* Seleccione los nodos de trabajo (los que se van a retirar), haga clic en "Acciones" > "Hosts seleccionados" > "Hosts" > haga clic en "Activar modo de mantenimiento". Por ejemplo, en la siguiente imagen se han seleccionado wn3 y wn4 para su retirada.  

   ![retirar nodos de trabajo](./media/hdinsight-hadoop-r-server-get-started/get-started-operationalization.png)  

* Seleccione **Acciones** > **Hosts seleccionados** > **DataNodes** > haga clic en **Retirar**
* Seleccione **Acciones** > **Hosts seleccionados** > **NodeManagers** > haga clic en **Retirar**
* Seleccione **Acciones** > **Hosts seleccionados** > **DataNodes** > haga clic en **Detener**
* Seleccione **Acciones** > **Hosts seleccionados** > **NodeManagers** > haga clic en **Detener**
* Seleccione **Acciones** > **Hosts seleccionados** > **Hosts** > haga clic en **Detener todos los componentes**
* Anulación de la selección de los nodos de trabajo y selección de los nodos principales
* Seleccione **Acciones** > **Hosts seleccionados** > "**Hosts** > **Reiniciar todos los componentes**

### <a name="configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Configuración de los nodos de proceso en cada nodo de trabajo retirado

1. SSH en cada nodo de trabajo retirado.
2. Ejecute la utilidad de administración mediante `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`.
3. Escriba "1" para seleccionar la opción "Configure R Server for Operationalization" (Configurar R Server para operacionalización).
4. Escriba "c" para seleccionar la opción "C. Nodo de proceso". Esto configurará el nodo de proceso en el nodo de trabajo.
5. Salga de la utilidad de administración.

### <a name="add-compute-nodes-details-on-web-node"></a>Incorporación de detalles de nodos de proceso en el nodo web

Una vez que todos los nodos de trabajo retirados se han configurado para ejecutar nodos de proceso, vuelva al nodo perimetral y agregue las direcciones IP de los nodos de trabajo retirados a la configuración del nodo web de Microsoft R Server:

* Uso de SSH en el nodo perimetral.
* Ejecute `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.
* Busque la sección de "identificadores URI" y agregue la dirección IP del nodo de trabajo y los detalles del puerto.

    ![línea de comandos para retirar nodos de trabajo](./media/hdinsight-hadoop-r-server-get-started/get-started-op-cmd.png)


## <a name="troubleshoot"></a>Solución de problemas

Si experimenta problemas con la creación de clústeres de HDInsight, consulte los [requisitos de control de acceso](hdinsight-administer-use-portal-linux.md#create-clusters).


## <a name="next-steps"></a>Pasos siguientes

Ahora debe aprender a crear un nuevo clúster de HDInsight que incluya R Server y los conceptos básicos del uso de la consola de R desde una sesión de SSH. Los siguientes temas explican otras maneras de administrar y trabajar con R Server en HDInsight:

* [Agregar R Server Studio a HDInsight (si no está instalado durante la creación del clúster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Opciones de contexto de proceso para R Server en HDInsight (versión preliminar)](hdinsight-hadoop-r-server-compute-contexts.md)
* [Opciones de Azure Storage para R Server en HDInsight](hdinsight-hadoop-r-server-storage.md)

