## <a name="prepare-your-raspberry-pi"></a>Preparación de su Raspberry Pi

### <a name="install-raspbian"></a>Instalación de Raspbian

Si es la primera vez que usa su Raspberry Pi, debe instalar el sistema operativo Raspbian con NOOBS, que se encuentra en la tarjeta SD incluida en el kit. En la [guía de software de Raspberry Pi][lnk-install-raspbian], se describe cómo instalar un sistema operativo en Raspberry Pi. En este tutorial se da por supuesto que ha instalado el sistema operativo Raspbian en su Raspberry Pi.

> [!NOTE]
> La tarjeta SD incluida en el [kit de inicio de Microsoft Azure IoT para Raspberry Pi 3][lnk-starter-kits] ya tiene NOOBS instalado. Puede iniciar Raspberry Pi desde esta tarjeta y elegir instalar el sistema operativo Raspbian.

### <a name="set-up-the-hardware"></a>Configuración del hardware

Este tutorial utiliza el sensor de BME280 incluido en el [Kit de inicio de Microsoft Azure IoT para Raspberry Pi 3][lnk-starter-kits] para generar datos de telemetría. Usa un LED para indicar si Raspberry Pi procesa una invocación de método desde el panel de soluciones.

Los componentes en la placa de pruebas son:

- LED rojo
- Resistencia de 220 ohmios (rojo, rojo, marrón)
- Sensor de BME280

En el diagrama siguiente se muestra cómo conectar el hardware:

![Configuración de hardware para Raspberry Pi][img-connection-diagram]

En la tabla siguiente se resumen las conexiones entre Raspberry Pi y los componentes en la placa de pruebas:

| Raspberry Pi            | Placa de pruebas             |Color         |
| ----------------------- | ---------------------- | ------------- |
| GND (patilla 14)            | Patilla -ve de LED (18A)      | Púrpura          |
| GPCLK0 (patilla 7)          | Resistencia (25A)         | Naranja          |
| SPI_CE0 (patilla 24)        | CS (39A)               | Azul          |
| SPI_SCLK (patilla 23)       | SCK (36A)              | Amarillo        |
| SPI_MISO (patilla 21)       | SDO (37A)              | Blanco         |
| SPI_MOSI (patilla 19)       | SDI (38A)              | Verde         |
| GND (patilla 6)             | GND (35A)              | Negro         |
| 3.3 V (patilla 1)           | 3Vo (34A)              | Rojo           |

Para completar la configuración de hardware, debe:

- Conectar su Raspberry Pi a la fuente de alimentación que se incluye en el kit.
- Conectar su Raspberry Pi a la red mediante el cable Ethernet incluido en el kit. Como alternativa, puede configurar [Conectividad inalámbrica][lnk-pi-wireless] para su Raspberry Pi.

Ahora ha completado la configuración de hardware de su Raspberry Pi.

### <a name="sign-in-and-access-the-terminal"></a>Inicio de sesión y acceso al terminal

Dispone de dos opciones para acceder a un entorno de terminal en su Raspberry Pi:

- Si tiene un teclado y un monitor conectados a su Raspberry Pi, puede usar la GUI de Raspbian para acceder a una ventana de terminal.

- Acceda a la línea de comandos en su Raspberry Pi mediante SSH desde la máquina de escritorio.

#### <a name="use-a-terminal-window-in-the-gui"></a>Uso de una ventana de terminal en la GUI

Las credenciales predeterminadas para Raspbian son el nombre de usuario **pi** y la contraseña **raspberry**. En la barra de tareas en la GUI, puede iniciar la utilidad **Terminal** mediante el icono que parece un monitor.

#### <a name="sign-in-with-ssh"></a>Inicio de sesión con SSH

Puede usar SSH para el acceso de línea de comandos a su Raspberry Pi. En el artículo [SSH (Secure Shell)][lnk-pi-ssh], se describe cómo configurar SSH en Raspberry Pi y cómo conectarse desde [Windows][lnk-ssh-windows] o [Linux y Mac OS][lnk-ssh-linux].

Inicie sesión con el nombre de usuario **pi** y la contraseña **raspberry**.

#### <a name="optional-share-a-folder-on-your-raspberry-pi"></a>Opcional: Compartir una carpeta en su Raspberry Pi

Si lo desea, puede compartir una carpeta en su Raspberry Pi con el entorno de escritorio. Compartir una carpeta le permite usar el editor de texto de escritorio preferido (como [Visual Studio Code](https://code.visualstudio.com/) o [Sublime Text](http://www.sublimetext.com/)) para editar archivos en su Raspberry Pi en lugar de usar `nano` o `vi`.

Para compartir una carpeta con Windows, configure un servidor Samba en Raspberry Pi. Como alternativa, use el servidor [SFTP](https://www.raspberrypi.org/documentation/remote-access/) integrado con un cliente SFTP en el escritorio.

### <a name="enable-spi"></a>Habilitación de SPI

Para poder ejecutar la aplicación de ejemplo, debe habilitar el bus de interfaz de periféricos en serie (SPI) en Raspberry Pi. Raspberry Pi se comunica con el dispositivo de sensor de BME280 a través del bus SPI. Use el comando siguiente para editar el archivo de configuración:

```sh
sudo nano /boot/config.txt
```

Busque la línea:

`#dtparam=spi=on`

- Para quitar la marca de comentario de la línea, elimine `#` al principio.
- Guarde los cambios (**Ctrl-O**, **ENTRAR**) y salga del editor (**Ctrl-X**).
- Para habilitar SPI, reinicie Raspberry Pi. Al reiniciarlo, se desconecta el terminal. Debe iniciar sesión de nuevo cuando se reinicia Raspberry Pi:

  ```sh
  sudo reboot
  ```


[img-connection-diagram]: media/iot-suite-raspberry-pi-kit-prepare-pi/rpi2_remote_monitoring.png

[lnk-install-raspbian]: https://www.raspberrypi.org/learning/software-guide/quickstart/
[lnk-pi-wireless]: https://www.raspberrypi.org/documentation/configuration/wireless/README.md
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/