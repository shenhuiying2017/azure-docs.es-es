<properties 
	pageTitle="Uso de SQL Azure (Java) - Guía de características de Azure" 
	description="Obtenga información acerca de cómo usar la base de datos SQL de Azure desde el código de Java." 
	services="sql-database" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="jeffreyg" 
	editor="jimbe"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="robmcm"/>

# Utilización de Base de datos SQL de Azure en Java

Los siguientes pasos muestran cómo utilizar Base de datos SQL de Azure con Java. Se muestran ejemplos de línea de comandos para fines de simplicidad, pero serían apropiados pasos muy similares para las aplicaciones web, ya sea hospedadas de manera local, dentro de Azure o en otros entornos. En esta guía se describe la creación de un servidor y una base de datos desde el [Portal de administración de Azure](https://windows.azure.com).

## Qué es una base de datos SQL de Azure

Base de datos SQL de Azure ofrece un sistema de administración de bases de datos relacionales para Azure, y se basa en la tecnología de SQL Server. Con una instancia de base de datos SQL se pueden aprovisionar e implementar soluciones de bases de datos relacionales en la nube con toda facilidad, así como beneficiarse de un centro de datos distribuido que ofrece disponibilidad, escalabilidad y seguridad de clase empresarial con las ventajas de contar con funciones de protección de datos y recuperación automática integradas.



## Conceptos
Puesto que Base de datos SQL de Azure se basa en las tecnologías de SQL Server, el acceso a Base de datos SQL desde Java es muy similar al acceso de SQL Server desde Java. Puede desarrollar una aplicación localmente (con SQL Server) y, a continuación, conectarse a Base de datos SQL cambiando solo la cadena de conexión. Puede utilizar un controlador JDBC para SQL Server para la aplicación. Sin embargo, existen algunas diferencias entre Base de datos SQL y SQL Server que podrían afectar a su aplicación. Para obtener más información, consulte [Instrucciones y limitaciones de Base de datos SQL de Azure](http://msdn.microsoft.com/library/windowsazure/ff394102.aspx).

Para obtener recursos adicionales para Base de datos SQL, consulte la sección [Pasos siguientes][].

## Requisitos previos

Los siguientes son requisitos previos si tiene pensado usar Base de datos SQL con Java.

* Un kit para desarrolladores de Java (JDK) v1.6 o superior.
* Una suscripción de Azure, que se puede adquirir en <http://www.microsoft.com/windowsazure/offers/>.
* Si está usando Eclipse, necesitará Eclipse IDE para los desarrolladores de Java EE, Indigo o posterior. Esto se puede descargar en <http://www.eclipse.org/downloads/>. También necesitará el complemento de Azure para Eclipse con Java (de Microsoft Open Technologies). Durante la instalación de este complemento, asegúrese de que se incluya Microsoft JDBC Driver 4.0 para SQL Server. Para obtener más información, consulte [Instalación del complemento de Azure para Eclipse con Java (de Microsoft Open Technologies)](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
* Si no está usando Eclipse, necesitará Microsoft JDBC Driver 4.0 para SQL Server, que puede descargar desde <http://www.microsoft.com/download/details.aspx?id=11774>.

## Creación de una base de datos SQL de Azure

Antes de usar Base de datos SQL de Azure en código Java, necesitará crear un servidor de base de datos SQL de Azure.

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
2. Haga clic en **Nuevo**.

    ![Creación de una base de datos SQL nueva][create_new]

3. Haga clic en **Base de datos SQL** y, a continuación, en **Creación personalizada**.

    ![Creación de una base de datos SQL personalizada][create_new_sql_db]

4. En el cuadro de diálogo **Configuración de la base de datos**, especifique el nombre de la base de datos. Para los propósitos de esta guía, utilice **gettingstarted** como nombre de la base de datos.
5. En **Servidor**, seleccione **Nuevo servidor de bases de datos SQL**. Utilice los valores predeterminados para los demás campos.

    ![Configuración de la base de datos SQL][create_database_settings]

6. Haga clic en la flecha siguiente.
7. En el cuadro de diálogo **Configuración del servidor**, especifique un nombre de inicio de sesión de SQL Server. Para los propósitos de esta guía, se utilizó **MySQLAdmin**. Especifique y confirme una contraseña. Especifique una región y asegúrese de que la casilla **Permitir que los servicios de Azure accedan al servidor** esté activada.

    ![Configuración de SQL Server][create_server_settings]

8. Haga clic en el botón de finalización.

## Determinación de la cadena de conexión de Base de datos SQL

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
2. Haga clic en **Bases de datos SQL**.
3. Haga clic en la base de datos que desea utilizar.
4. Haga clic en **Mostrar cadenas de conexión**.
5. Resalte el contenido de la cadena de conexión **JDBC**.

    ![Determinación de la cadena de conexión JDBC][get_jdbc_connection_string]

6. Haga clic con el botón derecho en el contenido resaltado de la cadena de conexión **JDBC** y haga clic en **Copiar**.
7. Ahora puede pegar este valor en el archivo de código para crear una cadena de conexión de la siguiente forma. Reemplace *your\_server* (en dos lugares) por el texto que copió en el paso anterior y reemplace *your\_password* por el valor de la contraseña que especificó cuando creó la cuenta de Base de datos SQL. (Reemplace también los valores asignados a **database=** y **user=** si no utilizó **gettingstarted** y **MySQLAdmin**, respectivamente). 

    String connectionString = "jdbc:sqlserver://*your_server*.database.windows.net:1433" + ";" + "database=gettingstarted" + ";" + "user=MySQLAdmin@*your\_server*" + ";" + "password=*your\_password*" + ";" + "encrypt=true" + ";" + "hostNameInCertificate=*.int.mscds.com" + ";" + "loginTimeout=30";

De hecho, usaremos esta cadena más adelante en esta guía, porque ahora ya conoce los pasos para determinar la cadena de conexión. Además, según sus necesidades de aplicación, puede que no necesite utilizar los valores **encrypt** y **hostNameInCertificate**. Además, puede que necesite modificar el valor **loginTimeout**.

## Acceso a un rango de direcciones IP

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).
2. Haga clic en **Bases de datos SQL**.
3. Haga clic en **Servidores**.
4. Haga clic en el servidor que desea utilizar.
5. Haga clic en **Administrar**.
6. Haga clic en **Configurar**.
7. Bajo **Direcciones IP permitidas**, escriba el nombre de una nueva regla IP. Especifique el rango de comienzo y fin de las direcciones IP. Para su conveniencia, se muestra la dirección IP del cliente actual. El siguiente ejemplo permite una sola dirección IP del cliente (su dirección IP será diferente).

    ![Cuadro de diálogo de direcciones IP permitidas][allowed_ips_dialog]

8. Haga clic en el botón de finalización. A las direcciones IP que especifica se les permitirá ahora el acceso a su servidor de base de datos.

## Uso de Base de datos SQL de Azure en Java

1. Cree un proyecto Java. Para los propósitos de este tutorial, llámelo **HelloSQLAzure**.
2. Agregue un archivo de clase Java llamado **HelloSQLAzure.java** al proyecto.
3. Agregue **Microsoft JDBC Driver para SQL Server** a su ruta de acceso de compilación.

   Si está usando Eclipse:

    1. Within Eclipse's Project Explorer, right-click the **HelloSQLAzure** project and click **Properties**.
    2. In the left-hand pane of the **Properties** dialog, click **Java Build Path**.
    3. Click the **Libraries** tab, and then click **Add Library**.
    4. In the **Add Library** dialog, select **Microsoft JDBC Driver 4.0 for SQL Server**, click **Next**, and then click **Finish**.
    5. Click **OK** to close the **Properties** dialog.

    If you are not using Eclipse, add the Microsoft JDBC Driver 4.0 for SQL Server JAR to your class path. For related information, see [Using the JDBC Driver](http://msdn.microsoft.com/library/ms378526.aspx).

4. Dentro de su código **HelloSQLAzure.java**, agregue instrucciones `import` de la siguiente forma:

        import java.sql.*;
        import com.microsoft.sqlserver.jdbc.*;

5. Especifique la cadena de conexión. El siguiente es un ejemplo. Como antes, reemplace *your\_server* (en dos lugares), *your\_user* y *your\_password* por los valores apropiados para su servidor de Base de datos SQL.

        String connectionString =
        	"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
        		"database=master" + ";" + 
        		"user=your_user@your_server" + ";" +  
        		"password=your_password";

Ahora está listo para agregar el código que se comunicará con el servidor de Base de datos SQL.

## Comunicación con Base de datos SQL de Azure desde el código

El resto de este tema muestra ejemplos que hacen lo siguiente:

1. Conectarse con el servidor de Base de datos SQL.
2. Definir una instrucción SQL, por ejemplo, para crear o eliminar una tabla, insertar/seleccionar/eliminar filas, etc.
3. Ejecutar la instrucción SQL, ya sea a través de una llamada a **executeUpdate** o **executeQuery**.
4. Mostrar resultados de la consulta, si procede.

Las siguientes secciones deberían leerse (muestrearse) por orden. El primer fragmento es un ejemplo completo; los demás dependen en parte del marco del ejemplo completo, como las instrucciones **import**, las declaraciones **class** y **main**, el control de errores y el cierre de recursos.

## Creación de una tabla

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
	

## Creación de un índice en una tabla

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



## Inserción de filas

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

 
## Recuperación de filas

El código siguiente muestra cómo recuperar filas de la tabla **Person**.

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

 
## Recuperación de filas con una cláusula WHERE

Para recuperar filas mediante una cláusula, utilice el código como se muestra arriba, sin cambiar la instrucción SQL para que incluya una cláusula. La siguiente instrucción SQL incluye una cláusula para las filas cuyo valor **FirstName** es igual a **Jim**.

	// Define the SQL string.
	String sqlString = "SELECT * FROM Person WHERE FirstName='Jim'";
	
Las cláusulas WHERE pueden utilizarse también al recuperar cuentas o actualizar o eliminar filas.

## Recuperación de un recuento de filas

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

## Actualización de filas

El código siguiente muestra cómo actualizar filas. En este ejemplo, el valor **LastName** cambia a **Kim** en todas las filas donde el valor **FirstName** es **Jim**.

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

 

## Eliminación de filas

El código siguiente muestra cómo eliminar filas. En este ejemplo, se eliminan todas las filas donde el valor **FirstName** es **Jim**.

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
	
 
## Comprobación de la existencia de una tabla

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

## Eliminación de un índice

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

 
## Eliminación de una tabla

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

## Utilización de Base de datos SQL en Java dentro de una implementación de Azure

Para utilizar Base de datos SQL en Java dentro de una implementación de Azure, además de tener Microsoft JDBC Driver 4.0 para SQL Server como biblioteca en su ruta de acceso de clase, como se muestra arriba, necesitará empaquetarlo con su implementación.


**Empaquetado de Microsoft JDBC Driver 4.0 SQL Server si utiliza Eclipse**

1. Dentro del Explorador de proyectos de Eclipse, haga clic con el botón derecho en el proyecto y haga clic en **Propiedades**.
2. En el panel izquierdo del cuadro de diálogo **Propiedades**, haga clic en **Ensamblaje de implementación** y, a continuación, en **Agregar**.
3. En el cuadro de diálogo **Nueva directiva de ensamblaje**, haga clic en **Entradas de ruta de acceso de compilación de Java** y haga clic en **Siguiente**.
4. Seleccione **Microsoft JDBC Driver 4.0 SQL Server** y, a continuación, haga clic en **Finalizar**.
5. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Propiedades**.
6. Exporte el archivo WAR del proyecto a la carpeta approot y vuelva a compilar el proyecto de Azure, según los pasos documentados en [Creación de una aplicación Hello World con el complemento de Azure para Eclipse con Java (de Microsoft Open Technologies)](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx). Ese tema también describe cómo ejecutar la aplicación en el emulador de proceso y en Azure.

**Empaquetado de Microsoft JDBC Driver 4.0 SQL Server si no utiliza Eclipse**

* Asegúrese de que la biblioteca de Microsoft JDBC Driver 4.0 SQL Server esté incluida dentro del rol de Azure como su aplicación Java y agregada a la ruta de acceso de clase de su aplicación.

## Pasos siguientes

Para obtener más información acerca de Microsoft JDBC Driver para SQL Server, consulte [Información general de JDBC Driver](http://msdn.microsoft.com/library/ms378749.aspx). Para obtener más información acerca de Base de datos SQL, consulte [Información general de Base de datos SQL](http://msdn.microsoft.com/library/windowsazure/ee336241.aspx).

[Concepts]: #concepts
[Prerequisites]: #prerequisites
[Creating an Azure SQL Database]: #create_db
[Determining the SQL Database connection string]: #determine_connection_string
[To allow access to a range of IP addresses]: #specify_allowed_ips
[To use Azure SQL Database in Java]: #use_sql_azure_in_java
[Communicating with Azure SQL Database from your code]: #communicate_from_code
[To create a table]: #to_create_table
[To create an index on a table]: #to_create_index
[To insert rows]: #to_insert_rows
[To retrieve rows]: #to_retrieve_rows
[To retrieve rows using a WHERE clause]: #to_retrieve_rows_using_where
[To retrieve a count of rows]: #to_retrieve_row_count
[To update rows]: #to_update_rows
[To delete rows]: #to_delete_rows
[To check whether a table exists]: #to_check_table_existence
[To drop an index]: #to_drop_index
[To drop a table]: #to_drop_table
[Using SQL Database in Java within an Azure Deployment]: #using_in_azure
[Pasos siguientes]: #nextsteps
[create_new]: ./media/sql-data-java-how-to-use-sql-database/WA_New.png
[create_new_sql_db]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_DB_Create.png
[create_database_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_1.png
[create_server_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_2.png
[get_jdbc_connection_string]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_JDBC_ConnectionString.png
[allowed_ips_dialog]: ./media/sql-data-java-how-to-use-sql-database/WA_Allowed_IPs.png
 

<!---HONumber=August15_HO6-->