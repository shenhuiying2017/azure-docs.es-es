<properties
	pageTitle="Conexión a la base de datos SQL mediante PHP en Windows"
	description="Presenta un programa PHP de ejemplo que se conecta a la base de datos SQL de Azure desde un cliente de Windows y proporciona vínculos a los componentes de software necesarios que necesita el cliente."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="04/27/2015"
	ms.author="genemi"/>


# Conexión a la base de datos SQL mediante PHP en Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tema muestra cómo puede conectarse a la base de datos SQL de Azure desde una aplicación cliente escrita en PHP que se ejecuta en Windows.


## Requisitos previos


Para ejecutar el ejemplo de código PHP que se proporciona en este tema, el equipo cliente debe tener los siguientes elementos de software instalados:


- [Controladores de Microsoft para PHP para Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=20098) (SQLSRV32.EXE contiene las versiones más recientes)
- [Microsoft SQL Server Native Client 11.0](http://www.microsoft.com/download/details.aspx?id=36434)
- Las instalaciones específicas se deben realizar mediante el [Instalador de plataforma web](http://www.microsoft.com/web/downloads/platform.aspx). Consulte la siguiente sección para obtener información detallada.


### Instalaciones con el Instalador de plataforma web


1. Descargue y ejecute el [Instalador de plataforma web](http://www.microsoft.com/web/downloads/platform.aspx) y seleccione los módulos necesarios.
2. Use el [Instalador de plataforma web](http://www.microsoft.com/web/downloads/platform.aspx) para instalar los componentes siguiente:
 - IIS o IIS Express.<br/>Por ejemplo, encontrará la [descarga de IIS Express 8.0](http://www.microsoft.com/download/details.aspx?id=34679) al buscar en la Web: descarga de IIS Express.
 - PHP para Windows, versión 5.5 o posterior (versión de 32 bits).
3. Edite el archivo PHP.INI. Agregue una entrada en la sección de extensiones dinámicas, como se muestra a continuación:<br/>**extension=php_sqlsrv_55_nts.dll**


## Conexión a la base de datos SQL


La función **OpenConnection** se llama casi al principio de todas las demás funciones siguientes.


	function OpenConnection()
	{
		try
		{
			$serverName = "tcp:myserver.database.windows.net,1433";

			$connectionOptions = array("Database"=>"AdventureWorks",
				"Uid"=>"MyUser", "PWD"=>"MyPassword");

			$conn = sqlsrv_connect($serverName, $connectionOptions);

			if($conn == false)
				die(FormatErrors(sqlsrv_errors()));
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Inserción de valores en la tabla


	function InsertData()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "INSERT INTO [SalesLT].[Customer]
					   ([NameStyle],[FirstName],[MiddleName],[LastName],
					   [PasswordHash],[PasswordSalt],[rowguid],[ModifiedDate])
					   VALUES (?,?,?,?,?,?,?,?)";

			$params = array("0", "Luiz", "F", "Santos",
				"L/Rlwxzp4w7RWmEgXX+/A7cXaePEPcp+KwQhl2fJL7w=",
				"1KjXYs4=", "88949BFE-0BB4-4148-AFC2-DD8C8DAE4CD6",
				date("Y-m-d"));

			$insertReview = sqlsrv_prepare($conn, $tsql, $params);
			if($insertReview == FALSE)
				die(FormatErrors( sqlsrv_errors()));

			if(sqlsrv_execute($insertReview) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			sqlsrv_free_stmt($insertReview);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Eliminación de valores de la tabla


	function DeleteData()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "DELETE FROM [SalesLT].[Customer] WHERE FirstName=? AND LastName=?";
			$params = array($_REQUEST['FirstName'], $_REQUEST['LastName']);

			$deleteReview = sqlsrv_prepare($conn, $tsql, $params);
			if($deleteReview == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			if(sqlsrv_execute($deleteReview) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			sqlsrv_free_stmt($deleteReview);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Selección de valores de la tabla


	function ReadData()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "SELECT [CompanyName] FROM SalesLT.Customer";

			$getProducts = sqlsrv_query($conn, $tsql);

			if ($getProducts == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			$productCount = 0;

			while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))
			{
				echo($row['CompanyName']);
				echo("<br/>");
				$productCount++;
			}

			sqlsrv_free_stmt($getProducts);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Transacciones


	function Transactions()
	{
		try
		{
			$conn = OpenConnection();

			if (sqlsrv_begin_transaction($conn) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			/* Initialize parameter values. */
			$orderId = 43659;
			$qty = 5;
			$productId = 709;
			$offerId = 1;
			$price = 5.70;

			/* Set up and execute the first query. */
			$tsql1 = "INSERT INTO SalesLT.SalesOrderDetail
			   (SalesOrderID,OrderQty,ProductID,SpecialOfferID,UnitPrice)
			   VALUES (?, ?, ?, ?, ?)";
			$params1 = array($orderId, $qty, $productId, $offerId, $price);
			$stmt1 = sqlsrv_query($conn, $tsql1, $params1);

			/* Set up and execute the second query. */
			$tsql2 = "UPDATE Production.ProductInventory SET Quantity = (Quantity - ?)
					  WHERE ProductID = ?";
			$params2 = array($qty, $productId);
			$stmt2 = sqlsrv_query( $conn, $tsql2, $params2 );

			/* If both queries were successful, commit the transaction. */
			/* Otherwise, rollback the transaction. */
			if($stmt1 && $stmt2)
			{
					sqlsrv_commit($conn);
					echo "Transaction was committed.\n";
			}
			else
			{
					sqlsrv_rollback($conn);
					echo "Transaction was rolled back.\n";
			}

			/* Free statement and connection resources. */
			sqlsrv_free_stmt( $stmt1);
			sqlsrv_free_stmt( $stmt2);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Procedimientos almacenados


	function ExecuteSProc()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "EXEC [dbo].[uspLogError]";

			$execReview = sqlsrv_prepare($conn, $tsql);
			if($execReview == FALSE)
				die(FormatErrors( sqlsrv_errors()));

			if(sqlsrv_execute($execReview) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			sqlsrv_free_stmt($execReview);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Lecturas adicionales


Para obtener más información sobre el uso y la instalación de PHP, vea [Acceso a bases de datos de SQL Server con PHP](http://technet.microsoft.com/library/cc793139.aspx).

<!---HONumber=58-->