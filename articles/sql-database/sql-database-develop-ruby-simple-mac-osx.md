<properties 
	pageTitle="Conexión a Base de datos SQL mediante Ruby con TinyTDS en Mac OS X (Yosemite)" 
	description="Ejemplo de código Ruby que puede ejecutar en Mac OS X (Yosemite) para conectarse a Base de datos SQL de Azure."
	services="sql-database" 
	documentationCenter="" 
	authors="ajlam" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="07/20/2015" 
	ms.author="andrela"/>


# Conectarse a la Base de datos SQL mediante el uso de Ruby en Mac OS X (Yosemite)

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Este tema presenta un ejemplo de código Ruby que se ejecuta en el equipo Mac que dispone de Yosemite para conectarse a una base de datos de Base de datos SQL de Azure.

## Instalación de los módulos necesarios

Abra el terminal e instale lo siguiente:

**1) Homebrew**: ejecute el siguiente comando desde su terminal. Esto descargará el administrador de paquetes Homebrew en su equipo.

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

**2) FreeTDS**: ejecute el siguiente comando desde su terminal. Esto instalará FreeTDS en su equipo y es necesario para que TinyTDS funcione.

    brew install FreeTDS

**3) TinyTDS**: ejecute el siguiente comando desde su terminal. Esto instalará TinyTDS en su equipo.

    sudo ARCHFLAGS="-arch x86_64" gem install tiny_tds

## Creación de una base de datos y recuperación de la cadena de conexión

El ejemplo Ruby se basa en la base de datos de ejemplo AdventureWorks. Si no tiene AdventureWorks, puede ver cómo crearla en el siguiente tema: [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md)

El tema también explica cómo recuperar la cadena de conexión de base de datos.

## Conexión a la base de datos SQL

La función [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) se usa para conectarse a Base de datos SQL.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 

## Ejecución de una instrucción SELECT y recuperación del conjunto de resultados

La función [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) se usa para recuperar un conjunto de resultados de una consulta en Base de datos SQL. Esta función acepta una consulta y devuelve un conjunto de resultados. El conjunto de resultados se recorre en iteración usando [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds).

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 
    results = client.execute("select * from SalesLT.Product") 
    results.each do |row| 
    puts row 
    end 

## Inserción de filas, mediante el paso de parámetros, y recuperación del valor clave principal generado

Código de ejemplo:

- Pasa los parámetros para los valores que deben insertarse en una fila.
- Inserta la fila.
- Recupera el valor generado para la clave principal.

En Base de datos SQL, la propiedad [IDENTITY](http://msdn.microsoft.com/library/ms186775.aspx) y el objeto [SEQUENCE](http://msdn.microsoft.com/library/ff878058.aspx) pueden usarse para generar automáticamente los [valores de clave principal](http://msdn.microsoft.com/library/ms179610.aspx).

Para utilizar TinyTDS con Azure, se recomienda ejecutar varias instrucciones `SET` para cambiar la forma en que la sesión actual trata determinada información. Las instrucciones `SET` recomendadas se proporcionan en el código de ejemplo. Por ejemplo, `SET ANSI_NULL_DFLT_ON` permitirá crear nuevas columnas para permitir valores null, aunque no se indique explícitamente el estado de aceptación de valores NULL de la columna.

Para estar en consonancia con el formato [datetime](http://msdn.microsoft.com/library/ms187819.aspx) de Microsoft SQL Server, use la función [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) para convertir al formato de fecha y hora correspondiente.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 
    results = client.execute("SET ANSI_NULLS ON")
    results = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
    results = client.execute("SET ANSI_NULL_DFLT_ON ON")
    results = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
    results = client.execute("SET ANSI_PADDING ON")
    results = client.execute("SET QUOTED_IDENTIFIER ON")
    results = client.execute("SET ANSI_WARNINGS ON")
    results = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")
    require 'date'
    t = Time.now
    curr_date = t.strftime("%Y-%m-%d %H:%M:%S.%L") 
    results = client.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) 
    OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, '#{curr_date}' )")
    results.each do |row| 
    puts row
    end

<!---HONumber=July15_HO4-->