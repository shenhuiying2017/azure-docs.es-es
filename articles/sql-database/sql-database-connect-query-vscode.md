---
title: "VS Code: conexión con Azure SQL Database y consulta de datos | Microsoft Docs"
description: "Aprenda a conectarse a SQL Database en Azure con Visual Studio Code. A continuación, ejecute instrucciones de Transact-SQL (T-SQL) para consultar y editar los datos."
metacanonical: 
keywords: "conexión a sql database"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 676bd799-a571-4bb8-848b-fb1720007866
ms.service: sql-database
ms.custom: manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 86471fe29bbc9076624d96b83f7001d8755363bc
ms.lasthandoff: 03/25/2017


---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Azure SQL Database: uso de Visual Studio Code para conectar y consultar datos

[Visual Studio Code](https://code.visualstudio.com/docs) es un editor de código gráfico para Linux, macOS y Windows que admite extensiones. Use Visual Studio Code con la [extensión mssql](https://aka.ms/mssql-marketplace) para conectarse a Azure SQL Database y consultar la base de datos. En esta guía se explica cómo utilizar Visual Studio Code para conectarse a una instancia de Azure SQL Database y, a continuación, ejecutar la consulta, insertar instrucciones, actualizarlas y eliminarlas.

En esta guía de inicio rápido se utilizan como punto de partida los recursos creados en una de las siguientes guías:

- [Creación de la base de datos: Azure Portal](sql-database-get-started-portal.md)
- [Creación de la base de datos: CLI](sql-database-get-started-cli.md)

Antes de empezar, asegúrese de que tiene instalada la versión más reciente de [Visual Studio Code](https://code.visualstudio.com/Download) y cargue la [extensión mssql](https://aka.ms/mssql-marketplace). Para obtener instrucciones de instalación para la extensión mssql, consulte [Install VS Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code) (Instalación de VS Code). 

## <a name="get-connection-information"></a>Obtención de información sobre la conexión

Obtenga el nombre completo del servidor de Azure SQL Database en Azure Portal. Utilice el nombre completo del servidor para conectarse al servidor mediante Visual Studio Code.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**. 
3. En el panel **Essentials** de la página de Azure Portal de la base de datos, busque y copie el **nombre del servidor** para usarlo más adelante en esta guía de inicio rápido.

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="set-language-mode-to-sql"></a>Definición del modo de lenguaje en SQL

El modo de lenguaje está establecido en **SQL** en Visual Studio Code para habilitar comandos mssql y T-SQL IntelliSense.

1. Abra una nueva ventana de Visual Studio Code. 

2. Presione **CTRL + K, M**, escriba **SQL** y presione **ENTRAR** para establecer el modo de lenguaje en SQL. 

<img src="./media/sql-database-connect-query-vscode/vscode-language-mode.png" alt="SQL language mode" style="width: 780px;" />

## <a name="connect-to-the-server"></a>Conexión al servidor

Use Visual Studio Code para establecer una conexión con el servidor de Azure SQL Database.

1. En VS Code, presione **CTRL + MAYÚS + P** (o **F1**) para abrir la paleta de comandos.

2. Escriba **sqlcon** y presione **ENTRAR**.

3. Haga clic en **Sí** para establecer el lenguaje en **SQL**.

4. Presione **ENTRAR** para seleccionar **Create Connection Profile** (Crear perfil de conexión). Al hacerlo, se creará un perfil de conexión para la instancia de SQL Server.

5. Siga las indicaciones para especificar las propiedades de conexión para el nuevo perfil de conexión. Después de especificar cada valor, presione **ENTRAR** para continuar. 

   En la tabla siguiente se describen las propiedades del perfil de conexión.

   | Configuración | Descripción |
   |-----|-----|
   | **Nombre del servidor** | Escriba el nombre completo del servidor, como **mynewserver20170313.database.windows.net** |
   | **Nombre de la base de datos** | Escriba el nombre de la base de datos, como **mySampleDatabase** |
   | **Autenticación** | Seleccione Inicio de sesión de SQL. |
   | **Nombre de usuario** | Escriba la cuenta de administrador del servidor. |
   | **Contraseña (Inicio de sesión de SQL)** | Escriba la contraseña de la cuenta de administrador del servidor. | 
   | **¿Guardar la contraseña?** | Seleccione **Sí** o **No**. |
   | **[Opcional] Enter a name for this profile** (Escribir un nombre para este perfil) | Escriba un nombre de perfil de conexión, como **mySampleDatabase**. 

6. Presione la tecla **ESC** para cerrar el mensaje de información que indica que el perfil se ha creado y conectado.

7. Compruebe la conexión en la barra de estado.

   <img src="./media/sql-database-connect-query-vscode/vscode-connection-status.png" alt="Connection status" style="width: 780px;" />

## <a name="query-data"></a>Datos de consulta

Use la instrucción [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) de Transact-SQL para consultar datos de la instancia de Azure SQL Database.

1. En la ventana del **editor**, escriba la siguiente consulta en la ventana de consulta vacía:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Presione **CTRL + MAYÚS + E** para recuperar datos de las tablas Product y ProductCategory.

    <img src="./media/sql-database-connect-query-vscode/query.png" alt="Query" style="width: 780px;" />

## <a name="insert-data"></a>Insertar datos

Use la instrucción [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) de Transact-SQL para insertar datos en la instancia de Azure SQL Database.

1. En la ventana del **editor**, elimine la consulta anterior y escriba la siguiente consulta:

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

3. Presione **CTRL + MAYÚS + E** para insertar una nueva fila en la tabla Product.

## <a name="update-data"></a>Actualización de datos

Use la instrucción [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) de Transact-SQL para actualizar datos en la instancia de Azure SQL Database.

1.  En la ventana del **editor**, elimine la consulta anterior y escriba la siguiente consulta:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

3. Presione **CTRL + MAYÚS + E** para actualizar la fila especificada en la tabla Product.

## <a name="delete-data"></a>Eliminación de datos

Use la instrucción [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) de Transact-SQL para eliminar datos en la instancia de Azure SQL Database.

1. En la ventana del **editor**, elimine la consulta anterior y escriba la siguiente consulta:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

3. Presione **CTRL + MAYÚS + E** para eliminar la fila especificada en la tabla Product.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre Visual Studio Code, consulte [Visual Studio Code](https://code.visualstudio.com/docs).
- Para más información sobre cómo consultar y modificar datos mediante SQL Server Management Studio, consulte [SSMS](https://msdn.microsoft.com/library/ms174173.aspx).

