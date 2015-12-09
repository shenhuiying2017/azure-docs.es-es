<properties
	pageTitle="Introducción al centro de IoT de Azure | Microsoft Azure"
	description="Siga este tutorial para aprender a usar el centro de IoT de Azure con C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# Tutorial: Introducción al Centro de IoT

## Introducción

El centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y una aplicación back-end. Uno de los mayores desafíos relacionados con los proyectos de IoT consiste en conectar dispositivos al servidor de aplicaciones de manera segura y fiable. Para simplificar este escenario, el centro de IoT de Azure permite el envío a gran escala de mensajes de dispositivo a nube y viceversa. Asimismo, permite establecer comunicaciones seguras mediante el uso de credenciales de seguridad por cada dispositivo y funciones de control de acceso; además, incluye bibliotecas de dispositivos para las plataformas y los lenguajes más populares.

Este tutorial muestra cómo usar el portal de Azure para crear un centro de IoT. También muestra cómo crear una identidad de dispositivo en el centro de IoT, cómo crear un dispositivo simulado que envía mensajes de dispositivo a nube y recibe dichos mensajes desde el back-end de la nube.

Al final de este tutorial tendrá tres aplicaciones de consola de Windows:

* **CreateDeviceIdentity**, que crea una identidad de dispositivo y una clave de seguridad asociada para conectar el dispositivo simulado.
* **ReadDeviceToCloudMessages**, que lee los mensajes enviados del dispositivo a la nube y muestra su contenido.
* **SimulatedDevice**, que se conecta a su centro de IoT con la identidad del dispositivo creada anteriormente y envía un mensaje de dispositivo a nube cada segundo.

> [AZURE.NOTE]El centro de IoT ofrece compatibilidad con el SDK para muchas plataformas de dispositivos y lenguajes (incluido C, Java y Javascript), mediante los SDK del dispositivo de IoT de Azure. Consulte el [Centro para desarrolladores de IoT de Azure][] para obtener instrucciones paso a paso sobre cómo conectar su dispositivo al código de este tutorial y, en general, al Centro de IoT de Azure. Próximamente estarán disponibles SDK de servicios IoT de Azure para Java y Node.

Para completar este tutorial, necesitará lo siguiente:

+ Microsoft Visual Studio 2015

+ Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdevelop%2Fiot%2Ftutorials%2Fgetstarted%2F target="\_blank").

## Crear un centro de IoT

1. Inicie sesión en el [Portal de Azure].

2. En la barra de accesos directos, haga clic en **Nuevo**. A continuación, haga clic en **Internet de las cosas** y, a continuación, haga clic en **Centro de IoT**.

   	![][1]

3. En la hoja **Nuevo centro de IoT**, especifique la configuración deseada para el centro de IoT.

   	![][2]

    * En el cuadro **Nombre**, escriba un nombre para identificar el centro de IoT. Cuando se valide el **Nombre**, aparecerá una marca de verificación verde en el cuadro **Nombre**.
    * Cambie el **Nivel de escala y precios** según lo deseado. Este tutorial no requiere ningún nivel determinado.
    * En el cuadro **Grupo de recursos**, cree un nuevo grupo de recursos o seleccione un grupo existente. Para obtener más información, consulte [Uso de grupos de recursos para administrar los recursos de Azure](resource-group-portal.md).
    * Use **Ubicación** para especificar la ubicación geográfica en la que se hospedará el centro de IoT.  


4. Una vez configuradas las opciones del nuevo centro de IoT, haga clic en **Crear**. La creación del centro de IoT puede tardar unos minutos. Para comprobar el estado, puede supervisar el progreso en el panel de inicio. O bien, puede supervisar su progreso desde la sección Notificaciones.

    ![][3]


5. Una vez creado correctamente el centro de IoT, abra la hoja del nuevo centro de IoT, tome nota del URI y seleccione el icono **Clave** en la parte superior.

   	![][4]

6. Seleccione la directiva de acceso compartido denominada **iothubowner** y, a continuación, copie y anote la cadena de conexión en la hoja de la derecha.

   	![][5]

El centro de IoT se creará y tendrá la cadena de conexión y el URI que necesita para completar este tutorial.

[AZURE.INCLUDE [iot-hub-get-started-cloud-csharp](../../includes/iot-hub-get-started-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-csharp](../../includes/iot-hub-get-started-device-csharp.md)]

## Ejecución de las aplicaciones

Ya está preparado para ejecutar las aplicaciones.

1.	Desde Visual Studio, haga clic con el botón secundario del mouse en la solución y seleccione **Establecer proyectos de inicio...** Seleccione **Proyectos de inicio múltiples**, a continuación, seleccione la acción **Iniciar** para las aplicaciones **ProcessDeviceToCloudMessages** y **SimulatedDevice**.

   	![][41]

2.	Al presionar **F5**, deberían iniciarse ambas aplicaciones, la aplicación simulada debería enviar los mensajes y la aplicación del procesador debería recibir dichos mensajes.

   	![][42]

## Pasos siguientes

En este tutorial, ha configurado un nuevo centro de IoT, ha creado una identidad en el registro de la identidad del centro y ha usado dicha identidad para programar un simulado que envía mensajes de dispositivo a nube. Puede seguir explorando las características del centro de IoT y distintos escenarios con el tutorial siguiente:

- [Envío de mensajes de nube a dispositivo con el Centro de IoT][] muestra cómo enviar mensajes a dispositivos y procesar los comentarios de entrega generados por el Centro de IoT.
- [Procesamiento de mensajes de dispositivo a nube][] muestra cómo procesar de forma fiable la telemetría y los mensajes interactivos procedentes de los dispositivos.
- [Carga de archivos desde dispositivos][], describe un patrón que usa los mensajes de nube a dispositivo para facilitar la carga de archivos desde los dispositivos.

Información adicional sobre el Centro de IoT:

* [Información general sobre el Centro de IoT][]
* [Guía para desarrolladores del Centro de IoT][]
* [Directrices sobre el Centro de IoT][]
* [Lenguajes y plataformas de dispositivos compatibles][Supported devices]
* [Centro para desarrolladores de IoT de Azure][]

<!-- Images. -->
[1]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub5.png

[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png

<!-- Links -->
[Portal de Azure]: https://portal.azure.com/

[Envío de mensajes de nube a dispositivo con el Centro de IoT]: iot-hub-csharp-csharp-c2d.md
[Procesamiento de mensajes de dispositivo a nube]: iot-hub-csharp-csharp-process-d2c.md
[Carga de archivos desde dispositivos]: iot-hub-csharp-csharp-file-upload.md

[Información general sobre el Centro de IoT]: iot-hub-what-is-iot-hub.md
[Directrices sobre el Centro de IoT]: iot-hub-guidance.md
[Guía para desarrolladores del Centro de IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centro para desarrolladores de IoT de Azure]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_1203_2015-->