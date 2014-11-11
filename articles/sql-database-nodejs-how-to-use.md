<properties linkid="develop-node-how-to-sql-database" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (Node.js) - Azure feature guide" metaKeywords="" description="Learn how to use Azure SQL Database from Node.js." metaCanonical="" services="sql-database" documentationCenter="nodejs" title="How to Access Azure SQL Database from Node.js" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr" />

# Acceso a una base de datos SQL de Azure desde Node.js

En esta guía se muestran los aspectos básicos del uso de Microsoft Driver para Node.JS en SQL Server a fin de obtener acceso a una base de datos SQL de Azure. Entre los escenarios descritos se incluyen la **creación de una base de datos SQL** y la **conexión a una base de datos SQL**. En esta guía abordaremos la creación de una base de datos SQL desde el [Portal de administración de Azure][Portal de administración de Azure].

## Tabla de contenido

-   [Conceptos][Conceptos]
-   [Direccionamiento del del entorno][Direccionamiento del del entorno]
-   [Direccionamiento del de una Base de datos SQL][Direccionamiento del de una Base de datos SQL]
-   [Direccionamiento del de información de conexión de la Base de datos SQL][Direccionamiento del de información de conexión de la Base de datos SQL]
-   [Direccionamiento del a una instancia de Base de datos SQL][Direccionamiento del a una instancia de Base de datos SQL]
-   [Consideraciones de la implementación de Azure][Consideraciones de la implementación de Azure]
-   [Pasos siguientes][Pasos siguientes]

## <span id="Concepts"></span></a>Conceptos

### Qué es una base de datos SQL de Azure

Una base de datos SQL de Azure proporciona un sistema de administración de bases de datos relacionales para Azure y se basa en la tecnología de SQL Server. Con una Base de datos SQL se pueden aprovisionar e implementar soluciones de bases de datos relacionales en la nube con toda facilidad, así como beneficiarse de un centro de datos distribuido que ofrece disponibilidad, escalabilidad y seguridad de clase empresarial con las ventajas de contar con funciones de protección de datos y recuperación automática integradas.

## Qué es Microsoft Driver para Node.JS en SQL Server

Microsoft Driver para Node.JS en SQL Server permite a los desarrolladores obtener acceso a la información almacenada en una base de datos de Microsoft SQL Server o SQL de Azure desde una aplicación Node.js. Actualmente, el controlador solo se encuentra disponible como versión preliminar. A medida que se completen características adicionales, se irán integrando en el proyecto. Para obtener más información acerca de este controlador, consulte la [página Github][página Github] (en inglés) del proyecto de Microsoft Driver para Node.JS en SQL Server y el [wiki][wiki] asociado.

<div class="dev-callout">
<b>Nota:</b>
<p>Actualmente, Microsoft Driver para Node.JS de SQL Server est&aacute; a disposici&oacute;n de los usuarios como versi&oacute;n preliminar y se basa en componentes en tiempo de ejecuci&oacute;n solamente disponibles en los sistemas operativos Microsoft Windows y Azure.</p>
</div>

## <span id="Setup"></span></a>Direccionamiento del del entorno

### Instalar SQL Server Native Client

El controlador de Microsoft SQL Server para Node.js se basa en SQL Server Native Client. Si bien el cliente nativo está disponible automáticamente al implementar la aplicación en Azure, es posible que esté presente en el entorno de desarrollo local. Puede instalar SQL Server Native Client desde la página de descarga de [Microsoft SQL Server 2012 Feature Pack][Microsoft SQL Server 2012 Feature Pack].

<div class="dev-callout">
<b>Nota:</b>
<p>SQL Server Native Client solo est&aacute; disponible para el sistema operativo Microsoft Windows. Si bien este controlador se encuentra disponible en Azure de manera nativa, la aplicaci&oacute;n no se podr&aacute; probar localmente usando la informaci&oacute;n de este art&iacute;culo si las tareas de desarrollo se realizan en un sistema operativo distinto de Microsoft Windows.</p>
</div>

### Instalar Node.js

Node.js se puede instalar desde [][]<http://nodejs.org/#download></a>. En caso de no haber disponible un paquete de instalación para su sistema operativo, puede compilar Node.js a partir del código fuente.

## <span id="CreateServer"></span></a>Direccionamiento del una Base de datos SQL

Siga estos pasos para crear una base de datos SQL de Azure:

1.  Inicie sesión en el [Portal de administración][Portal de administración de Azure].
2.  Haga clic en el icono **+ New**, situado en la parte inferior izquierda del portal.

    ![Crear un sitio web de Azure][Crear un sitio web de Azure]

3.  Haga clic en **Base de datos SQL** y, a continuación, en **Custom Create**.

    ![Crear una Base de datos SQL personalizada][Crear una Base de datos SQL personalizada]

4.  Escriba el valor **NAME** de la base de datos, seleccione el tipo de **EDITION** (WEB o BUSINESS), el valor **MAX SIZE** de la base de datos, la clase de **COLLATION** y, por último, seleccione **NEW SQL Database Server**. Haga clic en la flecha que aparece en la parte inferior del cuadro de diálogo. (Tenga en cuenta que si ha creado una Base de datos SQL anteriormente, puede elegir un servidor ya existente en la lista desplegable **Choose a server**).

    ![Rellenar configuración de la base de datos SQL][Rellenar configuración de la base de datos SQL]

5.  Especifique un nombre y una contraseña de administrador (y confirme la contraseña), elija la región donde se creará la Base de datos SQL y active la casilla `Allow Azure Services to access the server`.

    ![Crear servidor de base de datos SQL][Crear servidor de base de datos SQL]

Para ver la información del servidor y de la base de datos, haga clic en la opción **SQL Databases** del Portal de administración de Azure. A continuación, puede hacer clic en **DATABASES** o **SERVERS** para ver la información pertinente.

![Ver información del servidor y de la base de datos][Ver información del servidor y de la base de datos]

## <span id="ConnectionInfo"></span></a>Direccionamiento del Obtención de información de conexión de la Base de datos SQL

Para obtener información de conexión de la Base de datos SQL, haga clic en la opción **SQL DATABASES** del portal y, después, en el nombre de la base de datos.

![Ver información de la base de datos][Ver información de la base de datos]

A continuación, haga clic en **Show connection strings**.

![Mostrar cadenas de conexión][Mostrar cadenas de conexión]

Anote los valores de la cadena de conexión que aparecen en la sección ODBC de la ventana resultante. Esta es la cadena de conexión que se usará al conectarse a la base de datos SQL desde la aplicación Node. La contraseña será la misma que se utilizó al crear la Base de datos SQL.

## <span id="Connect"></span></a>Direccionamiento del a una instancia de Base de datos SQL

### Instalar node-sqlserver

Microsoft Driver para Node.JS en SQL Server está disponible como módulo nativo node-sqlserver. En el [centro de descarga][centro de descarga] puede encontrarse una versión binaria de este módulo. Para usar dicha versión, realice estos pasos:

1.  Extraiga el archivo binario en el directorio **node\_modules** de la aplicación.
2.  Ejecute el archivo **node-sqlserver-install.cmd** extraído. Al hacerlo, se creará un subdirectorio **node-sqlserver** bajo **node\_modules** y los archivos del controlador se trasladarán a esta nueva estructura de directorios.
3.  Elimine el archivo **node-sqlserver-install.cmd**, puesto que ya no es necesario.

<div class="dev-callout">
<b>Nota:</b>
<p>El m&oacute;dulo node-sqlserver tambi&eacute;n se puede instalar mediante la utilidad npm. Sin embargo, este m&eacute;todo invocar&aacute; a node-gyp para compilar una versi&oacute;n binaria del m&oacute;dulo en el sistema.</p>
</div>

### Especificar la cadena de conexión

Para usar node-sqlserver, debe solicitarlo en la aplicación y especificar una cadena de conexión. Dicha cadena debe ser el valor ODBC devuelto en la sección [Obtención de información de conexión de la base de datos SQL][Direccionamiento del de información de conexión de la Base de datos SQL] de este artículo. El código que aparece debe ser similar al siguiente:

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

## <span id="Deploy"></span></a>Consideraciones de la implementación de Azure

<div class="dev-callout">
<b>Nota:</b>
<p>La informaci&oacute;n siguiente se basa en una versi&oacute;n preliminar de Microsoft Driver para Node.JS en SQL Server. Puede que los datos incluidos en esta secci&oacute;n no sean los m&aacute;s recientes sobre el uso del m&oacute;dulo node-sqlserver con Azure. Para obtener la informaci&oacute;n m&aacute;s reciente, visite la <a href="https://github.com/WindowsAzure/node-sqlserver">p&aacute;gina de proyecto de Microsoft Driver para Node.JS en SQL Server</a> (en ingl&eacute;s) en Github.</p>
</div>

Azure no instalará de forma dinámica el módulo node-sqlserver en tiempo de ejecución, por lo que debe asegurarse de que la implementación de la aplicación incluya una versión binaria del módulo. Para comprobar que la implementación contiene una versión binaria de este, asegúrese de que exista la siguiente estructura de directorios y de que contenga los archivos que se describen a continuación:

    application directory
        node_modules
            node-sqlserver
                lib

El directorio **node-sqlserver** debe contener un archivo **package.json**. El directorio **lib** debe contener los archivos **sql.js** y **sqlserver.node**, que son la forma compilada del módulo node-sqlserver.

Para obtener más información sobre cómo implementar una aplicación Node.js en Azure, consulte [Creación e implementación de una aplicación Node.js en sitios web de Azure][Creación e implementación de una aplicación Node.js en sitios web de Azure] y [Servicio en la nube Node.js][Servicio en la nube Node.js].

## <span id="NextSteps"></span></a>Pasos siguientes

-   [Presentación de Microsoft Driver para Node.JS en SQL Server][Presentación de Microsoft Driver para Node.JS en SQL Server]
-   [Microsoft Driver para Node.js para SQL Server en Github.com][página Github]

  [Portal de administración de Azure]: https://manage.windowsazure.com
  [Conceptos]: #Concepts
  [Direccionamiento del del entorno]: #Setup
  [Direccionamiento del de una Base de datos SQL]: #CreateServer
  [Direccionamiento del de información de conexión de la Base de datos SQL]: #ConnectionInfo
  [Direccionamiento del a una instancia de Base de datos SQL]: #Connect
  [Consideraciones de la implementación de Azure]: #Deploy
  [Pasos siguientes]: #NextSteps
  [página Github]: https://github.com/WindowsAzure/node-sqlserver
  [wiki]: https://github.com/WindowsAzure/node-sqlserver/wiki
  [Microsoft SQL Server 2012 Feature Pack]: http://www.microsoft.com/es-es/download/details.aspx?id=29065
  []: http://nodejs.org/#download
  [Crear un sitio web de Azure]: ./media/sql-database-nodejs-how-to-use/new_website.jpg
  [Crear una Base de datos SQL personalizada]: ./media/sql-database-nodejs-how-to-use/create_custom_sql_db.jpg
  [Rellenar configuración de la base de datos SQL]: ./media/sql-database-nodejs-how-to-use/new-sql-db.png
  [Crear servidor de base de datos SQL]: ./media/sql-database-nodejs-how-to-use/db-server-settings.png
  [Ver información del servidor y de la base de datos]: ./media/sql-database-nodejs-how-to-use/sql-dbs-portal.png
  [Ver información de la base de datos]: ./media/sql-database-nodejs-how-to-use/go-to-db-info.png
  [Mostrar cadenas de conexión]: ./media/sql-database-nodejs-how-to-use/show-connection-string.png
  [centro de descarga]: http://www.microsoft.com/es-es/download/details.aspx?id=29995
  [Creación e implementación de una aplicación Node.js en sitios web de Azure]: /es-es/develop/nodejs/tutorials/create-a-website-(mac)/
  [Servicio en la nube Node.js]: /es-es/develop/nodejs/tutorials/getting-started/
  [Presentación de Microsoft Driver para Node.JS en SQL Server]: http://blogs.msdn.com/b/sqlphp/archive/2012/06/08/introducing-the-microsoft-driver-for-node-js-for-sql-server.aspx
