<properties linkid="develop-java-sql-azure" urlDisplayName="SQL Database" pageTitle="How to use SQL Azure (Java) - Azure feature guide" metaKeywords="" description="Learn how to use the Azure SQL Database from Java code. " metaCanonical="" services="sql-database" documentationCenter="Java" title="How to Use Azure SQL Database in Java" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Utilización de Base de datos SQL de Azure en Java

Los siguientes pasos muestran cómo utilizar Base de datos SQL de Azure con Java. Se muestran ejemplos de línea de comandos para fines de simplicidad, pero serían apropiados pasos muy similares para las aplicaciones web, ya sea hospedadas de manera local, dentro de Azure o en otros entornos. En esta guía se describe la creación de un servidor y una base de datos desde el [Portal de administración de Azure][].

## Qué es una base de datos SQL de Azure

Una base de datos SQL de Azure proporciona un sistema de administración de bases de datos relacionales para Azure y se basa en la tecnología de SQL Server. Con una instancia de base de datos SQL se pueden aprovisionar e implementar soluciones de bases de datos relacionales en la nube con toda facilidad, así como beneficiarse de un centro de datos distribuido que ofrece disponibilidad, escalabilidad y seguridad de clase empresarial con las ventajas de contar con funciones de protección de datos y recuperación automática integradas.

## Tabla de contenido

-   [Conceptos][]
-   [Requisitos previos][]
-   [Creación de una base de datos SQL de Azure][]
-   [Determinación de la cadena de conexión de Base de datos SQL][]
-   [Acceso a un rango de direcciones IP][]
-   [Uso de Base de datos SQL de Azure en Java][]
-   [Comunicación con Base de datos SQL de Azure desde el código][]
-   [Creación de una tabla][]
-   [Creación de un índice en una tabla][]
-   [Inserción de filas][]
-   [Recuperación de filas][]
-   [Recuperación de filas con una cláusula WHERE][]
-   [Recuperación de un recuento de filas][]
-   [Actualización de filas][]
-   [Eliminación de filas][]
-   [Comprobación de la existencia de una tabla][]
-   [Eliminación de un índice][]
-   [Eliminación de una tabla][]
-   [Utilización de Base de datos SQL en Java dentro de una implementación de Azure][]
-   [Pasos siguientes][]

## <span id="concepts"></span></a>Conceptos

Puesto que Base de datos SQL de Azure se basa en las tecnologías de SQL Server, el acceso a Base de datos SQL desde Java es muy similar al acceso de SQL Server desde Java. Puede desarrollar una aplicación localmente (con SQL Server) y, a continuación, conectarse a Base de datos SQL cambiando solo la cadena de conexión. Puede utilizar un controlador JDBC para SQL Server para la aplicación. Sin embargo, existen algunas diferencias entre Base de datos SQL y SQL Server que podrían afectar a su aplicación. Para obtener más información, consulte [Instrucciones y limitaciones (Base de datos SQL de Azure)][].

Para obtener recursos adicionales para Base de datos SQL, consulte la sección [Pasos siguientes][].

## <span id="prerequisites"></span></a>Requisitos previos

Los siguientes son requisitos previos si tiene pensado usar Base de datos SQL con Java.

-   Un kit para desarrolladores de Java (JDK) v1.6 o superior.
-   Una suscripción de Azure, que se puede adquirir en <http://www.microsoft.com/windowsazure/offers/>.
-   Si está usando Eclipse, necesitará Eclipse IDE para los desarrolladores de Java EE, Indigo o posterior. Esto se puede descargar en <http://www.eclipse.org/downloads/>. También necesitará el complemento de Azure para Eclipse con Java (de Microsoft Open Technologies). Durante la instalación de este complemento, asegúrese de que se incluya Microsoft JDBC Driver 4.0 para SQL Server. Para obtener más información, consulte [Instalación del complemento de Azure para Eclipse con Java (de Microsoft Open Technologies)][].
-   Si no está usando Eclipse, necesitará Microsoft JDBC Driver 4.0 para SQL Server, que puede descargar desde <http://www.microsoft.com/es-es/download/details.aspx?id=11774>.

## <span id="create_db"></span></a>Creación de una base de datos SQL de Azure

Antes de usar Base de datos SQL de Azure en código Java, necesitará crear un servidor de base de datos SQL de Azure.

1.  Inicie sesión en el [Portal de administración de Azure][1].
2.  Haga clic en **New**.

    ![Creación de una base de datos SQL nueva][]

3.  Haga clic en **Base de datos SQL** y, a continuación, haga clic en **Custom create**.

    ![Creación de una base de datos SQL personalizada][]

4.  En el cuadro de diálogo **Database settings**, especifique el nombre de la base de datos. Para los propósitos de esta guía, utilice **gettingstarted** como nombre de la base de datos.
5.  En **Server**, seleccione **Nuevo servidor de bases de datos SQL**. Utilice los valores predeterminados para los demás campos.

    ![Configuración de la base de datos SQL][]

6.  Haga clic en la flecha siguiente.
7.  En el cuadro de diálogo **Server settings**, especifique un nombre de inicio de sesión de SQL Server. Para los propósitos de esta guía, se utilizó **MySQLAdmin**. Especifique y confirme una contraseña. Especifique una región y asegúrese de que la casilla **Allow Azure Services to access the server** esté activada.

    ![Configuración de SQL Server][]

8.  Haga clic en el botón de finalización.

## <span id="determine_connection_string"></span></a>Determinación de la cadena de conexión de Base de datos SQL

1.  Inicie sesión en el [Portal de administración de Azure][1].
2.  Haga clic en **Bases de datos SQL**.
3.  Haga clic en la base de datos que desea utilizar.
4.  Haga clic en **Mostrar cadenas de conexión**.
5.  Resalte el contenido de la cadena de conexión **JDBC**.

    ![Determinación de la cadena de conexión JDBC][]

6.  Haga clic con el botón secundario en el contenido resaltado de la cadena de conexión **JDBC** y haga clic en **Copy**.
7.  Ahora puede pegar este valor en el archivo de código para crear una cadena de conexión de la siguiente forma. Reemplace *your\_server* (en dos lugares) por el texto que copió en el paso anterior y reemplace *your\_password* por el valor de la contraseña que especificó cuando creó la cuenta de Base de datos SQL. (Reemplace también los valores asignados a **database** y **user** si no ha utilizado **gettingstarted** y **MySQLAdmin**, respectivamente).

    String connectionString =
     "jdbc:sqlserver://*your\_server*.database.windows.net:1433" + ";" +
     "database=gettingstarted" + ";" +
     ["user=MySQLAdmin@\*your\_server\*][]" + ";" +
     "password=*your\_password*" + ";" +
     "encrypt=true" + ";" +
     "hostNameInCertificate=\*.int.mscds.com" + ";" +
     "loginTimeout=30";

De hecho, usaremos esta cadena más adelante en esta guía, porque ahora ya conoce los pasos para determinar la cadena de conexión. Además, según sus necesidades de aplicación, no necesitará utilizar los valores **encrypt** y **hostNameInCertificate** y puede necesitar modificar el valor **loginTimeout**.

## <span id="specify_allowed_ips"></span></a>Acceso a un rango de direcciones IP

1.  Inicie sesión en el [Portal de administración][1].
2.  Haga clic en **Bases de datos SQL**.
3.  Haga clic en **Servers**.
4.  Haga clic en el servidor que desea utilizar.
5.  Haga clic en **Manage**.
6.  Haga clic en **Configure**.
7.  Bajo **Direcciones IP permitidas**, escriba el nombre de una nueva regla IP. Especifique el rango de comienzo y fin de las direcciones IP. Para su conveniencia, se muestra la dirección IP del cliente actual. El siguiente ejemplo permite una sola dirección IP del cliente (su dirección IP será diferente).

    ![Cuadro de diálogo de direcciones IP permitidas][]

8.  Haga clic en el botón de finalización. A las direcciones IP que especifica se les permitirá ahora el acceso a su servidor de base de datos.

## <span id="use_sql_azure_in_java"></span></a>Uso de Base de datos SQL de Azure en Java

1.  Cree un proyecto Java. Para los propósitos de este tutorial, llámelo **HelloSQLAzure**.
2.  Agregue un archivo de clase Java llamado **HelloSQLAzure.java** al proyecto.
3.  Agregue **Microsoft JDBC Driver para SQL Server** a su ruta de acceso de compilación.

Si está usando Eclipse:

    1. Within Eclipse's Project Explorer, right-click the **HelloSQLAzure** project and click **Properties**.
    2. In the left-hand pane of the **Properties** dialog, click **Java Build Path**.
    3. Click the **Libraries** tab, and then click **Add Library**.
    4. In the **Add Library** dialog, select **Microsoft JDBC Driver 4.0 for SQL Server**, click **Next**, and then click **Finish**.
    5. Click **OK** to close the **Properties** dialog.

    If you are not using Eclipse, add the Microsoft JDBC Driver 4.0 for SQL Server JAR to your class path. For related information, see [Using the JDBC Driver](http://msdn.microsoft.com/es-es/library/ms378526.aspx).

1.  Dentro de su código **HelloSQLAzure.java**, agregue instrucciones `import` como se muestra a continuación:

        import java.sql.*;
        import com.microsoft.sqlserver.jdbc.*;

2.  Especifique la cadena de conexión. El siguiente es un ejemplo. Como antes, reemplace *your\_server* (en dos lugares), *your\_user* y *your\_password* por los valores apropiados para su servidor de Base de datos SQL.

        String connectionString =
            "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
                "database=master" + ";" + 
                "user=your_user@your_server" + ";" +  
                "password=your_password";

Ahora está listo para agregar el código que se comunicará con el servidor de Base de datos SQL.

## <span id="communicate_from_code"></span></a>Comunicación con Base de datos SQL de Azure desde el código

El resto de este tema muestra ejemplos que hacen lo siguiente:

1.  Conectarse con el servidor de Base de datos SQL.
2.  Definir una instrucción SQL, por ejemplo, para crear o eliminar una tabla, insertar/seleccionar/eliminar filas, etc.
3.  Ejecutar la instrucción SQL, ya sea a través de una llamada a **executeUpdate** o **executeQuery**.
4.  Mostrar resultados de la consulta, si procede.

Las siguientes secciones deberían leerse (tomar sus ejemplos) en orden. El primer fragmento es una muestra completa; los demás dependen de parte del marco de la muestra completa, como las instrucciones **import**, declaraciones **class** y **main**, administración de errores y cierre de recursos.

## <span id="to_create_table"></span></a>Creación de una tabla

El código siguiente muestra cómo crear una tabla llamada **Person**.

    import java.sql.*;
    import com.microsoft.sqlserver.jdbc.*;

    public class HelloSQLAzure {

        public static void main(String[] args) 
        {

            // Connection string for your SQL Database server.
            // Change the values assigned to your_server, 
            // your_user@your_server,
            // and your_password.
            String connectionString = 
                "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
                    "database=gettingstarted" + ";" + 
                    "user=your_user@your_server" + ";" +  
                    "password=your_password";
            
            // The types for the following variables are
            // defined in the java.sql library.
            Connection connection = null;  // For making the connection
            Statement statement = null;    // For the SQL statement
            ResultSet resultSet = null;    // For the result set, if applicable
            
            try
            {
                // Ensure the SQL Server driver class is available.
                Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
            
                // Establish the connection.
                connection = DriverManager.getConnection(connectionString);
            
                // Define the SQL string.
                String sqlString = 
                    "CREATE TABLE Person (" + 
                        "[PersonID] [int] IDENTITY(1,1) NOT NULL," +
                        "[LastName] [nvarchar](50) NOT NULL," + 
                        "[FirstName] [nvarchar](50) NOT NULL)";
            
                // Use the connection to create the SQL statement.
                statement = connection.createStatement();
            
                // Execute the statement.
                statement.executeUpdate(sqlString);
            
                // Provide a message when processing is complete.
                System.out.println("Processing complete.");
            
            }
            // Exception handling
            catch (ClassNotFoundException cnfe)  
            {
                
                System.out.println("ClassNotFoundException " +
                                   cnfe.getMessage());
            }
            catch (Exception e)
            {
                System.out.println("Exception " + e.getMessage());
                e.printStackTrace();
            }
            finally
            {
                try
                {
                    // Close resources.
                    if (null != connection) connection.close();
                    if (null != statement) statement.close();
                    if (null != resultSet) resultSet.close();
                }
                catch (SQLException sqlException)
                {
                    // No additional action if close() statements fail.
                }
            }
            
        }

    }

## <span id="to_create_index"></span></a>Creación de un índice en una tabla

El código siguiente muestra cómo crear un índice llamado **index1** en la tabla **Person**, mediante la columna **PersonID**.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
            "database=gettingstarted" + ";" + 
            "user=your_user@your_server" + ";" +  
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

        // Define the SQL string.
        String sqlString = 
            "CREATE CLUSTERED INDEX index1 " + "ON Person (PersonID)";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();

        // Execute the statement.
        statement.executeUpdate(sqlString);

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }
    // Exception handling and resource closing not shown...

## <span id="to_insert_rows"></span></a>Inserción de filas

El código siguiente muestra cómo agregar filas a la tabla **Person**.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
            "database=gettingstarted" + ";" + 
            "user=your_user@your_server" + ";" +  
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

        // Define the SQL string.
        String sqlString = 
            "SET IDENTITY_INSERT Person ON " + 
                "INSERT INTO Person " + 
                "(PersonID, LastName, FirstName) " + 
                "VALUES(1, 'Abercrombie', 'Kim')," + 
                      "(2, 'Goeschl', 'Gerhard')," + 
                      "(3, 'Grachev', 'Nikolay')," + 
                      "(4, 'Yee', 'Tai')," + 
                      "(5, 'Wilson', 'Jim')";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();

        // Execute the statement.
        statement.executeUpdate(sqlString);

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }
    // Exception handling and resource closing not shown...

## <span id="to_retrieve_rows"></span></a>Recuperación de filas

El código siguiente muestra cómo recuperar las filas de la tabla **Person**.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
            "database=gettingstarted" + ";" + 
            "user=your_user@your_server" + ";" +  
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

        // Define the SQL string.
        String sqlString = "SELECT TOP 10 * FROM Person";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();

        // Execute the statement.
        resultSet = statement.executeQuery(sqlString);

        // Loop through the results
        while (resultSet.next())
        {
            // Print out the row data
            System.out.println(
                "Person with ID " + 
                resultSet.getString("PersonID") + 
                " has name " +
                resultSet.getString("FirstName") + " " +
                resultSet.getString("LastName"));
            }

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }
    // Exception handling and resource closing not shown...

El código anterior seleccionó las 10 primeras filas de la tabla **Person**. Si desea devolver todas las filas, modifique la instrucción de SQL a lo siguiente:

    String sqlString = "SELECT * FROM Person";

## <span id="to_retrieve_rows_using_where"></span></a>Recuperación de filas con una cláusula WHERE

Para recuperar filas mediante una cláusula, utilice el código como se muestra arriba, excepto cambiar la instrucción SQL para que incluya una cláusula. La siguiente instrucción SQL incluye una cláusula para las filas cuyo valor **FirstName** es igual a **Jim**.

    // Define the SQL string.
    String sqlString = "SELECT * FROM Person WHERE FirstName='Jim'";

Las cláusulas WHERE pueden utilizarse también al recuperar cuentas o actualizar o eliminar filas.

## <span id="to_retrieve_row_count"></span></a>Recuperación de un recuento de filas

El código siguiente muestra cómo recuperar un recuento de filas de la tabla **Person**.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
            "database=gettingstarted" + ";" + 
            "user=your_user@your_server" + ";" +  
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

    // Define the SQL string.
        String sqlString = "SELECT COUNT (PersonID) FROM Person";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();

        // Execute the statement.
        resultSet = statement.executeQuery(sqlString);

        // Print out the returned number of rows.
        while (resultSet.next())
        {
            System.out.println("There were " + 
                             resultSet.getInt(1) +
                             " rows returned.");
        }

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }
    // Exception handling and resource closing not shown...

## <span id="to_update_rows"></span></a>Actualización de filas

El código siguiente muestra cómo actualizar filas. En este ejemplo, el valor **LastName** se cambia a **Kim** para todas las filas donde el valor **FirstName** es **Jim**.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
            "database=gettingstarted" + ";" + 
            "user=your_user@your_server" + ";" +  
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

        // Define the SQL string.
        String sqlString = 
            "UPDATE Person " + "SET LastName = 'Kim' " + "WHERE FirstName='Jim'";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();
        
        // Execute the statement.
        statement.executeUpdate(sqlString);

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }// Exception handling and resource closing not shown...

## <span id="to_delete_rows"></span></a>Eliminación de filas

El código siguiente muestra cómo eliminar filas. En este ejemplo, cualquiera de las filas donde el valor **FirstName** sea **Jim** se elimina.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
            "database=gettingstarted" + ";" + 
            "user=your_user@your_server" + ";" +  
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

        // Define the SQL string.
        String sqlString = 
            "DELETE from Person " + 
                "WHERE FirstName='Jim'";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();

        // Execute the statement.
        statement.executeUpdate(sqlString);

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }
    // Exception handling and resource closing not shown...

## <span id="to_check_table_existence"></span></a>Comprobación de la existencia de una tabla

El código siguiente muestra cómo determinar si existe una tabla.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
            "database=gettingstarted" + ";" + 
            "user=your_user@your_server" + ";" +  
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

        // Define the SQL string.
        String sqlString = 
            "IF EXISTS (SELECT 1 " +
                "FROM sysobjects " + 
                "WHERE xtype='u' AND name='Person') " +
                "SELECT 'Person table exists.'" +
                "ELSE  " +
                "SELECT 'Person table does not exist.'";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();

        // Execute the statement.
        resultSet = statement.executeQuery(sqlString);

        // Display the result.
        while (resultSet.next())
        {
            System.out.println(resultSet.getString(1));
        }

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }
    // Exception handling and resource closing not shown...

## <span id="to_drop_index"></span></a>Eliminación de un índice

El código siguiente muestra cómo eliminar un índice llamado **index1** en la tabla **Person**.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +
            "database=gettingstarted" + ";" +
            "user=your_user@your_server" + ";" +
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

        // Define the SQL string.
        String sqlString = 
            "DROP INDEX index1 " + 
                "ON Person";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();

        // Execute the statement.
        statement.executeUpdate(sqlString);

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }
    // Exception handling and resource closing not shown...

## <span id="to_drop_table"></span></a>Eliminación de una tabla

El código siguiente muestra cómo eliminar una tabla llamada **Person**.

    // Connection string for your SQL Database server.
    // Change the values assigned to your_server, 
    // your_user@your_server,
    // and your_password.
    String connectionString = 
        "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
            "database=gettingstarted" + ";" + 
            "user=your_user@your_server" + ";" +  
            "password=your_password";

    // The types for the following variables are
    // defined in the java.sql library.
    Connection connection = null;  // For making the connection
    Statement statement = null;    // For the SQL statement
    ResultSet resultSet = null;    // For the result set, if applicable

    try
    {
        // Ensure the SQL Server driver class is available.
        Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");

        // Establish the connection.
        connection = DriverManager.getConnection(connectionString);

        // Define the SQL string.
        String sqlString = "DROP TABLE Person";

        // Use the connection to create the SQL statement.
        statement = connection.createStatement();

        // Execute the statement.
        statement.executeUpdate(sqlString);

        // Provide a message when processing is complete.
        System.out.println("Processing complete.");

    }
    // Exception handling and resource closing not shown...

## <span id="using_in_azure"></span></a>Utilización de Base de datos SQL en Java dentro de una implementación de Azure

Para utilizar Base de datos SQL en Java dentro de una implementación de Azure, además de tener Microsoft JDBC Driver 4.0 para SQL Server como biblioteca en su ruta de acceso de clase, como se muestra arriba, necesitará empaquetarlo con su implementación.

**Empaquetado de Microsoft JDBC Driver 4.0 SQL Server si utiliza Eclipse**

1.  Dentro del Explorador de proyectos de Eclipse, haga clic con el botón secundario en el proyecto y haga clic en **Properties**.
2.  En el panel izquierdo del cuadro de diálogo **Properties**, haga clic en **Deployment Assembly** y, a continuación, en **Add**.
3.  En el cuadro de diálogo **New Assembly Directive**, haga clic en **Java Build Path Entries** y, a continuación, en **Next**.
4.  Seleccione **Microsoft JDBC Driver 4.0 SQL Server** y, a continuación, haga clic en **Finish**.
5.  Haga clic en **OK** para cerrar el cuadro de diálogo **Properties**.
6.  Exporte el archivo WAR del proyecto a la carpeta approot y vuelva a compilar el proyecto de Azure, según los pasos documentados en [Creación de una aplicación Hello World con el complemento de Azure para Eclipse con Java (de Microsoft Open Technologies)][]. Ese tema también describe cómo ejecutar la aplicación en el emulador de proceso y en Azure.

**Empaquetado de Microsoft JDBC Driver 4.0 SQL Server si no utiliza Eclipse**

-   Asegúrese de que la biblioteca de Microsoft JDBC Driver 4.0 SQL Server esté incluida dentro del rol de Azure como su aplicación Java y agregada a la ruta de acceso de clase de su aplicación.

## <span id="nextsteps"></span></a>Pasos siguientes

Para obtener más información acerca de Microsoft JDBC Driver para SQL Server, consulte [Información general de JDBC Driver][]. Para obtener más información acerca de Base de datos SQL, consulte [Información general de Base de datos SQL][].

  [Portal de administración de Azure]: https://windows.azure.com
  [Conceptos]: #concepts
  [Requisitos previos]: #prerequisites
  [Creación de una base de datos SQL de Azure]: #create_db
  [Determinación de la cadena de conexión de Base de datos SQL]: #determine_connection_string
  [Acceso a un rango de direcciones IP]: #specify_allowed_ips
  [Uso de Base de datos SQL de Azure en Java]: #use_sql_azure_in_java
  [Comunicación con Base de datos SQL de Azure desde el código]: #communicate_from_code
  [Creación de una tabla]: #to_create_table
  [Creación de un índice en una tabla]: #to_create_index
  [Inserción de filas]: #to_insert_rows
  [Recuperación de filas]: #to_retrieve_rows
  [Recuperación de filas con una cláusula WHERE]: #to_retrieve_rows_using_where
  [Recuperación de un recuento de filas]: #to_retrieve_row_count
  [Actualización de filas]: #to_update_rows
  [Eliminación de filas]: #to_delete_rows
  [Comprobación de la existencia de una tabla]: #to_check_table_existence
  [Eliminación de un índice]: #to_drop_index
  [Eliminación de una tabla]: #to_drop_table
  [Utilización de Base de datos SQL en Java dentro de una implementación de Azure]: #using_in_azure
  [Pasos siguientes]: #nextsteps
  [Instrucciones y limitaciones (Base de datos SQL de Azure)]: http://msdn.microsoft.com/es-es/library/windowsazure/ff394102.aspx
  [Instalación del complemento de Azure para Eclipse con Java (de Microsoft Open Technologies)]: http://msdn.microsoft.com/es-es/library/windowsazure/hh690946.aspx
  [1]: https://manage.windowsazure.com
  [Creación de una base de datos SQL nueva]: ./media/sql-data-java-how-to-use-sql-database/WA_New.png
  [Creación de una base de datos SQL personalizada]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_DB_Create.png
  [Configuración de la base de datos SQL]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_1.png
  [Configuración de SQL Server]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_2.png
  [Determinación de la cadena de conexión JDBC]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_JDBC_ConnectionString.png
  ["user=MySQLAdmin@\*your\_server\*]: mailto:"user=MySQLAdmin@*your_server*
  [Cuadro de diálogo de direcciones IP permitidas]: ./media/sql-data-java-how-to-use-sql-database/WA_Allowed_IPs.png
  [Creación de una aplicación Hello World con el complemento de Azure para Eclipse con Java (de Microsoft Open Technologies)]: http://msdn.microsoft.com/es-es/library/windowsazure/hh690944.aspx
  [Información general de JDBC Driver]: http://msdn.microsoft.com/es-es/library/ms378749.aspx
  [Información general de Base de datos SQL]: http://msdn.microsoft.com/es-es/library/windowsazure/ee336241.aspx
