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

1. Haga clic en **Crear**. El centro de IoT puede tardar varios minutos en crearse. Puede ver el progreso en el panel **Notificaciones**.
<!-- Images -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md