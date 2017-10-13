---
title: Uso de PHP para consultar Azure SQL Database | Microsoft Docs
description: "En este tema se muestra cómo usar PHP para crear un programa que se conecta a una base de datos SQL de Azure y realiza consultas mediante instrucciones Transact-SQL."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a 22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: quickstart
ms.date: 08/08/2017
ms.author: carlrab
ms.openlocfilehash: 1cf95cf2c9413f99a4013ff961c8a18c31cc51f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-php-to-query-an-azure-sql-database"></a>Uso de PHP para consultar una base de datos SQL de Azure

Este tutorial de introducción muestra cómo usar [PHP](http://php.net/manual/en/intro-whatis.php) para crear un programa que se conecta a una base de datos SQL de Azure y utiliza instrucciones Transact-SQL para consultar los datos.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, asegúrese de que dispone de lo siguiente:

- Una base de datos SQL de Azure. En esta guía de inicio rápido se utilizan como punto de partida los recursos creados en una de las siguientes guías: 

   - [Creación de la base de datos: Azure Portal](sql-database-get-started-portal.md)
   - [Creación de la base de datos: CLI](sql-database-get-started-cli.md)
   - [Creación de la base de datos: PowerShell](sql-database-get-started-powershell.md)

- Una [regla de firewall de nivel de servidor](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) para la dirección IP pública del equipo que usa para seguir este tutorial.

- Ha instalado PHP y el software relacionado para el sistema operativo.

    - **MacOS**: instalación de Homebrew y PHP, instalación del controlador ODBC y SQLCMD y, a continuación, instalación del controlador PHP para SQL Server. Consulte los [pasos 1.2, 1.3 y 2.1](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/mac/).
    - **Ubuntu**: instalación de PHP y otros paquetes necesarios y, a continuación, instalación del controlador PHP para SQL Server. Consulte los [pasos 1.2 y 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/).
    - **Windows**: instalación de la versión más reciente de PHP para IIS Express, la versión más reciente de Microsoft Drivers para SQL Server en IIS Express, Chocolatey, el controlador ODBC y SQLCMD. Consulte los [pasos 1.2 y 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).    

## <a name="sql-server-connection-information"></a>Información de conexión de SQL server

Obtención de la información de conexión necesaria para conectarse a Azure SQL Database. En los procedimientos siguientes, necesitará el nombre completo del servidor, el nombre de la base de datos y la información de inicio de sesión.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**. 
3. En la página **Introducción** de la base de datos, revise el nombre completo del servidor tal como se muestra en la siguiente imagen. Mantenga el puntero sobre el nombre del servidor hasta que aparezca la opción **Haga clic para copiar**.  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Si olvida la información de inicio de sesión del servidor, navegue hasta la página del servidor de SQL Database para ver el nombre de administrador del servidor y, si es necesario, restablecer la contraseña.     
    
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
