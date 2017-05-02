---
title: "Conexión a Azure SQL Database mediante Node.js | Microsoft Docs"
description: "En este tema se presenta un ejemplo de código Node.js que puede usar para conectarse a Azure SQL Database y realizar consultas."
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/17/2017
ms.author: lbosq
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 7365945818c56279bd5945fee8d0048ef425bfc7
ms.lasthandoff: 04/18/2017


---
# <a name="azure-sql-database-use-nodejs-to-connect-and-query-data"></a>Azure SQL Database: uso de Node.js para conectar y consultar datos

Este inicio rápido muestra cómo usar [Node.js](https://nodejs.org/en/) para conectarse a una base de datos de Azure SQL Database, para después usar las instrucciones Transact-SQL para consultar, insertar, actualizar y eliminar datos en la base de datos desde las plataformas de Windows, Ubuntu Linux y Mac.

En esta guía de inicio rápido se utilizan como punto de partida los recursos creados en una de estas guías:

- [Creación de la base de datos: Azure Portal](sql-database-get-started-portal.md)
- [Creación de la base de datos: CLI](sql-database-get-started-cli.md)

## <a name="install-nodejs"></a>Instalación de Node.js 

### <a name="mac-os"></a>**Mac OS**
Escriba los comandos siguientes para instalar **brew**, un administrador de paquetes fácil de usar para Mac OS X y **Node.js**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Escriba los comandos siguientes para instalar **Node.js** y **npm**, el administrador de paquetes para Node.js.

```bash
sudo apt-get install -y nodejs npm
```

### <a name="windows"></a>**Windows**
Visite la [página de descargas de Node.js](https://nodejs.org/en/download/) y seleccione la opción deseada de Windows Installer.


## <a name="install-the-tedious-sql-server-driver-for-nodejs"></a>Instalación del controlador Tedious de SQL Server para Node.js
Es el controlador recomendado para Node.js es **[tedious](https://github.com/tediousjs/tedious)**. Tedious es una iniciativa de código abierto a la que Microsoft contribuye para aplicaciones Node.js en cualquier plataforma. Para este tutorial necesita un directorio vacío para que contenga el código y las dependencias `npm` que se van a instalar.

Para instalar el controlador **tedioso**, ejecute el siguiente comando en el directorio:

```cmd
npm install tedious
```

## <a name="get-connection-information"></a>Obtención de información sobre la conexión

Obtenga la cadena de conexión en Azure Portal. La cadena de conexión se usa para conectarse a Azure SQL Database.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**. 
3. En la página **Introducción** de la base de datos, revise el nombre completo del servidor, tal como se muestra en la imagen siguiente. Mantenga el puntero sobre el nombre del servidor hasta que aparezca la opción **Haga clic para copiar**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Si ha olvidado la información de inicio de sesión para el servidor de Azure SQL Database, navegue a la página del servidor de SQL Database para ver el nombre del Administrador del servidor y, si es necesario, restablecer la contraseña.
    
## <a name="select-data"></a>Selección de datos

Utilice el código siguiente para consultar Azure SQL Database. En primer lugar, importe las clases Connect y Request del controlador desde la biblioteca del controlador tedious. Después de crear el objeto de configuración y reemplazar las variables **nombre de usuario**, **contraseña**, **servidor** y **base de datos** por los valores especificados cuando creó la base de datos con los datos de ejemplo AdventureWorksLT. Cree un objeto `Connection` con el objeto `config` especificado. Después, defina la devolución de llamada para el evento `connect` del objeto `connection` para ejecutar la función `queryDatabase()`.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}
var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        queryDatabase()
    }
});

function queryDatabase(){
    console.log('Reading rows from the Table...');

    // Read all rows from table
    request = new Request(
        "SELECT TOP 1 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    
    request.on('row', function(columns) {
        columns.forEach(function(column) {
            console.log("%s\t%s", column.metadata.colName, column.value);
        });
    });

    connection.execSql(request);
}
```

## <a name="insert-data-into-the-database"></a>Inserción de datos en la base de datos
Utilice el código siguiente para insertar un nuevo producto en la tabla SalesLT.Product. Reemplace las variables **nombre de usuario**, **contraseña**, **servidor** y **base de datos** por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT. Esta vez, utilice una **instrucción INSERT** en la función `insertIntoDatabase()`.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        insertIntoDatabase()
    }
});

function insertIntoDatabase(){
    console.log("Inserting a brand new product into database...");
    request = new Request(
        "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) inserted');
        }
    );
    connection.execSql(request);
}
```

## <a name="update-data-in-the-database"></a>Actualización de datos de la base de datos
Use el código siguiente para actualizar los datos de la base de datos. Reemplace las variables **nombre de usuario**, **contraseña**, **servidor** y **base de datos** por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT. Esta vez, utilice una **instrucción UPDATE** en la función `updateInDatabase()`. En este ejemplo se utiliza el nombre de producto insertado en el ejemplo anterior.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        updateInDatabase()
    }
});

function updateInDatabase(){
    console.log("Updating the price of the brand new product in database...");
    request = new Request(
        "UPDATE SalesLT.Product SET ListPrice = 50 WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) updated');
        }
    );
    connection.execSql(request);
}
```

## <a name="delete-data-from-the-database"></a>Eliminación de datos de la base de datos
Use el código siguiente para eliminar datos de la base de datos. Reemplace las variables **nombre de usuario**, **contraseña**, **servidor** y **base de datos** por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT. Esta vez, utilice una **instrucción DELETE** en la función `deleteFromDatabase()`. En este ejemplo también se utiliza el nombre de producto insertado en el ejemplo anterior.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        deleteFromDatabase()
    }
});

function deleteFromDatabase(){
    console.log("Deleting the brand new product in database...");
    request = new Request(
        "DELETE FROM SalesLT.Product WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    connection.execSql(request);
}
```


## <a name="next-steps"></a>Pasos siguientes

- Puede encontrar más información en [Microsoft Node.js Driver para SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- Para conectarse y consultar mediante SQL Server Management Studio, consulte el artículo de [Conexión y consultas con SSMS](sql-database-connect-query-ssms.md).
- Para conectarse y consultar con Visual Studio, vea [Conexión y consultas con Visual Studio Code](sql-database-connect-query-vscode.md).
- Para conectarse y consultar con .NET, vea [Conexión y consultas con .NET](sql-database-connect-query-dotnet.md).
- Para conectarse y consultar con PHP, vea [Conexión y consultas con PHP](sql-database-connect-query-php.md).
- Para conectarse y consultar con Java, vea [Conexión y consultas con Java](sql-database-connect-query-java.md).
- Para conectarse y consultar con Python, vea [Conexión y consultas con Python](sql-database-connect-query-python.md).
- Para conectarse y consultar con Ruby, vea [Conexión y consultas con Ruby](sql-database-connect-query-ruby.md).


