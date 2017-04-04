## <a name="create-an-iot-hub"></a>Crear un centro de IoT
Cree un Centro de IoT al que se pueda conectar la aplicación de dispositivo simulado. En los siguientes pasos se muestra cómo completar esta tarea mediante el Portal de Azure.

1. Inicie sesión en [Azure Portal][lnk-portal].
1. En la barra de salto, haga clic en **Nuevo** > **Internet de las cosas** > **IoT Hub**.
   
    ![Barra de accesos del Portal de Azure][1]
1. En la hoja **Centro de IoT** , elija la configuración para su Centro de IoT.
   
    ![Hoja del Centro de IoT][2]
   
   1. En el cuadro **Nombre** , escriba un nombre para identificar el Centro de IoT. Si el **Nombre** es válido y está disponible, aparecerá una marca de verificación verde en el cuadro **Nombre**.
   1. Seleccione un [plan de tarifa y escalado][lnk-pricing]. Este tutorial no requiere ningún nivel determinado. Para este tutorial, use el nivel F1 gratis.
   1. En **Grupo de recursos**, cree un grupo de recursos o seleccione uno existente. Para más información, consulte [Using resource groups to manage your Azure resources][lnk-resource-groups] (Uso de grupos de recursos para administrar los recursos de Azure).
   1. En **Ubicación**, seleccione la ubicación para hospedar su Centro de IoT. Para este tutorial, elija la ubicación más cercana.
1. Cuando haya elegido las opciones de configuración del Centro de IoT, haga clic en **Crear**.  La creación del Centro de IoT puede tardar unos minutos. Para comprobar el estado, puede supervisar el progreso en el panel de inicio o en el panel de notificaciones.
   
    ![Estado del nuevo Centro de IoT][3]
1. Cuando se haya creado correctamente el centro de IoT, haga clic en el icono Nuevo del centro de IoT en Azure Portal para abrir la hoja del nuevo centro de IoT. Anote el **Nombre de host** y haga clic en **Directivas de acceso compartido**.
   
    ![Nueva hoja del Centro de IoT][4]
1. En la hoja **Directivas de acceso compartido**, haga clic en la directiva **iothubowner** y copie y anote la cadena de conexión de IoT Hub en la hoja **iothubowner**. Consulte [Control de acceso][lnk-access-control] en la Guía del desarrollador de Azure IoT Hub para más información.
   
    ![Hoja de directivas de acceso compartido][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
