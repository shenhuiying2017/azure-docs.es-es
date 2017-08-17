### <a name="open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine"></a>Apertura de puertos TCP en el firewall de Windows para la instancia predeterminada del motor de base de datos
1. Conéctese a la máquina virtual con Escritorio remoto. Para obtener instrucciones detalladas acerca de cómo conectarse a la máquina virtual, consulte [Open a SQL VM with Remote Desktop](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md#open-the-vm-with-remote-desktop)(Apertura de una VM de SQL con Escritorio remoto).
2. Una vez que ha iniciado sesión, en la pantalla Inicio, escriba **WF.msc**y, a continuación, presione ENTRAR.
   
    ![Iniciar el programa de firewall](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
3. En **Firewall de Windows con seguridad avanzada**, en el panel de la izquierda, haga clic con el botón derecho en **Reglas de entrada** y, después, haga clic en **Nueva regla** en el panel de acciones.
   
    ![Nueva regla](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)
4. En el cuadro de diálogo **Asistente para nueva regla de entrada**, en **Tipo de regla**, seleccione **Puerto** y, a continuación, haga clic en **Siguiente**.
5. En el cuadro de diálogo **Protocolo y puertos**, use el **TCP** predeterminado. En el cuadro **Puertos locales específicos**, escriba el número de puerto de la instancia del motor de base de datos (**1433** para la instancia predeterminada que elija para el puerto privado en el paso del punto de conexión).
   
    ![Puerto TCP 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)
6. Haga clic en **Siguiente**.
7. En el cuadro de diálogo **Acción**, seleccione **Permitir la conexión** y haga clic en **Siguiente**.
   
    **Nota de seguridad:** si selecciona **Permitir la conexión si es segura**, puede proporcionar una mayor seguridad. Seleccione esta opción si desea configurar opciones de seguridad adicionales en el entorno.
   
    ![Permitir conexiones](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)
8. En el cuadro de diálogo **Perfil**, seleccione **Público**, **Privado** y **Dominio**. A continuación, haga clic en **Siguiente**.
   
    **Nota de seguridad**: si se selecciona **Público**, se permite el acceso a través de Internet. Cuando sea posible, seleccione un perfil más restrictivo.
   
    ![Perfil público](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)
9. En el cuadro de diálogo **Nombre**, escriba el nombre y la descripción de esta regla y haga clic en **Finalizar**.
   
    ![Nombre de la regla](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

Abra puertos adicionales para otros componentes cada vez que sea necesario. Para obtener más información, consulte [Configurar Firewall de Windows para permitir el acceso a SQL Server](http://msdn.microsoft.com/library/cc646023.aspx).

### <a name="configure-sql-server-to-listen-on-the-tcp-protocol"></a>Configuración de SQL Server para escuchar en el protocolo TCP

[!INCLUDE [Enable TCP](virtual-machines-sql-server-connection-tcp-protocol.md)]

### <a name="configure-sql-server-for-mixed-mode-authentication"></a>Configuración de SQL Server para autenticación de modo mixto
El motor de base de datos de SQL Server no puede utilizar la autenticación de Windows sin un entorno de dominio. Para conectarse al motor de base de datos desde otro equipo, configure SQL Server para autenticación de modo mixto. La autenticación de modo mixto permite la autenticación de SQL Server y la autenticación de Windows.

> [!NOTE]
> Si ha configurado una red virtual de Azure con un entorno de dominio configurado, es posible que no sea necesario configurar la autenticación de modo mixto.
> 
> 

1. Mientras está conectado a la máquina virtual, en la página de inicio, escriba **SQL Server Management Studio** y haga clic en el icono seleccionado.
   
    La primera vez que abra Management Studio se debe crear el entorno de Management Studio para los usuarios. Esta operación puede tardar unos minutos.
2. Management Studio presenta el cuadro de diálogo **Conectar con el servidor** . En el cuadro **Nombre del servidor**, escriba el nombre de la máquina virtual para conectarse al motor de base de datos con el explorador de objetos [en lugar del nombre de la máquina virtual, se puede utilizar **(local)** o un punto (.) como **nombre del servidor**]. Seleccione **Autenticación de Windows** y deje ***nombre_de_su_MV*\su_administrador_local** en el cuadro **Nombre de usuario**. Haga clic en **Conectar**.
   
    ![Conectar con el servidor](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)
3. En el Explorador de objetos de SQL Server Management Studio, haga clic con el botón derecho en el nombre de la instancia de SQL Server (el nombre de la máquina virtual) y, a continuación, haga clic en **Propiedades**.
   
    ![Propiedades del servidor](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)
4. En la página **Seguridad**, en **Autenticación de servidor**, seleccione **Modo de autenticación de Windows y SQL Server** y, luego, haga clic en **Aceptar**.
   
    ![Seleccionar el modo de autenticación](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)
5. En el cuadro de diálogo de SQL Server Management Studio, haga clic en **Aceptar** para aceptar el requisito de reiniciar SQL Server.
6. En el Explorador de objetos, haga clic con el botón derecho en el servidor y, a continuación, haga clic en **Reiniciar**. (También se debe reiniciar Agente SQL Server si está en ejecución).
   
    ![Reiniciar](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)
7. En el cuadro de diálogo de SQL Server Management Studio, haga clic en **Sí** para indicar que desea reiniciar SQL Server.

### <a name="create-sql-server-authentication-logins"></a>Creación de inicios de sesión para la autenticación de SQL Server
Para conectarse al motor de base de datos desde otro equipo, debe crear al menos un inicio de sesión para la autenticación de SQL Server.

1. En el Explorador de objetos de SQL Server Management Studio, expanda la carpeta de la instancia de servidor en la que desea crear el nuevo inicio de sesión.
2. Haga clic con el botón derecho en la carpeta **Security**, apunte a **Nuevo** y seleccione **Inicio de sesión...**.
   
    ![Nuevo inicio de sesión](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)
3. En el cuadro de diálogo **Inicio de sesión - Nuevo**, en la página **General**, escriba el nombre del usuario nuevo en el cuadro **Nombre de inicio de sesión**.
4. Seleccione **Autenticación de SQL Server**.
5. En el campo **Contraseña** , escriba una contraseña para el usuario nuevo. Vuelva a escribir esa contraseña en el cuadro **Confirmar contraseña** .
6. Seleccione las opciones de cumplimiento de la contraseña requeridas (**Exigir directivas de contraseña**, **Exigir expiración de contraseña** y **El usuario debe cambiar la contraseña en el siguiente inicio de sesión**). Si usa este inicio de sesión para usted mismo, no es necesario requerir un cambio de contraseña en el siguiente inicio de sesión.
7. En la lista **Base de datos predeterminada** , seleccione una base de datos predeterminada para el inicio de sesión. **master** es el valor predeterminado para esta opción. Si todavía no ha creado una base de datos de usuario, deje este valor en **master**.
   
    ![Propiedades de inicio de sesión](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)
8. Si este es el primer inicio de sesión que crea, es posible que desee designar este inicio de sesión como administrador de SQL Server. En ese caso, en la página **Roles de servidor**, active **sysadmin**.
   
   > [!NOTE]
   > los miembros del rol del servidor fijo sysadmin tienen el control completo del motor de base de datos. Deberá restringir cuidadosamente la suscripción en este rol.
   > 
   > 
   
   ![sysadmin](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)
9. Haga clic en Aceptar.

Para ver más información acerca de los inicios de sesión de SQL Server, consulte [Crear un inicio de sesión](http://msdn.microsoft.com/library/aa337562.aspx).

