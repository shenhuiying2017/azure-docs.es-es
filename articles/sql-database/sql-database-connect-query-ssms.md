---
title: "SSMS: conexión y consulta de datos en Azure SQL Database | Microsoft Docs"
description: "Aprenda a conectarse a Base de datos SQL en Azure con SQL Server Management Studio (SSMS). A continuación, ejecute instrucciones de Transact-SQL (T-SQL) para consultar y editar los datos."
metacanonical: 
keywords: "conexión a base de datos sql,sql server management studio"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.custom: quick start manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/15/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: a08fe566f4962684bc4aad220687e9cd36fc4abf
ms.lasthandoff: 04/17/2017


---
# <a name="azure-sql-database-use-sql-server-management-studio-to-connect-and-query-data"></a>Azure SQL Database: use SQL Server Management Studio para conectarse a los datos y realizar consultas en ellos

[SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) es una herramienta de administración que se usa para crear y administrar recursos de SQL Server desde la interfaz de usuario o en scripts. Este inicio rápido muestra cómo usar SSMS para conectarse a una base de datos de SQL Azure Database, para después usar las instrucciones Transact-SQL para consultar, insertar, actualizar y eliminar datos en la base de datos. 

En esta guía de inicio rápido se utilizan como punto de partida los recursos creados en una de las siguientes guías:

- [Creación de la base de datos: Azure Portal](sql-database-get-started-portal.md)
- [Creación de la base de datos: CLI](sql-database-get-started-cli.md)

Antes de empezar, asegúrese de que ha instalado la versión más reciente de [SSMS](https://msdn.microsoft.com/library/mt238290.aspx). 

## <a name="get-connection-information"></a>Obtención de información sobre la conexión

Obtenga el nombre completo del servidor de Azure SQL Database en Azure Portal. Utilice el nombre completo del servidor para conectarse al servidor mediante SQL Server Management Studio.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**. 
3. En la página **Introducción** de la base de datos, revise el nombre completo del servidor, tal como se muestra en la imagen siguiente. Mantenga el puntero sobre el nombre del servidor hasta que aparezca la opción **Haga clic para copiar**.

   ![información sobre la conexión](./media/sql-database-connect-query-ssms/connection-information.png) 

4. Si ha olvidado la información de inicio de sesión para el servidor de Azure SQL Database, navegue a la página del servidor de SQL Database para ver el nombre del Administrador de servidor y, si es necesario, restablecer la contraseña. 

## <a name="connect-to-your-database-in-the-sql-database-logical-server"></a>Conexión con la base de datos en el servidor lógico de SQL Database

Use SQL Server Management Studio para establecer una conexión con un servidor de Azure SQL Database. 

> [!IMPORTANT]
> Un servidor lógico de Azure SQL Database escucha en el puerto 1433. Si intenta conectarse a un servidor lógico de Azure SQL Database desde dentro de un firewall corporativo, este puerto debe estar abierto en el firewall corporativo para poder conectarse correctamente.
>

1. Abra SQL Server Management Studio.

2. En el cuadro de diálogo **Conectar con el servidor**, especifique la siguiente información:
   - **Tipo de servidor**: especifique el motor de base de datos
   - **Nombre de servidor**: escriba el nombre completo del servidor, como **mynewserver20170313.database.windows.net**
   - **Autenticación**: especifique la autenticación de SQL Server
   - **Inicio de sesión**: escriba la cuenta de administrador del servidor
   - **Contraseña**: escriba la contraseña de la cuenta de administrador del servidor

   ![conectar con el servidor](./media/sql-database-connect-query-ssms/connect.png)  

3. Haga clic en **Opciones** en el cuadro de diálogo **Conectar con el servidor**. En la sección **Conectar con base de datos**, escriba **mySampleDatabase** para conectarse a esta base de datos.

   ![conectar a base de datos en el servidor](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Haga clic en **Conectar**. Se abre la ventana del Explorador de objetos en SSMS. 

   ![conectado al servidor](./media/sql-database-connect-query-ssms/connected.png)  

5. En el Explorador de objetos, expanda **Bases de datos** y, después, expanda **mySampleDatabase** para ver los objetos de la base de datos de ejemplo.

## <a name="query-data"></a>Datos de consulta

Use la instrucción [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) de Transact-SQL para consultar datos de la instancia de Azure SQL Database.

1. En el Explorador de objetos, haga clic con el botón derecho en **mySampleDatabase** y luego en **Nueva consulta**. Se abre una ventana de consulta en blanco que está conectada a la base de datos.
2. En la ventana Consulta, escriba la consulta siguiente:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. En la barra de herramientas, haga clic en **Ejecutar** para recuperar datos de las tablas Product y ProductCategory.

    ![query](./media/sql-database-connect-query-ssms/query.png)

## <a name="insert-data"></a>Insertar datos

Use la instrucción [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) de Transact-SQL para insertar datos en la instancia de Azure SQL Database.

1. En la ventana Consulta, reemplace la consulta anterior con la siguiente consulta:

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. En la barra de herramientas, haga clic en **Ejecutar** para insertar una nueva fila en la tabla Product.

    <img src="./media/sql-database-connect-query-ssms/insert.png" alt="insert" style="width: 780px;" />

## <a name="update-data"></a>Actualización de datos

Use la instrucción [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) de Transact-SQL para actualizar datos en la instancia de Azure SQL Database.

1. En la ventana Consulta, reemplace la consulta anterior con la siguiente consulta:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. En la barra de herramientas, haga clic en **Ejecutar** para actualizar la fila especificada en la tabla Product.

    <img src="./media/sql-database-connect-query-ssms/update.png" alt="update" style="width: 780px;" />

## <a name="delete-data"></a>Eliminación de datos

Use la instrucción [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) de Transact-SQL para eliminar datos en la instancia de Azure SQL Database.

1. En la ventana Consulta, reemplace la consulta anterior con la siguiente consulta:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. En la barra de herramientas, haga clic en **Ejecutar** para eliminar la fila especificada en la tabla Product.

    <img src="./media/sql-database-connect-query-ssms/delete.png" alt="delete" style="width: 780px;" />

## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca de SSMS, consulte [Uso de SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Para conectarse y consultar con Visual Studio, vea [Conexión y consultas con Visual Studio Code](sql-database-connect-query-vscode.md).
- Para conectarse y consultar con .NET, vea [Conexión y consultas con .NET](sql-database-connect-query-dotnet.md).
- Para conectarse y consultar con PHP, vea [Conexión y consultas con PHP](sql-database-connect-query-php.md).
- Para conectarse y consultar con Node.js, vea [Conexión y consultas con Node.js](sql-database-connect-query-nodejs.md).
- Para conectarse y consultar con Java, vea [Conexión y consultas con Java](sql-database-connect-query-java.md).
- Para conectarse y consultar con Python, vea [Conexión y consultas con Python](sql-database-connect-query-python.md).
- Para conectarse y consultar con Ruby, vea [Conexión y consultas con Ruby](sql-database-connect-query-ruby.md).

