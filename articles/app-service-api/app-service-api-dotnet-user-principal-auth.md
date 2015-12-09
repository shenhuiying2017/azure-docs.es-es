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

En este tutorial, se muestra cómo usar las características de autenticación y autorización de Servicio de aplicaciones de Azure para proteger una aplicación de API y cómo consumir la aplicación de API en nombre de usuarios finales. El proveedor de autenticación que se muestra en el tutorial es Azure Active Directory y el cliente de ejemplo es una aplicación de una única página de AngularJS que se ejecuta en un explorador.

![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)
 
Esta es la tercera de una serie de tutoriales que muestran cómo trabajar con aplicaciones de API del Servicio de aplicaciones de Azure. Para obtener información acerca de la serie, consulte [Introducción a Aplicaciones de API y ASP.NET en el Servicio de aplicaciones de Azure](app-service-api-dotnet-get-started.md).

## Autenticación y autorización en el Servicio de aplicaciones de Azure

Servicio de aplicaciones de Azure ofrece servicios integrados para la autenticación y autorización. El usuario puede controlar la autenticación en el propio código, pero Servicio de aplicaciones ofrece una solución llave en mano si desea minimizar la cantidad de código que hay que escribir y mantener.

Puede usar cualquiera de los cinco proveedores de autenticación admitidos por Servicio de aplicaciones: Azure Active Directory, Facebook, Google, Twitter y cuenta de Microsoft. Y puede proteger una API escrita en cualquier lenguaje compatible con Servicio de aplicaciones: sin escribir ningún código en la API, puede requerir el inicio de sesión del usuario o un token para acceder a él.

Servicio de aplicaciones de Azure controla la autenticación y deja que el código controle la autorización. Puede configurar Servicio de aplicaciones para permitir que solo los usuarios autenticados llamen a la API o bien puede permitir a todos los que llaman. En cualquier caso, Servicio de aplicaciones pasa la información de autenticación a la aplicación en los encabezados HTTP y el código puede usar esa información para tomar decisiones de autorización.

* En una API de .NET, puede usar el atributo `Authorize` y, en el caso de una autorización específica, puede escribir fácilmente código según las notificaciones. La información de notificaciones se rellena en las clases .NET.

* Para las API escritas en otros lenguajes, Servicio de aplicaciones se pasa en el token JWT del encabezado de autorización de una solicitud HTTP. Además, Azure establece algunos encabezados especiales (por ejemplo, `x-ms-client-principal-id`) para darle un acceso más sencillo a las notificaciones más importantes.

Para obtener más información acerca de los servicios de autenticación y autorización del Servicio de aplicaciones de Azure, consulte [Expanding App Service authentication/authorization](/blog/announcing-app-service-authentication-authorization/) y [Aplicaciones de API del Servicio de aplicaciones: lo que ha cambiado](app-service-api-whats-changed.md).

## El proyecto de ejemplo ContactsList.Angular.AAD

En este tutorial, puede usar los proyectos de ejemplo que descargó en el [primer tutorial de esta serie](app-service-api-dotnet-get-started.md) así como los recursos de Azure (aplicación de API y aplicación web) que creó en el primer tutorial.

El proyecto ContactsList.Angular.AAD es un cliente de AngularJS que incluye código para trabajar con Azure Active Directory. El código se basa en un ejemplo de AAD que puede encontrarse en el repositorio [Azure-Samples/active-directory-angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp).

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

Aquí, el método `Get` está etiquetado como `getItems` y, en el controlador (*app/scripts/contactsCtrl.js*) `getItems` se conecta a `$scope.populate`.

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

## Configuración de la autenticación y autorización en Azure

1. En el [Portal de Azure](https://portal.azure.com/), vaya a la hoja **Aplicación de API** de la Aplicación API que creó en el primer tutorial y después haga clic en **Configuración**.

2. Busque la sección **Características** y después haga clic en **Autenticación y autorización**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. En la hoja **Autenticación y autorización**, haga clic en **Activado**.

4. En la lista desplegable **Acción por realizar cuando no se autentique la solicitud**, seleccione **Iniciar sesión con Azure Active Directory**.

5. En **Proveedores de autenticación**, haga clic en **Azure Active Directory**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. En la hoja **Configuración de Azure Active Directory**, haga clic en **Rápido**.

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	Azure creará automáticamente una aplicación AAD en su inquilino de AAD. Anote el nombre de la nueva aplicación AAD, pues lo seleccionará más adelante en el Portal de Azure clásico para obtener su identificador de cliente de la nueva aplicación AAD.

7. Haga clic en **Aceptar**.

10. En la hoja **Autenticación y autorización**, haga clic en **Guardar**.

8. Para comprobar que la aplicación de API ya está protegida, vaya a la dirección URL de la aplicación de API `/swagger` como hizo en el primer tutorial para usar la interfaz de usuario de Swagger.

	Esta vez, se le redirigirá a una página de inicio de sesión.

	![](./media/app-service-api-dotnet-user-principal-auth/loginpage.png)

11. En el [Portal de Azure clásico](https://manage.windowsazure.com/), vaya a **Azure Active Directory**.

12. En la pestaña Directorio, haga clic en su inquilino de AAD.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Haga clic en **Aplicaciones > Aplicación propiedad de mi compañía** y después haga clic en la marca de verificación.

	También es posible que tenga que actualizar la página para ver la nueva aplicación.

15. En la lista de aplicaciones, haga clic en el nombre de aquella que Azure creó automáticamente cuando habilitó la autenticación para la aplicación de API.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Haga clic en **Configurar**.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

15. En la parte inferior de la página, haga clic en **Administrar manifiesto > Descargar manifiesto** y guarde el archivo en una ubicación donde se pueda editar.

16. En el archivo de manifiesto descargado, busque la propiedad `oauth2AllowImplicitFlow`. Cambie el valor de esta propiedad de `false` a `true` y después guarde el archivo.

16. Haga clic en **Administrar manifiesto > Cargar manifiesto** y cargue el archivo actualizado en el paso anterior.

17. Mantenga esta página abierta para poder copiar y pegar valores desde ella y actualizar los valores de la página en pasos posteriores del tutorial.

## Configuración de proyectos de Visual Studio para https

1. En el **Explorador de soluciones**, haga clic en el proyecto ContactList.API y después en la ventana **Propiedades** cambie **SSL habilitado** a **True**.

2. Copie la URL de SSL.

	![](./media/app-service-api-dotnet-user-principal-auth/enablessl.png)

3. Haga clic con el botón derecho en el proyecto ContactsList.API y después haga clic en **Propiedades**.

5. Haga clic en la pestaña **Web**, pegue la dirección URL de SSL en el campo **URL del proyecto** y después guarde los cambios.

	![](./media/app-service-api-dotnet-user-principal-auth/setprojecturl.png)

1. Siga el mismo procedimiento para habilitar SSL en el proyecto ContactsList.Angular.AAD.

2. Establezca el proyecto ContactsList.API y los proyectos de ContactsList.Angular.AAD como proyectos de inicio y establezca el proyecto ContactsList.API para que comience el primero.

## Actualización de la dirección URL del punto de conexión y la configuración de AAD en el proyecto ContactsList.Angular.AAD

7. En el proyecto ContactsList.Angular.AAD, abra el archivo *app/scripts/app.js*.

8. En el código que establece la variable `endpoints`, asegúrese de que tiene la dirección URL de SSL correcta para el proyecto ContactsList.API y reemplace ambas instancias de "suIdentificadorDeCliente" por el valor real del identificador de cliente de la aplicación de AAD desde la pestaña **Configurar** del portal clásico para la aplicación AAD. Asegúrese de que la dirección URL del punto de conexión termina con una barra diagonal.

	El código será similar al ejemplo siguiente:

		var endpoints = {
		    //"https://{your api app name}.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    "https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. Además en *app.js*, en el código `adalProvider.init`, reemplace "{direcciónURLDelInquilino}" y "{suIdentificadorDeCliente}" por los valores reales.

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

10. En *app/scripts/contactsSvc.js*, asegúrese de que apiEndpoint está establecido en la dirección URL de SSL correcta para el proyecto ContactsList.API.

		//var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		var apiEndpoint = "https://localhost:44300";

## Actualización de la configuración de AAD en el proyecto ContactsList.API

1. En el proyecto ContactsList.API, abra el archivo *Web.config* de la aplicación.

2. En el elemento appSettings, establezca ida:Authority en el valor "https://login.windows.net/{direcciónURLDelInquilino}" y establezca ida:ClientId en el identificador de cliente de la aplicación AAD, como se muestra en el ejemplo siguiente.

		<appSettings>
		  <add key="ida:Authority" value="https://login.windows.net/contoso.onmicrosoft.com" />
		  <add key="ida:ClientId" value="1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3" />
		</appSettings>

## Configuración de la aplicación AAD para el localhost 

1. En la pestaña **Configurar** para la aplicación AAD en el portal clásico, en el campo **URL de inicio de sesión**, pegue la dirección URL de SSL del proyecto ContactsList.Angular.AAD y quite la barra diagonal final.

	![](./media/app-service-api-dotnet-user-principal-auth/signonurl.png)

3. Cerca de la parte inferior de la pestaña **Configurar**, en el campo **URL de respuesta**, pegue la dirección URL de SSL del proyecto ContactsList.Angular.AAD, reemplazando el valor ya existente y dejando la barra diagonal final en su lugar.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurl.png)

4. Haga clic en **Guardar**.

## Ejecución de los proyectos de cliente y de API localmente

Cuando se ejecuta localmente HTTPS, recibirá mensajes de advertencia sobre el certificado SSL de IIS Express para localhost. Para ejecutarlo localmente, puede hacer clic después de estos mensajes. Si lo prefiere, puede omitir esta sección e ir directamente a la siguiente sección donde se preparará para ejecutar la aplicación y la API en Azure.

Si tiene problemas al iniciar sesión, pruebe con otro explorador o abra una ventana InPrivate o de incógnito en la dirección URL del proyecto AngularJS, por ejemplo, `https://localhost:44301`.

5. En Visual Studio, presione F5 para ejecutar los proyectos de API y AngularJS localmente.

	Aparece la pestaña de inicio de la aplicación de AngularJS.

10. Haga clic en la pestaña **Inicio de sesión**.

	Se le pedirá que inicie sesión.

7. Inicie sesión con las credenciales de usuario para un usuario en su inquilino de AAD.

10. Haga clic en la pestaña **Contactos**.

	Se muestra la página **Contactos**.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspagelocal.png)

11. Cierre las ventanas del explorador.

El código del proyecto ContactsList.Angular.AAD requiere haber iniciado sesión antes de llamar a la API y mostrar los contactos. Sin embargo, no hay nada que impida que los autores de llamadas no autenticadas llamen a la API. Puede comprobarlo mediante la ejecución de la interfaz de usuario de Swagger en la ventana del explorador que se abre en la dirección URL de SSL del proyecto ContactsList.API. La API estará protegida frente a los autores de llamadas no autenticadas solo cuando se ejecuta en el Servicio de aplicaciones de Azure.

En las secciones siguientes configurará los proyectos y AAD para ejecutar el cliente y la API de Servicio de aplicaciones de Azure, implementar los proyectos en Azure y probarlos en Azure.

## Configuración del proyecto ContactsList.Angular.AAD para llamar a la aplicación de API de Azure

1. En el proyecto ContactsList.Angular.AAD, abra el archivo *app/scripts/app.js*.

2. Comente el punto de conexión de localhost, quite el comentario del punto de conexión de Azure y reemplace "{your api app name}" por el nombre de la aplicación de API.

	El código será similar al del ejemplo siguiente:

		var endpoints = {
		    "https://contactslistapi.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    //"https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

1. Realice el mismo cambio de la dirección URL del punto de conexión en el archivo *app/scripts/contactsSvc.js*.

	El código será similar al del ejemplo siguiente:

		var apiEndpoint = "https://contactslistapi.azurewebsites.net";
		//var apiEndpoint = "https://localhost:44300";


## Configuración de la aplicación AAD para la aplicación web de Azure

1. En la pestaña **Configurar** para la aplicación AAD en el portal clásico, en el campo **URL de inicio de sesión**, elimine la dirección URL de SSL del proyecto ContactsList.Angular.AAD y reemplácela por la URL base de la aplicación web sin la barra diagonal final. (Tenga en cuenta que esta es la dirección URL de la aplicación web, no de la aplicación de API).

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

3. En el campo **URL de respuesta**, reemplace la dirección URL de SSL del proyecto ContactsList.Angular.AAD por la URL base de la aplicación web, sin quitar la barra diagonal final.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

4. Haga clic en **Guardar**.

## Implementación del proyecto ContactsList.API en Azure

8. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto ContactsList.API y, después, haga clic en **Publicar**.

	Se abre el asistente **Publicación web** en el último perfil de publicación usado en este proyecto, que es para implementar en la aplicación de API creada anteriormente.

7. Haga clic en **Publicar**.

8. Cierre la ventana del explorador que se abre automáticamente.

## Implementación del proyecto ContactsList.Angular.AAD en Azure

8. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto ContactsList.Angular.API y, después, haga clic en **Publicar**.

9. Haga clic en **Servicio de aplicaciones de Microsoft Azure**.

10. En el cuadro de diálogo **Servicio de aplicaciones**, en la lista desplegable **Suscripción**, elija la suscripción.

11. Expanda el grupo de recursos que ha creado en este tutorial y seleccione la aplicación web que creó en el segundo tutorial.

	![](./media/app-service-api-dotnet-user-principal-auth/deploytowebapp.png)

12. Haga clic en **Aceptar**.

12. En el asistente **Publicación web**, haga clic en la pestaña **Conexión** y, después, en el cuadro **Dirección URL de destino** cambie `http://` por `https://`.

	![](./media/app-service-api-dotnet-user-principal-auth/httpsinconntab.png)

12. En el asistente **Publicación web**, haga clic en la pestaña **Configuración**, expanda **Opciones de publicación de archivos** y active la casilla **Quitar archivos adicionales en destino**.

7. Haga clic en **Publicar**.

	Visual Studio implementa el proyecto y abre un explorador en la página principal de la aplicación.

## Prueba de la aplicación web de AngularJS en Azure

8. Haga clic en la pestaña **Contactos**.

	Se le pedirá que inicie sesión.

9. Inicie sesión con las credenciales de usuario en su inquilino de AAD.

10. Se muestra la página **Contactos**.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)

11. Para comprobar que la aplicación de API está protegida, vaya a la dirección URL de la interfaz de usuario de Swagger en una ventana de explorador InPrivate o de incógnito.

	Se le redirigirá a una página de inicio de sesión.

	El front-end ahora puede llamar a la API en nombre de un usuario autenticado, pero los usuarios no autenticados no pueden llamar a la API.

## Pasos siguientes

En este tutorial se usó la autenticación y autorización de Servicio de aplicaciones para restringir el acceso a una aplicación de API para que solo los usuarios autenticados pueden llamarla. En el siguiente tutorial de la serie, aprenderá cómo [restringir el acceso a la aplicación de API en escenarios de servicio a servicio](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_1203_2015-->