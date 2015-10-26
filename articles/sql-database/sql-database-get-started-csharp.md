<properties 
   pageTitle="Creación de una Base de datos SQL de Azure con C#" 
   description="En este artículo se muestra cómo crear una base de datos SQL de Azure con C# usando la biblioteca de Base de datos SQL de Azure para .NET." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="09/01/2015"
   ms.author="sstein"/>

# Creación de una Base de datos SQL con C&#x23;

**Base de datos única**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)



En este artículo se ofrecen comandos para crear una base de datos SQL de Azure con C# usando la [biblioteca de Base de datos SQL de Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).

En este artículo se muestra cómo crear una base de datos única. Para crear bases de datos elásticas, consulte [Creación de un grupo de bases de datos elásticas](sql-database-elastic-pool-portal.md).

Los fragmentos de código individuales se dividen por motivos de claridad y una aplicación de consola de ejemplo reúne todos los comandos en la sección de la parte inferior de este artículo.

La biblioteca de Base de datos SQL de Azure para .NET ofrece una API basada en el [Administrador de recursos de Azure](resource-group-overview.md) que encapsula la [API de REST de Base de datos SQL basada en el Administrador de recursos](https://msdn.microsoft.com/library/azure/mt163571.aspx). Esta biblioteca cliente sigue el patrón común para las bibliotecas de cliente basada en el Administrador de recursos. El Administrador de recursos requiere grupos de recursos y la autenticación con [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD).

<br>

> [AZURE.NOTE]La biblioteca de bases de datos SQL para .NET está actualmente en vista previa.

<br>

Necesitará lo siguiente para completar los pasos de este artículo:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Visual Studio. Para obtener una copia gratuita de Visual Studio, consulte la página [Descargas de Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).


## Instalación de las bibliotecas necesarias

Obtenga las bibliotecas de administración necesarias instalando los siguientes paquetes mediante la [consola del Administrador de paquetes](http://docs.nuget.org/Consume/Package-Manager-Console):

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre
    PM> Install-Package Microsoft.Azure.Management.Resources –Pre
    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


## Configurar la autenticación con Azure Active Directory

Debe habilitar primero su aplicación cliente para que tenga acceso a la API de REST configurando la autenticación necesaria.

Las [API de REST del Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx) usan Azure Active Directory para la autenticación.

Para autenticar la aplicación de cliente basada en el usuario actual, primero debe registrar su aplicación en el dominio de AAD asociado a la suscripción en la que se han creado los recursos de Azure. Si se creó su suscripción de Azure con una cuenta de Microsoft en lugar de una cuenta profesional o educativa, ya tendrá un dominio de AAD predeterminado. El registro de la aplicación se puede realizar en el [Portal de Azure](https://manage.windowsazure.com/).

Para crear una nueva aplicación y registrarla en el directorio activo correcto, haga lo siguiente:

1. Desplace el menú del lado izquierdo para buscar el servicio de **Active Directory** y ábralo.

    ![AAD][1]

2. Seleccione el directorio para autenticar la aplicación y haga clic en su **Nombre**.

    ![Directorios][4]

3. En la página del directorio, haga clic en **APLICACIONES**.

    ![Aplicaciones][5]

4. Haga clic en **AGREGAR** para crear una nueva aplicación.

    ![Agregar aplicación][6]

5. Haga clic en **Agregar una aplicación que mi organización está desarrollando**.

5. Asigne un **NOMBRE** para la aplicación y seleccione **APLICACIÓN DE CLIENTE NATIVO**.

    ![Agregar aplicación][7]

6. Suministre un **URI DE REDIRECCIÓN**. No tiene que ser un extremo real, simplemente un URI válido.

    ![Agregar aplicación][8]

7. Finalice la creación de la aplicación, haga clic en **CONFIGURAR** y copie el **ID. DE CLIENTE** (lo necesitará en su código).

    ![obtener id. de cliente][9]


1. En la parte inferior de la página, haga clic en **Agregar aplicación**.
1. Seleccione **Aplicaciones de Microsoft**.
1. Seleccione **API de administración de servicios de Azure** y luego complete el asistente.
2. Con la API seleccionada ahora debe conceder los permisos específicos necesarios para tener acceso a esta API seleccionando **Acceder a la administración del servicio de Azure (vista previa)**.

    ![permisos][2]

2. Haga clic en **GUARDAR**.



### Identificar el nombre de dominio

Se requiere el nombre de dominio para su código. Para identificar de manera sencilla el nombre de dominio adecuado:

1. Vaya al [Portal de vista previa de Azure](https://portal.azure.com).
2. Mantenga el puntero sobre su nombre en la esquina superior derecha y anote el dominio que aparece en la ventana emergente.

    ![Identificar nombre de dominio][3]





**Recursos de AAD adicionales**

Encontrará información adicional sobre el uso de Azure Active Directory para la autenticación en [esta útil entrada de blog](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Recuperar el token de acceso para el usuario actual 

La aplicación cliente debe recuperar el token de acceso de la aplicación para el usuario actual. La primera vez que un usuario ejecuta este código se le pedirá que escriba sus credenciales de usuario y el token resultante se almacenará en caché localmente. Las ejecuciones posteriores recuperarán el token de la memoria caché y solo pedirán al usuario que inicie sesión si el token ha expirado.

    /// <summary>
    /// Prompts for user credentials when first run or if the cached credentials have expired.
    /// </summary>
    /// <returns>The access token from AAD.</returns>
    private static AuthenticationResult GetAccessToken()
    {
        AuthenticationContext authContext = new AuthenticationContext
            ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

        AuthenticationResult token = authContext.AcquireToken
            ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                clientId,
        new Uri(clientAppUri) /* redirect URI */,
        PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

        return token;
    }



> [AZURE.NOTE]En los ejemplos de este artículo se usa un formulario sincrónico de cada solicitud de API y se bloquea hasta que finaliza la llamada de REST en el servicio subyacente. Hay métodos asincrónicos disponibles.



## Crear un grupo de recursos

Con el Administrador de recursos, todos los recursos se deben crear en un grupo de recursos. Un grupo de recursos es un contenedor que incluye los recursos relacionados de una aplicación. Con la Base de datos SQL de Azure el servidor de bases de datos debe crearse dentro de un grupo de recursos existente y después crearse la base de datos en el servidor. Luego, antes de que una aplicación pueda conectarse al servidor o a la base de datos, también debe crear una regla de firewall en el servidor para abrir el acceso desde la dirección IP del cliente.


    // Create a resource management client 
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));
    
    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };
    
    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("ResourceGroupName", resourceGroupParameters);



## Creación de un servidor 

Las bases de datos SQL se incluye en servidores. El nombre de servidor debe ser único globalmente entre todos los servidores SQL de Azure de manera que obtendrá un error aquí si el nombre del servidor ya existe. También debe tener en cuenta que este comando puede tardar varios minutos en completarse.


    // Create a SQL Database management client
    SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken));

    // Create a server
    ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ServerCreateOrUpdateProperties()
        {
            AdministratorLogin = "ServerAdmin",
            AdministratorLoginPassword = securelyStoredPassword,
            Version = "12.0"
        }
    };

    var serverResult = sqlClient.Servers.CreateOrUpdate("ResourceGroupName", "ServerName", serverParameters);




## Creación de una regla de firewall para permitir el acceso al servidor

De forma predeterminada, no se puede conectar a un servidor desde cualquier ubicación. Para conectarse a un servidor o a cualquier base de datos del servidor, se debe definir una [regla de firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx) que permita el acceso desde la dirección IP del cliente.

En el ejemplo siguiente se crea una regla que abre el acceso al servidor desde cualquier dirección IP. Se recomienda crear inicios de sesión y contraseñas de SQL adecuados para proteger la base de datos y no depender de reglas de firewall como defensa principal frente a las intrusiones.


    // Create a firewall rule on the server to allow TDS connection 
    FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
    {
        Properties = new FirewallRuleCreateOrUpdateProperties()
        {
            StartIpAddress = "0.0.0.0",
            EndIpAddress = "255.255.255.255"
        }
    };

    var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("ResourceGroupName", "ServerName", "FirewallRuleName", firewallParameters);




Para permitir que otros servicios de Azure tengan acceso a un servidor, agregue una regla de firewall y establezca tanto StartIpAddress como EndIpAddress en 0.0.0.0. Tenga en cuenta que esto permite que el tráfico de Azure de *cualquier* suscripción de Azure tenga acceso al servidor.


## Creación de una base de datos

El siguiente comando creará una nueva base de datos básica si no hay una base de datos con el mismo nombre en el servidor. Si existe una base de datos con el mismo nombre, esta se actualizará.

        // Create a database

        // Retrieve the server on which the database will be created
        Server currentServer = sqlClient.Servers.Get("ResourceGroupName", "ServerName").Server;
 
        // Create a database: configure create or update parameters and properties explicitly
        DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
        {
            Location = currentServer.Location,
            Properties = new DatabaseCreateOrUpdateProperties()
            {
                Edition = "Standard",
                RequestedServiceObjectiveName = "S0",
            }
        };

        var dbResponse = sqlClient.Databases.CreateOrUpdate("ResourceGroupNname", "ServerName", "Database1", newDatabaseParameters);






## Aplicación de consola de ejemplo


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    
    namespace CreateSqlDatabase
    {
     class Program
     {

        #region user variables
        
        // Azure subscription id
        private static string subscriptionId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";
     
        
        // application client ID from Azure Active Directory (AAD)
        private static string clientAppUri = "http://app1";
        private static string clientId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";

        private static string resourcegroupName = "rg1";

        private static string dataCenterLocation = "Japan West";

        private static string databaseName = "newDatabaseName";
        private static string databaseEdition = "Standard";
        private static string databasePerformanceLevel = "S0";

        private static string serverName = "serverName";
        private static string serverAdmin = "admin";
        private static string serverAdminPassword = securelyStoredPassword;
        private static string serverVersion = "12.0";

        private static string firewallRuleName = "rule1";
        private static string firewallRuleStartIp = "0.0.0.0";
        private static string firewallRuleEndIp = "255.255.255.255";



        private static string domainName = "microsoft.onmicrosoft.com";

        private static string serverLocation = "Japan West";

        #endregion


        static void Main(string[] args)
        {
            var token = GetAccessToken();

            // Who am I?
            Console.WriteLine("Identity: {0} {1}", token.UserInfo.GivenName, token.UserInfo.FamilyName);
            Console.WriteLine("Token expires on {0}", token.ExpiresOn);
            Console.WriteLine("");

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = dataCenterLocation
            };

            //Create a resource group
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourcegroupName, resourceGroupParameters);

            Console.WriteLine("Resource group {0} create or update completed with status code {1} ", resourceGroupResult.ResourceGroup.Name, resourceGroupResult.StatusCode);

            //create a SQL Database management client
            TokenCloudCredentials tokenCredentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);

            SqlManagementClient sqlClient = new SqlManagementClient(tokenCredentials);

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = serverVersion
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate(resourcegroupName, serverName, serverParameters);

            var serverGetResult = sqlClient.Servers.Get(resourcegroupName, serverName);


            Console.WriteLine("Server {0} create or update completed with status code {1}", serverResult.Server.Name, serverResult.StatusCode);

            // Create a firewall rule on the server to allow TDS connection 

            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = firewallRuleStartIp,
                    EndIpAddress = firewallRuleEndIp
                }
            };

            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourcegroupName, serverName, firewallRuleName, firewallParameters);

            Console.WriteLine("Firewall rule {0} create or update completed with status code: {1}", firewallResult.FirewallRule.Name, firewallResult.StatusCode);

            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourcegroupName, serverName).Server;

            // Create a database
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerformanceLevel,
                }
            };

            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourcegroupName, serverName, databaseName, newDatabaseParameters);

            Console.WriteLine("Database {0} created with status code: {1}. Service Objective: {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);
           
            Console.WriteLine(System.Environment.NewLine + "Press any key to exit.");
            Console.ReadKey();
        }

        /// <summary>
        /// Prompts for user credentials when first run or if the cached credentials have expired.
        /// </summary>
        /// <returns>The access token from AAD.</returns>
        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(clientAppUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
      }
    }


## Pasos siguientes

- [Conexión y consultas a Base de datos SQL con C#](sql-database-connect-query.md)
- [Conexión con SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)

## Recursos adicionales

- [Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)





<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png

<!---HONumber=Oct15_HO3-->