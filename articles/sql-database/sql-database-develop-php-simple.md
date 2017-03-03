---
title: "Conexión a SQL Database mediante PHP en Windows | Microsoft Docs"
description: "Presenta un programa PHP de ejemplo que se conecta a la base de datos SQL de Azure desde un cliente de Windows y proporciona vínculos a los componentes de software necesarios que necesita el cliente."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 02/13/2017
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 94fa09526683582bc017213d0ad9455f31cb22ae
ms.openlocfilehash: fba66e9d41daa2df34fbb3ffd8c92e664eaa560e
ms.lasthandoff: 02/21/2017


---

# <a name="connect-to-sql-database-by-using-php"></a>Conexión a la base de datos SQL mediante PHP en Windows
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

En este tema se muestra cómo conectarse a Azure SQL Database y consultarla mediante PHP. Puede ejecutar este ejemplo en Windows o Linux. 


## <a name="step-1-create-a-sql-database"></a>Paso 1: Creación de una base de datos SQL
Vea la [página de introducción](sql-database-get-started.md) para aprender a crear una base de datos de ejemplo.  Es importante seguir las directrices para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestran a continuación solo funcionan con el **esquema de AdventureWorks**. Una vez creada la base de datos, asegúrese de habilitar el acceso a su dirección IP mediante la habilitación de las reglas de firewall como se describe en la [página de introducción](sql-database-get-started.md).

## <a name="step-2-configure-development-environment"></a>Paso 2: Configuración del entorno de desarrollo

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Abra el terminal y desplácese hasta el directorio donde planea crear secuencias de comandos de Python. Escriba los comandos siguientes para instalar el **controlador ODBC de Microsoft para Linux** y **pdo_sqlsrv** y **sqlsrv**. El controlador PHP de Microsoft PHP para SQL Server usa el controlador ODBC de Microsoft en Linux para conectarse a instancias de SQL Databases.

```
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-release.list
exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install msodbcsql unixodbc-dev gcc g++ build-essential
sudo pecl install sqlsrv pdo_sqlsrv
sudo echo "extension= pdo_sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
sudo echo "extension= sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
```

### <a name="windows"></a>**Windows**
- Instale PHP 7.1.1 (x64) [desde el instalador WebPlatform](https://www.microsoft.com/web/downloads/platform.aspx?lang=). 
- Instale [Microsoft ODBC Driver 13.1](https://www.microsoft.com/download/details.aspx?id=53339). 
- Descargue los .dll de no subproceso para el [controlador PHP de Microsoft para SQL Server](https://pecl.php.net/package/sqlsrv/4.1.6.1/windows) y coloque los archivos binarios en la carpeta PHP\v7.x\ext.
- A continuación, modifique el archivo php.ini (C:\Program Files\PHP\v7.1\php.ini) agregando la referencia al archivo .dll. Por ejemplo:
      
      extension=php_sqlsrv.dll
      extension=php_pdo_sqlsrv.dll

En este momento debe tener el .dll registrado con PHP.

## <a name="step-3-run-sample-code"></a>Paso 3: Ejecución del código de ejemplo
Cree un archivo llamado **sql_sample.php** y pegue dentro de él el código siguiente. Puede ejecutar esto desde la línea de comandos mediante:

```
php sql_sample.php
```

### <a name="connect-to-your-sql-database"></a>Conexión a la base de datos SQL
La función [sqlsrv connect](http://php.net/manual/en/function.sqlsrv-connect.php) se usa para conectarse a SQL Database.

```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
    );
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
?>
```

### <a name="execute-an-sql-select-statement"></a>Ejecución de la instrucción SQL SELECT
La función [sqlsrv_query](http://php.net/manual/en/function.sqlsrv-query.php) puede usarse para recuperar un conjunto de resultados de una consulta realizada a la SQL Database. 

```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
$tsql = "SELECT [CompanyName] FROM SalesLT.Customer";  
$getProducts = sqlsrv_query($conn, $tsql);  
if ($getProducts == FALSE)  
    die(FormatErrors(sqlsrv_errors()));  
$productCount = 0;  
while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))  
{  
    echo($row['CompanyName']);  
    echo("<br/>");  
    $productCount++;  
}  
sqlsrv_free_stmt($getProducts);  
sqlsrv_close($conn);    
function FormatErrors( $errors )
{
    /* Display errors. */
    echo "Error information: ";
    
    foreach ( $errors as $error )
    {
        echo "SQLSTATE: ".$error['SQLSTATE']."";
        echo "Code: ".$error['code']."";
        echo "Message: ".$error['message']."";
    }
}
?>
```

### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Inserción de filas, paso de parámetros y recuperación de la clave principal generada
En SQL Database, la propiedad [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) y el objeto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) pueden usarse para generar automáticamente los valores de [clave principal](https://msdn.microsoft.com/library/ms179610.aspx). 


```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
$tsql = "INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server 1', 'SQL Server 2', 0, 0, getdate())";  
//Insert query  
$insertReview = sqlsrv_query($conn, $tsql);  
if($insertReview == FALSE)  
    die(FormatErrors( sqlsrv_errors()));  
echo "Product Key inserted is :";  
while($row = sqlsrv_fetch_array($insertReview, SQLSRV_FETCH_ASSOC))  
{     
    echo($row['ProductID']);  
}  
sqlsrv_free_stmt($insertReview);  
sqlsrv_close($conn);  
function FormatErrors( $errors )
{
    /* Display errors. */
    echo "Error information: ";
    foreach ( $errors as $error )
    {
        echo "SQLSTATE: ".$error['SQLSTATE']."";
        echo "Code: ".$error['code']."";
        echo "Message: ".$error['message']."";
    }
}
?>
```


## <a name="next-steps"></a>Pasos siguientes
* Consulte [Información general de desarrollo de Base de datos SQL](sql-database-develop-overview.md)
* Puede encontrar más información en [Microsoft PHP Driver para SQL Server](https://github.com/Microsoft/msphpsql/)
* [Registrar problemas y plantear preguntas](https://github.com/Microsoft/msphpsql/issues).

## <a name="additional-resources"></a>Recursos adicionales
* [Modelos de diseño para las aplicaciones SaaS multiinquilino con base de datos SQL de Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Descubra todas las [funcionalidades de Base de datos SQL](https://azure.microsoft.com/services/sql-database/)

