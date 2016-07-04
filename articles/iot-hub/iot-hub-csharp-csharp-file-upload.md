<properties
	pageTitle="Carga de archivos desde dispositivos mediante el centro de IoT | Microsoft Azure"
	description="Siga este tutorial para aprender a cargar archivos desde dispositivos con el centro de IoT de Azure con C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/21/2016"
     ms.author="elioda"/>

# Tutorial: cómo cargar archivos desde dispositivos a la nube con un centro de IoT

## Introducción

El centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y una aplicación back-end. Existen tutoriales anteriores ([Introducción al Centro de IoT de Azure para .NET] y [Tutorial: cómo envío mensajes de nube a dispositivo con el Centro de IoT]) que muestran la funcionalidad básica de comunicación de dispositivo a nube y viceversa. Asimismo, en [Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT] se describe una forma de almacenar de manera confiable los mensajes de dispositivo a nube en el Almacenamiento de blobs de Azure. Sin embargo, en algunos casos no se pueden asignar fácilmente los datos de que los dispositivos envían en los mensajes de dispositivo a nube con un tamaño relativamente reducido que acepta el Centro de IoT. Algunos ejemplos son los archivos de gran tamaño que contienen imágenes, vídeos, muestras de datos de vibración a alta frecuencia, o bien archivos que contienen algún tipo de datos preprocesados. Dichos archivos se suelen procesar por lotes en la nube mediante herramientas como [Data Factory de Azure] o la pila [Hadoop]. Cuando se prefiera cargar un archivo desde un dispositivo en lugar de enviar eventos, se puede utilizar la funcionalidad de fiabilidad y seguridad del Centro de IoT.

Este tutorial se basa en el código de [Tutorial: cómo envío mensajes de nube a dispositivo con el Centro de IoT] para mostrarle cómo usar las funcionalidades de carga de archivos del Centro de IoT. También explica cómo proporcionar de forma segura un URI de blob de Azure a un dispositivo para cargar un archivo, además de cómo usar las notificaciones de carga de archivos del Centro de IoT con el objetivo de desencadenar el procesamiento del archivo en el back-end de la aplicación.

Al final de este tutorial, ejecutará dos aplicaciones de consola de Windows:

* **SimulatedDevice**, una versión modificada de la aplicación creada en [Tutorial: cómo envío mensajes de nube a dispositivo con el Centro de IoT], que carga un archivo en el almacenamiento empleando un URI de SAS que proporciona el Centro de IoT.
* **ReadFileUploadNotification**, que recibe notificaciones de carga de archivos del Centro de IoT.

> [AZURE.NOTE] El Centro de IoT admite muchas plataformas de dispositivos y lenguajes (incluido C, Java y JavaScript), mediante los SDK de dispositivo IoT de Azure. Consulte el [Centro para desarrolladores de IoT de Azure] para ver instrucciones paso a paso sobre cómo conectar el dispositivo al código que se muestra en este tutorial y, en general, al Centro de IoT de Azure.

Para completar este tutorial, necesitará lo siguiente:

+ Microsoft Visual Studio 2015

+ Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta gratuita en tan solo unos minutos. Para más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdevelop%2Fiot%2Ftutorials%2Ffile-upload%2F target="\_blank").

[AZURE.INCLUDE [iot-hub-file-upload-device-csharp](../../includes/iot-hub-file-upload-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-file-upload-cloud-csharp](../../includes/iot-hub-file-upload-cloud-csharp.md)]

## Ejecución de las aplicaciones

Ya está preparado para ejecutar las aplicaciones.

1. Desde Visual Studio, haga clic con el botón derecho en la solución y seleccione **Establecer proyectos de inicio**. Seleccione **Proyectos de inicio múltiples**, después, elija la acción **Iniciar** para **ReadFileUploadNotification** y **SimulatedDevice**.

2. Presione **F5**. Deberían iniciarse las dos aplicaciones. Debería ver que se ha completado la carga en una aplicación de consola y que la otra aplicación de consola ha recibido el mensaje de notificación de carga. Puede usar el [Portal de Azure] o el Explorador de servidores de Visual Studio para que el archivo cargado está en la cuenta de almacenamiento.

  ![][50]


## Pasos siguientes

En este tutorial ha aprendido a usar la funcionalidad de carga de archivos del Centro de IoT para simplificar la carga de archivos desde los dispositivos. Puede explorar las características del centro de IoT y distintos escenarios con el tutorial siguiente:

- [Process Device-to-Cloud messages] (Procesamiento de mensajes de dispositivo a nube) muestra cómo procesar de forma fiable la telemetría y los mensajes interactivos procedentes de los dispositivos.

Información adicional sobre el centro de IoT:

* [Información general sobre el centro de IoT]
* [Guía del desarrollador del centro de IoT]
* [Directrices sobre el centro de IoT]
* [Lenguajes y plataformas de dispositivos compatibles][Supported devices]
* [Centro para desarrolladores de Azure]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png

<!-- Links -->

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md

[Portal de Azure]: https://portal.azure.com/

[Data Factory de Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Tutorial: cómo envío mensajes de nube a dispositivo con el Centro de IoT]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT]: iot-hub-csharp-csharp-process-d2c.md
[Uploading files from devices]: iot-hub-csharp-csharp-file-upload.md

[Información general sobre el centro de IoT]: iot-hub-what-is-iot-hub.md
[Directrices sobre el centro de IoT]: iot-hub-guidance.md
[Guía del desarrollador del centro de IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Introducción al Centro de IoT de Azure para .NET]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centro para desarrolladores de Azure]: http://www.azure.com/develop/iot
[Centro para desarrolladores de IoT de Azure]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_0622_2016-->