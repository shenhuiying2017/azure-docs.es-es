---
title: "Kit de herramientas de Azure para IntelliJ: depuración de aplicaciones de forma remota en Spark de HDInsight | Microsoft Docs"
description: "Aprenda a usar las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones que se ejecutan en clústeres de HDInsight Spark mediante VPN."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 55fb454f-c7dc-46de-a978-e242e9a94f4c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 7450938198c6bf5e8e1522d08df00f677961713e
ms.contentlocale: es-es
ms.lasthandoff: 06/23/2017


---
# <a name="use-azure-toolkit-for-intellij-to-debug-applications-remotely-on-hdinsight-spark-through-vpn"></a>Uso del kit de herramientas de Azure para IntelliJ para depurar aplicaciones de forma remota en HDInsight Spark mediante VPN
En este artículo se proporcionan instrucciones paso a paso para usar las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ para enviar un trabajo de Spark en un clúster de HDInsight Spark y depurarlo de forma remota desde el equipo de escritorio. Para ello, debe realizar los siguientes pasos de alto nivel:

1. Creación de una red virtual de Azure de sitio a sitio o de punto a sitio. Para los pasos descritos en este documento, se da por supuesto que usa una red de sitio a sitio.
2. Creación de un clúster de Spark en HDInsight de Azure que forma parte de la red virtual de Azure de sitio a sitio.
3. Comprobación de la conectividad entre el nodo principal del clúster y el escritorio.
4. Creación de una aplicación Scala en IntelliJ IDEA y configuración para la depuración remota.
5. Ejecución y depuración de la aplicación.

## <a name="prerequisites"></a>Requisitos previos
* Una suscripción de Azure. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).
* Kit de desarrollo de Oracle Java. Puede instalarlo desde [aquí](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IntelliJ IDEA. En este artículo se usa la versión 2017.1. Puede instalarlo desde [aquí](https://www.jetbrains.com/idea/download/).
* Herramientas de HDInsight del kit de herramientas de Azure para IntelliJ Las herramientas de HDInsight para IntelliJ están disponibles como parte del kit de herramientas de Azure para IntelliJ. Para obtener instrucciones sobre cómo instalar el kit de herramientas de Azure, consulte [Instalación del kit de herramientas de Azure para IntelliJ](../azure-toolkit-for-intellij-installation.md).
* Inicie sesión en la suscripción de Azure desde IntelliJ IDEA. Siga las instrucciones que se describen [aquí](hdinsight-apache-spark-intellij-tool-plugin.md).
* Mientras se ejecuta la aplicación Scala Spark para la depuración remota en un equipo Windows, puede obtener una excepción, como se explica en [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) , que se produce por la ausencia del archivo WinUtils.exe en Windows. Para solucionar este error, debe [descargar el archivo ejecutable desde aquí](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) y guardarlo en una ubicación como **C:\WinUtils\bin**. Después, debe agregar una variable de entorno **HADOOP_HOME** y establecer el valor de la variable en **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Paso 1: Creación de una red virtual de Azure
Siga las instrucciones de los vínculos a continuación para crear una red virtual de Azure y comprobar la conectividad entre ella y el escritorio.

* [Creación de una red virtual con una conexión VPN de sitio a sitio mediante el Portal de Azure y Azure Resource Manager](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Creación de una red virtual con una conexión VPN de sitio a sitio mediante Azure Resource Manager y PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Configuración de una conexión punto a sitio a una red virtual mediante PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Paso 2: Creación de un clúster de Spark en HDInsight
También debe crear un clúster de Apache Spark en HDInsight de Azure que forme parte de la red virtual de Azure que ha creado. Use la información disponible en [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Como parte de la configuración opcional, seleccione la red virtual de Azure que creó en el paso anterior.

## <a name="step-3-verify-the-connectivity-between-the-cluster-headnode-and-your-desktop"></a>Paso 3: Comprobación de la conectividad entre el nodo principal del clúster y el escritorio
1. Obtenga la dirección IP del nodo principal. Abra la IU de Ambari para el clúster. En la hoja del clúster, haga clic en **Panel de inicio**.

    ![Buscar dirección IP del nodo principal](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/launch-ambari-ui.png)
2. En la IU de Ambari, en la esquina superior derecha, haga clic en **Hosts**.

    ![Buscar dirección IP del nodo principal](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/ambari-hosts.png)
3. Debería ver una lista de nodos principales, nodos de trabajo y nodos de Zookeeper. Los nodos principales tienen el prefijo **hn***. Haga clic en el primer nodo principal.

    ![Buscar dirección IP del nodo principal](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/cluster-headnodes.png)
4. En la parte inferior de la página que se abre, en el cuadro **Summary** (Resumen), copie la dirección IP del nodo principal y el nombre de host.

    ![Buscar dirección IP del nodo principal](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/headnode-ip-address.png)
5. Incluya la dirección IP y el nombre de host del nodo principal en el archivo de **hosts** en el equipo desde el que desee ejecutar y depurar de forma remota los trabajos de Spark. Esto le permitirá comunicarse con el nodo principal mediante la dirección IP, así como el nombre de host.

   1. Abra el Bloc de notas con permisos elevados. En el menú Archivo, haga clic en **Abrir** y vaya a la ubicación del archivo de hosts. En un equipo Windows, es `C:\Windows\System32\Drivers\etc\hosts`.
   2. Agregue lo siguiente al archivo de **hosts** .

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
6. En el equipo conectado a la red virtual de Azure que el clúster de HDInsight usa, compruebe que pueda hacer ping a ambos nodos principales mediante la dirección IP, así como el nombre de host.
7. Conéctese mediante SSH con el nodo principal del clúster según las instrucciones en [Conexión a un clúster de HDInsight basado en Linux](hdinsight-hadoop-linux-use-ssh-unix.md). En el nodo principal del clúster, haga ping a la dirección IP del equipo de escritorio. Debe probar la conectividad a ambas direcciones IP asignadas al equipo, una para la conexión de red y otro para la red virtual de Azure a la que está conectado el equipo.
8. Repita los pasos también para el otro nodo principal.

## <a name="step-4-create-a-spark-scala-application-using-the-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Paso 4: Creación de una aplicación Scala Spark mediante las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ y configuración para la depuración remota
1. Inicie IntelliJ IDEA y cree un nuevo proyecto. En el cuadro de diálogo del nuevo proyecto, realice las siguientes selecciones y después haga clic en **Next**(Siguiente).

    ![Crear aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/create-hdi-scala-app.png)

   * En el panel izquierdo, seleccione **HDInsight**.
   * En el panel derecho, seleccione **Spark on HDInsight (Scala)**(Spark en HDInsight [Scala]).
   * Haga clic en **Siguiente**.
2. En la ventana siguiente, proporcione los detalles de proyecto siguientes y, a continuación, haga clic en **Finish** (Finalizar).  
   - Proporcione un nombre de proyecto y la ubicación del proyecto.
   - En **Project SDK** (SDK de proyecto), use Java 1.8 para el clúster Spark 2.x o Java 1.7 para el clúster Spark 1.x.
   - En **Spark Version** (Versión de Spark), el Asistente para crear un proyecto Scala integra la versión correcta del SDK de Spark y el IDE de Scala. Si la versión del clúster de Spark es inferior a la 2.0, elija Spark 1.x. En caso contrario, debe seleccionar spark2.x. Este ejemplo utiliza Spark2.0.2 (Scala 2.11.8).
       ![Creación de una aplicación Spark en Scala](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/hdi-scala-project-details.png)
  
3. El proyecto Spark creará automáticamente un artefacto. Para ver el artefacto, siga estos pasos.

   1. En el menú **File** (Archivo), haga clic en **Project Structure** (Estructura del proyecto).
   2. En el cuadro de diálogo **Project Structure** (Estructura del proyecto), haga clic en **Artifacts** (Artefactos) para ver el artefacto predeterminado que se crea.
   ![Crear un JAR](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/default-artifact.png)

      También puede crear su propio artefacto haciendo clic en el icono **+** (resaltado en la imagen anterior).

4. Agregue bibliotecas al proyecto. Para agregar una biblioteca, haga clic con el botón derecho en el nombre del proyecto en el árbol y después haga clic en **Open Module Settings**(Abrir configuración de módulo). En el cuadro de diálogo **Project Structure** (Estructura del proyecto) del panel izquierdo, haga clic en **Libraries** (Bibliotecas), en el símbolo (+) y en **From Maven** (Desde Maven).

    ![Agregar biblioteca](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/add-library.png)

    En el cuadro de diálogo **Download Library from Maven Repository** (Descargar biblioteca desde repositorio de Maven), busque y agregue las siguientes bibliotecas.

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
5. Copie `yarn-site.xml` y `core-site.xml` del nodo principal del clúster y agréguelos al proyecto. Use los comandos siguientes para copiar los archivos. Puede usar [Cygwin](https://cygwin.com/install.html) para ejecutar los siguientes comandos `scp` y copiar los archivos desde los nodos principales del clúster.

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    Como ya se han agregado la dirección IP y los nombres de host del nodo principal del clúster al archivo de hosts del escritorio, se pueden usar los comandos **scp** de la siguiente manera.

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    Para agregar estos archivos a su proyecto, cópielos en la carpeta **/src** del árbol; por ejemplo, `<your project directory>\src`.
6. Actualice `core-site.xml` con los siguientes cambios.

   1. `core-site.xml` incluye la clave cifrada para la cuenta de almacenamiento asociada con el clúster. En el archivo `core-site.xml` que agregó al proyecto, reemplace la clave cifrada por la clave de almacenamiento real asociada a la cuenta de almacenamiento predeterminada. Consulte [Administración de la cuenta de almacenamiento](../storage/storage-create-storage-account.md#manage-your-storage-account).

           <property>
                 <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
                 <value>access-key-associated-with-the-account</value>
           </property>
   2. Quite las siguientes entradas del archivo `core-site.xml`.

           <property>
                 <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
                 <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
           </property>

           <property>
                 <name>fs.azure.shellkeyprovider.script</name>
                 <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
           </property>

           <property>
                 <name>net.topology.script.file.name</name>
                 <value>/etc/hadoop/conf/topology_script.py</value>
           </property>
   3. Guarde el archivo .
7. Agregue la clase Main para la aplicación. En **Project Explorer** (Explorador de proyectos), haga clic con el botón derecho en **src**, elija **New** (Nueva) y haga clic en **Scala class** (Clase de Scala).

    ![Agregar código fuente](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/hdi-spark-scala-code.png)
8. En el cuadro de diálogo **Create New Scala Class** (Crear nueva clase de Scala), proporcione un nombre, seleccione **Object** (Objeto) en **Kind** (Variante) y haga clic en **OK** (Aceptar).

    ![Agregar código fuente](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/hdi-spark-scala-code-object.png)
9. En el archivo `MyClusterAppMain.scala` , pegue el código siguiente. Con este código se crea el contexto de Spark y se inicia un método `executeJob` desde el objeto `SparkSample`.

        import org.apache.spark.{SparkConf, SparkContext}

        object SparkSampleMain {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkSample")
                                      .set("spark.hadoop.validateOutputSpecs", "false")
            val sc = new SparkContext(conf)

            SparkSample.executeJob(sc,
                                   "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                                   "wasbs:///HVACOut")
          }
        }

10. Repita los pasos 8 y 9 anteriores para agregar un nuevo objeto de Scala llamado `SparkSample`. Agregue el siguiente código a esta clase. Este código lee los datos de HVAC.csv (disponible en todos los clústeres de Spark de HDInsight), recupera las filas que solo tienen un dígito en la séptima columna del CSV y escribe el resultado en **/HVACOut** bajo el contenedor de almacenamiento predeterminado para el clúster.

        import org.apache.spark.SparkContext

        object SparkSample {
         def executeJob (sc: SparkContext, input: String, output: String): Unit = {
           val rdd = sc.textFile(input)

           //find the rows which have only one digit in the 7th column in the CSV
           val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

           val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
           println(s)

           rdd1.saveAsTextFile(output)
           //rdd1.collect().foreach(println)
         }
        }
11. Repita los pasos 8 y 9 anteriores para agregar una nueva clase llamada `RemoteClusterDebugging`. Esta clase implementa el marco de pruebas de Spark que se usa para depurar aplicaciones. Agregue el siguiente código a la clase `RemoteClusterDebugging` .

        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasbs:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\WORK\IntelliJApps\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasbs:///HVACOut")
         }
        }

     Hay un par de cosas importantes que se deben tener en cuenta aquí:

   * Para `.set("spark.yarn.jar", "wasbs:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, asegúrese de que el archivo JAR del ensamblado Spark está disponible en el almacenamiento de clúster de la ruta especificada.
   * Para `setJars`, especifique la ubicación donde se creará el archivo JAR de artefacto. Normalmente es `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.
12. En la clase `RemoteClusterDebugging`, haga clic con el botón derecho en la palabra clave `test` y seleccione **Create RemoteClusterDebugging Configuration** (Crear configuración de RemoteClusterDebugging).

    ![Crear configuración remota](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/create-remote-config.png)

13. En el cuadro de diálogo, especifique un nombre para la configuración y seleccione **Test name** (Nombre de prueba) para **Test kind** (Tipo de prueba). Deje los demás valores predeterminados y haga clic en **Apply** (Aplicar) y en **OK** (Aceptar).

    ![Crear configuración remota](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/provide-config-value.png)
14. Ahora se debería ver un menú desplegable de configuración **Remote Run** (Ejecución remota) en la barra de menús.

    ![Crear configuración remota](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Paso 5: Ejecución de la aplicación en modo de depuración
1. En el proyecto de IntelliJ IDEA, abra `SparkSample.scala` y cree un punto de interrupción junto a "val rdd1". En el menú emergente para crear un punto de interrupción, seleccione **line in function executeJob**(línea en función executeJob).

    ![Agregar un punto de interrupción](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/create-breakpoint.png)
2. Haga clic en el botón **Debug Run** (Depurar ejecución) situado junto al menú desplegable de configuración **Remote Run** (Ejecución remota) para comenzar a ejecutar la aplicación.

    ![Ejecutar el programa en modo de depuración](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/debug-run-mode.png)
3. Cuando la ejecución del programa alcance el punto de interrupción, debería ver una pestaña **Debugger** (Depurador) en el panel inferior.

    ![Ejecutar el programa en modo de depuración](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/debug-add-watch.png)
4. Haga clic en el icono (**+**) para agregar una inspección como se muestra en la siguiente imagen.

    ![Ejecutar el programa en modo de depuración](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/debug-add-watch-variable.png)

    Aquí, como la aplicación se interrumpió antes de que se creara la variable `rdd1`, con esta inspección se ve cuáles son las cinco primeras filas de la variable `rdd`. Presione **ENTRAR**.

    ![Ejecutar el programa en modo de depuración](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/debug-add-watch-variable-value.png)

    Lo que se ve en la imagen anterior es que, en tiempo de ejecución, se pueden consultar terabytes de datos y depurar la ejecución de la aplicación. Por ejemplo, en el resultado que se muestra en la imagen anterior, se ve que la primera fila es un encabezado. Según esto, puede modificar el código de la aplicación para omitir la fila de encabezado si es necesario.
5. Ahora puede hacer clic en el icono **Resume Program** (Reanudar programa) para continuar con la ejecución de la aplicación.

    ![Ejecutar el programa en modo de depuración](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/debug-continue-run.png)
6. Si la aplicación se completa correctamente, debería ver resultados parecidos a los siguientes.

    ![Ejecutar el programa en modo de depuración](./media/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely-through-vpn/debug-complete.png)

## <a name="seealso"></a>Otras referencias
* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Uso de las herramientas de HDInsight del kit de herramientas de Azure para IntelliJ con el fin de crear y enviar aplicaciones Spark en Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Uso del kit de herramientas de Azure para IntelliJ para depurar de forma remota aplicaciones Spark mediante SSH](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Uso de las herramientas de HDInsight del kit de herramientas de Azure para Eclipse con el fin de crear aplicaciones Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)

