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

En este tutorial, se muestra cómo usar las características de autenticación y autorización del Servicio de aplicaciones de Azure para proteger una aplicación de API y cómo consumir una aplicación de API protegida en nombre de una cuenta de servicio. La cuenta de servicio se denomina también *entidad de servicio* y la autenticación que usa una cuenta de este tipo se conoce además como escenario *entre servicios*. En este tutorial, va a proteger una aplicación de API para un escenario entre servicios usando Azure Active Directory para la autenticación y consumiendo la API desde un cliente .NET.

El tutorial usa ASP.NET Web API tanto para el cliente que realiza la llamada como para la API a la que se llama, pero las técnicas mostradas también se aplican a otros lenguajes y marcos admitidos por el Servicio de aplicaciones de Azure. El código de cliente que se muestra aquí es código estándar de Azure Active Directory para obtener y pasar un token de portador para una cuenta de servicio. No se necesita ningún código especial solo para Azure, como el que se solía usar para controlar el token de Zumo de Servicios móviles.

Esta es la cuarta parte de una serie de tutoriales que muestran cómo trabajar con aplicaciones de API en el Servicio de aplicaciones de Azure. Para más información acerca de la serie, consulte el primer tutorial [Introducción a Aplicaciones de API y ASP.NET en el Servicio de aplicaciones de Azure](app-service-api-dotnet-get-started.md). Para más información sobre la autenticación y la autorización en el Servicio de aplicaciones de Azure, consulte el anterior tutorial de la serie, [Autenticación de usuario para aplicaciones de API en el Servicio de aplicaciones de Azure](app-service-api-dotnet-user-principal-auth.md).

## Otras opciones para la autenticación entre servicios

Si desea controlar un escenario entre servicios sin usar la autenticación y la autorización del Servicio de aplicaciones, como por ejemplo, usando certificados de cliente, consulte la sección [Pasos siguientes](#next-steps).

## Proyecto de ejemplo CompanyUsers.API

En este tutorial, usará los proyectos de ejemplo que descargó en el [primer tutorial de esta serie](app-service-api-dotnet-get-started.md), así como los recursos de Azure (aplicación de API y aplicación web) que creó en los anteriores tutoriales.

El proyecto CompanyUsers.API es un proyecto de API web simple que contiene un método Get que devuelve una lista de contactos codificada de forma rígida. Para mostrar un escenario entre servicios, el método Get en ContactsList.API llama al método Get de CompanyContacts.API, agrega los contactos que obtiene a lo que ya esté presente en su propio almacén de datos y después devuelve la lista combinada.

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

El objeto de cliente para CompanyContacts.API es una modificación del código de cliente de aplicación de API generado que agrega un token a la solicitud HTTP.

		private static CompanyContactsAPI CompanyContactsAPIClientWithAuth()
		{
		    var client = new CompanyContactsAPI(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		    client.HttpClient.DefaultRequestHeaders.Authorization =
		        new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		    return client;
		}

## Creación de una aplicación de API en Azure e implementación del proyecto CompanyContacts.API en ella

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto CompanyContacts.API y después haga clic en **Publicar**.

3.  En el paso **Perfil** del asistente **Publicación web**, haga clic en **Servicio de aplicaciones de Microsoft Azure**.

	![](./media/app-service-api-dotnet-service-principal-auth/selectappservice.png)

4. Inicie sesión en su cuenta de Azure si aún no lo hizo o actualice sus credenciales si expiraron.

4. En el cuadro de diálogo Servicio de aplicaciones, elija la **suscripción** de Azure que desee usar y después haga clic en **Nuevo**.

	![](./media/app-service-api-dotnet-service-principal-auth/clicknew.png)

3. En la pestaña **Hospedaje** del cuadro de diálogo **Crear servicio de aplicaciones**, haga clic en **Cambiar tipo** y después en **Aplicación de API**.

4. En **Nombre de aplicación de API**, escriba un nombre que sea único en el dominio *azurewebsites.net*.

6. En el menú desplegable **Grupo de recursos**, seleccione el grupo de recursos que haya usado en estos tutoriales.

4. En el menú desplegable **Plan del servicio de aplicaciones**, seleccione el plan que haya usado en estos tutoriales.

7. Haga clic en **Crear**.

	![](./media/app-service-api-dotnet-service-principal-auth/createappservice.png)

	Visual Studio crea la aplicación de API y un perfil de publicación que tiene toda la configuración necesaria para la nueva aplicación de API.

8. En la pestaña **Conexión** del asistente **Publicación web**, haga clic en **Publicar**.

	![](./media/app-service-api-dotnet-service-principal-auth/conntab.png)

	Visual Studio implementa el proyecto en la nueva aplicación de API y abre un explorador con la dirección URL de la aplicación de API. Aparece una página que indica que "se creó correctamente".

9. Cierre el explorador.

## Actualización del código de cliente generado en el proyecto ContactsList.API

El proyecto ContactsList.API ya tiene el código de cliente generado, pero podrá eliminarlo y volver a generarlo desde su propia aplicación de API.

1. En el **Explorador de soluciones** de Visual Studio, en el proyecto ContactsList.API, elimine la carpeta *CompanyContactsAPI*.

2. Haga clic con el botón derecho en el proyecto ContactsList.API y después haga clic en **Agregar > Cliente de API de REST**.

3. En el cuadro de diálogo **Agregar cliente de API de REST**, haga clic en **Descargar desde la aplicación de API de Microsoft Azure** y después en **Examinar**.

8. En el cuadro de diálogo **Servicio de aplicaciones**, expanda el grupo de recursos que use para este tutorial y después seleccione la aplicación de API que acaba de crear.

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
 
4. En el método Get, quite la marca de comentario del bloque de código que usa el objeto de cliente devuelto por `CompanyContactsAPIClientWithAuth`.

		using (var client = CompanyContactsAPIClientWithAuth())
		{
		    var results = await client.Contacts.GetAsync();
		    foreach (Contact c in results)
		    {
		        contactsList.Add(c);
		    }
		}

2. Haga clic con el botón derecho en el proyecto ContactsList.API y después haga clic en **Publicar**.

	Se abre el asistente **Publicación web** con el perfil de publicación que usó antes.

3. En el Asistente para **publicación web**, haga clic en **Publicar**.

## Configuración de la autenticación y la autorización en Azure para la nueva aplicación de API

1. En el [Portal de Azure](https://portal.azure.com/), vaya a la hoja Aplicación de API correspondiente a la aplicación de API que creó en este tutorial para el proyecto CompanyContacts.API y después haga clic en **Configuración**.

2. Busque la sección **Características** y después haga clic en **Autenticación y autorización**.

3. En la hoja **Autenticación y autorización**, haga clic en **Activado**.

4. En la lista desplegable **Acción por realizar cuando no se autentique la solicitud**, seleccione **Iniciar sesión con Azure Active Directory**.

5. En **Proveedores de autenticación**, haga clic en **Azure Active Directory**.

6. En la hoja **Configuración de Azure Active Directory**, haga clic en **Rápida**.

	Azure creará automáticamente una aplicación AAD en su inquilino de AAD. Anote el nombre de la nueva aplicación AAD, pues lo seleccionará más adelante en el Portal de Azure clásico para obtener su identificador de cliente.

7. Haga clic en **Aceptar**.

10. En la hoja **Autenticación y autorización**, haga clic en **Guardar**.
 
11. En el [Portal de Azure clásico](https://manage.windowsazure.com/), vaya a **Azure Active Directory**.

12. En la pestaña **Directorio**, haga clic en su inquilino de AAD.

14. Haga clic en **Aplicaciones > Aplicación propiedad de mi compañía** y después haga clic en la marca de verificación.

15. En la lista de aplicaciones, haga clic en el nombre de aquella que Azure creó automáticamente cuando habilitó la autenticación para la aplicación de API.

16. Haga clic en **Configurar**.

15. En la parte inferior de la página, haga clic en **Administrar manifiesto > Descargar manifiesto** y guarde el archivo en una ubicación donde se pueda editar.

16. En el archivo de manifiesto descargado, busque la propiedad `oauth2AllowImplicitFlow`. Cambie el valor de esta propiedad de `false` a `true` y después guarde el archivo.

16. Haga clic en **Administrar manifiesto > Cargar manifiesto** y cargue el archivo actualizado en el paso anterior.

17. Mantenga esta página abierta para poder copiar y pegar valores desde ella y actualizar los valores de la página en pasos posteriores del tutorial.

## Actualización de la configuración de la aplicación de API que ejecuta el código del proyecto ContactsList.API

1. En el [Portal de Azure](https://portal.azure.com/), vaya a la hoja Aplicación de API correspondiente a la aplicación de API en la que implementó el proyecto ContactsList.API. Se trata de la aplicación de API que llama y no a la que se llama, que acaba de crear en este tutorial.

2. Haga clic en **Configuración > Configuración de la aplicación**.

	Va a agregar algunos valores de configuración aquí, pero tiene que obtenerlos de otra página del Portal de Azure clásico.

3. En el [Portal de Azure clásico](https://manage.windowsazure.com/), vaya a la pestaña **Configurar** para la aplicación AAD que creó para la aplicación de API de ContactsList.API.

5. En **Claves**, seleccione **1 año** en la lista desplegable **Seleccionar duración**.

6. Haga clic en **Guardar**.

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)


7. Copie el valor de la clave.

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

3. En la hoja **Configuración de la aplicación** del Portal de Azure, en la sección **Configuración de aplicación**, agregue una clave denominada ida:ClientSecret y pegue la clave que acaba de crear en el campo de valor.

3. En el Portal de Azure clásico, vaya a la pestaña **Configurar** para la aplicación AAD que creó para la aplicación de API de CompanyContacts.API.

4. Copie el Id. de cliente.

3. En la hoja **Configuración de la aplicación** del Portal de Azure, en la sección **Configuración de aplicación**, agregue una clave denominada ida:Resource y pegue el identificador de cliente que acaba de copiar en el campo de valor.

4. Agregue una clave denominada CompanyContactsAPIUrl y, en el campo de valor, escriba https://{your nombre de la aplicación de API}.azurewebsites.net, por ejemplo: https://companycontactsapi.azurewebsites.net.

6. Haga clic en Guardar.

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

## Prueba en Azure

1. Vaya a la dirección URL de la aplicación web en la que implementó el proyecto ContactsList.Angular.AAD.

2. Haga clic en la pestaña **Contactos** y después inicie sesión.

	Verá la página de contactos con los contactos adicionales que se recuperaron de la aplicación de API de CompanyContacts.API.

	![](./media/app-service-api-dotnet-service-principal-auth/contactspagewithdavolio.png)

## Pasos siguientes

Este es el último tutorial de la serie de introducción a Aplicaciones de API. En esta sección se ofrecen más sugerencias para aprender a usar las aplicaciones de API.

* Otras formas de consumir una aplicación de API protegida con la autorización y la autenticación del Servicio de aplicaciones de Azure

	En este artículo, se muestra cómo proteger una aplicación de API y cómo llamarla desde código que se ejecuta en otra aplicación de API. Para información sobre cómo llamar a una aplicación de API protegida desde una aplicación lógica, consulte [Uso de la API personalizada hospedada en Servicio de aplicaciones con aplicaciones lógicas](../app-service-logic/app-service-logic-custom-hosted-api.md).

* Otras formas de proteger una aplicación de API para los escenarios entre servicios

	Como alternativa a la autorización y la autenticación del Servicio de aplicaciones, puede proteger una aplicación de API con certificados de cliente o la autenticación básica. Para información acerca de los certificados de cliente en Azure, consulte [Configuración de la autenticación mutua de TLS para una aplicación web](../app-service-web/app-service-web-configure-tls-mutual-auth.md).

* Otras maneras de implementar una aplicación del Servicio de aplicaciones

	Para información sobre otras formas de implementar proyectos web en aplicaciones web, ya sea con Visual Studio o con la [automatización de la implementación](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) desde un [sistema de control de código fuente](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), consulte [Documentación de implementación del Servicio de aplicaciones de Azure](web-sites-deploy.md).

	Visual Studio también puede generar scripts de Windows PowerShell que puede usar para automatizar la implementación. Para obtener más información, consulte [Automatizar todo (creación de aplicaciones en la nube para el mundo real con Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* Solución de problemas de una aplicación del Servicio de aplicaciones

	Visual Studio proporciona características que facilitan la visualización de los registros de Azure conforme se generan, en tiempo real. La ejecución también se puede realizar en modo de depuración de manera remota en Azure. Para obtener más información, consulte [Solución de problemas de aplicaciones web de Azure en Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

* Incorporación de un nombre de dominio personalizado y SSL

	Para obtener información acerca de cómo usar SSL y su propio dominio (por ejemplo, www.contoso.com, en lugar de contoso.azurewebsites.net), consulte los siguientes recursos:

	* [Configuración de un nombre de dominio personalizado en el Servicio de aplicaciones de Azure](web-sites-custom-domain-name.md)
	* [Habilitación de HTTPS en un sitio web de Azure](web-sites-configure-ssl-certificate.md)

<!---HONumber=AcomDC_1203_2015-->