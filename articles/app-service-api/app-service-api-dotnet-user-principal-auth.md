<properties
	pageTitle="Autenticación de usuario para aplicaciones de API en el Servicio de aplicaciones de Azure | Microsoft Azure"
	description="Obtenga información sobre cómo proteger una aplicación de API del Servicio de aplicaciones de Azure al permitir el acceso únicamente a usuarios autenticados."
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
	ms.topic="hero-article"
	ms.date="11/30/2015"
	ms.author="tdykstra"/>

# Autenticación de usuario para aplicaciones de API en el Servicio de aplicaciones de Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Información general

En este tutorial se muestra cómo usar las características de autenticación y autorización del Servicio de aplicaciones de Azure para proteger una aplicación de API y cómo consumir la aplicación de API en nombre de los usuarios finales. El proveedor de autenticación que se muestra en el tutorial es Azure Active Directory, la API es ASP.NET Web API y el cliente es una aplicación de una sola página de AngularJS que se ejecuta en un explorador.

![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)
 
## Autenticación y autorización en el Servicio de aplicaciones

Para ver una introducción sobre las características de autenticación y autorización usadas en este tutorial, consulte el anterior tutorial de la serie, [Introducción a Aplicaciones de API y ASP.NET en el Servicio de aplicaciones de Azure](app-service-api-dotnet-get-started.md).

## Seguimiento de este tutorial

En este tutorial se usa una aplicación de ejemplo que descarga y crea en la aplicación de API en el primer tutorial: [Introducción a Aplicaciones de API y ASP.NET en el Servicio de aplicaciones de Azure](app-service-api-dotnet-get-started.md).

## El proyecto de ejemplo ContactsList.Angular.AAD

En la [aplicación de ejemplo ContactsList](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list), el proyecto ContactsList.Angular.AAD es un cliente de AngularJS que incluye código para trabajar con Azure Active Directory. El código se basa en un ejemplo de AAD que puede encontrarse en el repositorio [Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi).

El código del proyecto ContactsList.Angular.AAD está estructurado de manera diferente que el proyecto ContactsLists.Angular más sencillo. El código que llama a la API se encuentra en el archivo *app/scripts/contactsSvc.js* del proyecto ContactsList.Angular.AAD.

		angular.module('contactsListApp')
		.factory('contactsSvc', ['$http', function ($http) {
		    //var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		    var apiEndpoint = "https://localhost:44300";
		
		    $http.defaults.useXDomain = true;
		    delete $http.defaults.headers.common['X-Requested-With']; 
		
		    return {
		        getItems: function () {
		            return $http.get(apiEndpoint + '/api/contacts');
		        },
		        getItem : function(id){
		            return $http.get(apiEndpoint + '/api/contacts/' + id);
		        },
		        postItem : function(item){
		            return $http.post(apiEndpoint + '/api/contacts', item);
		        },
		        putItem : function(item){
		            return $http.put(apiEndpoint + '/api/contacts/', item);
		        },
		        deleteItem : function(id){
		            return $http({
		                method: 'DELETE',
		                url: apiEndpoint + '/api/contacts/' + id
		            });
		        }
		    };
		}]);

En este caso, el método `Get` se denomina `getItems`. En el controlador (*app/scripts/contactsCtrl.js*) `getItems` se conecta a `$scope.populate`.

		$scope.populate = function () {
		    contactsSvc.getItems().then(function (results) {
		        $scope.contactsList = results.data;
		        $scope.loadingMessage = "";
		    }, function (err) {
		        $scope.loadingMessage = "";
		        $scope.error = "Error: " + err;
		    });
		};

En la vista (*app/views/Contacts.html*), se llama a $scope.populate en la inicialización.

		<div ng-init="populate()">

La [Biblioteca de autenticación de Azure Active Directory para JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js), en los archivos *adal.js* y *adal-angular.js*, proporciona el código adicional para iniciar sesión e incluir un token de autorización con las solicitudes de API.

En el archivo *app.js*, el código pasa la información de configuración y el `$http` proveedor a la función `adalProvider.init`. La información de configuración incluye el identificador de cliente de la aplicación AAD que pertenece a cada punto de conexión de API y el identificador del cliente que pertenece a esta aplicación de AngularJS. La función `init` agrega interceptores al proveedor `$http`; éstos agregan el token de autorización a las solicitudes.

		var endpoints = { 
		    //"https://{your api app name}.azurewebsites.net/": "{your client id}"
		    "https://localhost:44300/": "{your client id}"
		};

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: '{your tenant url}',
		        clientId: '{your client id}',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		        //cacheLocation: 'localStorage', // enable this for 
		    },
		    $httpProvider
		    );

## Configuración de la autenticación y autorización en Azure

1. En el [portal de Azure](https://portal.azure.com/), vaya a la hoja **Aplicación de API** de la aplicación de API que desea proteger para que solo los usuarios autenticados puedan llamarla. (Para este tutorial, elija la aplicación de API para la que se implementó el proyecto de ContactsList.API).

2. Haga clic en **Configuración**

2. Busque la sección **Características** y haga clic en **Autenticación/autorización**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. En la hoja **Autenticación/autorización**, haga clic en **Activado**.

4. En la lista desplegable **Acción por realizar cuando no se autentique la solicitud**, seleccione **Iniciar sesión con Azure Active Directory**.

5. En **Proveedores de autenticación**, haga clic en **Azure Active Directory**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. En la hoja **Configuración de Azure Active Directory**, haga clic en **Rápida**

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	"Rápida" significa aquí que Azure creará automáticamente una aplicación AAD en su inquilino de AAD. Anote el nombre de la nueva aplicación AAD, pues lo seleccionará más adelante en el Portal de Azure clásico para obtener su identificador de cliente de la nueva aplicación AAD.

7. Haga clic en **Aceptar**.

10. En la hoja **Autenticación/autorización**, haga clic en **Guardar**.

8. Para comprobar que la aplicación de API ya está protegida, vaya a la dirección URL de la aplicación de API + `/swagger` como hizo en el primer tutorial para usar la interfaz de usuario de Swagger.

	Esta vez, se le redirigirá a una página de inicio de sesión.

	![](./media/app-service-api-dotnet-user-principal-auth/loginpage.png)

11. En el [Portal de Azure clásico](https://manage.windowsazure.com/), vaya a **Azure Active Directory**.

	Tiene que ir al portal clásico porque determinadas opciones de Azure Active Directory a las que necesita acceder aún no están disponibles en el portal de Azure actual.

12. En la pestaña **Directorio**, haga clic en su inquilino de AAD.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Haga clic en **Aplicaciones > Aplicación propiedad de mi compañía** y después clic en la marca de verificación.

	También es posible que tenga que actualizar la página para ver la nueva aplicación.

15. En la lista de aplicaciones, haga clic en el nombre de aquella que Azure creó automáticamente cuando habilitó la autenticación para la aplicación de API.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Haga clic en **Configurar**.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

15. En la parte inferior de la página, haga clic en **Administrar manifiesto > Descargar manifiesto** y guarde el archivo en una ubicación donde se pueda editar.

16. En el archivo de manifiesto descargado, busque la propiedad `oauth2AllowImplicitFlow`. Cambie el valor de esta propiedad de `false` a `true` y después guarde el archivo.

	Esta configuración es necesaria para el acceso desde una aplicación de una página de JavaScript. Permite que el token de portador de Oauth 2.0 se devuelva al fragmento de dirección URL.

16. Haga clic en **Administrar manifiesto > Cargar manifiesto** y cargue el archivo actualizado en el paso anterior.

17. Mantenga esta página abierta para poder copiar y pegar valores desde ella y actualizar los valores de la página en pasos posteriores del tutorial.

## Configuración del proyecto ContactsList.Angular.AAD para llamar a la aplicación de API de Azure

Las instrucciones siguientes explican cómo implementar la aplicación en Azure y ejecutarlo allí, pero con cambios menores que puede ejecutar localmente. El código de ejemplo contiene puntos de conexión de la dirección URL de localhost. Si desea ejecutarlo localmente, configure los proyectos de SSL y utilice las direcciones URL de SSL de localhost en el código del proyecto y en la configuración de la aplicación AAD. Mientras se ejecuta localmente, el código de AngularJS solo permitirá llamar a la API a los usuarios que han iniciado sesión, pero los autores de llamadas no autenticados de otros clientes podrían llamar a la API.

1. En el proyecto ContactsList.Angular.AAD, abra el archivo *app/scripts/app.js*.

8. En el código que establece la variable `endpoints`, convierta en comentario el punto de conexión de localhost y quite la marca de comentario del punto de conexión de Azure.

10. Reemplace "yourclientid" por el valor real del identidicador de cliente de la aplicación de AAD desde la pestaña **Configurar** del portal clásico para la aplicación de AAD.

2. Reemplace "{nombreDeSuAplicaciónApi}" por el nombre de la aplicación de API que implementó el proyecto ContactsList.API.

	El código será similar al del ejemplo siguiente:

		var endpoints = {
		    "https://contactslistapi.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    //"https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. Además en *app.js*, en la llamada a `adalProvider.init`, reemplace "{direcciónURLDelInquilino}" y "{suIdentificadorDeCliente}" por los valores reales.

	El código será similar al ejemplo siguiente:

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: 'contoso.onmicrosoft.com',
		        clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		    },
		    $httpProvider
		    );

1. En el archivo *app/scripts/contactsSvc.js*, cambie la URL del mismo punto de conexión de cambiar desde localhost a la dirección URL de la aplicación de API que creó anteriormente en *app.js*.

	El código será similar al del ejemplo siguiente:

		var apiEndpoint = "https://contactslistapi.azurewebsites.net";
		//var apiEndpoint = "https://localhost:44300";

## Configuración de la aplicación AAD para la aplicación web de Azure

1. En la pestaña **Configurar** para la aplicación AAD en el portal clásico, en el campo **URL de inicio de sesión**, elimine la dirección URL que ya está ahí y reemplácela por la URL base de la aplicación web, incluida la barra diagonal final. (Tenga en cuenta que se trata de la dirección URL de la aplicación web que ejecutará el código de AngularJS, no la URL de la aplicación de API).

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

3. En el campo **URL de respuesta**, reemplace la dirección URL que ya existe por la dirección URL base de la aplicación web.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

4. Haga clic en **Guardar**.

## Implementación del proyecto ContactsList.Angular.AAD en Azure

8. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto ContactsList.Angular.AAD y, después, en **Publicar**.

9. Haga clic en **Servicio de aplicaciones de Microsoft Azure**.

10. En el cuadro de diálogo **Servicio de aplicaciones**, en la lista desplegable **Suscripción**, elija la suscripción.

11. Expanda el grupo de recursos que ha creado en este tutorial y seleccione la aplicación web que creó en el segundo tutorial.

	![](./media/app-service-api-dotnet-user-principal-auth/deploytowebapp.png)

12. Haga clic en **Aceptar**.

12. En el Asistente para **publicación web**, haga clic en la pestaña **Conexión** y, después, en el cuadro **Dirección URL de destino** cambie `http://` a `https://`.

	![](./media/app-service-api-dotnet-user-principal-auth/httpsinconntab.png)

	Esta configuración determina qué dirección URL en la que se abrirá el explorador predeterminado después de una implementación correcta.

12. En el Asistente para **publicación web**, haga clic en la pestaña **Configuración**, expanda **Opciones de publicación de archivos** y active la casilla **Quitar archivos adicionales en el destino**.

7. Haga clic en **Publicar**.

	Visual Studio implementa el proyecto y abre un explorador en la página principal de la aplicación.

## Prueba de la aplicación web de AngularJS en Azure

8. Haga clic en la pestaña **Contactos**.

	Se le pedirá que inicie sesión.

9. Inicie sesión con las credenciales de usuario en su inquilino de AAD.

10. Se muestra la página **Contactos**.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)

El front-end ahora puede llamar a la API en nombre de un usuario autenticado, pero los usuarios no autenticados no pueden llamar a la API.

## Pasos siguientes

En este tutorial se usó la autenticación y autorización de Servicio de aplicaciones para restringir el acceso a una aplicación de API para que solo los usuarios autenticados pueden llamarla. En el siguiente tutorial de la serie, aprenderá cómo [restringir el acceso a la aplicación de API en escenarios de servicio a servicio](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_1210_2015-->