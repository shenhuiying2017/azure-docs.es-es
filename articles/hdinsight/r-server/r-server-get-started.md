---
title: Introducción a R Server en HDInsight - Azure | Microsoft Docs
description: Aprenda a crear un motor Apache Spark en un clúster de HDInsight que incluya R Server y a enviar un script de R al clúster.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: d6910ab257312626ca25126721410edeed6cdeae
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
ms.locfileid: "31411488"
---
# <a name="get-started-with-r-server-cluster-on-azure-hdinsight"></a>Introducción al clúster de R Server en Azure HDInsight

Azure HDInsight incluye una opción de R Server que se integra en el clúster de HDInsight. Esta opción permite que los scripts de R usen Spark y MapReduce para ejecutar cálculos distribuidos. En este artículo aprenderá a crear una instancia de R Server en un clúster de HDInsight. A continuación, aprenderá a ejecutar un script de R que muestra el uso de Spark para cálculos de R distribuidos.


## <a name="prerequisites"></a>requisitos previos

* **Una suscripción a Azure**: antes de comenzar este tutorial, debe tener una suscripción a Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Obtener una evaluación gratuita de Azure para probar Hadoop en HDInsight) para más información.
* **Un cliente de Secure Shell (SSH)**: el cliente de SSH se usa para conectarse al clúster de HDInsight de forma remota y ejecutar comandos directamente desde el clúster. Para más información, consulte [Uso SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Creación de un clúster mediante Azure Portal

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. Haga clic en **Crear un recurso** > **Datos y análisis** > **HDInsight**.

3. En **Básico**, escriba la siguiente información:

    * **Nombre del clúster**: nombre del clúster de HDInsight.
    * **Suscripción**: seleccione la suscripción que vaya a usar.
    * **Nombre de usuario de inicio de sesión del clúster** y **contraseña de inicio de sesión de clúster**: inicio de sesión de acceso al clúster a través de HTTPS. Use estas credenciales para acceder a servicios como la interfaz de usuario de Ambari Web o la API de REST.
    * **Nombre de usuario de Secure Shell (SSH)**: inicio de sesión para acceder al clúster a través de SSH. De forma predeterminada, la contraseña es la misma que la de inicio de sesión en el clúster.
    * **Grupo de recursos**: en el que se va a crear el clúster.
    * **Ubicación**: región de Azure donde se va crear el clúster.

        ![Detalles básicos del clúster](./media/r-server-get-started/clustername.png)

4. Seleccione **Tipo de clúster** y establezca los siguientes valores en la sección **Configuración de clúster**:

    * **Tipo de clúster**: R Server

    * **Sistema operativo**: Linux

    * **Versión**: R Server 9.1 (HDI 3.6). Puede consultar las notas de las versiones disponibles de R Server [aquí](https://docs.microsoft.com/machine-learning-server/whats-new-in-r-server#r-server-91).

    * **R Studio community edition for R Server**: este IDE basado en el explorador se instala de forma predeterminada en el nodo perimetral. Si prefiere que no se instale, desactive la casilla. Si decide instalarlo, la dirección URL para acceder al inicio de sesión de RStudio Server está disponible en la hoja de aplicación del portal del clúster, una vez que se ha creado.

        ![Detalles básicos del clúster](./media/r-server-get-started/clustertypeconfig.png)

4. Después de seleccionar el tipo de clúster, use el botón __Seleccionar__ para establecer el tipo de clúster. A continuación, use el botón __Siguiente__ para finalizar la configuración básica.

5. Desde la sección **Storage**, seleccione o cree una cuenta de Storage. Para seguir los pasos de este documento, deje los demás campos de esta sección con los valores predeterminados. Use el botón __Siguiente__ para guardar la configuración de almacenamiento.

    ![Configuración de la cuenta de almacenamiento de HDInsight](./media/r-server-get-started/cluster-storage.png)

6. En la sección **Resumen**, revise la configuración para el clúster. Use los vínculos __Edit__ (Editar) para cambiar cualquier configuración incorrecta. Por último, use el botón __Crear__ para crear el clúster.

    ![Configuración de la cuenta de almacenamiento de HDInsight](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]
    > Un clúster puede tardar hasta 20 minutos en crearse.

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Conexión a RStudio Server

Si decidió instalar RStudio Server Community Edition como parte de su clúster de HDInsight, puede acceder al inicio de sesión de RStudio mediante uno de los dos métodos siguientes:

* **Opción 1**: vaya a la siguiente dirección URL (donde **CLUSTERNAME** es el nombre del clúster de R Server que ha creado):

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **Opción 2**: abra el clúster de R Server en Azure Portal y, en **Vínculos rápidos**, haga clic en **R Server Dashboards** (Paneles de R Server).

     ![Configuración de la cuenta de almacenamiento de HDInsight](./media/r-server-get-started/dashboard-quick-links.png)

    En **Cluster Dashboards** (Paneles del clúster), haga clic en **R Studio Server**.

    ![Configuración de la cuenta de almacenamiento de HDInsight](./media/r-server-get-started/r-studio-server-dashboard.png)

   > [!IMPORTANT]
   > Independientemente del método que se use, la primera vez que inicie sesión deberá autenticarse dos veces.  En el primer mensaje de autenticación, proporcione el *identificador de usuario administrador del clúster* y la *contraseña*. En el segundo mensaje de autenticación, proporcione el *identificador de usuario de SSH* y la *contraseña*. En los sucesivos inicios de sesión solo se requieren las credenciales de SSH.

Una vez conectado, la pantalla debe ser similar a la siguiente captura de pantalla:

![Conexión a RStudio](./media/r-server-get-started/connect-to-r-studio.png)

## <a name="run-a-sample-job"></a>Ejecución de un trabajo de ejemplo

Puede enviar un trabajo mediante funciones de ScaleR. Este es un ejemplo de los comandos que se usan para ejecutar un trabajo:

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
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

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-cluster-edge-node"></a>Conexión al nodo perimetral del clúster

En esta sección, aprenderá a conectarse al nodo perimetral de un clúster de HDInsight para R Server mediante SSH. Para familiarizarse con el uso de SSH, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

El comando SSH para conectarse al nodo perimetral de R Server es:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

Para encontrar el comando SSH de su clúster, en Azure Portal, haga clic en el nombre del clúster, haga clic en **SSH + Cluster login** (SSH e inicio de sesión del clúster) y, en **Nombre de host**, seleccione el nodo perimetral. Esto hace que aparezca la información del punto de conexión de SSH para el nodo perimetral.

![Imagen del punto de conexión SSH del nodo perimetral](./media/r-server-get-started/sshendpoint.png)

Si usó una contraseña para proteger la cuenta de usuario SSH, se le pedirá que la escriba. Si usa una clave pública, tal vez tenga que usar el parámetro `-i` para especificar la ruta de acceso a la correspondiente clave privada. Por ejemplo: 

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Una vez conectado, obtendrá un símbolo del sistema similar al siguiente:

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-server-console"></a>Uso de la consola de R Server

1. En la sesión SSH, use el siguiente comando para iniciar la consola de R:  

        R

2. Debería ver una salida con la versión de R Server, además de otra información.
    
3. Desde el símbolo de sistema de `>` , puede escribir el código de R. R Server en HDInsight incluye paquetes que le permiten interactuar fácilmente con Hadoop y ejecutar cálculos distribuidos. Por ejemplo, use el siguiente comando para ver la raíz del sistema de archivos predeterminado del clúster de HDInsight:

        rxHadoopListFiles("/")

4. También puede usar el direccionamiento de estilo WASB.

        rxHadoopListFiles("wasb:///")

5. Para salir de la consola de R, use el siguiente comando:

        quit()

## <a name="automated-cluster-creation"></a>Creación automatizada del clúster

Puede automatizar la creación de un clúster de R Server para HDInsight mediante plantillas de Azure Resource Manager, el SDK y PowerShell.

* Para crear un clúster de R Server con una plantilla de Azure Resource Manager, consulte [Deploy an R-server HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/) (Implementación de un clúster de R Server para HDInsight).
* Para crear un clúster de R Server mediante el SDK de .NET, consulte [Crear clústeres basados en Linux en HDInsight con el SDK de .NET](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Para crear un clúster de R Server con PowerShell, consulte el artículo [Crear clústeres de HDInsight con Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

## <a name="delete-the-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Solución de problemas

Si experimenta problemas con la creación de clústeres de HDInsight, consulte los [requisitos de control de acceso](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Pasos siguientes

En este artículo aprendió a crear un nuevo clúster de R Server en Azure HDInsight y los conceptos básicos de uso de la consola de R desde una sesión de SSH. En los siguientes artículos se explican otras maneras de administrar y trabajar con R Server en HDInsight:

* [Envío de trabajos desde Herramientas de R para Visual Studio](r-server-submit-jobs-r-tools-vs.md)
* [Manage R Server cluster on HDInsight](r-server-hdinsight-manage.md) (Administración de un clúster de R Server en HDInsight)
* [Operationalize R Server cluster on HDInsight](r-server-operationalize.md) (Operacionalización de un clúster de R Server en HDInsight)
* [Opciones de contexto de proceso para un clúster de R Server en HDInsight](r-server-compute-contexts.md)
* [Opciones de Azure Storage para un clúster de R Server en HDInsight](r-server-storage.md)
