## <a name="view-device-telemetry-in-the-dashboard"></a>Ver la telemetría de dispositivo en el panel
El panel de la solución de supervisión remota permite ver la telemetría que los dispositivos envían a IoT Hub.

1. En el explorador, vuelva al panel de la solución de supervisión remota, haga clic en **Dispositivos** en el panel izquierdo para navegar hasta la **lista de dispositivos**.
2. En la **lista de dispositivos**, debería ver que el estado del dispositivo es **En ejecución**. Si no es así, haga clic en **Habilitar dispositivo** en el panel **Detalles del dispositivo**.
   
    ![Ver el estado del dispositivo][18]
3. Haga clic en el **Panel** para volver a él, seleccione el dispositivo en la lista desplegable **Device to View** (Dispositivo para ver) para ver su telemetría. La telemetría de la aplicación de ejemplo es 50 unidades para la temperatura interior, 55 unidades para la temperatura exterior y 50 unidades para la humedad.
   
    ![Ver la telemetría de dispositivo][img-telemetry]

## <a name="invoke-a-method-on-your-device"></a>Invocar un método en el dispositivo
El panel de la solución de supervisión remota le permite invocar métodos en los dispositivos a través de IoT Hub. Por ejemplo, en la solución de supervisión remota, puede invocar un método para simular que se reinicia un dispositivo.

1. En el panel de la solución de supervisión remota, haga clic en **Devices** (Dispositivos) en el panel izquierdo para navegar a la **lista de dispositivos**.
2. Haga clic en **Device ID** (Id. de dispositivo) en el dispositivo de la **lista de dispositivos**.
3. En el panel **Detalles del dispositivo**, haga clic en **Métodos**.
   
    ![Métodos de dispositivo][13]
4. En el menú desplegable **Método**, seleccione **InitiateFirmwareUpdate** y, luego, en **FWPACKAGEURI**, escriba una dirección URL ficticia. Haga clic en **Invocar método** para llamar al método en el dispositivo.
   
    ![Invocar un método de dispositivo][14]
   

5. Puede ver un mensaje en la consola donde se ejecuta el código del dispositivo cuando el dispositivo controla el método. Los resultados del método se agregan al historial en el portal de la solución:

    ![Ver el historial de métodos][img-method-history]

## <a name="next-steps"></a>Pasos siguientes
En el artículo [Personalización de soluciones preconfiguradas][lnk-customize] se describen algunas formas de ampliar este ejemplo. Entre las posibles extensiones se incluyen el uso de sensores reales y la implementación de comandos adicionales.

Puede obtener más información sobre los [permisos en el sitio azureiotsuite.com][lnk-permissions].

[13]: ./media/iot-suite-v1-visualize-connecting/suite4.png
[14]: ./media/iot-suite-v1-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-v1-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-v1-visualize-connecting/telemetry.png
[img-method-history]: ./media/iot-suite-v1-visualize-connecting/history.png
[lnk-customize]: ../articles/iot-suite/iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-v1-permissions.md
