### <a name="prepare-for-push-install-on-windows-machines"></a>Preparación para la instalación de inserción en máquinas Windows

1. Asegúrese de que haya conectividad de red entre la máquina Windows y el servidor de procesos.
2. Cree una cuenta que el servidor de procesos pueda utilizar para tener acceso a la máquina. La cuenta debe tener privilegios de administrador (local o dominio) y solo se utiliza para la instalación de inserción y las actualizaciones de agentes.

   > [!NOTE]
   > Si no utiliza una cuenta de dominio, deberá deshabilitar el control de acceso de usuario remoto en la máquina local. Puede hacerlo mediante la adición de un nuevo valor DWORD **LocalAccountTokenFilterPolicy** en la clave de registro HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System y estableciendo su valor en 1. Este es el comando para hacer lo mismo desde un símbolo del sistema **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.
   >
   >
2. En el Firewall de Windows de la máquina que desea proteger, seleccione **Permitir una aplicación o una característica a través de Firewall**. Habilite **Compartir archivos e impresoras** e **Instrumental de administración de Windows**. Para las máquinas que pertenecen a un dominio, puede configurar el firewall con un GPO.

   ![Configuración de firewall](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

3. Agregue la cuenta que creó en la herramienta CSPSConfigtool.
    - Inicie sesión en el servidor de configuración.
    - Abra **cspsconfigtool.exe**. (Esta herramienta está disponible como acceso directo en el escritorio y se encuentra en la carpeta %ProgramData%\home\svsystems\bin)
    - En la pestaña **Administrar cuentas**, haga clic en **Agregar cuenta**.
    - Agregue la cuenta que creó. Después de agregar la cuenta, tiene que proporcionar las credenciales para habilitar la replicación de una máquina.


<!--HONumber=Jan17_HO3-->


