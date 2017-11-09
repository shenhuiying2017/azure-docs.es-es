## <a name="view-the-telemetry"></a>Visualización de la telemetría

Raspberry Pi ahora está enviando telemetría a la solución de supervisión remota. Puede verla en el panel de soluciones. También puede enviar mensajes a su Raspberry Pi desde el panel de soluciones.

- Vaya al panel de soluciones.
- Seleccione el dispositivo en la lista desplegable **Dispositivo que se visualizará**.
- La telemetría de Raspberry Pi se muestra en el panel.

![Mostrar telemetría desde Raspberry Pi][img-telemetry-display]

## <a name="initiate-the-firmware-update"></a>Inicio de la actualización del firmware

El proceso de actualización del firmware descarga e instala una versión actualizada de la aplicación cliente de dispositivo en Raspberry Pi. Para más información sobre el proceso de actualización del firmware, consulte la descripción del patrón de actualización del firmware en [Overview of device management with IoT Hub][lnk-update-pattern] (Introducción a la administración de dispositivos con IoT Hub).

Inicie el proceso de actualización del firmware invocando un método en el dispositivo. Este método es asincrónico y vuelve tan pronto como comienza el proceso de actualización. El dispositivo utiliza propiedades notificadas para notificar a la solución sobre el progreso de la actualización.

Invoque métodos en su Raspberry Pi desde el panel de soluciones. La primera vez que Raspberry Pi se conecta a la solución de supervisión remota, envía información acerca de los métodos que admite. 

[img-telemetry-display]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced/telemetry.png
[lnk-update-pattern]: ../articles/iot-hub/iot-hub-device-management-overview.md
