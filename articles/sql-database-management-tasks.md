<properties umbracoNaviHide="0" pageTitle="Administración de Base de datos SQL" metaKeywords ="base de datos SQL de Azure, base de datos SQL, administrar base de datos sql, agregar inicios de sesión, conectarse a base de datos sql" description="Learn how to manage Azure SQL database." urlDisplayName="Cloud Services" headerExpose="" footerExpose="" disqusComments="1" title="How to Manage SQL Database" authors="jeffreyg" manager="jeffreyg" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/31/2015" ms.author="jeffreyg" />


<h1><a id="swap"></a>Administración de Base de datos SQL</h1>

Esta documentación muestra cómo realizar tareas de administración sencillas en Base de datos SQL de Azure. 

##Tabla de contenido##

* [Direccionamiento del Base de datos SQL en Azure con Management Studio](#connect)
* [Direccionamiento del inicios de sesión y usuarios a Base de datos SQL en Azure](#addlogins)


<h2><a id="connect"></a>Direccionamiento del Base de datos SQL en Azure con Management Studio</h2>

Management Studio es una herramienta administrativa que permite administrar varias instancias y servidores de SQL Server en una sola área de trabajo. Si ya tiene una instancia de SQL Server local, puede abrir una conexión tanto a la instancia local como a un servidor lógico en Azure para realizar las tareas de manera paralela.

Management Studio incluye características que no están actualmente disponibles en el portal de administración, como un comprobador de sintaxis y la posibilidad de guardar scripts y consultas con nombre para volver a usarlos. Base de datos SQL es un extremo de secuencia de datos tubular (TDS). Todas las herramientas que funcionan con TDS, incluido Management Studio, son válidas para las operaciones de Base de datos SQL. Los scripts que desarrolla para el servidor local se ejecutarán en un servidor lógico de Base de datos SQL. 

En el siguiente paso, utilizará Management Studio para conectarse a un servidor lógico en Azure. Este paso requiere tener SQL Server Management Studio versión 2008 R2 o 2012. Si necesita ayuda para descargar o conectarse a Management Studio, consulte [Administración de Base de datos SQL con Management Studio][] en este sitio.

Para poder conectarse, a veces es necesario crear una excepción de firewall que permita realizar solicitudes salientes en el puerto 1433 en el sistema local. Los equipos que están asegurados de forma predeterminada suelen tener el puerto 1433 cerrado. 

##Configuración del firewall para un servidor local

1. En el Firewall de Windows con seguridad avanzada, cree una nueva regla saliente.

2. Elija **Puerto**, especifique TCP 1433, especifique **Permitir la conexión** y asegúrese de que el perfil **Público** esté seleccionado.

3. Especifique un nombre significativo como, por ejemplo, *WindowsAzureSQLDatabase puerto (salida tcp) 1433*. 


##Conexión con un servidor lógico

1. En Management Studio, en Conectar al servidor, asegúrese de que Motor de base de datos esté seleccionado y escriba el nombre del servidor lógico con el formato siguiente: *servername*.database.windows.net

	También puede obtener el nombre completo del servidor en el portal de administración, en el panel del servidor, en Administrar dirección URL.

2. En Autenticación, seleccione **Autenticación de SQL Server** y, a continuación, escriba el inicio de sesión de administrador que creó al configurar el servidor lógico.

3. Haga clic en **Opciones**. 

4. En Conectar con base de datos, especifique **master**.


##Conexión con un servidor local

1. En Management Studio, en Conectar al servidor, asegúrese de que Motor de base de datos esté seleccionado y, a continuación, escriba el nombre de una instancia local con el formato siguiente: *servername*\\*instancename*. Si el servidor es local y se trata de una instancia predeterminada, escriba *localhost*.

2. En Autenticación, elija **Autenticación de Windows** y escriba una cuenta de Windows que pertenezca al rol sysadmin.


<h2><a id="addlogins"></a>Direccionamiento del inicios de sesión y usuarios a Base de datos SQL en Azure</h2>

Después de implementar una base de datos, debe configurar los inicios de sesión y asignar permisos. En el siguiente paso, ejecutará dos scripts.

Con el primer script, se conectará con master y ejecutará un script para crear los inicios de sesión. Los inicios de sesión se utilizarán para admitir las operaciones de lectura y escritura y para delegar las tareas de las operaciones, como la capacidad de ejecutar las consultas del sistema sin permisos 'sa'.

Los inicios de sesión que cree deben ser inicios de sesión de autenticación de SQL Server. Si tiene scripts ya preparados que utilicen identidades de usuario Windows o identidades de notificaciones, esos scripts no funcionarán en Base de datos SQL.

El segundo script asigna permisos de usuario de base de datos. Con este script, se conectará con una base de datos que ya esté cargada en Azure.

##Creación de inicios de sesión

1. En Management Studio, conéctese con un servidor lógico en Azure, expanda la carpeta Bases de datos, haga clic con el botón secundario en **master** y seleccione **Nueva consulta**.

2. Utilice las siguientes instrucciones Transact-SQL para crear inicios de sesión. Sustituya la contraseña por una contraseña válida. Seleccione cada una de forma individual y, a continuación, haga clic en **Ejecutar**. Repita la operación con los inicios de sesión restantes.

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on master, execute each line separately
    -- use this login to manage other logins on this server
    CREATE LOGIN sqladmin WITH password='<ProvidePassword>'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- use this login to create or copy a database
    CREATE LOGIN sqlops WITH password='<ProvidePassword>';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
</pre></div>


##Creación de usuarios de bases de datos

1. Expanda la carpeta Bases de datos, haga clic con el botón secundario en **school** y seleccione **Nueva consulta**.

2. Utilice las siguientes instrucciones Transact-SQL para agregar usuarios de bases de datos. Sustituya la contraseña por una contraseña válida. 

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on a regular database, execute each line separately
    -- use this login for read operations
    CREATE LOGIN sqlreader WITH password='<ProvidePassword>';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- use this login for write operations
    CREATE LOGIN sqlwriter WITH password='<ProvidePassword>';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- grant DMV permissions on the school database to 'sqlops'
    GRANT VIEW DATABASE STATE to 'sqlops';
</pre></div>

##Visualización y prueba de inicios de sesión

1. En una ventana de nueva consulta, conéctese con **master** y ejecute la siguiente instrucción: 

        SELECT * from sys.sql_logins;

2. En Management Studio, haga clic con el botón secundario en la base de datos **school** y seleccione **Nueva consulta**.

3. En el menú Consulta, seleccione **Conexión** y haga clic en **Cambiar conexión**.

4. Inicie sesión como *sqlreader*.

5. Copie e intente ejecutar la siguiente instrucción. Debería recibir un error que informe de que el objeto no existe.

        INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
        VALUES (1061, 30, 9);

6. Abra una segunda ventana de consulta y cambie el contexto de conexión a *sqlwriter*. La misma consulta debería ejecutarse ahora correctamente.

Ya ha creado y probado varios inicios de sesión. Para obtener más información, consulte [Administrar bases de datos e inicios de sesión en Base de datos SQL de Azure][] y [Supervisar Base de datos SQL de Azure mediante vistas de administración dinámica][].

[Administrar bases de datos e inicios de sesión en Base de datos SQL]: http://msdn.microsoft.com/es-es/library/windowsazure/ee336235.aspx
[Supervisar Base de datos SQL de Azure mediante vistas de administración dinámica]: http://msdn.microsoft.com/es-es/library/windowsazure/ff394114.aspx
[Administración de base de datos de SQL con Management Studio]: http://www.windowsazure.com/es-es/develop/net/common-tasks/sql-azure-management/





