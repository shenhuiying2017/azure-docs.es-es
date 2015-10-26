Los pasos siguientes muestran cómo conectarse a la instancia de SQL Server a través de Internet mediante SQL Server Management Studio (SSMS). Sin embargo, se aplican los mismos pasos para hacer que la máquina virtual de SQL Server sea accesible para sus aplicaciones, tanto locales como de Azure.

Antes de que pueda conectarse a la instancia de SQL Server desde otra máquina virtual o Internet, debe completar las siguientes tareas descritas en las secciones que aparecen a continuación:

- [Creación de un extremo TCP para la máquina virtual](#create-a-tcp-endpoint-for-the-virtual-machine)
- [Apertura de puertos TCP en el firewall de Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configuración de SQL Server para escuchar en el protocolo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configuración de SQL Server para autenticación de modo mixto](#configure-sql-server-for-mixed-mode-authentication)
- [Creación de inicios de sesión para la autenticación de SQL Server](#create-sql-server-authentication-logins)
- [Determinación del nombre DNS de la máquina virtual](#determine-the-dns-name-of-the-virtual-machine)
- [Conexión al motor de base de datos desde otro equipo](#connect-to-the-database-engine-from-another-computer)

El siguiente diagrama resume la ruta de conexión:

![Conexión a una máquina virtual de SQL Server](./media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

### Creación de un extremo TCP para la máquina virtual

Para poder acceder a SQL Server desde Internet, la máquina virtual debe tener un extremo para escuchar la comunicación TCP de entrada. Este paso de la configuración de Azure dirige el tráfico del puerto TCP de entrada a un puerto TCP al que puede tener acceso la máquina virtual.

>[AZURE.NOTE]Si se va a conectar en el mismo servicio en la nube o red virtual, no es necesario crear un extremo accesible públicamente. En ese caso, puede continuar con el paso siguiente. Para obtener más información, consulte: [Escenarios de conexión](../articles/virtual-machines/virtual-machines-sql-server-connectivity.md#connection-scenarios).

1. En el Portal de administración de Azure, haga clic en **MÁQUINAS VIRTUALES**.
	
2. Haga clic en la máquina virtual recién creada. Se muestra información acerca de su máquina virtual.
	
3. Cerca de la parte superior de la página, seleccione la página **EXTREMOS** y, a continuación, en la parte inferior de la página haga clic en **AGREGAR**.
	
4. En la página **Agregar extremo a máquina virtual**, haga clic en **Agregar extremo independiente** y, a continuación, en la flecha de avance para continuar.
	
5. En la página **Especifique los detalles del extremo**, proporcione la siguiente información.

	- En el cuadro **NOMBRE**, escriba un nombre para el extremo.
	- En el cuadro **PROTOCOLO**, seleccione **TCP**. De manera similar, puede escribir **57500** en el cuadro **PUERTO PÚBLICO**. Del mismo modo, puede escribir el puerto de escucha predeterminado de SQL Server **1433** en el cuadro **Puerto privado**. Observe que muchas organizaciones seleccionan números de puerto distintos para evitar ataques malintencionados a la seguridad. 

6. Haga clic en la marca de verificación para continuar. Se crea el extremo.

### Apertura de puertos TCP en el firewall de Windows para la instancia predeterminada del motor de base de datos

1. Conéctese a la máquina virtual a través del Escritorio remoto de Windows. Una vez que ha iniciado sesión, en la pantalla Inicio, escriba **WF.msc** y, a continuación, presione ENTRAR. 

	![Iniciar el programa de firewall](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
2. En **Firewall de Windows con seguridad avanzada**, en el panel de la izquierda, haga clic con el botón derecho en **Reglas de entrada** y, a continuación, haga clic en **Nueva regla** en el panel de acciones.

	![Nueva regla](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)

3. En el cuadro de diálogo **Asistente para nueva regla de entrada**, en **Tipo de regla**, seleccione **Puerto** y, a continuación, haga clic en **Siguiente**.

4. En el cuadro de diálogo **Protocolo y puertos**, use el **TCP** predeterminado. En el cuadro **Puertos locales específicos**, escriba el número de puerto de la instancia del motor de base de datos (**1433** para la instancia predeterminada o la opción que elija para el puerto privado en el paso del extremo).

	![Puerto TCP 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)

5. Haga clic en **Siguiente**.

6. En el cuadro de diálogo **Acción**, seleccione **Permitir la conexión** y haga clic en **Siguiente**.

	**Nota de seguridad:** Si selecciona **Permitir la conexión si es segura**, puede proporcionar una mayor seguridad. Seleccione esta opción si desea configurar opciones de seguridad adicionales en el entorno.

	![Permitir conexiones](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)

7. En el cuadro de diálogo **Perfil**, seleccione **Público**, **Privado** y **Dominio**. A continuación, haga clic en **Siguiente**.

    **Nota de seguridad:** Al seleccionar **Público**, se permite el acceso a través de Internet. Cuando sea posible, seleccione un perfil más restrictivo.

	![Perfil público](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)

8. En el cuadro de diálogo **Nombre**, escriba un nombre y una descripción para esta regla y, a continuación, haga clic en** Finalizar**.

	![Nombre de la regla](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

Abra puertos adicionales para otros componentes cada vez que sea necesario. Para obtener más información, consulte [Configurar Firewall de Windows para permitir el acceso a SQL Server](http://msdn.microsoft.com/library/cc646023.aspx).


### Configuración de SQL Server para escuchar en el protocolo TCP

1. Mientras está conectado a la máquina virtual, en la página de inicio, escriba **Administrador de configuración de SQL Server** y presione ENTRAR.
	
	![Abrir SSCM](./media/virtual-machines-sql-server-connection-steps/9Click-SSCM.png)

2. En el panel de la consola del Administrador de configuración de SQL Server, expanda **Configuración de red de SQL Server**.

3. En el panel de la consola, haga clic en **Protocolos para MSSQLSERVER** (el nombre de instancia predeterminado). En el panel de detalles, haga clic con el botón secundario en TCP; el valor predeterminado debe ser Habilitado para las imágenes de la galería. Para sus imágenes personalizadas, haga clic en **Habilitar** (si el estado es Deshabilitado).

	![Habilitar TCP](./media/virtual-machines-sql-server-connection-steps/10Enable-TCP.png)

5. En el panel de la consola, haga clic en **Servicios de SQL Server**. En el panel de detalles, haga clic con el botón secundario en **SQL Server (_nombre de la instancia_)** (la instancia predeterminada es **SQL Server (MSSQLSERVER)**) y, a continuación, haga clic en **Reiniciar** para detener y reiniciar la instancia de SQL Server.

	![Reiniciar el motor de base de datos](./media/virtual-machines-sql-server-connection-steps/11Restart.png)

7. Cierre el Administrador de configuración de SQL Server.

Para obtener más información acerca de la habilitación de protocolos para el motor de base de datos de SQL Server, consulte [Habilitar o deshabilitar un protocolo de red de servidor](http://msdn.microsoft.com/library/ms191294.aspx).

### Configuración de SQL Server para autenticación de modo mixto

El motor de base de datos de SQL Server no puede utilizar la autenticación de Windows sin un entorno de dominio. Para conectarse al motor de base de datos desde otro equipo, configure SQL Server para autenticación de modo mixto. La autenticación de modo mixto permite la autenticación de SQL Server y la autenticación de Windows.

>[AZURE.NOTE]Si ha configurado una red virtual de Azure con un entorno de dominio configurado, es posible que no sea necesario configurar la autenticación de modo mixto.

1. Mientras está conectado a la máquina virtual, en la página de inicio, escriba **SQL Server 2014 Management Studio** y haga clic en el icono seleccionado.

	![Iniciar SSMS](./media/virtual-machines-sql-server-connection-steps/18Start-SSMS.png)

	La primera vez que abra Management Studio se debe crear el entorno de Management Studio para los usuarios. Esta operación puede tardar unos minutos.

2. Management Studio presenta el cuadro de diálogo **Conectar con el servidor**. En el cuadro **Nombre del servidor**, escriba el nombre de la máquina virtual para conectar al motor de base de datos con el Explorador de objetos. (En lugar del nombre de la máquina virtual, también puede utilizar **(local)** o un punto como **Nombre del servidor**. Seleccione **Autenticación de Windows** y deje **_su\_nombre\_de\_MV_\\su\_administrador\_local** en el cuadro **Nombre de usuario**. Haga clic en **Conectar**.

	![Conectar al servidor](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)

3. En el Explorador de objetos de SQL Server Management Studio, haga clic con el botón derecho en el nombre de la instancia de SQL Server (el nombre de la máquina virtual) y, a continuación, haga clic en **Propiedades**.

	![Propiedades del servidor](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)

4. En la página **Seguridad**, en **Autenticación de servidor**, seleccione **Modo de autenticación de Windows y SQL Server** y, a continuación, haga clic en **Aceptar**.

	![Seleccionar el modo de autenticación](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)

5. En el cuadro de diálogo de SQL Server Management Studio, haga clic en **Aceptar** para aceptar el requisito de reiniciar SQL Server.

6. En el Explorador de objetos, haga clic con el botón derecho en el servidor y, a continuación, haga clic en **Reiniciar**. (También se debe reiniciar Agente SQL Server si está en ejecución).

	![Reinicio](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)

7. En el cuadro de diálogo de SQL Server Management Studio, haga clic en **Sí** para indicar que desea reiniciar SQL Server.

### Creación de inicios de sesión para la autenticación de SQL Server

Para conectarse al motor de base de datos desde otro equipo, debe crear al menos un inicio de sesión para la autenticación de SQL Server.

1. En el Explorador de objetos de SQL Server Management Studio, expanda la carpeta de la instancia de servidor en la que desea crear el nuevo inicio de sesión.

2. Haga clic con el botón derecho en la carpeta **Seguridad**, vaya a **Nuevo** y seleccione **Inicio de sesión...**.

	![Nuevo inicio de sesión](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)

3. En el cuadro de diálogo **Inicio de sesión - Nuevo**, en la página **General**, escriba el nombre del usuario nuevo en el cuadro **Nombre de inicio de sesión**.

4. Seleccione **Autenticación de SQL Server**.

5. En el campo **Contraseña**, escriba una contraseña para el usuario nuevo. Vuelva a escribir esa contraseña en el cuadro **Confirmar contraseña**.

6. Para exigir que las opciones de directivas de contraseña sean complejas y aplicables, seleccione **Exigir directivas de contraseña** (recomendado). Esta es una opción predeterminada cuando se selecciona la autenticación de SQL Server.

7. Para exigir que las opciones de directivas de contraseña expiren, seleccione **Exigir expiración de contraseña** (recomendado). Exigir directivas de contraseña debe estar seleccionada para habilitar esta casilla. Esta es una opción predeterminada cuando se selecciona la autenticación de SQL Server.

8. Para exigir al usuario que cree una contraseña nueva después de la primera vez que se use el inicio de sesión, seleccione **El usuario debe cambiar la contraseña en el siguiente inicio de sesión** (recomendado si este inicio de sesión lo utiliza alguien más. Si solo usted utiliza el inicio de sesión, no seleccione esta opción). Exigir expiración de contraseña debe estar seleccionada para habilitar esta casilla. Esta es una opción predeterminada cuando se selecciona la autenticación de SQL Server.

9. En la lista **Base de datos predeterminada**, seleccione una base de datos predeterminada para el inicio de sesión. **master** es el valor predeterminado para esta opción. Si todavía no ha creado una base de datos de usuario, deje este valor en **master**.

10. En la lista **Idioma predeterminado**, deje el valor **predeterminado**.
    
	![Propiedades de inicio de sesión](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)

11. Si este es el primer inicio de sesión que crea, es posible que desee designar este inicio de sesión como administrador de SQL Server. Si es así, en la página **Roles de servidor**, active **sysadmin**.

	**Nota de seguridad:** Los miembros del rol del servidor fijo sysadmin tienen el control completo del motor de base de datos. Deberá restringir cuidadosamente la suscripción en este rol.

	![sysadmin](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)

12. Haga clic en Aceptar.

Para ver más información acerca de los inicios de sesión de SQL Server, consulte [Crear un inicio de sesión](http://msdn.microsoft.com/library/aa337562.aspx).

### Determinación del nombre DNS de la máquina virtual

Para conectarse al motor de base de datos de SQL Server desde otro equipo, debe conocer el nombre del Sistema de nombres de dominio (DNS) de la máquina virtual. (Este es el nombre que Internet utiliza para identificar la máquina virtual. Puede utilizar la dirección IP, pero esta podría cambiar cuando Azure mueva recursos por redundancia o mantenimiento. El nombre DNS será estable, porque se puede redirigir a una dirección IP nueva).

1. En el Portal de administración de Azure (o desde el paso anterior), seleccione **MÁQUINAS VIRTUALES**. 

2. En la página **INSTANCIAS DE MÁQUINA VIRTUA**L, en la columna **Vista rápida**, encuentre y copie el nombre DNS para la máquina virtual.

	![Nombre DNS](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)
	

### Conexión al motor de base de datos desde otro equipo
 
1. En otro equipo conectado a Internet, abra SQL Server Management Studio.
2. En el cuadro de diálogo **Conectar al servidor ** o **Conectar al motor de base de datos**, en el cuadro **Nombre del servidor**, escriba el nombre DNS de la máquina virtual (determinado en la tarea anterior) y un número de puerto de extremo público con formato *NombreDNS,nombrepuerto*, tal como **tutorialtestVM.cloudapp.net,57500**. Para obtener el número de puerto, inicie sesión en el Portal de administración de Azure y encuentre la máquina virtual. En el panel, haga clic en **EXTREMOS** y use el **PUERTO PÚBLICO** asignado a **MSSQL**. ![Public Port](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. En el cuadro **Autenticación**, seleccione **Autenticación de SQL Server**.
5. En el cuadro **Inicio de sesión**, escriba el nombre de un inicio de sesión que haya creado en una tarea anterior.
6. En el cuadro **Contraseña**, escriba la contraseña del inicio de sesión que creó en una tarea anterior.
7. Haga clic en **Conectar**.

	![Conectar mediante SSMS](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)

<!---HONumber=Oct15_HO3-->