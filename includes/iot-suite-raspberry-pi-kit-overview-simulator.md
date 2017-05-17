## <a name="overview"></a>Información general

En este tutorial, va a completar los siguientes pasos:

- Implemente una instancia de la solución preconfigurada de supervisión remota en su suscripción de Azure. Este paso implementa y configura varios servicios de Azure automáticamente.
- Configure el dispositivo para que se comunique con el equipo y la solución de supervisión remota.
- Actualice el código del dispositivo de ejemplo para que se conecte a la solución de supervisión remota y envíe telemetría simulada que aparezca en el panel de soluciones.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, deberá tener una suscripción activa de Azure.

> [!NOTE]
> En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte la [evaluación gratuita de Azure][lnk-free-trial].

### <a name="required-software"></a>Requisitos de software

Necesita el cliente SSH en su máquina de escritorio para poder acceder de forma remota a la línea de comandos en su Raspberry Pi.

- Windows no incluye ningún cliente SSH. Se recomienda usar [PuTTY](http://www.putty.org/).
- La mayoría de las distribuciones de Linux y Mac OS incluyen la utilidad de línea de comandos de SSH. Para más información, consulte [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md) (SSH cuando se usa Linux o Mac OS).

### <a name="required-hardware"></a>Requisitos de hardware

Un equipo de escritorio que permita conectarse remotamente a la línea de comandos en Raspberry Pi.

[Kit de inicio de Microsoft IoT para Raspberry Pi 3][lnk-starter-kits] o componentes equivalentes. En este tutorial se usan los siguientes elementos del kit:

- Raspberry Pi 3
- Tarjeta MicroSD (con NOOBS)
- Un cable USB mini
- Un cable Ethernet

[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/