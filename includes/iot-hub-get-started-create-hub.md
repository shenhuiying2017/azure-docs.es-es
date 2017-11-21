## <a name="create-an-iot-hub"></a>Crear un centro de IoT
Cree un Centro de IoT al que se pueda conectar la aplicación de dispositivo simulado. En los siguientes pasos se muestra cómo completar esta tarea mediante el Portal de Azure.

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Ahora que ha creado un centro de IoT, busque la información importante que usa para conectar a él dispositivos y aplicaciones. 

1. Cuando se haya creado correctamente el centro de IoT, haga clic en el nuevo icono del centro de IoT de Azure Portal para abrir la ventana de propiedades del mismo. Anote el **Nombre de host** y haga clic en **Directivas de acceso compartido**.
   
    ![Ventana del nuevo centro de IoT][4]
1. En **Directivas de acceso compartido**, haga clic en la directiva **iothubowner** y copie y anote la cadena de conexión de IoT Hub en la ventana **iothubowner**. Consulte [Control de acceso][lnk-access-control] en la Guía del desarrollador de Azure IoT Hub para más información.
   
    ![Directivas de acceso compartido][5]

<!-- Images. -->
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
