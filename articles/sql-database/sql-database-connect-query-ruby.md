---
title: "Conexión a Azure SQL Database mediante Ruby | Microsoft Docs"
description: "En este tema se presenta un ejemplo de código Ruby que puede usar para conectarse a Azure SQL Database y realizar consultas."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: andrela
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: c5d09cf03c87c8da1d8588be62fea3f0cc3eec4f
ms.contentlocale: es-es
ms.lasthandoff: 06/22/2017

---

# <a name="azure-sql-database-use-ruby-to-connect-and-query-data"></a>Azure SQL Database: uso de Ruby para conectar y consultar datos

En esta guía de inicio rápido se muestra cómo usar [Ruby](https://www.ruby-lang.org) para conectarse a una base de datos de Azure SQL Database y, después, usar instrucciones Transact-SQL para consultar, insertar, actualizar y eliminar datos de la base de datos desde las plataformas Mac OS y Ubuntu Linux.

## <a name="prerequisites"></a>Requisitos previos

En esta guía de inicio rápido se utilizan como punto de partida los recursos creados en una de las siguientes guías:

- [Creación de la base de datos: Azure Portal](sql-database-get-started-portal.md)
- [Creación de la base de datos: CLI](sql-database-get-started-cli.md)
- [Creación de la base de datos: PowerShell](sql-database-get-started-powershell.md)

## <a name="install-ruby-and-database-communication-libraries"></a>Instalación de bibliotecas de comunicación de bases de datos y Ruby

En esta sección se da por hecho que está familiarizado con el desarrollo mediante Ruby y que nunca ha utilizado Azure SQL Database. Si no está familiarizado con el desarrollo con Ruby, vaya a [Build an app using SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) (Crear una aplicación con SQL Server), seleccione **Ruby** y, a continuación, seleccione el sistema operativo.

### <a name="mac-os"></a>**Mac OS**
Abra el terminal y desplácese hasta el directorio donde planea crear su script de Ruby. Escriba los comandos siguientes para instalar **brew**, **FreeTDS** y **TinyTDS**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install FreeTDS
gem install tiny_tds
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Abra el terminal y desplácese hasta el directorio donde planea crear su script de Ruby. Escriba los comandos siguientes para instalar **FreeTDS** y **TinyTDS**.

```bash
wget ftp://ftp.freetds.org/pub/freetds/stable/freetds-1.00.27.tar.gz
tar -xzf freetds-1.00.27.tar.gz
cd freetds-1.00.27
./configure --prefix=/usr/local --with-tdsver=7.3
make
make install
gem install tiny_tds
```

## <a name="get-connection-information"></a>Obtención de información sobre la conexión

Obtención de la información de conexión necesaria para conectarse a Azure SQL Database. En los procedimientos siguientes, necesitará el nombre completo del servidor, el nombre de la base de datos y la información de inicio de sesión.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**. 
3. En la página **Introducción** de la base de datos, revise el nombre completo del servidor, tal como se muestra en la imagen siguiente. Mantenga el puntero sobre el nombre del servidor hasta que aparezca la opción **Haga clic para copiar**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Si olvida la información de inicio de sesión del servidor, navegue hasta la página del servidor de SQL Database para ver el nombre de administrador del servidor y, si es necesario, restablecer la contraseña.
    

## <a name="select-data"></a>Selección de datos
Utilice el código siguiente para consultar los 20 primeros productos por categoría mediante la función [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) con una instrucción [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) de Transact-SQL. La función TinyTDS::Client acepta una consulta y devuelve un conjunto de resultados. El conjunto de resultados se recorre en iteración usando [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds). Reemplace los parámetros de servidor, base de datos, nombre de usuario y contraseña por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

puts "Reading data from table"
tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
        FROM [SalesLT].[ProductCategory] pc
        JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid"
result = client.execute(tsql)
result.each do |row|
    puts row
end
```

## <a name="insert-data"></a>Insertar datos
Use el código siguiente para insertar un producto nuevo en la tabla SalesLT.Product mediante la función [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) con una instrucción [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) de Transact-SQL. Reemplace los parámetros de servidor, base de datos, nombre de usuario y contraseña por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT.

En este ejemplo se muestra cómo ejecutar una instrucción INSERT de forma segura, pasar parámetros que protejan la aplicación contra la vulnerabilidad de [inyección de código SQL](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) y recuperar el valor [Clave principal](https://docs.microsoft.com/sql/relational-databases/tables/primary-and-foreign-key-constraints) generado automáticamente.    
  
Para utilizar TinyTDS con Azure, se recomienda ejecutar varias instrucciones `SET` para cambiar la forma en que la sesión actual trata determinada información. Las instrucciones `SET` recomendadas se proporcionan en el código de ejemplo. Por ejemplo, `SET ANSI_NULL_DFLT_ON` permitirá crear nuevas columnas para permitir valores null, aunque no se indique explícitamente el estado de aceptación de valores NULL de la columna.  
  
Para estar en consonancia con el formato [datetime](https://docs.microsoft.com/sql/t-sql/data-types/datetime-transact-sql) de Microsoft SQL Server, use la función [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) para convertir al formato de fecha y hora correspondiente.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def insert(name, productnumber, color, standardcost, listprice, sellstartdate)
    tsql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) 
        VALUES (N'#{name}', N'#{productnumber}',N'#{color}',N'#{standardcost}',N'#{listprice}',N'#{sellstartdate}')"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
insert('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')
```

## <a name="update-data"></a>Actualización de datos
Utilice el código siguiente para actualizar el nuevo producto que ha agregado anteriormente mediante la función [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) con una instrucción [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) de Transact-SQL. Reemplace los parámetros de servidor, base de datos, nombre de usuario y contraseña por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true
    
def update(name, listPrice, client)
    tsql = "UPDATE SalesLT.Product SET ListPrice = N'#{listPrice}' WHERE Name =N'#{name}'";
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
update('BrandNewProduct', 500, client)
```

## <a name="delete-data"></a>Eliminación de datos
Utilice el código siguiente para eliminar el nuevo producto que ha agregado anteriormente mediante la función [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) con una instrucción [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) de Transact-SQL. Reemplace los parámetros de servidor, base de datos, nombre de usuario y contraseña por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def delete(name, client)
    tsql = "DELETE FROM SalesLT.Product WHERE Name = N'#{name}'"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
delete('BrandNewProduct', client)
```

## <a name="next-steps"></a>Pasos siguientes
- [Diseño de su primera base de datos SQL de Azure](sql-database-design-first-database.md)
- [Repositorio de Github para TinyTDS](https://github.com/rails-sqlserver/tiny_tds)
- [Informe de los problemas y realización de preguntas](https://github.com/rails-sqlserver/tiny_tds/issues)
- [Controladores de Ruby para SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)


