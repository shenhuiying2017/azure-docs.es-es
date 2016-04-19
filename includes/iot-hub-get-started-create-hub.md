## Crear un centro de IoT

Debe crear un Centro de IoT al que se conectará el dispositivo simulado. En los siguientes pasos se muestra cómo completar esta tarea con el Portal de Azure.

1. Inicie sesión en el [Portal de Azure][lnk-portal].

2. En la barra de accesos directos, haga clic en **Nuevo**. A continuación, haga clic en **Internet de las cosas** y en **Centro de IoT de Azure**.

    ![][1]

3. En la hoja **Centro de IoT**, elija la configuración para su Centro de IoT.

    ![][2]

    * En el cuadro **Nombre**, escriba un nombre para identificar el Centro de IoT. Si el **Nombre** es válido y está disponible, aparecerá una marca de verificación verde en el cuadro **Nombre**.
    * Seleccione un [Plan de tarifa y escalado][lnk-pricing]. Este tutorial no requiere ningún nivel determinado. Para este tutorial, se recomienda utilizar el nivel F1 gratis.
    * En **Grupo de recursos**, cree un grupo de recursos o seleccione uno existente. Para obtener más información, consulte [Uso de grupos de recursos para administrar los recursos de Azure][lnk-resource-groups].
    * En **Ubicación**, seleccione la ubicación para hospedar su Centro de IoT. Para este tutorial, se recomienda que elija la ubicación más cercana.

4. Cuando haya elegido las opciones de configuración del Centro de IoT, haga clic en **Crear**. La creación del Centro de IoT puede tardar unos minutos. Para comprobar el estado, puede supervisar el progreso en el panel de inicio o en el panel de notificaciones.

    ![][3]

5. Cuando se haya creado correctamente el Centro de IoT, haga clic en el icono nuevo para su Centro de IoT en el portal para abrir la hoja para el nuevo Centro de IoT. Anote el **nombre de host** y haga clic en el icono **Claves**.

    ![][4]

6. En la hoja **Directiva de acceso compartido**, haga clic en la directiva **iothubowner** y copie y anote la cadena de conexión en la hoja **iothubowner**. Consulte [Control de acceso][lnk-access-control] en la Guía del desarrollador del Centro de IoT de Azure para más información.

    ![][5]


<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-portal/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide.md#accesscontrol

<!---HONumber=AcomDC_0413_2016-->