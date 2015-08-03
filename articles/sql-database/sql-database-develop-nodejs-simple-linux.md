<properties 
	pageTitle="Conexión a la base de datos SQL mediante Node.Js con Tedious en Ubuntu Linux" 
	description="Este tema muestra un ejemplo de código Node.js que puede usar para conectarse a la base de datos SQL de Azure. El ejemplo usa el controlador Tedious para la conexión."
	services="sql-database" 
	documentationCenter="" 
	authors="meet-bhagdev" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="07/20/2015" 
	ms.author="mebha"/>


# Conexión a la base de datos SQL mediante Node.Js con Tedious en Ubuntu Linux


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tema presenta un ejemplo de código de Node.js que se ejecuta en Ubuntu Linux. El ejemplo se conecta a una base de datos SQL de Azure mediante el controlador Tedious.


## Elementos de software necesarios


Abra el terminal e instale **node** y **npm**, a menos que ya estén instalados en el equipo.


	sudo apt-get install node
	sudo apt-get install npm


Cuando el equipo esté configurado con **node** y **npm**, navegue hasta el directorio donde piensa crear el proyecto Node.js y escriba los comandos siguientes.


	sudo npm init
	sudo npm install tedious


**init npm** para crear un proyecto de nodo. Para conservar los valores predeterminados durante la creación del proyecto, presione Entrar hasta que se cree el proyecto. Ahora verá el archivo **package.json** en el directorio del proyecto.


### Creación de una base de datos de AdventureWorks


El ejemplo de código de este tema necesita una base de datos de **AdventureWorks** de prueba. Si aún no dispone de esta base de datos, vea [Introducción a la base de datos SQL](sql-database-get-started.md). Es importante seguir las directrices para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestran a continuación solo funcionan con el **esquema de AdventureWorks**.


## Conexión a la base de datos SQL

La función [new Connection](http://pekim.github.io/tedious/api-connection.html) se utiliza para conectarse a la base de datos SQL.

	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Microsoft Azure, you need this:
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
	// If no error, then good to proceed.
		console.log("Connected");
	});


## Ejecución de la instrucción SQL SELECT


Todas las instrucciones SQL se ejecutan utilizando la función [new Request()](http://pekim.github.io/tedious/api-request.html). Si la instrucción devuelve filas, como una instrucción select, se podrán recuperar mediante la función [request.on()](http://pekim.github.io/tedious/api-request.html). Si no hay ninguna fila, la función [request.on()](http://pekim.github.io/tedious/api-request.html) devuelve listas vacías.


	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// When you connect to Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement();
	});
	
	var Request = require('tedious').Request;
	var TYPES = require('tedious').TYPES;
	
	function executeStatement() {
		request = new Request("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function(err) {
	  	if (err) {
	   		console.log(err);} 
		});
		var result = "";
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        result+= column.value + " ";
		      }
		    });
		    console.log(result);
		    result ="";
		});
	
		request.on('done', function(rowCount, more) {
		console.log(rowCount + ' rows returned');
		});
		connection.execSql(request);
	}


## Inserción de filas, aplicación de parámetros y recuperación de la clave principal generada


En Base de datos SQL, la propiedad [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) y el objeto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) pueden usarse para generar automáticamente los [valores de clave principal](https://msdn.microsoft.com/library/ms179610.aspx). En este ejemplo se muestra cómo ejecutar la instrucción insert-statement, mediante el paso seguro de parámetros que ofrecen protección frente a la inyección SQL, y recuperar el valor de clave principal generado automáticamente.


El ejemplo de código que se muestra en esta sección aplica parámetros a una instrucción SQL INSERT. El programa recupera el valor de clave principal generado.


	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement1();
	});
	
	var Request = require('tedious').Request
	var TYPES = require('tedious').TYPES;
	
	function executeStatement1() {
		request = new Request("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP);", function(err) {
		 if (err) {
		 	console.log(err);} 
		});
		request.addParameter('Name', TYPES.NVarChar,'SQL Server Express 2014');
		request.addParameter('Number', TYPES.NVarChar , 'SQLEXPRESS2014');
		request.addParameter('Cost', TYPES.Int, 11);
		request.addParameter('Price', TYPES.Int,11);
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        console.log("Product id of inserted item is " + column.value);
		      }
		    });
		});		
		connection.execSql(request);
	}

 

<!---HONumber=July15_HO4-->