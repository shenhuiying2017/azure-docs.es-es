## <a name="create-an-iot-hub"></a>Crear un centro de IoT

1. En [Azure Portal](https://portal.azure.com/), haga clic en **Nuevo** > **Internet de las cosas** > **IoT Hub**.

   ![Creación de un centro de IoT mediante Azure Portal](../articles/iot-hub/media/iot-hub-create-hub-and-device/1_create-azure-iot-hub-portal.png)
2. En el panel **Centro de IoT**, escriba la información necesaria para su centro de IoT:

     **Nombre**: escriba el nombre de su centro de IoT. Si el nombre que escribe es válido, aparece una marca de verificación verde.

     **Nivel de precios y de escala**: seleccione el nivel **F1 - Gratis**. Esta opción es suficiente para esta demostración. Para más información, consulte la página de [precios de IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

     **Grupo de recursos**: cree un grupo de recursos para hospedar el centro de IoT o use uno existente. Para más información, consulte [Administración de los recursos de Azure a través del Portal](../articles/azure-resource-manager/resource-group-portal.md).

     **Ubicación**: seleccione la ubicación más cercana a usted donde se crea el centro de IoT.

     **Anclar al panel**: seleccione esta opción para facilitar el acceso al centro de IoT desde el panel.

   ![Escribir la información necesaria para crear su centro de IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/2_fill-in-fields-for-azure-iot-hub-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

3. Haga clic en **Crear**. El centro de IoT puede tardar varios minutos en crearse. Puede ver el progreso en el panel **Notificaciones**.

   ![Ver las notificaciones de progreso para el centro de IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/3_notification-azure-iot-hub-creation-progress-portal.png)

4. Después de crear el centro de IoT, haga clic en él en el panel. Anote el **Nombre de host** y haga clic en **Directivas de acceso compartido**.

   ![Obtener el nombre de host de su centro de IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

5. En el panel **Directivas de acceso compartido**, haga clic en la directiva **iothubowner** y, luego, copie y anote la **cadena de conexión** de su centro de IoT. Para más información, consulte [Control del acceso a IoT Hub](../articles/iot-hub/iot-hub-devguide-security.md).

> [!NOTE] 
En este tutorial de configuración, no necesita esta cadena de conexión iothubowner. Sin embargo, puede necesitarla en algunos de los tutoriales en diferentes escenarios de IoT después de realizar esta configuración.

   ![Obtener la cadena de conexión del centro de IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Registro de su dispositivo en IoT Hub

1. En [Azure Portal](https://portal.azure.com/), abra su centro de IoT.

2. Haga clic en **Explorador de dispositivos**.
3. En el panel Explorador del dispositivo, haga clic en **Agregar** para agregar un dispositivo a su centro de IoT. A continuación, haga lo siguiente:

   **Id. de dispositivo**: escriba el identificador del nuevo dispositivo. Los identificadores de dispositivos distinguen mayúsculas de minúsculas.

   **Tipo de autenticación**: seleccione **Clave simétrica**.

   **Generar claves automáticamente**: active esta casilla de verificación.

   **Connect device to IoT Hub** (Conectar dispositivos a IoT Hub): haga clic en **Habilitar**.

   ![Agregar un dispositivo en Device Explorer de su centro de IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Haga clic en **Guardar**.
5. Después de crear el dispositivo, ábralo en el panel **Explorador de dispositivos**.
6. Anote la clave principal de la cadena de la cadena de conexión.

   ![Obtener la cadena de conexión del dispositivo](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)
