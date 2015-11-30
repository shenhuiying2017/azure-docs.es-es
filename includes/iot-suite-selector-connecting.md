> [AZURE.SELECTOR]
- [C on Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
- [C on Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [C on mbed](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## Información general de escenario

En este escenario, creará un dispositivo que envía la siguiente telemetría a la [solución preconfigurada][lnk-what-are-preconfig-solutions] de supervisión remota:

- Temperatura exterior
- Temperatura interior
- Humedad

Para simplificar, el código del dispositivo genera valores de ejemplo, pero le recomendamos que amplíe el ejemplo conectando sensores reales a su dispositivo y enviando telemetría real.

## Antes de comenzar

Antes de escribir ningún código para el dispositivo, debe aprovisionar la solución preconfigurada de supervisión remota y luego ofrecer un dispositivo en esa solución.

### Aprovisionar su solución preconfigurada de supervisión remota

El dispositivo que crea enviará datos a una instancia de la solución preconfigurada de [supervisión remota][lnk-remote-monitoring]. Visite [Introducción al Conjunto de aplicaciones de IoT de Azure][lnk-getstarted] para crear una cuenta de Azure y aprovisionar el Conjunto de aplicaciones de IoT. Seleccione **Supervisión remota** al crear la nueva solución.

Cuando se haya aprovisionado la solución de supervisión, haga clic en **Iniciar** para abrir el panel de la solución.

![][img-dashboard]

### Aprovisionar el dispositivo en la solución de supervisión remota

> [AZURE.NOTE]Si ya ha aprovisionado un dispositivo en la solución, puede omitir este paso. Deberá conocer las credenciales del dispositivo al crear la aplicación cliente.

Para que un dispositivo se conecte a la solución preconfigurada, debe poder identificarse con credenciales válidas. Puede obtener las credenciales del dispositivo desde el panel de la solución y luego incluirlas en la aplicación cliente.

Para agregar un nuevo dispositivo a su solución de supervisión remota, complete los pasos siguientes en el panel de la solución:

1.  En la esquina inferior izquierda del panel, haga clic en **Agregar un dispositivo**.

    ![][1]

2.  En el panel **Dispositivo personalizado**, haga clic en **Agregar nuevo**.

    ![][2]

3.  Elija **Permitirme definir mi propio id. de dispositivo**, especifique un id. de dispositivo como **mydevice**, haga clic en **Comprobar id.** para comprobar que no se está usando ese nombre y luego haga clic en **Crear** para aprovisionar el dispositivo.

    ![][3]

5. Anote las credenciales del dispositivo (Id. de dispositivo, Nombre de host del Centro de IoT y Clave de dispositivo), la aplicación cliente necesitará que conecten su dispositivo a la solución de supervisión remota. Luego haga clic en **Hecho**.

    ![][4]

6. Asegúrese de que el dispositivo se muestra correctamente en la sección de dispositivos. El estado es **Pendiente** hasta que el dispositivo establezca una conexión con la solución de supervisión remota.

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-getstarted]: http://www.microsoft.com/server-cloud/internet-of-things/getting-started.aspx
[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md

<!---HONumber=Nov15_HO4-->