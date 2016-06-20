<properties
   pageTitle="Registro de la aplicación y obtención de identificador de cliente y la clave para conectarse a Base de datos SQL desde el código | Microsoft Azure"
   description="Obtenga el identificador de cliente y la clave para acceder a Base de datos SQL desde el código."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="06/06/2016"
   ms.author="sstein"/>

# Obtención del identificador de cliente y la clave para conectarse a Base de datos SQL desde el código

Para crear y administrar la Base de datos SQL desde el código debe registrar la aplicación en el dominio de Azure Active Directory (AAD) asociado a la suscripción donde se han creado los recursos de Azure. Al registrar la aplicación, Azure generará un identificador de cliente y una clave que necesitará en el código para autenticar la aplicación. Para obtener más información, consulte [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

## Registro de una aplicación de cliente nativa y obtención del identificador de cliente

Para crear una nueva aplicación y registrarla, haga lo siguiente:

1. Inicie sesión en el [Portal clásico](https://manage.windowsazure.com/) (actualmente, el registro de aplicaciones se debe realizar en el Portal clásico).
1. Busque **Active Directory** en el menú y selecciónelo.

    ![AAD][1]

2. Seleccione el directorio para autenticar la aplicación y haga clic en su **Nombre**.

    ![Directorios][4]

3. En la página del directorio, haga clic en **APLICACIONES**.

    ![Aplicaciones][5]

4. Haga clic en **AGREGAR** para crear una nueva aplicación.

    ![Agregar aplicación][6]

5. Asigne un **NOMBRE** a la aplicación y seleccione **APLICACIÓN DE CLIENTE NATIVO**.

    ![Agregar aplicación][7]

6. Proporcione un **URI DE REDIRECCIÓN**. No tiene que ser un extremo real, simplemente un URI válido.

    ![Agregar aplicación][8]

7. Finalice la creación de la aplicación, haga clic en **CONFIGURAR** y copie el **ID. DE CLIENTE** (este es el valor que necesitará en su código).

    ![obtener id. de cliente][9]


1. Desplácese hacia abajo por la página y haga clic en **Agregar aplicación**.
1. Seleccione **Aplicaciones de Microsoft**.
1. Seleccione **API de administración de servicios de Windows Azure** y, después, complete el asistente.
2. En la sección **Permisos para otras aplicaciones** localice **API de administración de servicios de Windows Azure** y haga clic en **Permisos delegados**.
3. Seleccione **Acceder a la administración de servicios de Azure...**

    ![permisos][2]

2. Haga clic en **GUARDAR** en la parte inferior de la página.



## Registro de una aplicación web (o api web) y obtención del identificador de cliente y la clave

Para crear una nueva aplicación y registrarla en el directorio activo correcto, haga lo siguiente:

1. Inicie sesión en el [Portal clásico](https://manage.windowsazure.com/).
1. Busque **Active Directory** en el menú y selecciónelo.

    ![AAD][1]

2. Seleccione el directorio para autenticar la aplicación y haga clic en su **Nombre**.

    ![Directorios][4]

3. En la página del directorio, haga clic en **APLICACIONES**.

    ![Aplicaciones][5]

4. Haga clic en **AGREGAR** para crear una nueva aplicación.

    ![Agregar aplicación][6]

5. Asigne un **NOMBRE** para la aplicación y seleccione **APLICACIÓN WEB Y/O API WEB**.

    ![Agregar aplicación][10]

6. Rellene la información pertinente en los campos **URL DE INICIO DE SESIÓN** y **URI DE ID. DE APLICACIÓN**. No tiene que ser un extremo real, simplemente un URI válido.

    ![Agregar aplicación][11]

7. Terminar de crear la aplicación y haga clic en **CONFIGURAR**.

    ![configurar][12]

8. Desplácese hasta la sección **Claves** sección y seleccione **1 año** en la lista **Seleccionar duración**. El valor de la clave se mostrará después de guardar, por lo que se deberá volver y copiar la clave más tarde.

    ![establecer duración de la clave][13]



1. Desplácese hacia abajo por la página y haga clic en **Agregar aplicación**.
1. Seleccione **Aplicaciones de Microsoft**.
1. Busque la opción **API de administración de servicios de Windows Azure** y selecciónela y, después, complete el asistente.
2. En la sección **Permisos para otras aplicaciones** localice **API de administración de servicios de Windows Azure** y haga clic en **Permisos delegados**.
3. Seleccione **Acceder a la administración de servicios de Azure...**

    ![permisos][2]

2. Haga clic en **GUARDAR** en la parte inferior de la página.
3. Una vez completada la operación de guardar, localice el ID. DE CLIENTE y la clave:

    ![secretos de la aplicación web][14]



## Obtención del nombre de dominio

A veces se requiere el nombre de dominio para el código de autenticación. Para identificar de manera sencilla el nombre de dominio adecuado:

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Mantenga el puntero sobre su nombre en la esquina superior derecha y anote el dominio que aparece en la ventana emergente.

    ![Identificar nombre de dominio][3]




## Aplicación de consola de ejemplo


Obtenga las bibliotecas de administración necesarias, para lo que debe instalar los siguientes paquetes mediante la [consola del Administrador de paquetes](http://docs.nuget.org/Consume/Package-Manager-Console) en Visual Studio (**Herramientas** > **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**):


    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


Cree una aplicación de consola llamada **SqlDbAuthSample** y reemplace el contenido de **Program.cs** por el código siguiente:

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbAuthSample
    {
    class Program
    {
        static void Main(string[] args)
        {
            token = GetAccessTokenforNativeClient();
            // token = GetAccessTokenUsingUserCredentials(new UserCredential("<email address>"));
            // token = GetAccessTokenForWebApp();

            Console.WriteLine("Signed in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Press Enter to continue...");
            Console.ReadLine();
        }


        // authentication variables (native)
        static string nclientId = "<your client id>";
        static string nredirectUri = "<your redirect URI>";
        static string ndomainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        private static AuthenticationResult GetAccessTokenforNativeClient()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + ndomainName /* Tenant ID or AAD domain */);

            token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    nclientId,
            new Uri(nredirectUri),
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }


        // authentication variables (web)
        static string wclientId = "<your client id>";
        static string wkey = "<your key>";
        static string wdomainName = "<i.e. microsoft.onmicrosoft.com>";

        private static AuthenticationResult GetAccessTokenForWebApp()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + wdomainName /* Tenant ID or AAD domain */);

            ClientCredential cc = new ClientCredential(wclientId, wkey);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                cc);

            return token;
        }


        private static AuthenticationResult GetAccessTokenUsingUserCredentials(UserCredential userCredential)
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + ndomainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                nclientId /* application client ID from AAD*/,
                new Uri(nredirectUri) /* redirect URI */,
                PromptBehavior.Auto,
                new UserIdentifier(userCredential.UserName, UserIdentifierType.RequiredDisplayableId));

            return token;
        }
    }
    }


Para ejemplos de código específicos y que estén relacionados con la autenticación de Azure AD, vea el [Blog de seguridad de SQL Server](http://blogs.msdn.com/b/sqlsecurity/) en MSDN.

## Consulte también

- [Creación de una Base de datos SQL con C#](sql-database-get-started-csharp.md)
- [Conexión a Base de datos SQL mediante autenticación de Azure Active Directory](sql-database-aad-authentication.md)



<!--Image references-->
[1]: ./media/sql-database-client-id-keys/aad.png
[2]: ./media/sql-database-client-id-keys/permissions.png
[3]: ./media/sql-database-client-id-keys/getdomain.png
[4]: ./media/sql-database-client-id-keys/aad2.png
[5]: ./media/sql-database-client-id-keys/aad-applications.png
[6]: ./media/sql-database-client-id-keys/add.png
[7]: ./media/sql-database-client-id-keys/add-application.png
[8]: ./media/sql-database-client-id-keys/add-application2.png
[9]: ./media/sql-database-client-id-keys/clientid.png
[10]: ./media/sql-database-client-id-keys/add-application-web.png
[11]: ./media/sql-database-client-id-keys/add-application-app-properties.png
[12]: ./media/sql-database-client-id-keys/configure.png
[13]: ./media/sql-database-client-id-keys/key-duration.png
[14]: ./media/sql-database-client-id-keys/web-secrets.png

<!---HONumber=AcomDC_0608_2016-->