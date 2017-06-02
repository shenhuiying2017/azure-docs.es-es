---
title: "Conexión a Azure SQL Database mediante PHP | Microsoft Docs"
description: "Este tema presenta un ejemplo de código PHP que puede usar para conectarse a Azure SQL Database y realizar consultas."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: quick start connect, mvc
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: herp-article
ms.date: 05/24/2017
ms.author: meetb
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 693bfdf7bb3d9d72fbd6ac42734ca261af7b492d
ms.contentlocale: es-es
ms.lasthandoff: 05/26/2017


---
# <a name="azure-sql-database-use-php-to-connect-and-query-data"></a>Azure SQL Database: uso de PHP para conectar y consultar datos

En esta guía de inicio rápido se muestra cómo usar [PHP](http://php.net/manual/en/intro-whatis.php) para conectarse a una base de datos de Azure SQL Database y, después, usar instrucciones Transact-SQL para consultar, insertar, actualizar y eliminar datos de la base de datos desde las plataformas Mac OS, Ubuntu Linux y Windows.

## <a name="prerequisites"></a>Requisitos previos

En esta guía de inicio rápido se utilizan como punto de partida los recursos creados en una de las siguientes guías:

- [Creación de la base de datos: Azure Portal](sql-database-get-started-portal.md)
- [Creación de la base de datos: CLI](sql-database-get-started-cli.md)
- [Creación de la base de datos: PowerShell](sql-database-get-started-powershell.md)

## <a name="install-php-and-database-communications-software"></a>Instalación del software de comunicaciones de bases de datos y PHP

En esta sección se da por hecho que está familiarizado con el desarrollo mediante PHP y que nunca ha utilizado Azure SQL Database. Si no está familiarizado con el desarrollo con PHP, vaya a [Build an app using SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) (Crear una aplicación con SQL Server), seleccione **PHP** y, a continuación, seleccione el sistema operativo.

### <a name="mac-os"></a>**Mac OS**
Abra el terminal y escriba los comandos siguientes para instalar **brew**, **Microsoft ODBC Driver for Mac** y **Microsoft PHP Driver for SQL Server**. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql 
pecl install sqlsrv-4.1.7preview
pecl install pdo_sqlsrv-4.1.7preview
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Escriba los comandos siguientes para instalar **Microsoft ODBC Driver for Linux** y **Microsoft PHP Driver for SQL Server**.

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev gcc g++ php-dev
sudo pecl install sqlsrv pdo_sqlsrv
sudo echo "extension= pdo_sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
sudo echo "extension= sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
```

### <a name="windows"></a>**Windows**
- Instale PHP 7.1.1 (x64) [desde el instalador WebPlatform](https://www.microsoft.com/web/downloads/platform.aspx?lang=). 
- Instale [Microsoft ODBC Driver 13.1](https://www.microsoft.com/download/details.aspx?id=53339). 
- Descargue los archivos dll seguros para subprocesos para el [ controlador de Microsoft para PHP para SQL Server](https://pecl.php.net/package/sqlsrv/4.1.6.1/windows) y coloque los archivos binarios en la carpeta PHP\v7.x\ext.
- A continuación, modifique el archivo php.ini (C:\Program Files\PHP\v7.1\php.ini) agregando la referencia al archivo .dll. Por ejemplo:
      
      extension=php_sqlsrv.dll
      extension=php_pdo_sqlsrv.dll

En este punto, todos los archivos dll deben estar registrados en PHP.

## <a name="get-connection-information"></a>Obtención de información sobre la conexión

Obtención de la información de conexión necesaria para conectarse a Azure SQL Database. En los procedimientos siguientes, necesitará el nombre completo del servidor, el nombre de la base de datos y la información de inicio de sesión.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**. 
3. En la página **Introducción** de la base de datos, revise el nombre completo del servidor, tal como se muestra en la imagen siguiente. Mantenga el puntero sobre el nombre del servidor hasta que aparezca la opción **Haga clic para copiar**.  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Si olvida la información de inicio de sesión del servidor, navegue hasta la página del servidor de SQL Database para ver el nombre de administrador del servidor y, si es necesario, restablecer la contraseña.     
    
## <a name="select-data"></a>Selección de datos
Utilice el código siguiente para consultar los 20 primeros productos por categoría mediante la función [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) con una instrucción [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) de Transact-SQL. La función sqlsrv_query se usa para recuperar un conjunto de resultados de una consulta realizada a SQL Database. Esta función acepta una consulta y devuelve un conjunto de resultados que se puede iterar mediante el uso de [sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php). Reemplace los parámetros de servidor, base de datos, nombre de usuario y contraseña por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT. 

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
     FROM [SalesLT].[ProductCategory] pc
     JOIN [SalesLT].[Product] p
     ON pc.productcategoryid = p.productcategoryid";
$getResults= sqlsrv_query($conn, $tsql);
echo ("Reading data from table" . PHP_EOL);
if ($getResults == FALSE)
    echo (sqlsrv_errors());
while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
    echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
}
sqlsrv_free_stmt($getResults);
?>
```


## <a name="insert-data"></a>Insertar datos
Utilice el código siguiente para insertar un nuevo producto en la tabla SalesLT.Product mediante la función [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) y la instrucción [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) de Transact-SQL. Reemplace los parámetros de servidor, base de datos, nombre de usuario y contraseña por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT. 

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";
$params = array("BrandNewProduct", "200989", "Blue", 75, 80, "7/1/2016");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) inserted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="update-data"></a>Actualización de datos
Use el código siguiente para actualizar los datos de Azure SQL Database mediante la función [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) con una instrucción [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) de Transact-SQL. Reemplace los parámetros de servidor, base de datos, nombre de usuario y contraseña por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT.

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "UPDATE SalesLT.Product SET ListPrice =? WHERE Name = ?";
$params = array(50,"BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) updated" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="delete-data"></a>Eliminación de datos
Utilice el código siguiente para eliminar el nuevo producto que ha agregado anteriormente mediante la función [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) y la instrucción [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) de Transact-SQL. Reemplace los parámetros de servidor, base de datos, nombre de usuario y contraseña por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT.

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "DELETE FROM SalesLT.Product WHERE Name = ?";
$params = array("BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) deleted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
```

## <a name="next-steps"></a>Pasos siguientes
- [Diseño de su primera base de datos SQL de Azure](sql-database-design-first-database.md)
- [Controladores de Microsoft para PHP para SQL Server](https://github.com/Microsoft/msphpsql/)
- [Informe de los problemas y realización de preguntas](https://github.com/Microsoft/msphpsql/issues)


