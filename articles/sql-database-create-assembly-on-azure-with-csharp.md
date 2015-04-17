<properties 
	pageTitle="CREATE ASSEMBLY en una Base de datos SQL de Azure con CSharp"
	description="Proporciona código fuente de C# para emitir CREATE ASSEMBLY en la Base de datos SQL de Azure después de la primera codificación de un archivo DLL en una cadena que contiene un número hexadecimal largo". 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="genemi"/>


# CREATE ASSEMBLY en una Base de datos SQL de Azure con CSharp


<!--
GeneMi , Latest edit = 2015-March-25  Wednesday  10:22am
Converting plain text "CREATE ASSEMBLY" into a link to the MSDN topic, ms189524.aspx. And ms186755.aspx for "CREATE FUNCTION".
-->


En este tema se proporciona un ejemplo de código de C# que puede usarse para emitir una instrucción [CREATE ASSEMBLY](http://msdn.microsoft.com/library/ms189524.aspx) en la Base de datos SQL de Azure. En las bases de datos SQL, la cláusula FROM no puede aceptar el formato simple de una ruta de acceso en el equipo local que hospeda la base de datos. Una alternativa consiste en codificar los bits binarios del archivo DLL del ensamblado en una cadena larga que contenga un número hexadecimal. A continuación, proporcione la cadena como el valor en la cláusula FROM.


### Requisitos previos


Para entender este tema, ya debe conocer parcialmente lo siguiente:


- [Funciones con valores de tabla de CLR](http://msdn.microsoft.com/library/ms131103.aspx)<br/>Explica cómo funciona la instrucción CREATE ASSEMBLY de Transact-SQL con otras instrucciones de Microsoft SQL Server local.


## A. Técnica general


1. Ejecute DROP FUNCTION y DROP ASSEMBLY, si es necesario para limpiar una ejecución anterior.
2. Recuerde la ubicación del archivo DLL de ensamblado de Microsoft .NET Framework que compiló desde su propio código. Proporcione la ubicación en el paso siguiente. 
3. Ejecute el archivo EXE del que se proporciona el código fuente de C# en este tema. Indique al archivo EXE dónde se encuentra el archivo DLL.
 - Codifique el archivo DLL binario en una cadena larga que contenga un número hexadecimal.
 - Emita una instrucción CREATE ASSEMBLY con la cadena hexadecimal especificada en la cláusula FROM.
4. [CREATE FUNCTION](http://msdn.microsoft.com/library/ms186755.aspx) para hacer referencia a un método en el ensamblado.
5. Instrucción SELECT T-SQL para llamar y probar la función.


La lista anterior no menciona...<br/>
**execute sp_configure 'clr enabled', 1;**<br/>
...porque no es necesario para la Base de datos SQL de Azure, aunque es necesario para Microsoft SQL Server.


Si es necesario para nuevas ejecuciones, el código de T-SQL para quitar la función y el ensamblado es el siguiente:


    DROP FUNCTION fnCompareStringsCaseSensitive;
    DROP ASSEMBLY CreateAssemblyFunctions3;


## B. DLL de ensamblado simple para la función de T-SQL para hacer referencia


El ejemplo de código C# trivial de esta sección se puede compilar en un archivo DLL de ensamblado.


Este ejemplo de código contiene el método **CompareCaseSensitiveNet** al que se hace referencia más adelante en una instrucción CREATE FUNCTION de T-SQL. Observe que el método está decorado con un atributo de .NET denominado **SqlFunction**. Un método que esté decorado con este atributo se puede llamarse mediante su T-SQL como una función.


	using           System;   // C#
	using SDSqTyp = System.Data.SqlTypes;
	using MSqServ = Microsoft.SqlServer.Server;
	
	namespace CreateAssemblyFunctions3
	{
	    public class SqlFunctionMethodsForSprocs
	    {
	        /// <summary> This method is referenced
	        /// by a T-SQL CREATE FUNCTION statement. </summary>
	        [MSqServ.SqlFunction(IsDeterministic = true, IsPrecise = true)]
	        static public SDSqTyp.SqlInt32 CompareCaseSensitiveNet(string strA, string strB)
	        {
	            return String.Compare(strA, strB, false);
	        }
	    }
	}


## C. Código de ejemplo de C&#x23; para un archivo EXE que emite CREATE ASSEMBLY


Al ejecutar el archivo ejecutable que se crea en este ejemplo de C#, se produce la siguiente secuencia:


1. La ejecución de línea de comandos de las llamadas al archivo EXE llama al método **Main**.
2. Main llama al método **ObtainHexStringOfAssembly**.
 - El método da como resultado una cadena SqlString que almacena el ensamblado como un número hexadecimal.
3. Main llama al método **SubmitCreateAssemblyToAzureSqlDb**.
 - La entrada principal es la cadena SqlString.
 - El resultado es una llamada a CREATE ASSEMBLY que se envía a la Base de datos SQL de Azure.


			using             System;   // C#
			using SDat      = System.Data;
			using SDSClient = System.Data.SqlClient;
			using SGlo      = System.Globalization;
			using SIo       = System.IO;
			using STex      = System.Text;
			
			namespace CreateAssemblyFromHexString6
			{
			    /// <summary>
			    /// Run the Main method on your local computer console, so it can issue a
			    /// a CREATE ASSEMBLY statement to your Azure SQL Database server:
			    /// </summary>
			    class Program
			    {
			        /// <summary>
			        /// Calls the methods in the proper sequence.
			        /// </summary>
			        /// <param name="args">
			        /// Parameters for the cmd.exe command line
			        ///    run of CreateAssemblyFromHexString6.exe:
			        /// args[0] = FullDirPathFileNameOfAssembly.
			        /// args[1] = AssemblyName.
			        ///    For the CREATE ASSEMBLY assemblyName statement.
			        /// args[2] = Azure SQL Database - ServerName, including a suffix like .database.windows.net .
			        /// args[3] = Azure SQL Database - DatabaseName.
			        /// args[4] = Azure SQL Database - LoginName.
			        /// args[5] = Azure SQL Database - Password for login.
			        ///    (Better if from .config file.)
			        /// </param>
			        static int Main(string[] args)
			        {
			            int returnCode = 1; // Only 0 (zero) means Good Success.
			            string
			                fullPathNameAssemblyFile,
			                assemblyName,
			                AzureSqlDbServerName,
			                AzureSqlDbDatabaseName,
			                AzureSqlDbLoginName,
			                AzureSqlDbPassword;
			
			            try
			            {
			                fullPathNameAssemblyFile = args[0];
			                assemblyName             = args[1];
			                AzureSqlDbServerName     = args[2];
			                AzureSqlDbDatabaseName   = args[3];
			                AzureSqlDbLoginName      = args[4];
			                AzureSqlDbPassword       = args[5];
			
			                string hexStringOfAssembly = Program
			                    .ObtainHexStringOfAssembly(fullPathNameAssemblyFile);
			
			                Program.SubmitCreateAssemblyToAzureSqlDb(
			                    hexStringOfAssembly,
			                    assemblyName,
			                    AzureSqlDbServerName,
			                    AzureSqlDbDatabaseName,
			                    AzureSqlDbLoginName,
			                    AzureSqlDbPassword);
			
			                returnCode = 0;
			            }
			            catch (Exception ex)
			            {
			                Console.WriteLine("Bad, Failure.");
			                throw ex;
			            }
			            Console.WriteLine("Good, Success.");
			            return returnCode;
			        }
			
			        /// <summary> Encodes the binary bits of the assembly DLL into a 
			        /// string containing a hexadecimal number. </summary>
			        /// <param name="fullPathToAssembly"
			        /// >Full directory path plus the file name, to the .DLL file.</param>
			        /// <returns>A string containing a hexadecimal number that encodes
			        /// the binary bits of the .DLL file.</returns>
			        static private string ObtainHexStringOfAssembly
			            (string fullPathToAssembly)
			        {
			            STex.StringBuilder sbuilder = new STex.StringBuilder("0x");
			            using (SIo.FileStream fileStream = new SIo.FileStream(
			                fullPathToAssembly,
			                SIo.FileMode.Open, SIo.FileAccess.Read, SIo.FileShare.Read)
			                )
			            {
			                int byteAsInt;
			                while (true)
			                {
			                    byteAsInt = fileStream.ReadByte();
			                    if (-1 >= byteAsInt) { break; }
			                    sbuilder.Append(byteAsInt.ToString
			                        ("X2", SGlo.CultureInfo.InvariantCulture));
			                }
			            }
			            return sbuilder.ToString();
			        }
			
			        /// <summary>
			        /// Sends a Transact-SQL CREATE ASSEMBLY FROM hexString.
			        /// </summary>
			        static private void SubmitCreateAssemblyToAzureSqlDb(
			            string hexStringOfAssembly,
			            string assemblyName,
			            string AzureSqlDbServerName,
			            string AzureSqlDbDatabaseName,
			            string AzureSqlDbLoginName,
			            string AzureSqlDbPassword)
			        {
			            string sqlCreateAssembly = "CREATE ASSEMBLY [" + assemblyName
			                + "] FROM " + hexStringOfAssembly + ";";
			            STex.StringBuilder sbuilderConnection = new STex.StringBuilder();
			
			            sbuilderConnection.Append("Server=tcp:");
			            sbuilderConnection.Append(AzureSqlDbServerName);
			            sbuilderConnection.Append(",1433;");
			
			            sbuilderConnection.Append("Database=");
			            sbuilderConnection.Append(AzureSqlDbDatabaseName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Trusted_Connection=False;");
			            sbuilderConnection.Append("Connection Timeout=30;");
			
			            sbuilderConnection.Append("User ID=");
			            sbuilderConnection.Append(AzureSqlDbLoginName);
			            sbuilderConnection.Append(";");
			
			            sbuilderConnection.Append("Password=");
			            sbuilderConnection.Append(AzureSqlDbPassword);
			            sbuilderConnection.Append(";");
			
			            using (SDSClient.SqlConnection sqlConnection =
			                new SDSClient.SqlConnection())
			            {
			                SDSClient.SqlCommand sqlCommand;
			                sqlConnection.ConnectionString = sbuilderConnection.ToString();
			                sqlCommand = sqlConnection.CreateCommand();
			                sqlCommand.CommandType = SDat.CommandType.Text;
			                sqlCommand.CommandText = sqlCreateAssembly;
			                sqlConnection.Open();
			                sqlCommand.ExecuteNonQuery();
			            }
			            return;
			        }
			    }
			}


### C.1 Compilar referencias y versiones


Cuando se compiló y probó el código de ejemplo de la herramienta EXE, se usó lo siguiente:


- Visual Studio 2013, actualización 4
 - El tipo de plantilla de proyecto era la aplicación de consola simple.
- .NET Framework 4.5


Nuestro proyecto de Visual Studio hace referencia a los ensamblados siguientes para la compilación:


- Microsoft.CSharp
- System
- System.Core
- System.Data
- System.Data.DataSetExtensions
- System.Xml
- System.Xml.Linq


### C.2 Línea de comandos para ejecutar el archivo EXE


El bloque de código siguiente muestra un ejemplo de la línea de comandos que se escribiría para ejecutar el archivo EXE desde la consola. Los parámetros de la línea de comandos se encapsulan aquí artificialmente para una mejor visualización.


	CreateAssemblyFromHexString6.exe
		C:\my\bin\debug\CreateAssemblyFunctions3.dll
		CreateAssemblyFunctions3
		myazuresqldbsvr2.database.windows.net
		myazuresqldbdab4
		myazurelogin
		Mypassword123


Para simplificar la explicación, en este ejemplo se pasa la contraseña como un parámetro de línea de comandos. Un mejor diseño sería hacer que el código C# obtuviese la contraseña a partir de un archivo CONFIG.


## D. Ejecutar la instrucción CREATE FUNCTION


Después de que el ensamblado se almacene en el servidor de Base de datos SQL de Azure, debe ejecutar una instrucción CREATE FUNCTION de Transact-SQL que haga referencia al método en el ensamblado.


El siguiente bloque de código de Transact-SQL incluye un par de instrucciones SELECT que no son esenciales, para demostrar que el sistema de base de datos tiene los registros del ensamblado y de la función. Por último, hay una instrucción SELECT que llama a la función.


	SELECT a11.*, am2.*
		FROM           sys.assemblies       AS a11
		    INNER JOIN sys.assembly_modules AS am2 ON am2.assembly_id = a11.assembly_id
		WHERE a11.name = 'CreateAssemblyFunctions3';
	GO
	
	CREATE FUNCTION fnCompareStringsCaseSensitive
	    (@strA nvarchar(128), @strB nvarchar(128))
	    returns int
	    AS EXTERNAL NAME
	        CreateAssemblyFunctions3
	            .[CreateAssemblyFunctions3.SqlFunctionMethodsForSprocs]
	                .CompareCaseSensitiveNet;
	GO
	SELECT * FROM sys.objects WHERE name = 'fnCompareStringsCaseSensitive';
	
	 -- Use the new function.
	SELECT dbo.fnCompareStringsCaseSensitive('BLUE', 'blue') as returnedValue;
	GO


El bloque de código de Transact-SQL anterior finaliza con una instrucción SELECT que llama a la función nueva. Puede colocar la instrucción SELECT en un procedimiento almacenado.


<!-- EndOfFile -->


<!--HONumber=49-->