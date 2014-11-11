<properties linkid="develop-mobile-tutorials-js-rbac-with-aad" urlDisplayName="Role Based Access Control with Azure Active Directory" pageTitle="Role Based Access Control in Mobile Services and Azure Active Directory (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to control access based on Azure Active Directory roles in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Role Based Access Control in Mobile Services and Azure Active Directory" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/21/2014" ms.author="wesmc" />

# Control de acceso basado en roles en Servicios móviles y Azure Active Directory

<div class="dev-center-tutorial-selector sublanding">
    <a href="/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/" title="C# para Tienda Windows" class="current">C# para Tienda Windows</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/es-es/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/" title="Back-end de .NET">Back-end de .NET</a> |
    <a href="/es-es/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/" title="Back-end de JavaScript" class="current">Back-end de JavaScript</a>
</div>

El control de acceso basado en roles (RBAC) es la práctica de asignar permisos a roles que los usuarios pueden mantener, definiendo límites con precisión en lo que ciertas clases de usuarios pueden y no pueden hacer. Este tutorial le enseñará cómo agregar RBAC básico a Servicios móviles de Azure.

En este tutorial se mostrará el control de acceso basado en roles, comprobando la suscripción de cada usuario a un grupo Sales definido en Azure Active Directory (AAD). La comprobación del acceso se realizará con JavaScript en el back-end del servicio móvil mediante la [API Graph][API Graph] para Azure Active Directory. Solamente a los usuarios que pertenezcan al rol Sales se les permitirá consultar los datos.

> [WACOM.NOTE] El objetivo de este tutorial es ampliar su conocimiento sobre la autenticación para incluir prácticas de autorización. Se espera que primero complete el tutorial [Introducción a la autenticación][Introducción a la autenticación] usando el proveedor de autenticación de Azure Active Directory. Este tutorial continua para actualizar la aplicación TodoItem usada en el tutorial [Introducción a la autenticación][Introducción a la autenticación].

Este tutorial le guiará a través de los siguientes pasos:

1.  [Creación de un grupo Sales con suscripción][Creación de un grupo Sales con suscripción]
2.  [Generación de una clave para la aplicación integrada][Generación de una clave para la aplicación integrada]
3.  [Adición de un script compartido que comprueba la suscripción][Adición de un script compartido que comprueba la suscripción]
4.  [Adición de comprobación del acceso basado en rol a las operaciones de base de datos][Adición de comprobación del acceso basado en rol a las operaciones de base de datos]
5.  [Prueba del acceso del cliente][Prueba del acceso del cliente]

Este tutorial requiere lo siguiente:

-   Visual Studio 2013 en Windows 8.1.
-   Finalización del tutorial [Introducción a la autenticación][Introducción a la autenticación] usando el proveedor de autenticación de Azure Active Directory.
-   Finalización del tutorial [Almacenamiento de scripts de servidor][Almacenamiento de scripts de servidor] para familiarizarse con el uso de un repositorio Git para almacenar scripts de servidor.

## <a name="create-group"></a>Creación de un grupo Sales con suscripción

[WACOM.INCLUDE [mobile-services-aad-rbac-create-sales-group](../includes/mobile-services-aad-rbac-create-sales-group.md)]

## <a name="generate-key"></a>Generación de una clave para la aplicación integrada

Durante el tutorial [Introducción a la autenticación][Introducción a la autenticación], creó un registro para la aplicación integrada cuando completó el paso [Registro para usar un inicio de sesión de Azure Active Directory][Registro para usar un inicio de sesión de Azure Active Directory]. En esta sección generará una clave para utilizar cuando lea información de directorio con el identificador de cliente de la aplicación integrada.

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]

## <a name="add-shared-script"></a>Adición de un script compartido al servicio móvil que comprueba la suscripción

En esta sección utilizará Git para implementar un archivo de script compartido llamado *rbac.js* en el servicio móvil. Este archivo de script compartido continuará las funciones que usan la [API Graph][API Graph] para comprobar la suscripción al grupo del usuario.

Si no está familiarizado con la implementación de scripts en el servicio móvil con Git, revise el tutorial [Almacenamiento de scripts de servidor][Almacenamiento de scripts de servidor] antes de completar esta sección.

1.  Cree un nuevo archivo de script llamado *rbac.js* en el directorio *./service/shared/* del repositorio local para el servicio móvil.
2.  Agregue el siguiente script a la parte superior del archivo que define la función `getAADToken`. Dado el *tenant\_domain*, el *identificador de cliente* de la aplicación integrada y la *clave* de la aplicación, esta función proporciona un token de acceso Graph usado para leer información de directorio.

    > [WACOM.NOTE] Debe almacenar en caché el token en lugar de crear uno nuevo con cada comprobación de acceso. A continuación, actualice la memoria caché cuando los intentos de uso del token arrojen una respuesta 401 Authentication\_ExpiredToken como se indica en la [referencia de errores de la API Graph][referencia de errores de la API Graph]. Por simplificar, esto no se muestra en el código que aparece a continuación, pero aliviará el tráfico de red adicional que sufre su Active Directory.

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;
        exports.SalesGroup = appSettings.AAD_GROUP_ID;

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

3.  Agregue el siguiente código a *rbac.js* para definir la función `getGroupId`. Esta función utiliza el token de acceso para obtener el identificador de grupo basándose en el nombre de grupo usado en un filtro.

    > [WACOM.NOTE] Este código busca el grupo de Active Directory por nombre. En muchos casos, puede ser mejor almacenar el identificador de grupo como un valor de aplicación de servicio móvil y usar solamente ese identificador de grupo. El motivo es que el nombre del grupo puede cambiar pero el identificador permanece igual. Sin embargo, con un cambio de nombre de grupo normalmente hay un cambio en el ámbito del rol que también puede requerir una actualización en el código del servicio móvil.

        function getGroupId(groupname, accessToken, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/groups" + 
                      "?$filter=displayName%20eq%20'" + groupname + "'" + 
                  "&api-version=2013-04-05" ,
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + accessToken,
                    "Content-Type": "application/json",
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value[0].objectId);
            });
        }

4.  Agregue el siguiente código a *rbac.js* para definir la función `isMemberOf` que llama al extremo [IsMemberOf][IsMemberOf] de la API Graph REST.

    Esta función es un contenedor delgado alrededor del extremo [IsMemberOf][IsMemberOf] de la API Graph REST. Usa el token de acceso Graph para comprobar si el identificador de objeto de directorio del usuario tiene suscripción en el grupo basándose en dicho identificador.

        function isMemberOf(access_token, userObjectId, groupObjectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/isMemberOf" + "?api-version=2013-04-05",
                method: 'POST',
                headers: {
                    "Authorization": "Bearer " + access_token,
                    "Content-Type": "application/json",
                },
                body: JSON.stringify({
                "groupId": groupObjectId,
                "memberId": userObjectId
                })
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).value);
            });
        };

5.  Agregue la siguiente función exportada `checkGroupMembership` a *rbac.js*.

    Esta función encapsula el uso de las otras funciones de script y se exporta desde el script compartido para que pueda recibir llamadas de otros scripts para realizar las comprobaciones de acceso reales. Dado el objeto de usuario de servicio móvil, y el identificador de grupo, el script recuperará el identificador de objeto de Azure Active Directory para la identidad del usuario y comprobará la suscripción al grupo.

        exports.checkGroupMembership = function (user, groupName, callback) {
            user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) callback(err);
                else getGroupId(groupName, access_token, function (err, groupId) { 
                            if (err) callback(err);
                            else isMemberOf(access_token, objectId, groupId, function (err, isInGroup) {
                                if (err) errorHandler(err);
                                else callback(null, isInGroup);
                            });
                        });
                    });
                },
                error: callback
            });
        }

6.  Guarde los cambios realizados en *rbac.js*.

## <a name="add-access-checking"></a>Adición de comprobación del acceso basado en rol a las operaciones de base de datos

Cuando completó el tutorial [Introducción a la autenticación][Introducción a la autenticación], debió haber establecido las operaciones de tabla para solicitar autenticación tal y como se muestra a continuación.

![][0]

Con cada operación de base de datos que requiere autenticación, podemos agregar scripts que usan el objeto de usuario para comprobaciones de acceso.

En los pasos siguientes se muestra cómo implementar el control de acceso basado en roles usando scripts en cada operación de tabla del servicio móvil. Para cada operación de tabla, se ejecuta un script que usa el script *rbac.js* compartido.

1.  Agregue un nuevo archivo de script llamado *todoitem.insert.js* al directorio *./service/table/* del repositorio Git local para el servicio móvil. Pegue el siguiente script en ese archivo.

        function insert(item, user, request) {

            var RBAC = require('../shared/rbac.js');

            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

2.  Agregue un nuevo archivo de script llamado *todoitem.read.js* al directorio *./service/table/* del repositorio Git local para el servicio móvil. Pegue el siguiente script en ese archivo.

        function read(query, user, request) {

            var RBAC = require('../shared/rbac.js');

            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

3.  Agregue un nuevo archivo de script llamado *todoitem.update.js* al directorio *./service/table/* del repositorio Git local para el servicio móvil. Pegue el siguiente script en ese archivo.

        function update(item, user, request) {

            var RBAC = require('../shared/rbac.js');

            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

4.  Agregue un nuevo archivo de script llamado *todoitem.delete.js* al directorio *./service/table/* del repositorio Git local para el servicio móvil. Pegue el siguiente script en ese archivo.

        function del(id, user, request) {

            var RBAC = require('../shared/rbac.js');

            RBAC.checkGroupMembership(user, "sales", function(err, isInGroup) {
                if (err) request.respond(err);
                else if (!isInGroup) request.respond(statusCodes.UNAUTHORIZED, null);
                else {
                    request.execute();
                }
            });
        }

5.  En la interfaz de línea de comandos al repositorio Git, agregue un seguimiento para los archivos de script que agregó ejecutando el siguiente comando.

        git add .

6.  En la interfaz de línea de comandos al repositorio Git, confirme las actualizaciones ejecutando el siguiente comando.

        git commit -m "Added role based access control to table operations"

7.  En la interfaz de línea de comandos al repositorio Git, implemente las actualizaciones en el repositorio Git local para el servicio móvil ejecutando el siguiente comando. Este comando asume que hizo las actualizaciones en la rama *master*.

        git push origin master

8.  En el [Portal de administración de Azure][Portal de administración de Azure] debe poder navegar a las operaciones de tabla del servicio móvil y ver las actualizaciones en su lugar tal y como se muestra a continuación.

    ![][1]

## <a name="test-client"></a>Prueba del acceso del cliente

[WACOM.INCLUDE [mobile-services-aad-rbac-test-app](../includes/mobile-services-aad-rbac-test-app.md)]

<!-- Anchors. --> <!-- Images --> <!-- URLs. -->

  [API Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
  [Introducción a la autenticación]: /es-es/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Creación de un grupo Sales con suscripción]: #create-group
  [Generación de una clave para la aplicación integrada]: #generate-key
  [Adición de un script compartido que comprueba la suscripción]: #add-shared-script
  [Adición de comprobación del acceso basado en rol a las operaciones de base de datos]: #add-access-checking
  [Prueba del acceso del cliente]: #test-client
  [Almacenamiento de scripts de servidor]: /es-es/documentation/articles/mobile-services-store-scripts-source-control/
  [mobile-services-aad-rbac-create-sales-group]: ../includes/mobile-services-aad-rbac-create-sales-group.md
  [Registro para usar un inicio de sesión de Azure Active Directory]: /es-es/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
  [referencia de errores de la API Graph]: http://msdn.microsoft.com/es-es/library/azure/hh974480.aspx
  [IsMemberOf]: http://msdn.microsoft.com/es-es/library/azure/dn151601.aspx
  [0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/table-perms.png
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/insert-table-op-view.png
  [mobile-services-aad-rbac-test-app]: ../includes/mobile-services-aad-rbac-test-app.md
