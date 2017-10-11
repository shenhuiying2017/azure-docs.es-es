
<a name="cs_0_csharpprogramexample_h2"/>

## <a name="c-program-example"></a>Ejemplo de programa C#

En las secciones siguientes de este artículo se presenta un programa C# que usa ADO.NET para enviar instrucciones Transact-SQL a la base de datos SQL. El programa C# realiza las acciones siguientes:

1. [Se conecta a nuestra base de datos SQL mediante ADO.NET](#cs_1_connect).
2. [Crea tablas](#cs_2_createtables).
3. [Rellena las tablas con datos, mediante la emisión de instrucciones T-SQL INSERT](#cs_3_insert).
4. [Actualiza datos mediante una combinación](#cs_4_updatejoin).
5. [Elimina datos mediante una combinación](#cs_5_deletejoin).
6. [Selecciona filas de datos mediante una combinación](#cs_6_selectrows).
7. Cierra la conexión (lo que quita cualquier tabla temporal de tempdb).

El programa C# contiene:

- El código C# para conectarse a la base de datos.
- Métodos que devuelven el código fuente T-SQL.
- Dos métodos que enviar el código T-SQL a la base de datos.

#### <a name="to-compile-and-run"></a>Para compilarlo y ejecutarlo

Este programa C# es lógicamente un archivo .cs. Pero aquí el programa se divide físicamente en varios bloques de código, para que cada bloque resulte más fácil de ver y comprender. Para compilar y ejecutar este programa, haga lo siguiente:

1. Cree un proyecto C# en Visual Studio.
    - El tipo de proyecto debe ser un *consola* aplicación de algo parecido a la siguiente jerarquía: **plantillas** > **Visual C#** >  **Escritorio clásico de Windows** > **(.NET Framework) de la aplicación de consola**.
3. En el archivo **Program.cs**, borre las primeras líneas cortas de código.
3. En Program.cs, copie y pegue cada uno de los siguientes bloques, en la misma secuencia en que se muestran aquí.
4. En Program.cs, edite los valores siguientes en el método **Main**:

   - **cb.DataSource**
   - **cd.UserID**
   - **cb.Password**
   - **InitialCatalog**

5. Compruebe que se haga referencia al ensamblado **System.Data.dll**. Para comprobarlo, expanda el nodo **Referencias** en el panel **Explorador de soluciones**.
6. Para compilar el programa en Visual Studio, haga clic en el menú **Compilación**.
7. Para ejecutar el programa desde Visual Studio, haga clic en el botón **Iniciar**. La salida del informe se muestra en una ventana cmd.exe.

> [!NOTE]
> Tiene la opción de editar el código T-SQL para agregar un carácter **#** inicial a los nombres de tabla, lo que los crea como tablas temporales en **tempdb**. Esto puede ser útil como demostración, cuando no haya ninguna base de datos de prueba disponible. Las tablas temporales se eliminan automáticamente cuando se cierra la conexión. Todas las REFERENCIAS a claves externas no se aplican para las tablas temporales.
>

<a name="cs_1_connect"/>
### <a name="c-block-1-connect-by-using-adonet"></a>Bloque de C# 1: conectar mediante ADO.NET

- [Siguiente](#cs_2_createtables)


```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll 
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
   class Program
   {
      static void Main(string[] args)
      {
         try
         {
            var cb = new SqlConnectionStringBuilder();
            cb.DataSource = "your_server.database.windows.net";
            cb.UserID = "your_user";
            cb.Password = "your_password";
            cb.InitialCatalog = "your_database";

            using (var connection = new SqlConnection(cb.ConnectionString))
            {
               connection.Open();

               Submit_Tsql_NonQuery(connection, "2 - Create-Tables",
                  Build_2_Tsql_CreateTables());

               Submit_Tsql_NonQuery(connection, "3 - Inserts",
                  Build_3_Tsql_Inserts());

               Submit_Tsql_NonQuery(connection, "4 - Update-Join",
                  Build_4_Tsql_UpdateJoin(),
                  "@csharpParmDepartmentName", "Accounting");

               Submit_Tsql_NonQuery(connection, "5 - Delete-Join",
                  Build_5_Tsql_DeleteJoin(),
                  "@csharpParmDepartmentName", "Legal");

               Submit_6_Tsql_SelectEmployees(connection);
            }
         }
         catch (SqlException e)
         {
            Console.WriteLine(e.ToString());
         }
         Console.WriteLine("View the report output here, then press any key to end the program...");
         Console.ReadKey();
      }
```


<a name="cs_2_createtables"/>
### <a name="c-block-2-t-sql-to-create-tables"></a>Bloque de C# 2: T-SQL para crear tablas

- [Anterior](#cs_1_connect) &nbsp; / &nbsp; [Siguiente](#cs_3_insert)

```csharp
      static string Build_2_Tsql_CreateTables()
      {
         return @"
DROP TABLE IF EXISTS tabEmployee;
DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.


CREATE TABLE tabDepartment
(
   DepartmentCode  nchar(4)          not null
      PRIMARY KEY,
   DepartmentName  nvarchar(128)     not null
);

CREATE TABLE tabEmployee
(
   EmployeeGuid    uniqueIdentifier  not null  default NewId()
      PRIMARY KEY,
   EmployeeName    nvarchar(128)     not null,
   EmployeeLevel   int               not null,
   DepartmentCode  nchar(4)              null
      REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
);
";
      }
```

#### <a name="entity-relationship-diagram-erd"></a>Diagrama de relaciones de entidades (ERD)

Las instrucciones CREATE TABLE anteriores emplean la palabra clave **REFERENCES** para crear una relación de *clave externa* (FK) entre dos tablas.  Si usa tempdb, convierta en comentario la palabra clave `--REFERENCES` con un par de guiones iniciales.

A continuación, se ve un ERD que muestra la relación entre ambas tablas. Los valores de la columna *child* de #tabEmployee.DepartmentCode están limitados a los valores presentes en la columna *parent* de #tabDepartment.Department.

![ERD que muestra la clave externa](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)


<a name="cs_3_insert"/>
### <a name="c-block-3-t-sql-to-insert-data"></a>Bloque de C# 3: T-SQL para insertar datos

- [Anterior](#cs_2_createtables) &nbsp; / &nbsp; [Siguiente](#cs_4_updatejoin)


```csharp
      static string Build_3_Tsql_Inserts()
      {
         return @"
-- The company has these departments.
INSERT INTO tabDepartment
   (DepartmentCode, DepartmentName)
      VALUES
   ('acct', 'Accounting'),
   ('hres', 'Human Resources'),
   ('legl', 'Legal');

-- The company has these employees, each in one department.
INSERT INTO tabEmployee
   (EmployeeName, EmployeeLevel, DepartmentCode)
      VALUES
   ('Alison'  , 19, 'acct'),
   ('Barbara' , 17, 'hres'),
   ('Carol'   , 21, 'acct'),
   ('Deborah' , 24, 'legl'),
   ('Elle'    , 15, null);
";
      }
```


<a name="cs_4_updatejoin"/>
### <a name="c-block-4-t-sql-to-update-join"></a>Bloque de C# 4: T-SQL para la actualización-combinación

- [Anterior](#cs_3_insert) &nbsp; / &nbsp; [Siguiente](#cs_5_deletejoin)


```csharp
      static string Build_4_Tsql_UpdateJoin()
      {
         return @"
DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';


-- Promote everyone in one department (see @parm...).
UPDATE empl
   SET
      empl.EmployeeLevel += 1
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName1;
";
      }
```


<a name="cs_5_deletejoin"/>
### <a name="c-block-5-t-sql-to-delete-join"></a>Bloque de C# 5: T-SQL para la eliminación-combinación

- [Anterior](#cs_4_updatejoin) &nbsp; / &nbsp; [Siguiente](#cs_6_selectrows)


```csharp
      static string Build_5_Tsql_DeleteJoin()
      {
         return @"
DECLARE @DName2  nvarchar(128);
SET @DName2 = @csharpParmDepartmentName;  --'Legal';


-- Right size the Legal department.
DELETE empl
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName2

-- Disband the Legal department.
DELETE tabDepartment
   WHERE DepartmentName = @DName2;
";
      }
```



<a name="cs_6_selectrows"/>
### <a name="c-block-6-t-sql-to-select-rows"></a>Bloque de C# 6: T-SQL para seleccionar filas

- [Anterior](#cs_5_deletejoin) &nbsp; / &nbsp; [Siguiente](#cs_6b_datareader)


```csharp
      static string Build_6_Tsql_SelectEmployees()
      {
         return @"
-- Look at all the final Employees.
SELECT
      empl.EmployeeGuid,
      empl.EmployeeName,
      empl.EmployeeLevel,
      empl.DepartmentCode,
      dept.DepartmentName
   FROM
      tabEmployee   as empl
      LEFT OUTER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   ORDER BY
      EmployeeName;
";
      }
```


<a name="cs_6b_datareader"/>
### <a name="c-block-6b-executereader"></a>Bloque de C# 6b: ExecuteReader

- [Anterior](#cs_6_selectrows) &nbsp; / &nbsp; [Siguiente](#cs_7_executenonquery)

Este método se ha diseñado para ejecutar la instrucción T-SQL SELECT que se genera con el método **Build_6_Tsql_SelectEmployees**.


```csharp
      static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("Now, SelectEmployees (6)...");

         string tsql = Build_6_Tsql_SelectEmployees();

         using (var command = new SqlCommand(tsql, connection))
         {
            using (SqlDataReader reader = command.ExecuteReader())
            {
               while (reader.Read())
               {
                  Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                     reader.GetGuid(0),
                     reader.GetString(1),
                     reader.GetInt32(2),
                     (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                     (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
               }
            }
         }
      }
```


<a name="cs_7_executenonquery"/>
### <a name="c-block-7-executenonquery"></a>Bloque de C# 7: ExecuteNonQuery

- [Anterior](#cs_6b_datareader) &nbsp; / &nbsp; [Siguiente](#cs_8_output)

Se llama a este método para las operaciones que modifican el contenido de datos de tablas sin devolver ninguna fila de datos.


```csharp
      static void Submit_Tsql_NonQuery(
         SqlConnection connection,
         string tsqlPurpose,
         string tsqlSourceCode,
         string parameterName = null,
         string parameterValue = null
         )
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

         using (var command = new SqlCommand(tsqlSourceCode, connection))
         {
            if (parameterName != null)
            {
               command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                  parameterName,
                  parameterValue);
            }
            int rowsAffected = command.ExecuteNonQuery();
            Console.WriteLine(rowsAffected + " = rows affected.");
         }
      }
   } // EndOfClass
}
```


<a name="cs_8_output"/>
### <a name="c-block-8-actual-test-output-to-the-console"></a>Bloque de C# 8: salida de la prueba real en la consola

- [Anterior](#cs_7_executenonquery)

En esta sección se captura la salida que el programa envía a la consola. Los valores de GUID son lo único que varía entre series de pruebas.


```text
[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>> csharp_db_test.exe

=================================
Now, CreateTables (10)...

=================================
Now, Inserts (20)...

=================================
Now, UpdateJoin (30)...
2 rows affected, by UpdateJoin.

=================================
Now, DeleteJoin (40)...

=================================
Now, SelectEmployees (50)...
0199be49-a2ed-4e35-94b7-e936acf1cd75 , Alison , 20 , acct , Accounting
f0d3d147-64cf-4420-b9f9-76e6e0a32567 , Barbara , 17 , hres , Human Resources
cf4caede-e237-42d2-b61d-72114c7e3afa , Carol , 22 , acct , Accounting
cdde7727-bcfd-4f72-a665-87199c415f8b , Elle , 15 , NULL , NULL

[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>>
```
