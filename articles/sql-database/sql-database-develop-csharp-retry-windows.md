<properties
	pageTitle="Lógica de reintento en C# para conectarse a la Base de datos SQL | Microsoft Azure"
	description="La muestra de C# incluye lógica de reintentos para interactuar de manera fiable con la base de datos de SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/08/2016"
	ms.author="annemill"/>


# Ejemplo de código: lógica de reintento en C# para conectarse a la Base de datos SQL



[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]



Este tema ofrece un ejemplo de código C# que muestra la lógica de reintento personalizada. La lógica de reintento está diseñada para procesar correctamente los errores temporales o los *errores transitorios* que tienden a desaparecer si el programa espera varios segundos y vuelve a intentarlo.


Las clases ADO.NET para conectarse al servidor Microsoft SQL Server local también pueden conectarse a la Base de datos SQL de Azure. Sin embargo, por sí mismas las clases ADO.NET no pueden proporcionar la solidez y confiabilidad necesarias en el uso de producción. Su programa cliente puede detectar errores transitorios de los que debería recuperarse de forma silenciosa y correcta y continuar por sí mismo.


A continuación se muestran un par de ejemplos de errores transitorios:


- Una conexión a través de Internet está sujeta a breves interrupciones de red, tras las cuales se puede volver a crear la conexión.
- La informática en la nube implica el equilibrio de carga que puede bloquear brevemente intentos de conectarse o realizar consultas.


## R: Identificar errores transitorios


El programa debe distinguir entre errores transitorios frente a errores persistentes. Si se programa tiene un error ortográfico en el nombre de base de datos de destino, el error "No se encontró esa base de datos" persistirá y se repetirá cada vez que vuelva a ejecutar el programa.

La lista de números de error que se clasifican como errores transitorios está disponible en:

- [Mensajes de error para los programas de cliente de base de datos SQL](sql-database-develop-error-messages.md#bkmk_connection_errors)
  - Vea la sección superior sobre *Errores transitorios, errores de pérdida de conexión*.


El ejemplo de código C# que aparece más adelante en este tema se enumeran los números de errores transitorios en un campo denominado **TransientErrorNumbers**.



## B. Ejemplo de código C#


El ejemplo de código C# del tema actual contiene la lógica de reintento y detección personalizada para controlar errores transitorios. En el ejemplo se da por supuesto que está instalado .NET Framework 4.5.1 o posterior.


El ejemplo de código sigue unas recomendaciones o directrices básicas que se aplican sin tener en cuenta la tecnología que se usa para interactuar con la Base de datos SQL de Azure. Puede ver las recomendaciones generales en:


- [Conexión a la base de datos SQL: vínculos, prácticas recomendadas y directrices de diseño](sql-database-connect-central-recommendations.md)


La muestra de código C# consta de un archivo denominado Program.cs. En la sección siguiente se proporciona su código.


### B.1 Captura y compilación de la muestra de código


Puede compilar el ejemplo con los pasos siguientes:


1. En la [edición gratuita de Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs), cree un nuevo proyecto desde la plantilla de aplicación de consola de C#.
  - Archivo > Nuevo > Proyecto > Instalado > Plantillas > Visual C# > Windows > Escritorio clásico > Aplicación de consola
  - Asigne al proyecto el nombre **RetryAdo2**.
2. Abra el panel del Explorador de soluciones.
  - Vea el nombre del proyecto.
  - Vea el nombre del archivo Program.cs.
3. Abra el archivo Program.cs.
4. Reemplace el contenido del archivo Program.cs por el código siguiente:
5. Haga clic en el menú Compilar > Compilar solución.


#### Código fuente de C# para pegar


Pegue este código en su archivo **Program.cs**.


A continuación, debe modificar las cadenas del nombre de servidor, la contraseña y así sucesivamente. Encontrará estas cadenas en el método denominado **GetSqlConnectionStringBuilder**.



```
using System;   // C#
using G = System.Collections.Generic;
using S = System.Data.SqlClient;
using T = System.Threading;
   
namespace RetryAdo2
{
   public class Program
   {
      static public int Main(string[] args)
      {
         bool succeeded = false;
         int totalNumberOfTimesToTry = 4;
         int retryIntervalSeconds = 10;
   
         for (int tries = 1;
            tries <= totalNumberOfTimesToTry;
            tries++)
         {
            try
            {
               if (tries > 1)
               {
                  Console.WriteLine
                     ("Transient error encountered. Will begin attempt number {0} of {1} max...",
                     tries, totalNumberOfTimesToTry
                     );
                  T.Thread.Sleep(1000 * retryIntervalSeconds);
                  retryIntervalSeconds = Convert.ToInt32
                     (retryIntervalSeconds * 1.5);
               }
               AccessDatabase();
               succeeded = true;
               break;
            }
   
            catch (S.SqlException sqlExc)
            {
               if (TransientErrorNumbers.Contains
                  (sqlExc.Number) == true)
               {
                  Console.WriteLine("{0}: transient occurred.", sqlExc.Number);
                  continue;
               }
               else
               {
                  Console.WriteLine(sqlExc);
                  succeeded = false;
                  break;
               }
            }
   
            catch (TestSqlException sqlExc)
            {
               if (TransientErrorNumbers.Contains
                  (sqlExc.Number) == true)
               {
                  Console.WriteLine("{0}: transient occurred. (TESTING.)", sqlExc.Number);
                  continue;
               }
               else
               {
                  Console.WriteLine(sqlExc);
                  succeeded = false;
                  break;
               }
            }
   
            catch (Exception Exc)
            {
               Console.WriteLine(Exc);
               succeeded = false;
               break;
            }
         }
   
         if (succeeded == true)
         {
            return 0;
         }
         else
         {
            Console.WriteLine("ERROR: Unable to access the database!");
            return 1;
         }
      }
   
      /// <summary>
      /// Connects to the database, reads,
      /// prints results to the console.
      /// </summary>
      static public void AccessDatabase()
      {
         //throw new TestSqlException(4060); //(7654321);  // Uncomment for testing.
   
         using (var sqlConnection = new S.SqlConnection
                  (GetSqlConnectionString()))
         {
            using (var dbCommand = sqlConnection.CreateCommand())
            {
               dbCommand.CommandText = @"
SELECT TOP 3
      ob.name,
      CAST(ob.object_id as nvarchar(32)) as [object_id]
   FROM sys.objects as ob
   WHERE ob.type='IT'
   ORDER BY ob.name;";
   
               sqlConnection.Open();
               var dataReader = dbCommand.ExecuteReader();
   
               while (dataReader.Read())
               {
                  Console.WriteLine("{0}\t{1}",
                     dataReader.GetString(0),
                     dataReader.GetString(1));
               }
            }
         }
      }
   
      /// <summary>
      /// You must edit the four 'my' string values.
      /// </summary>
      /// <returns>An ADO.NET connection string.</returns>
      static private string GetSqlConnectionString()
      {
         // Prepare the connection string to Azure SQL Database.
         var sqlConnectionSB = new S.SqlConnectionStringBuilder();
   
         // Change these values to your values.
         sqlConnectionSB.DataSource = "tcp:myazuresqldbserver.database.windows.net,1433"; //["Server"]
         sqlConnectionSB.InitialCatalog = "MyDatabase"; //["Database"]
   
         sqlConnectionSB.UserID = "MyLogin";  // "@yourservername"  as suffix sometimes.
         sqlConnectionSB.Password = "MyPassword";
         sqlConnectionSB.IntegratedSecurity = false;
   
         // Adjust these values if you like. (ADO.NET 4.5.1 or later.)
         sqlConnectionSB.ConnectRetryCount = 3;
         sqlConnectionSB.ConnectRetryInterval = 10;  // Seconds.
   
         // Leave these values as they are.
         sqlConnectionSB.IntegratedSecurity = false;
         sqlConnectionSB.Encrypt = true;
         sqlConnectionSB.ConnectTimeout = 30;
   
         return sqlConnectionSB.ToString();
      }
   
      static public G.List<int> TransientErrorNumbers =
         new G.List<int> { 4060, 40197, 40501, 40613,
         49918, 49919, 49920, 11001 };
   }
   
   /// <summary>
   /// For testing retry logic, you can have method
   /// AccessDatabase start by throwing a new
   /// TestSqlException with a Number that does
   /// or does not match a transient error number
   /// present in TransientErrorNumbers.
   /// </summary>
   internal class TestSqlException : ApplicationException
   {
      internal TestSqlException(int testErrorNumber)
      { this.Number = testErrorNumber; }
   
      internal int Number
      { get; set; }
   }
}
```



## C. Ejecución del programa


El archivo ejecutable **RetryAdo2.exe** no emite ningún parámetro. Para ejecutar el archivo .exe:

1. Abra una ventana de consola donde haya compilado el binario RetryAdo2.exe.
2. Ejecute RetryAdo2.exe sin parámetros de entrada.



```
database_firewall_rules_table   245575913
filestream_tombstone_2073058421 2073058421
filetable_updates_2105058535    2105058535
```



## D. Formas de probar la lógica de reintento

Hay varias maneras de simular un error transitorio para probar la lógica de reintento.


### D.1 Producir una excepción de prueba

El ejemplo de código incluye:

- Una pequeña segunda clase denominada **TestSqlException** con una propiedad denominada **Number**.
- `//throw new TestSqlException(4060);`, cuyos comentarios se pueden eliminar.

Si quita los comentarios de la excepción y vuelve a compilar, la siguiente ejecución de **RetryAdo2.exe** genera algo parecido a lo siguiente.



```
[C:\_MainW\VS15\RetryAdo2\RetryAdo2\bin\Debug]
>> RetryAdo2.exe
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 2 of 4 max...
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 3 of 4 max...
4060: transient occurred. (TESTING.)
Transient error encountered. Will begin attempt number 4 of 4 max...
4060: transient occurred. (TESTING.)
ERROR: Unable to access the database!

[C:\_MainW\VS15\RetryAdo2\RetryAdo2\bin\Debug]
>>
```



#### Volver a probar con un error persistente


Para demostrar que el código gestiona los errores persistentes de forma correcta, vuelva a ejecutar la prueba anterior sin usar el número de un error transitorio real como 4060. En su lugar, use el número sin sentido 7654321. El programa debería tratarlo como un error persistente y omitir cualquier reintento.



### D.2 Desconectar de la red

1. Desconecte el equipo cliente de la red.
  - En el caso de un equipo de escritorio, desconecte el cable de red.
  - En el caso de un equipo portátil, presione la combinación de función de teclas para desactivar el adaptador de red.
2. Inicie RetryAdo2.exe y espere a que la consola muestre el primer error transitorio, probablemente 11001.
3. Vuelva a conectar a la red mientras RetryAdo2.exe continúa ejecutándose.
4. Verá cómo la consola informa de que todo es correcto en un reintento posterior.


### D.3 Escribir mal temporalmente el nombre del servidor

1. Agregue temporalmente 40615 como otro número de error a **TransientErrorNumbers** y vuelva a compilar.
2. Establezca un punto de interrupción en la línea: `new S.SqlConnectionStringBuilder()`.
3. Use la característica *Editar y continuar* para escribir mal deliberadamente el nombre del servidor un par de líneas por debajo.
  - Deje que el programa se ejecute y vuelva al punto de interrupción.
  - Se produce el error 40615.
4. Corrija el error ortográfico.
5. Deje que el programa se ejecute y finalice correctamente.
6. Quite 40615 y vuelva a compilar.


## E. Vínculos relacionados

- [Ejemplos de código de inicio rápido de cliente para Base de datos SQL](sql-database-develop-quick-start-client-code-samples.md)
- [Prueba de Base de datos SQL: Use C# para crear una Base de datos SQL con la biblioteca de Base de datos SQL para .NET](sql-database-get-started-csharp.md)

<!---HONumber=AcomDC_0413_2016-->