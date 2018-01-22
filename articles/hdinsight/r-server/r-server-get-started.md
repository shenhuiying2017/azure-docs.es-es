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
ms.openlocfilehash: e688068efb41cdccbeb23de3c8ad7a09021e5b3f
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="get-started-with-r-server-on-hdinsight"></a>Introducción al uso de R Server en HDInsight

Azure HDInsight incluye una opción de R Server que se integra en el clúster de HDInsight. Esta opción permite que los scripts de R usen Spark y MapReduce para ejecutar cálculos distribuidos. En este artículo aprenderá a crear una instancia de R Server en un clúster de HDInsight. A continuación, aprenderá a ejecutar un script de R que muestra el uso de Spark para cálculos de R distribuidos.


## <a name="prerequisites"></a>requisitos previos

* **Una suscripción a Azure**: antes de comenzar este tutorial, debe tener una suscripción a Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Obtener una evaluación gratuita de Azure para probar Hadoop en HDInsight) para más información.
* **Un cliente de Secure Shell (SSH)**: el cliente de SSH se usa para conectarse al clúster de HDInsight de forma remota y ejecutar comandos directamente desde el clúster. Para más información, consulte [Uso SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
* **Claves SSH (opcional)**: puede proteger la cuenta de SSH usada para conectarse al clúster mediante una contraseña o una clave pública. Usar una contraseña es más sencillo y le permite empezar sin tener que crear un par de claves pública y privada. Sin embargo, es más seguro utilizar una clave.

  > [!NOTE]
  > Los pasos de este artículo asumen que está usando una contraseña.


## <a name="automate-cluster-creation"></a>Creación automatizada del clúster

Puede automatizar la creación de instancias de R Server para HDInsight mediante plantillas de Azure Resource Manager, el SDK y PowerShell.

* Para crear una instancia de R Server con una plantilla de Azure Resource Manager, consulte [Deploy an R server HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/) (Implementación de un clúster de HDInsight con R Server).
* Para crear una instancia de R Server mediante el SDK de .NET, consulte [Creación de clústeres basados en Linux en HDInsight con el SDK de .NET](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Consulte [Creación de clústeres basados en Linux en HDInsight con Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md) para implementar R Server con PowerShell.


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-a-cluster-by-using-the-azure-portal"></a>Creación de un clúster mediante Azure Portal

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. Seleccione **Nuevo** > **Inteligencia y análisis** > **HDInsight**.

    ![Imagen de la creación de un nuevo clúster](./media/r-server-get-started/newcluster.png)

3. En la experiencia **Creación rápida**, escriba un nombre para el clúster en el cuadro de texto **Nombre del clúster**. Si tiene varias suscripciones de Azure, use el cuadro **Suscripción** para seleccionar la que quiere usar.

    ![Opciones de nombre de clúster y suscripción](./media/r-server-get-started/clustername.png)

4. Seleccione **Tipo de clúster** para abrir el panel **Configuración del clúster**. En el panel **Configuración de clúster**, seleccione las opciones siguientes:

    * **Tipo de clúster**: seleccione **R Server**.
    * **Versión**: seleccione la versión de R Server que se va a instalar en el clúster. La versión disponible actualmente es **R Server 9.1 (HDI 3.6)**. Puede consultar las notas de las versiones disponibles de R Server [aquí](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes).
    * **R Studio community edition for R Server**: este IDE basado en el explorador se instala de forma predeterminada en el nodo perimetral. Si prefiere que no se instale, desactive la casilla. Si desea instalarlo, la dirección URL para acceder al inicio de sesión de RStudio Server está en un panel de aplicación del portal del clúster, una vez que se haya creado.
    * Deje las demás opciones con los valores predeterminados y use el botón **Seleccionar** para guardar el tipo de clúster.

        ![Captura de pantalla del panel "Tipo de clúster"](./media/r-server-get-started/clustertypeconfig.png)

5. En el panel **Aspectos básicos**, en los cuadros de texto **Nombre de usuario de inicio de sesión del clúster** y **Contraseña de inicio de sesión del clúster**, escriba el nombre de usuario y la contraseña (respectivamente) del clúster.

6. En el cuadro **Nombre de usuario de Secure Shell (SSH)**, especifique el nombre de usuario de SSH. SSH se usa para conectarse de forma remota al clúster con un cliente de SSH. Puede especificar el usuario SSH en este cuadro de texto o una vez creado el clúster (en la pestaña **Configuración** del clúster).
   
   > [!NOTE] 
   > R Server está configurado para esperar un nombre de usuario de SSH "remoteuser". Si utiliza un nombre de usuario diferente, tendrá que realizar un paso adicional después de crear el clúster.

7. Deje la casilla **Utilizar la misma contraseña que en el inicio de sesión del clúster** activada para usar la **CONTRASEÑA** como tipo de autenticación, a menos que prefiera usar una clave pública. Para acceder a R Server en el clúster a través de un cliente remoto como, por ejemplo, Herramientas de R para Visual Studio, RStudio u otro IDE de escritorio, necesitará un par de claves pública y privada. Debe elegir una contraseña SSH si instala RStudio Server community edition.     

   Para crear y usar un par de claves pública y privada, desactive **Utilizar la misma contraseña que en el inicio de sesión del clúster**. A continuación, seleccione **CLAVE PÚBLICA** y haga lo siguiente. Estas instrucciones asumen que dispone de Cygwin con ssh-keygen o equivalente instalado.

   a. Genere un par de claves pública y privada desde el símbolo del sistema en el equipo portátil:

        ssh-keygen -t rsa -b 2048

   b. Siga las indicaciones para nombrar un archivo de clave y, después, escriba una frase de contraseña para mayor seguridad. La pantalla debe ser similar a lo que muestra la siguiente imagen:

      ![Línea de comandos de SSH en Windows](./media/r-server-get-started/sshcmdline.png)

      Este comando crea un archivo de clave privada y un archivo de clave pública con el nombre <nombreDeArchivo-Clave-Privada>.pub. En este ejemplo, los archivos son furiosa y furiosa.pub:

      ![Resultado de ejemplo del comando dir](./media/r-server-get-started/dir.png)

   c. Especifique el archivo de clave pública (&#42;.pub) al asignar las credenciales del clúster de HDI. Confirme el grupo de recursos y la región y seleccione **siguiente**.

      ![Panel de credenciales](./media/r-server-get-started/publickeyfile.png)  

   d. Cambie los permisos del archivo de clave privada en el equipo portátil:

        chmod 600 <private-key-filename>

   e. Use el archivo de clave privada con SSH para el inicio de sesión remoto:

        ssh –i <private-key-filename> remoteuser@<hostname public ip>

      O utilice el archivo de clave privada como parte de la definición del contexto de proceso de Hadoop Spark para R Server en el cliente. Para más información, consulte [Creación de un contexto de proceso para Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started).

8. La creación rápida le pasa al panel **Almacenamiento**. En el panel, seleccione la configuración de la cuenta de almacenamiento que se va a usar para la ubicación principal del sistema de archivos HDFS utilizado por el clúster. Seleccione una cuenta de Azure Storage nueva o existente o seleccione una cuenta de Azure Data Lake Store existente.

    - Si selecciona una cuenta de Azure Storage, puede seleccionar una cuenta de almacenamiento existente en **Seleccionar cuenta de almacenamiento** y, una vez allí, seleccionar la cuenta. Cree una cuenta mediante el vínculo **Crear nueva** en la sección **Seleccionar cuenta de almacenamiento**.

      > [!NOTE]
      > Si selecciona **Nuevo**, debe escribir un nombre para la nueva cuenta de almacenamiento. Si se acepta el nombre, aparece una marca de verificación verde.

      **Contenedor predeterminado** el valor predeterminado es el nombre del clúster. Déjelo este valor predeterminado como valor.

      Si ha seleccionado una cuenta de almacenamiento nueva, utilice **Ubicación** en el símbolo del sistema para seleccionar una región para la misma.  

         ![Panel Origen de datos](./media/r-server-get-started/datastore.png)  

      > [!IMPORTANT]
      > Al seleccionar la ubicación del origen de datos predeterminado también establece la ubicación del clúster de HDInsight. El origen de datos predeterminado y el clúster deben estar en la misma región.

    - Si desea usar una cuenta de Data Lake Store existente, seleccione la cuenta que se usará. A continuación, agregue la identidad de *AAD* del clúster al clúster para permitir el acceso al almacén. Para más información sobre este proceso, consulte [Creación de clústeres de HDInsight con Data Lake Store mediante Azure Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal).

    Use el botón **Seleccionar** para guardar la configuración del origen de datos.


9. A continuación, aparece el panel **Resumen** para que pueda comprobar la configuración. Aquí puede cambiar el tamaño del clúster para modificar el número de servidores del clúster. También puede especificar las acciones de script que desea ejecutar. A menos que sepa que necesita un clúster de mayor tamaño, deje el número de nodos de trabajo en el valor predeterminado de **4**. El panel también muestra el costo estimado del clúster.

    ![Resumen del clúster](./media/r-server-get-started/clustersummary.png)

   > [!NOTE]
   > Si es necesario, puede volver a cambiar el tamaño del clúster más adelante mediante el portal (**Clúster** > **Configuración** > **Escalar clúster**) para aumentar o reducir el número de nodos de trabajo. Este cambio de tamaño puede ser útil para desactivar el clúster cuando no esté en uso o para agregar más capacidad para satisfacer las necesidades de las tareas más grandes.
   >
   >

   Para cambiar el tamaño del clúster, los nodos de datos y el nodo perimetral hay que tener en cuenta los siguientes factores:  

   * El rendimiento de los análisis distribuidos de R Server en Spark es proporcional al número de nodos de trabajo si el volumen de datos es elevado.  

   * El rendimiento de los análisis de R Server es lineal según el tamaño de los datos analizados. Por ejemplo:   

     * Para datos de tamaño reducido y medio, el rendimiento será mayor si los datos se analizan en un contexto de proceso local del nodo perimetral. Para más información sobre los escenarios en los que los contextos de proceso local y de Spark funcionan mejor, consulte [Opciones de contexto de proceso para R Server en HDInsight](r-server-compute-contexts.md).<br>
     * Si inicia sesión en el nodo perimetral y ejecuta el script de R allí, se ejecutarán todas las funciones rx de ScaleR *localmente* en el nodo perimetral. Por este motivo, la memoria y el número de núcleos del nodo perimetral deben ajustarse según corresponda. Lo mismo se aplica si utiliza R Server en HDI como contexto de proceso remoto desde el equipo portátil.

   Use el botón **Seleccionar** para guardar la configuración de precios del nodo.

   ![Panel de planes de tarifa de nodo](./media/r-server-get-started/pricingtier.png)

   Observe que también existe el vínculo **Descargar plantilla y parámetros**. Seleccione este vínculo para que se muestren los scripts que pueden utilizarse para automatizar la creación de un clúster con la configuración seleccionada. Estos scripts también están disponibles en la entrada de Azure Portal para el clúster una vez que se ha creado.

   > [!NOTE]
   > El clúster tarda algo de tiempo en crearse, normalmente unos 20 minutos. Use el icono del Panel de inicio o la entrada **Notificaciones** de la izquierda de la página para comprobar el proceso de creación.
   >
   >

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Conexión a RStudio Server

Si ha elegido incluir RStudio Server community edition en la instalación, puede acceder al inicio de sesión de RStudio a través de dos métodos:

- Vaya a la dirección URL siguiente (donde *CLUSTERNAME* es el nombre del clúster que ha creado):

    https://*CLUSTERNAME*.azurehdinsight.net/rstudio/

- Abra la entrada del clúster en Azure Portal, seleccione el vínculo rápido **Paneles de R Server** y, después, seleccione el **Panel de R Studio**:

  ![Acceso al panel de RStudio](./media/r-server-get-started/rstudiodashboard1.png)

  ![Acceso al panel de RStudio](./media/r-server-get-started/rstudiodashboard2.png)

> [!IMPORTANT]
> Independientemente del método que se use, la primera vez que inicie sesión deberá autenticarse dos veces. En la primera autenticación, especifique el identificador del usuario administrador del clúster y la contraseña. En el segundo aviso, especifique el identificador de usuario de SSH y la contraseña. Los inicios de sesión posteriores requieren solo el identificador de usuario de SSH y la contraseña.

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-r-server-edge-node"></a>Conectarse al nodo perimetral de R Server

Conéctese al nodo perimetral de R Server del clúster de HDInsight con SSH mediante el comando:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> Puede encontrar la dirección `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` en Azure Portal. Seleccione el clúster y, a continuación, seleccione **Todas las configuraciones** > **Aplicaciones** > **RServer**. Esto hace que aparezca la información del punto de conexión de SSH del nodo perimetral.
>
> ![Imagen del punto de conexión de SSH del nodo perimetral](./media/r-server-get-started/sshendpoint.png)
>
>

Si usó una contraseña para ayudar a proteger la cuenta de usuario de SSH, se le pedirá que la escriba. Si usó una clave pública, puede usar el parámetro `-i` para especificar la clave privada correspondiente. Por ejemplo: 

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Para más información, consulte [Conexión a través de SSH con HDInsight (Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md).

Una vez conectado, llegará a un símbolo del sistema similar al siguiente:

    sername@ed00-myrser:~$

<a name="enable-concurrent-users"></a>
## <a name="enable-multiple-concurrent-users"></a>Habilitación de varios usuarios simultáneos

Para habilitar varios usuarios simultáneos, agregue más usuarios al nodo perimetral en el que se ejecuta la versión de comunidad de RStudio.

Al crear un clúster de HDInsight, es preciso especificar dos usuarios: un usuario de HTTP y un usuario de SSH.

![Entrada de las credenciales del usuario del clúster y del usuario de SSH](./media/r-server-get-started/concurrent-users-1.png)

- **Nombre de usuario de inicio de sesión del clúster**: un usuario HTTP para la autenticación a través de la puerta de enlace de HDInsight que se usa para proteger los clústeres de HDInsight que ha creado. Este usuario HTTP se utiliza para acceder a la interfaz de usuario de Ambari, a la interfaz de usuario de YARN y a otros componentes de la interfaz de usuario.
- **Nombre de usuario de Secure Shell (SSH)**: un usuario de SSH para acceder al clúster a través de Secure Shell. Este es un usuario del sistema Linux para todos los nodos principales, nodos de trabajo y nodos perimetrales. Por consiguiente, puede usar SSH para acceder a cualquiera de los nodos de un clúster remoto.

La versión de comunidad de RStudio Server que se usa en Microsoft R Server en un clúster del tipo HDInsight acepta únicamente el nombre de usuario y la contraseña de Linux como mecanismo de inicio de sesión. No admite tokens de paso. Por consiguiente, si ha creado un nuevo clúster y desea usar RStudio para acceder a él, es preciso que inicie sesión dos veces.

1. Inicie sesión con las credenciales de usuario HTTP a través de la puerta de enlace de HDInsight: 

    ![Entrada de las credenciales de usuario HTTP](./media/r-server-get-started/concurrent-users-2a.png)

2. Use las credenciales de usuario de SSH para iniciar sesión en RStudio:
  
    ![Entrada de las credenciales de usuario de SSH](./media/r-server-get-started/concurrent-users-2b.png)

Actualmente, solo se puede crear una cuenta de usuario de SSH al aprovisionar un clúster de HDInsight. Para que varios usuarios puedan acceder a Microsoft R Server en clústeres de HDInsight, es preciso crear usuarios adicionales en el sistema Linux.

Dado que la versión de comunidad de RStudio Server se ejecuta en el nodo perimetral del clúster, hay que realizar tres pasos:

1. Uso del usuario de SSH creado para iniciar sesión en el nodo perimetral.
2. Adición de más usuarios de Linux al nodo perimetral.
3. Uso de la versión de comunidad de RStudio con el usuario creado.

### <a name="use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>Uso del usuario SSH creado para iniciar sesión en el nodo perimetral.

Descargue cualquier herramienta de SSH (como Putty) y utilice el usuario de SSH existente para iniciar sesión. Luego, siga las instrucciones que se proporcionan en [Conexión a través de SSH con HDInsight (Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md) para acceder al nodo perimetral. La dirección del nodo perimetral de R Server en el clúster de HDInsight es: **clustername-ed-ssh.azurehdinsight.net**


### <a name="add-more-linux-users-to-the-edge-node"></a>Adición de más usuarios de Linux al nodo perimetral

Para agregar un usuario al nodo perimetral, ejecute los comandos:

    sudo useradd yournewusername -m
    sudo passwd yourusername

Debería ver que se devuelven los siguientes elementos: 

![Salida de los comandos sudo](./media/r-server-get-started/concurrent-users-3.png)

Cuando se le pida la contraseña de Kerberos actual, seleccione la tecla ENTRAR para omitirla. La opción `-m` del comando `useradd` indica que el sistema creará una carpeta principal para el usuario. Esta carpeta es necesaria para la versión de comunidad de RStudio.


### <a name="use-the-rstudio-community-version-with-the-created-user"></a>Uso de la versión de comunidad de RStudio con el usuario creado

Utilice el usuario creado para iniciar sesión en RStudio:

![Página de inicio de sesión de RStudio](./media/r-server-get-started/concurrent-users-4.png)

Observe que RStudio indica que usa el nuevo usuario (aquí, por ejemplo, **sshuser6**) para iniciar sesión en el clúster: 

![Ubicación del nuevo usuario en la barra de comandos de RStudio](./media/r-server-get-started/concurrent-users-5.png)

También puede iniciar sesión con las credenciales originales (de forma predeterminada, es **sshuser**) simultáneamente desde otra ventana del explorador.

Puede enviar un trabajo mediante las funciones de scaleR. Este es un ejemplo de los comandos que se usan para ejecutar un trabajo:

    # Set the HDFS (Azure Blob storage) location of example data
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storing data temporarily
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

    # Set the directory in bigDataDirRoot to load the data
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (Blob storage) file system
    hdfsFS <- RxHdfsFileSystem()

    # Create an info list for the airline data
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in the local system
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

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


Tenga en cuenta que los trabajos enviados se encuentran en varios nombres de usuario en la interfaz de usuario de YARN:

![Lista de usuarios en la interfaz de usuario de YARN](./media/r-server-get-started/concurrent-users-6.png)

Tenga en cuenta también que los usuarios recién agregados no tienen privilegios de raíz en el sistema Linux. Pero tienen el mismo acceso a todos los archivos en el sistema de archivos HDFS remoto y el almacenamiento de blobs.


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

3. Desde el símbolo de sistema de `>` , puede escribir el código de R. R Server incluye paquetes que le permiten interactuar con Hadoop y ejecutar cálculos distribuidos fácilmente. Por ejemplo, use el siguiente comando para ver la raíz del sistema de archivos predeterminado del clúster de HDInsight:

        rxHadoopListFiles("/")

4. También puede usar el estilo de direccionamiento de Blob Storage:

        rxHadoopListFiles("wasb:///")


## <a name="use-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Uso de R Server en HDI desde una instancia remota de Microsoft R Server o Microsoft R Client

Es posible configurar el acceso al contexto de proceso de HDI Hadoop Spark desde una instancia remota de Microsoft R Server o Microsoft R Client que se ejecute en un equipo de escritorio o portátil. Para más información, consulte la sección "Using Microsoft R Server as a Hadoop Client" (Uso de Microsoft R Server como cliente de Hadoop) en [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started.md) (Creación de un contexto de proceso para Spark). Para ello, especifique las siguientes opciones al definir el contexto de proceso RxSpark en el equipo portátil: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches y sshProfileScript. Este es un ejemplo:


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

Puede utilizar un contexto de proceso para controlar si un cálculo se realizará de forma local en el nodo perimetral o se distribuirá en los nodos del clúster de HDInsight.

1. Desde RStudio Server o la consola de R (en una sesión SSH), use el siguiente código para cargar datos de ejemplo en el almacenamiento predeterminado de HDInsight:

        # Set the HDFS (Blob storage) location of example data
        bigDataDirRoot <- "/example/data"

        # Create a local folder for storing data temporarily
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

        # Set the directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. Cree alguna información de datos y defina dos orígenes de datos para poder trabajar con los datos:

        # Define the HDFS (Blob storage) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create an info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # Get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in HDFS
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in the local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # Formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. Ejecute una regresión lógica a través de los datos con el contexto de proceso local:

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

Con R Server puede seleccionar fácilmente el código de R existente y ejecutarlo en varios nodos del clúster mediante `rxExec`. Esta función es útil cuando se realiza un barrido de parámetros o simulaciones. El siguiente código es un ejemplo de cómo usar `rxExec`:

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Si aún usa el contexto de Spark o MapReduce, este comando devuelve el valor de `nodename` de los nodos de trabajo en los que se ejecuta el código `(Sys.info()["nodename"])`. Por ejemplo, en un clúster de cuatro nodos, lo esperable es recibir una salida similar a la siguiente:

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

Una característica disponible en R Server 9.1 permite el acceso directo a los datos en Hive y Parquet para usarlos en funciones ScaleR en el contexto de proceso de Spark. Estas funcionalidades están disponibles a través de las nuevas funciones de origen de datos de ScaleR llamadas RxHiveData y RxParquetData. Estas funciones hacen uso de Spark SQL para cargar los datos directamente en una trama de datos de Spark cuyo análisis realizará ScaleR.  

El código siguiente muestra algunos ejemplos de cómo usar las nuevas funciones:

    #Create a Spark compute context
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model
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

    #Check on Spark data objects, clean up, and close the Spark session
    lsObj <- rxSparkListData() #Two data objects are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() #It should show an empty list
    rxSparkDisconnect(myHadoopCluster)


Para más información sobre estas nuevas funciones, consulte la ayuda en línea de R Server mediante los comandos `?RxHivedata` y `?RxParquetData`.  


## <a name="install-additional-r-packages-on-the-edge-node"></a>Instalar paquetes de R adicionales en el nodo perimetral

Si quiere instalar paquetes de R adicionales en el nodo perimetral, puede usar `install.packages()` directamente desde la consola de R cuando se conecte al nodo perimetral a través de SSH. Sin embargo, si necesita instalar paquetes de R en los nodos de trabajo del clúster, debe usar una acción de script.

Las acciones de script son scripts de Bash que se usan para realizar cambios en la configuración del clúster de HDInsight o para instalar software adicional, como paquetes de R adicionales. Para instalar paquetes adicionales con una acción de script, use los pasos siguientes.

> [!IMPORTANT]
> Puede usar acciones de script para instalar paquetes de R adicionales solo después de haber creado el clúster. No use este procedimiento durante la creación del clúster, ya que el script cuenta con que R Server esté completamente configurado e instalado.
>
>

1. En [Azure Portal](https://portal.azure.com), seleccione R Server en el clúster de HDInsight.

2. Desde el panel **Configuración**, seleccione **Acciones de script** > **Enviar nueva**.

   ![Imagen del panel de acciones de script](./media/r-server-get-started/scriptaction.png)

3. En el panel **Enviar acción de script**, proporcione la siguiente información:

   * **Nombre**: nombre descriptivo para identificar este script.

   * **URI de script de Bash**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Principal**: este elemento debe estar desactivado.

   * **Trabajo**: este elemento debe estar desactivado.

   * **Zookeeper**: este elemento debe estar desactivado.

   * **Nodos perimetrales**: este elemento debe estar seleccionado.

   * **Parámetros**: los paquetes de R que se van a instalar, por ejemplo, `bitops stringr arules`.

   * **Conservar este script**: este elemento debe estar seleccionado.  

   > [!NOTE]
   > De forma predeterminada, todos los paquetes de R se instalan desde una instantánea del repositorio Microsoft R Application Network coherente con la versión instalada de R Server. Si desea instalar las versiones más recientes de los paquetes, corre el riesgo de que se produzca incompatibilidad. Sin embargo, este tipo de instalación es posible si especifica `useCRAN` como primer elemento de la lista de paquetes, por ejemplo `useCRAN bitops, stringr, arules`.  
   > 
   > Algunos paquetes de R requieren otras bibliotecas de sistema de Linux. Por comodidad, hemos instalado previamente las dependencias que necesitan los 100 paquetes de R más populares. Si los paquetes de R que instale necesitan otras bibliotecas, debe descargar el script base usado aquí y realizar los pasos para instalar las bibliotecas del sistema. A continuación, debe cargar el script modificado a un contenedor de blobs público en Azure Storage y usar el script modificado para instalar los paquetes.
   >
   > Para más información sobre cómo desarrollar acciones de script, consulte [Desarrollo de acciones de script](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Agregar una Acción de script](./media/r-server-get-started/submitscriptaction.png)

4. Seleccione **Crear** para ejecutar el script. Una vez finalizada la ejecución del script, los paquetes de R están disponibles en todos los nodos de trabajador.


## <a name="configure-microsoft-r-server-operationalization"></a>Configuración de la puesta en operación de Microsoft R Server

Cuando se complete el modelado de datos, podrá hacer operativo el modelo para realizar predicciones. Para configurar la puesta en operación de Microsoft R Server, siga estos pasos:

1. Use el comando `ssh` para el nodo perimetral, por ejemplo: 

       ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

2. Cambie el directorio para la versión pertinente y use el comando `sudo dotnet` para el archivo .dll. 

   Para Microsoft R Server 9.1:

       cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
       sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

   Para Microsoft R Server 9.0:

       cd /usr/lib64/microsoft-deployr/9.0.1
       sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Para configurar la puesta en operación de Microsoft R Server con una configuración One-box, haga lo siguiente:

   a. Seleccione `Configure R Server for Operationalization`.

   b. Seleccione `A. One-box (web + compute nodes)`.

   c. Escriba la contraseña del usuario `admin`.

   ![Puesta en operación One-box](./media/r-server-get-started/admin-util-one-box-.png)

4. Como paso opcional, puede realizar una prueba de diagnóstico tal y como se muestra a continuación:

   a. Seleccione `6. Run diagnostic tests`.

   b. Seleccione `A. Test configuration`.

   c. Escriba `admin` para el nombre de usuario y escriba la contraseña del paso de configuración anterior.

   d. Confirme `Overall Health = pass`.

   e. Salga de la utilidad de administración.

   f. Salga de SSH.

   ![Diagnóstico de puesta en operación](./media/r-server-get-started/admin-util-diagnostics.png)


>[!NOTE]
>Si se producen retrasos prolongados al intentar consumir un servicio web creado con funciones de mrsdeploy en un contexto de proceso de Spark, puede que necesite agregar carpetas que falten. La aplicación de Spark pertenece a un usuario llamado *rserve2* cuando se la invoca desde un servicio web mediante las funciones de mrsdeploy. Para evitar este problema:

    #Create these required folders for user rserve2 in local and HDFS
    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    #Create a new Spark compute context 
    rxSparkConnect(reset = TRUE)


En esta fase, la configuración de la puesta en operación está completa. Ahora puede usar el paquete de mrsdeploy en el cliente de R para conectarse a la puesta en operación en el nodo perimetral. A continuación, puede comenzar a usar sus características, como la [ejecución remota](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution) y los [servicios web](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette). Dependiendo de si el clúster se configura en una red virtual o no, puede que necesite configurar la tunelización de reenvío del puerto mediante un inicio de sesión SSH.

### <a name="r-server-cluster-on-a-virtual-network"></a>Clúster de R Server en una red virtual

Asegúrese de que permite que el tráfico pase a través del puerto 12800 al nodo perimetral. De este modo, puede utilizar el nodo perimetral para conectarse a la característica de puesta en operación.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Si `remoteLogin()` no se puede conectarse al nodo perimetral, pero es posible usar SSH para conectarse a dicho nodo, tendrá que comprobar si se ha configurado correctamente la regla para permitir el tráfico en el puerto 12800. Si el problema no desaparece, puede solucionarlo mediante la configuración de la tunelización de reenvío del puerto a través de SSH. Para obtener instrucciones, consulte la siguiente sección.

### <a name="r-server-cluster-not-set-up-on-a-virtual-network"></a>Clúster de R Server no configurado en una red virtual

Si el clúster no está configurado en una red virtual o si tiene problemas con la conectividad a través de una red virtual, puede usar la tunelización de reenvío de puerto de SSH:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

También lo puede configurar con PuTTY:

![conexión SSH de PuTTY](./media/r-server-get-started/putty.png)

Una vez que la sesión SSH esté activa, el tráfico del puerto 12800 del equipo se reenviará al puerto 12800 del nodo perimetral mediante la sesión de SSH. Asegúrese de utilizar `127.0.0.1:12800` en el método `remoteLogin()`. Este método inicia sesión en la puesta en operación del nodo perimetral mediante el reenvío de puerto.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Escalado de los nodos de proceso de la puesta en operación de Microsoft R Server en nodos de trabajo de HDInsight

### <a name="decommission-the-worker-nodes"></a>Retirada de los nodos de trabajo

Microsoft R Server no se administra actualmente mediante Yarn. Si los nodos de trabajo no se retiran, el administrador de recursos de Yarn no funcionará según lo previsto ya que no reconocerá los recursos que utiliza el servidor. Para evitar esta situación, se recomienda la retirada de los nodos de trabajo antes de escalar horizontalmente los nodos de proceso.

Para retirar nodos de trabajo:

1. Inicie sesión en la consola de Ambari del clúster de HDI y seleccione la pestaña **Hosts**.
2. Seleccione los nodos de trabajo que se van a retirar y, a continuación, seleccione **Acciones** > **Hosts seleccionados** > **Hosts** > **Activar modo de mantenimiento**. Por ejemplo, en la siguiente imagen se han seleccionado wn3 y wn4 para su retirada.  

   ![Captura de pantalla de los comandos para activar el modo de mantenimiento](./media/r-server-get-started/get-started-operationalization.png)  

3. Seleccione **Acciones** > **Hosts seleccionados** > **DataNodes** > **Retirar**.
4. Seleccione **Acciones** > **Hosts seleccionados** > **NodeManagers** > **Retirar**.
5. Seleccione **Acciones** > **Hosts seleccionados** > **DataNodes** > **Detener**.
6. Seleccione **Acciones** > **Hosts seleccionados** > **DataNodes** > **Detener**.
7. Seleccione **Acciones** > **Hosts seleccionados** > **Hosts** > **Detener todos los componentes**.
8. Anule la selección de los nodos de trabajo y selecciones los nodos principales.
9. Seleccione **Acciones** > **Hosts seleccionados** > **Hosts** > **Reiniciar todos los componentes**.

### <a name="configure-compute-nodes-on-each-decommissioned-worker-node"></a>Configuración de los nodos de proceso en cada nodo de trabajo retirado

1. Utilice SSH para conectarse a cada nodo de trabajo retirado.
2. Ejecute la utilidad de administración mediante `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`.
3. Escriba `1` para seleccionar la opción `Configure R Server for Operationalization`.
4. Escriba `c` para seleccionar la opción `C. Compute node`. Este paso configura el nodo de proceso en el nodo de trabajo.
5. Salga de la utilidad de administración.

### <a name="add-compute-nodes-details-on-the-web-node"></a>Adición de los detalles de los nodos de proceso en el nodo web

Una vez que todos los nodos de trabajo retirados se han configurado para ejecutarse en el nodo de proceso, vuelva al nodo perimetral y agregue las direcciones IP de los nodos de trabajo retirados a la configuración del nodo web de Microsoft R Server:

1. Use SSH para conectarse al nodo perimetral.
2. Ejecute `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.
3. Busque la sección `URIs` y agregue la dirección IP de los nodos de trabajo y los detalles del puerto.

    ![Línea de comandos del nodo perimetral](./media/r-server-get-started/get-started-op-cmd.png)


## <a name="troubleshoot"></a>Solución de problemas

Si experimenta problemas con la creación de clústeres de HDInsight, consulte los [requisitos de control de acceso](../hdinsight-administer-use-portal-linux.md#create-clusters).


## <a name="next-steps"></a>pasos siguientes

Ahora debería saber cómo crear un clúster de HDInsight que incluya R Server. También ha aprendido los conceptos básicos del uso de la consola de R desde una sesión de SSH. Los siguientes temas explican otras maneras de administrar y trabajar con R Server en HDInsight:

* [Opciones de contexto de proceso para R Server en HDInsight (versión preliminar)](r-server-compute-contexts.md)
* [Opciones de Azure Storage para R Server en HDInsight](r-server-storage.md)
