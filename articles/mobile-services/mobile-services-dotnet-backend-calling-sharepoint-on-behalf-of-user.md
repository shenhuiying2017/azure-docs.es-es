<properties 
	pageTitle="Acceso a SharePoint en nombre del usuario | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo realizar llamadas a SharePoint en nombre del usuario" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="mahender"/>

# Acceso a SharePoint en nombre del usuario

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>En este tema se muestra cómo tener acceso a las API de SharePoint en nombre del usuario que tiene la sesión actualmente iniciada.</p>
<p>Si prefiere ver un vídeo, el clip que aparece a la derecha muestra los mismos pasos que este tutorial. En el vídeo, Mat Velloso le conduce a través de la actualización de una aplicación de la Tienda Windows para interactuar con SharePoint Online.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Azure-Mobile-Services-AAD-O365-Authentication-identity-across-services" target="_blank" class="label">Ver el tutorial</a> <a style="background-image: url('http://media.ch9.ms/ch9/f217/3f8cbf94-f36b-4162-b3da-1c00339ff217/AzureMobileServicesAADO365AuthenticationIdentityA_960.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Azure-Mobile-Services-AAD-O365-Authentication-identity-across-services" target="_blank" class="dev-onpage-video"><span class="icon"> (en inglés) Reproducir vídeo</span></a> (en inglés) <span class="time">12:51</span></div>
</div>

En este tutorial, actualizará la aplicación a partir del tutorial Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory para crear un documento de Word en SharePoint Online cuando se agrega un nuevo TodoItem.

Este tutorial le guía través de estos pasos básicos para habilitar el acceso en nombre de otro a SharePoint:

1. [Registro de su aplicación para acceso delegado a SharePoint]
2. [Incorporación de información de SharePoint a su servicio móvil]
3. [Obtención de un token de acceso y llamada a la API de SharePoint]
4. [Creación y carga de un documento de Word]
5. [Prueba de la aplicación]

Este tutorial requiere lo siguiente:

* Visual Studio 2013 en Windows 8.1
* Una suscripción activa a [SharePoint Online]
* Realización del tutorial [Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory]. Debería utilizar el inquilino que le suministra su suscripción a SharePoint.

## <a name="configure-permissions"></a>Configuración de la aplicación para acceso delegado a SharePoint
De manera predeterminada, el token que recibe de AAD tiene permisos limitados. Para acceder a una aplicación SaaS o a un recurso de terceros, como SharePoint Online, debe permitirlo explícitamente.

1. En la sección **Active Directory** del [Portal de administración de Azure], seleccione su inquilino. Vaya a la aplicación web que creó para el servicio móvil.

    ![][0]

2. En la pestaña **Configurar**, desplácese hacia la parte inferior de la página hasta llegar a la sección de permisos para otras aplicaciones. Seleccione **Office 365 SharePoint Online** y conceda el permiso delegado **Editar o eliminar archivos de usuarios**. A continuación, haga clic en **Guardar**.

    ![][1]

Ahora ha configurado AAD para emitir un token de acceso a SharePoint para el servicio móvil.

## <a name="store-credentials"></a>Incorporación de información de SharePoint a su servicio móvil

A fin de realizar una llamada a SharePoint, tiene que especificar los extremos a los que tiene que hablar el servicio móvil. También tiene que probar la identidad del servicio móvil. Para ello, se utiliza un par de identificador de cliente y secreto de cliente. Ya ha obtenido y almacenado el identificador de cliente para el servicio móvil durante la configuración del inicio de sesión de AAD. Dado que son credenciales que distinguen mayúsculas de minúsculas, no debe almacenarlas como texto no cifrado en nuestro código, sino que debe configurar estos valores como Configuración de la aplicación para nuestro servicio móvil.

1. Vuelva a la pestaña Aplicaciones de AAD para su inquilino y seleccione la aplicación web para su servicio móvil.

2. En Configurar, desplácese hacia abajo hasta llegar a Claves. Obtendrá un Secreto de cliente generando una clave nueva. Tenga en cuenta que una vez que cree una clave y abandone la página, no hay manera de volver a sacarla del portal. En el momento de la creación debe copiar y guardar este valor en una ubicación segura. Seleccione una duración para la clave y, a continuación, haga clic en guardar y copie el valor resultante.

    ![][2]

3. En la sección Servicios móviles del Portal de administración, vaya a la pestaña Configurar y desplácese hacia abajo hasta Configuración de la aplicación. Aquí puede suministrar un par clave-valor que le ayudará a hacer referencia a las credenciales necesarias.

    ![][3]

4. Configure SP_Authority para que sea el extremo de autoridad de su inquilino de AAD. Debería ser igual que el valor de autoridad utilizado para su aplicación cliente. Tendrá el formato https://login.windows.net/contoso.onmicrosoft.com

5. Configure SP_ClientSecret para que sea el valor del secreto de cliente que obtuvo anteriormente.

6. Configure SP_SharePointURL para que sea la dirección URL de su sitio de SharePoint. Debe tener el formato https://contoso-my.sharepoint.com

Podrá obtener estos valores de nuevo en nuestro código utilizando ApiServices.Settings.

## <a name="obtain-token"></a>Obtención de un token de acceso y llamada a la API de SharePoint

A fin de tener acceso a SharePoint, necesita un token de acceso especial con SharePoint como audiencia de destino. Para obtener este token, tiene que llamar de nuevo a AAD con la identidad del servicio móvil y el token que se emitió para el usuario.

1. Abra su proyecto de back-end de Servicios móviles en Visual Studio.

[AZURE.INCLUDE [mobile-services-dotnet-adal-install-nuget](../../includes/mobile-services-dotnet-adal-install-nuget.md)]

2. En el proyecto de backend de Servicios móviles, cree una clase llamada SharePointUploadContext. En ella, agregue lo siguiente:

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

    ![][4]


<!-- Images. -->

[0]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-web-application.png
[1]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-sharepoint-permissions.png
[2]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/aad-manage-secret-key.png
[3]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/mobile-services-app-settings-sharepoint.png
[4]: ./media/mobile-services-dotnet-backend-calling-sharepoint-on-behalf-of-user/sharepoint-document-created.png

<!-- Anchors. -->

[Registro de su aplicación para acceso delegado a SharePoint]: #configure-permissionss
[Incorporación de información de SharePoint a su servicio móvil]: #store-credentials
[Obtención de un token de acceso y llamada a la API de SharePoint]: #obtain-token
[Creación y carga de un documento de Word]: #create-document
[Prueba de la aplicación]: #test-application

<!-- URLs. -->
[Portal de administración de Azure]: https://manage.windowsazure.com/
[SharePoint Online]: http://office.microsoft.com/sharepoint/
[Autenticación de la aplicación con el inicio de sesión único de la biblioteca de autenticación de Active Directory]: http://azure.microsoft.com/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/
 

<!---HONumber=July15_HO4-->