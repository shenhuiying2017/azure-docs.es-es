<properties linkid="manage-windows-common-tasks-install-mysql" urlDisplayName="Install MySQL" pageTitle="Create a virtual machine running MySQL in Azure " metaKeywords="Azure virtual machines, Azure Windows Server, Azure installing MySQL, Azure configuring MySQL, Azure databases" description="Create an Azure virtual machine running Windows Server 2008 R2, and then install and configure a MySQL database on the virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MySQL on a virtual machine running Windows Server 2008 R2 in Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="12/15/2013" ms.author="kathydav" />

# Instalación de MySQL en una máquina virtual que ejecuta Windows Server 2008 R2 en Azure

[MySQL][MySQL] es una conocida base de datos SQL de código abierto. Con el [Portal de administración de Azure][Portal de administración de Azure], puede crear una máquina virtual que ejecuta Windows Server 2008 R2 desde la Galería de imágenes. Después, puede instalar y configurar una base de datos MySQL en la máquina virtual.

En este tutorial, aprenderá a:

-   Utilizar el Portal de administración para crear una máquina virtual que ejecuta Windows Server 2008 R2.

-   Instalar y ejecutar MySQL Community Server en la máquina virtual.

## Creación de una máquina virtual con Windows Server 2008 R2

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

## Acoplamiento de un disco de datos

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

## Instalación y ejecución de MySQL Community Server en la máquina virtual

Siga estos pasos para instalar, configurar y ejecutar MySQL Community Server:

1.  Una vez que se haya conectado a la máquina virtual mediante el Escritorio remoto, abra **Internet Explorer** desde el menú **Inicio**.

2.  Seleccione el botón **Herramientas** de la esquina superior derecha. En **Opciones de Internet**, seleccione la pestaña **Seguridad** y, a continuación, seleccione el icono **Sitios de confianza**. Finalmente, haga clic en el botón **Sitios**. Agregue *http://\*.mysql.com* a la lista de sitios de confianza.

3.  Vaya a [Download MySQL Community Server][Download MySQL Community Server].

4.  Seleccione **Microsoft Windows** en el menú desplegable **Platform** y haga clic en **Select**.

5.  Busque la versión más reciente de **Windows (x86, 64-bit), MSI Installer** y haga clic en **Download**.

6.  Seleccione **No thanks, just start my download!** (o bien, regístrese para obtener una cuenta). Si se le solicita, seleccione un sitio reflejado para descargar el instalador de MySQL y guardarlo en el escritorio.

7.  Haga doble clic en el archivo del instalador en el escritorio para comenzar la instalación.

8.  Haga clic en **Next**.

    ![Configuración de MySQL][Configuración de MySQL]

9.  Acepte los términos de licencia y, a continuación, haga clic en **Next**.

    ![Configuración de MySQL][1]

10. Haga clic en **Typical** para instalar las características comunes.

    ![Configuración de MySQL][2]

11. Haga clic en **Instalar**.

    ![Configuración de MySQL][3]

12. Inicie el Asistente para configuración de MySQL y haga clic en **Next**.

    ![Configuración de MySQL][4]

13. Seleccione **Detailed Configuration** y haga clic en Next.

    ![Configuración de MySQL][5]

14. Seleccione **Server Machine** si piensa ejecutar MySQL con otras aplicaciones en el servidor o la opción que mejor se adapte a sus necesidades. Haga clic en **Next**.

    ![Configuración de MySQL][6]

15. Seleccione **Multifunctional Database** o la opción que mejor se adapte a sus necesidades. Haga clic en **Next**.

    ![Configuración de MySQL][7]

16. Seleccione la unidad de datos que ha acoplado en los pasos anteriores.

    ![Configuración de MySQL][8]

17. Seleccione **Decision Support (DSS)/OLAP** o la opción que mejor se adapte a sus necesidades. Haga clic en **Next**.

    ![Configuración de MySQL][9]

18. Seleccione **Enable TCP/IP Networking** y **Add firewall exception for this port** (se creará una regla entrante en al Firewall de Windows llamada **MySQL Server**).

    ![Configuración de MySQL][10]

19. Seleccione **Best Support For Multilingualism** si necesita guardar el texto idiomas diferentes, o seleccione la opción que mejor se adapte a sus necesidades. Haga clic en **Next**.

    ![Configuración de MySQL][11]

20. Seleccione **Install As Windows Service** y **Launch the MySQL Server automatically**. Seleccione también **Include Bin Directory in Windows PATH**. Haga clic en **Next**.

    ![Configuración de MySQL][12]

21. Especifique la contraseña raíz. No active **Enable root access from remote machines** ni **Create An Anonymous Account**. Haga clic en **Next**.

    ![Configuración de MySQL][13]

22. Haga clic en **Execute** y espere a que finalice la configuración.

    ![Configuración de MySQL][14]

23. Haga clic en **Finish**.

    ![Configuración de MySQL][15]

24. Haga clic en **Start** y seleccione **MySQL 5.x Command Line Client** para iniciar el cliente de línea de comandos.

25. Especifique la contraseña raíz en el símbolo del sistema (que estableció en un paso anterior) y se mostrará un símbolo del sistema donde puede emitir instrucciones SQL para interactuar con la base de datos.

26. Para crear un nuevo usuario de MySQL, ejecute lo siguiente en el símbolo del sistema **mysql\>**:

        mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

    Tenga en cuenta que el punto y coma (;) al final de las líneas es fundamental para terminar los comandos.

27. Para crear una base de datos y conceder permisos de usuario `mysqluser` en ella, emita los siguientes comandos:

        mysql> CREATE DATABASE testdatabase;
        mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

    Tenga en cuenta que los nombres de usuario y contraseñas de la base de datos solo los usan los scripts que se conectan a la base de datos. Los nombres de cuenta de usuario de la base de datos no representan necesariamente las cuentas de usuario reales en el equipo.

28. Para iniciar sesión desde otro equipo, ejecute lo siguiente:

        mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

    donde `ip-address` es la dirección IP del equipo desde el que se conectará a MySQL.

29. Para salir del cliente de línea de comandos de MySQL, emita el siguiente comando:

        quit

30. Cuando MySQL esté instalado, debe configurar un extremo para que pueda obtenerse acceso a MySQL de manera remota. Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure]. En el Portal de Azure, haga clic en **Máquinas virtuales**, en el nombre de la nueva máquina virtual, en **Endpoints** y, finalmente, en **Add Endpoint**.

    ![Extremos][Extremos]

31. Seleccione **Add Endpoint** y haga clic en la flecha para continuar.

    ![Extremos][16]

32. Agregue un extremo con el nombre "MySQL", el protocolo **TCP** y los puertos **Public** y **Private** establecidos en "3306". Haga clic en la marca de verificación. Esto permitirá que se obtenga acceso remoto a MySQL.

    ![Extremos][17]

33. Puede conectarse de manera remota a MySQL ejecutando su máquina virtual en Azure. Desde un equipo local que ejecuta MySQL, ejecute el comando siguiente para iniciar sesión como el usuario **mysqluser** que ha creado en los pasos anteriores:

        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

    Por ejemplo, si utiliza la máquina virtual que creamos en este tutorial, el comando sería el siguiente:

        mysql -u mysqluser -p -h testwinvm.cloudapp.net

## Resumen

En este tutorial ha aprendido a crear un equipo virtual Windows 2008 R2 y a conectarse a él de forma remota. También ha aprendido a instalar y configurar MySQL en el equipo virtual, crear una base de datos y un nuevo usuario de MySQL. Para obtener más información sobre MySQL, consulte [MySQL Documentation][MySQL Documentation].

  [MySQL]: http://www.mysql.com
  [Portal de administración de Azure]: http://manage.windowsazure.com
  [create-and-configure-windows-server-2008-vm-in-portal]: ../includes/create-and-configure-windows-server-2008-vm-in-portal.md
  [attach-data-disk-windows-server-2008-vm-in-portal]: ../includes/attach-data-disk-windows-server-2008-vm-in-portal.md
  [Download MySQL Community Server]: http://www.mysql.com/downloads/mysql/
  [Configuración de MySQL]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall1.png
  [1]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall2.png
  [2]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall3.png
  [3]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLInstall4.png
  [4]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig1.png
  [5]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig2.png
  [6]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig3.png
  [7]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig4.png
  [8]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig5.png
  [9]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig6.png
  [10]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig7.png
  [11]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig8.png
  [12]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig9.png
  [13]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig10.png
  [14]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig11.png
  [15]: ./media/virtual-machines-mysql-windows-server-2008r2/MySQLConfig12.png
  [Extremos]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL0.png
  [16]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL1.png
  [17]: ./media/virtual-machines-mysql-windows-server-2008r2/WinVMAddEndpointMySQL.png
  [MySQL Documentation]: http://dev.mysql.com/doc/
