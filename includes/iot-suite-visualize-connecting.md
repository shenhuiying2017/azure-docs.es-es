## <a name="view-device-telemetry-in-the-dashboard"></a>Ver la telemetría de dispositivo en el panel
El panel de la solución de supervisión remota permite ver la telemetría que los dispositivos envían al Centro de IoT.

1. En el explorador, vuelva al panel de la solución de supervisión remota, haga clic en **Dispositivos** en el panel izquierdo para navegar hasta la **lista de dispositivos**.
2. En la **lista de dispositivos**, debería ver que el estado del dispositivo es **En ejecución**.
   
    ![][18]
3. Haga clic en el **Panel** para volver a él, seleccione el dispositivo en la lista desplegable **Device to View** (Dispositivo para ver) para ver su telemetría. La telemetría de la aplicación de ejemplo es 50 unidades para la temperatura interior, 55 unidades para la temperatura exterior y 50 unidades para la humedad. Tenga en cuenta que, de forma predeterminada, en el panel solo se muestran los valores de temperatura y humedad.
   
    ![][img-telemetry]

## <a name="send-a-command-to-your-device"></a>Envío de un comando a su dispositivo
El panel de la solución de supervisión remota le permite enviar comandos a sus dispositivos a través del Centro de IoT. Por ejemplo, en la solución de supervisión remota puede enviar un comando para establecer la temperatura interna de un dispositivo.

1. En el panel de la solución de supervisión remota, haga clic en **Devices** (Dispositivos) en el panel izquierdo para navegar a la **lista de dispositivos**.
2. Haga clic en **Device ID** (Id. de dispositivo) en el dispositivo de la **lista de dispositivos**.
3. En el panel **Device details** (Detalles del dispositivo), haga clic en **Commands** (Comandos).
   
    ![][13]
4. En la lista desplegable **Command** (Comando), seleccione **SetTemperature** y, en **Temperature** (Temperatura), escriba un nuevo valor de temperatura. Haga clic en **Enviar comando** para enviar el comando al dispositivo.
   
    ![][14]
   
   > [!NOTE]
   > Inicialmente, el historial de comandos muestra el estado del comando como **Pendiente**. Cuando el dispositivo reconoce el comando, el estado cambia a **Correcto**.
   > 
   > 
5. En el panel, compruebe que el dispositivo está enviando ahora 75 como el nuevo valor de temperatura.

## <a name="next-steps"></a>Pasos siguientes
En el artículo [Personalización de soluciones preconfiguradas][lnk-customize] se describen varias formas de ampliar este ejemplo. Entre las posibles extensiones se incluyen el uso de sensores reales y la implementación de comandos adicionales.

Puede aprender más sobre los [permisos en el sitio de azureiotsuite.com][lnk-permissions].

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-permissions.md


<!--HONumber=Nov16_HO3-->


