<properties
	pageTitle="Introducción a los Centros de eventos"
	description="Siga este tutorial para empezar a usar eventos de envío de centros de eventos de Azure con Java y recibirlos en un clúster de Apache Storm"
	services="event-hubs,service-bus"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="core"
	ms.tgt_pltfrm="java"
	ms.devlang="java"
	ms.topic="article"
	ms.date="06/17/2015"
	ms.author="sethm"/>

# Introducción a los Centros de eventos

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introducción

Centros de eventos es un sistema de recopilación de alta escalabilidad que puede recibir millones de eventos por segundo, habilitando una aplicación para procesar y analizar las grandes cantidades de datos generados por las aplicaciones y los dispositivos conectados. Una vez recopilados en los Centros de eventos, puede transformar y almacenar los datos usando cualquier proveedor de análisis en tiempo real o clúster de almacenamiento.

Para obtener más información, consulte [Información general de los centros de eventos].

En este tutorial, obtendrá información sobre cómo recopilar mensajes en un Centro de eventos mediante una aplicación de consola en Java y cómo recuperarlos en paralelo mediante Apache Storm.

Para completar este tutorial, necesitará lo siguiente:

+ Un entorno de desarrollo de Java configurado para ejecutar [Maven](http://maven.apache.org/). Para este tutorial, asumimos que vamos a trabajar con [Eclipse](https://www.eclipse.org/).

+ Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure￼</a>.

## Creación de un Centro de eventos

1. Inicie sesión en el [Portal de administración de Azure] y haga clic en **NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, **Service Bus**, **Centro de eventos** y, finalmente, en **Creación rápida**.

   	![][1]

3. Escriba un nombre para el centro de eventos, seleccione la región deseada y, a continuación, haga clic en **Crear un centro de eventos**.

   	![][2]

4. Haga clic en el espacio de nombres que acaba de crear (normalmente ***nombre del centro de eventos*-ns**).

   	![][3]

5. Haga clic en la pestaña **Centros de eventos** en la parte superior de la página y, a continuación, haga clic en el centro de eventos que acaba de crear.

   	![][4]

6. Haga clic en la pestaña **Configurar** en la parte superior de la página, agregue una regla llamada **SendRule** con derechos *Enviar*, agregue otra regla llamada **ReceiveRule** con derechos *Escuchar* y, a continuación, haga clic en **Guardar**.

   	![][5]

7. En la misma página, anote las claves generadas para **SendRule** y **ReceiveRule**.

   	![][6c]

Se creó el centro de eventos y cuenta con las cadenas de conexión que necesita para enviar y recibir eventos.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Ejecución de las aplicaciones

Ahora está preparado para ejecutar las aplicaciones.

1.	Ejecute la clase **LogTopology** desde Eclipse y espere a que inicie los receptores para todas las particiones.

2.	Ejecute el proyecto **Remitente**, presione **Entrar** en la ventana de la consola y vea cómo los eventos aparecen en la ventana del receptor.

   	![][22]

> [AZURE.NOTE]Solo en este tutorial, use Storm en modo local para fines de desarrollo. Consulte [Introducción a Apache Storm en HDInsight: análisis en tiempo real de Hadoop] y la documentación oficial de [Apache Storm] para obtener más información sobre los patrones y las implementaciones de Storm.

## Pasos siguientes

Los siguientes recursos están disponibles para desarrollar aplicaciones que integran Centros de eventos y Storm.

- [Análisis de datos de sensores con Apache Storm, Centro de eventos y HBase en HDInsight] es un tutorial de escenario integral donde se usan Centros de eventos, Storm y HBase para introducir datos de sensores en un clúster de Hadoop.
- [Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio] es un tutorial donde se describe cómo escribir procesos de Storm con C#.

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6c]: ./media/service-bus-event-hubs-java-storm-getstarted/create-event-hub6c.png

[22]: ./media/service-bus-event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Información general de los centros de eventos]: http://msdn.microsoft.com/library/azure/dn836025.aspx

[Apache Storm]: https://storm.incubator.apache.org
[Introducción a Apache Storm en HDInsight: análisis en tiempo real de Hadoop]: http://azure.microsoft.com/documentation/articles/hdinsight-storm-overview/
[Análisis de datos de sensores con Apache Storm, Centro de eventos y HBase en HDInsight]: http://azure.microsoft.com/documentation/articles/hdinsight-storm-sensor-data-analysis/
[Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio]: http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/
 

<!---HONumber=July15_HO3-->