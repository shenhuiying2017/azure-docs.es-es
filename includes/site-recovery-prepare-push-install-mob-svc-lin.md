### <a name="prepare-for-push-install-on-linux-servers"></a>Preparación para la instalación de inserción en servidores Linux

1. Asegúrese de que haya conectividad de red entre la máquina de Linux y el servidor de procesos.
2. Cree una cuenta que el servidor de procesos pueda utilizar para tener acceso a la máquina. La cuenta debe ser un usuario **raíz** en el servidor de origen de Linux() y solo se utiliza para la instalación de inserción y las actualizaciones.
3. Compruebe que el archivo `/etc/hosts` del servidor Linux de origen contiene entradas que asignan el nombre de host local a las direcciones IP asociadas con todos los adaptadores de red.
4. Instale los paquetes openssh, openssh-server, openssl más recientes en la máquina que desea replicar.
5. Asegúrese de que SSH está habilitado y ejecutándose en el puerto 22.
6. Habilite la autenticación de la contraseña y del subsistema SFTP en el archivo sshd_config:
  - Inicie sesión como **root**.
  - En el archivo /etc/ssh/sshd_config, encuentre la línea que comienza con **PasswordAuthentication**.
  - Quite la marca de comentario de la línea y cambie el valor de **no** a **yes**.
   Busque la línea que comienza por **Subsystem** y quite la marca de comentario.

     ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

7. Agregue la cuenta que creó en la herramienta CSPSConfigtool.

    - Inicie sesión en el servidor de configuración.
    - Abra **cspsconfigtool.exe**. (Esta herramienta está disponible como acceso directo en el escritorio y se encuentra en la carpeta %ProgramData%\home\svsystems\bin)
    - En la pestaña **Administrar cuentas**, haga clic en **Agregar cuenta**.
    - Agregue la cuenta que creó. Después de agregar la cuenta, tiene que proporcionar las credenciales para habilitar la replicación de una máquina.


<!--HONumber=Jan17_HO3-->


