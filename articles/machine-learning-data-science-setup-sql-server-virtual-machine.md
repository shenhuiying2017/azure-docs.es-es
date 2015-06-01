<properties 
	pageTitle="Configurar una máquina virtual de SQL Server de Azure para ciencia de datos" 
	description="Configurar una máquina virtual de ciencia de datos con SQL Server y IPython Server." 
	services="machine-learning" 
	solutions="" documentationCenter="" 
	authors="msolhab,xibingaomsft" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="mohabib;xibingao" />

# Configurar una máquina virtual de SQL Server de Azure para ciencia de datos

En este tema se muestra cómo crear y configurar una máquina virtual de SQL Server que se usará como parte de un entorno de ciencia de datos basado en la nube. La máquina virtual de Windows se configura con herramientas de compatibilidad como Bloc de notas de IPython, el Explorador de almacenamiento de Azure y AzCopy, así como otras utilidades que son útiles para los proyectos de ciencia de datos. El Explorador de almacenamiento de Azure y AzCopy, por ejemplo, permite cargar de manera cómoda datos en el almacenamiento de blobs de Azure desde la máquina local o descargarlos en el equipo local desde el almacenamiento de blobs.

La galería de máquinas virtuales de Azure incluye varias imágenes que contienen Microsoft SQL Server. Seleccione una imagen de máquina virtual de SQL Server que sea adecuada para sus necesidades de datos. Las imágenes recomendadas son:

- SQL Server 2012 SP2 Enterprise para tamaños de datos pequeños y medianos
- SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads para tamaños de datos grandes o muy grandes

 > [AZURE.NOTE] La imagen de SQL Server 2012 SP2 Enterprise **no incluye un disco de datos**. Deberá agregar o asociar uno o más discos duros virtuales para almacenar los datos. Al crear una máquina virtual de Azure, esta cuenta con un disco para el sistema operativo asignado a la unidad C y un disco temporal asignado a la unidad D. No utilice la unidad D para almacenar datos. Como señala su nombre, esta ofrece únicamente almacenamiento temporal. No ofrece redundancia o copias de seguridad porque no reside en el almacenamiento de Azure.


##<a name="Provision"></a>Conectarse al Portal de administración de Azure y aprovisionar una máquina virtual de SQL Server

1.  Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com/) con su cuenta. 
	Si no tiene una cuenta de Azure, visite [Evaluación gratuita de Azure](http://www.windowsazure.com/pricing/free-trial/).

2.  En el Portal de administración de Azure, en la parte inferior izquierda de la página web, haga clic sucesivamente en **+NUEVO**, **PROCESO**, **MÁQUINA VIRTUAL** y **DESDE LA GALERÍA**.

3.  En la página **Crear una máquina virtual**, seleccione una imagen de máquina virtual que contenga SQL Server según sus necesidades y, a continuación, haga clic en la flecha de avance en la parte inferior derecha de la página. Para conocer la información más actualizada sobre las imágenes compatibles de SQL Server en Azure, consulte el tema [Introducción a SQL Server en máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720) en el conjunto de documentación [SQL Server en máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719).

	![Select SQL Server VM][1]

4.  En la primera página de **Configuración de máquina virtual**, facilite la siguiente información:

    -   Proporcione un **NOMBRE DE MÁQUINA VIRTUAL**.
    -   En el cuadro **NOMBRE DE USUARIO NUEVO**, escriba el nombre de usuario único para la cuenta de administrador local de máquina virtual.
    -   En el cuadro **CONTRASEÑA NUEVA**, escriba una contraseña segura. Para obtener más información, consulte [Contraseñas seguras](http://msdn.microsoft.com/library/ms161962.aspx).
    -   En el cuadro **CONFIRMAR CONTRASEÑA**, vuelva a escribir la contraseña.
    -   Seleccione el tamaño correspondiente en la lista desplegable **TAMAÑO**.

     > [AZURE.NOTE] El tamaño de la máquina virtual se especifica durante el aprovisionamiento: A2 es el tamaño más pequeño recomendado para cargas de trabajo de producción. El tamaño mínimo recomendado es A3 para una máquina virtual cuando se usa SQL Server Enterprise Edition. Seleccione A3 o un tamaño superior cuando use SQL Server Enterprise Edition. Seleccione A4 si usa SQL Server 2012 o 2014 Enterprise optimizado para imágenes de cargas de trabajo transaccionales. Seleccione A7 si usa SQL Server 2012 o 2014 Enterprise optimizado para imágenes de cargas de trabajo de almacenamiento de datos. El tamaño seleccionado limita la cantidad de discos de datos que puede configurar. Para obtener la información más actualizada sobre los tamaños disponibles de máquina virtual y la cantidad de discos de datos que puede adjuntar a una máquina virtual, consulte [Tamaños de máquina virtual para Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Para obtener más información, consulte [Precios de las máquinas virtuales](http://azure.microsoft.com/pricing/details/virtual-machines/).

    Haga clic en la flecha de avance en la esquina inferior derecha para continuar.

    ![VM Configuration][2]

5.  En la segunda página de **Configuración de máquina virtual**, configure recursos para las redes, el almacenamiento y la disponibilidad:

    -   En el cuadro **Servicio en la nube**, seleccione **Crear un nuevo servicio en la nube**.
    -   En el cuadro **Nombre DNS de servicio en la nube**, proporcione la primera parte de un nombre DNS que elija, para que así se complete un nombre con el formato**TESTNAME.cloudapp.net**
    -   En el cuadro **REGIÓN/GRUPO DE AFINIDAD/RED VIRTUAL**, seleccione una región donde se hospedará esta imagen virtual.
    -   En **Cuenta de almacenamiento**, seleccione una cuenta de almacenamiento existente o una generada automáticamente.
    -   En el cuadro **CONJUNTO DE DISPONIBILIDAD**, seleccione **(none)**.
    -   Lea y acepte la información de precios.

6.	En la sección **EXTREMOS**, haga clic en el menú desplegable vacío en **NOMBRE** y seleccione **MSSQL**; a continuación, escriba el número de puerto de la instancia del motor de la base de datos (**1433** para la instancia predeterminada).

7.  Su máquina virtual de SQL Server también puede actuar como servidor de Bloc de notas de IPython, que se configurará en un paso posterior. 
	Agregue un nuevo extremo para especificar el puerto que usará el servidor de Bloc de notas de IPython. Escriba un nombre en la columna **NOMBRE**,	seleccione el número de puerto que prefiera como puerto público y 9999 como puerto privado.

	Haga clic en la flecha de avance en la esquina inferior derecha para continuar.

	![Select MSSQL and IPython ports][3]

8.  Acepte la opción predeterminada activada **Instalar agente de máquina virtual** y haga clic en la marca de verificación de la esquina inferior derecha del asistente para completar el proceso de aprovisionamiento de máquinas virtuales.

	![VM Final Options][4]

9.  Espere a que Azure prepare la máquina virtual. Espere el estado de la máquina virtual para continuar:

    -   Inicio (aprovisionamiento)
    -   Stopped
    -   Inicio (aprovisionamiento)
    -   Ejecución (aprovisionamiento)
    -   Ejecución

##<a name="RemoteDesktop"></a>Abrir la máquina virtual con Escritorio remoto y finalizar la configuración

1.  Cuando se completa el aprovisionamiento, haga clic en el nombre de la máquina virtual para ir a la página PANEL. En la parte inferior de la página, haga clic en **Conectar**.

2.  Elija abrir el archivo rpd con el programa Escritorio remoto de Windows (`%windir%\system32\mstsc.exe`).

3.  En el cuadro de diálogo **Seguridad de Windows**, escriba la contraseña de la cuenta de administrador local que especificó en un paso anterior. (Puede que se le pida comprobar las credenciales de la máquina virtual).

4.  Es posible que, la primera vez que inicie sesión en esta máquina virtual, deban completarse varios procesos, entre los que se incluyen la configuración del escritorio, las actualizaciones de Windows y la finalización de las tareas de configuración inicial de Windows (sysprep). Una vez que Sysprep de Windows finaliza, la configuración de SQL Server completa las tareas de configuración. Estas tareas pueden causar un retraso de unos minutos mientras se completan. `SELECT @@SERVERNAME` puede no devolver el nombre correcto hasta que finalice la instalación de SQL Server. Asimismo, SQL Server Management Studio puede no estar visible en la página de inicio.

Una vez se haya conectado a la máquina virtual con el Escritorio remoto de Windows, la máquina virtual funcionará como cualquier otro equipo. Conéctese a la instancia predeterminada de SQL Server con SQL Server Management Studio (en ejecución en la máquina virtual) de manera normal.

##<a name="InstallIPython"></a>Instalar Bloc de notas de IPython y otras herramientas de compatibilidad

Para configurar la nueva máquina virtual de SQL Server para que actúe como un servidor de Bloc de notas de IPython e instalar las herramientas de compatibilidad adicionales como AzCopy, Explorador de almacenamiento de Azure, paquetes de Python de ciencia de datos útiles y otros, le ofrecemos un script de personalización especial. Para instalarlo:

- Haga clic con el botón secundario en el icono Inicio de Windows y haga clic en **Símbolo del sistema (administrador)**
- Copie los comandos siguientes y péguelos en el símbolo del sistema. 

    	set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'
    	@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

- Cuando se le solicite, escriba la contraseña que prefiera para el servidor de Bloc de notas de IPython.
- El script de personalización automatiza varios procedimientos posteriores a la instalación, entre los que se incluyen:
	+ La instalación y configuración del servidor Bloc de notas de IPython.
	+ La apertura de los puertos TCP en Firewall de Windows para los extremos que creó anteriormente:
	+ Para la conectividad remota de SQL Server
	+ Para la conectividad remota del servidor de Bloc de notas de IPython
	+ La obtención de Blocs de notas de IPython y scripts de SQL de ejemplo
	+ La descarga e instalación de paquetes de Python de ciencia de datos útiles
	+ La descarga e instalación de las herramientas de Azure como AzCopy y Explorador de almacenamiento de Azure  

- Es posible tener acceso y ejecutar Bloc de notas de IPython desde cualquier explorador local o remoto mediante una dirección URL de la forma `https://<virtual_machine_DNS_name>:<port>`, donde puerto es el puerto público de IPython que seleccionó al aprovisionar la máquina virtual.
- El servidor de Bloc de notas de IPython se ejecuta como un servicio en segundo plano y se reiniciará automáticamente cuando se reinicie la máquina virtual.

##<a name="Optional"></a>Conectar discos de datos según sea necesario

Si la imagen de la máquina virtual no incluye discos de datos, es decir, discos que no sean la unidad C (disco del sistema operativo) y la unidad D (disco temporal), deberá agregar uno o más discos de datos para almacenar los datos. La imagen de máquina virtual de SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads viene configurada previamente con discos adicionales para los archivos de registro y de datos de SQL Server.

 > [AZURE.NOTE] No utilice la unidad D para almacenar datos. Como señala su nombre, esta ofrece únicamente almacenamiento temporal. No ofrece redundancia o copias de seguridad porque no reside en el almacenamiento de Azure.

Para conectar discos de datos adicionales, siga los pasos descritos en [Acoplamiento de un disco de datos a una máquina virtual de Windows](storage-windows-attach-disk.md), que le guiarán a través de:

1. Conexión de discos vacíos a la máquina virtual aprovisionada en los pasos anteriores
2. Inicialización de los nuevos discos en la máquina virtual


##<a name="SSMS"></a>Conectarse a SQL Server Management Studio y habilitar la autenticación de modo mixto

El motor de base de datos de SQL Server no puede utilizar la autenticación de Windows sin un entorno de dominio. Para conectarse al motor de base de datos desde otro equipo, configure SQL Server para autenticación de modo mixto. La autenticación de modo mixto permite la autenticación de SQL Server y la autenticación de Windows. Se requiere el modo de autenticación de SQL para la ingesta de datos directamente desde las bases de datos de su máquina virtual de SQL Server en el [Estudio de aprendizaje automático de Azure](https://studio.azureml.net) mediante el módulo del Lector.

1.  Mientras esté conectado a la máquina virtual mediante el panel **Búsqueda** de Escritorio remoto y escriba **SQL Server Management Studio** (SMSS). Haga clic para iniciar SQL Server Management Studio (SSMS). Puede que quiera agregar un acceso directo a SSMS en el escritorio para su uso futuro.

    ![Start SSMS][5]

    La primera vez que abra Management Studio se debe crear el entorno de Management Studio para los usuarios. Esta operación puede tardar unos minutos.

2.  Cuando se abre, Management Studio presenta el cuadro de diálogo **Conectar al servidor**. En el cuadro **Nombre del servidor**, escriba el nombre de la máquina virtual para conectar al motor de base de datos con el Explorador de objetos. (En lugar del nombre de la máquina virtual, también puede utilizar **(local)** o un punto como **Nombre del servidor**. Seleccione **Autenticación de Windows** y deje ***nombre_de_su_MV*\\su_administrador_local** en el cuadro **Nombre de usuario**. Haga clic en **Conectar**.

    ![Connect to Server][6]

	<br>

	 > [AZURE.TIP] Puede cambiar el modo de autenticación de SQL Server mediante un cambio de clave del registro de Windows o con SQL Server Management Studio. Para cambiar el modo de autenticación mediante el cambio de clave del registro, inicie una **Nueva consulta** y ejecute el script siguiente:
	
		USE master
    	go
    	
    	EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2
    	go


	Para cambiar el modo de autenticación mediante SQL Server Management Studio:

3.  En el Explorador de objetos de SQL Server Management Studio, haga clic con el botón secundario en el nombre de la instancia de SQL Server (el nombre de la máquina virtual) y, a continuación, haga clic en **Propiedades**.

    ![Server Properties][7]

4.  En la página **Seguridad**, en **Autenticación de servidor**, seleccione **Modo de autenticación de Windows y SQL Server** y, a continuación, haga clic en **Aceptar**.

    ![Select Authentication Mode][8]

5.  En el cuadro de diálogo de SQL Server Management Studio, haga clic en **Aceptar** para aceptar el requisito de reiniciar SQL Server.

6.  En el Explorador de objetos, haga clic con el botón secundario en el servidor y, a continuación, haga clic en **Reiniciar**. (También se debe reiniciar Agente SQL Server si está en ejecución).

    ![Restart][9]

7.  En el cuadro de diálogo de SQL Server Management Studio, haga clic en **Sí** para indicar que desea reiniciar SQL Server.

##<a name="Logins"></a>Creación de inicios de sesión para la autenticación de SQL Server

Para conectarse al motor de base de datos desde otro equipo, debe crear al menos un inicio de sesión para la autenticación de SQL Server.  

> [AZURE.TIP] Puede crear nuevos inicios de sesión de SQL Server mediante programación o con SQL Server Management Studio. Para crear un nuevo usuario sysadmin con autenticación de SQL mediante programación, inicie una **Nueva consulta** y ejecute el script siguiente. Reemplace <new user name> y <new password> por el nombre de usuario y la contraseña que prefiera. Ajuste la directiva de contraseñas según sea necesario (el código de ejemplo desactiva la expiración de la contraseña y la comprobación de la directiva). Para ver más información acerca de los inicios de sesión de SQL Server, consulte [Crear un inicio de sesión](http://msdn.microsoft.com/library/aa337562.aspx).  
	
    USE master
    go
    
    CREATE LOGIN <new user name> WITH PASSWORD = N'<new password>',
    	CHECK_POLICY = OFF,
    	CHECK_EXPIRATION = OFF;
    
    EXEC sp_addsrvrolemember @loginame = N'<new user name>', @rolename = N'sysadmin';

Para crear nuevos inicios de sesión de SQL Server con SQL Server Management Studio:

1.  En el Explorador de objetos de SQL Server Management Studio, expanda la carpeta de la instancia de servidor en la que desea crear el nuevo inicio de sesión.

2.  Haga clic con el botón secundario en la carpeta **Seguridad**, seleccione **Nuevo** y seleccione **Inicio de sesión...**.

    ![New Login][10]

3.  En el cuadro de diálogo **Inicio de sesión - Nuevo**, en la página **General**, escriba el nombre del usuario nuevo en el cuadro**Nombre de inicio de sesión**.

4.  Seleccione **Autenticación de SQL Server**.

5.  En el campo **Contraseña**, escriba una contraseña para el usuario nuevo. Vuelva a escribir esa contraseña en el cuadro **Confirmar contraseña**.

6.  Para exigir que las opciones de directivas de contraseña sean complejas y aplicables, seleccione **Exigir directivas de contraseña** (recomendado). Esta es una opción predeterminada cuando se selecciona la autenticación de SQL Server.

7.  Para exigir que las opciones de directivas de contraseña expiren, seleccione **Exigir expiración de contraseña** (recomendado). Exigir directivas de contraseña debe estar seleccionada para habilitar esta casilla. Esta es una opción predeterminada cuando se selecciona la autenticación de SQL Server.

8.  Para exigir al usuario que cree una contraseña nueva después de la primera vez que se use el inicio de sesión, seleccione **El usuario debe cambiar la contraseña en el siguiente inicio de sesión** (Se recomienda si este inicio de sesión lo utiliza alguien más. Si solo usted utiliza el inicio de sesión, no seleccione esta opción). Exigir expiración de contraseña debe estar seleccionada para habilitar esta casilla. Esta es una opción predeterminada cuando se selecciona la autenticación de SQL Server.

9.  En la lista **Base de datos predeterminada**, seleccione una base de datos predeterminada para el inicio de sesión. **master** es la base de datos predeterminada para esta opción. Si todavía no ha creado una base de datos de usuario, deje este valor en **master**.

10. En la lista **Idioma predeterminado**, deje el **valor predeterminado**.

    ![Login Properties][11]

11. Si este es el primer inicio de sesión que crea, es posible que desee designar este inicio de sesión como administrador de SQL Server. Si es así, en la página **Roles de servidor**, active **sysadmin**.

    **Nota de seguridad:** los miembros del rol fijo de servidor sysadmin tienen control total del motor de la base de datos. Deberá restringir la pertenencia a este rol con cuidado.

    ![sysadmin][12]

12. Haga clic en Aceptar.

##<a name="DNS"></a>Determinación del nombre DNS de la máquina virtual

Para conectarse al motor de base de datos de SQL Server desde otro equipo, debe conocer el nombre del Sistema de nombres de dominio (DNS) de la máquina virtual. (Este es el nombre que Internet utiliza para identificar la máquina virtual. Puede utilizar la dirección IP, pero esta podría cambiar cuando Azure mueva recursos por redundancia o mantenimiento. El nombre DNS será estable, porque se puede redirigir a una dirección IP nueva).

1.  En el Portal de administración de Azure (o desde el paso anterior), seleccione **MÁQUINAS VIRTUALES**.

2.  En la página **INSTANCIAS DE MÁQUINA VIRTUAL**, en la columna **NOMBRE DNS**, encuentre y copie el nombre DNS para la máquina virtual que aparece antecedido por **http://**. (Puede que la interfaz de usuario no muestre todo el nombre, pero puede hacer clic con el botón secundario en él y seleccionar Copiar).

##<a name="cde"></a>Conexión al motor de base de datos desde otro equipo

1.  En otro equipo conectado a Internet, abra SQL Server Management Studio.

2.  En el cuadro de diálogo **Conectar al servidor** o **Conectar al motor de base de datos**, en el cuadro **Nombre del servidor**, escriba el nombre DNS de la máquina virtual (determinado en la tarea anterior) y un número de puerto de extremo público con formato de *DNSName,portnumber* como, por ejemplo, **tutorialtestVM.cloudapp.net,57500**.

3.  En el cuadro **Autenticación**, seleccione **Autenticación de SQL Server**.

4.  En el cuadro **Inicio de sesión**, escriba el nombre de un inicio de sesión que haya creado en una tarea anterior.

5.  En el cuadro **Contraseña**, escriba la contraseña del inicio de sesión que creó en una tarea anterior.

6.  Haga clic en **Conectar**.

##<a name="amlconnect"></a>Conectarse al motor de base de datos de Aprendizaje automático de Azure

En las fases posteriores del proceso de ciencia de datos en la nube, se usará [Estudio de aprendizaje automático de Azure](https://studio.azureml.net) para compilar e implementar modelos de aprendizaje automático. Para la ingesta de los datos de las bases de datos de su máquina virtual de SQL Server directamente en Aprendizaje automático de Azure para su entrenamiento o puntuación, use el módulo del Lector en un nuevo experimento de [Estudio de aprendizaje automático de Azure](https://studio.azureml.net). Este tema se trata más en detalle en los vínculos del mapa del proceso de ciencia de datos en la nube. Para obtener una introducción, consulte [¿Qué es el Estudio de aprendizaje automático de Azure??](machine-learning-what-is-ml-studio.md).

2.	En el panel **Propiedades** del [módulo del Lector](https://msdn.microsoft.com/library/azure/dn905997.aspx), seleccione **Base de datos SQL de Azure** en la lista desplegable **Origen de datos**.

3.	En el cuadro de texto **Nombre del servidor de la base de datos**, escriba `tcp:<nombre DNS de su máquina virtual>,1433`

4.	Escriba el nombre de usuario de SQL en el cuadro de texto **Nombre de la cuenta de usuario del servidor**.

5.	Escriba la contraseña de usuario de sql en el cuadro de texto **Contraseña de la cuenta de usuario del servidor**.

	![Azure ML Reader][13]

##<a name="shutdown"></a>Apagado y desasignación de la máquina virtual cuando no esté en uso

Las máquinas virtuales de Azure tienen unas tarifas de tipo **pague solo por lo que use**. Para asegurarse de que no se le facture cuando no use la máquina virtual, debe estar en el estado **Detenida (desasignada)**.

> [AZURE.NOTE] Si se apaga la máquina virtual desde dentro (mediante las opciones de energía de Windows), se detiene la máquina virtual, pero permanece asignada. Para asegurarse de que no se le facture, detenga siempre las máquinas virtuales desde el [Portal de administración de Azure](http://manage.windowsazure.com/). También puede detener la máquina virtual a través de Powershell, con una llamada a ShutdownRoleOperation con "PostShutdownAction" igual a "StoppedDeallocated".

Para apagar y desasignar la máquina virtual:

1. Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com/) con su cuenta.  

2. Seleccione **MÁQUINAS VIRTUALES** en la barra de navegación de la izquierda.

3. En la lista de máquinas virtuales, haga clic en el nombre de su máquina virtual y, a continuación, vaya a la página **PANEL**.

4. En la parte inferior de la página, haga clic en **APAGAR**. 

![VM Shutdown][15]

Se desasignará la máquina virtual, pero no se eliminará. Puede reiniciar la máquina virtual en cualquier momento desde el Portal de administración de Azure.

## La máquina virtual de Azure SQL Server ya está lista para su uso: ¿qué es lo siguiente?

La máquina virtual ya está lista para su uso en los ejercicios de ciencia de datos. La máquina virtual también está preparada para su uso como un servidor de Bloc de notas de IPython para la exploración y el procesamiento de datos, y otras tareas junto con el Aprendizaje automático de Azure y el proceso de ciencia de datos en la nube. 

Los pasos siguientes en el proceso de ciencia de datos se asignan en la [Guía de aprendizaje: procesamiento avanzado de datos en Azure](machine-learning-data-science-advanced-data-processing.md) y pueden incluir pasos que muevan datos a HDInsight, los procese y muestree allí como preparación para el aprendizaje a partir de los datos con Aprendizaje automático de Azure.

* Para obtener información sobre cómo mover datos a HDInsight desde el almacenamiento de blobs de Azure, consulte [Crear y cargar datos en tablas de Hive desde el almacenamiento de blobs de Azure](machine-learning-data-science-hive-tables.md).
* Para obtener información sobre el procesamiento de datos en HDInsight con consultas de Hive, vea [Enviar consultas de Hive a clústeres de Hadoop de HDInsight en el proceso de ciencia de datos en la nube](machine-learning-data-science-hive-queries.md).
* Para obtener información sobre el muestreo de datos en HDInsight, consulte [Muestreo de datos en tablas de Hive en HDInsight de Azure](machine-learning-data-science-sample-data-hive.md).



[1]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/selectsqlvmimg.png
[2]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/4vm-config.png
[3]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/sqlvmports.png
[4]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmpostopts.png
[5]:./media/machine-learning-data-science-setup-sql-server-virtual-machine/searchssms.png
[6]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/19connect-to-server.png
[7]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/20server-properties.png
[8]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/21mixed-mode.png
[9]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/22restart2.png
[10]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/23new-login.png
[11]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/24test-login.png
[12]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/25sysadmin.png
[13]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/amlreader.png
[15]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmshutdown.png


<!--HONumber=49-->