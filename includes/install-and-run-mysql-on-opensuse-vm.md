
1. Para escalar privilegios, ejecute:

		sudo -s
	
	Especifique la contraseña.

2. Ejecute el siguiente comando para instalar MySQL Community Server Edition:

		# zypper install mysql-community-server

	Espere hasta que MySQL se descargue e instale.
3. Para configurar MySQL para que se inicie cuando el sistema arranque, ejecute el siguiente comando:

		# insserv mysql
4. Ahora puede iniciar manualmente el demonio MySQL (mysqld) con el siguiente comando:

		# rcmysql start

	Para comprobar el estado del demonio MySQL, ejecute:

		# rcmysql status

	Si desea detener el demonio MySQL, ejecute:

		# rcmysql stop

5. Advertencia: después de la instalación, la contraseña raíz de MySQL se encuentra vacía de forma predeterminada. Se recomienda ejecutar **mysql_secure_installation**, un script que ayuda a proteger MySQL. Cuando ejecute **mysql_secure_installation**, se le solicitará que cambie la contraseña raíz de MySQL, quite las cuentas de usuario anónimas, deshabilite los datos de inicio de sesión raíz remotos, quite las bases de datos de prueba y vuelva a cargar la tabla de privilegios. Es recomendable que diga que sí a todas las opciones y cambie la contraseña raíz. Ejecute el siguiente comando para ejecutar el script:

		$ mysql_secure_installation

6. Después de la ejecución, puede iniciar sesión en MySQL:

		$ mysql -u root -p

	Especifique la contraseña raíz de MySQL (que cambió en el paso anterior) y se mostrará un símbolo del sistema donde puede emitir certificados SQL para interactuar con la base de datos.

7. Para crear un nuevo usuario de MySQL, ejecute los siguientes comandos en el símbolo del sistema **mysql>**:

		mysql> CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Tenga en cuenta que el punto y coma (;) al final de las líneas es fundamental para terminar los comandos.

8. Para crear una base de datos y conceder permisos de usuario `mysqluser` en ella, emita los siguientes comandos:

		mysql> CREATE DATABASE testdatabase;
		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';

	Tenga en cuenta que los nombres de usuario y contraseñas de la base de datos solo los usan los scripts que se conectan a la base de datos. Los nombres de cuenta de usuario de la base de datos no representan necesariamente las cuentas de usuario reales en el sistema.

9. Para iniciar sesión desde otro equipo, ejecute lo siguiente:

		mysql> GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';

	donde `ip-address` es la dirección IP del equipo desde el que se conectará a MySQL.
	
10. Para salir de la utilidad administración de base de datos MySQL, emita el siguiente comando:

		quit

11. Cuando MySQL esté instalado, deberá configurar un extremo para que pueda obtenerse acceso a MySQL de manera remota. Inicie sesión en el [Portal de administración de Azure][AzurePreviewPortal]. En el Portal de Azure, haga clic en **Máquinas virtuales**, en el nombre de la nueva máquina virtual y luego en **Extremos**.

	![Endpoints][Image7]

12. Haga clic en **Agregar** en la parte inferior de la página. ![Endpoints][Image8]

13. Agregue un extremo con el nombre "MySQL", con el protocolo **TCP** y los puertos **Público** y **Privado** establecidos en "3306". Esto permite obtener acceso remoto a MySQL. ![Extremos][Image9]

14. Para conectarse remotamente a MySQL en la máquina virtual OpenSUSE en Azure, ejecute el siguiente comando en el equipo local:

		mysql -u mysqluser -p -h <yourservicename>.cloudapp.net

	Por ejemplo, si utiliza la máquina virtual que creamos en este tutorial, el comando sería el siguiente:

		mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

15. Ha configurado MySQL y ha creado una base de datos y un nuevo usuario correctamente. Para obtener más información sobre MySQL, consulte la [Documentación de MySQL][MySQLDocs].

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePreviewPortal]: http://manage.windowsazure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png

<!---HONumber=July15_HO1-->