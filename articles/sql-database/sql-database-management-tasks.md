<properties 
	pageTitle="Administración de Base de datos SQL" 
	description="Obtenga información acerca de cómo administrar la base de datos de SQL de Azure." 
	headerExpose="" 
	footerExpose="" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor="" 
	services="sql-database" 
	documentationCenter=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="jeffreyg"/>


## Información general

En este artículo se muestra cómo realizar tareas de administración sencillas en la base de datos de SQL Azure. 

## Codificación Base de datos SQL en Azure con Management Studio

Management Studio es una herramienta administrativa que permite administrar varias sesiones y servidores de SQL Server en un espacio de trabajo único. Si ya tiene una instancia SQL Server local, puede abrir una conexión tanto a la instancia local como a un servidor lógico en Azure para realizar las tareas de manera paralela.

Management Studio incluye características que no están actualmente disponibles en el portal de administración, como un comprobador de sintaxis y la posibilidad de guardar scripts y consultas con nombre para volver a usarlos. Base de datos SQL es un extremo de secuencia de datos tubular (TDS). Todas las herramientas que funcionan con TDS, incluido Management Studio, son válidas para las operaciones de Base de datos SQL. Los scripts que desarrolla para el servidor local se ejecutarán en un servidor lógico de Base de datos SQL. 

En el siguiente paso, utilizará Management Studio para conectarse a un servidor lógico en Azure. Este paso requiere contar con SQL Server Management Studio, versión 2008 R2 o 2012. Si necesita ayuda para descargar o conectarse a Management Studio, consulte [Administración de base de datos de SQL con Management Studio][] en este sitio.

Para poder conectarse, a veces es necesario crear una excepción de firewall que permita realizar solicitudes salientes en el puerto 1433 en el sistema local. Los equipos que están asegurados de forma predeterminada suelen tener el puerto 1433 cerrado. 

## Configuración del firewall para un servidor local

1. En el Firewall de Windows con seguridad avanzada, cree una nueva regla saliente.

2. Elija **Puerto**, especifique TCP 1433, indique **Permitir la conexión** y asegúrese de que el perfil **Público** esté seleccionado.

3. Proporcione un nombre significativo, por ejemplo, *WindowsAzureSQLDatabase (tcp-out) port 1433*. 


## Conexión a un servidor lógico

1. En Management Studio, en Conectar al servidor, asegúrese de que Motor de base de datos esté seleccionado y, a continuación, escriba el nombre del servidor lógico con el formato siguiente: *servername*.database.widnows.net.

	También puede obtener el nombre completo del servidor en el portal de administración, en el panel del servidor, en Administrar dirección URL.

2. En Autenticación, elija **Autenticación de SQL Server** y, a continuación, escriba el inicio de sesión de administrador que creó al configurar el servidor lógico.

3. Haga clic en **Opciones**. 

4. En Conectar con base de datos, especifique **master**.


## Conexión a un servidor local

1. En Management Studio, en Conectar al servidor, asegúrese de que Motor de base de datos esté seleccionado y, a continuación, escriba el nombre de una sesión local con el formato siguiente: *servername**instancename*. Si el servidor es local y se trata de una sesión predeterminada, escriba *localhost*.

2. En Autenticación, elija **Autenticación de Windows** y, a continuación, escriba una cuenta de Windows que pertenezca al rol sysadmin.


## Codificación Agregar inicios de sesión y usuarios a la Base de datos SQL de Azure

Después de implementar una base de datos, debe configurar los inicios de sesión y asignar permisos. En el siguiente paso, ejecutará dos scripts.

Con el primer script, se conectará con master y ejecutará un script para crear los inicios de sesión. Los inicios de sesión se utilizarán para admitir las operaciones de lectura y escritura y para delegar las tareas de las operaciones, como la capacidad de ejecutar las consultas del sistema sin permisos 'sa'.

Los inicios de sesión que cree deben ser inicios de sesión de autenticación de SQL Server. Si tiene scripts ya preparados que utilicen identidades de usuario Windows o identidades de notificaciones, esos scripts no funcionarán en Base de datos SQL.

El segundo script asigna permisos de usuario de base de datos. Con este script, se conectará con una base de datos que ya esté cargada en Azure.

## Creación de inicios de sesión

1. En Management Studio, conéctese con un servidor lógico en Azure, expanda la carpeta Bases de datos, haga clic con el botón derecho en **master** y seleccione **Nueva consulta**.

2. Utilice las siguientes instrucciones Transact-SQL para crear inicios de sesión. Sustituya la contraseña por una contraseña válida. Seleccione cada una de forma individual y, a continuación, haga clic en **Ejecutar**. Repita la operación con los inicios de sesión restantes.

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on master, execute each line separately
    -- use this login to manage other logins on this server
    CREATE LOGIN sqladmin WITH password='&lt;ProvidePassword&gt;'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- use this login to create or copy a database
    CREATE LOGIN sqlops WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
</pre></div>


## Creación de usuarios de base de datos

1. Expanda la carpeta Bases de datos, haga clic con el botón derecho en **Colegio** y seleccione **Nueva consulta**.

2. Utilice las siguientes instrucciones Transact-SQL para agregar usuarios de bases de datos. Sustituya la contraseña por una contraseña válida. 

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on a regular database, execute each line separately
    -- use this login for read operations
    CREATE LOGIN sqlreader WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- use this login for write operations
    CREATE LOGIN sqlwriter WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- grant DMV permissions on the school database to 'sqlops'
    GRANT VIEW DATABASE STATE to 'sqlops';
</pre></div>

## Visualización y prueba de inicios de sesión

1. En una ventana de consulta nueva, conéctese con **master** y ejecute la siguiente instrucción: 

        SELECT * from sys.sql_logins;

2. En Management Studio, haga clic con el botón derecho en la base de datos **Colegio** y seleccione **Nueva consulta**.

3. En el menú Consulta, seleccione **Conexión** y, a continuación, haga clic en **Cambiar conexión**.

4. Inicie sesión como *sqlreader*.

5. Copie e intente ejecutar la siguiente instrucción. Debería recibir un error que informe de que el objeto no existe.

        INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
        VALUES (1061, 30, 9);

6. Abra una segunda ventana de consulta y cambie el contexto de conexión a *sqlwriter*. La misma consulta debería ejecutarse ahora correctamente.

Ya ha creado y probado varios inicios de sesión. Para obtener más información, consulte [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure][Administración de bases de datos e inicios de sesión en Base de datos SQL] y [Supervisar Base de datos SQL de Azure mediante vistas de administración dinámica][Supervisión de Base de datos SQL de Azure mediante vistas de administración dinámica].

[Administración de bases de datos e inicios de sesión en Base de datos SQL]: http://msdn.microsoft.com/library/windowsazure/ee336235.aspx
[Supervisión de Base de datos SQL de Azure mediante vistas de administración dinámica]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
[Administración de base de datos de SQL con Management Studio]: http://www.windowsazure.com/develop/net/common-tasks/sql-azure-management/






<!--HONumber=47-->
 