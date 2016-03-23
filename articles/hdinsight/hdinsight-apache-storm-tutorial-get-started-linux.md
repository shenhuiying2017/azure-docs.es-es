<properties
	pageTitle="Tutorial de Apache Storm: Introducción a Storm basado en Linux en HDInsight | Microsoft Azure"
	description="Introducción al análisis de macrodatos con Apache Storm y los ejemplos de storm-starter en HDInsight basado en Linux. Aprenda a usar Storm para procesar datos en tiempo real."
	keywords="Storm de Apache, tutorial de Storm de Apache, análisis de macrodatos, inicio de Storm"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/10/2016"
   ms.author="larryfr"/>


# Tutorial de Apache Storm: introducción a las muestras de Inicio de Storm para análisis de grandes cantidades de datos en HDInsight

Apache Storm es un sistema de cálculo distribuido, escalable, con tolerancia a errores y en tiempo real para el procesamiento de secuencias de datos. Con Storm en HDInsight de Azure, puede crear un clúster de Storm basado en la nube que realice análisis en tiempo real de grandes cantidades de datos en tiempo real.

> [AZURE.NOTE] En los pasos de este artículo se crea un clúster de HDInsight basado en Linux. A fin de conocer los pasos para crear un clúster de Storm en HDInsight basado en Windows, consulte [Tutorial de Apache Storm: Introducción a las muestras de inicio de Storm con análisis de datos en HDInsight](hdinsight-apache-storm-tutorial-get-started.md).

## Antes de empezar

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Debe cumplir los siguientes requisitos previos para poder completar correctamente este tutorial sobre Apache Storm.

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Familiaridad con SSH y SCP**. Para obtener más información sobre el uso de SSH y SCP con HDInsight, vea lo siguiente:

    - **Clientes Linux, Unix u OS X**: vea [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X (vista previa)](hdinsight-hadoop-linux-use-ssh-unix.md).

	- **Clientes Windows**: consulte [Uso de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## Creación de un clúster de Storm

En esta sección, se crea un clúster de HDInsight versión 3.2 (Storm versión 0.9.3) mediante una plantilla de ARM de Azure. Para obtener información acerca de las diferentes versiones de HDInsight y sus contratos de nivel de servicio, consulte la página [Control de versiones de componentes de HDInsight](hdinsight-component-versioning.md). Para conocer otros métodos de creación de clústeres, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Haga clic en la imagen siguiente para abrir una plantilla ARM en el Portal de Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-storm-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    La plantilla de ARM se encuentra en un contenedor de blobs público, *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-storm-cluster-in-hdinsight.json*.
   
2. En la hoja Parámetros, escriba lo siguiente:

    - **ClusterName**: escriba un nombre para el clúster de Hadoop que va a crear.
    - **Nombre de inicio de sesión y contraseña de clúster**: el nombre de inicio de sesión predeterminado es admin.
    - **Nombre de usuario y contraseña de SSH**.
    
    Escriba estos valores. Los necesitará más adelante en el tutorial.

    > [AZURE.NOTE] SSH sirve para tener acceso remoto a un clúster de HDInsight mediante una línea de comandos. El nombre de usuario y la contraseña que usa aquí se emplean al conectarse al clúster a través de SSH. Además, el nombre de usuario SSH tiene que ser único, ya que crea una cuenta de usuario en todos los nodos del clúster de HDInsight. A continuación se indican algunos de los nombres de cuenta reservados para su uso en los servicios que se ejecutan en el clúster y no pueden usarse como nombre de usuario SSH:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

	> Para obtener más información sobre el uso de SSH con HDInsight, vea uno de los siguientes artículos:

	> * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	> * [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3\. Haga clic en **Aceptar** para guardar los parámetros.

4\. En la hoja **Implementación personalizada**, haga clic en el cuadro desplegable **Grupo de recursos** y, después, haga clic en **Nuevo** para crear un grupo de recursos nuevo. El grupo de recursos es un contenedor que agrupa al clúster, a la cuenta de almacenamiento dependiente y a otros recursos vinculados.

5\. Haga clic en **Condiciones legales** y, luego, en **Crear**.

6\. Haga clic en **Crear**. Verá un icono nuevo llamado Envío de implementación para la implementación de plantilla. Tarda aproximadamente 20 minutos en crear un clúster y la base de datos SQL.


##Ejecución de una muestra de inicio de Storm en HDInsight

Los ejemplos de [storm-starter](https://github.com/apache/storm/tree/master/examples/storm-starter) se incluyen en el clúster de HDInsight. En los pasos siguientes, ejecutará el ejemplo de WordCount.

1. Conéctese al clúster de HDInsight con SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	Si usó una contraseña para proteger la cuenta de usuario SSH, se le pedirá la contraseña. Si usa una clave pública, tal vez tenga que usar el parámetro `-i` para especificar la ruta de acceso a la correspondiente clave privada. Por ejemplo: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
		
	Para obtener más información sobre el uso de SSH con HDInsight basado en Linux, vea los siguientes artículos:
	
	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows)

2. Use el comando siguiente para iniciar una topología de ejemplo:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology wordcount
		
	> [AZURE.NOTE] La parte `0.9.3.2.2.4.9-1` del nombre del archivo puede cambiar conforme HDinsight se actualiza con las versiones más recientes de Storm.

    Esto iniciará la topología de WordCount de ejemplo en el clúster, con un nombre descriptivo de "wordcount". Generará frases aleatoriamente y contará la aparición de cada palabra en las oraciones.

    > [AZURE.NOTE] Al enviar la topología al clúster, primero debe copiar el archivo jar que contiene el clúster antes de usar el comando `storm`. Esto se puede lograr mediante el comando `scp` desde el cliente donde se encuentra el archivo. Por ejemplo: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > El ejemplo de WordCount, y otros ejemplos de inicio de Storm, ya están incluidos en el clúster en `/usr/hdp/current/storm-client/contrib/storm-starter/`.

##Supervisión de la topología

La interfaz de usuario de Storm ofrece una interfaz web para trabajar con topologías en ejecución y se incluye en el clúster de HDInsight.

Siga estos pasos para supervisar la topología mediante la interfaz de usuario de Storm:

1. Abra en un explorador web https://CLUSTERNAME.azurehdinsight.net/stormui, donde __CLUSTERNAME__ es el nombre del clúster. Se abrirá la interfaz de usuario de Storm.

	> [AZURE.NOTE] Si se le pide que ofrezca un nombre de usuario y una contraseña, escriba el administrador de clústeres (admin) y la contraseña que usó al crear el clúster.

2. En el **resumen de la topología**, seleccione la entrada **wordcount** de la columna **Nombre**. Se mostrará más información sobre la topología.

	![Panel de Storm con la información de topología de WordCount de inicio de Starter.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)

	Esta página ofrece la siguiente información:

	* **Estadísticas de topología**: información básica sobre el rendimiento de la topología, organizada en ventanas de tiempo.

		> [AZURE.NOTE] Al seleccionar una ventana de tiempo específica, se cambia la ventana de tiempo de la información que aparece en otras secciones de la página.

	* **Spouts**: información básica sobre spouts, entre la que se incluye el último error que ha devuelto cada spout.

	* **Bolts**: información básica sobre bolts.

	* **Configuración de la topología**: información detallada sobre la configuración de la topología.

	Esta página también ofrece acciones que se pueden realizar en la topología:

	* **Activar**: reanuda el procesamiento de una topología desactivada.

	* **Desactivar**: pausa una topología en ejecución.

	* **Reequilibrar**: ajusta el paralelismo de la topología. Debe volver a equilibrar las topologías en ejecución después de haber cambiado el número de nodos del clúster. Esto permite que la topología ajuste el paralelismo para compensar el mayor o menor número de nodos del clúster. Para más información, consulte la entrada de blog [Understanding the parallelism of a Storm topology](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

	* **Eliminar**: finaliza una topología de Storm tras el tiempo de espera especificado.

3. En esta página, seleccione una entrada en la sección **Spouts** o **Bolts**. Se mostrará información sobre el componente seleccionado.

	![Panel de Storm con información acerca de los componentes seleccionados.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)

	En esta página se muestra la siguiente información:

	* **Estadísticas de spouts y bolts**: información básica sobre el rendimiento de los componentes, organizada en ventanas de tiempo.

		> [AZURE.NOTE] Al seleccionar una ventana de tiempo específica, se cambia la ventana de tiempo de la información que aparece en otras secciones de la página.

	* **Estadísticas de entrada** (solo bolt): información sobre los componentes que generan los datos que consume el bolt.

	* **Estadísticas de salida**: información sobre los datos que emite este bolt.

	* **Ejecutores**: información sobre las instancias de este componente.

	* **Errores**: errores que ha generado este componente.

4. Al visualizar los detalles de un spout o bolt, seleccione una entrada de la columna **Puerto** en la sección **Ejecutores** para ver los detalles de una instancia específica del componente.

		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

	A partir de estos datos, puede ver que la palabra **seven** se ha repetido 1493957 veces. Ese es el número de veces que se ha encontrado desde que se inició esta topología.

##Detención de la topología

Vuelva a la página **Resumen de la topología** de la topología de recuento de palabras y, después, seleccione el botón **Eliminar** de la sección **Acciones de topología**. Cuando se le solicite, escriba 10 como los segundos de espera antes de detener la topología. Tras el período de tiempo de espera, ya no aparecerá la topología cuando visite la sección **IU de Storm** del panel.

##Eliminación del clúster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##Resumen

En este tutorial de Apache Storm, usó el inicio de Storm para aprender a crear un clúster de Storm en HDInsight y usar el panel de Storm para implementar, supervisar y administrar topologías de Storm.

##<a id="next"></a>Pasos siguientes

* El documento siguiente contiene una lista de otros ejemplos que se pueden usar con Storm en HDInsight:

	* [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

<!----HONumber=AcomDC_0316_2016-->