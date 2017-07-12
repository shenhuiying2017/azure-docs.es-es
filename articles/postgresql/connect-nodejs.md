---
title: "Conexión a Azure Database for PostgreSQL desde Node.js | Microsoft Docs"
description: "En este tutorial rápido se proporciona un ejemplo de código de Node.js que puede usar para conectarse y consultar datos desde Azure Database for PostgreSQL."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 06/23/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 6a51fcf4f4494e5b32ccf6dabb19f8d004bb20d4
ms.contentlocale: es-es
ms.lasthandoff: 06/26/2017

---

<a id="azure-database-for-postgresql-use-nodejs-to-connect-and-query-data" class="xliff"></a>

# Azure Database for PostgreSQL: uso de Node.js para conectarse y consultar datos
En este tutorial rápido se muestra cómo conectarse a una instancia de Azure Database for PostgreSQL mediante [Node.js](https://nodejs.org/) desde las plataformas Windows, Ubuntu Linux y Mac. Se indica cómo usar instrucciones SQL para consultar, insertar, actualizar y eliminar datos en la base de datos. En los pasos de este artículo se da por hecho que está familiarizado con el desarrollo mediante Node.js, pero que nunca ha trabajado con Azure Database for PostgreSQL.

<a id="prerequisites" class="xliff"></a>

## Requisitos previos
En este tutorial rápido se usan como punto de partida los recursos creados en una de estas guías:
- [Creación de la base de datos: Azure Portal](quickstart-create-server-database-portal.md)
- [Creación de la base de datos: CLI](quickstart-create-server-database-azure-cli.md)

Además, deberá:
- Instalar [Node.js](https://nodejs.org)
- Instalar el paquete [pg](https://www.npmjs.com/package/pg). 

<a id="install-nodejs" class="xliff"></a>

## Instalación de Node.js 
Dependiendo de la plataforma, para instalar Node.js:

<a id="mac-os" class="xliff"></a>

### **Mac OS**
Escriba los comandos siguientes para instalar **brew**, un administrador de paquetes fácil de usar para Mac OS X y **Node.js**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

<a id="linux-ubuntu" class="xliff"></a>

### **Linux (Ubuntu)**
Escriba los comandos siguientes para instalar **Node.js** y **npm**, el administrador de paquetes para Node.js.

```bash
sudo apt-get install -y nodejs npm
```

<a id="windows" class="xliff"></a>

### **Windows**
Visite la [página de descargas de Node.js](https://nodejs.org/en/download/) y seleccione la opción deseada de Windows Installer.

<a id="install-pg-client" class="xliff"></a>

## Instalación del cliente pg
Instale [pg](https://www.npmjs.com/package/pg), que es un cliente de desbloqueo de JavaScript puro para node.js que resulta útil para conectarse a PostgreSQL y realizar consultas en ella.

Para ello, ejecute el Administrador de paquetes de Node (npm) para JavaScript desde la línea de comandos.
```bash
npm install pg
```

Compruebe la instalación; para ello, enumere los paquetes instalados.
```bash
npm list
```
La salida del comando list confirma la versión de cada componente. 
```
`-- pg@6.2.3
  +-- buffer-writer@1.0.1
  +-- packet-reader@0.3.1
etc...
```

<a id="get-connection-information" class="xliff"></a>

## Obtención de información sobre la conexión
Obtenga la información de conexión necesaria para conectarse a Azure Database for PostgreSQL. Necesitará el nombre completo del servidor y las credenciales de inicio de sesión.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. En el menú izquierdo de Azure Portal, haga clic en **Todos los recursos** y busque el servidor **mypgserver-20170401** que acaba de crear.
3. Haga clic en el nombre del servidor **mypgserver-20170401**.
4. Seleccione la página **Introducción** del servidor. Tome nota del **Nombre del servidor** y del **Server admin login name** (Nombre de inicio de sesión del administrador del servidor).
 ![Azure Database for PostgreSQL: inicio de sesión del Administrador del servidor](./media/connect-nodejs/1-connection-string.png)
5. Si olvida la información de inicio de sesión del servidor, navegue hasta la página **Información general** para ver el nombre de inicio de sesión del administrador del servidor y, si es necesario, restablecer la contraseña.

<a id="running-the-javascript-code-in-nodejs" class="xliff"></a>

## Ejecución del código de JavaScript en Node.js
Puede iniciar Node.js desde el shell de Bash o el símbolo del sistema de Windows; para ello, escriba `node` y luego copie y pegue el ejemplo de código de JavaScript para ejecutarlo de manera interactiva. También puede guardar el código de JavaScript en un archivo de texto e iniciar `node filename.js` con el nombre de archivo como parámetro para ejecutarlo.

<a id="connect-create-table-and-insert-data" class="xliff"></a>

## Conexión, creación de una tabla e inserción de datos
Use el código siguiente para conectarse y cargar los datos mediante las instrucciones SQL **CREATE TABLE** e **INSERT INTO**.
El objeto [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) se usa para interactuar con el servidor de PostgreSQL. La función [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) se usa para establecer la conexión al servidor. La función [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) se usa para ejecutar la consulta SQL en la base de datos de PostgreSQL. 

Reemplace los parámetros host, dbname, user y password por los valores especificados al crear el servidor y la base de datos. 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }
});

function queryDatabase()
{
    client.query(
        ' \
            DROP TABLE IF EXISTS inventory; \
            CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER); \
            INSERT INTO inventory (name, quantity) VALUES (\'banana\', 150); \
            INSERT INTO inventory (name, quantity) VALUES (\'orange\', 154); \
            INSERT INTO inventory (name, quantity) VALUES (\'apple\', 100); \
        ',
        function (err)
    {
        console.log("Connection established");

        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;

                // Else closing connection finished without error
                console.log("Closed client connection");
            });

            console.log("Finished execution, exiting now");
            process.exit()
        }
    });
}
```

<a id="read-data" class="xliff"></a>

## Lectura de datos
Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL **SELECT**. El objeto [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) se usa para interactuar con el servidor de PostgreSQL. La función [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) se usa para establecer la conexión al servidor. La función [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) se usa para ejecutar la consulta SQL en la base de datos de PostgreSQL. 

Reemplace los parámetros host, dbname, user y password por los valores especificados al crear el servidor y la base de datos. 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};


const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;

    else
    {
        console.log("Connected to Azure Database for PostgreSQL server:" + config.host);
        queryDatabase();
    }
});

function queryDatabase()
{
    // Declare array to hold query result set
    const results = [];

    console.log("Running query to PostgreSQL server:" + config.host);

    // Perform query
    var query = client.query('SELECT * FROM inventory;');

    // Print result set
    query.on('row', function(row)
    {
        console.log("Read " + JSON.stringify(row));
    });

    // Exit program after execution
    query.on('end', function(row)
    {
        console.log("Finished execution, exiting now");
        process.exit()
    });
}
```

<a id="update-data" class="xliff"></a>

## Actualización de datos
Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL **UPDATE**. El objeto [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) se usa para interactuar con el servidor de PostgreSQL. La función [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) se usa para establecer la conexión al servidor. La función [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) se usa para ejecutar la consulta SQL en la base de datos de PostgreSQL. 

Reemplace los parámetros host, dbname, user y password por los valores especificados al crear el servidor y la base de datos. 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }   
});

function queryDatabase()
{
    client.query('UPDATE inventory SET quantity= 1000 WHERE name=\'banana\';', function (err, result)
    {
        console.log("Connection established");

        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;
                
                // Else closing connection finished without error
                console.log("Closed client connection");
            });             
        }

        console.log("Finished execution, exiting now");
        process.exit()
    });
}
```

<a id="delete-data" class="xliff"></a>

## Eliminación de datos
Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL **DELETE**. El objeto [pg.Client](https://github.com/brianc/node-postgres/wiki/Client) se usa para interactuar con el servidor de PostgreSQL. La función [pg.Client.connect()](https://github.com/brianc/node-postgres/wiki/Client#method-connect) se usa para establecer la conexión al servidor. La función [pg.Client.query()](https://github.com/brianc/node-postgres/wiki/Query) se usa para ejecutar la consulta SQL en la base de datos de PostgreSQL. 

Reemplace los parámetros host, dbname, user y password por los valores especificados al crear el servidor y la base de datos. 

```javascript
const pg = require('pg');

var config =
{
    host: 'mypgserver-20170401.postgres.database.azure.com',
    user: 'mylogin@mypgserver-20170401',
    password: '<server_admin_password>',
    database: 'mypgsqldb',
    port: 5432,
    ssl: true
};

const client = new pg.Client(config);

client.connect(function (err)
{
    if (err)
        throw err;
    else
    {
        queryDatabase();
    }   
});

function queryDatabase()
{
    client.query('DELETE FROM inventory WHERE name=\'apple\';', function (err, result)
    {
        console.log("Connection established");
        
        if (err)
            throw err;
        else
        {
            client.end(function (err)
            {
                if (err)
                    throw err;
                
                // Else closing connection finished without error
                console.log("Closed client connection");
            });

            console.log("Finished execution, exiting now");
            process.exit()          
        }
    }); 
}
```

<a id="next-steps" class="xliff"></a>

## Pasos siguientes
> [!div class="nextstepaction"]
> [Migración de una base de datos mediante exportación e importación](./howto-migrate-using-export-and-import.md)

