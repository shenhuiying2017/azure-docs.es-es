### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Preparación de una instalación de inserción en un servidor Linux

1. Asegúrese de que hay conectividad de red entre la el equipo con Linux y el servidor de procesos.
2. Cree una cuenta que el servidor de procesos pueda utilizar para acceder al equipo. La cuenta debe ser un usuario **raíz** en el servidor Linux de origen. (esta cuenta solo se usa para la instalación de inserción y para las actualizaciones).
3. Compruebe que el archivo /etc/hosts del servidor Linux de origen tiene entradas que asignan el nombre de host local a las direcciones IP asociadas con todos los adaptadores de red.
4. Instale los paquetes openssh, openssh-server y openssl más recientes en el equipo que desea replicar.
5. Asegúrese de que Secure Shell (SSH) está habilitado y se ejecuta en el puerto 22.
6. Habilite la autenticación de la contraseña y el subsistema SFTP en el archivo sshd_config:
  1.  Inicie sesión como usuario **raíz**.
  2.  En el archivo /etc/ssh/sshd_config, encuentre la línea que comienza con **PasswordAuthentication**.
  3.  Quite la marca de comentario de la línea y cambie el valor **yes**.
  4.  Busque la línea que comienza por **Subsystem** y quite la marca de comentario.

     ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

7. Agregue la cuenta que creó en CSPSConfigtool.
    1.  Inicie sesión en el servidor de configuración.
    2.  Abra **cspsconfigtool.exe**. (Esta herramienta está disponible como acceso directo en el escritorio y se encuentra en la carpeta %ProgramData%\home\svsystems\bin).
    3.  En la pestaña **Administrar cuentas**, haga clic en **Agregar cuenta**.
    4.  Agregue la cuenta que creó. 
    5.  Especifique las credenciales que utiliza al habilitar la replicación en un equipo.
