## <a name="view-the-solution-dashboard"></a>Visualización del panel de soluciones

El panel de la solución permite administrar la solución implementada. Por ejemplo, puede ver la telemetría, agregar dispositivos e invocar métodos.

1. Cuando se haya completado el aprovisionamiento y el icono de la solución preconfigurada indique **Listo**, seleccione **Iniciar** para abrir el portal de la solución de supervisión remota en una nueva pestaña.

    ![Iniciar la solución preconfigurada][img-launch-solution]

1. De forma predeterminada, el portal de la solución muestra el *panel*. Puede navegar a otras áreas del portal de solución mediante el menú en el lado izquierdo de la página.

    ![Panel de la solución preconfigurada de supervisión remota][img-menu]

## <a name="add-a-device"></a>Agregar un dispositivo

Para que un dispositivo se conecte a la solución preconfigurada, debe identificarse en Azure IoT Hub con credenciales válidas. Puede recuperar las credenciales del dispositivo desde el panel de la solución. Incluirá las credenciales del dispositivo en la aplicación de cliente más adelante en este tutorial.

Si aún no lo ha hecho, agregue un dispositivo personalizado a la solución de supervisión remota. Complete los pasos siguientes en el panel de soluciones:

1. En la esquina inferior izquierda del panel, haga clic en **Agregar un dispositivo**.

   ![Agregar un dispositivo][1]

1. En el panel **Dispositivo personalizado**, haga clic en **Agregar nuevo**.

   ![Agregar un dispositivo personalizado][2]

1. Elija **Permitirme definir mi propio id. de dispositivo**. Escriba un id. de dispositivo como **rasppi**, haga clic en **Comprobar id.** para comprobar que no haya usado ya el nombre en la solución y haga clic en **Crear** para aprovisionar el dispositivo.

   ![Agregar id. de dispositivo][3]

1. Anote las credenciales de dispositivo (**Id. de dispositivo**, **Nombre de host de IoT Hub** y **Clave de dispositivo**). La aplicación cliente en su Raspberry Pi necesita estos valores para conectarse con la solución de supervisión remota. A continuación, haga clic en **Hecho**.

    ![Ver las credenciales del dispositivo][4]

1. Seleccione el dispositivo en la lista de dispositivos del panel de la solución. Luego, en el panel **Detalles del dispositivo**, haga clic en **Habilitar dispositivo**. El estado del dispositivo ahora es **En ejecución**. La solución de supervisión remota ahora puede recibir telemetría desde el dispositivo e invocar métodos en el dispositivo.

[img-launch-solution]: media/iot-suite-v1-raspberry-pi-kit-view-solution/launch.png
[img-menu]: media/iot-suite-v1-raspberry-pi-kit-view-solution/menu.png
[1]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite0.png
[2]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite1.png
[3]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite2.png
[4]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite3.png