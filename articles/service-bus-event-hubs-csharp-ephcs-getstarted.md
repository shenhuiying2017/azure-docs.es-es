<properties pageTitle="Introducción a los Centros de eventos" metaKeywords="Bus de servicio de Azure, Centro de eventos, introducción a los Centros de eventos" description="Follow this tutorial to get started using Azure Event Hubs with C# using EventProcessorHost" metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="csharp" ms.devlang="csharp" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>Introducción a los Centros de eventos

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

Centros de eventos es un sistema de recopilación de alta escalabilidad que puede recibir millones de eventos por segundo, habilitando una aplicación para procesar y analizar las grandes cantidades de datos generados por las aplicaciones y los dispositivos conectados. Una vez que se recopilan los datos en los Centros de eventos, puede transformar y almacenar los datos con cualquier proveedor de análisis en tiempo real o clúster de almacenamiento.

Para obtener más información, consulte [Información general de los Centros de eventos].

En este tutorial, obtendrá información sobre cómo introducir mensajes en un Centro de eventos mediante una aplicación de consola en C# y recuperarlos en paralelo con la biblioteca [Host del procesador de eventos] de C#.

Para completar este tutorial necesitará lo siguiente:

+ Microsoft Visual Studio Express 2013 para Windows

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

6. Haga clic en la pestaña **Configurar** en la parte superior, agregue una regla llamada **SendRule** con derechos *Enviar*, agregue otra regla llamada **ReceiveRule** con derechos *Administrar, Enviar, Escuchar* y, a continuación, haga clic en **Guardar**.

   	![][5]

7. Haga clic en la pestaña **Panel** en la parte superior de la página y, a continuación, haga clic en **Información de conexión**. Anote las dos cadenas de conexión.

   	![][6]

Ahora ya se ha creado el Centro de eventos y ya tiene las cadenas de conexión que necesita para enviar y recibir eventos.

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Ejecución de las aplicaciones

Ahora está listo para ejecutar las aplicaciones.

1.	Ejecute el proyecto **Receptor** desde Visual Studio y, a continuación, espere a que inicie los receptores para todas las particiones.

   	![][21]

2.	Ejecute el proyecto **Remitente**, presione **Entrar** en las ventanas de la consola y vea los eventos que aparecen en la ventana del receptor.

   	![][22]

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
[Información general de los Centros de eventos]: http://msdn.microsoft.com/es-es/library/azure/dn836025.aspx
