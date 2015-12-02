<properties
	pageTitle="Procesamiento de mensajes de dispositivo a la nube del centro de IoT | Microsoft Azure"
	description="Siga este tutorial para aprender sobre patrones útiles para procesar mensajes de dispositivo a nube del centro de IoT."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT

## Introducción

El centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y una aplicación back-end. Existen tutoriales anteriores ([Comenzar a usar el centro de IoT] y [Envío de mensajes de nube a dispositivo con el centro de IoT]) que muestran la funcionalidad básica de comunicación de dispositivo a nube y viceversa mediante el centro de IoT, así como el modo de tener acceso a ellos desde componentes de dispositivos y la nube.

Este tutorial se basa en el código que se muestra en [Introducción al centro de IoT] para presentar dos modelos para el procesamiento de mensajes de dispositivo a nube.

El primer patrón es el almacenamiento fiable de mensajes de dispositivo a nube en [Blobs de Azure]. Este escenario es muy común al implementar análisis de *rutas de acceso inactivas*, donde los datos almacenados en blobs se usan como entrada para el análisis que se realiza con herramientas como, por ejemplo, [Factoría de datos de Azure] o la pila [Hadoop].

El segundo patrón es el procesamiento fiable de mensajes de dispositivo a nube *interactivos*. Los mensajes de dispositivo a nube se denominan *interactivos* cuando desencadenan de manera inmediata un conjunto de acciones en la aplicación back-end, en contraposición a los mensajes de *punto de datos*, que se envían a un motor de análisis. Por ejemplo, una alarma procedente de un dispositivo que tiene que desencadenar la inserción de un vale en un sistema CRM es un mensaje de dispositivo a nube *interactivo*, en contraposición con, por ejemplo, un mensaje de telemetría que contiene muestras de temperatura, que es un mensaje de *punto de datos*.

Puesto que el centro de IoT expone un extremo compatible con centros de eventos para recibir mensajes de dispositivo a nube, este tutorial usa [EventProcessorHost] para hospedar una clase de procesador de eventos, que:

* Almacena de manera fiable mensajes de *punto de datos* en Blobs de Azure.
* Reenvía mensajes de dispositivo a nube *interactivos* a una [cola de bus de servicio] para el procesamiento inmediato.

El [Bus de servicio][Service Bus Queue] es una excelente forma de asegurar un procesamiento fiable de mensajes interactivos, ya que proporciona puntos de comprobación de cada mensaje y deduplicación basada en periodos de tiempo.

Al final de este tutorial ejecutará tres aplicaciones de consola de Windows:

* **SimulatedDevice**, una versión modificada de la aplicación creada en [Introducción al centro de IoT], que envía mensajes de dispositivo a nube de *punto de datos* cada segundo y mensajes de dispositivo a nube *interactivos* cada 10 segundos.
* **ProcessDeviceToCloudMessages**, que usa [EventProcessorHost] para almacenar de manera fiable mensajes de *punto de datos* en un blob de Azure y reenvía mensajes *interactivos* a una cola de Bus de servicio.
* **ProcessD2cInteractiveMessages**, que quita los mensajes de la cola.

> [AZURE.NOTE]El centro de IoT ofrece compatibilidad con el SDK para muchas plataformas de dispositivos y lenguajes (incluido C, Java y Javascript), mediante los SDK del dispositivo de IoT de Azure. Consulte el [Centro para desarrolladores de IoT de Azure] para obtener instrucciones paso a paso sobre cómo conectar el dispositivo al código de este tutorial y, en general, al Centro de IoT de Azure.

> [AZURE.NOTE]El contenido de este tutorial es directamente aplicable a otras formas de consumir mensajes compatibles con centros de eventos como, por ejemplo, proyectos de [Hadoop] como Storm. Consulte [IoT Hub Guidance - Event Hubs compatibility] (Directrices sobre el centro de IoT - Compatibilidad con centros de eventos).

Para completar este tutorial, necesitará lo siguiente:

+ Microsoft Visual Studio 2015

+ Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdevelop%2Fiot%2Ftutorials%2Fprocess-d2c%2F target="\_blank").

También se dan por sentados ciertos conocimientos sobre el [Almacenamiento de Azure] y el [Bus de servicio de Azure].


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## Ejecución de las aplicaciones

Ya está preparado para ejecutar las aplicaciones.

1.	Desde Visual Studio, haga clic con el botón secundario del mouse en la solución y seleccione **Establecer proyectos de inicio...** Seleccione **Proyectos de inicio múltiples**, a continuación, seleccione la acción **Iniciar** para las aplicaciones **ProcessDeviceToCloudMessages**, **SimulatedDevice** y **ProcessD2cInteractiveMessages**.

2.	Presione **F5** y verá cómo se inician todas las aplicaciones. El procesador de mensajes interactivos deberá procesar todos los mensajes interactivos enviados por el dispositivo simulado.

  ![][50]

> [AZURE.NOTE]Para ver el archivo blob que se está actualizando, puede que tenga que reducir la constante `MAX_BLOCK_SIZE` en `StoreEventProcessor` a un valor más pequeño (es decir, `1024`). Esto es porque tardará algún tiempo en alcanzar el límite de tamaño de bloque con los datos enviados por el dispositivo simulado. Con dicha edición, debería poder ver un blob creado y actualizado en el contenedor de almacenamiento.

## Pasos siguientes

En este tutorial, ha aprendido a procesar de manera fiable *puntos de datos* y mensajes de dispositivo a nube *interactivos* mediante [EventProcessorHost]. Es posible implementar una lógica de procesamiento de mensajes analógica con:

- [Uploading files from devices] (Carga de archivos desde dispositivos) describe un patrón que hace uso de los mensajes de nube a dispositivo para facilitar la carga de archivos desde los dispositivos.

Información adicional sobre el Centro de IoT:

* [Información general sobre el centro de IoT]
* [Guía del desarrollador del centro de IoT]
* [Directrices sobre el Centro de IoT]
* [Lenguajes y plataformas de dispositivos compatibles][Supported devices]
* [Centro para desarrolladores de IoT de Azure]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Blobs de Azure]: https://azure.microsoft.com/es-ES/documentation/articles/storage-dotnet-how-to-use-blobs/
[Factoría de datos de Azure]: https://azure.microsoft.com/es-ES/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/es-ES/documentation/services/hdinsight/
[Service Bus Queue]: https://azure.microsoft.com/es-ES/documentation/articles/service-bus-dotnet-how-to-use-queues/
[cola de bus de servicio]: https://azure.microsoft.com/es-ES/documentation/articles/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Transient Fault Handling]: https://msdn.microsoft.com/es-ES/library/hh680901(v=pandp.50).aspx
[control de errores transitorios]: https://msdn.microsoft.com/es-ES/library/hh680901(v=pandp.50).aspx

[IoT Hub Guidance - Event Hubs compatibility]: iot-hub-guidance.md#eventhubcompatible

[Almacenamiento de Azure]: https://azure.microsoft.com/es-ES/documentation/services/storage/
[Bus de servicio de Azure]: https://azure.microsoft.com/es-ES/documentation/services/service-bus/

[Azure preview portal]: https://portal.azure.com/

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Envío de mensajes de nube a dispositivo con el centro de IoT]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Procesamiento de mensajes de dispositivo a nube]: iot-hub-csharp-csharp-process-d2c.md
[Uploading files from devices]: iot-hub-csharp-csharp-file-upload.md

[Información general sobre el centro de IoT]: iot-hub-what-is-iot-hub.md
[Directrices sobre el Centro de IoT]: iot-hub-guidance.md
[Guía del desarrollador del centro de IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Comenzar a usar el centro de IoT]: iot-hub-csharp-csharp-getstarted.md
[Introducción al centro de IoT]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centro para desarrolladores de IoT de Azure]: http://www.azure.com/develop/iot

<!---HONumber=Nov15_HO3-->