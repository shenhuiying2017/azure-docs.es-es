1. Conéctese a la máquina virtual del servidor de procesos mediante la Conexión a Escritorio remoto.
2. Una vez terminado el inicio de sesión, puede ver que la herramienta de configuración del servidor de procesos se inicia automáticamente y le solicita que escriba lo siguiente
  * Nombre completo (FQDN) o dirección IP del servidor de configuración
  * Puerto en el que el servidor de configuración realiza la escucha. El valor debería ser 443
  * Frase de contraseña de la conexión para conectarse al servidor de configuración.
  * Puerto de transferencia de datos que va a configurar para este servidor de procesos. Deje el valor predeterminado tal cual, a menos que lo haya cambiado a un número de puerto diferente en su entorno.

    ![Registrar servidor de procesos](./media/site-recovery-vmware-register-process-server/register-ps.png)
3. Haga clic en el botón de guardar para guardar la configuración.
4. Una vez completado el registro, el servidor de procesos aparece en el servidor de configuración y ya se puede utilizar para la conmutación por recuperación.

> [!TIP]
> La utilidad de configuración del servidor de procesos se puede iniciar haciendo doble clic en el acceso directo de **cspsconfigtool** disponible en el escritorio de la máquina virtual.


<!--HONumber=Feb17_HO1-->


