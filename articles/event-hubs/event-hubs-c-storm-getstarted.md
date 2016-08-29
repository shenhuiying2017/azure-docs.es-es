<properties
	pageTitle="Introducción a los Centros de eventos con C y Apache Storm | Microsoft Azure"
	description="Siga este tutorial para empezar a usar Centros de eventos de Azure; a enviar eventos en C y a recibirlos en un clúster de Apache Storm."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="c"
	ms.devlang="java"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="sethm"/>

# Introducción a los Centros de eventos

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introducción

Centros de eventos es un sistema de recopilación de alta escalabilidad que puede recibir millones de eventos por segundo, habilitando una aplicación para procesar y analizar las grandes cantidades de datos generados por las aplicaciones y los dispositivos conectados. Una vez recopilados en los Centros de eventos, puede transformar y almacenar los datos usando cualquier proveedor de análisis en tiempo real o clúster de almacenamiento.

Para obtener más información, consulte [Información general de los centros de eventos].

En este tutorial, obtendrá información sobre cómo insertar mensajes en un Centro de eventos mediante una aplicación de consola en C y cómo recuperarlos en paralelo mediante Apache Storm.

Para completar este tutorial, necesitará lo siguiente:

+ Un entorno de desarrollo de C. Para este tutorial, consideraremos la pila de gcc en una [VM Linux de Azure](../virtual-machines/virtual-machines-linux-quick-create-cli.md) con Ubuntu 14.04. En los vínculos externos se proporcionarán instrucciones para otros entornos.

+ Un entorno de desarrollo de Java configurado para ejecutar [Maven](http://maven.apache.org/). Para este tutorial, asumimos que vamos a trabajar con [Eclipse](https://www.eclipse.org/).

+ Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Ejecución de las aplicaciones

Ahora está preparado para ejecutar las aplicaciones.

1.	Ejecute la clase **LogTopology** desde Eclipse y espere a que inicie los receptores para todas las particiones.

2.	Ejecute el programa **sender** y verá que los eventos aparecen en la ventana del receptor.

	![][23]

> [AZURE.NOTE] Solo en este tutorial, use Storm en modo local para fines de desarrollo. Consulte [Introducción a Storm en HDInsight] y la documentación oficial de [Apache Storm] para obtener más información sobre los patrones y las implementaciones de Storm.

## Pasos siguientes

Los siguientes recursos están disponibles para desarrollar aplicaciones que integran Centros de eventos y Storm.

- [Análisis de datos de sensores con Apache Storm, Centro de eventos y HBase en HDInsight][] es un tutorial de escenario integral donde se usan Centros de eventos, Storm y HBase para introducir datos de sensores en un clúster de Hadoop.
- [Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio][] es un tutorial donde se describe cómo escribir procesos de Storm con C#.

<!-- Images. -->
[23]: ./media/event-hubs-c-storm-getstarted/receive-storm3.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Información general de los centros de eventos]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[Introducción a Storm en HDInsight]: ../hdinsight/hdinsight-storm-overview.md/
[Análisis de datos de sensores con Apache Storm, Centro de eventos y HBase en HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md

<!---HONumber=AcomDC_0817_2016-->