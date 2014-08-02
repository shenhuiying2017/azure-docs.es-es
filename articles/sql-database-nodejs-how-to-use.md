<properties linkid="develop-node-how-to-sql-database" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (Node.js) - Azure feature guide" metaKeywords="" description="Learn how to use Azure SQL Database from Node.js." metaCanonical="" services="sql-database" documentationCenter="Node.js" title="How to Access Azure SQL Database from Node.js" authors="" solutions="" manager="" editor="" />

Acceso a una base de datos SQL de Azure desde Node.js
=====================================================

En esta guía se muestran los aspectos básicos del uso de Microsoft Driver para Node.JS en SQL Server a fin de obtener acceso a una base de datos SQL de Azure. Entre los escenarios descritos se incluyen la **creación de una base de datos SQL** y la **conexión a una base de datos SQL**. Abordaremos la creación de una base de datos SQL desde el [Portal de administración de vista previa](https://manage.windowsazure.com).
 
Tabla de contenido
------------------

-   [Conceptos](#Concepts)
-   [Configuración del entorno](#Setup)
-   [Creación de una base de datos SQL](#CreateServer)
-   [Obtención de información de conexión de la base de datos SQL](#ConnectionInfo)
-   [Conexión a una instancia de base de datos SQL](#Connect)
-   [Consideraciones de la implementación de Azure](#Deploy)
-   [Pasos siguientes](#NextSteps)

Conceptos
---------

### Qué es una base de datos SQL de Azure

Una base de datos SQL de Azure proporciona un sistema de administración de bases de datos relacionales para Azure y se basa en la tecnología de SQL Server. Con una base de datos SQL se pueden aprovisionar e implementar soluciones de bases de datos relacionales en la nube con toda facilidad, así como beneficiarse de un centro de datos distribuido que ofrece disponibilidad, escalabilidad y seguridad de clase empresarial con las ventajas de contar con funciones de protección de datos y recuperación automática integradas.

Qué es Microsoft Driver para Node.JS en SQL Server
--------------------------------------------------

Microsoft Driver para Node.JS en SQL Server permite a los desarrolladores obtener acceso a la información almacenada en una base de datos de Microsoft SQL Server o SQL de Azure desde una aplicación Node.js. Actualmente, el controlador solo se encuentra disponible como versión preliminar. A medida que se completen características adicionales, se irán integrando en el proyecto. Para obtener más información acerca de este controlador, consulte la [página Github](https://github.com/WindowsAzure/node-sqlserver) (en inglés) del proyecto de Microsoft Driver para Node.JS en SQL Server y el [wiki](https://github.com/WindowsAzure/node-sqlserver/wiki) asociado.

**Nota:**

Actualmente, Microsoft Driver para Node.JS de SQL Server está a disposición de los usuarios como versión preliminar y se basa en componentes en tiempo de ejecución solamente disponibles en los sistemas operativos Microsoft Windows y Azure.

Configuración del entorno
-------------------------

### Instalar SQL Server Native Client

El controlador de Microsoft SQL Server para Node.js se basa en SQL Server Native Client. Si bien el cliente nativo está disponible automáticamente al implementar la aplicación en Azure, es posible que esté presente en el entorno de desarrollo local. Puede instalar SQL Server Native Client desde la página de descarga de [Microsoft SQL Server 2012 Feature Pack](http://www.microsoft.com/en-us/download/details.aspx?id=29065).

**Nota:**

SQL Server Native Client solo está disponible para el sistema operativo Microsoft Windows. Si bien este controlador se encuentra disponible en Azure de manera nativa, la aplicación no se podrá probar localmente usando la información de este artículo si las tareas de desarrollo se realizan en un sistema operativo distinto de Microsoft Windows.

### Instalar Node.js

Node.js se puede instalar desde <http://nodejs.org/#download>. En caso de no haber disponible un paquete de instalación para su sistema operativo, puede compilar Node.js a partir del código fuente.

Creación de una base de datos SQL
---------------------------------

Siga estos pasos para crear una base de datos SQL de Azure:

1.  Inicie sesión en el [Portal de administración de vista previa](https://manage.windowsazure.com).
2.  Haga clic en el icono **+ New**, situado en la parte inferior izquierda del portal.

    ![Crear un sitio web de Azure](./media/sql-database-nodejs-how-to-use/new_website.jpg)

3.  Haga clic en **Base de datos SQL** y, a continuación, en **Custom Create**.

    ![Crear una base de datos SQL personalizada](./media/sql-database-nodejs-how-to-use/create_custom_sql_db.jpg)

4.  Escriba el valor **NAME** de la base de datos, seleccione el tipo de **EDITION** (WEB o BUSINESS), el valor **MAX SIZE** de la base de datos, la clase de **COLLATION** y, por último, seleccione **NEW SQL Database Server**. Haga clic en la flecha que aparece en la parte inferior del cuadro de diálogo. (Tenga en cuenta que si ha creado una base de datos SQL anteriormente, puede elegir un servidor ya existente en la lista desplegable **Choose a server**).

    ![Rellenar configuración de la base de datos SQL](./media/sql-database-nodejs-how-to-use/new-sql-db.png)

5.  Especifique un nombre y una contraseña de administrador (y confirme la contraseña), elija la región donde se creará la base de datos SQL y active la casilla `Allow Azure Services to access the server`.

    ![Crear servidor de base de datos SQL](./media/sql-database-nodejs-how-to-use/db-server-settings.png)

Para ver la información del servidor y de la base de datos, haga clic en la opción **SQL Databases** del Portal de administración de vista previa. A continuación, puede hacer clic en **DATABASES** o **SERVERS** para ver la información pertinente.

![Ver información del servidor y de la base de datos](./media/sql-database-nodejs-how-to-use/sql-dbs-portal.png)

Obtención de información de conexión de la base de datos SQL
------------------------------------------------------------

Para obtener información de conexión de la base de datos SQL, haga clic en la opción **SQL DATABASES** del portal y, después, en el nombre de la base de datos.

![Ver información de la base de datos](./media/sql-database-nodejs-how-to-use/go-to-db-info.png)

A continuación, haga clic en **Show connection strings**.

![Mostrar cadenas de conexión](./media/sql-database-nodejs-how-to-use/show-connection-string.png)

Anote los valores de la cadena de conexión que aparecen en la sección ODBC de la ventana resultante. Esta es la cadena de conexión que se usará al conectarse a la base de datos SQL desde la aplicación Node. La contraseña será la misma que se utilizó al crear la base de datos SQL.

Conexión a una instancia de base de datos SQL
---------------------------------------------

### Instalar node-sqlserver

Microsoft Driver para Node.JS en SQL Server está disponible como módulo nativo node-sqlserver. En el [centro de descarga](http://www.microsoft.com/en-us/download/details.aspx?id=29995) puede encontrarse una versión binaria de este módulo. Para usar dicha versión, realice estos pasos:

1.  Extraiga el archivo binario en el directorio **node\_modules** de la aplicación.
2.  Ejecute el archivo **node-sqlserver-install.cmd** extraído. Al hacerlo, se creará un subdirectorio **node-sqlserver** bajo **node\_modules** y los archivos del controlador se trasladarán a esta nueva estructura de directorios.
3.  Elimine el archivo **node-sqlserver-install.cmd**, puesto que ya no es necesario.

**Nota:**

El módulo node-sqlserver también se puede instalar mediante la utilidad npm. Sin embargo, este método invocará a node-gyp para compilar una versión binaria del módulo en el sistema.

### Especificar la cadena de conexión

Para usar node-sqlserver, debe solicitarlo en la aplicación y especificar una cadena de conexión. Dicha cadena debe ser el valor ODBC devuelto en la sección [Obtención de información de conexión de la base de datos SQL](#ConnectionInfo) de este artículo. El código que aparece debe ser similar al siguiente:

    var sql = require('node-sqlserver');
    var conn_str = "Driver={SQL Server Native Client 10.0};Server=tcp:{dbservername}.database.windows.net,1433;Database={database};Uid={username};Pwd={password};Encrypt=yes;Connection Timeout=30;";

### Consultar la base de datos

Pueden realizarse consultas. Para ello, especifique una instrucción Transact-SQL con el método **query**. El código siguiente crea un servidor HTTP y devuelve datos de las filas **ID**, **Column1** y **Column2** de la tabla **Test** cuando se visualiza la página web:

    var http = require('http')
    var port = process.env.port||3000;
    http.createServer(function (req, res) {
        sql.query(conn_str, "SELECT * FROM TestTable", function (err, results) {
            if (err) {
                res.writeHead(500, { 'Content-Type': 'text/plain' });
                res.write("Got error :-( " + err);
                res.end("");
                return;
            }
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            for (var i = 0; i < results.length; i++) {
                res.write("ID: " + results[i].ID + " Column1: " + results[i].Column1 + " Column2: " + results[i].Column2);
            }
            res.end("; Done.");
        });
    }).listen(port);

Si bien el ejemplo anterior muestra cómo devolver todas las filas de una sola vez en la colección de resultados, también puede devolverse un objeto de instrucción que permita suscribirse a eventos. De esta forma, podrá recibir filas y columnas individuales a medida que se devuelven. En el ejemplo siguiente se muestra cómo realizar esta operación:

    var stmt = sql.query(conn_str, "SELECT * FROM TestTable");
    stmt.on('meta', function (meta) { console.log("We've received the metadata"); });
    stmt.on('row', function (idx) { console.log("We've started receiving a row"); });
    stmt.on('column', function (idx, data, more) { console.log(idx + ":" + data);});
    stmt.on('done', function () { console.log("All done!"); });
    stmt.on('error', function (err) { console.log("We had an error: " + err); });

Consideraciones de la implementación de Azure
---------------------------------------------

**Nota:**

La información siguiente se basa en una versión preliminar de Microsoft Driver para Node.JS en SQL Server. Puede que los datos incluidos en esta sección no sean los más recientes sobre el uso del módulo node-sqlserver con Azure. Para obtener la información más reciente, visite la [página de proyecto de Microsoft Driver para Node.JS en SQL Server](https://github.com/WindowsAzure/node-sqlserver) (en inglés) en Github.

Azure no instalará de forma dinámica el módulo node-sqlserver en tiempo de ejecución, por lo que debe asegurarse de que la implementación de la aplicación incluya una versión binaria del módulo. Para comprobar que la implementación contiene una versión binaria de este, asegúrese de que exista la siguiente estructura de directorios y de que contenga los archivos que se describen a continuación:

    application directory
        node_modules
            node-sqlserver
                lib

El directorio **node-sqlserver** debe contener un archivo **package.json**. El directorio **lib** debe contener los archivos **sql.js** y **sqlserver.node**, que son la forma compilada del módulo node-sqlserver.

Para obtener más información sobre cómo implementar una aplicación Node.js en Azure, consulte [Creación e implementación de una aplicación Node.js en sitios web de Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/) y [Servicio en la nube Node.js](/en-us/develop/nodejs/tutorials/getting-started/).

Pasos siguientes
----------------

-   [Presentación de Microsoft Driver para Node.JS en SQL Server](http://blogs.msdn.com/b/sqlphp/archive/2012/06/08/introducing-the-microsoft-driver-for-node-js-for-sql-server.aspx)
-   [Microsoft Driver para Node.js para SQL Server en Github.com](https://github.com/WindowsAzure/node-sqlserver)

