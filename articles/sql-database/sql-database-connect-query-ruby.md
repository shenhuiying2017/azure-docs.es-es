---
title: Uso de Ruby para consultar Azure SQL Database | Microsoft Docs
description: En este tema se muestra cómo usar Ruby para crear un programa que se conecta a Azure SQL Database y realiza consultas mediante instrucciones Transact-SQL.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: ruby
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 30dce6caefc0b95435d1dadc48aa927be475fc66
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="use-ruby-to-query-an-azure-sql-database"></a>Uso de Ruby para consultar una base de datos SQL de Azure

Este tutorial de inicio rápido muestra cómo usar [Ruby](https://www.ruby-lang.org) para crear un programa que se conecta a una base de datos SQL de Azure y utiliza instrucciones Transact-SQL para consultar los datos.

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial de inicio rápido, asegúrese de cumplir los siguientes requisitos previos:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Una [regla de firewall de nivel de servidor](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) para la dirección IP pública del equipo que usa para seguir este tutorial de inicio rápido.

- Ha instalado Ruby y el software relacionado para el sistema operativo:
    - **MacOS**: instalación de Homebrew, instalación de rbenv y ruby-build, instalación de Ruby y, a continuación, instalación de FreeTDS. Consulte [pasos 1.2, 1.3, 1.4 y 1.5](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
    - **Ubuntu**: instalación de requisitos previos para Ruby, instalación de rbenv y ruby-build, instalación de Ruby y, a continuación, instalación de FreeTDS. Consulte [pasos 1.2, 1.3, 1.4 y 1.5](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).

## <a name="sql-server-connection-information"></a>Información de conexión de SQL server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> Debe tener una regla de firewall activa para la dirección IP pública del equipo en el que sigue este tutorial. Si se encuentra en un equipo diferente o tiene una dirección IP pública diferente, cree una [regla de firewall de nivel de servidor mediante Azure Portal](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 

## <a name="insert-code-to-query-sql-database"></a>Inserción de código para consultar la base de datos SQL

1. En el editor de texto, cree un nuevo archivo, **sqltest.rb**

2. Reemplace el contenido con el código siguiente y agregue los valores adecuados para el servidor, la base de datos, el usuario y la contraseña.

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

## <a name="run-the-code"></a>Ejecución del código

1. En el símbolo del sistema, ejecute los siguientes comandos:

   ```bash
   ruby sqltest.rb
   ```

2. Compruebe que se han devuelto las primeras 20 filas y, a continuación, cierre la ventana de la aplicación.


## <a name="next-steps"></a>Pasos siguientes
- [Diseño de su primera base de datos SQL de Azure](sql-database-design-first-database.md)
- [Repositorio de Github para TinyTDS](https://github.com/rails-sqlserver/tiny_tds)
- [Informe de los problemas y realización de preguntas sobre TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues)
- [Controladores de Ruby para SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)
