---
title: "Conexión a Azure Database for MySQL mediante Ruby | Microsoft Docs"
description: "En este tutorial rápido se proporcionan ejemplos de código Ruby que se pueden usar para conectarse a Azure Database for MySQL y consultar datos en este servicio."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: 10f774262015cb19e158a687138b4618ce50063b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-ruby-to-connect-and-query-data"></a>Azure Database for MySQL: uso de Ruby para conectarse y consultar datos
En este tutorial rápido se muestra cómo conectarse a una instancia de Azure Database for MySQL mediante una aplicación de [Ruby](https://www.ruby-lang.org) y la gema [mysql2](https://rubygems.org/gems/mysql2) desde las plataformas Windows, Ubuntu Linux y Mac. Se indica cómo usar instrucciones SQL para consultar, insertar, actualizar y eliminar datos en la base de datos. En este artículo se da por hecho que está familiarizado con el desarrollo mediante Ruby, pero que nunca ha usado Azure Database for MySQL.

## <a name="prerequisites"></a>Requisitos previos
En este tutorial rápido se usan como punto de partida los recursos creados en una de estas guías:
- [Create an Azure Database for MySQL server using Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md) (Creación de un servidor de Azure Database for MySQL mediante Azure Portal)
- [Create an Azure Database for MySQL server using Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) (Creación de un servidor de Azure Database for MySQL mediante la CLI de Azure)

## <a name="install-ruby"></a>Instalación de Ruby
Instale Ruby, Gem y la biblioteca MySQL2 en su propio equipo. 

### <a name="windows"></a>Windows
1. Descargue e instale la versión 2.3 de [Ruby](http://rubyinstaller.org/downloads/).
2. Inicie un nuevo símbolo del sistema (cmd) desde el menú Inicio.
3. Cambie el directorio al directorio de Ruby para la versión 2.3. `cd c:\Ruby23-x64\bin`
4. Para probar la instalación de Ruby, ejecute el comando `ruby -v` para ver la versión instalada.
5. Para probar la instalación de Gem, ejecute el comando `gem -v` para ver la versión instalada.
6. Compile el módulo de Mysql2 para Ruby mediante Gem; para ello, ejecute el comando `gem install mysql2`.

### <a name="macos"></a>MacOS
1. Instale Ruby mediante Homebrew; para ello, ejecute el comando `brew install ruby`. Para más opciones de instalación, consulte la [documentación de instalación](https://www.ruby-lang.org/en/documentation/installation/#homebrew) de Ruby.
2. Para probar la instalación de Ruby, ejecute el comando `ruby -v` para ver la versión instalada.
3. Para probar la instalación de Gem, ejecute el comando `gem -v` para ver la versión instalada.
4. Compile el módulo de mysql2 para Ruby mediante Gem; para ello, ejecute el comando `gem install mysql2`.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Ejecute el comando `sudo apt-get install ruby-full` para instalar Ruby. Para más opciones de instalación, consulte la [documentación de instalación](https://www.ruby-lang.org/en/documentation/installation/) de Ruby.
2. Para probar la instalación de Ruby, ejecute el comando `ruby -v` para ver la versión instalada.
3. Instale las actualizaciones más recientes para Gem; para ello, ejecute el comando `sudo gem update --system`.
4. Para probar la instalación de Gem, ejecute el comando `gem -v` para ver la versión instalada.
5. Instale gcc, make y otras herramientas de compilación al ejecutar el comando `sudo apt-get install build-essential`.
6. Instale las bibliotecas de desarrollador de cliente de MySQL mediante la ejecución del comando `sudo apt-get install libmysqlclient-dev`.
7. Compile el módulo de mysql2 para Ruby mediante Gem; para ello, ejecute el comando `sudo gem install mysql2`.

## <a name="get-connection-information"></a>Obtención de información sobre la conexión
Obtenga la información de conexión necesaria para conectarse a Azure Database for MySQL. Necesitará el nombre completo del servidor y las credenciales de inicio de sesión.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En el menú de la izquierda de Azure Portal, haga clic en **Todos los recursos** y, después, busque el servidor que ha creado (por ejemplo, **myserver4demo**).
3. Haga clic en el nombre del servidor **myserver4demo**.
4. Seleccione la página **Propiedades** del servidor y anote la información que figura en **Nombre del servidor** y en **Nombre de inicio de sesión del administrador del servidor**.
 ![Azure Database for MySQL: inicio de sesión del Administrador del servidor](./media/connect-ruby/1_server-properties-name-login.png)
5. Si olvida la información de inicio de sesión del servidor, navegue hasta la página **Información general** para ver el nombre de inicio de sesión del administrador del servidor y, si es necesario, restablecer la contraseña.

## <a name="run-ruby-code"></a>Ejecución del código Ruby 
1. Pegue el código de Ruby de las secciones siguientes en archivos de texto y, después, guarde los archivos en una carpeta de proyecto con la extensión .rb (como `C:\rubymysql\createtable.rb` o `/home/username/rubymysql/createtable.rb`).
2. Para ejecutar el código, inicie el símbolo del sistema o el shell de Bash. Cambie el directorio a la carpeta de proyecto `cd rubymysql`.
3. A continuación, escriba el comando Ruby seguido del nombre de archivo, por ejemplo, `ruby createtable.rb` para ejecutar la aplicación.
4. En el sistema operativo Windows, si la aplicación Ruby no está en la variable de entorno path, puede que deba usar la ruta de acceso completa para iniciar la aplicación de Node, por ejemplo, `"c:\Ruby23-x64\bin\ruby.exe" createtable.rb`

## <a name="connect-and-create-a-table"></a>Conexión y creación de una tabla
Use el código siguiente para conectarse y crear una tabla mediante la instrucción SQL **CREATE TABLE**, seguida de las instrucciones SQL **INSERT INTO** para agregar filas a la tabla.

El código usa un método .new() con la clase [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) para conectar con Azure Database for MySQL. A continuación, llama al método [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) varias veces para ejecutar los comandos DROP, CREATE TABLE e INSERT INTO. A continuación, llama al método [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) para cerrar la conexión antes de terminar.

Reemplace `host`, `database`, `username` y las cadenas `password` por sus propios valores. 
```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('myserver4demo.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@myserver4demo')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Drop previous table of same name if one exists
    client.query('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    client.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    client.query("INSERT INTO inventory VALUES(1, 'banana', 150)")
    client.query("INSERT INTO inventory VALUES(2, 'orange', 154)")
    client.query("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="read-data"></a>Lectura de datos
Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL **SELECT**. 

El código usa un método .new() con la clase [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) para conectar con Azure Database for MySQL. A continuación, llama el método [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) para ejecutar los comandos SELECT. A continuación, llama al método [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) para cerrar la conexión antes de terminar.

Reemplace `host`, `database`, `username` y las cadenas `password` por sus propios valores. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('myserver4demo.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@myserver4demo')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Read data
    resultSet = client.query('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end
    puts 'Read ' + resultSet.count.to_s + ' row(s).'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="update-data"></a>Actualización de datos
Use el código siguiente para conectarse y actualizar los datos mediante la instrucción SQL **UPDATE**.

El código usa un método .new() con la clase [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) para conectar con Azure Database for MySQL. A continuación, llama al método [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) para ejecutar los comandos UPDATE. A continuación, llama al método [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) para cerrar la conexión antes de terminar.

Reemplace `host`, `database`, `username` y las cadenas `password` por sus propios valores. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('myserver4demo.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@myserver4demo')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Update data
   client.query('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
   puts 'Updated 1 row of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```


## <a name="delete-data"></a>Eliminación de datos
Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL **DELETE**. 

El código usa un método .new() con la clase [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) para conectar con Azure Database for MySQL. A continuación, llama el método [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) para ejecutar los comandos DELETE. A continuación, llama al método [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) para cerrar la conexión antes de terminar.

Reemplace `host`, `database`, `username` y las cadenas `password` por sus propios valores. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('myserver4demo.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@myserver4demo')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Delete data
    resultSet = client.query('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Migración de una base de datos mediante exportación e importación](./concepts-migrate-import-export.md)
