<properties 
	pageTitle="Conexión a Base de datos SQL mediante Ruby con TinyTDS en Ubuntu" 
	description="Ejemplo de código Ruby que puede ejecutar como un cliente en Ubuntu Linux para conectarse a Base de datos SQL de Azure."
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


# Conexión a Base de datos SQL mediante Ruby en Ubuntu Linux

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Este tema muestra un ejemplo de un código Ruby que se ejecuta en un equipo cliente Ubuntu Linux y que permite la conexión a una base de datos SQL de Azure.

## Instalación de los módulos necesarios

Abra el terminal e instale FreeTDS si no lo tiene en su equipo.
	
    sudo apt-get --assume-yes update 
    sudo apt-get --assume-yes install freetds-dev freetds-bin

Después de que el equipo está configurado con FreeTDS, instale Ruby si aún no lo tiene en su equipo.
    
    sudo apt-get install libgdbm-dev libncurses5-dev automake libtool bison libffi-dev 
    curl -L https://get.rvm.io | bash -s stable

Si tiene algún problema con las firmas, ejecute el siguiente comando.

    command curl -sSL https://rvm.io/mpapis.asc | gph --import - 

Si no hay ningún problema con las firmas, ejecute los siguientes comandos.

    source ~/.rvm/scripts/rvm 
    rvm install 2.1.2 
    rvm use 2.1.2 --default 
    ruby -v 

Asegúrese de que está ejecutando la versión 2.1.2 o la máquina virtual Ruby.

A continuación, instale TinyTDS.

    gem install tiny_tds

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

Para usar TinyTDS con Azure, se recomienda ejecutar varias instrucciones `SET` para cambiar la forma en que la sesión actual trata determinada información. Las instrucciones `SET` recomendadas se proporcionan en el código de ejemplo. Por ejemplo, `SET ANSI_NULL_DFLT_ON` permitirá crear nuevas columnas para permitir valores null, aunque no se indique explícitamente el estado de aceptación de valores NULL de la columna.

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