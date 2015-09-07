<properties 
	pageTitle="Ejemplo de código: lógica de reintento en C# para conectarse a la Base de datos SQL | Microsoft Azure"
	description="El ejemplo de C# incluye lógica de reintento sólida para interactuar con la Base de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags 
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2015"
	ms.author="genemi"/>


# Ejemplo de código: lógica de reintento en C# para conectarse a la Base de datos SQL


<!--
sql-database-develop-csharp-retry-windows.md  ,  NEW
mgm4f20150723retryfrommsdn68
. . . .
Old Titles on MSDN:  Code sample: Retry logic for connecting to Azure SQL Database with ADO.NET
.
How to: Connect to Azure SQL Database by using ADO.NET
.
ShortId on MSDN was:  ee336243.aspx
Dx  4d7936fd-341c-4a37-8796-25e385ae6c5b
-->


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tema ofrece un ejemplo de código C# que muestra la lógica de reintento personalizada. La lógica de reintento está diseñada para procesar correctamente los errores temporales o *errores transitorios* que tienden a desaparecer si el programa espera unos segundos y vuelve a intentarlo.


Las clases de ADO.NET que se usan para conectarse a su Microsoft SQL Server local también pueden conectarse a la Base de datos SQL de Azure. Sin embargo, por sí mismas las clases ADO.NET no pueden proporcionar la solidez y confiabilidad necesarias en el uso de producción. Su programa cliente puede encontrar errores transitorios de los que debería recuperarse de forma silenciosa y correcta por sí mismo. Un par de ejemplos de errores transitorios:


- Una conexión a través de Internet está sujeta a breves interrupciones de red, tras las cuales se puede volver a crear la conexión.

- La informática en la nube implica el equilibrio de carga que puede bloquear brevemente intentos de conectarse o realizar consultas.


## Identificar errores transitorios


El programa debe distinguir entre errores transitorios frente a errores persistentes. Si se programa tiene un error ortográfico en el nombre de base de datos de destino, el error "No se encontró esa base de datos" persistirá y se repetirá cada vez que vuelva a ejecutar el programa.


La lista de números de error que se clasifican como errores transitorios está disponible en:


- [Mensajes de error para los programas de cliente de base de datos SQL](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - Vea la sección acerca de *Errores transitorios, errores de pérdida de conexión*.


## Ejemplo de código C#


El ejemplo de código C# del tema actual contiene la lógica de reintento y detección personalizada para controlar errores transitorios.


El ejemplo de código sigue unas recomendaciones o directrices básicas que se aplican sin tener en cuenta la tecnología que se usa para interactuar con la Base de datos SQL de Azure. Puede ver las recomendaciones generales en:


- [Conexión a la base de datos SQL: vínculos, prácticas recomendadas y directrices de diseño](sql-database-connect-central-recommendations.md)


El ejemplo de código C# consta de los dos archivos. cs cuyo contenido se pega en las secciones siguientes. Sus nombres de archivo son:


- `Program.cs`
- `Custom_SqlDatabaseTransientErrorDetectionStrategy.cs`


## Compilar y ejecutar el ejemplo de código


Puede compilar el ejemplo con los pasos siguientes:


1. En Visual Studio, cree un nuevo proyecto desde una plantilla de aplicación de consola de C#.

2. Haga clic con el botón secundario en el proyecto y, a continuación, agregue el .cs para el que se ofrece el código fuente en este tema.

3. En un ventana de comandos `cmd.exe`, ejecute el programa como se muestra a continuación. También se muestra la salida real de una ejecución.


		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>> ConsoleApplication1.exe
		database_firewall_rules_table   245575913
		filestream_tombstone_2073058421 2073058421
		filetable_updates_2105058535    2105058535
		
		[C:\MyVS\ConsoleApplication1\ConsoleApplication1\bin\Debug]
		>>


El código fuente de C# para los archivos .cs se encuentran en las secciones siguientes.


## Archivo Program.cs


El programa de demostración está diseñado de manera que:


- Un error transitorio durante un intento de conexión lleve a un reintento.

- Un error transitorio durante un comando de consulta provoca que el programa descarte la conexión y cree una nueva antes de reintentar el comando de consulta.


Microsoft no se pronuncia respecto a la recomendación o no de esta opción de diseño. El programa de demostración muestra la flexibilidad del diseño que se encuentra disponible.


### Longitud del código fuente


La longitud del archivo `Program.cs` se debe principalmente a la lógica que captura excepciones.


Al final de este tema se presenta una [versión más breve](#shorter_program_cs) de este archivo `Program.cs`, con toda la lógica de reintento y control de `Exception` eliminados.


### Pila de llamadas


El método `Main` está en `Program.cs`. La pila de llamadas se ejecuta de la siguiente manera:


1. `Main` llama a `ConnectAndQuery`.

2. `ConnectAndQuery` llama a `EstablishConnection`.

3. `EstablishConnection` llama a `IssueQueryCommand`.


### Código fuente de Program.cs


	using     System;  // C#, pure ADO.NET, no Enterprise Library.
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	using T = System.Threading;
	
	namespace ConsoleApplication1
	{
	    class Program
	    {
	        // Fields, shared among methods.
	        C.SqlConnection sqlConnection;
	        C.SqlConnectionStringBuilder scsBuilder;
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	        /// <summary>
	        /// Prepares values for a connection. Then inside a loop, it calls a method
	        /// that opens a connection. The called method calls yet another method
	        /// that issues a query.
	        /// The loop reiterates only if a transient error is encountered.
	        /// </summary>
	        void ConnectAndQuery()
	        {
	            int connectionTimeoutSeconds = 30;  // Default of 15 seconds is too short over the Internet, sometimes.
	            int maxCountTriesConnectAndQuery = 3;  // You can adjust the various retry count values.
	            int secondsBetweenRetries = 6;  // Simple retry strategy.
	
	            // [A.1] Prepare the connection string to Azure SQL Database.
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";  // @yourservername suffix sometimes.
	            this.scsBuilder["Password"] = "MyPassword";
	            this.scsBuilder["Database"] = "MyDatabase";
	            // Leave these values as they are.
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = connectionTimeoutSeconds;
	
	            //-------------------------------------------------------
	            // Preparations are complete.
	
	            for (int cc = 1; cc <= maxCountTriesConnectAndQuery; cc++)
	            {
	                try
	                {
	                    // [A.2] Connect, which proceeds to issue a query command.
	                    this.EstablishConnection();
	
	                    // [A.3] All has gone well, so let the program end.
	                    break;
	                }
	                catch (C.SqlException sqlExc)
	                {
	                    bool isTransientError;
	
	                    // [A.4] Check whether sqlExc.Number is on the whitelist of transients.
	                    isTransientError = Custom_SqlDatabaseTransientErrorDetectionStrategy
	                        .IsTransientStatic(sqlExc);
	
	                    if (isTransientError == false)  // Is a persistent error...
	                    {
	                        Console.WriteLine();
	                        Console.WriteLine("Persistent error suffered, SqlException.Number=={0}.  Will terminate.",
	                            sqlExc.Number);
	                        Console.WriteLine(sqlExc.ToString());
	
	                        // [A.5] Either the connection attempt or the query command attempt suffered a persistent SqlException.
	                        // Break the loop, let the hopeless program end.
	                        break;
	                    }
	
	                    // [A.6] The SqlException identified a transient fault from an attempt to issue a query command.
	                    // So let this method reloop and try again. However, we recommend that the new query
	                    // attempt should start at the beginning and establish a new connection.
	                    Console.WriteLine();
	                    Console.WriteLine("Transient error encountered.  SqlException.Number=={0}."
	                        + "  Program might retry by itself.", sqlExc.Number);
	                    Console.WriteLine("{0} = Attempts so far. Might retry.", cc);
	                    Console.WriteLine(sqlExc.Message);
	                }
	                catch (Exception exc)
	                {
	                    Console.WriteLine();
	                    Console.WriteLine("Unexpected exception type caught in Main. Will terminate.");
	
	                    // [A.7] The program must end, so re-throw the unrecognized error.
	                    throw exc;
	                }
	
	                // [A.8] Throw an application exception if transient SqlExceptions caused us
	                // to exceed our self-imposed maximum count of retries.
	                if (cc > maxCountTriesConnectAndQuery)
	                {
	                    Console.WriteLine();
	                    string mesg = String.Format(
	                        "Transient errors suffered in too many retries ({0}). Will terminate.",
	                        cc - 1);
	                    Console.WriteLine(mesg);
	
	                    // [A.9] To end the program, throw a new exception of a different type.
	                    ApplicationException appExc = new ApplicationException(mesg);
	                    throw appExc;
	                }
	                // Else, can retry.
	
	                // A very simple retry strategy, a brief pause before looping.
	                T.Thread.Sleep(1000 * secondsBetweenRetries);
	            } // for cc
	            return;
	        } // method ConnectAndQuery
	
	
	        /// <summary>
	        /// Open a connection, then call a method that issues a query.
	        /// </summary>
	        void EstablishConnection()
	        {
	            try
	            {
	                // [B.1] The 'using' statement will .Dispose() the connection.
	                // If you are working with a connection pool, you might want instead
	                // to merely .Close() the connection.
	                using (this.sqlConnection = new C.SqlConnection(this.scsBuilder.ToString()))
	                {
	                    // [B.2] Open a connection.
	                    sqlConnection.Open();
	                    // [B.3]
	                    this.IssueQueryCommand();
	                }
	            }
	            catch (Exception exc)
	            {
	                // [B.4] This re-throw means we discard the connection whenever
	                // any error occurs during query command, even for a transient error.
	                throw exc;  // [B.5] Let caller assess any exception, SqlException or any kind.
	            }
	            return;
	        } // method EstablishConnection
	
	
	        /// <summary>
	        /// Issue a query, then write the result rows to the console.
	        /// </summary>
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	            X.StringBuilder sBuilder = new X.StringBuilder(512);
	
	            try
	            {
	                // [C.1] Use the connection to create a query command.
	                using (dbCommand = this.sqlConnection.CreateCommand())
	                {
	                    dbCommand.CommandText =
	                        @"SELECT TOP 3
	                              ob.name,
	                              CAST(ob.object_id as nvarchar(32)) as [object_id]
	                          FROM sys.objects as ob
	                          WHERE ob.type='IT'
	                          ORDER BY ob.name;";
	
	                    // [C.2] Issue the query command through the connection.
	                    using (dReader = dbCommand.ExecuteReader())
	                    {
	                        // [C.3] Loop through all returned rows, writing the data to the console.
	                        while (dReader.Read())
	                        {
	                            sBuilder.Length = 0;
	                            sBuilder.Append(dReader.GetString(0));
	                            sBuilder.Append("\t");
	                            sBuilder.Append(dReader.GetString(1));
	
	                            Console.WriteLine(sBuilder.ToString());
	                        }
	                    }
	                }
	            }
	            catch (Exception exc)
	            {
	                throw exc; // Let caller assess any exception.
	            }
	            return;
	        } // method IssueQueryCommand
	    } // class Program
	}


## Archivo de código Custom\_SqlDatabaseTransientErrorDetectionStrategy.cs


[`SqlDatabaseTransientErrorDetectionStrategy`](http://msdn.microsoft.com/library/microsoft.practices.enterpriselibrary.transientfaulthandling.sqldatabasetransienterrordetectionstrategy.aspx) es una clase de la API de Enterprise Library (EntLib). El presente ejemplo de código usa la siguiente clase personalizada que toma prestada la idea de la clase EntLib.


	using     System;
	using G = System.Collections.Generic;
	using C = System.Data.SqlClient;
	
	namespace ConsoleApplication1
	{
	    /// <summary>
	    /// A custom alternative to class SqlDatabaeTransientErrorDetectionStrategy.
	    /// </summary>
	    public class Custom_SqlDatabaseTransientErrorDetectionStrategy
	    {
	        static private G.List<int> M_listTransientErrorNumbers;
	
	
	        /// <summary>
	        /// This method happens to match ITransientErrorDetectionStrategy of EntLib60.
	        /// </summary>
	        public bool IsTransient(Exception exc)
	        {
	            return IsTransientStatic(exc);
	        }
	
	
	        /// <summary>
	        /// For general use beyond formal Enterprise Library classes.
	        /// </summary>
	        static public bool IsTransientStatic(Exception exc)
	        {
	            bool returnBool = false;  // Assume is a persistent error.
	            C.SqlException sqlExc;
	
	            if (exc is C.SqlException)
	            {
	                sqlExc = exc as C.SqlException;
	                if (M_listTransientErrorNumbers.Contains(sqlExc.Number) == true)
	                {
	                    returnBool = true;  // Error is transient, not persistent.
	                }
	            }
	            return returnBool;
	        }
	
	
	        /// <summary>
	        /// Lists the SqlException.Number values that are considered
	        /// to indicate transient errors.
	        /// </summary>
	        static Custom_SqlDatabaseTransientErrorDetectionStrategy()
	        {
	            int[] arrayOfTransientErrorNumbers =
	                {4060, 10928, 10929, 40197, 40501, 40613 };
	
	            M_listTransientErrorNumbers = new G.List<int>(arrayOfTransientErrorNumbers);
	        }
	    } // class Custom_SqlDatabaseTransientErrorDetectionStrategy
	}


<a id="shorter_program_cs" name="shorter_program_cs"></a>


&nbsp;


## Versión abreviada de Program.cs


El código fuente de esta sección es la repetición abreviada del archivo `Program.cs` más largo presentado anteriormente. Se ha quitado toda la lógica de reintento y todo el control de excepciones.


La versión corta facilita la visualización de las llamadas de ADO.NET, sabiendo que estas suelen funcionar. Normalmente no se producen errores transitorios y no se generan excepciones. Y normalmente un paracaidista no necesita un paracaídas de seguridad.


	using     System;  // C#, pure ADO.NET, no retry logic, no Exception handling.
	using X = System.Text;
	using D = System.Data;
	using C = System.Data.SqlClient;
	
	namespace ConsoleApplication1_dn864744
	{
	    class Program
	    {
	        C.SqlConnection sqlConnection;
	        C.SqlConnectionStringBuilder scsBuilder;
	
	        static void Main(string[] args)
	        {
	            new Program().ConnectAndQuery();
	        }
	
	        void ConnectAndQuery()
	        {
	            this.scsBuilder = new C.SqlConnectionStringBuilder();
	            // Change these values to your values.
	            this.scsBuilder["Server"] = "tcp:myazuresqldbserver.database.windows.net,1433";
	            this.scsBuilder["User ID"] = "MyLogin";
	            this.scsBuilder["Password"] = "MyPassword";
	            this.scsBuilder["Database"] = "MyDatabase";
	            this.scsBuilder["Trusted_Connection"] = false;
	            this.scsBuilder["Integrated Security"] = false;
	            this.scsBuilder["Encrypt"] = true;
	            this.scsBuilder["Connection Timeout"] = 30;
	
	            this.EstablishConnection();
	        } // method ConnectAndQuery
	
	        void EstablishConnection()
	        {
	            using (this.sqlConnection = new C.SqlConnection(this.scsBuilder.ToString()))
	            {
	                sqlConnection.Open();
	                this.IssueQueryCommand();
	            }
	        } // method EstablishConnection
	
	        void IssueQueryCommand()
	        {
	            D.IDataReader dReader = null;
	            D.IDbCommand dbCommand = null;
	            X.StringBuilder sBuilder = new X.StringBuilder(512);
	
	            using (dbCommand = this.sqlConnection.CreateCommand())
	            {
	                dbCommand.CommandText =
	                    @"SELECT TOP 3 ob.name, CAST(ob.object_id as nvarchar(32)) as [object_id]
	                        FROM sys.objects as ob
	                        WHERE ob.type='IT'
	                        ORDER BY ob.name;";
	
	                using (dReader = dbCommand.ExecuteReader())
	                {
	                    while (dReader.Read())
	                    {
	                        sBuilder.Length = 0;
	                        sBuilder.Append(dReader.GetString(0));
	                        sBuilder.Append("\t");
	                        sBuilder.Append(dReader.GetString(1));
	                        Console.WriteLine(sBuilder.ToString());
	                    }
	                }
	            }
	        } // method IssueQueryCommand
	    } // class Program
	}


## Vínculos relacionados


- [Ejemplos de código de inicio rápido de cliente para Base de datos SQL](sql-database-develop-quick-start-client-code-samples.md)

<!---HONumber=August15_HO9-->