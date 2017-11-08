## <a name="view-device-telemetry"></a>Ver la telemetría de dispositivo

Puede ver la telemetría enviada desde el dispositivo en la página **Dispositivos** de la solución.

1. Seleccione el dispositivo que aprovisionó en la lista de dispositivos de la página **Dispositivos**. Un panel muestra información sobre el dispositivo incluyendo un trazado de su telemetría:

    ![Ver detalles del dispositivo](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Elija **Presión** para cambiar la presentación de telemetría:

    ![Ver la telemetría de presión](media/iot-suite-visualize-connecting/devicespressure.png)

1. Para ver información de diagnóstico sobre el dispositivo, desplácese hacia abajo hasta **Diagnósticos**:

    ![Ver el diagnóstico de los dispositivos](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Actúe en su dispositivo

Para invocar métodos en los dispositivos, use la página **Dispositivos** de la solución de supervisión remota. Por ejemplo, en los dispositivos **Chiller** de la solución de supervisión remota, implemente un método **Reboot**.

1. Elija **Dispositivos** para ir hasta la página **Dispositivos** de la solución.

1. Seleccione el dispositivo que aprovisionó en la lista de dispositivos de la página **Dispositivos**:

    ![Seleccione el dispositivo físico](media/iot-suite-visualize-connecting/devicesselect.png)

1. Para mostrar una lista de los métodos se pueden llamar en su dispositivo, elija **Programación**. Para programar un método para ejecutarse en varios dispositivos, puede seleccionar varios en la lista. El panel **Programación** muestra los tipos de método comunes a todos los dispositivos seleccionados.

1. Elija **Reboot**, establezca el nombre del trabajo en **RebootPhysicalChiller** y elija **Aplicar**:

    ![Programe el reinicio](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Aparece un mensaje en la consola donde se ejecuta el código del dispositivo cuando el dispositivo controla el método.

> [!NOTE]
> Para realizar un seguimiento del estado del trabajo en la solución, elija **Vista**.

## <a name="next-steps"></a>Pasos siguientes

En el artículo [Personalización de la solución preconfigurada de supervisión remota](../articles/iot-suite/iot-suite-remote-monitoring-customize.md) se describen algunas formas de personalizar la solución preconfigurada.