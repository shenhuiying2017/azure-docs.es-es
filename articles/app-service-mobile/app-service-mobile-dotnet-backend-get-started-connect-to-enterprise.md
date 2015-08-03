<properties
	pageTitle="Conexión de una aplicación móvil a un SaaS empresarial | Centro de desarrollo móvil"
	description="Obtenga información acerca de cómo realizar llamadas a recursos empresariales como SharePoint Online"
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor="na"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="get-started-article" 
	ms.date="06/19/2015"
	ms.author="mahender"/>

# Conexión de una aplicación móvil a API de SaaS

En este tutorial, conectará su aplicación móvil a una solución de software como servicio (SaaS) empresarial. Actualizará la aplicación a partir de [Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory] para crear un documento de Word en SharePoint Online siempre que se agregue un nuevo TodoItem.

Este tutorial requiere lo siguiente:

* Visual Studio 2013 en Windows 8.1
* Una suscripción activa a [SharePoint Online]
* Realización del tutorial [Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory]. Debería utilizar el inquilino que le suministra su suscripción a SharePoint.

## <a name="configure-permissions"></a>Configuración de la aplicación para acceso delegado a SharePoint
De manera predeterminada, el token que recibe de AAD tiene permisos limitados. Para acceder a una aplicación SaaS o a un recurso de terceros, como SharePoint Online, debe permitirlo explícitamente.

1. En la sección **Active Directory** del [Portal de administración de Azure], seleccione su inquilino. Vaya a la aplicación web que creó para el Servicio de aplicaciones.

2. En la pestaña **Configurar**, desplácese hacia la parte inferior de la página hasta llegar a la sección de permisos para otras aplicaciones. Seleccione **Office 365 SharePoint Online** y conceda el permiso delegado **Editar o eliminar archivos de usuarios**. A continuación, haga clic en **Guardar**.

    ![][1]

Ahora ha configurado AAD para emitir un token de acceso a SharePoint para el Servicio de aplicaciones.

## <a name="store-credentials"></a>Incorporación de información de SharePoint a una aplicación móvil

Para realizar una llamada a SharePoint, tiene que especificar los extremos con los que tiene que hablar la aplicación móvil. También tiene que probar la identidad de la aplicación móvil. Para ello, se utiliza un par de identificador de cliente y secreto de cliente. Ya ha obtenido y almacenado el identificador de cliente para la aplicación móvil durante la configuración del inicio de sesión de AAD. Dado que son credenciales que distinguen mayúsculas de minúsculas, no debe almacenarlas como texto no cifrado en nuestro código, sino que debe configurar estos valores como Configuración de la aplicación para nuestro sitio Código de aplicación móvil.

1. Vuelva a la pestaña Aplicaciones de AAD para su inquilino y seleccione la aplicación web para su servicio de aplicaciones.

2. En Configurar, desplácese hacia abajo hasta llegar a Claves. Obtendrá un Secreto de cliente generando una clave nueva. Tenga en cuenta que una vez que cree una clave y abandone la página, no hay manera de volver a sacarla del portal. En el momento de la creación debe copiar y guardar este valor en una ubicación segura. Seleccione una duración para la clave y, a continuación, haga clic en guardar y copie el valor resultante.

3. En la sección Código de aplicación móvil del Portal de administración, vaya a la pestaña Configurar y desplácese hacia abajo hasta Configuración de aplicaciones. Aquí puede suministrar un par clave-valor que le ayudará a hacer referencia a las credenciales necesarias.

* Configure SP_Authority para que sea el extremo de autoridad de su inquilino de AAD. Debería ser igual que el valor de autoridad utilizado para su aplicación cliente. Tendrá el formato `https://login.windows.net/contoso.onmicrosoft.com`

* Configure SP_ClientSecret para que sea el valor del secreto de cliente que obtuvo anteriormente.

* Configure SP_SharePointURL para que sea la dirección URL de su sitio de SharePoint. Debe tener el formato `https://contoso-my.sharepoint.com`

Podrá obtener estos valores de nuevo en su código utilizando ApiServices.Settings.

## <a name="obtain-token"></a>Obtención de un token de acceso y llamada a la API de SharePoint

A fin de tener acceso a SharePoint, necesita un token de acceso especial con SharePoint como audiencia de destino. Para obtener este token, tiene que devolver la llamada en AAD con la identidad del Servicio de aplicaciones y el token que se emitió para el usuario.

1. Abra el proyecto de Código de aplicación móvil en Visual Studio.

[AZURE.INCLUDE [app-service-mobile-dotnet-adal-install-nuget](../../includes/app-service-mobile-dotnet-adal-install-nuget.md)]

2. En el Administrador de paquetes NuGet, haga clic en **En línea**. Escriba **Microsoft.Azure.Mobile.Server.AppService** como un término de búsqueda. A continuación, haga clic en **Instalar** para instalar el paquete [Extensión de servicio de aplicación de back-end de .NET de Aplicaciones móviles]. Este paquete le proporciona métodos de extensión para trabajar con información sobre el usuario que ha iniciado sesión actualmente.

2. En el proyecto de Código de aplicación móvil, cree una clase nueva llamada SharePointUploadContext. Añada una instrucción `using Microsoft.Azure.Mobile.Server.AppService;` al archivo. A continuación, agregue lo siguiente a la clase:

        private String accessToken;
        private String mySiteApiPath;
        private String clientId;
        private String clientSecret;
        private String sharepointURL;
        private String authority;
        private const string getFilesPath = "/getfolderbyserverrelativeurl('Documents')/Files";

        public static async Task<SharePointUploadContext> createContext(ServiceUser user, ServiceSettingsDictionary settings)
        {
            //Get the current access token
            AzureActiveDirectoryCredentials creds = (await user.GetIdentitiesAsync()).OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            string userToken = creds.AccessToken;
            return new SharePointUploadContext(userToken, settings);
        }

        private SharePointUploadContext(string userToken, ServiceSettingsDictionary settings)
        {
            //Call ADAL and request a token to SharePoint with the access token
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = ac.AcquireToken(sharepointURL, new ClientCredential(clientId, clientSecret), new UserAssertion(userToken));
            accessToken = ar.AccessToken;
            string upn = ar.UserInfo.UserId;
            mySiteApiPath = "/personal/" + upn.Replace('@','_').Replace('.','_') + "/_api/web";
            clientId = settings.AzureActiveDirectoryClientId;
            clientSecret = settings["SP_ClientSecret"];
            sharepointURL = settings["SP_SharePointURL"];
            authority = settings["SP_Authority"];
        }

3. Ahora cree un método para agregar el archivo a la biblioteca de documentos del usuario:

        public async Task<bool> UploadDocument(string docName, byte[] document)
        {
            string uploadUri = sharepointURL + mySiteApiPath + getFilesPath + string.Format(@"/Add(url='{0}.docx', overwrite=true)", docName);
            using (HttpClient client = new HttpClient())
            {
                Func<HttpRequestMessage> requestCreator = () =>
                {
                    HttpRequestMessage UploadRequest = new HttpRequestMessage(HttpMethod.Post, uploadUri);
                    UploadRequest.Content = new System.Net.Http.ByteArrayContent(document);
                    UploadRequest.Content.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                    UploadRequest.Content.Headers.ContentType.Parameters.Add(new NameValueHeaderValue("odata", "verbose"));
                    return UploadRequest;
                };
                using (HttpRequestMessage uploadRequest = requestCreator.Invoke())
                {
                    uploadRequest.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
                    HttpResponseMessage uploadResponse = await client.SendAsync(uploadRequest);
                }
            }
            return true;
        }

## <a name="create-document"></a>Creación y carga de un documento de Word

Para crear un documento de Word, utilizará el paquete de NuGet OpenXML. Instale este paquete abriendo el Administrador de NuGet y buscando DocumentFormat.OpenXml.

1. Agregue el siguiente código a TodoItemController. Esto creará un documento de Word basado en un TodoItem. El texto del documento será el nombre del elemento.

        private static byte[] CreateWordDocument(TodoItem todoItem)
        {
            byte[] document;
            using (MemoryStream generatedDocument = new MemoryStream())
            {
                using (WordprocessingDocument package = WordprocessingDocument.Create(generatedDocument, WordprocessingDocumentType.Document))
                {
                    MainDocumentPart mainPart = package.MainDocumentPart;
                    if (mainPart == null)
                    {
                        mainPart = package.AddMainDocumentPart();
                        new Document(new Body()).Save(mainPart);
                    }
                    Body body = mainPart.Document.Body;
                    Paragraph p =
                        new Paragraph(
                            new Run(
                                new Text(todoItem.Text)));
                    body.Append(p);
                    mainPart.Document.Save();
                }
                document = generatedDocument.ToArray();
            }
            return document;
        }

2. Reemplace PostTodoItem por lo siguiente:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            SharePointUploadContext context = await SharePointUploadContext.createContext((ServiceUser)this.User, Services.Settings);
            byte[] document = CreateWordDocument(item);
            bool uploadResult = await context.UploadDocument(item.Id, document);

            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

## <a name="test-application"></a>Prueba de la aplicación

1. Publique los cambios en el back-end y, a continuación, ejecute la aplicación cliente. Inicie sesión cuando se le solicite e inserte un nuevo TodoItem.

2. Vaya a su sitio de SharePoint e inicie sesión con el mismo usuario.

3. Seleccione la pestaña OneDrive. En la carpeta Documentos, debería aparecer un documento de Word con un título GUID. Cuando lo abra, debería encontrar el texto de su TodoItem.

<!-- Images. -->

[1]: ./media/app-service-mobile-dotnet-backend-get-started-connect-to-enterprise/aad-sharepoint-permissions.png

<!-- URLs. -->

[Preview Azure Management Portal]: https://portal.azure.com/
[Portal de administración de Azure]: https://manage.windowsazure.com/
[SharePoint Online]: http://office.microsoft.com/es-es/sharepoint/
[Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory]: app-service-mobile-dotnet-backend-ios-aad-sso-preview.md
[Extensión de servicio de aplicación de back-end de .NET de Aplicaciones móviles]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.AppService/
 

<!---HONumber=July15_HO4-->