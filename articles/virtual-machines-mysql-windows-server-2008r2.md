<properties urlDisplayName="Install MySQL" pageTitle="Creación de una máquina virtual que ejecuta MySQL en Azure " metaKeywords="Azure virtual machines, Azure Windows Server, Azure installing MySQL, Azure configuring MySQL, Azure databases" description="Create an Azure virtual machine running Windows Server 2008 R2, and then install and configure a MySQL database on the virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running Windows Server 2008 R2 in Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="12/15/2013" ms.author="kathydav" />


#Instalación de MySQL en una máquina virtual que ejecuta Windows Server 2008 R2 en Azure

[MySQL](http://www.mysql.com) es una conocida base de datos SQL de código abierto. Con el [Portal de administración de Azure][AzurePreviewPortal], puede crear una máquina virtual donde se ejecute Windows Server 2008 R2 desde la Galería de imágenes.  Luego puede instalar y configurar una base de datos MySQL en la máquina virtual.

En este tutorial, aprenderá a:

- Utilizar el Portal de administración para crear una máquina virtual que ejecuta Windows Server 2008 R2.

- Instalar y ejecutar MySQL Community Server en la máquina virtual.

##Creación de una máquina virtual con Windows Server 2008 R2

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

##Acoplamiento de un disco de datos

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

##Instalación y ejecución de MySQL Community Server en la máquina virtual
Siga estos pasos para instalar, configurar y ejecutar MySQL Community Server:

1. Una vez que se haya conectado a la máquina virtual mediante el Escritorio remoto, abra **Internet Explorer** desde el menú **Inicio**. 

2. Seleccione el botón **Herramientas** en la esquina superior derecha. En **Opciones de Internet**, seleccione la pestaña **Seguridad** y, a continuación, seleccione el icono **Sitios de confianza**. Finalmente, haga clic en el botón **Sitios**. Agregue *http://\*.mysql.com* a la lista de sitios de confianza.

3. Vaya a [Descargar MySQL Community Server][MySQLDownloads].

4. Seleccione **Microsoft Windows** en el menú desplegable **Platforma** y haga clic en **Seleccionar**.

5. Busque la versión más reciente de **Windows (x86, 64 bits), MSI Installer** y haga clic en **Download**. 

6. Seleccione **No thanks, just start my download!** (o, regístrese para obtener una cuenta).  Si se le solicita, seleccione un sitio reflejado para descargar el instalador de MySQL y guardarlo en el escritorio.

7. Haga doble clic en el archivo del instalador en el escritorio para comenzar la instalación.

8. Haga clic en **Next**.

	![MySQL Setup][MySQLInstall1]

9. Acepte los términos de licencia y, a continuación, haga clic en **Next**.

	![MySQL Setup][MySQLInstall2]

10. Haga clic en **Typical** para instalar las características comunes.

	![MySQL Setup][MySQLInstall3]

11. Haga clic en**Install**.

	![MySQL Setup][MySQLInstall4]

12. Inicie el Asistente para configuración de MySQL y haga clic en **Next**.

	![Configure MySQL][MySQLConfig1]

13. Seleccione **Detailed Configuration** y haga clic en Next.

	![Configure MySQL][MySQLConfig2]

14. Seleccione **Server Machine** si piensa ejecutar MySQL con otras aplicaciones en el servidor o la opción que mejor se adapte a sus necesidades.  Haga clic en **Next**.

	![Configure MySQL][MySQLConfig3]

15. Seleccione **Multifunctional Database** o la opción que mejor se adapte a sus necesidades.  Haga clic en **Next**.

	![Configure MySQL][MySQLConfig4]

16. Seleccione la unidad de datos que ha acoplado en los pasos anteriores.

	![Configure MySQL][MySQLConfig5]

17. Seleccione **Decision Support (DSS)/OLAP** o la opción que mejor se adapte a sus necesidades.  Haga clic en **Next**.

	![Configure MySQL][MySQLConfig6]

18. Seleccione **Enable TCP/IP Networking** y **Add firewall exception for this port** (se creará una regla entrante en al Firewall de Windows llamada **MySQL Server**).

	![Configure MySQL][MySQLConfig7]

19. Seleccione **Best Support For Multilingualism** si necesita guardar el texto idiomas diferentes, o seleccione la opción que mejor se adapte a sus necesidades.  Haga clic en **Next**.

	![Configure MySQL][MySQLConfig8]

20. Seleccione **Install As Windows Service** y **Launch the MySQL Server automatically**.  Seleccione también **Include Bin Directory in Windows PATH**. Haga clic en **Next**.

	![Configure MySQL][MySQLConfig9]

21. Especifique la contraseña raíz. No active **Enable root access from remote machines** ni **Create An Anonymous Account**.  Haga clic en **Next**.

	![Configure MySQL][MySQLConfig10]

22. Haga clic en **Execute** y espere a que finalice la configuración.

	![Configure MySQL][MySQLConfig11]

23. Haga clic en **Finish**.

	![Configure MySQL][MySQLConfig12]

24. Haga clic en **Start** y seleccione **MySQL 5.x Command Line Client** para iniciar el cliente de línea de comandos.

25.  Especifique la contraseña raíz en el símbolo del sistema (que estableció en un paso anterior) y se mostrará un símbolo del sistema donde puede emitir instrucciones SQL para interactuar con la base de datos.

26. Para crear un nuevo usuario de MySQL, ejecute lo siguiente en el símbolo del sistema **mysql>**:

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Tenga en cuenta que el punto y coma (;) al final de las líneas es fundamental para terminar los comandos.

27. Para crear una base de datos y conceder permisos de usuario `mysqluser` en ella, emita los siguientes comandos:

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Tenga en cuenta que los nombres de usuario y contraseñas de la base de datos solo los usan los scripts que se conectan a la base de datos.  Los nombres de cuenta de usuario de la base de datos no representan necesariamente las cuentas de usuario reales en el equipo.

28. Para iniciar sesión desde otro equipo, ejecute lo siguiente:

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	donde `ip-address` es la dirección IP del equipo desde el que se conectará a MySQL.
	
29. Para salir del cliente de línea de comandos de MySQL, emita el siguiente comando:

		quit

30. Cuando MySQL esté instalado, debe configurar un extremo para que pueda obtenerse acceso a MySQL de manera remota. Inicie sesión en el [Portal de administración de Azure][AzurePreviewPortal]. En el Portal de Azure, haga clic en **Máquinas virtuales**, en el nombre de la nueva máquina virtual, en **Extremos**, y, finalmente, en  **Agregar extremo**.

	![Endpoints][AddEndPoint]

31. Seleccione **Agregar extremo** y haga clic en la flecha para continuar.
	
	![Endpoints][AddEndPoint2]

32. Agregue un extremo con el nombre "MySQL", el protocolo **TCP** y los puertos **Public** y **Private** establecidos en "3306". Haga clic en la marca de verificación. Esto permitirá que se obtenga acceso remoto a MySQL.
	
	![Endpoints][AddEndPoint3]

33. Puede conectarse de manera remota a MySQL ejecutando su máquina virtual en Azure.  Desde un equipo local que ejecuta MySQL, ejecute el comando siguiente para iniciar sesión como el usuario **mysqluser** que ha creado en los pasos anteriores:

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	Por ejemplo, si utiliza la máquina virtual que creamos en este tutorial, el comando sería el siguiente:

		mysql -u mysqluser -p -h testwinvm.cloudapp.net

##Resumen

En este tutorial ha aprendido a crear un equipo virtual Windows 2008 R2 y a conectarse a él de forma remota. También ha aprendido a instalar y configurar MySQL en el equipo virtual, crear una base de datos y un nuevo usuario de MySQL. Para obtener más información sobre MySQL, consulte [Documentación de MySQL](http://dev.mysql.com/doc/).

[AzurePreviewPortal]: http://manage.windowsazure.com
[MySQLDownloads]: http://www.mysql.com/downloads/mysql/


[MySQLInstall1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall1.png
[MySQLInstall2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall2.png
[MySQLInstall3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall3.png
[MySQLInstall4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall4.png
[MySQLConfig1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig1.png
[MySQLConfig2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig2.png
[MySQLConfig3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig3.png
[MySQLConfig4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig4.png
[MySQLConfig5]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png
[MySQLConfig6]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig6.png
[MySQLConfig7]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png
[MySQLConfig8]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png
[MySQLConfig9]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png
[MySQLConfig10]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png
[MySQLConfig11]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig11.png
[MySQLConfig12]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig12.png
[AddEndPoint]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL0.png
[AddEndPoint2]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL1.png
[AddEndPoint3]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL.png
