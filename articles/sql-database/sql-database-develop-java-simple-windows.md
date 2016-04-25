<properties
	pageTitle="Conexión a la base de datos SQL mediante Java con JDBC en Windows"
	description="Este tema muestra un ejemplo de código Java que puede usar para conectarse a la base de datos SQL de Azure. El ejemplo usa JDBC y se ejecuta en un equipo cliente de Windows."
	services="sql-database"
	documentationCenter=""
	authors="ajlam"
	manager="jhubbard"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="04/04/2016"
	ms.author="andrela"/>


# Conexión a la base de datos SQL mediante Java con JDBC en Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tema presenta un ejemplo de código de Java que puede usar para conectarse a la base de datos SQL de Azure. El ejemplo de Java se basa en la versión 1.8 del kit de desarrollo de Java (JDK). El ejemplo se conecta a una base de datos SQL de Azure mediante el controlador JDBC.

## Paso 1: Configuración del entorno de desarrollo

Instale controladores y bibliotecas:

- [Microsoft JDBC Driver v4.2 para SQL Server](http://www.microsoft.com/download/details.aspx?displaylang=en&id=11774)
- Cualquier plataforma de sistema operativo que ejecute [Java Development Kit 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

## Paso 2: Creación de una base de datos SQL

Vea la [página de introducción](sql-database-get-started.md) para aprender a crear una base de datos de ejemplo. Es importante seguir las directrices para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestran a continuación solo funcionan con el **esquema de AdventureWorks**.

## Paso 3: Obtención de la cadena de conexión

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] Si usa el controlador JDBC de JTDS, tiene que agregar "ssl=require" a la dirección URL de la cadena de conexión y establecer la siguiente opción para JVM "-Djsse.enableCBCProtection=false". Esta opción de JVM deshabilita una revisión para una vulnerabilidad de seguridad; por tanto, asegúrese de entender qué riesgo implica antes de establecer esta opción.

## Paso 4: Conexión

Utilice la clase de conexión para conectarse a Base de datos SQL.

	// Use the JDBC driver
	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
		public class SQLDatabaseConnection {
	
			// Connect to your database.
			// Replace server name, username, and password with your credentials
			public static void main(String[] args) {
				String connectionString =
					"jdbc:sqlserver://yourserver.database.windows.net:1433;"
					+ "database=AdventureWorks;"
					+ "user=yourusername@yourserver;"
					+ "password=yourpassword;"
					+ "encrypt=true;"
					+ "trustServerCertificate=false;"
					+ "hostNameInCertificate=*.database.windows.net;"
					+ "loginTimeout=30;";
			
				// Declare the JDBC objects.
				Connection connection = null;
							
				try {
					connection = DriverManager.getConnection(connectionString);
	
				}
				catch (Exception e) {
					e.printStackTrace();
				}
				finally {
					if (connection != null) try { connection.close(); } catch(Exception e) {}
				}
			}
		}

## Paso 5: Ejecución de una consulta
En este ejemplo, se realiza una conexión a Base de datos de SQL Azure, se ejecuta una instrucción SELECT y se devuelven las filas seleccionadas.

	// Use the JDBC driver
	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
		public class SQLDatabaseConnection {
	
			// Connect to your database.
			// Replace server name, username, and password with your credentials
			public static void main(String[] args) {
				String connectionString =
					"jdbc:sqlserver://yourserver.database.windows.net:1433;"
					+ "database=AdventureWorks;"
					+ "user=yourusername@yourserver;"
					+ "password=yourpassword;"
					+ "encrypt=true;"
					+ "trustServerCertificate=false;"
					+ "hostNameInCertificate=*.database.windows.net;"
					+ "loginTimeout=30;";
			
				// Declare the JDBC objects.
				Connection connection = null;
				Statement statement = null; 
				ResultSet resultSet = null;
							
				try {
					connection = DriverManager.getConnection(connectionString);
	
					// Create and execute a SELECT SQL statement.
					String selectSql = "SELECT TOP 10 Title, FirstName, LastName from SalesLT.Customer";
					statement = connection.createStatement();
					resultSet = statement.executeQuery(selectSql);
	
					// Print results from select statement
					while (resultSet.next()) 
					{
						System.out.println(resultSet.getString(2) + " "
							+ resultSet.getString(3));
					}
				}
				catch (Exception e) {
					e.printStackTrace();
				}
				finally {
					// Close the connections after the data has been handled.
					if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
					if (statement != null) try { statement.close(); } catch(Exception e) {}
					if (connection != null) try { connection.close(); } catch(Exception e) {}
				}
			}
		}

## Paso 6: Inserción de una fila
En este ejemplo, se ejecuta una instrucción INSERT, se pasan parámetros y se recupera el valor de clave principal generado automáticamente.

	// Use the JDBC driver
	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
		public class SQLDatabaseConnection {
	
			// Connect to your database.
			// Replace server name, username, and password with your credentials
			public static void main(String[] args) {
				String connectionString =
					"jdbc:sqlserver://yourserver.database.windows.net:1433;"
					+ "database=AdventureWorks;"
					+ "user=yourusername@yourserver;"
					+ "password=yourpassword;"
					+ "encrypt=true;"
					+ "trustServerCertificate=false;"
					+ "hostNameInCertificate=*.database.windows.net;"
					+ "loginTimeout=30;";
			
				// Declare the JDBC objects.
				Connection connection = null;
				Statement statement = null; 
				ResultSet resultSet = null;
				PreparedStatement prepsInsertProduct = null;
				
				try {
					connection = DriverManager.getConnection(connectionString);
	
					// Create and execute an INSERT SQL prepared statement.
					String insertSql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES "
						+ "('NewBike', 'BikeNew', 'Blue', 50, 120, '2016-01-01');";
	
					prepsInsertProduct = connection.prepareStatement(
						insertSql,
						Statement.RETURN_GENERATED_KEYS);
					prepsInsertProduct.execute();
					
					// Retrieve the generated key from the insert.
					resultSet = prepsInsertProduct.getGeneratedKeys();
					
					// Print the ID of the inserted row.
					while (resultSet.next()) {
						System.out.println("Generated: " + resultSet.getString(1));
					}
				}
				catch (Exception e) {
					e.printStackTrace();
				}
				finally {
					// Close the connections after the data has been handled.
					if (prepsInsertProduct != null) try { prepsInsertProduct.close(); } catch(Exception e) {}
					if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
					if (statement != null) try { statement.close(); } catch(Exception e) {}
					if (connection != null) try { connection.close(); } catch(Exception e) {}
				}
			}
		}


## Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de Java](/develop/java/).

<!---HONumber=AcomDC_0413_2016-->