<properties pageTitle="Introducción a los Centros de eventos" metaKeywords="Bus de servicio de Azure, Centro de eventos, introducción a los Centros de eventos" description="Follow this tutorial to get started using Azure Event Hubs sending events with C and receiving them in an Apache Storm cluster" metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="c" ms.devlang="java" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>Introducción a los Centros de eventos

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

Centros de eventos es un sistema de recopilación de alta escalabilidad que puede recibir millones de eventos por segundo, habilitando una aplicación para procesar y analizar las grandes cantidades de datos generados por las aplicaciones y los dispositivos conectados. Una vez que se recopilan los datos en los Centros de eventos, puede transformar y almacenar los datos con cualquier proveedor de análisis en tiempo real o clúster de almacenamiento.

Para obtener más información, consulte [Información general de los Centros de eventos].

En este tutorial, obtendrá información sobre cómo introducir mensajes en un Centro de eventos mediante una aplicación de consola en C y cómo recuperarlos en paralelo mediante Apache Storm.

Para completar este tutorial necesitará lo siguiente:

+ Un entorno de desarrollo de C. Para este tutorial, consideraremos la pila de gcc en una [VM Linux de Azure](http://azure.microsoft.com/es-es/documentation/articles/virtual-machines-linux-tutorial/) con Ubuntu 14.04. En los vínculos externos se proporcionarán instrucciones para otros entornos.

+ Un entorno de desarrollo de Java configurado para ejecutar [Maven](http://maven.apache.org/). Para este tutorial, asumimos que vamos a trabajar con [Eclipse](https://www.eclipse.org/).

+ Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://www.windowsazure.com/es-es/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure</a>.

## Creación de un centro de eventos

1. Inicie sesión en el [Portal de administración de Azure] y haga clic en **NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, en **Bus de servicio**, en **Centro de eventos** y en **Creación rápida**.

   	![][1]

3. Escriba un nombre para su centro de eventos, seleccione la región deseada y, a continuación, haga clic en **Crear un centro de eventos nuevo**.

   	![][2]

4. Haga clic en el espacio de nombres que acaba de crear (normalmente ***nombre del centro de eventos*-ns**).

   	![][3]

5. Haga clic en la pestaña **Centros de eventos** en la parte superior de la página y, a continuación, haga clic en el centro de eventos que acaba de crear.

   	![][4]

6. Haga clic en la pestaña **Configurar** en la parte superior de la página, agregue una regla llamada **SendRule** con derechos *Enviar*, agregue otra regla llamada **ReceiveRule** con derechos *Escuchar* y, a continuación, haga clic en **Guardar**.

   	![][5]

7. En la misma página, anote las claves generadas para **SendRule** y **ReceiveRule**.

   	![][6c]

Ahora ya se ha creado el Centro de eventos y ya tiene las cadenas de conexión que necesita para enviar y recibir eventos.

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-c](../includes/service-bus-event-hubs-get-started-send-c.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Ejecución de las aplicaciones

Ahora está listo para ejecutar las aplicaciones.

1.	Ejecute la clase **LogTopology** desde Eclipse y espere a que inicie los receptores para todas las particiones.

2.	Ejecute el programa **remitente** y vea todos los eventos que aparecen en la ventana del receptor.

   	![][23]

> [AZURE.NOTE] Solo en este tutorial, use Storm en modo local para fines de desarrollo. Consulte la [Información general de Storm en HDInsight] y la documentación oficial de [Apache Storm] para obtener información de los patrones y las implementaciones de Storm.

## Pasos siguientes
Los siguientes recursos están disponibles para desarrollar aplicaciones que integran Centros de eventos y Storm.

- [Análisis de los datos de sensor con Storm y HBase en HDInsight] es un escenario integral donde se usan Centros de eventos, Storm y HBase para introducir datos de sensores en un clúster de Hadoop.
- [Desarrollo de aplicaciones de procesamiento de datos de streaming con SCP.NET y C# en Storm de HDInsight] es un tutorial donde se describe cómo escribir procesos de Storm con C#.

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png
[6c]: ./media/service-bus-event-hubs-getstarted/create-event-hub6c.png

[23]: ./media/service-bus-event-hubs-getstarted/receive-storm3.png

<!-- Links -->
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Host del procesador de eventos]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Información general de los Centros de eventos]: http://msdn.microsoft.com/es-es/library/azure/dn836025.aspx

[Apache Storm]: https://storm.incubator.apache.org
[Información general de Storm en HDinsight]: http://azure.microsoft.com/es-es/documentation/articles/hdinsight-storm-overview/
[Análisis de los datos de sensor con Storm y HDInsight]: http://azure.microsoft.com/es-es/documentation/articles/hdinsight-storm-sensor-data-analysis/
[Desarrollo de aplicaciones de procesamiento de datos de streaming con SCP.NET y C# en Storm y HDInsight]: http://azure.microsoft.com/es-es/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/
