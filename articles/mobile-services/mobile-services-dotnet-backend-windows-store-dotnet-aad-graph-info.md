<properties 
	pageTitle="Acceso a la información de gráficos de Azure Active Directory (Tienda Windows) | Microsoft Azure" 
	description="Obtenga información acerca de cómo acceder a la información de Active Directory de Azure con la API Graph en su aplicación de la Tienda Windows." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="wesmc"/>

# Acceso a información de Azure Active Directory Graph



[AZURE.INCLUDE [mobile-services-selector-aad-graph](../../includes/mobile-services-selector-aad-graph.md)]

##Información general

Al igual que los demás proveedores de identidades que ofrece Servicios móviles, El proveedor Azure Active Directory (AAD) admite también una completa biblioteca denominada API Graph que se puede usar para obtener acceso mediante programación al directorio. En este tutorial, va a actualizar la aplicación ToDoList para personalizar la experiencia de aplicación del usuario autenticado devolviendo información de usuario adicional que recuperará del directorio usando la [API de REST Graph].

Para obtener más información acerca de la API de Azure AD Graph, consulte el [Blog del equipo de Azure Active Directory Graph].


>[AZURE.NOTE]La finalidad de este tutorial es ampliar sus conocimientos sobre autenticación con Azure Active Directory. Debe haber realizado antes el tutorial [Incorporación de autenticación a la aplicación] usando el proveedor de autenticación de Azure Active Directory. Este tutorial continúa la actualización de la aplicación TodoItem que se usa en el tutorial [Incorporación de autenticación a la aplicación].




##Requisitos previos 

Antes de comenzar este tutorial, debe haber realizado los siguientes tutoriales de Servicios móviles:

+ [Agregar autenticación a su aplicación]<br/>Agrega un requisito de inicio de sesión a la aplicación de ejemplo TodoList.

+ [Tutorial sobre API personalizadas]<br/>Muestra cómo llamar a una API personalizada.



## <a name="generate-key"></a>Generación de una clave de acceso para el registro de la aplicación en AAD


Durante el tutorial [Incorporación de autenticación a la aplicación], creó un registro para la aplicación integrada cuando realizó el paso [Registro para usar un inicio de sesión de Azure Active Directory]. En esta sección, generará una clave que se usará cuando se lea información de directorios con el identificador de cliente de esa aplicación integrada.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]


## <a name="create-api"></a>Creación de una API GetUserInfo personalizada

En esta sección, creará la API personalizada GetUserInfo que usará la API de Azure AD Graph para recuperar de AAD más información sobre el usuario.

Si nunca ha usado API personalizadas con Servicios móviles, consulte el [Tutorial sobre API personalizadas] antes de realizar esta sección.

1. En Visual Studio, haga clic con el botón derecho en el proyecto de back-end de .NET de servicio móvil y elija **Administrar paquetes de NuGet**.
2. En el cuadro de diálogo Administrador de paquetes de NuGet, escriba **ADAL** en los criterios de búsqueda para buscar e instalar la **biblioteca de autenticación de Active Directory** para el servicio móvil. Este tutorial se ha probado recientemente con la versión 3.3.205061641-alpha (versión preliminar) del paquete ADAL.


3. En Visual Studio, haga clic con el botón derecho en la carpeta **Controladores** del proyecto de servicio móvil y haga clic en **Agregar** para agregar un nuevo controlador personalizado de **Servicios móviles de Microsoft Azure** llamado `GetUserInfoController`. El cliente llamará a esta API para obtener información del usuario de Active Directory.

4. En el nuevo archivo GetUserInfoController.cs, agregue las siguientes instrucciones `using`.

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using System.Globalization;
		using System.Threading.Tasks;
		using Newtonsoft.Json;
		using System.IO;

5. En el nuevo archivo GetUserInfoController.cs, agregue la siguiente clase `UserInfo` para retener la información que deseamos obtener de AAD.

	    public class UserInfo
	    {
	        public String displayName { get; set; }
	        public String streetAddress { get; set; }
	        public String city { get; set; }
	        public String state { get; set; }
	        public String postalCode { get; set; }
	        public String mail { get; set; }
	        public String[] otherMails { get; set; }
	
	        public override string ToString()
	        {
	            return "displayName : " + displayName + "\n" +
	                   "streetAddress : " + streetAddress + "\n" +
	                   "city : " + city + "\n" +
	                   "state : " + state + "\n" +
	                   "postalCode : " + postalCode + "\n" +
	                   "mail : " + mail + "\n" +
	                   "otherMails : " + string.Join(", ",otherMails);
	        }
	    }

6. En GetUserInfoController.cs, agregue las siguientes variables de miembros a la clase `GetUserInfoController`.

        private const string AadInstance = "https://login.windows.net/{0}";
        private const string GraphResourceId = "https://graph.windows.net/";
        private const string APIVersion = "?api-version=2013-04-05";

        private string tenantdomain;
        private string clientid;
        private string clientkey;
        private string token = null;


7. En GetUserInfoController.cs, agregue el siguiente método `GetAADToken` a la clase.

        private async Task<string> GetAADToken()
        {
            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientCred);

            if (result != null)
                token = result.AccessToken;
            else
                Services.Log.Error("GetAADToken() : Failed to return a token.");

            return token;
        }

    Este método usa la configuración de la aplicación que estableció en el servicio móvil en el [Portal de administración de Azure] para obtener un token de acceso a Active Directory.

7. En GetUserInfoController.cs, agregue el siguiente método `GetAADUser` a la clase.

        private async Task<UserInfo> GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetAADUser() : No ServiceUser or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetAADUser() : Could not get AAD credientials for the logged in user.");
                return null;
            }

            if (token == null)
                await GetAADToken();

            if (token == null)
            {
                Services.Log.Error("GetAADUser() : No token.");
                return null;
            }

            // User the AAD Graph REST API to get the user's information
            string url = GraphResourceId + tenantdomain + "/users/" + clientAadCredentials.ObjectId + APIVersion;
            Services.Log.Info("GetAADUser() : Request URL : " + url);
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
            request.Method = "GET";
            request.ContentType = "application/json";
            request.Headers.Add("Authorization", token);
            UserInfo userinfo = null;
            try
            {
                WebResponse response = await request.GetResponseAsync();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string userjson = sr.ReadToEnd();
                userinfo = JsonConvert.DeserializeObject<UserInfo>(userjson);
                Services.Log.Info("GetAADUser user : " + userinfo.ToString());
            }
            catch(Exception e)
            {
                Services.Log.Error("GetAADUser exception : " + e.Message);
            }

            return userinfo;
        }

    Este método obtiene el identificador del objeto de Active Directory para el usuario autorizado y usa la API de REST Graph para obtener de Active Directory la información del usuario.


8. En GetUserInfoController.cs, reemplace el método `Get` por el siguiente. Este método devuelve la entidad de usuario de Azure Active directory mediante la API de REST de Graph y requiere un usuario autorizado para llamar a la API.

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public async Task<UserInfo> Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return await GetAADUser();
        }

9. Guarde los cambios y compile el servicio para comprobar que no existen errores de sintaxis.
10. Publique el proyecto de servicio móvil en su cuenta de Azure. 


## <a name="update-app"></a>Actualización de la aplicación para que use GetUserInfo

En esta sección, actualizará el método `AuthenticateAsync` que implementó en el tutorial [Incorporación de autenticación a la aplicación] para llamar a la API personalizada y devolver información adicional sobre el usuario desde AAD.

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../../includes/mobile-services-aad-graph-info-update-app.md)]
  


## <a name="test-app"></a>Prueba de la aplicación

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../../includes/mobile-services-aad-graph-info-test-app.md)]





##<a name="next-steps"></a>Pasos siguientes

En el próximo tutorial, [Control de acceso basado en roles con Azure Active Directory en Servicios móviles], usará control de acceso basado en roles con Azure Active Directory (AAD) para comprobar la pertenencia a grupos antes de permitir el acceso.



<!-- Anchors. -->
[Generate an access key for the App registration in AAD]: #generate-key
[Create a GetUserInfo custom API]: #create-api
[Update the app to use the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images -->


<!-- URLs. -->
[Agregar autenticación a su aplicación]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Incorporación de autenticación a la aplicación]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Portal de administración de Azure]: https://manage.windowsazure.com/
[API de REST Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Tutorial sobre API personalizadas]: mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api.md
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[Registro para usar un inicio de sesión de Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Blog del equipo de Azure Active Directory Graph]: http://go.microsoft.com/fwlink/?LinkId=510536
[Get User]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Control de acceso basado en roles con Azure Active Directory en Servicios móviles]: mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac.md
 

<!---HONumber=August15_HO8-->