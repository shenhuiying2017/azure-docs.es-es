---
title: "Conexión a Azure Database for MySQL desde Node.js | Microsoft Docs"
description: "En este tutorial rápido se proporcionan ejemplos de código Node.js que se pueden usar para conectarse a Azure Database for MySQL y consultar datos en este servicio."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.custom: mvc
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: 2f18016614b229273aa4d661991149be949ce238
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-nodejs-to-connect-and-query-data"></a>Azure Database for MySQL: uso de Node.js para conectarse y consultar datos
En este tutorial rápido se muestra cómo conectarse a una instancia de Azure Database for MySQL mediante [Node.js](https://nodejs.org/) desde las plataformas Windows, Ubuntu Linux y Mac. Se indica cómo usar instrucciones SQL para consultar, insertar, actualizar y eliminar datos en la base de datos. En este tema se da por hecho que está familiarizado con el desarrollo mediante Node.js y que nunca ha usado Azure Database for MySQL.

## <a name="prerequisites"></a>Requisitos previos
En este tutorial rápido se usan como punto de partida los recursos creados en una de estas guías:
- [Create an Azure Database for MySQL server using Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md) (Creación de un servidor de Azure Database for MySQL mediante Azure Portal)
- [Create an Azure Database for MySQL server using Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) (Creación de un servidor de Azure Database for MySQL mediante la CLI de Azure)

Además, deberá:
- Instalar el entorno de tiempo de ejecución de [Node.js](https://nodejs.org).
- Instalar el paquete [mysql2](https://www.npmjs.com/package/mysql2) para conectarse a MySQL desde la aplicación Node.js. 

## <a name="install-nodejs-and-the-mysql-connector"></a>Instalación de Node.js y el conector de MySQL
Dependiendo de la plataforma, siga las instrucciones de la sección apropiada para instalar Node.js. Use npm para instalar el paquete de mysql2 y sus dependencias en la carpeta de proyecto.

### <a name="windows"></a>**Windows**
1. Visite la [página de descargas de Node.js](https://nodejs.org/en/download/) y luego seleccione la opción deseada de Windows Installer.
2. Cree una carpeta de proyecto local, como `nodejsmysql`. 
3. Inicie el símbolo del sistema y luego cambie el directorio a la carpeta de proyecto, por ejemplo, `cd c:\nodejsmysql\`.
4. Ejecute la herramienta NPM para instalar la biblioteca de mysql2 en la carpeta de proyecto.

   ```cmd
   cd c:\nodejsmysql\
   "C:\Program Files\nodejs\npm" install mysql2
   "C:\Program Files\nodejs\npm" list
   ```

5. Para comprobar la instalación, examine el texto de salida de `npm list` para `mysql2@1.3.5`.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
1. Ejecute los siguientes comandos para instalar **Node.js** y **npm**, el Administrador de paquetes para Node.js.

   ```bash
   sudo apt-get install -y nodejs npm
   ```

2. Ejecute los comandos siguientes para crear una carpeta de proyecto `mysqlnodejs` e instale el paquete mysql2 en ella.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql2
   npm list
   ```
3. Para comprobar la instalación, examine el texto de salida de npm list para `mysql2@1.3.5`.

### <a name="mac-os"></a>**Mac OS**
1. Escriba los comandos siguientes para instalar **brew**, un administrador de paquetes fácil de usar para Mac OS X y **Node.js**.

   ```bash
   ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
   brew install node
   ```
2. Ejecute los comandos siguientes para crear una carpeta de proyecto `mysqlnodejs` e instale el paquete mysql2 en ella.

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql2
   npm list
   ```

3. Para comprobar la instalación, examine el texto de salida de `npm list` para `mysql2@1.3.6`. El número de versión puede variar a medida que se publiquen nuevas revisiones.

## <a name="get-connection-information"></a>Obtención de información sobre la conexión
Obtenga la información de conexión necesaria para conectarse a Azure Database for MySQL. Necesitará el nombre completo del servidor y las credenciales de inicio de sesión.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En el panel izquierdo, haga clic en **Todos los recursos** y, a continuación, busque el servidor que ha creado (por ejemplo, **myserver4demo**).
3. Haga clic en el nombre del servidor **myserver4demo**.
4. Seleccione la página **Propiedades** del servidor y anote la información que figura en **Nombre del servidor** y en **Nombre de inicio de sesión del administrador del servidor**.
 ![Azure Database for MySQL: inicio de sesión del Administrador del servidor](./media/connect-nodejs/1_server-properties-name-login.png)
5. Si olvida la información de inicio de sesión del servidor, navegue hasta la página **Información general** para ver el nombre de inicio de sesión del administrador del servidor y, si es necesario, restablecer la contraseña.

## <a name="running-the-javascript-code-in-nodejs"></a>Ejecución del código de JavaScript en Node.js
1. Pegue el código de JavaScript en archivos de texto y luego guárdelos en una carpeta de proyecto con la extensión .js (por ejemplo, C:\nodejsmysql\createtable.js o /home/username/nodejsmysql/createtable.js).
2. Inicie el símbolo del sistema o el shell de Bash y luego cambie el directorio a la carpeta de proyecto `cd nodejsmysql`.
3. Para ejecutar la aplicación, escriba el comando node seguido del nombre de archivo, por ejemplo, `node createtable.js`.
4. En Windows, si la aplicación de Node no está en la ruta de la variable de entorno, puede que deba usar la ruta de acceso completa para iniciar la aplicación de Node, por ejemplo, `"C:\Program Files\nodejs\node.exe" createtable.js`.

## <a name="connect-create-table-and-insert-data"></a>Conexión, creación de una tabla e inserción de datos
Use el código siguiente para conectarse y cargar los datos mediante las instrucciones SQL **CREATE TABLE** e **INSERT INTO**.

El método [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) se usa para interactuar con el servidor de MySQL. La función [connect()](https://github.com/mysqljs/mysql#establishing-connections) se usa para establecer la conexión al servidor. La función [query()](https://github.com/mysqljs/mysql#performing-queries) se usa para ejecutar la consulta SQL en la base de datos MySQL. 

Reemplace los parámetros `host`, `user`, `password` y `database` por los valores especificados al crear el servidor y la base de datos.

```javascript
const mysql = require('mysql2');

var config =
{
    host: 'myserver4demo.mysql.database.azure.com',
    user: 'myadmin@myserver4demo',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
    if (err) { 
        console.log("!!! Cannot connect !!! Error:");
        throw err;
    }
    else
    {
       console.log("Connection established.");
           queryDatabase();
    }   
});

function queryDatabase(){
       conn.query('DROP TABLE IF EXISTS inventory;', function (err, results, fields) { 
            if (err) throw err; 
            console.log('Dropped inventory table if existed.');
        })
       conn.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);', 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Created inventory table.');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['banana', 150], 
            function (err, results, fields) {
                if (err) throw err;
            else console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['orange', 154], 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['apple', 100], 
        function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.end(function (err) { 
        if (err) throw err;
        else  console.log('Done.') 
        });
};
```

## <a name="read-data"></a>Lectura de datos
Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL **SELECT**. 

El método [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) se usa para interactuar con el servidor de MySQL. El método [connect()](https://github.com/mysqljs/mysql#establishing-connections) se usa para establecer la conexión al servidor. El método [query()](https://github.com/mysqljs/mysql#performing-queries) se usa para ejecutar la consulta SQL en la base de datos MySQL. La matriz de resultados se usa para almacenar los resultados de la consulta.

Reemplace los parámetros `host`, `user`, `password` y `database` por los valores especificados al crear el servidor y la base de datos.

```javascript
const mysql = require('mysql2');

var config =
{
    host: 'myserver4demo.mysql.database.azure.com',
    user: 'myadmin@myserver4demo',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            readData();
        }   
    });

function readData(){
        conn.query('SELECT * FROM inventory', 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Selected ' + results.length + ' row(s).');
                for (i = 0; i < results.length; i++) {
                    console.log('Row: ' + JSON.stringify(results[i]));
                }
                console.log('Done.');
            })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Closing connection.') 
        });
};
```

## <a name="update-data"></a>Actualización de datos
Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL **UPDATE**. 

El método [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) se usa para interactuar con el servidor de MySQL. El método [connect()](https://github.com/mysqljs/mysql#establishing-connections) se usa para establecer la conexión al servidor. El método [query()](https://github.com/mysqljs/mysql#performing-queries) se usa para ejecutar la consulta SQL en la base de datos MySQL. 

Reemplace los parámetros `host`, `user`, `password` y `database` por los valores especificados al crear el servidor y la base de datos.

```javascript
const mysql = require('mysql2');

var config =
{
    host: 'myserver4demo.mysql.database.azure.com',
    user: 'myadmin@myserver4demo',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            updateData();
        }   
    });

function updateData(){
       conn.query('UPDATE inventory SET quantity = ? WHERE name = ?', [200, 'banana'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Updated ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="delete-data"></a>Eliminación de datos
Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL **DELETE**. 

El método [mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) se usa para interactuar con el servidor de MySQL. El método [connect()](https://github.com/mysqljs/mysql#establishing-connections) se usa para establecer la conexión al servidor. El método [query()](https://github.com/mysqljs/mysql#performing-queries) se usa para ejecutar la consulta SQL en la base de datos MySQL. 

Reemplace los parámetros `host`, `user`, `password` y `database` por los valores especificados al crear el servidor y la base de datos.

```javascript
const mysql = require('mysql2');

var config =
{
    host: 'myserver4demo.mysql.database.azure.com',
    user: 'myadmin@myserver4demo',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            deleteData();
        }   
    });

function deleteData(){
       conn.query('DELETE FROM inventory WHERE name = ?', ['orange'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Deleted ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Migración de una base de datos mediante exportación e importación](./concepts-migrate-import-export.md)
