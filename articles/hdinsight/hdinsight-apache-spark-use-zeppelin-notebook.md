---
title: "Uso de acciones de script para instalar cuadernos de Zeppeling para un clúster Spark en Azure HDInsight | Microsoft Docs"
description: "Instrucciones paso a paso sobre cómo instalar y usar cuadernos de Zeppelin con clústeres Spark en HDInsight Linux."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: cb276220-fb02-49e2-ac55-434fcb759629
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: eadf0611ca46a975c364a1b073828c6c3faf5f77


---
# <a name="install-zeppelin-notebooks-for-apache-spark-cluster-on-hdinsight"></a>Instalación de cuadernos de Zeppelin en un clúster de Apache Spark en HDInsight

Obtenga información sobre cómo instalar cuadernos de Zeppelin en clústeres Apache Spark y cómo usar los cuadernos de Zeppelin para ejecutar trabajos de Spark.

> [!IMPORTANT]
> Si ha aprovisionado clústeres de Spark 1.6 en HDInsight 3.5, puede acceder a cuadernos de Zeppelin de forma predeterminada mediante las instrucciones que se indican en [Uso de cuadernos de Zeppelin con un clúster Apache Spark en HDInsight Linux](hdinsight-apache-spark-zeppelin-notebook.md). Si quiere usar Zeppelin en las versiones de clúster de HDInsight 3.3 y 3.4, o en Spark 2.0 en HDInsight 3.5, debe seguir las instrucciones de este artículo para instalar Zeppelin.
>
>

**Requisitos previos:**

* Antes de comenzar este tutorial, debe tener una suscripción a Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).
* Un cliente SSH. Para las distribuciones de Linux y Unix o Macintosh OS X, el comando `ssh` se proporciona con el sistema operativo. Para Windows, se recomienda [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

  > [!NOTE]
  > Si desea usar un cliente SSH distinto de `ssh` o PuTTY, vea la documentación de su cliente sobre cómo establecer un túnel SSH.
  >
  >
* Un explorador web que se puede configurar para usar un proxy SOCKS
* **(opcional)**: un complemento como [FoxyProxy](http://getfoxyproxy.org/,) que puede aplicar reglas que se limitan a enrutar solicitudes específicas a través del túnel.

  > [!WARNING]
  > Sin un complemento como FoxyProxy, todas las solicitudes realizadas a través del explorador se pueden enrutar a través del túnel. Esto puede producir una carga más lenta de las páginas web en el explorador.
  >
  >

## <a name="install-zeppelin-on-a-spark-cluster"></a>Instalación de Zeppelin en un clúster Spark
Puede instalar Zeppelin en un clúster Spark mediante la acción de scripts. La acción de scripts usa scripts personalizados para instalar los componentes en el clúster que no están disponibles de forma predeterminada. Puede utilizar el script personalizado para instalar Zeppelin desde el Portal de Azure, mediante el uso del SDK de .NET para HDInsight o Azure PowerShell. Puede utilizar el script para instalar Zeppelin como parte de la creación del clúster, o una vez que el clúster está en funcionamiento. Los vínculos en las secciones siguientes proporcionan las instrucciones sobre cómo hacerlo.

### <a name="using-the-azure-portal"></a>Uso del portal de Azure
Para obtener instrucciones sobre cómo usar el Portal de Azure con el fin de ejecutar acciones de scripts para instalar Zeppelin, vea [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Debe realizar un par de cambios en las instrucciones de ese artículo.

* Debe utilizar el script para instalar Zeppelin. El script personalizado para instalar Zeppelin en un clúster Spark de HDInsight está disponible en los siguientes vínculos:

  * Para clústeres de Spark 1.6.0: `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Para clústeres de Spark 1.5.2: `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* Debe ejecutar la acción de scripts solo en el nodo principal.
* El script no necesita ningún parámetro.

### <a name="using-hdinsight-net-sdk"></a>Uso del SDK .NET de HDInsight
Para obtener instrucciones sobre cómo usar SDK .NET de HDInsight para ejecutar acciones de scripts para instalar Zeppelin, vea [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Debe realizar un par de cambios en las instrucciones de ese artículo.

* Debe utilizar el script para instalar Zeppelin. El script personalizado para instalar Zeppelin en un clúster Spark de HDInsight está disponible en los siguientes vínculos:

  * Para clústeres de Spark 1.6.0: `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
  * Para clústeres de Spark 1.5.2: `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`
* El script no necesita ningún parámetro.
* Establece el tipo de clúster que se va a crear en Spark.

### <a name="using-azure-powershell"></a>Uso de Azure PowerShell
Utilice el siguiente fragmento de código de PowerShell para crear un clúster Spark en HDInsight Linux con Zeppelin instalado. En función de la versión del clúster de Spark que tenga, debe actualizar el fragmento de código de PowerShell para incluir el vínculo al script personalizado correspondiente.

* Para clústeres de Spark 1.6.0: `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark160-v01.sh`
* Para clústeres de Spark 1.5.2: `https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh`

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    Login-AzureRMAccount

    # PROVIDE VALUES FOR THE VARIABLES
    $clusterAdminUsername="admin"
    $clusterAdminPassword="<<password>>"
    $clusterSshUsername="adminssh"
    $clusterSshPassword="<<password>>"
    $clusterName="<<clustername>>"
    $clusterContainerName=$clusterName
    $resourceGroupName="<<resourceGroupName>>"
    $location="<<region>>"
    $storage1Name="<<storagename>>"
    $storage1Key="<<storagekey>>"
    $subscriptionId="<<subscriptionId>>"

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
    $clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
    $passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
    $clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)

    $azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
    $azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
    $azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"

    Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"

    New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"

## <a name="access-the-zeppelin-notebook"></a>Acceso al cuaderno de Zeppelin

Cuando haya instalado correctamente Zeppeling mediante la acción de script, puede usar los siguientes pasos para acceder al cuaderno de Zeppeling en el clúster de Spark siguiendo los pasos que se indican a continuación. En esta sección, verá cómo ejecutar instrucciones %sql y %hive.

1. En el explorador web, abra el siguiente punto de conexión:

        https://CLUSTERNAME.azurehdinsight.net/zeppelin

   
2. Cree un nuevo notebook. En el panel de encabezado, haga clic en **Cuaderno** y luego en **Create New Note** (Crear nota).

    ![Creación de un nuevo cuaderno de Zeppeling](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.createnewnote.png "Creación de un nuevo cuaderno de Zeppeling")

    En la misma página, en el encabezado **Cuaderno**, debería ver un nuevo cuaderno con un nombre que empiece por **Note XXXXXXXXX** (Nota XXXXXXXXX). Haga clic en el nuevo cuaderno.
3. En la página web del nuevo cuaderno, haga clic en el encabezado y cambie el nombre del cuaderno si quiere. Presione ENTRAR para guardar el cambio de nombre. Además, asegúrese de que el encabezado de cuaderno muestre el estado **Connected** (Conectado) en la esquina superior derecha.

    ![Estado del cuaderno de Zeppeling](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.newnote.connected.png "Estado del cuaderno de Zeppeling")

### <a name="run-sql-statements"></a>Ejecución de instrucciones de T-SQL
1. Cargue los datos de ejemplo en una tabla temporal. Cuando crea un clúster Spark en HDInsight, el archivo de datos de ejemplo, **hvac.csv**, se copia en la cuenta de almacenamiento asociada en **\HdiSamples\SensorSampleData\hvac**.

    En el párrafo vacío que se crea de manera predeterminada en el nuevo cuaderno, pegue el siguiente fragmento.

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0),
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()

        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")

    Presione **MAYÚS+ENTRAR** o haga clic en el botón **Reproducir** del párrafo para ejecutar el fragmento de código. El estado en la esquina derecha del párrafo debería avanzar de READY (Listo), PENDING (Pendiente) o RUNNING (En ejecución) a FINISHED (Finalizado). El resultado se muestra en la parte inferior del mismo párrafo. La captura de pantalla es similar a la siguiente:

    ![Creación de una tabla temporal a partir de datos sin procesar](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.loaddDataintotable.png "Creación de una tabla temporal a partir de datos sin procesar")

    También puede proporcionar un título para cada párrafo. En la esquina derecha, haga clic en el icono **Configuración** y luego haga clic en **Mostrar título**.
2. Ahora puede ejecutar instrucciones Spark SQL en la tabla **hvac** . Pegue la siguiente consulta en un nuevo párrafo. La consulta recupera el identificador del edificio y la diferencia entre la temperatura objetivo y la real para cada edificio en una fecha determinada. Presione **MAYÚS + ENTRAR**.

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date
        from hvac
        where date = "6/1/13"

    La instrucción **%sql** al principio indica al cuaderno que use el intérprete Spark SQL. Puede consultar los intérpretes definidos en la pestaña **Interpreter** (Intérprete) en el encabezado del cuaderno.

    En la captura de pantalla siguiente se muestra el resultado.

    ![Ejecución de una instrucción Spark SQL mediante el cuaderno](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Ejecución de una instrucción Spark SQL mediante el cuaderno")

     Haga clic en las opciones de presentación (resaltadas con el rectángulo) para alternar entre diferentes representaciones del mismo resultado. Haga clic en **Settings** (Configuración) para elegir qué constituye la clave y los valores de la salida. En la captura de pantalla anterior se usa **buildingID** como clave y la media de **temp_diff** como valor.
3. También puede ejecutar instrucciones Spark SQL usando variables en la consulta. El siguiente fragmento de código muestra cómo definir una variable, **Temp**, en la consulta con los valores posibles con los que quiere hacer la consulta. Cuando ejecuta la consulta por primera vez, se rellena una lista desplegable automáticamente con los valores especificados para la variable.

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
        from hvac
        where targettemp > "${Temp = 65,65|75|85}"

    Pegue este fragmento de código en un nuevo párrafo y presione **MAYÚS + ENTRAR**. En la captura de pantalla siguiente se muestra el resultado.

    ![Ejecución de una instrucción Spark SQL mediante el cuaderno](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Ejecución de una instrucción Spark SQL mediante el cuaderno")

    Para las consultas posteriores, puede seleccionar un nuevo valor en la lista desplegable y después volver a ejecutar la consulta. Haga clic en **Settings** (Configuración) para elegir qué constituye la clave y los valores de la salida. La captura de pantalla anterior usa **buildingID** como clave, la media de **temp_diff** como valor y **targettemp** como grupo.
4. Reinicie el intérprete Spark SQL para salir de la aplicación. Haga clic en la pestaña **Interpreter** (Intérprete) en la parte superior y, para el intérprete Spark, haga clic en **Reiniciar** (Reiniciar).

    ![Reinicio del intérprete de Zeppeling](./media/hdinsight-apache-spark-use-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Reinicio del intérprete de Zeppeling")

### <a name="run-hive-statements"></a>Ejecución de instrucciones de Hive
1. En Zeppelin Notebook, haga clic en el botón **Interpreter** (Intérprete).

    ![Actualización del intérprete de Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-1.png "Actualización del intérprete de Hive")
2. Para el intérprete de **Hive**, haga clic en **editar**.

    ![Actualización del intérprete de Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-2.png "Actualización del intérprete de Hive")

    Actualice las siguientes propiedades.

   * Establezca **default.password** en la contraseña que especificó para el usuario administrador al crear el clúster de HDInsight Spark.
   * Establezca **default.url`jdbc:hive2://<spark_cluster_name>.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2` en **. Sustituya **\<nombre_clúster_spark>** por el nombre del clúster de Spark.
   * Establezca **default.user** en el nombre del usuario administrador que especificó al crear el clúster. Por ejemplo, *admin*.
3. Haga clic en **Guardar** y, cuando se le pida reiniciar el intérprete de Hive, haga clic en **Aceptar**.
4. Cree un nuevo cuaderno y ejecute la instrucción siguiente para obtener una lista de todas las tablas de Hive del clúster.

        %hive
        SHOW TABLES

    De forma predeterminada, un clúster de HDInsight tiene una tabla de ejemplo denominada " **hivesampletable** ", por tanto, debería ver el siguiente resultado.

    ![Salida de Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-3.png "Salida de Hive")
5. Ejecute la instrucción siguiente para obtener una lista los registros de la tabla.

        %hive
        SELECT * FROM hivesampletable LIMIT 5

    Debe ver un resultado parecido al siguiente.

    ![Salida de Hive](./media/hdinsight-apache-spark-use-zeppelin-notebook/zeppelin-update-hive-interpreter-4.png "Salida de Hive")

## <a name="a-nameseealsoasee-also"></a><a name="seealso"></a>Otras referencias
* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (Linux)](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applications (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones Scala Spark)](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md



<!--HONumber=Jan17_HO4-->


