---
title: "Uso de la acción de script para instalar Spark en el clúster de Hadoop| Microsoft Docs"
description: "Obtenga información acerca de cómo personalizar un clúster de HDInsight con Spark mediante la acción de script."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 7ebf4e2b-0742-4a2f-b429-60dc30d3f905
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: c9e3c1d2a1f5b83c59fa2a22f3cb4d89df203384
ms.openlocfilehash: 4fa808b50b56122976cc176c55937f9443f31097


---
# <a name="install-and-use-spark-on-windows-based-hdinsight-clusters-using-script-action"></a>Instalación y uso de Spark en clústeres de HDInsight basados en Windows mediante acciones de script

> [!IMPORTANT]
> Este artículo está en desuso. HDInsight ofrece ahora Spark, un tipo de clúster de primera clase para los clústeres basados en Linux, lo que significa que puede crear directamente un clúster de Spark sin modificar un clúster de Hadoop mediante acciones de script. Además, los pasos de este tutorial solo se aplican a clústeres de HDInsight basados en Windows. HDInsight solo está disponible en Windows en versiones inferiores a la 3.4. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

Obtenga información acerca de cómo instalar Spark en clústeres de HDInsight basados en Windows mediante la acción de script y cómo ejecutar consultas de Spark en clústeres de HDInsight.

**Artículos relacionados**

* [Creación de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md): información general sobre la creación de clústeres de HDInsight.
* [Introducción a Apache Spark en HDInsight:](hdinsight-apache-spark-jupyter-spark-sql.md)creación de un clúster Spark de HDInsight.
* [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-cluster-customize]: información general sobre la personalización de clústeres de HDInsight mediante la acción de script.
* [Desarrollo de acciones de script con HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-spark"></a>¿Qué es Spark?
<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> es un marco de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones analíticas de Big Data. Las capacidades de cálculo en memoria de Spark lo convierten en una buena opción para algoritmos iterativos en los cálculos de gráficos y aprendizaje automático.

Spark también se puede usar para llevar a cabo el procesamiento de datos convencional basado en disco. Spark mejora el marco de MapReduce tradicional evitando escrituras en disco en las etapas intermedias. Además, Spark es compatible con el Sistema de archivos distribuido de Hadoop (HDFS) y el almacenamiento de blobs de Azure, por lo que se pueden procesar los datos existentes fácilmente a través de Spark.

Este tema proporciona instrucciones sobre cómo personalizar un clúster de HDInsight para instalar Spark.

## <a name="install-spark-using-the-azure-portal"></a>Instalación de Spark mediante el Portal de Azure
Hay un script de ejemplo para instalar Spark en un clúster de HDInsight disponible desde un blob de almacenamiento de Azure de solo lectura en [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1). Este script puede instalar Spark 1.2.0 o Spark 1.0.2, según la versión del clúster de HDInsight que cree.

* Si usa el script al crear un clúster de **HDInsight 3.2**, se instala **Spark 1.2.0**.
* Si usa el script al crear un clúster de **HDInsight 3.1**, se instala **Spark 1.0.2**.

Puede modificar este script o crear su propio script para instalar otras versiones de Spark.

> [!NOTE]
> El script de ejemplo solo funciona con clústeres de HDInsight 3.1 y 3.2. Para obtener más información acerca de las versiones de clústeres de HDInsight, consulte las [versiones de clústeres de HDInsight](hdinsight-component-versioning.md).

1. Comience a crear un clúster mediante la opción **CREACIÓN PERSONALIZADA**, como se describe en [Creación de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md). Elija la versión de clúster según lo siguiente:

   * Si desea instalar **Spark 1.2.0**, cree un clúster de HDInsight 3.2.
   * Si desea instalar **Spark 1.0.2**, cree un clúster de HDInsight 3.1.
2. En la página **Acciones de script** del asistente, haga clic en **Agregar acción de script** para proporcionar detalles acerca de la acción de script, como se muestra a continuación:

    ![Uso de la acción de script para personalizar un clúster](./media/hdinsight-hadoop-spark-install/HDI.CustomProvision.Page6.png "Uso de la acción de script para personalizar un clúster")

    <table border='1'>
        <tr><th>Propiedad</th><th>Valor</th></tr>
        <tr><td>Nombre</td>
            <td>Especifique un nombre para la acción de script. Por ejemplo, <b>Instalar Spark</b>.</td></tr>
        <tr><td>Identificador URI de script</td>
            <td>Especifique el Identificador uniforme de recursos (URI) al script que se ha invocado para personalizar el clúster. Por ejemplo, <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1</i></td></tr>
        <tr><td>Tipo de nodo</td>
            <td>Especifique los nodos en los que se ejecuta el script de personalización. Puede elegir <b>Todos los nodos</b>, <b>Solo nodos principales</b> o <b>Solo nodos de trabajo</b>.
        <tr><td>Parámetros</td>
            <td>Especifique los parámetros, si lo requiere el script. El script para instalar Spark no requiere ningún parámetro, por lo que puede dejarlo en blanco.</td></tr>
    </table>

    Puede agregar más de una acción de script para instalar varios componentes en el clúster. Después de haber agregado los scripts, haga clic en la marca de verificación para comenzar a crear el clúster.

También puede utilizar el script para instalar Spark en HDInsight con PowerShell de Azure o el SDK de .NET de HDInsight. Más adelante en este tema se proporcionan instrucciones para estos procedimientos.

## <a name="use-spark-in-hdinsight"></a>Uso de Spark en HDInsight
Spark proporciona las API en Scala, Python y Java. También puede usar el shell de Spark interactivo para ejecutar consultas de Spark. Esta sección proporciona instrucciones sobre cómo usar los diferentes enfoques para trabajar con Spark:

* [Uso del shell de Spark para ejecutar consultas interactivas](#sparkshell)
* [Uso del shell de Spark para ejecutar consultas de Spark SQL](#sparksql)
* [Uso de un programa de Scala independiente](#standalone)

### <a name="a-namesparkshellause-the-spark-shell-to-run-interactive-queries"></a><a name="sparkshell"></a>Uso del shell de Spark para ejecutar consultas interactivas
Realice los pasos siguientes para ejecutar consultas de Spark desde un shell de Spark interactivo. En esta sección, se ejecutará una consulta de Spark en un archivo de datos de ejemplo (/example/data/gutenberg/davinci.txt) que está disponible, de manera predeterminada, en los clústeres de HDInsight.

1. En el portal de Azure, habilite el Escritorio remoto para el clúster que ha creado con Spark instalado y, a continuación, remoto en el clúster. Para obtener instrucciones, vea [Conexión a los clústeres de HDInsight con RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. En la sesión del Protocolo de escritorio remoto (RDP), desde el escritorio, abra la línea de comandos de Hadoop (desde un acceso directo del escritorio) y vaya a la ubicación donde está instalado Spark; por ejemplo, **C:\apps\dist\spark-1.2.0**.
3. Ejecute el siguiente comando para iniciar el shell de Spark:

         .\bin\spark-shell --master yarn

    Cuando finalice la ejecución del comando, debe obtener un símbolo de sistema de Scala:

         scala>
4. En el símbolo de sistema de Scala, escriba la consulta de Spark que se muestra a continuación. Esta consulta cuenta la aparición de cada palabra en el archivo davinci.txt que está disponible en la ubicación /example/data/gutenberg/ en el almacenamiento de blobs de Azure asociado al clúster.

        val file = sc.textFile("/example/data/gutenberg/davinci.txt")
        val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
        counts.toArray().foreach(println)
5. La salida debe ser similar a la siguiente:

    ![Resultado de la ejecución del shell interactivo de Scala en un clúster de HDInsight](./media/hdinsight-hadoop-spark-install/hdi-scala-interactive.png)
6. Escriba: q para salir del símbolo del sistema de Scala.

        :q

### <a name="a-namesparksqlause-the-spark-shell-to-run-spark-sql-queries"></a><a name="sparksql"></a>Uso del shell de Spark para ejecutar consultas de Spark SQL
Spark SQL le permite usar Spark para ejecutar consultas relacionales expresadas en Lenguaje de consulta estructurado (SQL), HiveQL o Scala. En esta sección, veremos el uso de Spark para ejecutar una consulta de Hive en una tabla de Hive de ejemplo. La tabla de Hive usada en esta sección (llamada **hivesampletable**) está disponible de forma predeterminada cuando se crea un clúster.

> [!NOTE]
> El ejemplo siguiente se creó con **Spark 1.2.0**, que se instala si ejecuta la acción de script durante la creación del clúster de HDInsight 3.2.
>
>

1. En el portal de Azure, habilite el Escritorio remoto para el clúster que ha creado con Spark instalado y, a continuación, remoto en el clúster. Para obtener instrucciones, vea [Conexión a los clústeres de HDInsight con RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. En la sesión de RDP, desde el escritorio, abra la línea de comandos de Hadoop (desde un acceso directo del escritorio) y vaya a la ubicación donde está instalado Spark; por ejemplo, **C:\apps\dist\spark-1.2.0**.
3. Ejecute el siguiente comando para iniciar el shell de Spark:

         .\bin\spark-shell --master yarn

    Cuando finalice la ejecución del comando, debe obtener un símbolo de sistema de Scala:

         scala>
4. En el símbolo del sistema de Scala, establezca el contexto de Hive. Esto es necesario para trabajar con consultas de Hive mediante Spark.

        val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

    Tenga en cuenta que **sc** es contexto de Spark predeterminado que se establece cuando se inicia el shell de Spark.
5. Ejecute una consulta de Hive mediante el contexto de Hive e imprima la salida en la consola. La consulta recupera los datos en dispositivos de una marca y limita el número de registros recuperados a 20.

        hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)
6. Debe ver algo parecido a lo siguiente:

    ![Resultado de la ejecución de Spark SQL en un clúster de HDInsight](./media/hdinsight-hadoop-spark-install/hdi-spark-sql.png)
7. Escriba: q para salir del símbolo del sistema de Scala.

        :q

### <a name="a-namestandaloneause-a-standalone-scala-program"></a><a name="standalone"></a>Uso de un programa de Scala independiente
En esta sección se escribe una aplicación de Scala que cuenta el número de líneas que contienen las letras 'a' y 'b' en el archivo de datos de ejemplo (/example/data/gutenberg/davinci.txt) que está disponible, de manera predeterminada en los clústeres de HDInsight. Para escribir y usar un programa de Scala independiente con un clúster personalizado con la instalación de Spark, debe realizar los pasos siguientes:

* Escribir un programa de Scala
* Crear el programa de Scala para obtener el archivo .jar
* Ejecutar el trabajo en el clúster

#### <a name="write-a-scala-program"></a>Escribir un programa de Scala
En esta sección, escribe una aplicación de Scala que cuenta el número de líneas que contienen 'a' y 'b' en el archivo de datos de ejemplo.

1. Abra un editor de texto y pegue el código siguiente:

        /* SimpleApp.scala */
        import org.apache.spark.SparkContext
        import org.apache.spark.SparkContext._
        import org.apache.spark.SparkConf

        object SimpleApp {
          def main(args: Array[String]) {
            val logFile = "/example/data/gutenberg/davinci.txt"            //Location of the sample data file on Azure Blob storage
            val conf = new SparkConf().setAppName("SimpleApplication")
            val sc = new SparkContext(conf)
            val logData = sc.textFile(logFile, 2).cache()
            val numAs = logData.filter(line => line.contains("a")).count()
            val numBs = logData.filter(line => line.contains("b")).count()
            println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
          }
        }

1. Guarde el archivo con el nombre **SimpleApp.scala**.

#### <a name="build-the-scala-program"></a>Compilar el programa de Scala
En esta sección, use la <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">herramienta de compilación simple</a> (o sbt) para compilar el programa de Scala. Se quiere Java 1.6 o posterior para sbt; por tanto, asegúrese de que tenga la versión correcta de Java instalada antes de continuar con esta sección.

1. Instale sbt desde http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html.
2. Cree una carpeta llamada **SimpleScalaApp** y, dentro de ella, cree un archivo denominado **simple.sbt**. Se trata de un archivo de configuración que contiene información sobre la versión de Scala, dependencias de biblioteca, etc. Pegue lo siguiente en el archivo simple.sbt y guárdelo:

        name := "SimpleApp"

        version := "1.0"

        scalaVersion := "2.10.4"

        libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"

    > [!NOTE]
    > Asegúrese de que conserva las líneas vacías en el archivo.

1. En la carpeta **SimpleScalaApp**, cree una estructura de directorios **\src\main\scala** y pegue el programa de Scala (**SimpleApp.scala**) que creó antes en la carpeta \src\main\scala.
2. Abra un símbolo del sistema, navegue hasta el directorio SimpleScalaApp y escriba el siguiente comando:

        sbt package

    Una vez que se compile la aplicación, verá un archivo **simpleapp_2.10-1.0.jar** creado en el directorio **\target\scala-2.10** dentro de la carpeta raíz de SimpleScalaApp.

#### <a name="run-the-job-on-the-cluster"></a>Ejecutar el trabajo en el clúster
En esta sección, trabajará en remoto en el clúster que tiene Spark instalado y, a continuación, copiará la carpeta de destino del proyecto de SimpleScalaApp. A continuación, utilizará el comando **spark-submit** para enviar el trabajo del clúster.

1. Trabaje en remoto en el clúster que tiene Spark instalado. En el equipo donde escribió y creó el programa SimpleApp.scala, copie la carpeta **SimpleScalaApp\target** y péguela en una ubicación del clúster.
2. En la sesión RDP, en el escritorio, abra la línea de comandos de Hadoop y desplácese hasta la ubicación en la que ha pegado la carpeta de **destino** .
3. Escriba el siguiente comando para ejecutar el programa de SimpleApp.scala:

        C:\apps\dist\spark-1.2.0\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

1. Cuando el programa termine de ejecutarse, el resultado aparecerá en la consola.

        Lines with a: 21374, Lines with b: 11430

## <a name="install-spark-using-azure-powershell"></a>Instalación de Spark mediante Azure PowerShell
En esta sección, usamos el cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** para invocar scripts usando la acción de script para personalizar un clúster. Antes de continuar, asegúrese de que ha instalado y configurado Azure PowerShell. Para obtener información acerca de la configuración de una estación de trabajo para que ejecute cmdlets de Azure PowerShell para HDInsight, consulte [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs).

Lleve a cabo los siguiente pasos:

1. Abra una ventana de PowerShell de Azure y declare las siguientes variables:

    ```powershell
    # Provide values for these variables
    $subscriptionName = "<SubscriptionName>"        # Name of the Azure subscription
    $clusterName = "<HDInsightClusterName>"            # HDInsight cluster name
    $storageAccountName = "<StorageAccountName>"    # Azure Storage account that hosts the default container
    $storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
    $containerName = $clusterName
    $location = "<MicrosoftDataCenter>"                # Location of the HDInsight cluster. It must be in the same data center as the Storage account.
    $clusterNodes = <ClusterSizeInNumbers>            # Number of nodes in the HDInsight cluster
    $version = "<HDInsightClusterVersion>"          # For example, "3.2"
    ```

2. Especifique los valores de configuración como nodos del clúster y el almacenamiento predeterminado que se va a usar.

    ```powershell
    # Specify the configuration options
    Select-AzureSubscription $subscriptionName
    $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
    $config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
    $config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
    $config.DefaultStorageAccount.StorageContainerName=$containerName
    ```

3. Utilice el cmdlet **Add-AzureHDInsightScriptAction** para agregar una acción de script a la configuración de clúster. Más adelante, cuando se cree el clúster, se ejecutará la acción de script.

    ```powershell
    # Add a script action to the cluster configuration
    $config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1
    ```

    **Add-AzureHDInsightScriptAction** toma los siguientes parámetros:

    <table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
    <tr>
    <th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parámetro</th>
    <th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definición</th></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Configuración</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">El objeto de configuración al que se agrega la información de la acción del script</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre de la acción de script.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica los nodos en los que se ejecuta el script de personalización. Los valores válidos son HeadNode (para instalar en el nodo principal) o DataNode (para instalar en todos los nodos de datos). Puede usar uno de los valores o ambos.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Identificador URI</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Especifica el URI para el script que se ejecuta.</td></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parámetros</td>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parámetros requeridos por el script. El script de ejemplo que se usa en este tema no requiere ningún parámetro y, por tanto, no ve este parámetro en el fragmento de código anterior.
    </td></tr>
    </table>
4. Por último, comience a crear un clúster personalizado con Spark instalado.

    ```powershell
    # Start creating a cluster with Spark installed
    New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version
    ```

Cuando el sistema lo pida, escriba las credenciales para el clúster. El clúster puede tardar varios minutos en crearse.

## <a name="install-spark-using-powershell"></a>Instalación de Spark mediante PowerShell
Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).

## <a name="install-spark-using-net-sdk"></a>Instalación de Spark mediante .NET SDK
Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).

## <a name="see-also"></a>Otras referencias
* [Creación de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md): creación de clústeres de HDInsight.
* [Introducción a Apache Spark en HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): primeros pasos con Spark en HDInsight.
* [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-cluster-customize]: personalización de clústeres de HDInsight mediante la acción de script.
* [Desarrollo de scripts de acción de script para HDInsight](hdinsight-hadoop-script-actions.md): desarrollo de scripts de acción de script.
* [Instalación de R en clústeres de HDInsight][hdinsight-install-r] proporciona instrucciones sobre cómo usar la personalización del clúster para instalar y usar R en clústeres de Hadoop de HDInsight. R es un entorno y lenguaje de código abierto para computación estadística. Proporciona cientos de de funciones estadísticas integradas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientada a objetos. También proporciona amplias capacidades gráficas.
* [Instalación de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install.md). Use la personalización del clúster para instalar Giraph en clústeres de Hadoop para HDInsight. Giraph permite realizar un procesamiento gráfico mediante Hadoop, y se puede usar con HDInsight de Azure.
* [Instalación de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install.md). Use la personalización del clúster para instalar Solr en clústeres de Hadoop para HDInsight. Solr le permite realizar potentes operaciones de búsqueda en los datos almacenados.

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs



<!--HONumber=Jan17_HO3-->


