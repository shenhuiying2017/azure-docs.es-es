<properties linkid="develop-mobile-tutorials-dotnet-rbac-with-aad" urlDisplayName="Role Based Access Control with Azure Active Directory" pageTitle="Role Based Access Control in Mobile Services and Azure Active Directory (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to control access based on Azure Active Directory roles in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Role Based Access Control in Mobile Services and Azure Active Directory" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/21/2014" ms.author="wesmc" />

# Control de acceso basado en roles en Servicios móviles y Azure Active Directory

<div class="dev-center-tutorial-selector sublanding">
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/" title="C# para Tienda Windows" class="current">C# para Tienda Windows</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/" title="Back-end de .NET" class="current">Back-end de .NET</a> | 
    <a href="/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/" title="Back-end de JavaScript">Back-end de JavaScript</a>
</div>

El control de acceso basado en roles (RBAC) es la práctica de asignar permisos a roles que los usuarios pueden tener, donde se definen amablemente los límites de lo que determinadas clases de usuarios pueden y no pueden hacer. Este tutorial le guiará en el proceso para agregar RBAC básico a Servicios móviles de Azure.

En este tutorial, se muestra el control de acceso basado en roles comprobando la pertenencia de cada usuario al grupo Sales definido en Azure Active Directory (AAD). La comprobación de acceso se llevará a cabo con el backend .NET de Servicios Móviles mediante [Graph Client Library][Graph Client Library] para Azure Active Directory. Solo los usuarios que pertenecen al grupo Sales podrán consultar los datos.

> [WACOM.NOTE] La finalidad de este tutorial es ampliar sus conocimientos sobre autenticación para incluir prácticas de autorización. Debe realizar primero el tutorial [Introducción a la autenticación][Introducción a la autenticación] usando el proveedor de autenticación de Azure Active Directory. Este tutorial continúa la actualización de la aplicación TodoItem que se usa en el tutorial [Introducción a la autenticación][Introducción a la autenticación].

Este tutorial le guiará para llevar a cabo los siguientes pasos:

1.  [Creación de un grupo Sales con pertenencia][Creación de un grupo Sales con pertenencia]
2.  [Generación de una clave para la aplicación integrada][Generación de una clave para la aplicación integrada]
3.  [Creación de un atributo de autorización personalizado][Creación de un atributo de autorización personalizado]
4.  [Adición de comprobación de acceso basado en roles a las operaciones de la base de datos][Adición de comprobación de acceso basado en roles a las operaciones de la base de datos]
5.  [Prueba del acceso del cliente][Prueba del acceso del cliente]

Este tutorial requiere lo siguiente:

-   Visual Studio 2013 en Windows 8.1.
-   Realización del tutorial [Introducción a la autenticación][Introducción a la autenticación] usando el proveedor de autenticación de Azure Active Directory.
-   Realización del tutorial [Almacenamiento de scripts de servidor][Almacenamiento de scripts de servidor] para estar familiarizado con el uso de un repositorio Git para almacenar scripts de servidor.

## <a name="create-group"></a>Creación de un grupo Sales con pertenencia

[WACOM.INCLUDE [mobile-services-aad-rbac-create-sales-group](../includes/mobile-services-aad-rbac-create-sales-group.md)]

## <a name="generate-key"></a>Generación de una clave para la aplicación integrada

Durante el tutorial [Introducción a la autenticación][Introducción a la autenticación], creó un registro para la aplicación integrada cuando realizó el paso [Registro para usar un inicio de sesión de Azure Active Directory][Registro para usar un inicio de sesión de Azure Active Directory]. En esta sección, generará una clave que se usará cuando se lea información de directorios con el identificador de cliente de esa aplicación integrada.

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]

## <a name="create-custom-authorization-attribute"></a>Creación de un atributo de autorización personalizado en el servicio móvil

En esta sección, va a crear un nuevo atributo de autorización personalizado que se puede usar para realizar comprobaciones de acceso en operaciones del servicio móvil. El atributo buscará un grupo de Active Directory basándose en el nombre de rol que se le ha pasado. Después realizará comprobaciones de acceso según la pertenencia al grupo.

1.  En Visual Studio, haga clic con el botón secundario en el proyecto de backend .NET de servicio móvil y elija **Administrar paquetes de NuGet**.

2.  En el cuadro de diálogo Administrador de paquetes de NuGet, escriba **ADAL** en los criterios de búsqueda para buscar e instalar la **biblioteca de autenticación de Active Directory** para el servicio móvil.

3.  En el Administrador de paquetes de NuGet, instale también **Microsoft Azure Active Directory Graph Client Library** para su servicio móvil.

4.  En Visual Studio, haga clic con el botón secundario en el proyecto de servicio móvil y elija **Agregar** y después **Nueva carpeta**. Dele el nombre **Utilities** a esta carpeta.

5.  En Visual Studio, haga clic con el botón secundario en la nueva carpeta **Utilities** y agregue un archivo de clase nuevo denominado **AuthorizeAadRole.cs**.

    ![][0]

6.  En el archivo AuthorizeAadRole.cs, agregue las siguientes instrucciones `using` al principio del archivo.

        using System.Net;
        using System.Net.Http;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;
        using System.Linq.Expressions;

7.  En AuthorizeAadRole.cs, agregue el siguiente tipo enumerado al espacio de nombres Utilities. En este ejemplo, solo vamos a trabajar con el rol **Sales**. Los demás son solo ejemplos de grupos que podría usar.

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

8.  En AuthorizeAadRole.cs, agregue la siguiente definición de clase `AuthorizeAadRole` al espacio de nombres Utilities.

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

            public AadRoles Role { get; private set; }

            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }

9.  En AuthorizeAadRole.cs, agregue el siguiente método `GetAADToken` a la clase `AuthorizeAadRole`.

    > [WACOM.NOTE] Debe almacenar en caché el token en lugar de crear uno nuevo con cada comprobación de acceso. Después, actualice la memoria caché cuando haya intentos de uso del token que produzcan una excepción AccessTokenExpiredException como se indica en [Graph Client Library][Graph Client Library]. Esto no se muestra en el código siguiente por simplificar, pero paliará el tráfico extra de red para su instancia de Active Directory.

        private string GetAADToken(ApiServices services)
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;

            if (services == null)
                return null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken: Could not retrieve all AAD app settings from the mobile service configuration.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = authContext.AcquireToken(GraphResourceId, clientCred);

            if (result != null)
                token = result.AccessToken;

            return token;
        }

10. En AuthorizeAadRole.cs, actualice el método `OnAuthorization` en la clase `AuthorizeAadRole` con el siguiente código. Este código utiliza [Graph Client Library][Graph Client Library] para buscar el grupo de Active Directory correspondiente al rol. Después, comprueba la pertenencia del usuario a ese grupo para autorizarlo.

    > [WACOM.NOTE] Este código busca el grupo de Active Directory por el nombre. Muchas veces, es mejor almacenar el identificador del grupo como un valor de configuración de la aplicación de servicio móvil, porque el nombre del grupo puede cambiar, pero el identificador permanece igual. Sin embargo, si se cambia el nombre de un grupo, suele haber al menos un cambio en el ámbito del rol que puede requerir también una actualización del código del servicio móvil.

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            ApiServices services = new ApiServices(actionContext.ControllerContext.Configuration);

            // Check whether we are running in a mode where local host access is allowed 
            // through without authentication.
            if (!this.isInitialized)
            {
                HttpConfiguration config = actionContext.ControllerContext.Configuration;
                this.isHosted = config.GetIsHosted();
                this.isInitialized = true;
            }

            // No security when hosted locally
            if (!this.isHosted && actionContext.RequestContext.IsLocal)
            {
                services.Log.Warn("AuthorizeAadRole: Local Hosting.");
                return;
            }

            ApiController controller = actionContext.ControllerContext.Controller as ApiController;
            if (controller == null)
            {
                services.Log.Error("AuthorizeAadRole: No ApiController.");
                return;
            }

            string accessToken = GetAADToken(services);
            if (accessToken == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                return;
            }

            GraphConnection graphConnection = new GraphConnection(accessToken);
            if (graphConnection == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get a graph connection.");
                return;
            }

            bool isAuthorized = false;

            // Find the group using a filter on the name
            FilterGenerator groupFilter = new FilterGenerator();
            groupFilter.QueryFilter = ExpressionHelper
                .CreateConditionalExpression(typeof(Group), GraphProperty.DisplayName, Role.ToString(), 
                    ExpressionType.Equal);

            // Get the group id from the filtered results
            PagedResults<Group> groupPages = graphConnection.List<Group>(null, groupFilter);
            string groupId = groupPages.Results.FirstOrDefault().ObjectId;

            // Check group membership to see if the user is part of the group that corresponds to the role
            if (!string.IsNullOrEmpty(groupId))
            {
                ServiceUser serviceUser = controller.User as ServiceUser;
                if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                {
                    var idents = serviceUser.GetIdentitiesAsync().Result;
                    var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                    if (clientAadCredentials != null)
                    {
                        bool isMember = graphConnection.IsMemberOf(groupId, clientAadCredentials.ObjectId);
                        if (isMember)
                        {
                            isAuthorized = true;
                        }
                    }
                }
            }

            if (!isAuthorized)
            {
                actionContext.Response = actionContext.Request
                    .CreateErrorResponse(HttpStatusCode.Forbidden, 
                        "User is not logged in or not a member of the required group");
            }
        }

11. Guarde los cambios en AuthorizeAadRole.cs.

## <a name="add-access-checking"></a>Adición de comprobación de acceso basado en roles a las operaciones de la base de datos

1.  En Visual Studio, expanda la carpeta **Controllers** en el proyecto de servicio móvil. Abra TodoItemController.cs.

2.  En TodoItemController.cs, agregue una instrucción `using` para el espacio de nombres utilities que contiene el atributo de autorización personalizado.

        using todolistService.Utilities;

3.  En TodoItemController.cs, puede agregar el atributo a la clase de controlador o a métodos individuales en función de cómo desee comprobar el acceso. Si desea que todas las operaciones de controlador comprueben el acceso basándose en el mismo rol, solo tiene que agregar el atributo a la clase. Para probar este tutorial, agregue el atributo a la clase del siguiente modo:

        [AuthorizeAadRole(AadGroups.Sales)]
        public class TodoItemController : TableController<TodoItem>

    Si solo quisiera comprobar el acceso en operaciones insert, update y delete, establecería el atributo solo en esos métodos, del siguiente modo:

        // PATCH tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            return UpdateAsync(id, patch);
        }

        // POST tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

        // DELETE tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task DeleteTodoItem(string id)
        {
            return DeleteAsync(id);
        }

4.  Guarde TodoItemController.cs y compile el servicio móvil para comprobar que no hay errores de sintaxis.
5.  Publique el servicio móvil en su cuenta de Azure.

## <a name="test-client"></a>Prueba del acceso del cliente

[WACOM.INCLUDE [mobile-services-aad-rbac-test-app](../includes/mobile-services-aad-rbac-test-app.md)]

 
<!-- Images --> 


  [Graph Client Library]: http://go.microsoft.com/fwlink/?LinkId=510536
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Creación de un grupo Sales con pertenencia]: #create-group
  [Generación de una clave para la aplicación integrada]: #generate-key
  [Creación de un atributo de autorización personalizado]: #create-custom-authorization-attribute
  [Adición de comprobación de acceso basado en roles a las operaciones de la base de datos]: #add-access-checking
  [Prueba del acceso del cliente]: #test-client
  [Almacenamiento de scripts de servidor]: /es-es/documentation/articles/mobile-services-store-scripts-source-control/
  [Registro para usar un inicio de sesión de Azure Active Directory]: /es-es/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png
