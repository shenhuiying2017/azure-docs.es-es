---
title: Uso de PHP para consultar Azure SQL Database | Microsoft Docs
description: En este tema se muestra cómo usar PHP para crear un programa que se conecta a Azure SQL Database y realiza consultas mediante instrucciones Transact-SQL.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: php
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 8fe343587336ff22f82ed0d1ef700fc56c86f577
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
---
# <a name="use-php-to-query-an-azure-sql-database"></a>Uso de PHP para consultar una base de datos SQL de Azure

En esta guía de inicio rápido se muestra cómo se usa [PHP](http://php.net/manual/en/intro-whatis.php) para crear un programa que se conecte a una instancia de Azure SQL Database y que use instrucciones Transact-SQL para consultar los datos.

## <a name="prerequisites"></a>requisitos previos

Para completar esta guía de inicio rápido, asegúrese de que dispone de lo siguiente:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Una [regla de firewall de nivel de servidor](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) para la dirección IP pública del equipo que usa para seguir esta guía de inicio rápido.

- Instaló PHP y el software relacionado para el sistema operativo:

    - **MacOS**: instalación de Homebrew y PHP, instalación del controlador ODBC y SQLCMD y, a continuación, instalación del controlador PHP para SQL Server. Consulte los [pasos 1.2, 1.3 y 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/mac/).
    - **Ubuntu**: instalación de PHP y otros paquetes necesarios y, a continuación, instalación del controlador PHP para SQL Server. Consulte los [pasos 1.2 y 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/).
    - **Windows**: instalación de la versión más reciente de PHP para IIS Express, la versión más reciente de Microsoft Drivers para SQL Server en IIS Express, Chocolatey, el controlador ODBC y SQLCMD. Consulte los [pasos 1.2 y 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).    

## <a name="sql-server-connection-information"></a>Información de conexión de SQL server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="insert-code-to-query-sql-database"></a>Inserción de código para consultar la base de datos SQL

1. En el editor de texto, cree un nuevo archivo, **sqltest.php**.  

2. Reemplace el contenido con el código siguiente y agregue los valores adecuados para el servidor, la base de datos, el usuario y la contraseña.

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

## <a name="run-the-code"></a>Ejecución del código

1. En el símbolo del sistema, ejecute los siguientes comandos:

   ```php
   php sqltest.php
   ```

2. Compruebe que se han devuelto las primeras 20 filas y, a continuación, cierre la ventana de la aplicación.

## <a name="next-steps"></a>Pasos siguientes
- [Diseño de su primera base de datos SQL de Azure](sql-database-design-first-database.md)
- [Controladores de Microsoft para PHP para SQL Server](https://github.com/Microsoft/msphpsql/)
- [Informe de los problemas y realización de preguntas](https://github.com/Microsoft/msphpsql/issues)
- [Ejemplo de lógica de reintento: conexión resistente a SQL con PHP][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

