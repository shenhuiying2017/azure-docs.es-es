<properties
	pageTitle="Consumo de una aplicación de API de JavaScript con CORS | Microsoft Azure"
	description="Aprenda a usar una aplicación de API del Servicio de aplicaciones de Azure, desde un cliente de JavaScript y mediante CORS."
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
	ms.date="11/27/2015"
	ms.author="tdykstra"/>

# Consumo de una aplicación de API desde JavaScript con CORS

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Información general

En este tutorial se muestra cómo usar una aplicación de API desde el código JavaScript de un sitio web que se transmite desde un dominio diferente que la aplicación de API. El cliente de ejemplo usa AngularJS.

![](./media/app-service-api-cors-consume-javascript/homepageazure.png)
 
Esta es la segunda de una serie de tutoriales que muestran cómo trabajar con aplicaciones de API del Servicio de aplicaciones de Azure. Para obtener información acerca de la serie, consulte [Introducción a Aplicaciones de API y ASP.NET en el Servicio de aplicaciones de Azure](app-service-api-dotnet-get-started.md).

## Introducción a CORS

Por motivos de seguridad, el comportamiento predeterminado de los exploradores impide que JavaScript realice llamadas de API a otro dominio distinto del que procede el código JavaScript. Por ejemplo, puede realizar una llamada desde una página web de contoso.com a un punto de conexión de la API de contoso.com, pero no a un punto de conexión de fabrikam.com. Uso compartido de recursos de origen cruzado (CORS) es un protocolo de Internet diseñado para habilitar escenarios donde es necesario realizar llamadas API entre dominios. En el Servicio de aplicaciones de Azure, un ejemplo de este escenario es donde se ejecuta el cliente de JavaScript en una aplicación web mientras se ejecuta la API en una aplicación de API.

En un proyecto Web API puede instalar paquetes de NuGet de CORS que permitan especificar en el código de qué dominios aceptará la API llamadas de JavaScript. También, puede usar la característica CORS integrada en el Servicio de aplicaciones de Azure para especificar desde qué dominios la aplicación de API aceptará llamadas. La primera mitad de este tutorial muestra cómo usar la característica de Azure, que funciona para todos los idiomas que admite el Servicio de aplicaciones de API. La segunda parte es opcional y muestra el método del paquete de NuGet que funciona con ASP.NET Web API.

## El proyecto de ejemplo ContactsList.Angular

En este tutorial, puede usar los proyectos de ejemplo que descargó en el primer tutorial de esta serie, así como los recursos de Azure (aplicación de API y aplicación web) que creó en el primer tutorial.

El proyecto ContactsList.Angular es un cliente simple de AngularJS para el proyecto Web API de ContactsList.API. El código JavaScript de AngularJS que llama a la API se encuentra en el archivo *index.html* del proyecto ContactsList.Angular. El código define las funciones y las agrega al objeto `$scope`, como se muestra aquí donde el método Get de la API se define como `$scope.refresh()`.

		angular.module('myApp', []).controller('contactListCtrl', function ($scope, $http) {
		    $scope.baseurl = 'http://localhost:51864';
		
		    $scope.refresh = function () {
		        $scope.status = "Refreshing Contacts...";
		        $http({
		            method: 'GET',
		            url: $scope.baseUrl + '/api/contacts',
		            headers: {
		                'Content-Type': 'application/json'
		            }
		        }).then(function (results) {
		            $scope.contacts = results.data;
		            $scope.status = "Contacts loaded";
		        }, function (err) {
		            $scope.status = "Error loading contacts";
		        });
		    };
		
		    // POST and DELETE not shown
		
		    $scope.refresh();
		});

El código llama al método $scope.refresh() cuando se carga la página (al final del fragmento de código mostrado anteriormente) y se vincula al botón **Actualizar** botón de la interfaz de usuario.

		<th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>

## Ejecución de manera local del proyecto AngularJS

En esta sección compruebe que puede ejecutar localmente el cliente y que puede llamar a la API mientras se ejecuta también de manera local.

1. Establezca los proyectos ContactsList.API y ContactsList.Angular como proyectos de inicio, con ContactsList.API que se inicie antes de ContactsList.Angular. 

2. Presione F5 para iniciar los proyectos.

	La interfaz de usuario de AngularJS muestra los contactos que se almacenan localmente, y puede usar la interfaz de usuario para agregar y eliminar contactos.

	![](./media/app-service-api-cors-consume-javascript/homepagelocal.png)

3. Cierre las ventanas del explorador.

## Cambio del proyecto AngularJS para que apunte a la aplicación de API de Azure 

A continuación, va a ejecutar el front-end de AngularJS en la nube y llamar al back-end de API que se ejecuta en la nube. Antes de implementar el front-end en Azure, tiene que cambiar el punto de conexión de la API en el proyecto de AngularJS para que el código llame a la aplicación de API de Azure que creó anteriormente.

1. En el proyecto ContactsList.Angular, abra *index.html*.

2. Comente la línea que establece `baseUrl` en la dirección URL de localhost, quite el comentario de la línea que establece `baseUrl` en una dirección URL azurewebsites.net y reemplace el marcador de posición con el nombre real de la aplicación de API que creó anteriormente. Si asignó el nombre de ContactsListAPI a la aplicación de API, el código es ahora similar al ejemplo siguiente.

		$scope.baseUrl = 'https://ContactsListAPI.azurewebsites.net';
		//$scope.baseUrl = 'http://localhost:51864';

### Implementación del proyecto ContactsList.Angular en la aplicación web

Puede crear una nueva aplicación web para implementar el proyecto AngularJS, pero para este tutorial, implementará la misma aplicación web que creó anteriormente. El nombre de la aplicación web puede reflejar el hecho de que originalmente implementó un proyecto de ASP.NET MVC en ella, pero después de esta implementación, se ejecutará el código de AngularJS.

8. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto ContactsList.Angular y, después, haga clic en **Publicar**.

9. Haga clic en la pestaña **Perfil**.

3.  En el paso **Perfil** del asistente **Publicación web**, haga clic en **Servicio de aplicaciones de Microsoft Azure**.

4. En el cuadro de diálogo **Servicio de aplicaciones**, seleccione la suscripción.

5. Con el valor de **Vista** establecido en el valor predeterminado de **Grupo de recursos**, expanda el grupo de recursos que creó para esta serie de tutoriales.

7. Seleccione la aplicación web que creó en el primer tutorial (asegúrese de no seleccionar la aplicación de API) y, después, haga clic en **Aceptar**.

8. Haga clic en la pestaña **Configuración**.

9. Expanda **Opciones de publicación del archivo**, y, a continuación, seleccione **Opciones de publicación de archivos**.

	![](./media/app-service-api-cors-consume-javascript/removeadditionalfiles.png)

	Normalmente al implementar un proyecto web en una aplicación web del Servicio de aplicaciones, no se desea la opción "quitar archivos adicionales" porque los cambios suelen ser actualizaciones o nuevos archivos. En este caso va a implementar un proyecto diferente en la misma aplicación web, por lo que probablemente haya muchos archivos de la implementación anterior que no sean necesarios en la nueva.

10. Haga clic en **Publicar**.

	Visual Studio implementa el proyecto ContactsList.Angular en la aplicación web y abre un explorador en la dirección URL de la aplicación web. El explorador muestra la misma interfaz de usuario de AngularJS que vio ejecutándose localmente, excepto que ahora produce un error porque el front-end se ejecuta en un dominio diferente (la URL de la aplicación web) que el back-end (la URL de aplicación de API).

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

## Configuración de CORS para la aplicación de API de destino en Azure

8. En otra ventana del explorador, vaya al [Portal de Azure](https://portal.azure.com/).

9. Navegue a la hoja Aplicación de API de la aplicación de API que ha creado en el primer tutorial.

10. Haga clic en **Configuración**.

11. Busque la sección **API** y, después, haga clic en **CORS**.

12. En el cuadro de texto, escriba la dirección URL de la aplicación web que creó en el primer tutorial para el front-end de ASP.NET MVC. Por ejemplo, si denominó ContactsListMVC a la aplicación web, escriba "http://contactslistmvc.azurewebsites.net".

	Tenga en cuenta que como alternativa a escribir una dirección URL, puede indicar un asterisco (*) para especificar que se aceptan todos los dominios de origen.

13. Haga clic en **Guardar**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

14. Vuelva a la ventana del explorador que muestra el cliente de AngularJS ejecutándose en la aplicación web de Azure y actualice la página o haga clic en el botón **Actualizar**.

	La página muestra ahora los contactos almacenados en el sistema de archivos de la aplicación de API de Azure.

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

## Configuración de CORS para la aplicación de API en el código de Web API

Si no desea usar la compatibilidad con CORS del Servicio de aplicaciones de Azure, una alternativa es habilitar CORS en el código de ASP.NET Web API. Este proceso se documenta en profundidad en [Habilitación de solicitudes entre orígenes en ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api). Para las aplicaciones de API compiladas con ASP.NET Web API, el proceso es exactamente el mismo, aunque se resume aquí.

En esta sección podrá deshabilitar la compatibilidad con CORS del Servicio de aplicaciones de Azure y después habilitar la compatibilidad con CORS de Web API. Tenga en cuenta que deshabilitar la compatibilidad con CORS de Azure no es un paso opcional antes de habilitar la compatibilidad con Web API. Si usa ambos métodos juntos, CORS de Azure tendrá prioridad y CORS de Web API no tendrá ningún efecto. Por ejemplo, si habilita un dominio de origen en Azure y habilita todos los dominios de origen en el código de Web API, la aplicación de API de Azure solo aceptará llamadas del dominio especificado en Azure.

### Deshabilitación de la compatibilidad con CORS de Azure

1. Para deshabilitar la compatibilidad con CORS de Azure, vuelva al Portal de Azure y borre la dirección URL especificada en la hoja CORS y después haga clic en **Guardar**.
 
3.  Vuelva a la dirección URL de la aplicación web en la que implementó la aplicación de AngularJS y actualice la página o haga clic en el botón **Actualizar**.

	Volverá a ver "Error al cargar contactos".

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

### Habilitación de la compatibilidad con CORS de Web API

La funcionalidad de CORS para Web API se proporciona en el paquete de NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/). El paquete ya está instalado en la aplicación de ejemplo descargada y todo lo que hay que hacer para habilitar CORS es quitar el comentario en algún código.

1. En el proyecto ContactsList.API, abra el archivo *App\_Start/WebApiConfig.cs*. Quite el comentario de la línea de código config.EnableCors al método **Register** de la clase **WebApiConfig**. 

	Cuando se actualiza el archivo, el código debe tener un aspecto similar al siguiente:

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
		        // Uncomment the following line to control CORS by using Web API code
				config.EnableCors();
	
	            // Web API routes
	            config.MapHttpAttributeRoutes();
	
	            config.Routes.MapHttpRoute(
	                name: "DefaultApi",
	                routeTemplate: "api/{controller}/{id}",
	                defaults: new { id = RouteParameter.Optional }
	            );
	        }
	    }

1. Abra el archivo *Controllers/ContactsController.cs* y quite el comentario de la línea que agrega el atributo `EnableCors` a la clase `ContactsController`.

		namespace ContactList.Controllers
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ContactsController : ApiController
 
	Si lo prefiere, puede aplicar el atributo a métodos de acción individuales en lugar de a todo el controlador.

	> **Nota**: el uso de caracteres comodín para todos los parámetros con el atributo `EnableCors` está previsto solo para fines de demostración, y abrirá la API hasta todos los orígenes y todas las solicitudes de HTTP. Use este atributo con precaución.

### Implementación de la API en Azure y prueba del front-end

8. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto ContactsList.API y, después, haga clic en **Publicar**.

	Se abrirá automáticamente el asistente para **publicación web** en el paso **Vista previa** para el perfil de publicación de la aplicación de API que creó anteriormente, ya que es el último destino en el que implementó este proyecto.

3.  Haga clic en **Publicar**.

	![](./media/app-service-api-cors-consume-javascript/pubapi.png)

	Una vez completada la implementación, el explorador abre una ventana en la dirección URL de la aplicación de API, que después muestra una página de "aplicación web creada correctamente".

3.  Vuelva a la dirección URL de la aplicación web en la que implementó la aplicación de AngularJS y actualice la página o haga clic en el botón **Actualizar**.

	La página se vuelve a cargar correctamente.

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

## Rehabilitación de la compatibilidad con CORS de Azure

Para usar los servicios de autenticación de Azure integrados en los tutoriales siguientes, tendrá que usar CORS de Azure en lugar de CORS de Web API.
 
1. Para habilitar la compatibilidad con CORS de Azure, vuelva a la hoja CORS del Portal de Azure para su aplicación de API y vuelva a escribir la dirección URL de la aplicación web.

13. Haga clic en **Guardar**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

## Pasos siguientes 

En este tutorial, vio dos maneras de habilitar la compatibilidad con CORS para que el código JavaScript de cliente pueda llamar a una API en un dominio diferente. En el siguiente tutorial, aprenderá cómo [restringir el acceso a la aplicación de API para que solo los usuarios autenticados puedan tener acceso a ella](app-service-api-dotnet-user-principal-auth.md).

<!---HONumber=AcomDC_1203_2015-->