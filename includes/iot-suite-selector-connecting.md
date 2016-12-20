> [!div class="op_single_selector"]
> * [C en Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [C en Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [C en mbed](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
> * [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> 
> 

## <a name="scenario-overview"></a>Información general de escenario
En este escenario, cree un dispositivo que envíe la siguiente telemetría a la [solución preconfigurada][lnk-what-are-preconfig-solutions] de supervisión remota:

* Temperatura exterior
* Temperatura interior
* Humedad

Para simplificar, el código del dispositivo genera valores de ejemplo, pero le recomendamos que amplíe el ejemplo conectando sensores reales a su dispositivo y enviando telemetría real.

Para completar este tutorial, deberá tener una cuenta activa de Azure. En caso de no tener cuenta, puede crear una de evaluación gratuita en tan solo unos minutos. Para más información, consulte [Evaluación gratuita de Azure][lnk-free-trial].

## <a name="before-you-start"></a>Antes de comenzar
Antes de escribir ningún código para el dispositivo, debe aprovisionar la solución preconfigurada de supervisión remota y luego aprovisionar un nuevo dispositivo personalizado en esa solución.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Aprovisionar su solución preconfigurada de supervisión remota
El dispositivo que cree en este tutorial enviará datos a una instancia de la solución preconfigurada de [supervisión remota][lnk-remote-monitoring]. Si no ha suministrado ya la solución preconfigurada de supervisión remota en su cuenta de Azure, siga estos pasos:

1. En la página <https://www.azureiotsuite.com/> página, haga clic en **+** para crear una solución nueva.
2. Haga clic en **Seleccionar** en el panel **Supervisión remota** para crear la nueva solución.
3. En la página **Create Remote monitoring solution** (Crear solución de supervisión remota), escriba el **nombre de solución** que prefiera, seleccione la **región** en la que desea realizar la implementación y seleccione la suscripción de Azure que desea usar. Haga clic en **Crear solución**.
4. Espere a que finalice el proceso de aprovisionamiento.

> [!WARNING]
> Las soluciones preconfiguradas utilizan servicios de Azure facturables. Para evitar gastos innecesarios, asegúrese de quitar la solución preconfigurada de la suscripción cuando haya terminado. Para quitar completamente una solución preconfigurada de su suscripción, diríjase a la página <https://www.azureiotsuite.com/>.
> 
> 

Cuando finalice el proceso de aprovisionamiento para la solución de supervisión remota, haga clic en **Iniciar** para abrir el panel de la solución en el explorador.

![][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Aprovisionar el dispositivo en la solución de supervisión remota
> [!NOTE]
> Si ya ha aprovisionado un dispositivo en la solución, puede omitir este paso. Deberá conocer las credenciales del dispositivo al crear la aplicación cliente.
> 
> 

Para que un dispositivo se conecte a la solución preconfigurada, debe identificarse en el Centro de IoT con credenciales válidas. Puede recuperar las credenciales del dispositivo desde el panel de la solución. Incluirá las credenciales del dispositivo en la aplicación de cliente más adelante en este tutorial. 

Para agregar un nuevo dispositivo a su solución de supervisión remota, complete los pasos siguientes en el panel de la solución:

1. En la esquina inferior izquierda del panel, haga clic en **Agregar un dispositivo**.
   
   ![][1]
2. En el panel **Dispositivo personalizado**, haga clic en **Agregar nuevo**.
   
   ![][2]
3. Elija **Let me define my own Device ID** (Permitirme definir mi propio id. de dispositivo), especifique un id. de dispositivo como **mydevice**, haga clic en **Comprobar identificación** para comprobar que el nombre no está en uso y haga clic en **Crear** para aprovisionar el dispositivo.
   
   ![][3]
4. Anote las credenciales del dispositivo (Id. de dispositivo, Nombre de host del Centro de IoT y Clave de dispositivo), la aplicación cliente necesita que se conecten a la solución de supervisión remota. A continuación, haga clic en **Hecho**.
   
    ![][4]
5. Asegúrese de que el dispositivo se muestra en la sección de dispositivos. El estado es **Pendiente** hasta que el dispositivo establezca una conexión con la solución de supervisión remota.
   
    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

<!--HONumber=Nov16_HO3-->


