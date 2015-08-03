<properties 
	pageTitle="Uso de bases de datos SQL de .NET (C#)" 
	description="Use el código de ejemplo de este inicio rápido para crear una aplicación moderna con C# con el respaldado de una base de datos relacional eficaz en la nube con la base de datos SQL de Azure."
	services="sql-database" 
	documentationCenter="" 
	authors="tobbox" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="tobiast"/>


# Uso de bases de datos SQL de .NET (C#) 


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


## Requisitos

### .NET Framework

.NET framework está previamente instalado con Windows. Para Linux y Mac OS X, puede descargar .NET Framework desde [Mono Project](http://www.mono-project.com/).

### Base de datos SQL

Vea la [página de introducción](sql-database-get-started.md) para obtener información sobre cómo crear una base de datos de ejemplo y obtener la cadena de conexión.

## Conexión a la base de datos SQL

La clase [System.Data.SqlClient.SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) se usa para conectarse a la base de datos SQL.
	
```
using System.Data.SqlClient;

class Sample
{
  static void Main()
  {
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
	  {
		  conn.Open();	
	  }
  }
}	
```

## Ejecución de una consulta y recuperación del conjunto de resultados 

Las clases [System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx) y [SqlDataReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqldatareader.aspx) pueden usarse para recuperar un conjunto de resultados de una consulta realizada a la base de datos SQL. Tenga en cuenta que System.Data.SqlClient también admite la recuperación de datos en [System.Data.DataSet](https://msdn.microsoft.com/library/system.data.dataset.aspx) sin conexión.
	
```
using System;
using System.Data.SqlClient;

class Sample
{
	static void Main()
	{
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
		{
			var cmd = conn.CreateCommand();
			cmd.CommandText = @"
					SELECT 
						c.CustomerID
						,c.CompanyName
						,COUNT(soh.SalesOrderID) AS OrderCount
					FROM SalesLT.Customer AS c
					LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID
					GROUP BY c.CustomerID, c.CompanyName
					ORDER BY OrderCount DESC;";

			conn.Open();	
		
			using(var reader = cmd.ExecuteReader())
			{
				while(reader.Read())
				{
					Console.WriteLine("ID: {0} Name: {1} Order Count: {2}", reader.GetInt32(0), reader.GetString(1), reader.GetInt32(2));
				}
			}					
		}
	}
}

```

## Inserción de filas, mediante el paso de parámetros, y recuperación del valor clave principal generado 

En la base de datos SQL, la propiedad [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) y el objeto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) pueden usarse para generar automáticamente los valores de [clave principal](https://msdn.microsoft.com/library/ms179610.aspx). En este ejemplo se mostrará cómo ejecutar la instrucción [insert-statement](https://msdn.microsoft.com/library/ms174335.aspx), mediante el paso seguro de parámetros que ofrecen protección frente a la [inyección SQL](https://msdn.microsoft.com/magazine/cc163917.aspx), y recuperar el valor de clave principal generado automáticamente.

El método [ExecuteScalar](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executescalar.aspx) de la clase [System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx) puede usarse para ejecutar una instrucción y recuperar la primera columna y fila devuelta por esta instrucción. La cláusula [OUTPUT](https://msdn.microsoft.com/library/ms177564.aspx) de la instrucción INSERT puede usarse para devolver los valores insertados como conjunto de resultados a la aplicación que realiza la llamada. Tenga en cuenta la cláusula OUTPUT también es compatible con las instrucciones [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx), [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) y [MERGE](https://msdn.microsoft.com/library/bb510625.aspx). Si se inserta más de una fila, deberá usar el método [ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx) para recuperar los valores insertados de todas las filas.
	
```
class Sample
{
    static void Main()
    {
		using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
        {
            var cmd = conn.CreateCommand();
            cmd.CommandText = @"
                INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) 
                OUTPUT INSERTED.ProductID
                VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP)";

            cmd.Parameters.AddWithValue("@Name", "SQL Server Express");
            cmd.Parameters.AddWithValue("@Number", "SQLEXPRESS1");
            cmd.Parameters.AddWithValue("@Cost", 0);
            cmd.Parameters.AddWithValue("@Price", 0);

            conn.Open();

            int insertedProductId = (int)cmd.ExecuteScalar();

            Console.WriteLine("Product ID {0} inserted.", insertedProductId);
        }
    }
}
```

 

<!---HONumber=July15_HO4-->