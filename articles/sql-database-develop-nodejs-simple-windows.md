<properties 
	pageTitle="Conexión a la base de datos SQL mediante Node.js en Windows" 
	description="Este tema muestra un ejemplo de código Node.js que puede usar para conectarse a la base de datos SQL de Azure. El ejemplo se ejecuta en un equipo cliente de Windows."
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
	ms.date="04/27/2015" 
	ms.author="mebha"/>


# Conexión a la base de datos SQL mediante Node.js en Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tema presenta un ejemplo de código de Node.js que puede usar para conectarse a la base de datos SQL de Azure. El programa de Node.js se ejecuta en un equipo cliente de Windows. Para administrar la conexión, se usa el controlador msnodesql.


## Requisitos


A continuación se describen los elementos de software que deben existir en el equipo de desarrollo de cliente.


-  Node.js: [versión 0.8.9 (versión de 32 bits)](http://blog.nodejs.org/2012/09/11/node-v0-8-9-stable/). Desplácese y haga clic en la descarga de Windows Installer x86 de 32 bits. No seleccione la descarga de Windows Installer x64 de 64 bits.
- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/), el programa de instalación para sistemas x86 o x64. 
- [Visual C++ 2010](https://app.vssps.visualstudio.com/profile/review?download=true&family=VisualStudioCExpress&release=VisualStudio2010&type=web&slcid=0x409&context=eyJwZSI6MSwicGMiOjEsImljIjoxLCJhbyI6MCwiYW0iOjEsIm9wIjpudWxsLCJhZCI6bnVsbCwiZmEiOjAsImF1IjpudWxsLCJjdiI6OTY4OTg2MzU1LCJmcyI6MCwic3UiOjAsImVyIjoxfQ2): la versión Express está disponible de forma gratuita desde Microsoft.
- SQL Server Native Client 11.0: disponible como Microsoft SQL Server 2012 Native Client, que se encuentra en el [Feature Pack de SQL Server 2012](http://www.microsoft.com/download/details.aspx?id=29065).


### Instalación de los módulos necesarios


En la ventana de la línea de comandos **cmd.exe**, desplácese hasta el directorio en el que se encuentra msnodesql. Escriba los comandos siguientes en el orden indicado.


	npm install msnodesql
	npm install -g node-gyp


Ahora que ya tiene instalado node-gyp, desplácese hasta el directorio *YourProjectDirectory* y, a continuación, a **node_modules\msnodesql**. A continuación, emita los siguientes comandos en su ventana **cmd.exe**.


	node-gyp configure 
	node-gyp build


A continuación, desplácese al directorio **build\release**. Copie el archivo **sqlserver.node** y péguelo en el directorio **msnodesql\lib**. Si es necesario, reemplace el archivo antiguo.


### Creación de una base de datos y recuperación de la cadena de conexión
 
Vea el tema de [introducción](sql-database-get-started.md) para obtener información sobre cómo crear una base de datos de ejemplo y recuperar la cadena de conexión. Es importante seguir las directrices para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestran a continuación solo funcionan con el **esquema de AdventureWorks**.


## Conexión a la base de datos SQL


- Copie el código siguiente en un archivo .js que se encuentra en el directorio del proyecto.


		var http = require('http');
		var sql = require('msnodesql');
		var http = require('http');
		var fs = require('fs');
		var useTrustedConnection = false;
		var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
		(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
		"Database={AdventureWorks};"
		sql.open(conn_str, function (err, conn) {
		    if (err) {
		        console.log("Error opening the connection!");
		        return;
		    }
		    else
		        console.log("Successfuly connected");
		});	


- A continuación, ejecute el archivo .js. Para ello, ejecute el comando siguiente:


		node index.js


## Ejecución de la instrucción SQL SELECT


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function (err, results) {
	        if (err) {
	            console.log("Error running query1!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log(results.rows[i]);
	        }
	    });
	});


## Inserción de filas, paso de parámetros y recuperación de la clave principal generada


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });
	});


## Transacciones


El método **conn.beginTransactions** no funciona en la base de datos SQL de Azure. En su lugar, siga el ejemplo de código para realizar transacciones en la base de datos SQL.


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
	
	
	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New ', 'SQLEXPRESS New', 1, 1, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });
	    
	    conn.queryRaw("ROLLBACK TRANSACTION; ", function (err, results) {
            	if (err) {
        		console.log("Rollback failed");
        		return;
        	}
    	    });
	});


## Procedimientos almacenados


Para que este ejemplo de código funcione, primero debe tener o crear un procedimiento almacenado que no introduzca parámetros. Puede crear un procedimiento almacenado con una herramienta como, por ejemplo, SQL Server Management Studio (SSMS.exe).


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" + 
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") + 
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");
		
	    conn.query("exec NameOfStoredProcedure", function (err, results) {
	    	if (err) {
			console.log("Error running query8!");
			return;
		}
	    });
	});

<!---HONumber=58-->