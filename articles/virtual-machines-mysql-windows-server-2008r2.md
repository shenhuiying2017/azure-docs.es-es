<properties urlDisplayName="Install MySQL" pageTitle="Creación de una máquina virtual que ejecuta MySQL en Azure " metaKeywords="Azure virtual machines, Azure Windows Server, Azure installing MySQL, Azure configuring MySQL, Azure databases" description="Create an Azure virtual machine running Windows Server 2008 R2, and then install and configure a MySQL database on the virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running Windows Server 2008 R2 in Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="kathydav" />


#Instalación de MySQL en una máquina virtual que ejecuta Windows Server 2008 R2 en Azure

[MySQL](http://www.mysql.com) es una conocida base de datos SQL de código abierto. Con el [Portal de administración de Azure][AzurePreviewPortal], puede crear una máquina virtual que ejecuta Windows Server 2008 R2 desde la Galería de imágenes. Después, puede instalar y configurar una base de datos MySQL en la máquina virtual.

En este tutorial se muestra cómo realizar las siguientes acciones:

- Utilizar el Portal de administración para crear una máquina virtual que ejecuta Windows Server 2008 R2.

- Instalar y ejecutar MySQL Community Server en la máquina virtual.

##Creación de una máquina virtual que ejecuta Windows Server

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

##Acoplamiento de un disco de datos

Una vez creada una máquina virtual, conecte un disco de datos. Este disco ofrece almacenamiento de datos que necesitará para instalar MySQL. Consulte [Acoplamiento de un disco de datos a una máquina virtual de Windows](http://azure.microsoft.com/en-us/documentation/articles/storage-windows-attach-disk/) y siga las instrucciones para conectar un disco vacío.

##Iniciar sesión en la nueva máquina virtual
A continuación, inicie sesión en la máquina virtual para poder instalar MySQL.

[WACOM.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

##Instalación y ejecución de MySQL Community Server en la máquina virtual
Siga estos pasos para instalar, configurar y ejecutar MySQL Community Server:

1. Una vez que se haya conectado a la máquina virtual mediante el Escritorio remoto, abra **Internet Explorer** desde el menú **Inicio**. 

2. Seleccione el botón **Herramientas** de la esquina superior derecha. En **Opciones de Internet**, seleccione la pestaña **Seguridad** y, a continuación, el icono **Sitios de confianza** y, por último, haga clic en el botón **Sitios**. Agregue *http://\*.mysql.com* a la lista de sitios de confianza.

3. Vaya a [Descargar servidor de la comunidad de MySQL][MySQLDownloads].

4. Seleccione **Microsoft Windows** en el menú desplegable **Plataforma** y haga clic en **Seleccionar**.

5. Busque la versión más reciente de **Windows (x86, 64-bit), MSI Installer** y haga clic en **Descargar**. 

6. Seleccione **No thanks, just start my download! (No, gracias, ya he iniciado mi descarga)** (o bien, regístrese para obtener una cuenta).  Si se le solicita, seleccione un sitio reflejado para descargar el instalador de MySQL y guardarlo en el escritorio.

7. Haga doble clic en el archivo del instalador en el escritorio para comenzar la instalación.

8. Haga clic en **Siguiente**.

9. Acepte los términos de licencia y, a continuación, haga clic en **Next** (Siguiente).

10. Haga clic en **Typical** (Típico) para instalar las características comunes.

11. Haga clic en **Install** (Instalar).

12. Inicie el Asistente para configuración de MySQL y haga clic en **Next** (Siguiente).

13. Seleccione **Detailed Configuration** (Configuración detallada) y haga clic en Next (Siguiente).

14. Seleccione **Server Machine** (Equipo del servidor) si piensa ejecutar MySQL con otras aplicaciones en el servidor o la opción que mejor se adapte a sus necesidades.  Haga clic en **Siguiente**.

15. Seleccione **Multifunctional Database** (Base de datos multifuncional) o la opción que mejor se adapte a sus necesidades.  Haga clic en **Siguiente**.

16. Seleccione la unidad de datos que ha acoplado en la sección anterior.

	![Configure MySQL][MySQLConfig5]

17. Seleccione **Decision Support (DSS)/OLAP** (Apoyo de decisiones (DSS)/OLAP) o la opción que mejor se adapte a sus necesidades.  Haga clic en **Siguiente**.

18. Seleccione **Enable TCP/IP Networking** (Habilitar redes TCP/IP) y **Add firewall exception for this port** (Agregar la excepción del firewall para este puerto); se creará una regla entrante en al Firewall de Windows llamada **MySQL Server**.

	![Configure MySQL][MySQLConfig7]

19. Si necesita almacenar texto en un lenguaje muy diferente, seleccione **Best Support For Multilingualism** (Mejor soporte para plurilingüismo). De lo contrario, elija una de las demás opciones.  Haga clic en **Siguiente**.

	![Configure MySQL][MySQLConfig8]

20. Seleccione **Install As Windows Service** (Instalar como servicio de Windows) y **Launch the MySQL Server automatically** (Iniciar MySQL Server automáticamente).  Seleccione también **Include Bin Directory in Windows PATH** (Incluir el directorio Bin en la RUTA DE ACCESO de Windows). Haga clic en **Siguiente**.

	![Configure MySQL][MySQLConfig9]

21. Especifique la contraseña raíz. No active **Enable root access from remote machines** (Habilitar acceso raíz desde equipos remotos) ni **Create An Anonymous Account** (Crear una cuenta anónima).  Haga clic en **Siguiente**.

	![Configure MySQL][MySQLConfig10]

22. Haga clic en **Execute** (Ejecutar) y espere a que finalice la configuración.

23. Haga clic en **Finish** (Finalizar).

24. Haga clic en **Inicio** y seleccione **MySQL 5.x Command Line Client** para iniciar el cliente de línea de comandos.

25.  Especifique la contraseña raíz en el símbolo del sistema (que estableció en un paso anterior) y se mostrará un símbolo del sistema donde puede emitir instrucciones SQL para interactuar con la base de datos.

26. Para crear un nuevo usuario de MySQL, ejecute lo siguiente en el símbolo del sistema **mysql>**:

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	El punto y coma (;) al final de las líneas es necesario para terminar los comandos.

27. Para crear una base de datos y conceder permisos de usuario "mysqluser" en ella, ejecute los siguientes comandos:

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Tenga en cuenta que los nombres de usuario y contraseñas de la base de datos solo los usan los scripts que se conectan a la base de datos.  Los nombres de cuenta de usuario de la base de datos no representan necesariamente las cuentas de usuario reales en el equipo.

28. Para iniciar sesión desde otro equipo, ejecute el comando siguiente:

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	donde "ip-address" es la dirección IP del equipo desde el que se conectará a MySQL.
	
29. Para salir del cliente de línea de comandos de MySQL, ejecute el siguiente comando:

		quit

30. Cuando MySQL esté instalado, configure un extremo para que pueda obtenerse acceso a MySQL de manera remota. Inicie sesión en el [Portal de administración de Azure][AzurePreviewPortal]. En el Portal de Azure, haga clic en **Máquinas virtuales**, en el nombre de la nueva máquina virtual, en **Extremos** y, por último, en **Agregar extremo**.

31. Seleccione **Agregar extremo** y haga clic en la flecha para continuar.
	

32. Agregue un extremo con el nombre "MySQL", el protocolo **TCP** y los puertos **Público** y **Privado** establecidos en "3306". Haga clic en la marca de verificación. De esta forma, podrá tener acceso a MySQL de manera remota.
	

33. Pruebe la conexión remota a MySQL.  Desde un equipo local con MySQL, ejecute un comando similar al siguiente para iniciar sesión como usuario **mysqluser**:

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	Por ejemplo, si ha asignado el nombre "testwinvm" a la máquina virtual, ejecute este comando:

		mysql -u mysqluser -p -h testwinvm.cloudapp.net

##Recursos
Para obtener más información sobre MySQL, consulte la [documentasión sobre MySQL](http://dev.mysql.com/doc/).

[AzurePortal]: http://manage.windowsazure.com
[MySQLDownloads]: http://www.mysql.com/downloads/mysql/


[MySQLConfig5]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png
[MySQLConfig7]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png
[MySQLConfig8]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png
[MySQLConfig9]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png
[MySQLConfig10]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png


<!--HONumber=35.1-->
