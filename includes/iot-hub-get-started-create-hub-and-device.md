## <a name="create-an-iot-hub"></a>Crear un centro de IoT

1. En [Azure Portal](https://portal.azure.com/), haga clic en **Nuevo** > **Internet de las cosas** > **IoT Hub**.

   ![Creación de un centro de IoT mediante Azure Portal](../articles/iot-hub/media/iot-hub-create-hub-and-device/1_create-azure-iot-hub-portal.png)
1. En el panel **Centro de IoT**, escriba la información necesaria para su centro de IoT:

   **Nombre**: el nombre de su IoT Hub. Si el nombre que escribe es válido, aparece una marca de verificación verde.

   **Nivel de precios y de escala**: seleccione el nivel F1 gratis. Esta opción es suficiente para esta demostración. Seleccione un [nivel de precios y de escala](https://azure.microsoft.com/pricing/details/iot-hub/).

   **Grupo de recursos**: cree un grupo de recursos para hospedar el centro de IoT o use uno existente. Consulte [Uso de grupos de recursos para administrar los recursos de Azure](../articles/azure-resource-manager/resource-group-portal.md).

   **Ubicación**: seleccione la ubicación más cercana a usted donde se crea el centro de IoT.

   **Anclar al panel**: marque esta opción para facilitar el acceso al centro de IoT desde el panel.

   ![Rellenar los campos para crear el centro de Azure IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/2_fill-in-fields-for-azure-iot-hub-portal.png)

1. Haga clic en **Crear**. La creación del centro de IoT puede tardar unos minutos. Puede ver el progreso en el panel **Notificaciones**.

   ![Ver las notificaciones de su progreso de creación del centro de IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/3_notification-azure-iot-hub-creation-progress-portal.png)

1. Después de crear el centro de IoT, haga clic en él desde el panel. Anote el **Nombre de host** y haga clic en **Directivas de acceso compartido**.

   ![Obtener el nombre de host de su centro de IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. En el panel **Directivas de acceso compartido**, haga clic en la directiva **iothubowner** y, luego, copie y anote la **cadena de conexión** de su centro de IoT. Para más información, consulte [Control del acceso a IoT Hub](../articles/iot-hub/iot-hub-devguide-security.md).

   ![Obtener la cadena de conexión del centro de IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-the-your-device"></a>Registrar un dispositivo en el centro de IoT

1. En el [portal de Azure](https://portal.azure.com/), abra su centro de IoT.
1. Haga clic en **Explorador de dispositivos**.
1. En el panel Explorador del dispositivo, haga clic en **Agregar** para agregar un dispositivo a su centro de IoT.

   **Id. de dispositivo**: el identificador del nuevo dispositivo. Los identificadores de dispositivos distinguen mayúsculas de minúsculas.

   **Tipo de autenticación**: seleccione **Clave simétrica**.

   **Auto Generate Keys** (Generar claves automáticamente): active este campo.

   **Connect device to IoT Hub** (Conectar dispositivos a IoT Hub): haga clic en **Habilitar**.

   ![Agregar un dispositivo en el Explorador de dispositivos de su centro de IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

1. Haga clic en **Guardar**.
1. Después de crear el dispositivo, ábralo en el panel **Explorador de dispositivos**.
1. Anote la clave principal de la cadena de la cadena de conexión.

   ![Obtener la cadena de conexión del dispositivo](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)