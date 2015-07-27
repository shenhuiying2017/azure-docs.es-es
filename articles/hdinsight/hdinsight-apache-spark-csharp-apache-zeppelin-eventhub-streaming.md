<properties 
	pageTitle="Use los centros de eventos de Azure con Apache Spark en HDInsight para procesar los datos de transmisión por secuencias | Azure" 
	description="Instrucciones detalladas sobre cómo enviar un flujo de datos al centro de eventos de Azure y, a continuación, recibir estos eventos en Spark usando un notebook Zeppelin" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="nitinme"/>


# Transmisión Spark: procese eventos de Centros de eventos de Azure con Spark de Apache en HDInsight

La transmisión de Spark amplía la API de Spark de núcleo para crear aplicaciones escalables de procesamiento de transmisión de alto rendimiento y tolerantes a errores. Los datos pueden ser introducidos desde varios orígenes. En este artículo usamos los Centros de eventos para introducir datos. Los Centros de eventos son un sistema de introducción altamente escalable que introduce millones de eventos por segundo.

En este tutorial, obtendrá información sobre cómo crear un Centro de eventos de Azure, cómo introducir mensajes en un Centro de eventos mediante una aplicación de consola en C# y cómo recuperarlos en paralelo mediante un notebook Zeppelin configurado para Apache Spark en HDInsight.

**Requisitos previos:**

Debe tener lo siguiente:

- Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster Apache Spark. Para obtener instrucciones, consulte [Aprovisionamiento de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-provision-clusters.md).
- Un [Centro de eventos de Azure](service-bus-event-hubs-csharp-ephcs-getstarted.md).
- Una estación de trabajo con Microsoft Visual Studio 2013. Para obtener instrucciones, consulte [Instalar Visual Studio](https://msdn.microsoft.com/library/e2h7fzkw.aspx).

##<a name="createeventhub"></a>Crear un Centro de eventos de Azure

1. En el [Portal de Azure](https://manage.windowsazure.com), seleccione **NUEVO** > **Bus de servicio** > **Centro de eventos** > **Creación personalizada**.

2. En la pantalla **Agregar un nuevo centro de eventos**, escriba un **Nombre del centro de eventos**, seleccione la **Región** donde se va a crear el centro y cree un nuevo espacio de nombres o seleccione uno existente. Haga clic en la **Flecha** para continuar.

	![página 1 del asistente](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Create.Event.Hub.png "Crear un centro de eventos de Azure")

	> [AZURE.NOTE]Debe seleccionar la misma **ubicación** que la del clúster Apache Spark en HDInsight para reducir la latencia y los costes.

3. En la pantalla **Configuración del centro de eventos**, escriba los valores **Recuento de particiones** y **Retención de mensajes** y, a continuación, haga clic en la marca de verificación. En este ejemplo, utilice un recuento de particiones de 10 y una retención de mensajes de 1. Anote el recuento de particiones, porque necesitará más adelante este valor.

	![página 2 del asistente](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Create.Event.Hub2.png "Especificar los días de retención y el tamaño de la partición para el centro de eventos")

4. Haga clic en el centro de eventos que ha creado, en **Configurar** y, a continuación, cree dos directivas de acceso para el centro de eventos.

	<table>
<tr><th>Nombre</th><th>Permisos</th></tr>
<tr><td>mysendpolicy</td><td>Los métodos Send</td></tr>
<tr><td>myreceivepolicy</td><td>Escuchar</td></tr>
</table>Después de crear los permisos, seleccione el icono **Guardar** en la parte inferior de la página. Con esto se crean las directivas de acceso compartido que se usarán para enviar (**mysendpolicy**) y escuchar (**myreceivepolicy**) a este centro de eventos.

	![directivas](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policies.png "Crear directivas de centro de eventos")

	
5. En la misma página, tome nota de las claves de directiva que se generan para las dos directivas. Guarde estas claves, ya que se usarán después.

	![claves de directiva](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policy.Keys.png "Guardar claves de directiva")

6. En la página **Panel**, haga clic en **Información de conexión** desde la parte inferior para recuperar y guardar las cadenas de conexión para el centro de eventos mediante las dos directivas.

	![claves de directiva](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policy.Connection.Strings.png "Guardar cadenas de conexión de directivas")

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

##<a name="receivezeppelin"></a>Recibir mensajes de Spark en HDInsight mediante Zeppelin

En esta sección, deberá crear un notebook [Zeppelin](https://zeppelin.incubator.apache.org) para recibir mensajes desde el centro de eventos en el clúster Spark en HDInsight.

1. Inicie el notebook Zeppelin. Seleccione el clúster Spark en el portal de Azure y de la barra de tareas de portal en la parte inferior, haga clic en **Notebook Zeppelin**. Cuando se le solicite, escriba las credenciales de administrador para el clúster Spark. Siga las instrucciones de la página que se abre para iniciar el notebook.

2. Cree un nuevo notebook. En el panel de encabezado, haga clic en **Notebook** y, en la lista desplegable, haga clic en **Crear una nueva nota**.

	![Crear un nuevo notebook Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.CreateNewNote.png "Crear un nuevo notebook Zeppelin")

	En la misma página, en el encabezado **Notebook**, debería ver un nuevo notebook con un nombre que empiece por **Nota XXXXXXXXX**. Haga clic en el nuevo notebook.

3. En la página web del nuevo notebook, haga clic en el encabezado y cambie el nombre del notebook si quiere. Presione ENTRAR para guardar el cambio de nombre. Además, asegúrese de que el encabezado del notebook muestra un estado **Conectado** en la esquina superior derecha.

	![Estado del notebook Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.NewNote.Connected.png "Estado del notebook Zeppelin")

4. En el párrafo vacío que se crea de forma predeterminada en el nuevo notebook, pegue el siguiente fragmento de código y reemplace los marcadores de posición para usar la configuración del centro de eventos. En este fragmento de código, recibe la secuencia del centro de eventos y registra la secuencia en una tabla temporal, denominada **mytemptable**. En la siguiente sección, se iniciará la aplicación remitente. A continuación, puede leer los datos directamente de la tabla.

		import org.apache.spark.streaming.{Seconds, StreamingContext}
		import org.apache.spark.streaming.eventhubs.EventHubsUtils
		import sqlContext.implicits._
		
		val ehParams = Map[String, String](
		  "eventhubs.policyname" -> "<name of policy with listen permissions>",
		  "eventhubs.policykey" -> "<key of policy with listen permissions>",
		  "eventhubs.namespace" -> "<service bus namespace>",
		  "eventhubs.name" -> "<event hub in the service bus namespace>",
		  "eventhubs.partition.count" -> "1",
		  "eventhubs.consumergroup" -> "$default",
		  "eventhubs.checkpoint.dir" -> "/<check point directory in your storage account>",
		  "eventhubs.checkpoint.interval" -> "10"
		)
		
		val ssc =  new StreamingContext(sc, Seconds(10))
		val stream = EventHubsUtils.createUnionStream(ssc, ehParams)
		
		case class Message(msg: String)
		stream.map(msg=>Message(new String(msg))).foreachRDD(rdd=>rdd.toDF().registerTempTable("mytemptable"))

		stream.print
		ssc.start


##<a name="runapps"></a>Ejecución de las aplicaciones

1. En el notebook Zeppelin, ejecute el párrafo con el fragmento de código. Presione **MAYÚS + INTRO** o el botón **Reproducir** que se encuentra en la esquina superior derecha.

	El estado en la esquina derecha del párrafo debería avanzar de READY (Listo), PENDING (Pendiente) o RUNNING (En ejecución) a FINISHED (Finalizado). El resultado aparecerá en la parte inferior del mismo párrafo. La captura de pantalla es similar a la siguiente:

	![Salida del fragmento de código](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Zeppelin.Code.Output.png "Salida del fragmento de código")

2. Ejecute el proyecto de **remitente** y presione **Intro** en la ventana de la consola para iniciar el envío de mensajes al Centro de eventos.

3. En el notebook Zeppelin, en un nuevo párrafo, escriba el siguiente fragmento de código para leer los mensajes recibidos en Spark.

		%sql select * from mytemptable limit 10

	La siguiente captura de pantalla muestra los mensajes recibidos en **mytemptable**.

	![Recibir los mensajes en Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Zeppelin.Output.png "Recibir mensajes en el notebook de Zeppelin")

4. Reinicie el intérprete Spark SQL para salir de la aplicación. Haga clic en la pestaña **Interpreter** (Intérprete) en la parte superior y, para el intérprete Spark, haga clic en **Restart** (Reiniciar).

	![Reinicie el intepretador Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Zeppelin.Restart.Interpreter.png "Reinicie el intepretador Zeppelin")

##<a name="sparkstreamingha"></a>Ejecute la aplicación de transmisión por secuencias con alta disponibilidad

Usar Zeppelin para recibir datos de transmisión por secuencias en el clúster Spark en HDInsight es un buen enfoque para diseñar un prototipo de su aplicación. Sin embargo, para ejecutar la aplicación de transmisión por secuencias en una instalación de producción con alta disponibilidad y resistencia, deberá hacer lo siguiente:

1. Sobrescriba el jar de dependencia en la cuenta de almacenamiento asociada con el clúster.
2. Cree una aplicación de transmisión por secuencias.
3. RDP en el clúster y copie el jar de aplicación en el nodo principal del clúster.
3. RDP en el clúster y ejecute la aplicación en el nodo del clúster.

Puede descargar instrucciones sobre cómo realizar estos pasos y un ejemplo de aplicación de transmisión desde GitHub en [https://github.com/hdinsight/hdinsight-spark-examples](https://github.com/hdinsight/hdinsight-spark-examples).


##<a name="seealso"></a>Otras referencias


* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)
* [Inicio rápido: Aprovisionamiento de un clúster Spark en HDInsight y ejecución de consultas interactivas mediante Spark SQL](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Uso de Spark en HDInsight para crear aplicaciones de aprendizaje automático](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Uso de herramientas de BI con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-use-bi-tools.md)
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=July15_HO3-->