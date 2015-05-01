<properties
	pageTitle="Introducción a los Centros de eventos"
	description="Siga este tutorial para empezar a usar los centros de eventos de Azure con C# mediante EventProcessorHost"
	services="service-bus"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="core"
	ms.tgt_pltfrm="csharp"
	ms.devlang="csharp"
	ms.topic="hero-article"
	ms.date="04/13/2015"
	ms.author="sethm"/>

# Introducción a los Centros de eventos

[AZURE.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

## Introducción

Centros de eventos es un servicio que puede usar para procesar grande cantidades de datos de eventos desde dispositivos y aplicaciones conectados. Después de recopilar datos en Centros de eventos, puede almacenarlos mediante un clúster de almacenamiento o transformarlos por medio de un proveedor de análisis en tiempo real. Esta capacidad de recopilación y procesamiento de eventos a gran escala es un componente clave de las modernas arquitecturas de aplicaciones, entre las que se incluye Internet de las cosas.

En este tutorial se muestra cómo usar el Portal de administración de Azure para crear un centro de eventos. También se muestra cómo recopilar mensajes en un centro de eventos mediante el uso de una aplicación de consola en C# y cómo recuperarlos en paralelo por medio de la biblioteca [Host del procesador de eventos] de C#.

Para completar este tutorial, necesitará lo siguiente:

+ Microsoft Visual Studio 2013 o Microsoft Visual Studio Express 2013 para Windows.

+ Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure</a>.

## Creación de un Centro de eventos

1. Inicie sesión en el [Portal de administración de Azure] y haga clic en **NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, **Service Bus**, **Centro de eventos** y, finalmente, en **Creación rápida**.

   	![][1]

3. Escriba un nombre para el centro de eventos, seleccione la región deseada y, a continuación, haga clic en **Crear un centro de eventos nuevo**.

   	![][2]

4. Haga clic en el espacio de nombres que acaba de crear (normalmente ***nombre del centro de eventos*-ns**).

   	![][3]

5. Haga clic en la pestaña **Centros de eventos** en la parte superior de la página y, a continuación, haga clic en el centro de eventos que acaba de crear.

   	![][4]

6. Haga clic en la pestaña **Configurar** en la parte superior, agregue una regla llamada **SendRule** con derechos para *enviar*, agregue otra regla llamada **ReceiveRule** con derechos para *administrar, enviar y escuchar* y luego haga clic en **Guardar**.

   	![][5]

7. Haga clic en la pestaña **Panel** en la parte superior de la página y, a continuación, haga clic en **Información de conexión**. Anote las cadenas de conexión o cópielas en algún sitio para usarlas más tarde en este tutorial.

   	![][6]

Se creó el centro de eventos y cuenta con las cadenas de conexión que necesita para enviar y recibir eventos.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Ejecución de las aplicaciones

Ahora está preparado para ejecutar las aplicaciones.

1.	Desde Visual Studio, ejecute el proyecto **Receptor** y espere a que inicie los receptores para todas las particiones.

   	![][21]

2.	Ejecute el proyecto **Remitente**, presione **Entrar** en las ventanas de la consola y vea cómo los eventos aparecen en la ventana del receptor.

   	![][22]

## Pasos siguientes

Ahora que ha creado una aplicación de trabajo que crea un centro de eventos y envía y recibe datos, puede pasar a los siguientes escenarios:

- Una [aplicación de ejemplo completa que usa Centros de eventos].
- El ejemplo [Escala horizontal del procesamiento de eventos con Centros de eventos].
- Una [solución de mensajería en cola] mediante las colas de Bus de servicio.

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png

[21]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Host del procesador de eventos]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Información general de los Centros de eventos]: http://msdn.microsoft.com/library/azure/dn836025.aspx
[aplicación de ejemplo que usa Centros de eventos]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[Escala horizontal del procesamiento de eventos con Centros de eventos]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[solución de mensajería en cola]: cloud-services-dotnet-multi-tier-app-using-service-bus-queues.md


<!--HONumber=52-->