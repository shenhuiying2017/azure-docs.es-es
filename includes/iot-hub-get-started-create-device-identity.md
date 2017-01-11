## <a name="create-a-device-identity"></a>Creación de una identidad de dispositivo
En esta sección, se usa una herramienta de Node.js llamada [Explorador de IoT Hub][iot-hub-explorer] para crear una identidad de dispositivo para este tutorial.

1. En el entorno de línea de comandos, ejecute lo siguiente:
   
    npm install -g iothub-explorer@latest
2. Después, ejecute el siguiente comando para iniciar sesión en su centro, sustituyendo `{iot hub connection string}` por la cadena de conexión de IoT Hub que copió anteriormente:
   
    iothub-explorer login "{cadena de conexión de IoT Hub}"
3. Por último, cree una nueva identidad de dispositivo llamada `myDeviceId` con el comando:
   
    iothub-explorer create myDeviceId --connection-string

Anote la cadena de conexión de dispositivo del resultado. La aplicación de dispositivo usa esta cadena de conexión de dispositivo para conectarse a su instancia de IoT Hub como un dispositivo.

![][img-identity]

Consulte [Introducción a IoT Hub][lnk-getstarted] para ver cómo crear identidades de dispositivo mediante programación.

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md


<!--HONumber=Dec16_HO1-->


