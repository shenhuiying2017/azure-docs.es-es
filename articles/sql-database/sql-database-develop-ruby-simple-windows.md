<properties 
	pageTitle="Conexión a Base de datos SQL mediante Ruby con TinyTDS en Windows" 
	description="Ejemplo de código Ruby que puede ejecutar en Windows para conectarse a Base de datos SQL de Azure."
	services="sql-database" 
	documentationCenter="" 
	authors="meet-bhagdev" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="mebha"/>


# Conexión a Base de datos SQL mediante Ruby en Windows

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

En este tema se presenta un ejemplo de código Ruby que se ejecuta en un equipo Windows con Windows 8.1 para conectarse a una base de datos de Base de datos SQL de Azure.

## Instalación de los módulos necesarios

Abra el terminal e instale lo siguiente:

**1) Ruby:** si el equipo no tiene Ruby, instálelo. En el caso de nuevos usuarios de Ruby, se recomienda usar los instaladores de Ruby 2.1.X. Proporcionan un lenguaje estable y una amplia lista de paquetes (gemas) que son compatibles y están actualizados. [Vaya a página de descarga de Ruby]() y descargue el instalador adecuado de 2.1.x. Por ejemplo, si se encuentra en un equipo de 64 bits, descargue el instalador de **Ruby 2.1.6 (x 64)**. <br/><br/>Una vez descargado el instalador, haga lo siguiente:


- Haga doble clic en el archivo para iniciar el instalador.

- Seleccione el idioma y acepte los términos.

- En la pantalla de configuración de la instalación, active las casillas situadas junto a *Add Ruby executables to your PATH* y *Associate .rb and .rbw files with this Ruby installation*.


**2) DevKit:** descargar DevKit desde la [página RubyInstaller](http://rubyinstaller.org/downloads/)

Una vez finalizada la descarga, haga lo siguiente:


- Haga doble clic en el archivo. Se le preguntará dónde desea extraer los archivos.

- Haga clic en el botón "..." y seleccione "C:\\DevKit". Probablemente necesite crear primero esta carpeta haciendo clic en "Make New Folder".

- Haga clic en "OK" y luego en "Extract" para extraer los archivos.


Abra el símbolo del sistema y escriba los siguientes comandos:

	> chdir C:\DevKit
	> ruby dk.rb init
	> ruby dk.rb install

Ahora tiene un Ruby totalmente funcional con RubyGems.


**3) TinyTDS:** navegue a C:\\DevKit y ejecute el comando siguiente en el terminal. Esto instalará TinyTDS en su equipo.

	gem inst tiny_tds --pre

## Creación de una base de datos y recuperación de la cadena de conexión

El ejemplo Ruby se basa en la base de datos de ejemplo `AdventureWorks`. Si no tiene `AdventureWorks`, puede ver cómo crearla visitando: [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md).

El tema también explica cómo recuperar la cadena de conexión de base de datos.

## Conexión a la base de datos SQL

La función [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) sirve para conectarse a Base de datos SQL.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 

## Ejecución de una instrucción SELECT y recuperación del conjunto de resultados

Copie y pegue el código siguiente en un archivo vacío: Denomínelo test.rb. Ejecútelo escribiendo el comando siguiente en el símbolo del sistema:

	ruby test.rb

En el ejemplo de código, la función [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) sirve para recuperar un conjunto de resultados de una consulta en Base de datos SQL. Esta función acepta una consulta y devuelve un conjunto de resultados. El conjunto de resultados se recorre en iteración usando [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds).

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

Para estar en consonancia con el formato [datetime](http://msdn.microsoft.com/library/ms187819.aspx) de Microsoft SQL Server, use la función [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) para convertirlo al formato de fecha y hora correspondiente.

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

<!---HONumber=August15_HO7-->