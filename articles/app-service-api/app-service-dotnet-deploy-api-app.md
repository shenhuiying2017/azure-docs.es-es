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
 
## Implementación de aplicaciones de API 

En esta sección, verá los pasos necesarios para implementar una aplicación de API en una suscripción de Azure.

1. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto (no la solución) y en **Publicar...**. 

	![Opción de menú de publicación de proyecto](./media/app-service-dotnet-deploy-api-app/20-publish-gesture-v3.png)

2. Haga clic en la pestaña **Perfil** y en **Aplicaciones de API de Microsoft Azure (vista previa)**.

	![Cuadro de diálogo Publicación web](./media/app-service-dotnet-deploy-api-app/21-select-api-apps-for-deployment-v2.png)

3. Haga clic en **Nuevo** para aprovisionar una nueva aplicación de API en su suscripción de Azure.

	![Selección del cuadro de diálogo Servicios de API existentes](./media/app-service-dotnet-deploy-api-app/23-publish-to-apiapps-v3.png)

4. En el cuadro de diálogo **Crear una aplicación de API**, escriba lo siguiente:

	- En **Nombre de la aplicación de API**, escriba ContactsList. 
	- Si tiene varias suscripciones de Azure, seleccione la que desee usar.
	- En el **Plan del Servicio de aplicaciones**, seleccione de entre los planes del Servicio de aplicaciones o elija **Crear nuevo plan del Servicio de aplicaciones** y escriba el nombre de un nuevo plan. 
	- En **Grupo de recursos**, seleccione de entre los grupos de recursos existentes o elija **Crear nuevo grupo de recursos** y escriba un nombre. El nombre debe ser único; considere la posibilidad de usar el nombre de la aplicación como un prefijo y anexe información personal como el identificador de Microsoft (sin el signo @).  
	- En **Nivel de acceso**, seleccione **Disponible para cualquier persona**. Con esta opción, la API será totalmente pública, lo cual está bien para este tutorial. Puede restringir el acceso más adelante a través del Portal de vista previa de Azure.
	- En **Región**, seleccione una región cercana.  

	![Configuración del cuadro de diálogo de Aplicación web de Microsoft Azure](./media/app-service-dotnet-deploy-api-app/24-new-api-app-dialog-v3.png)

5. Haga clic en **Aceptar** para crear la aplicación de API en su suscripción. Como este proceso puede tardar unos minutos, Visual Studio muestra un cuadro de diálogo de confirmación.

	![Mensaje de confirmación de inicio de creación de Servicio de API](./media/app-service-dotnet-deploy-api-app/25-api-provisioning-started-v3.png)

6. Haga clic en **Aceptar** en el cuadro de diálogo de confirmación. El proceso de aprovisionamiento crea el grupo de recursos y la aplicación de API en su suscripción de Azure. Visual Studio muestra el progreso en la ventana **Actividad del Servicio de aplicaciones de Azure**.

	![Notificación de estado mediante la ventana de actividad del Servicio de aplicaciones de Azure](./media/app-service-dotnet-deploy-api-app/26-provisioning-success-v3.png)

7. Una vez que se ha aprovisionado la aplicación de API, haga clic con el botón secundario en el proyecto en el **Explorador de soluciones** y seleccione **Publicar** para volver a abrir el cuadro de diálogo de publicación. El perfil de publicación que creó en el paso anterior debe estar preseleccionado. Haga clic en **Publicar** para comenzar el proceso de implementación.

	![Implementación de la aplicación de API](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v3.png)

La ventana **Actividad del Servicio de aplicaciones de Azure** muestra el progreso de la implementación.

![Notificación de estado de la ventana de actividad del Servicio de aplicaciones de Azure](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v4.png)

## Visualización de aplicaciones en el Portal de vista previa de Azure

En esta sección, se desplazará al portal para ver la configuración básica disponible para las aplicaciones de API y realizar cambios iterativos en su aplicación de API. Con cada implementación, el portal reflejará los cambios que está realizando en la aplicación de API.

1. En el explorador, vaya al [Portal de vista previa de Azure](https://portal.azure.com). 

2. Haga clic en el botón **Examinar** de la barra lateral y seleccione **Aplicaciones de API**.

	![Examen de opciones en el portal de Azure](./media/app-service-dotnet-deploy-api-app/27-browse-in-portal-v3.png)

3. Seleccione la API que creó en la lista de aplicaciones de API de su suscripción.

	![Lista de Aplicaciones de API](./media/app-service-dotnet-deploy-api-app/28-view-api-list-v3.png)

4. Haga clic en **Definición de API**. La hoja **Definición de API** de la aplicación muestra la lista de operaciones de API que definió al crear la aplicación.

	![Definición de la API](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

5. Ahora, vuelva al proyecto de Visual Studio y agregue el código siguiente al archivo **ContactsController.cs**. Este código agrega un método **Post** que se puede usar para registrar nuevas instancias de `Contact` en la API.

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![Incorporación del método Post al controlador](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

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

<!--HONumber=54--> 