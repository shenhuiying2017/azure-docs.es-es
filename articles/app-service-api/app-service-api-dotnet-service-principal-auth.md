<properties
	pageTitle="Autenticación de entidad de servicio para Aplicaciones de API en el Servicio de aplicaciones de Azure | Microsoft Azure"
	description="Aprenda a proteger una aplicación de API en el Servicio de aplicaciones de Azure para escenarios entre servicios."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/28/2015"
	ms.author="tdykstra"/>

# Autenticación de entidad de servicio para Aplicaciones de API en el Servicio de aplicaciones de Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Información general

En este tutorial se muestra cómo usar las características de autenticación y autorización del Servicio de aplicaciones de Azure para proteger una aplicación de API y cómo consumir una aplicación de API protegida en nombre de una cuenta de servicio. El proveedor de autenticación que se muestra en el tutorial es Azure Active Directory, y el cliente y API son ASP.NET Web API que se ejecutan en las aplicaciones de API.

## Autenticación y autorización en el Servicio de aplicaciones

Para ver una introducción sobre las características de autenticación que se usan en este tutorial, consulte el tutorial anterior de la serie, [Autenticación y autorización para Aplicaciones de API en el Servicio de aplicaciones de Azure](app-service-api-authentication.md).

## Seguimiento de este tutorial

En este tutorial se usa una aplicación de ejemplo que descarga y crea en la aplicación de API en el primer tutorial: [Introducción a Aplicaciones de API y ASP.NET en el Servicio de aplicaciones de Azure](app-service-api-dotnet-get-started.md).

## Proyecto de ejemplo CompanyUsers.API

En la [aplicación de ejemplo ContactsList](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list), el proyecto CompanyUsers.API es un proyecto de Web API simple que contiene un método Get que devuelve una lista de contactos codificada de forma rígida. Para mostrar un escenario entre servicios, el método Get en ContactsList.API llama al método Get de CompanyContacts.API, agrega los contactos que se le devuelven a lo que ya está en su propio almacén de datos y después devuelve la lista combinada.

Este es el método Get de CompanyUsers.API.

		public async Task<IEnumerable<Contact>> Get()
		{
		    var contacts = new Contact[]{
		                new Contact { Id = 1, EmailAddress = "nancy@contoso.com", Name = "Nancy Davolio"},
		                new Contact { Id = 2, EmailAddress = "alexander@contoso.com", Name = "Alexander Carson"}
		            };
		
		    return contacts;
		}


Y este es el método Get de ContactsList.API, donde se muestra cómo llama a CompanyContacts.API y agrega los resultados a lo que se devuelve. (Se omite parte del código para que resulte más claro).

		private async Task<IEnumerable<Contact>> GetContacts()
		{
		    var contacts = await _storage.Get(FILENAME);
		
		    var contactsList = contacts.ToList<Contact>();
		    using (var client = CompanyContactsAPIClientWithAuth())
		    {
		        var results = await client.Contacts.GetAsync();
		        foreach (Contact c in results)
		        {
		            contactsList.Add(c);
		        }
		    }
		
		    return contactsList;
		}

El objeto de cliente devuelto por `CompanyContactsAPIClientWithAuth()` en el código anterior se basa en el código de cliente generado, pero agrega un token de autorización a las solicitudes HTTP.

		private static CompanyContactsAPI CompanyContactsAPIClientWithAuth()
		{
		    var client = new CompanyContactsAPI(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		    client.HttpClient.DefaultRequestHeaders.Authorization =
		        new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		    return client;
		}

## Creación de una aplicación de API en Azure e implementación del proyecto CompanyContacts.API en ella

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto CompanyContacts.API y después en **Publicar**.

3.  En el paso **Perfil** del Asistente para **publicación web**, haga clic en **Servicio de aplicaciones de Microsoft Azure**.

	![](./media/app-service-api-dotnet-service-principal-auth/selectappservice.png)

4. Inicie sesión en su cuenta de Azure si aún no lo hizo o actualice sus credenciales si expiraron.

4. En el cuadro de diálogo **Servicio de aplicaciones**, elija la **Suscripción** de Azure que desee usar y haga clic en **Nuevo**.

	![](./media/app-service-api-dotnet-service-principal-auth/clicknew.png)

3. En la pestaña **Hospedaje** del cuadro de diálogo **Crear servicio de aplicaciones**, haga clic en **Cambiar tipo** y después en **Aplicación de API**.

4. En **Nombre de aplicación de API**, escriba un nombre que sea único en el dominio *azurewebsites.net*.

6. En el menú desplegable **Grupo de recursos**, seleccione el grupo de recursos que haya usado en estos tutoriales.

4. En el menú desplegable **Plan de servicio de aplicaciones**, seleccione el plan que haya usado en estos tutoriales.

7. Haga clic en **Crear**.

	![](./media/app-service-api-dotnet-service-principal-auth/createappservice.png)

	Visual Studio crea la aplicación de API y un perfil de publicación que tiene toda la configuración necesaria para la nueva aplicación de API.

8. En la pestaña **Conexión** del Asistente para **publicación web**, haga clic en **Publicar**.

	![](./media/app-service-api-dotnet-service-principal-auth/conntab.png)

	Visual Studio implementa el proyecto en la nueva aplicación de API y abre un explorador con la dirección URL de la aplicación de API. Aparece una página que indica que "se creó correctamente".

9. Cierre el explorador.

## Actualización del código de cliente generado en el proyecto ContactsList.API

El proyecto ContactsList.API ya tiene el código de cliente generado, pero podrá eliminarlo y volver a generarlo desde su propia aplicación de API.

1. En el **Explorador de soluciones** de Visual Studio, en el proyecto ContactsList.API, elimine la carpeta *CompanyContactsAPI*.

2. Haga clic con el botón derecho en el proyecto ContactsList.API y después en **Agregar > Cliente de API de REST**.

3. En el cuadro de diálogo **Agregar cliente de API de REST**, haga clic en **Descargar desde la aplicación de API de Microsoft Azure** y, a continuación, en **Examinar**.

8. En el cuadro de diálogo **Servicio de aplicaciones**, expanda el grupo de recursos que usa para este tutorial y seleccione la aplicación de API que acaba de crear.

	Si no ve la aplicación de API en la lista, es probable que al crear la aplicación de API accidentalmente omitiera el paso que cambiaba el tipo de aplicación web a aplicación de API. En ese caso, puede crear una aplicación de API nueva, para lo que debe repetir los pasos que realizó anteriormente. Tendrá que elegir un nombre diferente para la aplicación de API, a menos que vaya al portal y elimine primero la aplicación web.

10. Haga clic en **Aceptar**.

9. En el cuadro de diálogo **Agregar cliente de API de REST**, haga clic en **Aceptar**.

	Visual Studio crea una carpeta con el nombre de la aplicación de API y genera clases cliente.

## Actualización del código en ContactsList.API e implementación del proyecto

El código de ContactsList.API que llama a CompanyContacts.API se convirtió en comentario para los tutoriales anteriores. En esta sección, quitará la marca de comentario de ese código e implementará la aplicación.

1. En el proyecto ContactsList.API, abra *Controllers/ContactsController.cs*.

2. Casi al principio de la clase `ContactsController`, en el código que usa la clase de cliente generada para agregar un token de autorización, reemplace el nombre de clase `CompanyContactsAPI` por el nombre de la clase generada desde su aplicación de API.

	Por ejemplo, si la aplicación de API se denomina CompanyContactsAPI3, el código será similar al siguiente:

		 private static CompanyContactsAPI3 CompanyContactsAPIClientWithAuth()
		 {
		     var client = new CompanyContactsAPI3(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		     client.HttpClient.DefaultRequestHeaders.Authorization =
		         new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		     return client;
		 }
 
4. En el método `Get`, quite la marca de comentario del bloque de código que llama a CompanyContacts.API.

		using (var client = CompanyContactsAPIClientWithAuth())
		{
		    var results = await client.Contacts.GetAsync();
		    foreach (Contact c in results)
		    {
		        contactsList.Add(c);
		    }
		}

2. Haga clic con el botón derecho en el proyecto ContactsList.API y después haga clic en **Publicar**.

	Se abre el Asistente para **publicación web** con el perfil de publicación que usó antes.

3. En el Asistente para **publicación web**, haga clic en **Publicar**.

	Visual Studio implementa el proyecto y abre una ventana del explorador en la dirección URL base de la aplicación de API. Cierre esta ventana del explorador.

## Configuración de la autenticación y la autorización en Azure para la nueva aplicación de API

1. En el [Portal de Azure](https://portal.azure.com/), vaya a la hoja Aplicación de API correspondiente a la aplicación de API que creó en este tutorial para el proyecto CompanyContacts.API y después haga clic en **Configuración**.

2. Busque la sección **Características** y después haga clic en **Autenticación/autorización**.

3. En la hoja **Autenticación/autorización**, haga clic en **Activado**.

4. En la lista desplegable **Acción por realizar cuando no se autentique la solicitud**, seleccione **Iniciar sesión con Azure Active Directory**.

5. En **Proveedores de autenticación**, haga clic en **Azure Active Directory**.

6. En la hoja **Configuración de Azure Active Directory**, haga clic en **Rápida**.

	Azure creará automáticamente una aplicación AAD en su inquilino de AAD. Anote el nombre de la nueva aplicación AAD, pues lo seleccionará más adelante en el Portal de Azure clásico para obtener su identificador de cliente.

7. Haga clic en **Aceptar**.

10. En la hoja **Autenticación/autorización**, haga clic en **Guardar**.
 
11. En el [Portal de Azure clásico](https://manage.windowsazure.com/), vaya a **Azure Active Directory**.

12. En la pestaña **Directorio**, haga clic en su inquilino de AAD.

14. Haga clic en **Aplicaciones > Aplicación propiedad de mi compañía** y después haga clic en la marca de verificación.

15. En la lista de aplicaciones, haga clic en el nombre de aquella que Azure creó automáticamente cuando habilitó la autenticación para la aplicación de API.

16. Haga clic en **Configurar**.

17. Mantenga esta página abierta para poder copiar y pegar valores desde ella y actualizar los valores de la página en pasos posteriores del tutorial.

## Actualización de la configuración de la aplicación de API que ejecuta el código del proyecto ContactsList.API

3. En otra ventana del explorador, vaya al [Portal de Azure clásico](https://manage.windowsazure.com/), vaya a la pestaña **Configurar** para la aplicación AAD que creó para la aplicación de API ContactsList.API.

5. En **Claves**, seleccione **1 año** en la lista desplegable **Seleccionar duración**.

6. Haga clic en **Guardar**.

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. Copie el valor de la clave.

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

1. En otra ventana del explorador, vaya al [Portal de Azure](https://portal.azure.com/) y, después, a la hoja Aplicación de API correspondiente a la aplicación de API en la que implementó el proyecto ContactsList.API. (Esta es la aplicación de API que realiza la llamada, no a la que se llama: ContactsList.API, no CompanyContacts.API).

2. Haga clic en **Configuración > Configuración de la aplicación**.

3. En la sección **Configuración de aplicación**, agregue una clave denominada ida:ClientSecret y pegue la clave que acaba de crear en el campo de valor.

3. Agregue una clave llamada "ida:ClientId" y, en el campo de valor, pegue el identificador de cliente desde la misma página **Configurar** de AAD.

4. Agregue una clave llamada "ida:Authority" y, en el campo de valor, escriba "https://login.windows.net/{suInquilino}"; por ejemplo, "https://login.windows.net/contoso.onmicrosoft.com".

3. En el Portal de Azure clásico, vaya a la pestaña **Configurar** para la aplicación AAD que creó para la aplicación de API CompanyContacts.API.

4. Copie el Id. de cliente.

3. En la hoja **Configuración de la aplicación** del Portal de Azure, sección **Configuración de aplicación**, agregue una clave denominada ida:Resource y, en el campo de valor, pegue el identificador de cliente que acaba de copiar.

4. Agregue una clave denominada "CompanyContactsAPIUrl" y, en el campo de valor, escriba "https://{nombreDeSuAplicaciónApi}.azurewebsites.net"; por ejemplo, "https://companycontactsapi.azurewebsites.net."

6. Haga clic en Guardar.

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

## Prueba en Azure

1. Vaya a la dirección URL de la aplicación web en la que implementó el proyecto ContactsList.Angular.AAD.

2. Haga clic en la pestaña **Contactos** y después inicie sesión.

	Verá la página de contactos con los contactos adicionales que se recuperaron de la aplicación de API de CompanyContacts.API.

	![](./media/app-service-api-dotnet-service-principal-auth/contactspagewithdavolio.png)

Igual que en el tutorial anterior, también puede configurar los proyectos de Visual Studio con las direcciones URL de SSL de localhost y ejecutar la aplicación localmente. En ese caso, puede almacenar en el archivo Web.config la configuración que almacenó en Azure para que se ejecute en Azure (identificador del cliente, secreto del cliente, etc.). Sin embargo, tenga cuidado de no insertar en el repositorio para el control de código fuente un archivo Web.config que contenga información confidencial, como el secreto del cliente. Para más información, consulte el artículo [Best practices for deploying passwords and other sensitive data to ASP.NET and Azure App Service](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

## Protección de la aplicación de API contra el acceso desde el explorador

En este tutorial, utilizó la opción Rápida del portal de Azure para configurar la autenticación de AAD para la aplicación de API a la que desea acceder mediante la autenticación de entidad de servicio. De forma predeterminada, el Servicio de aplicaciones configura la nueva aplicación AAD de manera que permite a un usuario ir a la dirección URL de la aplicación de API en un explorador e iniciar sesión. Esto significa no solo una entidad de servicio puede tener acceso a la API, también un usuario final. Si solo desea que una entidad de servicio tenga acceso a la API, puede impedir el acceso desde el explorador; para ello, cambie la **URL de respuesta** en la aplicación AAD para que sea diferente de la dirección URL base de la aplicación de API.

### Comprobación de que el acceso al explorador funciona

1. En una nueva ventana del explorador, vaya a la dirección URL HTTPS de la aplicación de API que creó para el proyecto CompanyContacts.API.

	El explorador irá a una pantalla de inicio de sesión.
	
2. Inicie sesión con las credenciales de un usuario en su inquilino de AAD.

3. El explorador muestra la pantalla de la aplicación de API "creado correctamente".

	Si se ha habilitado la interfaz de usuario de Swagger, podrá ir a la dirección URL `/swagger` y llamar a la API. Para llamar a la API desde el explorador, agregue `/api/contacts/get` a la dirección URL.

### Deshabilitación del acceso desde el explorador

1. En la pestaña **Configurar** del portal clásico para la aplicación AAD que se creó para el proyecto CompanyContacts.API, modifique el valor del campo **URL de respuesta** para que sea una dirección URL válida, pero no la URL de la aplicación de API.
 
2. Haga clic en **Guardar**.

### Comprobación de que el acceso desde el explorador ya no funciona

1. En una nueva ventana del explorador, vaya de nuevo a la dirección URL de la aplicación de API.

2. Inicie la sesión cuando se le solicite.

3. El inicio de sesión se realiza correctamente, pero genera una página de error.

	Aún puede tener acceso a la aplicación de API con un token de entidad de servicio, pero los usuarios en el inquilino de AAD no pueden iniciar sesión y acceder a la API desde un explorador.

## Pasos siguientes

Este es el último tutorial de la serie de introducción a Aplicaciones de API. En esta sección se ofrecen más sugerencias para aprender a trabajar con las aplicaciones de API.

* Otras maneras de implementar una aplicación del Servicio de aplicaciones

	Para más información sobre otras formas de implementar proyectos web en aplicaciones web, ya sea con Visual Studio o mediante la [automatización de la implementación](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) desde un [sistema de control de código fuente](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), consulte [Documentación de implementación del Servicio de aplicaciones de Azure](web-sites-deploy.md).

	Visual Studio también puede generar scripts de Windows PowerShell que puede usar para automatizar la implementación. Para obtener más información, consulte [Automatizar todo (creación de aplicaciones en la nube para el mundo real con Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* Solución de problemas de una aplicación del Servicio de aplicaciones

	Visual Studio proporciona características que facilitan la visualización de los registros de Azure conforme se generan, en tiempo real. La ejecución también se puede realizar en modo de depuración de manera remota en Azure. Para obtener más información, consulte [Solución de problemas de aplicaciones web de Azure en Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

* Incorporación de un nombre de dominio personalizado y SSL

	Para obtener información acerca de cómo usar SSL y su propio dominio (por ejemplo, www.contoso.com, en lugar de contoso.azurewebsites.net), consulte los siguientes recursos:

	* [Configuración de un nombre de dominio personalizado en el Servicio de aplicaciones de Azure](web-sites-custom-domain-name.md)
	* [Habilitación de HTTPS en un sitio web de Azure](web-sites-configure-ssl-certificate.md)

<!---HONumber=AcomDC_1217_2015-->