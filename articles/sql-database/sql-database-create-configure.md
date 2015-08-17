<properties 
	pageTitle="Creación y configuración de una base de datos SQL de Azure - Tutorial de Azure" 
	description="Creación y configuración de una base de datos SQL de Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="sidneyh" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="sidneyh"/>

# Creación y configuración de una Base de datos SQL de Azure

En este tema, creará y configurará una nueva Base de datos SQL de Azure mediante la opción **CREACIÓN RÁPIDA** del Portal de administración de Azure. Este proceso le muestra cómo crear una base de datos SQL mediante un servidor existente, y también muestra cómo crear un nuevo servidor si es necesario.

> [AZURE.NOTE]La creación de una Base de datos SQL con **CREACIÓN RÁPIDA** aprovisiona una base de datos Estándar (S0). Para crear una Base de datos SQL en una capa de servicio y rendimiento distinto de Estándar (S0), utilice la **CREACIÓN PERSONALIZADA**. Para obtener más información sobre la creación de una Base de datos SQL de Azure mediante** CREACIÓN PERSONALIZADA**, consulte [Información general sobre Base de datos SQL de Microsoft Azure](sql-database-get-started.md).

## Creación de una Base de datos SQL de Azure

1. Inicie sesión en el [Portal de administración](https://portal.azure.com/).

2. En la parte inferior de la página, haga clic en **NUEVO**.

	![Haga clic en Bases de datos SQL][1]

3. Haga clic en **SERVICIOS DE DATOS**, **BASE DE DATOS SQL** y, a continuación, en **CREACIÓN RÁPIDA**.

	![Haga clic en Nuevo, en Servicios de datos y en Creación rápida][2]
	 
5. En **CREACIÓN RÁPIDA**, escriba un nombre para la nueva base de datos, seleccione una suscripción y, a continuación, seleccione un servidor en la lista **SERVIDOR** (o vaya al paso siguiente para crear un nuevo servidor).

	![Creación de una nueva base de datos SQL en un servidor existente][7]

	Si lo desea, cree un nuevo servidor; para ello, seleccione **Nuevo servidor de Base de datos SQL**. ![Creación de una nueva base de datos SQL y un servidor][8]

	1. Seleccione una región. La región determina la ubicación geográfica del servidor. Las regiones no pueden cambiarse fácilmente, por lo que debe seleccionar la apropiada para el servidor. Seleccione la ubicación que le sea más próxima. Si mantiene la base de datos y la aplicación de Azure en la misma región, evita que se produzcan costes relacionados con el ancho de banda y la latencia de datos.
	2. Especifique un nombre de inicio de sesión con una sola palabra sin espacios. 

		Base de datos SQL usa la autenticación de SQL a través de una conexión cifrada. Se creará un nuevo inicio de sesión de autenticación de SQL Server asignado al rol del servidor fijo sysadmin con el nombre que proporcione.

		El inicio de sesión no puede ser una dirección de correo electrónico, la cuenta de usuario de Windows ni un Windows Live ID. La Base de datos SQL no admite notificaciones ni autenticación de Windows. 
	3. Proporcione una contraseña segura con más de ocho caracteres que contenga una combinación de valores en mayúsculas y minúsculas, y un número o símbolo.

	


9. Haga clic en la marca de verificación **CREAR BASE DE DATOS SQL** en la parte inferior de la página cuando haya terminado.

### Nombre de servidor generado automáticamente

Tenga en cuenta que si creó un nuevo servidor no especificó un nombre de servidor. Base de datos SQL genera automáticamente el nombre del servidor para garantizar que no existen entradas DNS duplicadas. El nombre del servidor es una cadena alfanumérica de 10 caracteres. No puede cambiar el nombre del servidor de Base de datos SQL.

En el próximo paso, configurará el firewall de manera que se permita el acceso a las conexiones de las aplicaciones que se ejecuten en la red.

<a id="configFWLogical"></a>

## Configuración del firewall para el servidor lógico

1. En el[ Portal de administración](http://manage.windowsazure.com), haga clic en** Bases de datos SQL** y, a continuación, haga clic en **Servidores**

	![Haga clic en Servidores][4]
2. En la lista, haga clic en el servidor que acaba de crear.

2. Haga clic en **Configurar**.

	![Haga clic en Configurar][5]

3. En la sección **Direcciones IP permitidas**, haga clic en **AGREGAR A DIRECCIONES IP PERMITIDAS**. Esta es la dirección IP que está escuchando el servidor proxy o el router actualmente. La Base de datos SQL detecta la dirección IP que usa la conexión actual y crea una regla de firewall para aceptar solicitudes de conexión desde ese dispositivo. ![Haga clic en Agregar a direcciones IP permitidas][6]

	Se genera un nombre predeterminado para la regla. Edite el nombre como desee.
	

4. Cuando se conecta a la base de datos desde otro equipo, debe crear una nueva regla para permitir que su dirección IP pueda conectarse. Utilice los cuadros Inicio y Fin para crear un intervalo de direcciones IP.

	Si los equipos cliente usan direcciones IP asignadas dinámicamente, puede especificar un intervalo que sea lo suficientemente amplio para que incluya direcciones IP asignadas a equipos en la red. Comience por un intervalo reducido y, a continuación, expándalo solo si lo necesita.

7. Haga clic en **Guardar** en la parte inferior de la página para completar el paso.

Ahora dispone de una Base de datos SQL, un servidor lógico, una regla de firewall que permite conexiones entrantes desde la dirección IP y un inicio de sesión de administrador.

**Nota:** el servidor lógico que acaba de crear es virtual y se hospedará dinámicamente en los servidores físicos de un centro de datos. La eliminación del servidor es una acción irreversible. Asegúrese de realizar una copia de seguridad de las bases de datos que cargue posteriormente en el servidor.


<!--Image references-->
[1]: ./media/sql-database-create-configure/click-new.png
[2]: ./media/sql-database-create-configure/new-data-services-sql-storage-quick-create.png
[3]: ./media/sql-database-create-configure/server-settings.png
[4]: ./media/sql-database-create-configure/click-servers.png
[5]: ./media/sql-database-create-configure/click-configure.png
[6]: ./media/sql-database-create-configure/allowed-ip-addresses.png
[7]: ./media/sql-database-create-configure/quick-create-existing-server.png
[8]: ./media/sql-database-create-configure/quick-create-new-server.png



 

<!---HONumber=August15_HO6-->