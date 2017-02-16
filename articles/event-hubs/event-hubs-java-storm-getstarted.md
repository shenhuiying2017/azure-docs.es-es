---
title: "Introducción a Event Hubs en Java con Apache Storm | Microsoft Docs"
description: "Siga este tutorial para empezar a usar Centros de eventos de Azure; a enviar eventos con Java y a recibirlos en un clúster de Apache Storm."
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 385869bd-1ebe-44ae-8113-cc4679a568eb
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 52c9ded8af99c5ae0836be8e71394dd1e5072aff
ms.openlocfilehash: 7566a8d0643607ee80c056ed4410aefd930226b8


---
# <a name="get-started-with-event-hubs"></a>Introducción a los Centros de eventos
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introducción
Centros de eventos es un sistema de recopilación de alta escalabilidad que puede recibir millones de eventos por segundo, habilitando una aplicación para procesar y analizar las grandes cantidades de datos generados por las aplicaciones y los dispositivos conectados. Una vez recopilados en los Centros de eventos, puede transformar y almacenar los datos usando cualquier proveedor de análisis en tiempo real o clúster de almacenamiento.

Para obtener más información, consulte [Información general sobre Centros de eventos][Event Hubs Overview].

En este tutorial se describe cómo recopilar mensajes en un Centro de eventos mediante una aplicación de consola en Java y cómo recuperarlos en paralelo mediante Apache Storm.

Para completar este tutorial, necesitará lo siguiente:

* Un entorno de desarrollo de Java configurado para ejecutar [Maven](http://maven.apache.org/). En este tutorial, se da por hecho que se va a trabajar con [Eclipse](https://www.eclipse.org/).
* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Ejecución de las aplicaciones
Ahora está preparado para ejecutar las aplicaciones.

1. Ejecute la clase **LogTopology** desde Eclipse y espere a que inicie los receptores para todas las particiones.
2. Ejecute el proyecto **Remitente**, presione **Entrar** en la ventana de la consola y vea cómo los eventos aparecen en la ventana del receptor.
   
    ![][22]

> [!NOTE]
> Solo en este tutorial, use Storm en modo local para fines de desarrollo. Consulte [Introducción a Storm en HDInsight][HDInsight Storm Overview] y la documentación oficial de [Apache Storm][Apache Storm] para obtener más información sobre los patrones y las implementaciones de Storm.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Los siguientes recursos están disponibles para desarrollar aplicaciones que integran Centros de eventos y Storm.

* [Análisis de datos de sensores con Apache Storm, Centro de eventos y HBase en HDInsight] es un tutorial de escenario integral donde se usan Centros de eventos, Storm y HBase para introducir datos de sensores en un clúster de Hadoop.
* [Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio][Develop streaming data processing applications with SCP.NET and C# on Storm and HDInsight] es un tutorial donde se describe cómo escribir procesos de Storm con C#.

<!-- Images. -->
[22]: ./media/event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs Overview]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[HDInsight Storm Overview]: ../hdinsight/hdinsight-storm-overview.md
[Análisis de datos de sensores con Apache Storm, Centro de eventos y HBase en HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Develop streaming data processing applications with SCP.NET and C# on Storm and HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md



<!--HONumber=Dec16_HO2-->


