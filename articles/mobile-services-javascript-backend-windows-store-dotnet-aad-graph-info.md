<properties linkid="develop-mobile-tutorials-javascript-aad-graph-info" urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Accessing Azure Active Directory Graph Information (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/20/2014" ms.author="wesmc" />

# Acceso a información de Azure Active Directory Graph

<div class="dev-center-tutorial-selector sublanding">
    <a href="/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-aad-graph-info/" title="C# para Tienda Windows" class="current">C# para Tienda Windows</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/" title="Back-end de .NET">Back-end de .NET</a> |
    <a href="/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/" title="Back-end de JavaScript" class="current">Back-end de JavaScript</a>
</div>

Al igual que los demás proveedores de identidades que ofrece Servicios móviles, El proveedor Azure Active Directory (AAD) admite también una completa biblioteca denominada [API Graph][API Graph] que se puede usar para obtener acceso mediante programación al directorio. En este tutorial, va a actualizar la aplicación ToDoList para personalizar la experiencia de aplicación del usuario autenticado de acuerdo con información de usuario adicional que recuperará del directorio usando la biblioteca [API Graph][API Graph].

> [WACOM.NOTE] La finalidad de este tutorial es ampliar sus conocimientos sobre autenticación con Azure Active Directory. Debe haber realizado antes el tutorial [Introducción a la autenticación][Introducción a la autenticación] usando el proveedor de autenticación de Azure Active Directory. Este tutorial continúa la actualización de la aplicación TodoItem que se usa en el tutorial [Introducción a la autenticación][Introducción a la autenticación].

Este tutorial le guiará para llevar a cabo los siguientes pasos:

1.  [Generación de una clave de acceso para el registro de la aplicación en AAD][Generación de una clave de acceso para el registro de la aplicación en AAD]
2.  [Creación de una API GetUserInfo personalizada][Creación de una API GetUserInfo personalizada]
3.  [Actualización de la aplicación para usar la API personalizada][Actualización de la aplicación para usar la API personalizada]
4.  [Prueba de la aplicación][Prueba de la aplicación]

## Requisitos previos

Antes de comenzar este tutorial, debe haber realizado los siguientes tutoriales de Servicios móviles:

-   [Introducción a la autenticación][Introducción a la autenticación]
    Agrega un requisito de inicio de sesión a la aplicación de ejemplo TodoList.

-   [Tutorial sobre API personalizadas][Tutorial sobre API personalizadas]
    Muestra cómo llamar a una API personalizada.

## <a name="generate-key"></a>Generación de una clave de acceso para el registro de la aplicación en AAD

Durante el tutorial [Introducción a la autenticación][Introducción a la autenticación], creó un registro para la aplicación integrada cuando realizó el paso [Registro para usar un inicio de sesión de Azure Active Directory][Registro para usar un inicio de sesión de Azure Active Directory]. En esta sección, generará una clave que se usará cuando se lea información de directorios con el identificador de cliente de esa aplicación integrada.

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]

## <a name="create-api"></a>Creación de una API GetUserInfo personalizada

En esta sección, creará la API personalizada GetUserInfo que usará la [API REST Graph][API REST Graph] para recuperar de AAD más información sobre el usuario.

Si nunca ha usado API personalizadas con Servicios móviles, debería revisar el [Tutorial sobre API personalizadas][Tutorial sobre API personalizadas] antes de realizar esta sección.

1.  En el [Portal de administración de Aure][Portal de administración de Aure], cree la nueva API personalizada GetUserInfo para su servicio móvil y establezca el permiso **Solo usuarios autenticados** para el método get.

    ![][0]

2.  Abra el editor de scripts para la nueva API GetUserInfo y agregue las variables siguientes al principio del script.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;

3.  Agregue la siguiente definición para la función `getAADToken`.

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

    Dados los valores de *tenant\_domain* (dominio del inquilino), *client id* (identificador del cliente) de la aplicación integrada y *key* (clave) de la aplicación, esta función proporciona un token de acceso a Graph que se usa para leer información de directorios.

4.  Agregue la siguiente función `getUser`, que usa la API Graph para devolver la información del usuario.

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

    Esta función es un contenedor ligero en torno al extremo de [obtención de usuarios][obtención de usuarios] de la API REST Graph. Utiliza el token de acceso de Graph para obtener la información del usuario usando el identificador de objeto del usuario.

5.  Actualice el método `get` exportado como se indica a continuación para devolver la información del usuario usando otras funciones.

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

## <a name="update-app"></a>Actualización de la aplicación para que use GetUserInfo

En esta sección, va a actualizar el método `AuthenticateAsync` que implementó en el tutorial [Introducción a la autenticación][Introducción a la autenticación] para que llame a la API personalizada y devuelva más información sobre el usuario de AAD.

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]

## <a name="test-app"></a>Prueba de la aplicación

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]

## <a name="next-steps"></a>Pasos siguientes

En el próximo tutorial, [Control de acceso basado en roles en Servicios móviles y Azure Active Directory][Control de acceso basado en roles en Servicios móviles y Azure Active Directory], usará control de acceso basado en roles con Azure Active Directory (AAD) para comprobar la pertenencia a grupos antes de permitir el acceso.

<!-- Anchors. --> 
<!-- Images --> 
<!-- URLs. -->

  [API Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Generación de una clave de acceso para el registro de la aplicación en AAD]: #generate-key
  [Creación de una API GetUserInfo personalizada]: #create-api
  [Actualización de la aplicación para usar la API personalizada]: #update-app
  [Prueba de la aplicación]: #test-app
  [Tutorial sobre API personalizadas]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
  [Registro para usar un inicio de sesión de Azure Active Directory]: /es-es/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
  [API REST Graph]: http://msdn.microsoft.com/es-es/library/azure/hh974478.aspx
  [Portal de administración de Aure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png
  [obtención de usuarios]: http://msdn.microsoft.com/es-es/library/azure/dn151678.aspx
  [mobile-services-aad-graph-info-update-app]: ../includes/mobile-services-aad-graph-info-update-app.md
  [mobile-services-aad-graph-info-test-app]: ../includes/mobile-services-aad-graph-info-test-app.md
  [Control de acceso basado en roles en Servicios móviles y Azure Active Directory]: /es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/
