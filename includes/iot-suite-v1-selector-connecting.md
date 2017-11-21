> [!div class="op_single_selector"]
> * [C en Windows](../articles/iot-suite/iot-suite-v1-connecting-devices.md)
> * [C en Linux](../articles/iot-suite/iot-suite-v1-connecting-devices-linux.md)
> * [Node.js](../articles/iot-suite/iot-suite-v1-connecting-devices-node.md)
> 
> 

## <a name="scenario-overview"></a>Información general de escenario
En este escenario, creará un dispositivo que envía la siguiente telemetría a la [solución preconfigurada][lnk-what-are-preconfig-solutions] de supervisión remota:

* Temperatura exterior
* Temperatura interior
* Humedad

Para simplificar, el código del dispositivo genera valores de ejemplo, pero le recomendamos que amplíe el ejemplo conectando sensores reales a su dispositivo y enviando telemetría real.

El dispositivo también puede responder a los métodos que se invocan desde el panel de la solución y los valores de propiedades deseadas establecidos en el panel de la solución.

Para completar este tutorial, deberá tener una cuenta activa de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte la [evaluación gratuita de Azure][lnk-free-trial].

## <a name="before-you-start"></a>Antes de comenzar
Antes de escribir ningún código para el dispositivo, debe aprovisionar la solución preconfigurada de supervisión remota y aprovisionar un nuevo dispositivo personalizado en esa solución.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Aprovisionar su solución preconfigurada de supervisión remota
El dispositivo que cree en este tutorial enviará datos a una instancia de la solución preconfigurada de [supervisión remota][lnk-remote-monitoring]. Si todavía no aprovisionó la solución preconfigurada de supervisión remota en su cuenta de Azure, use estos pasos:

1. En la página <https://www.azureiotsuite.com/>, haga clic en **+** para crear una solución.
2. Haga clic en **Seleccionar** en el panel de **supervisión remota** para crear la solución.
3. En la página **Create Remote monitoring solution** (Crear solución de supervisión remota), escriba el **nombre de solución** que prefiera, seleccione la **región** en la que desea realizar la implementación y seleccione la suscripción de Azure que desea usar. Haga clic en **Crear solución**.
4. Espere a que finalice el proceso de aprovisionamiento.

> [!WARNING]
> Las soluciones preconfiguradas utilizan servicios de Azure facturables. Para evitar gastos innecesarios, asegúrese de quitar la solución preconfigurada de la suscripción cuando haya terminado. Para quitar completamente una solución preconfigurada de su suscripción, diríjase a la página <https://www.azureiotsuite.com/>.
> 
> 

Cuando finalice el proceso de aprovisionamiento para la solución de supervisión remota, haga clic en **Iniciar** para abrir el panel de la solución en el explorador.

![Panel de soluciones][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Aprovisionar el dispositivo en la solución de supervisión remota
> [!NOTE]
> Si ya ha aprovisionado un dispositivo en la solución, puede omitir este paso. Debe conocer las credenciales del dispositivo cuando cree la aplicación cliente.
> 
> 

Para que un dispositivo se conecte a la solución preconfigurada, debe identificarse en Azure IoT Hub con credenciales válidas. Puede recuperar las credenciales del dispositivo desde el panel de la solución. Incluirá las credenciales del dispositivo en la aplicación de cliente más adelante en este tutorial.

Para agregar un dispositivo a su solución de supervisión remota, complete los pasos siguientes en el panel de la solución:

1. En la esquina inferior izquierda del panel, haga clic en **Agregar un dispositivo**.
   
   ![Agregar un dispositivo][1]
2. En el panel **Dispositivo personalizado**, haga clic en **Agregar nuevo**.
   
   ![Agregar un dispositivo personalizado][2]
3. Elija **Permitirme definir mi propio id. de dispositivo**. Especifique un id. de dispositivo, como **mydevice**, haga clic en **Comprobar id.** para comprobar que el nombre todavía no está en uso y, luego, haga clic en **Crear** para aprovisionar el dispositivo.
   
   ![Agregar id. de dispositivo][3]
4. Anote las credenciales de dispositivo (id. de dispositivo, nombre de host de IoT Hub y clave de dispositivo). La aplicación cliente necesita estos valores para conectarse con la solución de supervisión remota. A continuación, haga clic en **Hecho**.
   
    ![Ver las credenciales del dispositivo][4]
5. Seleccione el dispositivo en la lista de dispositivos del panel de la solución. Luego, en el panel **Detalles del dispositivo**, haga clic en **Habilitar dispositivo**. El estado del dispositivo ahora es **En ejecución**. La solución de supervisión remota ahora puede recibir telemetría desde el dispositivo e invocar métodos en el dispositivo.

[img-dashboard]: ./media/iot-suite-v1-selector-connecting/dashboard.png
[1]: ./media/iot-suite-v1-selector-connecting/suite0.png
[2]: ./media/iot-suite-v1-selector-connecting/suite1.png
[3]: ./media/iot-suite-v1-selector-connecting/suite2.png
[4]: ./media/iot-suite-v1-selector-connecting/suite3.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-v1-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/