## <a name="create-an-iot-hub"></a>Crear un centro de IoT

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Ahora que ha creado un centro de IoT, busque la información importante que usa para conectar a él dispositivos y aplicaciones. 

1. Después de crear el centro de IoT, haga clic en él en el panel. Anote el **Nombre de host** y haga clic en **Directivas de acceso compartido**.

   ![Obtener el nombre de host de su centro de IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. En el panel **Directivas de acceso compartido**, haga clic en la directiva **iothubowner** y, luego, copie y anote la **cadena de conexión** de su centro de IoT. Para más información, consulte [Control del acceso a IoT Hub](../articles/iot-hub/iot-hub-devguide-security.md).

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
