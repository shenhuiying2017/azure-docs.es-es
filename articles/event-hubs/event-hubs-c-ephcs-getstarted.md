<properties
	pageTitle="Introducción a los Centros de eventos en C y C# | Microsoft Azure"
	description="Siga este tutorial para empezar a usar Centros de eventos de Azure, a enviar eventos con C y a recibirlos en C# mediante EventProcessorHost."
	services="event-hubs"
	documentationCenter=""
	authors="jtaubensee"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="c"
	ms.devlang="csharp"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="jotaub;sethm"/>

# Introducción a los Centros de eventos

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introducción

Centros de eventos es un sistema de recopilación de alta escalabilidad que puede recibir millones de eventos por segundo, habilitando una aplicación para procesar y analizar las grandes cantidades de datos generados por las aplicaciones y los dispositivos conectados. Una vez recopilados en los Centros de eventos, puede transformar y almacenar los datos usando cualquier proveedor de análisis en tiempo real o clúster de almacenamiento.

Para obtener más información, consulte [Información general de los Centros de eventos de Azure][].

En este tutorial, aprenderá a recopilar mensajes en un Centro de eventos mediante una aplicación de consola en C y a recuperarlos en paralelo con la biblioteca [Event Processor Host][] de C#.

Para completar este tutorial, necesitará lo siguiente:

+ Un entorno de desarrollo de C. Para este tutorial, consideraremos la pila de gcc en una [VM Linux de Azure](../virtual-machines/virtual-machines-linux-quick-create-cli.md) con Ubuntu 14.04. En los vínculos externos se proporcionarán instrucciones para otros entornos.

+ Microsoft Visual Studio Express para Windows

+ Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Ejecución de las aplicaciones

Ahora está preparado para ejecutar las aplicaciones.

1.	Ejecute el proyecto **Receiver** desde Visual Studio y espere a que inicie los receptores para todas las particiones.

	![][21]

2.	Ejecute el programa **Sender** y verá que los eventos aparecen en la ventana del receptor.

	![][24]

## Pasos siguientes

Ahora que ha creado una aplicación de trabajo que crea un centro de eventos y envía y recibe datos, puede pasar a los siguientes escenarios:

- Una [aplicación de ejemplo completa que usa Centros de eventos][].
- El ejemplo [Escala horizontal del procesamiento de eventos con Centros de eventos][].
- Una [solución de mensajería en cola][] mediante las colas de Bus de servicio.
- [Información general de los Centros de eventos][]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Información general de los Centros de eventos]: event-hubs-overview.md
[Información general de los Centros de eventos de Azure]: event-hubs-overview.md
[aplicación de ejemplo completa que usa Centros de eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escala horizontal del procesamiento de eventos con Centros de eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[solución de mensajería en cola]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

<!---HONumber=AcomDC_0928_2016-->