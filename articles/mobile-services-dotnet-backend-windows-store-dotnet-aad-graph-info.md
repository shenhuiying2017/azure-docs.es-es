<properties urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Acceso a la información de gráficos de Azure Active Directory (Tienda Windows) | Centro de desarrollo móvil" metaKeywords="" description="Obtenga información acerca de cómo acceder a la información de Active Directory de Azure con la API Graph en su aplicación de la Tienda Windows." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="wesmc" />

# Acceso a información de Azure Active Directory Graph

[WACOM.INCLUDE [mobile-services-selector-aad-graph](../includes/mobile-services-selector-aad-graph.md)]



Al igual que los demás proveedores de identidades que ofrece Servicios móviles, el proveedor Azure Active Directory (AAD) admite también una completa biblioteca denominada [Graph Client Library] que se puede usar para obtener acceso mediante programación al directorio. En este tutorial, va a actualizar la aplicación ToDoList para personalizar la experiencia de aplicación del usuario autenticado de acuerdo con información de usuario adicional que recuperará del directorio usando la biblioteca [Graph Client Library].

>[AZURE.NOTE] La finalidad de este tutorial es ampliar sus conocimientos sobre autenticación con Azure Active Directory. Debe haber realizado antes el tutorial [Incorporación de autenticación a la aplicación] usando el proveedor de autenticación de Azure Active Directory. Este tutorial continúa la actualización de la aplicación TodoItem que se usa en el tutorial [Incorporación de autenticación a la aplicación]. 



Este tutorial le guiará para llevar a cabo los siguientes pasos:


1. [Generación de una clave de acceso para el registro de la aplicación en AAD] 
2. [Creación de una API GetUserInfo personalizada] 
3. [Actualización de la aplicación para usar la API personalizada]
4. [Prueba de la aplicación]

##Requisitos previos 

Antes de comenzar este tutorial, debe haber realizado los siguientes tutoriales de Servicios móviles:

+ [Introducción a la autenticación]<br/>Agrega un requisito de inicio de sesión a la aplicación de ejemplo TodoList.

+ [Tutorial sobre API personalizadas]<br/>Muestra cómo llamar a una API personalizada. 



## <a name="generate-key"></a>Generación de una clave de acceso para el registro de la aplicación en AAD


Durante el tutorial [Introducción a la autenticación], creó un registro para la aplicación integrada cuando realizó el paso [Registro para usar un inicio de sesión de Azure Active Directory]. En esta sección, generará una clave que se usará cuando se lea información de directorios con el identificador de cliente de esa aplicación integrada. 

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]


## <a name="create-api"></a>Creación de una API GetUserInfo personalizada

En esta sección, creará la API personalizada GetUserInfo que usará la biblioteca [Graph Client Library] para recuperar de AAD más información sobre el usuario.

Si nunca ha usado API personalizadas con Servicios móviles, consulte el [Tutorial sobre API personalizadas] antes de realizar esta sección.

1. En Visual Studio, haga clic con el botón secundario en el proyecto de backend .NET de servicio móvil y elija **Administrar paquetes de NuGet**.
2. En el cuadro de diálogo Administrador de paquetes de NuGet, escriba **ADAL** en los criterios de búsqueda para buscar e instalar la **biblioteca de autenticación de Active Directory** para el servicio móvil.
3. En el Administrador de paquetes de NuGet, instale también **Microsoft Azure Active Directory Graph Client Library** para su servicio móvil.

4. En Visual Studio, haga clic con el botón secundario en la carpeta **Controllers** del proyecto de servicio móvil y elija **Agregar** para añadir un nuevo **controlador personalizado de Servicios Móviles de Microsoft Azure** denominado `GetUserInfoController`. El cliente llamará a esta API para obtener información del usuario de Active Directory.

5. En el nuevo archivo GetUserInfoController.cs, agregue las siguientes instrucciones `using`.

        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;

6. En GetUserInfoController.cs, agregue el siguiente método `GetAADToken` a la clase.

        private string GetAADToken()
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetUserInfo API: Could not retrieve AAD app settings from the mobile service configuration.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientid, clientCred);

            if (result != null)            
                token = result.AccessToken;

            return token;
        }

    Este método usa la configuración de la aplicación que estableció en el servicio móvil en el [Portal de administración de Azure] para obtener un token de acceso a Active Directory.

7. En GetUserInfoController.cs, agregue el siguiente método `GetAADUser` a la clase.

        private User GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetUserInfo API: No Service or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetUserInfo API: Could not get AAD credientials for the logged in user.");
                return null;
            }

            string accesstoken = GetAADToken();
            if (accesstoken == null)
            {
                Services.Log.Error("GetUserInfo API: Failed to get an AAD access token.");
                return null;
            }

            GraphConnection graphConnection = new GraphConnection(accesstoken);
            var user = graphConnection.Get<User>(clientAadCredentials.ObjectId);

            return user;
        }

    Este método obtiene el identificador del objeto de Active Directory para el usuario autorizado y usa la biblioteca Graph Client Library para obtener de Active Directory la información del usuario.


8. En GetUserInfoController.cs, reemplace el método `Get` por el siguiente. Este método devuelve el objeto `User` de la biblioteca Graph Client Library y requiere que un usuario autorizado llame a la API.

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public User Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return GetAADUser();
        }

9. Guarde los cambios y compile el servicio para comprobar que no existen errores de sintaxis.
10. Publique el proyecto de servicio móvil en su cuenta de Azure. 


## <a name="update-app"></a>Actualización de la aplicación para que use GetUserInfo

En esta sección, va a actualizar el método `AuthenticateAsync` que implementó en el tutorial [Incorporación de autenticación a la aplicación] para que llame a la API personalizada y devuelva más información sobre el usuario de AAD. 

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]
  


## <a name="test-app"></a>Prueba de la aplicación

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]





##<a name="next-steps"></a>Pasos siguientes

En el próximo tutorial, [Control de acceso basado en roles en Servicios móviles y Azure Active Directory], usará control de acceso basado en roles con Azure Active Directory (AAD) para comprobar la pertenencia a grupos antes de permitir el acceso. 



<!-- Anchors. -->
[Generación de una clave de acceso para el registro de la aplicación en AAD]: #generate-key
[Creación de una API GetUserInfo personalizada]: #create-api
[Actualización de la aplicación para usar la API personalizada]: #update-app
[Prueba de la aplicación]: #test-app
[Pasos siguientes]:#next-steps

<!-- Images -->


<!-- URLs. -->
[Incorporación de autenticación a la aplicación]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Registro en Azure Active Directory]: /es-es/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Tutorial sobre API personalizadas]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
[Almacenamiento de scripts de servidor]: /es-es/documentation/articles/mobile-services-store-scripts-source-control/
[Registro para usar un inicio de sesión de Azure Active Directory]: /es-es/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Graph Client Library]: http://go.microsoft.com/fwlink/?LinkId=510536
[Obtener usuario]: http://msdn.microsoft.com/es-es/library/azure/dn151678.aspx
[Control de acceso basado en roles con Azure Active Directory en Servicios móviles]: /es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/
