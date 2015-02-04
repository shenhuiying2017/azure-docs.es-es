<properties urlDisplayName="How to create and configure an Azure SQL DB" pageTitle="Creación y configuración de una base de datos SQL de Azure - Tutorial de Azure" metaKeywords="Creación de una base de datos SQL de Azure, Configuración de una base de datos SQL de Azure" description="Creación y configuración de una base de datos SQL de Azure." metaCanonical="" services="sql-database" documentationCenter="" title="How to Create and Configure an Azure SQL Database" authors="sidneyh" solutions="" manager="jhubbard" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sidneyh" />

<h1><a id="configLogical"></a>Creación y configuración de una base de datos SQL de Azure</h1>

En este tema, deberá crear y configurar una base de datos SQL de Azure mediante el Portal de administración de Azure. En este flujo de trabajo, creará primero el servidor. Es posible que prefiera este enfoque si dispone de bases de datos SQL Server existentes que quiera cargar.

## Tabla de contenido##
* [Creación de un servidor lógico](#createLogical)
* [Configuración del firewall para el servidor lógico](#configFWLogical)

<h2><a id="createLogical"></a>Creación de un servidor lógico</h2>

1. Inicie sesión en el [Portal de administración](http://manage.windowsazure.com).

2. En la parte inferior de la página, haga clic en **NUEVO**.

	![Click SQL Databases][1]

3. Haga clic en **Servicios de datos**, en **Base de datos SQL** y, a continuación, en **Creación rápida**.

	![Click New, Data Services, and Quick Create][2]
	 
5. En **Configuración del servidor de bases de datos SQL**, seleccione una suscripción.

	![Select a subscription][3]

6. En **Configuración del servidor de bases de datos SQL**, escriba un nombre de inicio de sesión como una palabra sin espacios. 

	Base de datos SQL usa la autenticación de SQL a través de una conexión cifrada. Se creará un nuevo inicio de sesión de autenticación de SQL Server asignado al rol del servidor fijo sysadmin con el nombre que proporcione. 

	El inicio de sesión no puede ser una dirección de correo electrónico, una cuenta de usuario de Windows o un Windows Live ID. Las reclamaciones y la autenticación de Windows no son compatibles con Base de datos SQL.
	
7. Proporcione una contraseña segura con más de ocho caracteres que contenga una combinación de valores en mayúsculas y minúsculas, y un número o símbolo.

7. Seleccione una región. La región determina la ubicación geográfica del servidor. Las regiones no pueden cambiarse fácilmente, por lo que debe seleccionar la apropiada para el servidor. Seleccione la ubicación que le sea más próxima. Si mantiene la base de datos y la aplicación de Azure en la misma región, evita que se produzcan costes relacionados con el ancho de banda y la latencia de datos.

8. Asegúrese de mantener la opción **Permitir que los servicios de Windows Azure accedan al servidor** seleccionada de manera que pueda conectarse a esta base de datos con el Portal de administración, servicios de almacenamiento y otros servicios en Azure. 

9. Haga clic en la marca de verificación en la parte inferior de la página cuando haya finalizado.

### Nombre de servidor generado automáticamente

Tenga en cuenta que no especificó un nombre de servidor. Base de datos SQL genera automáticamente el nombre del servidor para garantizar que no existen entradas DNS duplicadas. El nombre del servidor es una cadena alfanumérica de 10 caracteres. No puede cambiar el nombre del servidor de Base de datos SQL.

En el próximo paso, configurará el firewall de manera que se permita el acceso a las conexiones de las aplicaciones que se ejecuten en la red.

## Configuración del firewall para el servidor lógico

1. En el [Portal de administración](http://manage.windowsazure.com), haga clic en **Bases de datos SQL** y, a continuación, en **Servidores**

	![Click Servers][4]
2. En la lista, haga clic en el servidor que acaba de crear.

2. Haga clic en **Configurar**.

	![Click Configure][5]

3. En la sección **Dirección IP permitidas**, haga clic en **AGREGAR DIRECCIONES IP PERMITIDAS**. Esta es la dirección IP que está escuchando el servidor proxy o el router actualmente. Base de datos SQL detecta la dirección IP que usa la conexión actual y crea una regla de firewall para aceptar solicitudes de conexión desde ese dispositivo. 
	![Click Add to the allowed IP addresses][6]

	Se genera un nombre predeterminado para la regla. Edite el nombre como desee. 
	

4. Cuando se conecta a la base de datos desde otro equipo, debe crear una nueva regla para permitir que su dirección IP pueda conectarse. Utilice los cuadros Inicio y Fin para crear un intervalo de direcciones IP.

	Si los equipos cliente usan direcciones IP asignadas dinámicamente, puede especificar un intervalo que sea lo suficientemente amplio para que incluya direcciones IP asignadas a equipos en la red. Comience por un intervalo reducido y, a continuación, expándalo solo si lo necesita.

7. Haga clic en **Guardar** en la parte inferior de la página para completar el paso. 

Ahora dispone de un servidor lógico, una regla de firewall que permite conexiones entrantes desde la dirección IP, y un inicio de sesión de administrador. 

**Nota:** el servidor lógico que acaba de crear es virtual y se hospedará dinámicamente en los servidores físicos de un centro de datos. La eliminación del servidor es una acción irreversible. Asegúrese de realizar una copia de seguridad de las bases de datos que cargue posteriormente en el servidor. 


<!--Image references-->
[1]: ./media/sql-database-create-configure/click-new.png
[2]: ./media/sql-database-create-configure/new-data-services-sql-storage-quick-create.png
[3]: ./media/sql-database-create-configure/server-settings.png
[4]: ./media/sql-database-create-configure/click-servers.png
[5]: ./media/sql-database-create-configure/click-configure.png
[6]: ./media/sql-database-create-configure/allowed-ip-addresses.png

