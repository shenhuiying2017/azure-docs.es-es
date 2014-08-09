<properties linkid="dev-net-how-to-sql-azure" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (.NET) - Azure feature guide" metaKeywords="Get started SQL Azure, Getting started SQL Azure, SQL Azure database connection, SQL Azure ADO.NET, SQL Azure ODBC, SQL Azure EntityClient" description="Get started with SQL Database. Learn how to create a SQL Database instance and connect to it using ADO.NET, ODBC, and EntityClient Provider." metaCanonical="" services="sql-database" documentationCenter=".NET" title="How to use Azure SQL Database in .NET applications" authors="" solutions="" manager="" editor="" />

Uso de Base de datos SQL de Azure en aplicaciones .NET
======================================================

En esta guía se muestra cómo crear una instancia de base de datos y un servidor lógico en Base de datos SQL de Azure y conectarse a la base de datos con las siguientes tecnologías del proveedor de datos .NET Framework: ADO.NET, ODBC y proveedor de EntityClient.

¿Qué es Base de datos SQL?
--------------------------

Una Base de datos SQL proporciona un sistema de administración de bases de datos relacionales para Azure y se basa en la tecnología de SQL Server. Con una instancia de base de datos SQL se pueden aprovisionar e implementar soluciones de bases de datos relacionales en la nube con toda facilidad, así como beneficiarse de un centro de datos distribuido que ofrece disponibilidad, escalabilidad y seguridad de clase empresarial con las ventajas de contar con funciones de protección de datos y recuperación automática integradas.

Tabla de contenido
------------------

-   [Inicio de sesión en Azure](#PreReq1)
-   [Creación y configuración de Base de datos SQL](#PreReq2)
-   [Conectarse a Base de datos SQL](#connect-db)
-   [Conexión mediante ADO.NET](#using-sql-server)
-   [Conexión mediante ODBC](#using-ODBC)
-   [Conexión mediante el proveedor de EntityClient](#using-entity)
-   [Pasos siguientes](#next-steps)

Inicio de sesión en Azure
-------------------------

Base de datos SQL proporciona servicios de administración, acceso y almacenamiento de datos relacionales en Azure. Para usarla, necesitará una suscripción a Azure.

1.  Abra un explorador web y diríjase a <http://www.windowsazure.com>. Para comenzar con una cuenta gratuita, haga clic en Evaluación gratuita en la esquina superior derecha y siga los pasos.

2.  Se ha creado su cuenta. Ya está listo para comenzar.

Creación y configuración de Base de datos SQL
---------------------------------------------

A continuación, creará y configurará una base de datos y un servidor. En el Portal de administración de Azure, los flujos de trabajo revisados le permiten crear primero las bases de datos y, a continuación, realizar un seguimiento del aprovisionamiento del servidor.

### Creación de una instancia de base de datos y servidor lógico

1.  Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com).

2.  Haga clic en **+NEW** en la parte inferior de la página.

3.  Haga clic en **Servicios de datos**.

4.  Haga clic en **Base de datos SQL**.

5.  Haga clic en **Creación personalizada**.

6.  En Name, especifique un nombre para la base de datos.

7.  Seleccione una edición, un tamaño máximo y una intercalación. Para los propósitos de esta guía, puede usar los valores predeterminados.

    Base de datos SQL proporciona dos ediciones de base de datos. La edición Web tiene un tamaño de 5 GB. La edición Business tiene un tamaño de 50 GB.

    Se especifica MAXSIZE cuando se crea la base de datos por primera vez y, a continuación, puede cambiarse con ALTER DATABASE. MAXSIZE ofrece la posibilidad de limitar el tamaño de la base de datos.

    Para cada base de datos SQL creada con Azure, existen realmente tres réplicas de esa base de datos. Esto se realiza para garantizar una disponibilidad alta. La conmutación por error es transparente y forma parte del servicio. El [contrato de nivel de servicio]({localLink:1132} "SLA") ofrece un 99,9 % de tiempo activo para la Base de datos SQL.

8.  En Server, seleccione **Nuevo servidor de bases de datos SQL**.

9.  Haga clic en la flecha para ir a la siguiente página.

10. En Server Settings, especifique un nombre de inicio de sesión de autenticación de SQL Server.

    Base de datos SQL usa la autenticación de SQL a través de una conexión cifrada. Se creará un nuevo inicio de sesión de autenticación de SQL Server asignado al rol del servidor fijo sysadmin con el nombre que proporcione.

    El inicio de sesión no puede ser una dirección de correo electrónico, una cuenta de usuario de Windows o un Windows Live ID. Las reclamaciones y la autenticación de Windows no son compatibles con Base de datos SQL.

11. Proporcione una contraseña segura con más de ocho caracteres que contenga una combinación de valores en mayúsculas y minúsculas, y un número o símbolo.

12. Seleccione una región. La región determina la ubicación geográfica del servidor. Las regiones no pueden cambiarse fácilmente, por lo que debe seleccionar la apropiada para el servidor. Seleccione la ubicación que le sea más próxima. Si mantiene la base de datos y la aplicación de Azure en la misma región, evita que se produzcan costes relacionados con el ancho de banda y la latencia de datos.

13. Asegúrese de mantener la opción **Allow Azure Services to access the server** seleccionada de manera que pueda conectarse a esta base de datos con el Portal de administración para Base de datos SQL, servicios de almacenamiento y otros servicios en Azure.

14. Haga clic en la marca de verificación en la parte inferior de la página cuando haya finalizado.

Tenga en cuenta que no especificó un nombre de servidor. Base de datos SQL genera automáticamente el nombre del servidor para garantizar que no existen entradas DNS duplicadas. El nombre del servidor es una cadena alfanumérica de 10 caracteres. No puede cambiar el nombre del servidor de Base de datos SQL.

Una vez que se cree la base de datos, haga clic en ella para abrir el panel. El panel proporciona cadenas de conexión que puede copiar y usar en el código de aplicación. También muestra la dirección URL de administración que tendrá que especificar si se conecta a una base de datos desde Management Studio u otra herramienta administrativa.

![imagen](./media/sql-database-dotnet-how-to-use/SQLDbDashboard.PNG)

En el próximo paso, configurará el firewall de manera que se permita el acceso a las conexiones de las aplicaciones que se ejecuten en la red.

### Configuración del firewall para el servidor lógico

1.  Haga clic en **Bases de datos SQL**, haga clic en **Servidores** en la parte superior de la página y, a continuación, haga clic en el servidor que acaba de crear.

    ![Imagen2](./media/sql-database-dotnet-how-to-use/SQLDBFirewall.PNG)

2.  Haga clic en **Configure**.

3.  Copie la dirección IP del cliente actual. Si está conectado desde una red, esta es la dirección IP que está escuchando el servidor proxy o el enrutador. Base de datos SQL detecta la dirección IP que usa la conexión actual para que pueda crear una regla de firewall para aceptar solicitudes de conexión desde ese dispositivo.

4.  Pegue la dirección IP en Dirección IP inicial y Dirección IP final para establecer las direcciones de intervalo que permiten el acceso al servidor. Después, si se producen errores de conexión que indican que el intervalo es demasiado estrecho, puede editar la regla para ampliarlo.

    Si los equipos cliente usan direcciones IP asignadas dinámicamente, debe especificar un intervalo que sea lo suficientemente amplio para que incluya direcciones IP asignadas a equipos en la red. Comience por un intervalo reducido y, a continuación, expándalo solo si lo necesita.

5.  Especifique un nombre para la regla de firewall, como el nombre del equipo o la compañía.

6.  Haga clic en la marca de verificación junto a la regla para guardarla.

    ![Imagen3](./media/sql-database-dotnet-how-to-use/SQLDBIPRange.PNG)

7.  Haga clic en **Save** en la parte inferior de la página para completar el paso. Si no consigue ver **Save**, actualice la página del explorador.

Ahora dispone de una instancia de base de datos, una regla de firewall que permite conexiones entrantes desde la dirección IP, y un inicio de sesión de administrador. Ahora está listo para conectar se una base de datos mediante programación.

Conectarse a Base de datos SQL
------------------------------

Esta sección le muestra cómo conectarse a la instancia de Base de datos SQL con diferentes proveedores de datos .NET Framework.

Si selecciona usar Visual Studio y su configuración no incluye una aplicación web de Azure como front-end, no existen herramientas adicionales o SDK necesarios que deban instalarse en el equipo de desarrollo. Puede comenzar por desarrollar la aplicación.

Puede usar las mismas herramientas del diseñador en Visual Studio para trabajar con Base de datos SQL que con SQL Server. El Explorador de servidores le permite ver (pero no editar) objetos de base de datos. El diseñador de modelos de datos de entidades de Visual Studio es totalmente funcional y puede usarlo para crear modelos relacionados con Base de datos SQL para que funcionen con Entity Framework.

### Uso del proveedor de datos .NET Framework para SQL Server

El espacio de nombres **System.Data.SqlClient** es el proveedor de datos .NET Framework para SQL Server.

La cadena de conexión estándar tendrá la siguiente apariencia:

    Server=tcp:.database.windows.net;
    Database=;
    User ID=@;
    Password=;
    Trusted_Connection=False;
    Encrypt=True;

Puede usar la clase **SQLConnectionStringBuilder** para generar una cadena de conexión como se muestra en el siguiente ejemplo de código:

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder();
    csBuilder.DataSource = xxxxxxxxxx.database.windows.net;
    csBuilder.InitialCatalog = testDB;
    csBuilder.Encrypt = true;
    csBuilder.TrustServerCertificate = false;
    csBuilder.UserID = MyAdmin;
    csBuilder.Password = pass@word1;

Si los elementos de una cadena de conexión se conocen de antemano, pueden almacenarse en un archivo de configuración y recuperarse en el tiempo de ejecución para construir una cadena de conexión. Aquí tiene una cadena de conexión de ejemplo en un archivo de configuración:

    <connectionStrings>
      <add name="ConnectionString" 
           connectionString ="Server=tcp:xxxxxxxxxx.database.windows.net;Database=testDB;User ID=MyAdmin@xxxxxxxxxx;Password=pass@word1;Trusted_Connection=False;Encrypt=True;" />
    </connectionStrings>

Para recuperar la cadena de conexión en un archivo de configuración, use la clase **ConfigurationManager**:

    SqlConnectionStringBuilder csBuilder;
    csBuilder = new SqlConnectionStringBuilder(ConfigurationManager.ConnectionStrings["ConnectionString"].ConnectionString);
    Una vez que haya generado la cadena de conexión, puede usar la clase SQLConnection para conectarse al servidor de Base de datos SQL:
    SqlConnection conn = new SqlConnection(csBuilder.ToString());
    conn.Open();

### Uso del proveedor de datos .NET Framework para ODBC

El espacio de nombres **System.Data.Odbc** es el proveedor de datos .NET Framework para ODBC. A continuación se muestra una cadena de conexión para ODBC de ejemplo:

    Driver={SQL Server Native Client 10.0};
    Server=tcp:.database.windows.net;
    Database=;
    Uid=@;
    Pwd=;
    Encrypt=yes;

La clase **OdbcConnection** representa una conexión abierta para un origen de datos. A continuación, se muestra un ejemplo de código de cómo abrir una conexión:

    string cs = "Driver={SQL Server Native Client 10.0};" +
                "Server=tcp:xxxxxxxxxx.database.windows.net;" +
                "Database=testDB;"+
                "Uid=MyAdmin@xxxxxxxxxx;" +
                "Pwd=pass@word1;"+
                "Encrypt=yes;";

    OdbcConnection conn = new OdbcConnection(cs);
    conn.Open();

Si desea generar la cadena de conexión en el tiempo de ejecución, puede usar la clase **OdbcConnectionStringBuilder**.

### Uso del proveedor de EntityClient

El espacio de nombres **System.Data.EntityClient** es el proveedor de datos .NET Framework para Entity Framework.

Entity Framework permite a los desarrolladores crear aplicaciones de acceso mediante la programación de un modelo de aplicación conceptual en lugar de la programación directamente de un esquema de almacenamiento relacional. Entity Framework se basa en los proveedores de datos ADO.NET específicos de almacenamiento que ofrecen un objeto **EntityConnection** a una base de datos relacional y a un proveedor de datos subyacente.

Para construir un objeto **EntityConnection**, tiene que hacer referencia a un conjunto de metadatos que contiene la asignación y los modelos necesarios y también una cadena de conexión y un nombre de proveedor de datos específico del almacenamiento. Una vez que **EntityConnection** está en su lugar, puede obtenerse acceso a las entidades a través de las clases generadas a partir del modelo conceptual.

A continuación se ofrece un ejemplo de una cadena de conexión:

    metadata=res://*/SchoolModel.csdl|res://*/SchoolModel.ssdl|res://*/SchoolModel.msl;provider=System.Data.SqlClient;provider connection string="Data Source=xxxxxxxxxx.database.windows.net;Initial Catalog=School;Persist Security Info=True;User ID=MyAdmin;Password=***********"

Para obtener más información, consulte [Proveedor de EntityClient para Entity Framework](http://msdn.microsoft.com/es-es/library/bb738561.aspx).

Pasos siguientes
----------------

Ahora que conoce los aspectos básicos de la conexión a Base de datos SQL, consulte los siguientes recursos para obtener más información sobre Base de datos SQL.

-   [Desarrollo de Base de datos SQL de Azure: Temas de procedimientos](http://msdn.microsoft.com/es-es/library/windowsazure/ee621787.aspx)
-   [Base de datos SQL de Azure](http://msdn.microsoft.com/es-es/library/windowsazure/ee336279.aspx)

