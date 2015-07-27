<properties 
	pageTitle="Implementación de una aplicación de API en el Servicio de aplicaciones de Azure" 
	description="Aprenda a implementar un proyecto de aplicación de API en su suscripción de Azure." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="bradyg;tarcher"/>

# Implementación de una aplicación de API en el Servicio de aplicaciones de Azure 

## Información general

En este tutorial, implementará el proyecto Web API que creó en el [tutorial anterior](app-service-dotnet-create-api-app.md) en una nueva [aplicación de API](app-service-api-apps-why-best-platform.md). Usará Visual Studio para crear el recurso de aplicación de API en el [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md) e implementará su código Web API a la aplicación de API de Azure.

### Otras opciones de implementación

Existen muchas otras formas de implementar aplicaciones de API. Una aplicación de API es una [aplicación web](../app-service-web/app-service-web-overview.md) con características adicionales para hospedar servicios web, y todos los [métodos de implementación que están disponibles para aplicaciones web](../app-service-web/web-sites-deploy.md) también pueden usarse con aplicaciones de API. La aplicación web que hospeda una aplicación de API se denomina host de aplicación de API en el Portal de vista previa de Azure y puede configurar la implementación mediante la hoja del portal de host de aplicación de API. Para obtener información sobre la hoja del host de aplicación de API, consulte [Administración de una aplicación de API](app-service-api-manage-in-portal.md).

El hecho de que las aplicaciones de API se basen en aplicaciones web también significa que puede implementar el código escrito para plataformas que no sean ASP.NET en las aplicaciones de API. Para obtener un ejemplo que usa Git para implementar código de Node.js en una aplicación de API, consulte [Creación de una aplicación de API Node.js en el Servicio de aplicaciones de Azure](app-service-api-nodejs-api-app.md).
 
## <a id="provision"></a>Creación de la aplicación de API en Azure 

En esta sección, use el asistente **Publicación web** de Visual Studio para crear una nueva aplicación de API en Azure. Cuando las instrucciones indican que escriba un nombre para la aplicación de API, escriba *ContactsList*.

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## <a id="deploy"></a>Implemente el código en la nueva aplicación de API

Use el mismo asistente para **Publicación web** para implementar el código en la nueva aplicación de API.

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Visualización de aplicaciones en el Portal de vista previa de Azure

En esta sección, verá la configuración básica disponible para las aplicaciones de API en el portal y realizará cambios iterativos en la aplicación de API. Con cada implementación, el portal refleja los cambios que está realizando en la aplicación de API.

1. En el [Portal de vista previa de Azure](https://portal.azure.com), vaya a la hoja **Aplicación de API** de la aplicación de API que ha implementado.

4. Haga clic en **Definición de API**.
 
	La hoja **Definición de API** de la aplicación muestra la lista de operaciones de API que definió al crear la aplicación.

	![Definición de la API](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

5. Ahora, vuelva al proyecto de Visual Studio y agregue el código siguiente al archivo **ContactsController.cs**.

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![Incorporación del método Post al controlador](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

	Este código agrega un método **Post** que se puede usar para registrar nuevas instancias de `Contact` en la API.

6. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y, a continuación, seleccione **Publicar**.

	![Menú contextual de publicación del proyecto](./media/app-service-dotnet-deploy-api-app/31-publish-gesture-v3.png)

7. Haga clic en la pestaña **Configuración**.

8. Desde la lista desplegable **Configuración**, seleccione **Depurar**.

	![Configuración de Publicación web](./media/app-service-dotnet-deploy-api-app/36.5-select-debug-option-v3.png)

9. Haga clic en la pestaña **Vista previa**.

10. Haga clic en **Iniciar vista previa** para ver los cambios que se realizarán.

	![Cuadro de diálogo Publicación web](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

11. Haga clic en **Publicar**.

12. Cuando haya completado el proceso de publicación, vuelva al portal y cierre y vuelva a abrir la hoja **Definición de API**. Verá el nuevo extremo de API que acaba de crear e implementar directamente en su suscripción de Azure.

	![Definición de la API](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## Pasos siguientes

Ha visto cómo las capacidades de implementación directa en Visual Studio facilitan la iteración y la implementación rápida y prueban que la API funcione correctamente. En el [siguiente tutorial](../app-service-dotnet-remotely-debug-api-app.md), aprenderá a depurar la aplicación de API mientras se ejecuta en Azure.
 

<!---HONumber=July15_HO3-->