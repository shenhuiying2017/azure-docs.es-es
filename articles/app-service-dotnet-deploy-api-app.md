<properties 
	pageTitle="Implementación de una aplicación de API del Servicio de aplicaciones de Azure" 
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
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Implementación de una aplicación de API del Servicio de aplicaciones de Azure

## Información general

Si está desarrollando activamente su propia aplicación de API con Visual Studio y necesita probar la API en la nube, puede crear una nueva aplicación de API en su suscripción de Azure e implementar el código con las cómodas características de implementación del Servicio de aplicaciones de Visual Studio. 

Este es el segundo tutorial de una serie de tres:

1. En [Creación de una API de aplicación](app-service-dotnet-create-api-app.md) creó un proyecto de aplicación de API. 
* En este tutorial, implementará la aplicación de API en su suscripción de Azure.
* En [Depuración de una aplicación de API](app-service-dotnet-remotely-debug-api-app.md), usará Visual Studio para depurar el código de forma remota mientras se ejecuta en Azure.

## Implementación de aplicaciones de API 

En el **Explorador de soluciones**, haga clic en el proyecto (no la solución) y en **Publicar...**. 

![Project publish menu option](./media/app-service-dotnet-deploy-api-app/20-publish-gesture-v3.png)

Haga clic en la pestaña **Perfil** y en **Aplicaciones de API de Microsoft Azure (vista previa)**. 

![Publish Web dialog](./media/app-service-dotnet-deploy-api-app/21-select-api-apps-for-deployment-v2.png)

Haga clic en **Nuevo** para aprovisionar una nueva aplicación de API en su suscripción de Azure.

![Select Existing API Services dialog](./media/app-service-dotnet-deploy-api-app/23-publish-to-apiapps-v3.png)

En el cuadro de diálogo **Crear una aplicación de API**, escriba lo siguiente:

- En **Nombre de la aplicación de API**, escriba un nombre para la aplicación. 
- Si tiene varias suscripciones de Azure, seleccione la que desee usar.
-En el Plan del Servicio de aplicaciones, seleccione de entre los planes del Servicio de aplicaciones o elija **Crear nuevo plan del Servicio de aplicaciones** y escriba el nombre de un nuevo plan. 
- En **Grupo de recursos**, seleccione de entre los grupos de recursos existentes o elija **Crear nuevo grupo de recursos** y escriba un nombre. El nombre debe ser único; considere la posibilidad de usar el nombre de la aplicación como un prefijo y anexe información personal como el identificador de Microsoft (sin el signo @).  
- En **Nivel de acceso**, seleccione **Disponible para cualquier persona**. Con esta opción, la API será totalmente pública, lo cual está bien para este tutorial. Puede restringir el acceso más adelante a través del Portal de Azure.
- Seleccione una región.  

![Configure Microsoft Azure Web App dialog](./media/app-service-dotnet-deploy-api-app/24-new-api-app-dialog-v3.png)

Haga clic en **Aceptar** para crear la aplicación de API en su suscripción. El proceso puede tardar unos minutos, por lo que Visual Studio muestra un cuadro de diálogo que le notifica que se ha iniciado el proceso. 

![API Service Creation Started confirmation message](./media/app-service-dotnet-deploy-api-app/25-api-provisioning-started-v3.png)

El proceso de aprovisionamiento crea el grupo de recursos y la aplicación de API en su suscripción de Azure. Visual Studio muestra el progreso en la ventana **Actividad del Servicio de aplicaciones de Azure**. 

![Status notification via the Azure App Service Activity window](./media/app-service-dotnet-deploy-api-app/26-provisioning-success-v3.png)

Una vez que se ha aprovisionado la aplicación de API, haga clic con el botón secundario en el proyecto en el Explorador de soluciones y seleccione **Publicar** para volver a abrir el cuadro de diálogo de publicación. El perfil de publicación que creó en el paso anterior debe estar preseleccionado. Haga clic en **Publicar** para comenzar el proceso de implementación. 

![Deploying the API App](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v3.png)

La ventana **Actividad del Servicio de aplicaciones de Azure** muestra el progreso de la implementación. 

![Status notification of the Azure App Service Activity window](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v4.png)

## Visualización de aplicaciones en el Portal de Azure

En esta sección, se desplazará al portal para ver la configuración básica disponible para las aplicaciones de API y realizar cambios iterativos en su aplicación de API. Con cada implementación, el portal reflejará los cambios que está realizando en la aplicación de API. 

En el explorador, vaya al [Portal de Azure](https://portal.azure.com). 

Haga clic en el botón **Examinar** de la barra lateral y seleccione **Aplicaciones de API**.

![Browse options on Azure portal](./media/app-service-dotnet-deploy-api-app/27-browse-in-portal-v3.png)

Seleccione la API que creó en la lista de aplicaciones de API de su suscripción.

![API Apps list](./media/app-service-dotnet-deploy-api-app/28-view-api-list-v3.png)

Haga clic en **Definición de API**. La hoja **Definición de API de la aplicación** muestra la lista de operaciones de API que definió al crear la aplicación. 

![API Definition](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

Ahora vuelva al proyecto en Visual Studio. Agregue el siguiente código al archivo **ContactsController.cs**.  

	[HttpPost]
	public HttpResponseMessage Post([FromBody] Contact contact)
	{
		// todo: save the contact somewhere
		return Request.CreateResponse(HttpStatusCode.Created);
	}

Este código agrega un método **Post** que se puede usar para registrar nuevas instancias de `Contacto` en la API. 

![Adding the Post method to the controller](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y, a continuación, seleccione **Publicar**. 

![Project Publish context menu](./media/app-service-dotnet-deploy-api-app/31-publish-gesture-v3.png)

Seleccione la configuración **Depurar** del menú desplegable **Configuración** y haga clic en **Publicar** para volver a implementar la aplicación de API. 

![Publish Web settings](./media/app-service-dotnet-deploy-api-app/36.5-select-debug-option-v3.png)

En la pestaña **Vista previa** del asistente **Publicación web**, haga clic en **Publicar**.  

![Publish Web dialog](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

Cuando haya completado el proceso de publicación, vuelva al portal y cierre y vuelva a abrir la hoja **Definición de API**. Verá el nuevo extremo de API que acaba de crear e implementar directamente en su suscripción de Azure.

![API Definition](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## Pasos siguientes

Ha visto cómo las capacidades de implementación directa en Visual Studio facilitan la iteración y la implementación rápida y prueban que la API funcione correctamente. En el [siguiente tutorial](app-service-dotnet-remotely-debug-api-app.md), aprenderá a depurar la aplicación de API mientras se ejecuta en Azure.

<!--HONumber=49-->