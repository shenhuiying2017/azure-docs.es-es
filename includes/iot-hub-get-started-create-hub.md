## <a name="create-an-iot-hub"></a>Crear un centro de IoT
Cree un Centro de IoT al que se pueda conectar la aplicación de dispositivo simulado. En los siguientes pasos se muestra cómo completar esta tarea mediante el Portal de Azure.

1. Inicie sesión en [Azure Portal][lnk-portal].

1. Haga clic en **Nuevo** > **Internet de las cosas** > **IoT Hub**.
   
    ![Barra de accesos del Portal de Azure][1]

1. En el panel **Centro de IoT**, escriba la información necesaria para su centro de IoT:

   * **Nombre**: cree un nombre para su centro de IoT. Si el nombre que escribe es válido, aparece una marca de verificación verde.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   * **Nivel de precios y de escala**: en este tutorial, seleccione el nivel **F1 - Gratis**. Para más información, consulte [Nivel de precios y de escala][lnk-pricing].

   * **Grupo de recursos**: cree un grupo de recursos para hospedar el centro de IoT o use uno existente. Para más información, consulte [Uso de grupos de recursos para administrar recursos de Azure][lnk-resource-groups].

   * **Ubicación**: seleccione la ubicación más cercana a usted.

   * **Anclar al panel**: active esta opción para facilitar el acceso al IoT Hub desde el panel.

    ![Ventana Centro de IoT][2]

1. Haga clic en **Create**(Crear). El centro de IoT puede tardar varios minutos en crearse. Puede ver el progreso en el panel **Notificaciones**.

1. Cuando se haya creado correctamente el centro de IoT, haga clic en el nuevo icono del centro de IoT de Azure Portal para abrir la ventana de propiedades del mismo. Ahora que ha creado un centro de IoT, busque la información importante que usa para conectar a él dispositivos y aplicaciones. Anote el **Nombre de host** y haga clic en **Directivas de acceso compartido**.
   
    ![Ventana del nuevo centro de IoT][4]

1. En **Directivas de acceso compartido**, haga clic en la directiva **iothubowner** y copie y anote la cadena de conexión de IoT Hub en la ventana **iothubowner**. Consulte [Control de acceso][lnk-access-control] en la Guía del desarrollador de Azure IoT Hub para más información.
   
    ![Directivas de acceso compartido][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
