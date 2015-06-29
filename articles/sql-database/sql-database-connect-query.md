<properties
	pageTitle="Conexión y consultas a la base de datos SQL con C#"
	description="Ejemplo de código para un cliente de C# que usa ADO.NET para conectarse e interactuar con la base de datos de AdventureWorks en el servicio de nube de la base de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="ckarst"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="04/14/2015"
	ms.author="cakarst"/>


# Conexión y consultas a Base de datos SQL con C&#x23;


Este tema proporciona un ejemplo de código C# que muestra cómo conectarse a una base de datos SQL de AdventureWorks existente mediante el uso de ADO.NET. El ejemplo se compila en una aplicación de consola que consulta la base de datos y muestra el conjunto de resultados.


## Requisitos previos


- Base de datos de AdventureWorks en base de datos SQL de Azure. [Cree una en solo unos minutos](sql-database-get-started.md).
- [Visual Studio con .NET Framework](https://www.visualstudio.com/es-es/visual-studio-homepage-vs.aspx).


## Paso 1: Aplicación de consola


1. Cree una aplicación de consola en C# mediante Visual Studio.


![Conexión y consulta](./media/sql-database-connect-query/ConnectandQuery_VisualStudio.png)


## Paso 2: Ejemplo de código SQL


1. Copie y pegue el ejemplo de código que se muestra continuación en la aplicación de consola.


> [AZURE.WARNING]El ejemplo de código está diseñado para ser lo más corto posible con el fin de facilitar la comprensión. El ejemplo no se ha diseñado para el uso en entornos de producción.


Este código no está pensado para la producción. Si desea implementar código para entornos de producción, a continuación se describen determinadas prácticas recomendadas del sector:


- Control de excepciones.
- Lógica de reintento para errores transitorios.
- Almacenamiento seguro de contraseñas en un archivo de configuración.



### Código fuente de ejemplo de C#.


Pegue el código fuente siguiente en su archivo **Program.cs**.


	using System;  // C#
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Data.SqlClient;

	namespace ConnectandQuery_Example
	{
		class Program
		{
			static void Main()
			{
				string SQLConnectionString = "[Your_Connection_String]";
				// Create a SqlConnection from the provided connection string.
				using (SqlConnection connection = new SqlConnection(SQLConnectionString))
				{
					// Begin to formulate the command.
					SqlCommand command = new SqlCommand();
					command.Connection = connection;

					// Specify the query to be executed.
					command.CommandType = System.Data.CommandType.Text;
						command.CommandText =
						@"SELECT TOP 10
						CustomerID, NameStyle, Title, FirstName, LastName
						FROM SalesLT.Customer";

					// Open connection to database.
					connection.Open();

					// Read data from the query.
					SqlDataReader reader = command.ExecuteReader();
					while (reader.Read())
					{
						// Formatting will depend on the contents of the query.
						Console.WriteLine("Value: {0}, {1}, {2}, {3}, {4}",
							reader[0], reader[1], reader[2], reader[3], reader[4]);
					}
				}
				Console.WriteLine("Press any key to continue...");
				Console.ReadKey();
			}
		}
	}


## Paso 3: Búsqueda de la cadena de conexión para su base de datos


1. Abra el [Portal de vista previa de Azure](http://portal.azure.com/).
2. Haga clic en **Examinar** > **Bases de datos SQL** > **Base de datos de "Adventure Works"** > **Propiedades** > **Mostrar cadenas de conexión de la base de datos**.


![Portal](./media/sql-database-connect-query/ConnectandQuery_portal.png)


En la hoja de cadenas de conexión de la base de datos, encontrará las cadenas de conexión adecuadas para ADO.NET, ODBC, PHP y JDBC.


## Paso 4: Sustitución de la información de conexión real


- En el código fuente pegado, reemplace el marcador de posición *[Your_Connection_String]* por la cadena de conexión y asegúrese de reemplazar *your_password_here* en dicha cadena por su contraseña real.


## Paso 5: Ejecución de la aplicación


1. Para compilar y ejecutar la aplicación, haga clic en **DEPURAR** > **Iniciar depuración**.
2. El programa imprimirá los resultados de la consulta en la ventana de la consola.
 

<!---HONumber=58_postMigration-->