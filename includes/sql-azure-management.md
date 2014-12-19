
# Administración de Base de datos SQL de Azure con el uso de SQL Server Management Studio 

Puede usar el Portal de administración de Base de datos SQL de Azure o la aplicación cliente SQL Server Management Studio (SSMS) para administrar las suscripciones a Base de datos SQL y crear y administrar las bases de datos y los servidores lógicos asociados. En la siguiente guía se describe cómo usar Management Studio para administrar las bases de datos y los servidores lógicos de Base de datos SQL.

<div class="dev-callout-new-collapsed">
<strong>Nota: <span>Click to collapse</span></strong>
<div class="dev-callout-content">
<p>Puede usar la versión SQL Server 2014, SQL Server 2012 o SQL Server 2008 R2 de SSMS. Las versiones anteriores no son compatibles.</p>
</div>
</div>

En este tema se incluyen los pasos siguientes:

-   [Paso 1: Obtener SQL Server Management Studio][]
-   [Paso 2: Conectarse a Base de datos SQL][]
-   [Paso 3: Crear y administrar las bases de datos][]
-   [Paso 4: Crear y administrar los inicios de sesión][]
-   [Paso 5: Supervisar Base de datos SQL mediante las vistas de administración dinámica][]

<h2><a id="Step1" name="Step1"> </a>Paso 1: Obtener Management Studio</h2>

Management Studio es un entorno integrado para
administrar bases de datos SQL. Al administrar
bases de datos en Azure, puede usar la aplicación Management Studio instalada con
SQL Server o descargar la versión gratuita de SQL Server 2012 Management Studio Express (SSMSE) A continuación se describe
cómo instalar SSMSE.

1.  En la página [Microsoft SQL Server 2012 Express][], seleccione la versión x86 de Management Studio si ejecuta un sistema operativo de 32 bits, o bien x64 si ejecuta un sistema operativo de 64 bits. Haga clic en **Descargar** y, cuando se le solicite, ejecute Instalación.

2.  Haga clic en **Nueva instalación independiente de SQL Server o agregar características a una
    instalación existente** y haga clic en **Aceptar**.

3.  Acepte los términos de licencia y, a continuación, haga clic en **Siguiente**.

4. Haga clic en **Instalar** para instalar todos los archivos necesarios de SQL Server.

5.  En la pantalla **Selección de características**, la opción **Herramientas de administración -
    Básica** está preseleccionada. Esto se debe a que se encuentra en ejecución el instalador de Management Studio. Si ejecuta la instalación de todas las ediciones de SQL Server Express, seleccione la opción **Herramientas de administración - Básica** y, a continuación, haga clic en **Siguiente**.

6.  En la pantalla **Informe de errores**, se puede optar por enviar
    informes de errores Microsoft. No es obligatorio usar SSMSE. Haga clic en
    **Siguiente** para iniciar la instalación.

7.  Cuando se haya completado la instalación, se abrirá la página **Operación completada**
.. Haga clic en **Cerrar**. 


<h2><a id="Step2" name="Step2"> </a>Paso 2: Conectarse a Base de datos SQL</h2>

Para conectarse a Base de datos SQL, debe saber el nombre del servidor de Azure. Es necesario que inicie sesión en el portal para tener acceso a esta información.

1.  Inicie sesión en el [Portal de administración de Azure][].

2.  En el panel izquierdo, haga clic en **Bases de datos SQL**.

3.  En la página principal Bases de datos SQL, haga clic en **SERVIDORES** en la parte superior de la página para ver todos los servidores asociados con la suscripción. Encuentre el nombre del servidor con el que desea establecer la conexión y cópielo en el Portapapeles.

	A continuación, configure el firewall de base de datos SQL para
permitir las conexiones desde el equipo local. Para ello, agregue la dirección IP de las máquinas locales a la lista de excepciones del firewall.

1.  En la página principal Bases de datos SQL, haga clic en **SERVIDORES** y, a continuación, haga clic en el servidor al que desea conectarse.

2.  Haga clic en **Configurar** en la parte superior de la página.

3.  Copie la dirección IP en CURRENT CLIENT IP ADDRESS.

4.  En la página Configurar, la sección **Direcciones IP permitidas** incluye tres cuadros donde puede especificar un nombre de regla y un intervalo de direcciones IP como valores iniciales y finales. Para un nombre de regla, puede escribir el nombre del PC. Para el intervalo inicial y final, pegue la dirección IP del PC en ambos cuadros y, a continuación, haga clic en la casilla que aparece.

	El nombre de regla debe ser exclusivo. Si se trata de un equipo de desarrollo, puede escribir la dirección IP en el cuadro de inicio de intervalo de IP y en el cuadro de final de intervalo de IP. De lo contrario, puede que tenga que escribir un intervalo más amplio de direcciones IP para tener en cuenta conexiones de equipos adicionales de la organización.
 
5. Haga clic en **GUARDAR** en la parte inferior de la página.

    **Nota:** Los cambios de la configuración del firewall
    pueden tardar hasta cinco minutos en aplicarse.

	Ya puede conectarse a Base de datos SQL con Management Studio.

1.  En la barra de tareas, haga clic en **Inicio**, vaya a **Todos los programas**, vaya a
    **Microsoft SQL Server 2012** y, a continuación, haga clic en **SQL Server
    Management Studio**.

2.  En **Conectar con el servidor**, especifique el nombre de servidor completo
    como *serverName*.database.windows.net. En Azure, el nombre del servidor es una cadena generada automáticamente compuesta de caracteres alfanuméricos.

3.  Seleccione **Autenticación de SQL Server**.

4.  En el cuadro **Iniciar sesión**, escriba el inicio de sesión del administrador de SQL Server
    especificados en el portal cuando creó el servidor con el formato
    *login*@*yourServerName*.

5.  En el cuadro **Contraseña**, escriba la contraseña especificada
    en el portal cuando creó el servidor.

8.  Haga clic en **Conectar** para establecer la conexión.

En Azure, cada servidor lógico de Base de datos SQL es una abstracción que define una agrupación de bases de datos. La ubicación física de cada base de datos puede estar en el centro de datos de cualquier PC. 

En versiones anteriores, tenía que conectarse directamente a la base de datos **maestra** al configurar la conexión en Management Studio. Este paso ya no es necesario, ya que, ahora, las conexiones se establecerán correctamente en función del nombre del servidor, el tipo de autenticación y las credenciales de administrador.

Muchos de los asistentes SSMS que puede usar para tareas como
crear y modificar inicios de sesión y bases de datos en una base de datos de SQL Server no
están disponibles para Bases de datos SQL de Azure, por lo que deberá utilizar
instrucciones Transact-SQL para realizar estas tareas. Los pasos siguientes
proporcionan ejemplos de estas instrucciones. Para obtener más información sobre cómo usar
Transact-SQL con Base de datos SQL, incluidos los detalles sobre los comandos
admitidos, consulte [	Referencia de Transact-SQL (Base de datos SQL)][].

<h2><a id="Step3" name="Step3"> </a>Paso 3: Crear y administrar las bases de datos</h2>

Mientras está conectado a la base de datos **maestra**, puede crear bases de datos nuevas
en el servidor y modificar o quitar bases de datos existentes. Los pasos
siguientes describen cómo llevar a cabo varias tareas comunes de administración de base de
datos a través de Management Studio. Para realizar estas tareas, asegúrese de que está conectado a la base de datos
**maestra** con el inicio de sesión principal de nivel de servidor que ha
creado al configurar el servidor.

Para abrir una ventana de consulta en Management Studio, abra la carpeta Bases de datos, expanda la carpeta **Bases de datos del sistema**, haga clic con el botón secundario en **maestra** y, a continuación, haga clic en **Nueva consulta**.

-   Use la instrucción **CREAR BASE DE DATOS** para crear una base de datos nueva. Para obtener
    más información, consulte [CREAR BASE DE DATOS (Base de datos SQL)][]. La instrucción siguiente crea una nueva base de datos con el nombre
    **myTestDB** y especifica que se trata de una base de datos Web Edition
    con un tamaño máximo de 1 GB.

        CREATE DATABASE myTestDB
        (MAXSIZE=1GB,
         EDITION='web');

Haga clic en **Ejecutar** para ejecutar la consulta.

-   Use la instrucción **MODIFICAR BASE DE DATOS** para modificar una base de datos existente,
    por ejemplo, si desea cambiar el nombre, el tamaño máximo o la edición
    (empresarial o web) de la base de datos. Para obtener más información, consulte [ALTER DATABASE (Base de datos SQL)][]. La
    instrucción siguiente modifica la base de datos que creó en el paso anterior
    para cambiar el tamaño máximo a 5 GB.

        ALTER DATABASE myTestDB
        MODIFY
        (MAXSIZE=5GB,
         EDITION='web');

-   Use la instrucción **QUITAR BASE DE DATOS** para eliminar una base de datos existente.
    Para obtener más información, consulte [QUITAR BASE DE DATOS (Base de datos SQL)][]. La instrucción siguiente elimina la base de datos **myTestDB**
    pero no la quita ahora porque la utilizará para crear inicios de sesión en el paso siguiente.

        DROP DATABASE myTestBase;

-   La base de datos maestra tiene la vista **sys.databases** que puede usar para ver
    detalles sobre todas las bases de datos. Para ver todas las bases de datos existentes,
    ejecute la siguiente instrucción:

        SELECT * FROM sys.databases;

-   En Base de datos SQL, la instrucción **USAR** no es compatible para cambiar
    entre bases de datos. En su lugar, debe establecer una conexión
    directamente con la base de datos de destino.

<div class="dev-callout-new">
 <strong>Nota: <span>Click to collapse</span></strong>
 <div class="dev-callout-content">
   <p>Muchas de las instrucciones de Transact-SQL que crean o modifican una
base de datos deben ejecutarse en su propio lote y no se pueden agrupar con
otras instrucciones Transact-SQL. Para obtener más información, consulte la información específica de las instrucciones disponible en los vínculos mencionados anteriormente.</p>
</div>
</div>

<h2><a id="Step4" name="Step4"> </a>Paso 4: Crear y administrar los inicios de sesión</h2>

La base de datos **maestra** realiza un seguimiento de los inicios de sesión y los inicios de sesión tienen
permiso para crear bases de datos u otros inicios de sesión. Administrar inicios de sesión mediante
una conexión con la base de datos **maestra** con el inicio de sesión principal de nivel de servidor
creado al configurar el servidor. Puede usar las instrucciones
**CREAR INICIO DE SESIÓN**, **MODIFICAR INICIO DE SESIÓN** o **QUITAR INICIO DE SESIÓN** para
ejecutar consultas en la base de datos maestra que administrará los inicios de sesión
en todo el servidor. Para obtener más información, consulte [Administrar bases de datos e inicios de sesión en Base de datos SQL][]. 


-   Use la instrucción **CREAR INICIO DE SESIÓN** para crear un inicio de sesión nuevo
    a nivel de servidor. Para obtener más información, consulte [CREATE LOGIN (Base de datos SQL)][]. La instrucción siguiente crea un nuevo inicio de sesión
    llamado **login1**. Reemplace **password1** por la contraseña
    que desee.

        CREATE LOGIN login1 WITH password='password1';

-   Use la instrucción **CREAR USUARIO** para conceder permisos
    a nivel de base de datos. Todos los inicios de sesión deben crearse en la base de datos **maestra**,
    pero para que un inicio de sesión se conecte a una base de datos diferente, deben
    concedérsele permisos de nivel de base de datos mediante la instrucción **CREAR USUARIO**
    en dicha base de datos. Para obtener más información, consulte [CREATE USER (Base de datos SQL)][]. 

-   Para conceder permisos login1
    a una base de datos llamada **myTestDB**, siga estos
    pasos:

 1.  Para actualizar el Explorador de objetos para ver la base de datos **myTestDB** que acaba de crear haga clic con el botón secundario en el nombre del servidor en el Explorador de objetos y a continuación haga clic en **Actualizar**.  

     Si ha cerrado la conexión, puede volver a establecerla si selecciona **Conectar Explorador de objetos** en el menú Archivo. Repita las instrucciones del [Paso 2: Conectarse a Base de datos SQL][] para conectarse a la base de datos.

 2. Haga clic con el botón secundario en la base de datos **myTestDB** y seleccione **Nueva consulta**.

    3.  Ejecute la instrucción siguiente en la base de datos myTestDB para
        crear un usuario de base de datos llamado **login1User** que se corresponda con el
        inicio de sesión de nivel de servidor **login1**.

            CREATE USER login1User FROM LOGIN login1;

-   Use el procedimiento almacenado **sp\_addrolemember** para dar a la cuenta de usuario
    el nivel apropiado de permisos sobre la base de datos. Para obtener
    más información, consulte [sp_addrolemember (Transact-SQL)][]. La instrucción siguiente otorga permisos de solo lectura **login1User**
    para la base de datos al agregar **login1User** al
    rol **db\_datareader**.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Use la instrucción **MODIFICAR INICIO DE SESIÓN** para modificar el inicio de sesión existente, por ejemplo
    si desea cambiar la contraseña para el inicio de sesión. Para obtener
    más información, consulte [MODIFICAR INICIO DE SESIÓN (Base de datos SQL)][]. La instrucción **MODIFICAR INICIO DE SESIÓN** debe ejecutarse en
    la base de datos **maestra**. Vuelva a la ventana de consulta conectada a la base de datos. 

    La instrucción siguiente modifica el inicio de sesión **login1** para restablecer la contraseña.
    Reemplace **newPassword** con la contraseña que desee y
    **oldPassword** con la contraseña actual para el inicio de sesión.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Use la instrucción **QUITAR INICIO DE SESIÓN** para eliminar el inicio de sesión existente.
    La eliminación de un inicio de sesión en el nivel de servidor también elimina las
    cuentas de usuario asociadas de la base de datos. Para obtener más información,
    consulte [QUITAR BASE DE DATOS (Base de datos SQL)][]. La instrucción **QUITAR INICIO DE SESIÓN**
    debe ejecutarse en la base de datos **maestra**. La
    instrucción siguiente elimina el inicio de sesión **login1**.

        DROP LOGIN login1;

-   La base de datos maestra tiene la vista **sys.sql\_logins** que puede usar
    para ver inicios de sesión. Para ver todos los inicios de sesión existentes, ejecute la instrucción
    siguiente:

        SELECT * FROM sys.sql_logins;

<h2><a id="Step5" name="Step5"> </a>Paso 5: Supervisar Base de datos SQL mediante las vistas de administración dinámica</h2>

Base de datos SQL admite varias vistas de administración dinámica que puede
utilizar para supervisar una base de datos individual. A continuación se muestran algunos ejemplos del
tipo de datos de supervisión que se pueden recuperar a través de estas vistas. Para obtener
todos los detalles y más ejemplos de uso, consulte [Supervisar Base de datos SQL de Azure mediante vistas de administración dinámica][].

-   Para consultar una vista de administración dinámica, es necesario disponer de los permisos **VER ESTADO DE BASE DE DATOS**
    . Para conceder el permiso **VER ESTADO DE BASE DE DATOS** a un
    usuario de base de datos específico, conéctese a la base de datos que desea administrar
    con el inicio de sesión principal a nivel de servidor y ejecute la siguiente
    instrucción en la base de datos:

        GRANT VIEW DATABASE STATE TO login1User;

-   Calcule el tamaño de la base de datos con la vista **sys.dm\_db\_partition\_stats**
    . La vista **sys.dm\_db\_partition\_stats** devuelve información de página y
    recuento de filas para cada partición de la base de datos, que se puede
    usar para calcular el tamaño de la base de datos. La consulta siguiente devuelve
    el tamaño de la base de datos en megabytes:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Use las vistas **sys.dm\_exec\_connections** y **sys.dm\_exec\_sessions**
    para recuperar información acerca de las conexiones de usuario actuales y
    de las tareas internas asociadas con la base de datos. La siguiente consulta
    devuelve información acerca de la conexión actual:

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   Use la vista **sys.dm\_exec\_query\_stats** para recuperar estadísticas de rendimiento agregadas
    para planes de consulta en caché. La siguiente consulta
    devuelve información acerca de las cinco primeras consultas clasificadas por promedio
    de tiempo de CPU.

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;

<h2>Recursos adicionales</h2>

* [Introducción a Base de datos de SQL][]   
* [Administrar bases de datos e inicios de sesión en Base de datos SQL][]   
* [Supervisar Base de datos SQL de Azure mediante vistas de administración dinámica][]   
* [Modelo de aprovisionamiento de Base de datos SQL][]   
* [Agregar usuarios a la Base de datos SQL][]   
* [Referencia de Transact-SQL (Base de datos SQL)][]

  [Uso de Base de datos SQL de Azure]: http://www.windowsazure.com/es-es/develop/net/how-to-guides/sql-azure/
  [Paso 1: Obtener SQL Server Management Studio]: #Step1
  [Paso 2: Conectarse a Base de datos SQL]: #Step2
  [Paso 3: Crear y administrar las bases de datos]: #Step3
  [Paso 4: Crear y administrar los inicios de sesión]: #Step4
  [Paso 5: Supervisar Base de datos SQL mediante las vistas de administración dinámica]:
    #Paso 5
  [Microsoft SQL Server 2012 Express]: http://www.microsoft.com/es-es/download/details.aspx?id=29062
  [Instalador de SSMS - Seleccionar el tipo de instalación]: /media/installer_installation_type.png
  [Instalador de SSMS - Seleccionar características]: /media/installer_feature_selection.png
  [Instalador de SSMS - Instalación completada]: /media/installer_completed.png
  [Portal de administración de Azure]: http://manage.windowsazure.com/
  [Obtener el nombre del servidor de Base de datos SQL desde el Portal de administración]: /media/portal_get_database_name.png
  [Conexión a SSMS]: /media/ssms_connect.png
  [Conexión a SSMS -- propiedades]: /media/ssms_connect_properties.png
  [Referencia de Transact-SQL (Base de datos SQL)]: http://msdn.microsoft.com/es-es/library/bb510741(v=sql.120).aspx
  [CREAR BASE DE DATOS (Base de datos SQL)]: http://msdn.microsoft.com/es-es/library/windowsazure/ee336274.aspx
  [MODIFICAR BASE DE DATOS (Base de datos SQL)]: http://msdn.microsoft.com/es-es/library/windowsazure/ff394109.aspx
  [QUITAR BASE DE DATOS (Base de datos SQL)]: http://msdn.microsoft.com/es-es/library/windowsazure/ee336259.aspx
  [Administrar bases de datos e inicios de sesión en Base de datos SQL]: http://msdn.microsoft.com/es-es/library/windowsazure/ee336235.aspx
  [CREAR INICIO DE SESIÓN (Base de datos SQL)]: http://msdn.microsoft.com/es-es/library/windowsazure/ee336268.aspx
  [CREAR USUARIO (Base de datos SQL)]: http://msdn.microsoft.com/es-es/library/ee336277.aspx
  [sp_addrolemember (Transact-SQL)]: http://msdn.microsoft.com/es-es/library/ms187750.aspx
  [MODIFICAR INICIO DE SESIÓN (Base de datos SQL)]: http://msdn.microsoft.com/es-es/library/windowsazure/ee336254.aspx
  [Supervisar Base de datos SQL de Azure mediante vistas de administración dinámica]: http://msdn.microsoft.com/es-es/library/windowsazure/ff394114.aspx
  [Introducción a Base de datos de SQL]: http://msdn.microsoft.com/es-es/library/windowsazure/ee336230.aspx
  [Modelo de aprovisionamiento de Base de datos SQL]: http://msdn.microsoft.com/es-es/library/ee336227.aspx
  [Agregar usuarios a la Base de datos SQL]: http://blogs.msdn.com/b/sqlazure/archive/2010/06/21/10028038.aspx
