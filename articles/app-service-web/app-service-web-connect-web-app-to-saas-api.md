<properties 
	pageTitle="Conexión de una aplicación web en una aplicación de API en Servicios de aplicaciones de Azure" 
	description="En este tutorial se muestra cómo consumir una aplicación de API desde una aplicación web ASP.NET hospedada en Servicio de aplicaciones de Azure." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="syntaxc4" 
	manager="yochayk" 
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na" 
	ms.date="09/15/2015"
	ms.author="cfowler"/>

# Conexión de una aplicación web en una aplicación de API en Servicios de aplicaciones de Azure

En este tutorial se muestra cómo consumir una aplicación de API desde una aplicación web ASP.NET hospedada en [Servicio de aplicaciones](../app-service.md).

## Requisitos previos

Este tutorial se basa en las series de tutoriales de aplicaciones de API:

1. [Creación de una aplicación de API de Azure](../app-service-dotnet-create-api-app)
3. [Implementación de una aplicación de API de Azure](../app-service-dotnet-deploy-api-app)
4. [Depuración de una aplicación de API de Azure](../app-service-dotnet-remotely-debug-api-app)

## Hacer que la aplicación de API sea de acceso público

En el [Portal de vista previa de Azure](http://go.microsoft.com/fwlink/?LinkId=529715), seleccione la aplicación de API. Haga clic en el botón **Configuración** de la barra de comandos. En la hoja **Configuración de la aplicación**, cambie el valor de **Nivel de acceso** a **Público (anónimo)**.

![](./media/app-service-web-connect-web-app-to-saas-api/4-5-Change-Access-Level-To-Public.png)

## Creación de una aplicación de ASP.NET MVC en Visual Studio

1. Abra Visual Studio. Use el cuadro de diálogo **Nuevo proyecto** para agregar una nueva **Aplicación web ASP.NET**. Haga clic en **Aceptar**.

	![Archivo > Nuevo > Web > Aplicación web ASP.NET](./media/app-service-web-connect-web-app-to-saas-api/1-Create-New-MVC-App-For-Consumption.png)

1. Seleccione la plantilla **MVC**. Haga clic en **Cambiar autenticación** y, a continuación, seleccione **Sin autenticación** y haga doble clic en **Aceptar**.

	![Nueva aplicación ASP.NET](./media/app-service-web-connect-web-app-to-saas-api/2-Change-Auth-To-No-Auth.png)

1. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto de aplicación web recién creado y seleccione **Agregar referencia de aplicación de Azure**.

	![Adición de la referencia de aplicación de API de Azure...](./media/app-service-web-connect-web-app-to-saas-api/3-Add-Azure-API-App-SDK.png)

1. En la lista desplegable **Aplicaciones de API existentes**, seleccione la aplicación de API a la que desea conectarse.

	![Selección de la aplicación de API existente](./media/app-service-web-connect-web-app-to-saas-api/4-Add-Azure-API-App-SDK-Dialog.png)

	>[AZURE.NOTE]El código de cliente para conectarse a la aplicación de API se generará automáticamente a partir de un extremo de API de Swagger.

1. Para aprovechar el código de API generado, abra el archivo HomeController.cs y reemplace la acción `Contact` por lo siguiente:

	    public async Task<ActionResult> Contact()
	    {
	        ViewBag.Message = "Your contact page.";
	
	        var contacts = new ContactsList();
	        var response = await contacts.Contacts.GetAsync();
	        var contactList = response.Body;
	
	        return View(contactList);
	    }

	![Actualizaciones de código de HomeController.cs](./media/app-service-web-connect-web-app-to-saas-api/5-Write-Code-Which-Leverages-Swagger-Generated-Code.png)

1. Actualice la vista `Contact` para que refleje la lista dinámica de contactos con el código siguiente:
	<pre>// Add to the very top of the view file
@model IList&lt;MyContactsList.Web.Models.Contact>

// Replace the default email addresses with the following
&lt;h3>Public Contacts&lt;/h3>
&lt;ul>
    @foreach (var contact in Model)
    {
        &lt;li>&lt;a href="mailto:@contact.EmailAddress">@contact.Name &amp;lt;@contact.EmailAddress&amp;gt;&lt;/a>&lt;/li>
    }
&lt;/ul> 
</pre>![Actualizaciones de código de Contact.cshtml](./media/app-service-web-connect-web-app-to-saas-api/6-Update-View-To-Reflect-Changes.png)

## Implementación de la aplicación web en Aplicaciones web del Servicio de aplicaciones

Siga las instrucciones disponibles en [Implementación de una aplicación web de Azure](web-sites-deploy.md).

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=Oct15_HO4-->