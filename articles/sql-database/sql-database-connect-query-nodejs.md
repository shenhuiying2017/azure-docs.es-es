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
ms.custom: quick start connect, mvc
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: lbosq
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 4d3c3541749870b09aecc9efb63413f7c045e044
ms.contentlocale: es-es
ms.lasthandoff: 05/26/2017


---
# <a name="azure-sql-database-use-nodejs-to-connect-and-query-data"></a>Azure SQL Database: uso de Node.js para conectar y consultar datos

Esta guía de inicio rápido muestra cómo conectarse a una base de datos de Azure SQL Database mediante [Node.js](https://nodejs.org/en/)y, posteriormente, usar instrucciones Transact-SQL para consultar, insertar, actualizar y eliminar datos de la base de datos desde las plataformas Windows, Ubuntu Linux y Mac.

## <a name="prerequisites"></a>Requisitos previos

En esta guía de inicio rápido se utilizan como punto de partida los recursos creados en una de las siguientes guías:

- [Creación de la base de datos: Azure Portal](sql-database-get-started-portal.md)
- [Creación de la base de datos: CLI](sql-database-get-started-cli.md)
- [Creación de la base de datos: PowerShell](sql-database-get-started-powershell.md)

## <a name="install-nodejs"></a>Instalación de Node.js 

En esta sección se da por hecho que está familiarizado con el desarrollo mediante Node.js y que nunca ha utilizado Azure SQL Database. Si no está familiarizado con el desarrollo con Node.js, vaya a [Build an app using SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) (Crear una aplicación con SQL Server), seleccione **Node.js** y, a continuación, seleccione el sistema operativo.

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

Obtención de la información de conexión necesaria para conectarse a Azure SQL Database. En los procedimientos siguientes, necesitará el nombre completo del servidor, el nombre de la base de datos y la información de inicio de sesión.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**. 
3. En la página **Introducción** de la base de datos, revise el nombre completo del servidor, tal como se muestra en la imagen siguiente. Mantenga el puntero sobre el nombre del servidor hasta que aparezca la opción **Haga clic para copiar**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Si ha olvidado la información de inicio de sesión para el servidor de Azure SQL Database, navegue a la página del servidor de SQL Database para ver el nombre del Administrador del servidor y, si es necesario, restablecer la contraseña.
    
## <a name="select-data"></a>Selección de datos

Utilice el código siguiente para consultar en Azure SQL Database los 20 primeros productos por categoría. En primer lugar, importe las clases Connect y Request del controlador desde la biblioteca del controlador tedious. Después de crear el objeto de configuración y reemplazar las variables **nombre de usuario**, **contraseña**, **servidor** y **base de datos** por los valores especificados cuando creó la base de datos con los datos de ejemplo AdventureWorksLT. Cree un objeto `Connection` con el objeto `config` especificado. Después, defina la devolución de llamada para el evento `connect` del objeto `connection` para ejecutar la función `queryDatabase()`.

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
Utilice el código siguiente para insertar un nuevo producto en la tabla SalesLT.Product mediante la función `insertIntoDatabase()` y la instrucción [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) de Transact-SQL. Reemplace las variables **nombre de usuario**, **contraseña**, **servidor** y **base de datos** por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT. 

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
Utilice el código siguiente para eliminar el nuevo producto que ha agregado anteriormente mediante la función `updateInDatabase()` y la instrucción [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) de Transact-SQL. Reemplace las variables **nombre de usuario**, **contraseña**, **servidor** y **base de datos** por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT. En este ejemplo se utiliza el nombre de producto insertado en el ejemplo anterior.

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
- [Diseño de su primera base de datos SQL de Azure](sql-database-design-first-database.md)
- [Controlador de Microsoft Node.js para SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- [Conectarse y realizar consultas con SSMS](sql-database-connect-query-ssms.md)
- [Conexión y consultas con Visual Studio Code](sql-database-connect-query-vscode.md).



