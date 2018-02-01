---
title: "Envío de trabajos desde Herramientas de R para Visual Studio en Azure HDInsight | Microsoft Docs"
description: "Envío de trabajos de R desde la máquina local de Visual Studio hasta un clúster de HDInsight."
services: hdinsight
documentationcenter: 
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: 1a82ba7790f739768156a8bee33a74d7130e24e1
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Envío de trabajos desde Herramientas de R para Visual Studio

[Herramientas de R para Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) es una extensión gratuita de código abierto de las ediciones Community (gratuita), Professional y Enterprise de [Visual Studio 2017](https://www.visualstudio.com/downloads/) y [Visual Studio 2015 Update 3](http://go.microsoft.com/fwlink/?LinkId=691129) o superior.

RTVS mejora el flujo de trabajo de R al ofrecer herramientas como la [ventana R interactivo](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), IntelliSense (finalización de código), [visualización de trazados](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) mediante bibliotecas de R como ggplot2 y ggviz, [depuración de código de R](https://docs.microsoft.com/visualstudio/rtvs/debugging) y muchas más.

## <a name="set-up-your-environment"></a>Configuración del entorno

1. Instale [Herramientas de R para Visual Studio](https://docs.microsoft.com/visualstudio/rtvs/installation).

    ![Instalación de RTVS en Visual Studio de 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Seleccione la carga de trabajo *Aplicaciones de ciencia de datos y de análisis* y luego seleccione las opciones **Compatibilidad con el lenguaje R**, **Compatibilidad de runtime con las herramientas de desarrollo de R** y **Microsoft R Client**.

3. Debe tener las claves públicas y privadas para la autenticación de SSH.
<!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Instale [R Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) en su máquina. R Server proporciona las funciones [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) y `RxSpark`.

5. Instale [PuTTY](http://www.putty.org/) para proporcionar un contexto de proceso para ejecutar funciones de `RevoScaleR` desde el cliente local hasta el clúster de HDInsight.

6. Tiene la opción de aplicar la Configuración de ciencia de datos al entorno de Visual Studio, lo que proporciona un nuevo diseño para el área de trabajo de las herramientas de R.
    1. Para guardar la configuración actual de Visual Studio, use el comando **Herramientas > Importar y exportar configuraciones** y, luego, seleccione **Exportar la configuración de entorno seleccionada** y especifique un nombre de archivo. Para restaurar esa configuración, use el mismo comando y seleccione **Importar la configuración de entorno seleccionada**.

    2. Vaya al elemento de menú **Herramientas de R** y seleccione **Configuración de ciencia de datos...**.

        ![Configuración de ciencia de datos...](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

    > [!NOTE]
    > Mediante el enfoque del paso 1, también puede guardar y restaurar su diseño de ciencia de datos personalizado en lugar de repetir el comando **Configuración de ciencia de datos**.

## <a name="execute-local-r-methods"></a>Ejecución de métodos de R locales

1. Cree su [clúster de HDInsight para R Server](r-server-get-started.md).
2. Instale la [extensión RTVS](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Descargue el [archivo ZIP de ejemplos](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Abra `examples/Examples.sln` para iniciar la solución en Visual Studio.
5. Abra el archivo `1-Getting Started with R.R` de la carpeta de la solución `A first look at R`.
6. Desde la parte superior del archivo, presione Ctrl + Entrar para enviar cada línea, de una en una, a la ventana de R interactivo. Puede que algunas líneas tarden un rato mientras instalan los paquetes.
    * Existe la alternativa de seleccionar todas las líneas del archivo de R (Ctrl+A) y luego ejecutar todo (Ctrl+Entrar) o seleccionar el icono Execute Interactive (Ejecutar Interactivo) en la barra de herramientas.
        ![Execute interactive](./media/r-server-submit-jobs-r-tools-vs/execute-interactive.png) (Ejecutar Interactivo)

7. Después de ejecutar todas las líneas del script, debería ver una salida similar a la siguiente:

    ![Configuración de ciencia de datos...](./media/r-server-submit-jobs-r-tools-vs/workspace.png)

## <a name="submit-jobs-to-an-hdinsight-r-cluster"></a>Envío de trabajos a un clúster de R para HDInsight

Mediante una instancia de Microsoft R Server/Microsoft R Client desde un equipo Windows equipado con PuTTY, puede crear un contexto de proceso que ejecute funciones `RevoScaleR` distribuidas desde el cliente local hasta el clúster de HDInsight. Use `RxSpark` para crear el contexto de proceso y especifique el nombre de usuario, el nodo perimetral del clúster de Hadoop, los modificadores de SSH, etc.

1. Para encontrar el nombre de host del nodo perimetral, abra el panel del clúster de R para HDInsight en Azure y seleccione **Secure Shell (SSH)** en el menú superior del panel de información general.

    ![Secure Shell (SSH)](./media/r-server-submit-jobs-r-tools-vs/ssh.png)

2. Copie el valor de **Nombre de host del nodo perimetral**.

    ![Nombre de host del nodo perimetral](./media/r-server-submit-jobs-r-tools-vs/edge-node.png)

3. Pegue el código siguiente en la ventana de R interactivo en Visual Studio y modifique los valores de las variables de configuración para que coincidan con el entorno.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
      sshHostname = mySshHostname,
      sshSwitches = mySshSwitches,
      sshProfileScript = mySshProfileScript,
      consoleOutput = TRUE,
      hdfsShareDir = myHdfsShareDir,
      shareDir = myShareDir,
      sshClientDir = mySshClientDir
    )
    
    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```
    
    ![Configuración del contexto de Spark](./media/r-server-submit-jobs-r-tools-vs/spark-context.png)

4. Ejecute los siguientes comandos en la ventana de R interactivo:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Debería ver una salida similar a la siguiente:

    ![Ejecución correcta de comandos de rx](./media/r-server-submit-jobs-r-tools-vs/rx-commands.png)

5. Compruebe que el comando `rxHadoopCopy` copió correctamente el archivo `people.json` de la carpeta de datos de ejemplo en la carpeta `/user/RevoShare/newUser` recién creada:

    1. En el panel del clúster de R para HDInsight en Azure, seleccione **Cuentas de almacenamiento** en el menú izquierdo.

        ![Cuentas de almacenamiento](./media/r-server-submit-jobs-r-tools-vs/storage-accounts.png)

    2. Seleccione la cuenta de almacenamiento predeterminada del clúster y anote el nombre del contenedor o directorio.

    3. Seleccione **Contenedores** en el menú izquierdo del panel de la cuenta de almacenamiento.

        ![Contenedores](./media/r-server-submit-jobs-r-tools-vs/containers.png)

    4. Seleccione el nombre del contenedor del clúster, vaya a la carpeta **user** (puede que tenga que hacer clic en *Cargar más* en la parte inferior de la lista), luego seleccione *RevoShare* y, a continuación, **newUser**. El archivo `people.json` se debe mostrar en la carpeta `newUser`.

        ![Archivo copiado](./media/r-server-submit-jobs-r-tools-vs/copied-file.png)

6. Cuando haya terminado de usar el contexto de Spark actual, debe detenerlo. No puede ejecutar varios contextos a la vez.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>pasos siguientes

* [Opciones de contexto de proceso para R Server en HDInsight (versión preliminar)](r-server-compute-contexts.md)
* La [combinación de ScaleR y SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) proporciona un ejemplo de predicciones de retrasos en los vuelos.
<!-- * You can also submit R jobs with the [R Studio Server](hdinsight-submit-jobs-from-r-studio-server.md) -->
