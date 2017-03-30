### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>Preparación de una instalación de inserción en un equipo Windows

1. Asegúrese de que hay conectividad de red entre la el equipo Windows y el servidor de procesos.
2. Cree una cuenta que el servidor de procesos pueda utilizar para acceder al equipo. La cuenta debe tener derechos de administrador (local o dominio). (Use esta cuenta solo para la instalación de inserción y para las actualizaciones del agente).

   > [!NOTE]
   > Si no utiliza una cuenta de dominio, deshabilite el control de acceso de usuarios remotos en el equipo local. Para ello, en la clave de HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System agregue un nuevo valor de DWORD: **LocalAccountTokenFilterPolicy**. Establezca el valor en **1**. Para hacerlo en un símbolo del sistema, ejecute el siguiente comando:  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
2. En el Firewall de Windows del equipo que se desea proteger, seleccione **Permitir una aplicación o una característica a través de Firewall**. Habilite **Compartir archivos e impresoras** e **Instrumental de administración de Windows (WMI)**. En el caso de los equipos que pertenecen a un dominio, el firewall se puede configurar mediante un objeto de directiva de grupo (GPO).

   ![Configuración de firewall](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

3. Agregue la cuenta que creó en CSPSConfigtool.
    1.  Inicie sesión en el servidor de configuración.
    2.  Abra **cspsconfigtool.exe**. (Esta herramienta está disponible como acceso directo en el escritorio y se encuentra en la carpeta %ProgramData%\home\svsystems\bin).
    3.  En la pestaña **Administrar cuentas**, seleccione en **Agregar cuenta**.
    4.  Agregue la cuenta que creó.
    5.  Especifique las credenciales que utiliza al habilitar la replicación en un equipo.
