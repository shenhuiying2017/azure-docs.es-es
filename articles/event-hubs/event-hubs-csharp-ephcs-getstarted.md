---
title: Introducción a los Centros de eventos en C# | Microsoft Docs
description: Siga este tutorial para empezar a usar Centros de eventos de Azure con C# y EventProcessorHost
services: event-hubs
documentationcenter: ''
author: jtaubensee
manager: timlt
editor: ''

ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/02/2016
ms.author: jotaub;sethm

---
# <a name="get-started-with-event-hubs"></a>Introducción a los Centros de eventos
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introducción
Centros de eventos es un servicio que procesa grandes cantidades de datos de eventos (telemetría) desde aplicaciones y dispositivos conectados. Después de recopilar datos en Centros de eventos, puede almacenarlos mediante un clúster de almacenamiento o transformarlos por medio de un proveedor de análisis en tiempo real. Esta funcionalidad de recopilación y procesamiento de eventos a gran escala es un componente clave de las modernas arquitecturas de aplicaciones, entre las que se incluye Internet de las cosas (IoT).

En este tutorial se muestra cómo usar el Portal de Azure clásico para crear un centro de eventos. También se muestra cómo recopilar mensajes en un centro de eventos mediante el uso de una aplicación de consola escrita en C# y cómo recuperarlos en paralelo por medio de la biblioteca [Host del procesador de eventos][] de C#.

Para completar este tutorial, necesitará lo siguiente:

* [Microsoft Visual Studio](http://visualstudio.com)
* Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/free/).

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Ejecución de las aplicaciones
Ahora está preparado para ejecutar las aplicaciones.

1. En Visual Studio, abra el proyecto **Receiver** que creó anteriormente.
2. Haga clic con el botón derecho en la solución **Receiver**, luego en **Agregar** y, finalmente, en **Proyecto existente**.
3. Busque el archivo Sender.csproj existente, y haga doble clic en él para agregarlo a la solución.
4. Vuelva a hacer clic con el botón derecho en la solución **Receiver** y, después, en **Propiedades**. Se muestra la página de propiedades de **Receiver** .
5. Haga clic en **Proyecto de inicio** y luego en el botón **Proyectos de inicio múltiples**. En el cuadro **Acción** de los proyectos **Receiver** y **Sender**, seleccione **Inicio**.
   
    ![][19]
6. Haga clic en **Dependencias del proyecto**. En el cuadro **Proyectos**, haga clic en **Sender**. En el cuadro **Depende de:**, asegúrese de que está seleccionado **Receiver**.
   
    ![][20]
7. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Propiedades**.
8. Pulse F5 para ejecutar el proyecto **Receiver** desde Visual Studio y espere a que inicie los receptores de todas las particiones.
   
   ![][21]
9. El proyecto **Sender** proyecto se ejecutará automáticamente. Presione **Entrar** en la ventana de la consola y verá que los eventos aparecen en la ventana del receptor.
   
   ![][22]

Presione **Ctrl + C** en la ventana de **Sender** para finalizar la aplicación Sender y, después, presione **ENTRAR** en la ventana de Receiver para cerrar la aplicación.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha creado una aplicación de trabajo que crea un centro de eventos y envía y recibe datos, puede pasar a los siguientes escenarios:

* Una [aplicación de ejemplo completa que usa Centros de eventos][aplicación de ejemplo completa que usa Centros de eventos].
* El ejemplo [Escala horizontal del procesamiento de eventos con Centros de eventos][Escala horizontal del procesamiento de eventos con Centros de eventos] .
* [Información general de los Centros de eventos][]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Portal de Azure clásico]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Información general sobre Centros de eventos]: event-hubs-overview.md
[aplicación de ejemplo completa que usa Centros de eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escala horizontal del procesamiento de eventos con Centros de eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[solución de mensajería en cola]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md




<!--HONumber=Oct16_HO2-->


