> [!div class="op_single_selector"]
> * [C en Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [C en Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [Node.js (genérico)](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> * [Node.js en Raspberry Pi](../articles/iot-suite/iot-suite-connecting-pi-node.md)
> * [C en Raspberry Pi](../articles/iot-suite/iot-suite-connecting-pi-c.md)

En este tutorial, implementa un dispositivo **Chiller** que envía la siguiente telemetría a la [solución preconfigurada](../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md) de supervisión remota:

* Temperatura
* Presión
* Humedad

Para simplificar, el código genera valores de telemetría de ejemplo para el **Chiller**. Puede ampliar el ejemplo conectando sensores reales al dispositivo y enviando telemetría real.

El dispositivo de ejemplo también:

* Envía los metadatos a la solución para describir sus funcionalidades.
* Responde a las acciones que se desencadenan desde la página **Dispositivos** de la solución.
* Responde a los cambios de configuración que se envían desde la página **Dispositivos** de la solución.

Para completar este tutorial, deberá tener una cuenta activa de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Antes de comenzar

Antes de escribir ningún código para el dispositivo, debe aprovisionar la solución preconfigurada de supervisión remota y aprovisionar un nuevo dispositivo personalizado en esa solución.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Aprovisionar su solución preconfigurada de supervisión remota

El dispositivo **Chiller** que se crea en este tutorial envía datos a una instancia de la solución preconfigurada de [supervisión remota](../articles/iot-suite/iot-suite-remote-monitoring-explore.md). Si todavía no ha aprovisionado la solución preconfigurada de supervisión remota en su cuenta de Azure, consulte [Implementación de la solución preconfigurada de supervisión remota](../articles/iot-suite/iot-suite-remote-monitoring-deploy.md)

Cuando finalice el proceso de aprovisionamiento para la solución de supervisión remota, haga clic en **Iniciar** para abrir el panel de la solución en el explorador.

![El panel de soluciones](media/iot-suite-selector-connecting/dashboard.png)

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Aprovisionar el dispositivo en la solución de supervisión remota

> [!NOTE]
> Si ya ha aprovisionado un dispositivo en la solución, puede omitir este paso. Necesitará las credenciales del dispositivo cuando cree la aplicación cliente.

Para que un dispositivo se conecte a la solución preconfigurada, debe identificarse en Azure IoT Hub con credenciales válidas. Puede recuperar las credenciales del dispositivo desde la página **Dispositivos** de la solución. Incluirá las credenciales del dispositivo en la aplicación de cliente más adelante en este tutorial.

Para agregar un dispositivo a su solución de supervisión remota, realice los pasos siguientes en la página **Dispositivos** de la solución:

1. Elija **Aprovisionar** y, a continuación, elija **Físico** como el **Tipo de dispositivo**:

    ![Aprovisione un dispositivo físico](media/iot-suite-selector-connecting/devicesprovision.png)

1. Escriba **Refrigerador físico** como identificador de dispositivo. Elija las opciones **Clave simétrica** y **Generar claves automáticamente**:

    ![Elección de las opciones de dispositivo](media/iot-suite-selector-connecting/devicesoptions.png)

Para buscar las credenciales que el dispositivo debe usar para conectarse a la solución preconfigurada, desplácese hasta Azure Portal en el explorador. Inicie sesión en su suscripción.

1. Busque el grupo de recursos que contiene los servicios de Azure que la solución de supervisión remota utiliza. El grupo de recursos tiene el mismo nombre que la solución de supervisión remota aprovisionada.

1. Navegue hasta el centro de IoT de este grupo de recursos. A continuación, elija **dispositivos de IoT**:

    ![Explorador de dispositivos](media/iot-suite-selector-connecting/deviceexplorer.png)

1. Elija el **Id. de dispositivo** que creó en la página **Dispositivos** de la solución de supervisión remota.

1. Anote el **identificador del dispositivo** y la **clave principal**. Use estos valores al agregar código para conectar el dispositivo a la solución.

Ahora ha aprovisionado un dispositivo físico en la solución preconfigurada de supervisión remota. En las siguientes secciones, se implementa la aplicación cliente que utiliza las credenciales del dispositivo para conectarse a la solución.

La aplicación cliente implementa el modelo de dispositivo **Chiller** integrado. Un modelo de dispositivo de la solución preconfigurada especifica lo siguiente acerca de un dispositivo:

* Las propiedades que el dispositivo notifica a la solución. Por ejemplo, un dispositivo **Chiller** notifica información acerca de su ubicación y firmware.
* Los tipos de telemetría que el dispositivo envía a la solución. Por ejemplo, un dispositivo **Chiller** envía los valores de temperatura, humedad y presión.
* Los métodos que puede programar desde la solución para ejecutarse en el dispositivo. Por ejemplo, un dispositivo **Chiller** debe implementar los métodos **Reboot**, **FirmwareUpdate**, **EmergencyValveRelease** e **IncreasePressuree**.