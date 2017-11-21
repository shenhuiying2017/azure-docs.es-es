## <a name="view-the-telemetry"></a>Visualización de la telemetría

Raspberry Pi ahora está enviando telemetría a la solución de supervisión remota. Puede verla en el panel de soluciones. También puede enviar mensajes a su Raspberry Pi desde el panel de soluciones.

- Vaya al panel de soluciones.
- Seleccione el dispositivo en la lista desplegable **Dispositivo que se visualizará**.
- La telemetría de Raspberry Pi se muestra en el panel.

![Mostrar telemetría desde Raspberry Pi][img-telemetry-display]

## <a name="act-on-the-device"></a>Actuación en el dispositivo

En el panel de soluciones, puede invocar métodos en su Raspberry Pi. Cuando Raspberry Pi se conecta a la solución de supervisión remota, envía información acerca de los métodos que admite.

- En el panel de soluciones, haga clic en **Dispositivos** para visitar la página **Dispositivos**. Seleccione su Raspberry Pi en la **lista de dispositivos**. A continuación, elija **Métodos**:

    ![Lista de dispositivos en el panel][img-list-devices]

- En la página **Invocar método**, elija **LightBlink** en la lista desplegable **Método**.

- Elija **Invocar método**. El LED conectado a Raspberry Pi parpadea varias veces. La aplicación en Raspberry Pi envía una confirmación de vuelta al panel de soluciones:

    ![Mostrar el historial de métodos][img-method-history]

- Puede activar el LED y desactivarlo mediante el método **ChangeLightStatus** con un valor de **LightStatusValue** establecido en **1** para activado o **0** para desactivado.

> [!WARNING]
> Si deja la solución de supervisión remota ejecutándose en su cuenta de Azure, se le cobra por el tiempo que se ejecute. Para más información sobre cómo reducir el consumo mientras se ejecuta la solución de supervisión remota, consulte [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configuración de soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure para fines de demostración). Elimine la solución preconfigurada de su cuenta de Azure cuando haya terminado de usarla.


[img-telemetry-display]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/telemetry.png
[img-list-devices]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/listdevices.png
[img-method-history]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md