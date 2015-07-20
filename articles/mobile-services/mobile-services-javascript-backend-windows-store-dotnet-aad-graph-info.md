<properties 
	pageTitle="Acceso a la información de gráficos de Azure Active Directory (Tienda Windows) | Centro de desarrollo móvil" 
	description="Obtenga información acerca de cómo acceder a la información de Active Directory de Azure con la API Graph en su aplicación de la Tienda Windows." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="wesmc"/>

# Acceso a información de Azure Active Directory Graph


[AZURE.INCLUDE [mobile-services-selector-aad-graph](../../includes/mobile-services-selector-aad-graph.md)]
##Información general

Al igual que los demás proveedores de identidades que ofrece Servicios móviles, El proveedor Azure Active Directory (AAD) admite también una completa biblioteca denominada API Graph que se puede usar para obtener acceso mediante programación al directorio. En este tutorial, va a actualizar la aplicación ToDoList para personalizar la experiencia de aplicación del usuario autenticado devolviendo información de usuario adicional que recuperará del directorio usando la [API de REST Graph].

Para obtener más información acerca de la API de Azure AD Graph, consulte el [Blog del equipo de Azure Active Directory Graph].

>[AZURE.NOTE]La finalidad de este tutorial es ampliar sus conocimientos sobre autenticación con Azure Active Directory. Debe haber realizado antes el tutorial [Incorporación de autenticación a la aplicación] usando el proveedor de autenticación de Azure Active Directory. Este tutorial continúa la actualización de la aplicación TodoItem que se usa en el tutorial [Incorporación de autenticación a la aplicación].



##Requisitos previos 

Antes de comenzar este tutorial, debe haber realizado los siguientes tutoriales de Servicios móviles:

+ [Agregar autenticación a su aplicación]<br/>Agrega un requisito de inicio de sesión a la aplicación de ejemplo TodoList.

+ [Tutorial sobre API personalizadas]<br/>Muestra cómo llamar a una API personalizada.



##Generación de una clave de acceso para el registro de la aplicación en AAD


Durante el tutorial [Incorporación de autenticación a la aplicación], creó un registro para la aplicación integrada cuando realizó el paso [Registro para usar un inicio de sesión de Azure Active Directory]. En esta sección, generará una clave que se usará cuando se lea información de directorios con el identificador de cliente de esa aplicación integrada.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]



##Creación de una API GetUserInfo personalizada

En esta sección, creará la API personalizada GetUserInfo que usará la [API REST Graph] para recuperar de AAD más información sobre el usuario.

Si nunca ha usado API personalizadas con Servicios móviles, debería revisar el [Tutorial sobre API personalizadas] antes de realizar esta sección.

1. En el [Portal de administración de Azure], cree la nueva API personalizada GetUserInfo para su servicio móvil y establezca el permiso **Solo usuarios autenticados** para el método get.

    ![][0]

2. Abra el editor de scripts para la nueva API GetUserInfo y agregue las variables siguientes al principio del script.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;



3. Agregue la siguiente definición para la función `getAADToken`.

        function getAADToken(callback) {
            var req = require("request");
            var options = {
                url: "https://login.windows.net/" + tenant_domain + "/oauth2/token?api-version=1.0",
                method: 'POST',
                form: {
                    grant_type: "client_credentials",
                    resource: "https://graph.windows.net",
                    client_id: clientID,
                    client_secret: key
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).access_token);
            });
        }

    Dados los valores de *tenant_domain*, *client id* de la aplicación integrada y *key* de la aplicación, esta función proporciona un token de acceso a Graph que se usa para leer información de directorios.

4. Agregue la siguiente función `getUser`, que usa la API Graph para devolver la información del usuario.

        function getUser(access_token, objectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/users/" + objectId + "?api-version=1.0",
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + access_token
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body));
            });
        };

    Esta función es un contenedor ligero en torno al extremo [Get User] de la API REST Graph. Utiliza el token de acceso de Graph para obtener la información del usuario usando el identificador de objeto del usuario.

5. Actualice el método `get` exportado como se indica a continuación para devolver la información del usuario usando otras funciones.

        exports.get = function (request, response) {
            if (request.user.level == 'anonymous') {
                response.send(statusCodes.UNAUTHORIZED, null);
                return;
            }
            var errorHandler = function (err) {
                console.log(err);
                response.send(statusCodes.INTERNAL_SERVER_ERROR, err);
            };
            request.user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) errorHandler(err);
                        else getUser(access_token, objectId, function (err, user_info) {
                            if (err) errorHandler(err);
                            else {
                                console.log('GetUserInfo: ' + JSON.stringify(user_info, null, 4));
                                response.send(statusCodes.OK, user_info);
                              }
                            });
                    });
                },
                error: errorHandler
            });
        };


##Actualización de la aplicación para que use GetUserInfo


En esta sección, va a actualizar el método `AuthenticateAsync` que implementó en el tutorial [Introducción a la autenticación] para que llame a la API personalizada y devuelva más información sobre el usuario de AAD.

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../../includes/mobile-services-aad-graph-info-update-app.md)]


 


##Prueba de la aplicación

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../../includes/mobile-services-aad-graph-info-test-app.md)]




##Pasos siguientes

En el próximo tutorial, [Control de acceso basado en roles con Azure Active Directory en Servicios móviles], usará control de acceso basado en roles con Azure Active Directory (AAD) para comprobar la pertenencia a grupos antes de permitir el acceso.



<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png


<!-- URLs. -->
[Agregar autenticación a su aplicación]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Incorporación de autenticación a la aplicación]: ../mobile-services-windows-store-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Tutorial sobre API personalizadas]: mobile-services-windows-store-dotnet-call-custom-api.md
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[Registro para usar un inicio de sesión de Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Graph API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[API REST Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[API de REST Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Get User]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Control de acceso basado en roles con Azure Active Directory en Servicios móviles]: mobile-services-javascript-backend-windows-store-dotnet-aad-rbac.md
[Blog del equipo de Azure Active Directory Graph]: http://go.microsoft.com/fwlink/?LinkId=510536

<!---HONumber=July15_HO2-->