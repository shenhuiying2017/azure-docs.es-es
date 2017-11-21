---
title: "Conexión a Azure Database for PostgreSQL mediante Ruby | Microsoft Docs"
description: "En este tutorial rápido se proporciona un ejemplo de código de Ruby que puede usar para conectarse a Azure Database for PostgreSQL y consultar datos en este servicio."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 11/03/2017
ms.openlocfilehash: 0b8ee73ab86dde2b2c09c9fe2e73209d000b3f26
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="azure-database-for-postgresql-use-ruby-to-connect-and-query-data"></a>Azure Database for PostgreSQL: uso de Ruby para conectarse y consultar datos
En este tutorial rápido se muestra cómo conectarse a una instancia de Azure Database for PostgreSQL mediante una aplicación de [Ruby](https://www.ruby-lang.org). Se indica cómo usar instrucciones SQL para consultar, insertar, actualizar y eliminar datos en la base de datos. En los pasos de este artículo se da por hecho que está familiarizado con el desarrollo mediante Ruby, pero que nunca ha trabajado con Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Requisitos previos
En este tutorial rápido se usan como punto de partida los recursos creados en una de estas guías:
- [Creación de la base de datos: Azure Portal](quickstart-create-server-database-portal.md)
- [Creación de la base de datos: CLI de Azure](quickstart-create-server-database-azure-cli.md)

## <a name="install-ruby"></a>Instalación de Ruby
Instale Ruby en su propia máquina. 

### <a name="windows"></a>Windows
- Descargue e instale la versión más reciente de [Ruby](http://rubyinstaller.org/downloads/).
- En la pantalla de finalización del instalador MSI, active la casilla que dice "Run 'ridk install' to install MSYS2 and development toolchain" (Ejecute "ridk install" para instalar MSYS2 y la cadena de herramientas de desarrollo). A continuación, haga clic en **Finalizar** para iniciar el programa de instalación siguiente.
- Se iniciará el instalador de RubyInstaller2 para Windows. Escriba 2 para instalar la actualización del repositorio de MSYS2. Cuando haya finalizado y se vuelva al mensaje de instalación, cierre el símbolo del sistema.
- Inicie un nuevo símbolo del sistema (cmd) desde el menú Inicio.
- Pruebe la instalación de Ruby `ruby -v` para ver la versión instalada.
- Pruebe la instalación de Gem `gem -v` para ver la versión instalada.
- Compile el módulo de PostgreSQL para Ruby con Gem; para ello, ejecute el comando `gem install pg`.

### <a name="macos"></a>MacOS
- Instale Ruby mediante Homebrew; para ello, ejecute el comando `brew install ruby`. Para más opciones de instalación, consulte la [documentación de instalación](https://www.ruby-lang.org/en/documentation/installation/#homebrew) de Ruby.
- Pruebe la instalación de Ruby `ruby -v` para ver la versión instalada.
- Pruebe la instalación de Gem `gem -v` para ver la versión instalada.
- Compile el módulo de PostgreSQL para Ruby con Gem; para ello, ejecute el comando `gem install pg`.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- Ejecute el comando `sudo apt-get install ruby-full` para instalar Ruby. Para más opciones de instalación, consulte la [documentación de instalación](https://www.ruby-lang.org/en/documentation/installation/) de Ruby.
- Pruebe la instalación de Ruby `ruby -v` para ver la versión instalada.
- Instale las actualizaciones más recientes para Gem; para ello, ejecute el comando `sudo gem update --system`.
- Pruebe la instalación de Gem `gem -v` para ver la versión instalada.
- Instale gcc, make y otras herramientas de compilación al ejecutar el comando `sudo apt-get install build-essential`.
- Ejecute el comando `sudo apt-get install libpq-dev` para instalar las bibliotecas de PostgreSQL.
- Ejecute el comando `sudo gem install pg` para compilar el módulo pg de Ruby con Gem.

## <a name="run-ruby-code"></a>Ejecución del código Ruby 
- Guarde el código en un archivo de texto con la extensión .rb y guárdelo en una carpeta de proyecto, como `C:\rubypostgres\read.rb` o `/home/username/rubypostgres/read.rb`.
- Para ejecutar el código, inicie el símbolo del sistema o el shell de Bash. Cambie el directorio a la carpeta de proyecto `cd rubypostgres` y escriba el comando `ruby read.rb` para ejecutar la aplicación.

## <a name="get-connection-information"></a>Obtención de información sobre la conexión
Obtenga la información de conexión necesaria para conectarse a Azure Database for PostgreSQL. Necesitará el nombre completo del servidor y las credenciales de inicio de sesión.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. En el menú izquierdo de Azure Portal, haga clic en **Todos los recursos** y busque el servidor que ha creado, por ejemplo, **mypgserver-20170401**.
3. Haga clic en el nombre del servidor **mypgserver-20170401**.
4. Seleccione la página **Introducción** del servidor. Tome nota del **Nombre del servidor** y del **Server admin login name** (Nombre de inicio de sesión del administrador del servidor).
 ![Azure Database for PostgreSQL: inicio de sesión del administrador del servidor](./media/connect-ruby/1-connection-string.png)
5. Si olvida la información de inicio de sesión del servidor, navegue hasta la página **Información general** para ver el nombre de inicio de sesión del Administrador del servidor. Si es necesario, restablezca la contraseña.

## <a name="connect-and-create-a-table"></a>Conexión y creación de una tabla
Use el código siguiente para conectarse y crear una tabla mediante la instrucción SQL **CREATE TABLE**, seguida de las instrucciones SQL **INSERT INTO** para agregar filas a la tabla.

El código usa un objeto [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) con el constructor [new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) para conectarse a Azure Database for PostgreSQL. A continuación, realiza una llamada al método [exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) para ejecutar los comandos DROP, CREATE TABLE e INSERT INTO. El código comprueba si hay errores mediante la clase [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error). A continuación, llama al método [close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) para cerrar la conexión antes de terminar.

Reemplace `host`, `database`, `user` y las cadenas `password` por sus propios valores. 
```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mypgserver-20170401.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mypgserver-20170401')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database'

    # Drop previous table of same name if one exists
    connection.exec('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    connection.exec('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    connection.exec("INSERT INTO inventory VALUES(1, 'banana', 150)")
    connection.exec("INSERT INTO inventory VALUES(2, 'orange', 154)")
    connection.exec("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="read-data"></a>Lectura de datos
Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL **SELECT**. 

El código usa un objeto [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) con el constructor [new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) para conectarse a Azure Database for PostgreSQL. A continuación, llama al método [exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) para ejecutar el comando SELECT, conservando los resultados en un conjunto de resultados. La colección de resultados se repite con el bucle `resultSet.each do`, conservando los valores de fila actuales de la variable `row`. El código comprueba si hay errores mediante la clase [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error). A continuación, llama al método [close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) para cerrar la conexión antes de terminar.

Reemplace `host`, `database`, `user` y las cadenas `password` por sus propios valores. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mypgserver-20170401.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mypgserver-20170401')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :database => dbname, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    resultSet = connection.exec('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="update-data"></a>Actualización de datos
Use el código siguiente para conectarse y actualizar los datos mediante la instrucción SQL **UPDATE**.

El código usa un objeto [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) con el constructor [new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) para conectarse a Azure Database for PostgreSQL. A continuación, llama al método [exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) para ejecutar el comando UPDATE. El código comprueba si hay errores mediante la clase [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error). A continuación, llama al método [close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) para cerrar la conexión antes de terminar.

Reemplace `host`, `database`, `user` y las cadenas `password` por sus propios valores. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mypgserver-20170401.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mypgserver-20170401')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
    puts 'Updated 1 row of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```


## <a name="delete-data"></a>Eliminación de datos
Use el código siguiente para conectarse y leer los datos mediante la instrucción SQL **DELETE**. 

El código usa un objeto [PG::Connection](http://www.rubydoc.info/gems/pg/PG/Connection) con el constructor [new()](http://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) para conectarse a Azure Database for PostgreSQL. A continuación, llama al método [exec()](http://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) para ejecutar el comando UPDATE. El código comprueba si hay errores mediante la clase [PG::Error](http://www.rubydoc.info/gems/pg/PG/Error). A continuación, llama al método [close()](http://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) para cerrar la conexión antes de terminar.

Reemplace `host`, `database`, `user` y las cadenas `password` por sus propios valores. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mypgserver-20170401.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin@mypgserver-20170401')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Migración de una base de datos mediante exportación e importación](./howto-migrate-using-export-and-import.md)
