<properties title="Getting started using Storm with Hadoop in HDInsight" pageTitle="Introducción a Apache Storm con Microsoft Azure HDInsight (Hadoop)" description="Aprenda a usar Apache Storm para procesar datos en tiempo real con HDInsight (Hadoop)" metaKeywords="Azure hdinsight storm, Azure hdinsight realtime, azure hadoop storm, azure hadoop realtime, azure hadoop real-time, azure hdinsight real-time" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="larryfr" />

#Introducción al uso de Storm con HDInsight (Hadoop)

Apache Storm es un sistema de cálculo distribuido, escalable, con tolerancia a errores y en tiempo real para el procesamiento de secuencias de datos. Con Azure HDInsight, puede crear un clúster de Hadoop basado en la nube que realice análisis en tiempo real de los datos con Storm.

##En este tutorial, aprenderá a:

* [Aprovisionar un clúster de Storm en HDInsight](#provision)

* [Conectarse al clúster](#connect)

* [Ejecutar una topología de Storm](#run)

* [Supervisar una topología de Storm](#monitor)

* [Detener una topología de Storm](#stop)

* [Pasos siguientes](#next)

##Antes de empezar

Debe cumplir los siguientes requisitos previos antes de realizar este tutorial.

* Una suscripción de Azure

* Windows Azure PowerShell

* Si no está familiarizado con Apache Storm, primero debe leer el artículo [Introducción a Storm de HHDInsight](/en-us/documentation/articles/hdinsight-storm-overview) .

##<a id="provision"></a>Aprovisionamiento de un clúster de Storm en el portal de Azure

[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

1. Inicie sesión en el [Portal de administración de Azure][azureportal]

2. Haga clic en **HDInsight** a la izquierda y luego en **+NUEVO** en la esquina inferior izquierda de la página.

3. Haga clic en el icono de HDInsight en la segunda columna y después seleccione **Personalizado**.

4. En la página **Detalles del clúster**, escriba el nombre del nuevo clúster y seleccione **Storm** como **Tipo de clúster**. Seleccione la flecha para continuar.

	![cluster details](./media/hdinsight-storm-getting-started/wizard1.png)

5. Escriba el número de **Nodos de datos** que desea usar en este clúster y la **Región/Red virtual** en la que se creará este clúster. Seleccione la flecha para continuar.

	> [WACOM.NOTE] Para reducir el coste del clúster usado en este artículo, reduzca el **Tamaño del clúster** a 1, y elimine el clúster cuando haya terminado de usarlo.

	![data nodes and region](./media/hdinsight-storm-getting-started/wizard2.png)

6. Escriba el **Nombre de usuario** y la **Contraseña** de administrador y luego seleccione la flecha para continuar.

	![account and password](./media/hdinsight-storm-getting-started/wizard3.png)

4. En **Cuenta de almacenamiento**, seleccione **Crear nuevo almacenamiento** o seleccione una cuenta de almacenamiento existente. Seleccione o escriba el **Nombre de cuenta** y el **Contenedor predeterminado** que quiere usar. Haga clic en el icono de verificación en la parte inferior izquierda para crear el clúster de Storm.

	![storage account](./media/hdinsight-storm-getting-started/wizard4.png)

##Uso de Storm en HDInsight

En la versión anterior de Storm en HDInsight, debe conectarse al clúster mediante Escritorio remoto para trabajar con Storm. Use los siguientes pasos para conectarse al clúster de HDInsight y usar el comando Storm.

###<a id="connect"></a>Conexión al clúster

1. Habilite la conectividad de Escritorio remoto al clúster de HDInsight mediante el [Portal de administración de Azure][management]. En el portal, seleccione el clúster de HDInsight y luego seleccione __Habilitar de forma remota__ en la parte inferior de la página __Configuración__.

    ![enable remote](./media/hdinsight-storm-getting-started/enableremotedesktop.png)

    Cuando se le pida, escriba un nombre de usuario y una contraseña para usar en las sesiones remotas. También debe seleccionar una fecha de caducidad para el acceso remoto.

	![remote desktop config](./media/hdinsight-storm-getting-started/configremotedesktop.png)

2. Una vez habilitado el acceso remoto, seleccione __Conectar__ para comenzar la conexión. Esta acción descargará un archivo __.rdp__, que se puede usar para iniciar una sesión de Escritorio remoto.

    ![connect](./media/hdinsight-storm-getting-started/connect.png)

	> [WACOM.NOTE] Puede que mientras se conecta a la máquina remota reciba varias solicitudes de confirmación de que confía en los certificados.

3. Después de conectar, use el icono de __línea de comandos de Hadoop__ del escritorio para abrir la línea de comandos de Hadoop.

	![hadoop cli](./media/hdinsight-storm-getting-started/hadoopcommandline.png)

3. Desde la línea de comandos de Hadoop, use el siguiente comando para cambiar los directorios por el directorio que contiene el comando Storm.

	cd %storm_home%\bin

4. Para ver una lista de comandos disponibles, escriba `storm` sin ningún parámetro.

El clúster de HDInsight incluye varios ejemplos de topologías de Storm. La muestra **WordcountTopology** se usa en los siguientes pasos. Para obtener más información sobre los ejemplos proporcionados con Storm en HDInsight, consulte [Pasos siguientes](#next).

###<a id="run"></a>Ejecución de una topología de Storm

Para ejecutar **WordCountTopology**, use los siguientes comandos.

	storm jar ..\contrib\storm-starter\storm-starter-<version>-jar-with-dependencies.jar storm.starter.WordCountTopology wordcount

Con esto se indica a Storm que queremos ejecutar la topología **wordcount** desde la clase **storm.starter.WordCountTopology** contenida en el archivo **storm-starter-&lt;version>-jar-with-dependencies.jar**. Este archivo está ubicado en la carpeta contrib bajo el directorio %storm_home%, junto con los demás ejemplos de Storm.

> [WACOM.NOTE] La versión del archivo JAR que contiene los ejemplos puede cambiar periódicamente. Especifique la versión del archivo que se proporciona con su clúster al ejecutar este comando.

Observe que no se devuelve ningún resultado al insertar el comando. **Una topología de Storm, una vez que se inicia, se ejecuta hasta que la detiene.** La topología WordCountTopology generará instrucciones aleatorias y mantendrá un recuento del número de veces que encuentra cada palabra hasta que la detenga.

###<a id="monitor"></a>Supervisión del estado de una topología de Storm

La muestra WordCountTopology no escribe resultados en un directorio, pero podemos usar las páginas web de UI de Storm para ver el estado de la topología, así como el resultado.

1. Mediante Escritorio remoto, conéctese a su clúster de HDInsight.

2. Abra el vínculo de la **UI de Storm** desde el escritorio. Esta acción mostrará la página web de la UI de Storm.  En el **resumen de la topología**, seleccione la entrada **wordcount**.

	![storm ui](./media/hdinsight-storm-getting-started/stormui.png)

	Esta acción mostrará las estadísticas de la topología, que incluyen los componentes que la constituyen **spouts** y **bolts**.

5. Seleccione el vínculo **spout** de la página y luego seleccione el número de **Puerto** de una de las entradas en la lista **Executors (All time)** (Ejecutores) (Todo el tiempo) que tenga un número mayor que 0 en las columnas **Emitted** (Emitidos) y **Transferred** (Transferidos).

	![spouts and bolts](./media/hdinsight-storm-getting-started/stormuiboltsnspouts.png)

	![selecting executors](./media/hdinsight-storm-getting-started/executors.png)

	> [WACOM.NOTE] La topología puede tardar varios minutos en procesarse, según el número de nodos del clúster y la topología que se esté usando. Actualice la página regularmente hasta que los valores de **emitido** y **transferido** comiencen a aumentar.

6. Debe ver una página que contiene información similar a la siguiente.

		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [snow white and the seven dwarfs]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16774]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16775]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8359]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8360]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [i am at two with nature]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:23, stream: default, id: {}, [two]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [two, 8236]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:22, stream: default, id: {}, [a]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [a, 8280]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:19, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16776]

	En este fragmento de código, puede ver que el spout emitió 'snow white and the seven dwarfs', que se dividió en palabras individuales. Además, se está manteniendo un recuento de cuántas veces ha procesado la topología cada palabra desde que se iniciara. Por ejemplo, el spout había emitido la palabra 'dwarfs' 8360 cuando se vio el resultado anterior.

###<a id="stop"></a>Detención de una topología de Storm

La muestra **WordCountTopology** se seguirá ejecutando hasta que la detenga. Para detenerla, use el siguiente comando.

	storm kill wordcount

Si ve la página web de la UI de Storm inmediatamente después de este comando, observará que el estado de **wordcount** en el **resumen de la topología** ha cambiado a KILLED. Al cabo de unos segundos, la topología ya no se mostrará en la sección de **resumen de la topología**.

##<a id="next"></a>Pasos siguientes

* **Archivos de muestra**: el clúster de Storm en HDInsight proporciona varios ejemplos en el directorio **%storm_home%\contrib**. Cada ejemplo debe contener lo siguiente:

	* El código fuente, por ejemplo, storm-starter-0.9.1.2.1.5.0-2057-sources.jar

	* Los documentos de Java, por ejemplo, storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar

	* El ejemplo, por ejemplo, storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar

	Use el comando 'jar' para extraer el código fuente o los documentos de Java. Por ejemplo, 'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'.

	> [WACOM.NOTE] Los documentos de Java constan de páginas web. Una vez extraídos, use un explorador para ver el archivo **index.html**.

* [Análisis de los datos de sensor con Storm y HDInsight](/en-us/documentation/articles/hdinsight-storm-sensor-data-analysis)

* [Desarrollo de aplicaciones de procesamiento de datos de streaming con SCP.NET y C# en Storm de HDInsight](/en-us/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/

<!--HONumber=35_1-->
