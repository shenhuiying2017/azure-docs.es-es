## <a name="prepare-your-intel-nuc"></a>Preparación de Intel NUC

Para completar la configuración de hardware, debe:

- Conectar su Intel NUC a la fuente de alimentación que se incluye en el kit.
- Conecte su Intel NUC a la red mediante un cable Ethernet.

Tras ello, habrá completado la configuración de hardware de su dispositivo de puerta de enlace Intel NUC.

### <a name="sign-in-and-access-the-terminal"></a>Inicio de sesión y acceso al terminal

Dispone de dos opciones para acceder a un entorno de terminal en su Intel NUC:

- Si tiene un teclado y un monitor conectado a su Intel NUC, puede tener acceso directamente al shell. Las credenciales predeterminadas son el nombre de usuario **root** y la contraseña **root**.

- Acceda al shell en su Intel NUC mediante SSH desde el equipo de escritorio.

#### <a name="sign-in-with-ssh"></a>Inicio de sesión con SSH

Para iniciar sesión con SSH, necesitará la dirección IP de su Intel NUC. Si tiene un teclado y un monitor conectado a su Intel NUC, use el comando `ifconfig` para obtener la dirección IP. También puede conectarse a su enrutador para mostrar las direcciones de los dispositivos de la red.

Inicie sesión con el nombre de usuario **root** y la contraseña **root**.

#### <a name="optional-share-a-folder-on-your-intel-nuc"></a>Opcional: Compartir una carpeta en su Intel NUC

Si lo desea, puede compartir una carpeta en su Intel NUC con el entorno de escritorio. Compartir una carpeta le permite usar el editor de texto de escritorio preferido (como [Visual Studio Code](https://code.visualstudio.com/) o [Sublime Text](http://www.sublimetext.com/)) para editar archivos en su Intel NUC en lugar de usar `nano` o `vi`.

Para compartir una carpeta con Windows, configure un servidor Samba en Intel NUC. Como alternativa, use el servidor SFTP en Intel NUC con un cliente SFTP en la máquina de escritorio.
