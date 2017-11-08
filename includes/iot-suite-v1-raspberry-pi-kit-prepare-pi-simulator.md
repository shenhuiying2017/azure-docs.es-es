## <a name="prepare-your-raspberry-pi"></a>Preparación de su Raspberry Pi

### <a name="install-raspbian"></a>Instalación de Raspbian

Si es la primera vez que usa su Raspberry Pi, debe instalar el sistema operativo Raspbian con NOOBS, que se encuentra en la tarjeta SD incluida en el kit. En la [guía de software de Raspberry Pi][lnk-install-raspbian], se describe cómo instalar un sistema operativo en Raspberry Pi. En este tutorial se da por supuesto que ha instalado el sistema operativo Raspbian en su Raspberry Pi.

> [!NOTE]
> La tarjeta SD incluida en el [kit de inicio de Microsoft Azure IoT para Raspberry Pi 3][lnk-starter-kits] ya tiene NOOBS instalado. Puede iniciar Raspberry Pi desde esta tarjeta y elegir instalar el sistema operativo Raspbian.

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

[lnk-install-raspbian]: https://www.raspberrypi.org/learning/software-guide/quickstart/
[lnk-pi-wireless]: https://www.raspberrypi.org/documentation/configuration/wireless/README.md
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/