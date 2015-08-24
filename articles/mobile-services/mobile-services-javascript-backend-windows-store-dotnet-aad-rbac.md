<properties 
	pageTitle="Control de acceso basado en rol en Servicios móviles y Azure Active Directory (Tienda Windows) | Microsoft Azure" 
	description="Obtenga información acerca de cómo controlar el acceso basado en roles de Azure Active Directory en su aplicación de la Tienda Windows." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/15/2015" 
	ms.author="wesmc"/>

# Control de acceso basado en roles en Servicios móviles y Azure Active Directory

[AZURE.INCLUDE [mobile-services-selector-rbac](../../includes/mobile-services-selector-rbac.md)]

##Información general

El control de acceso basado en roles (RBAC) es la práctica de asignar permisos a roles que los usuarios pueden mantener, definiendo límites con precisión en lo que ciertas clases de usuarios pueden y no pueden hacer. Este tutorial le enseñará cómo agregar RBAC básico a Servicios móviles de Azure.

En este tutorial, se muestra el control de acceso basado en roles comprobando la pertenencia de cada usuario al grupo Sales definido en Azure Active Directory (AAD). La comprobación del acceso se realizará con JavaScript en el back-end del servicio móvil mediante la [API Graph] para Azure Active Directory. Solamente a los usuarios que pertenezcan al rol Sales se les permitirá consultar los datos.


>[AZURE.NOTE]La finalidad de este tutorial es ampliar sus conocimientos sobre autenticación para incluir prácticas de autorización. Debe haber completado antes el tutorial [Incorporación de autenticación a la aplicación de Servicios móviles] usando el proveedor de autenticación de Azure Active Directory. Este tutorial continúa la actualización de la aplicación TodoItem que se usa en el tutorial [Incorporación de autenticación a la aplicación de Servicios móviles].

##Requisitos previos

Este tutorial requiere lo siguiente:

* Visual Studio 2013 en Windows 8.1.
* Realización del tutorial [Incorporación de autenticación a la aplicación de Servicios Móviles] con el proveedor de autenticación de Azure Active Directory.
* Finalización del tutorial [Almacenamiento de scripts de servidor] para familiarizarse con el uso de un repositorio Git para almacenar scripts de servidor.
 


##Creación de un grupo Sales con suscripción

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../../includes/mobile-services-aad-rbac-create-sales-group.md)]


##Generación de una clave para la aplicación integrada


Durante el tutorial [Incorporación de autenticación a la aplicación de Servicios móviles], creó un registro para la aplicación integrada cuando realizó el paso [Registro para usar un inicio de sesión de Azure Active Directory]. En esta sección, generará una clave que se usará cuando se lea información de directorios con el identificador de cliente de esa aplicación integrada.

Si ha realizado el tutorial [Acceso a información de Azure Active Directory Graph], ya ha completado este paso y puede omitir esta sección.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]





##Adición de un script compartido al servicio móvil que comprueba la suscripción

En esta sección utilizará Git para implementar un archivo de script compartido denominado *rbac.js* en el servicio móvil. Este archivo de script compartido contendrá las funciones que usan la [API Graph] para comprobar la suscripción al grupo del usuario.

Si no está familiarizado con la implementación de scripts en el servicio móvil con Git, revise el tutorial [Almacenamiento de scripts de servidor] antes de completar esta sección.

1. Cree un nuevo archivo de script llamado *rbac.js* en el directorio *./service/shared/* del repositorio local para el servicio móvil.
2. Agregue el siguiente script a la parte superior del archivo que define la función `getAADToken`. Dados los valores de *tenant\_domain*, *client id* de la aplicación integrada y *key* de la aplicación, esta función proporciona un token de acceso a Graph que se usa para leer información de directorios.

    >[AZURE.NOTE]Debe almacenar en caché el token en lugar de crear uno nuevo con cada comprobación de acceso. A continuación, actualice la memoria caché cuando los intentos de uso del token arrojen una respuesta 401 Authentication\_ExpiredToken como se indica en la [referencia de errores de la API Graph]. Esto no se muestra en el código siguiente por simplificar, pero paliará el tráfico extra de red para su instancia de Active Directory.

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


3. Agregue el siguiente código a *rbac.js* para definir la función `getGroupId`. Esta función utiliza el token de acceso para obtener el identificador de grupo basándose en el nombre de grupo usado en un filtro.
 
    >[AZURE.NOTE]Este código busca el grupo de Active Directory por el nombre. En muchos casos, puede ser mejor almacenar el identificador de grupo como un valor de aplicación de servicio móvil y usar solamente ese identificador de grupo. porque el nombre del grupo puede cambiar, pero el identificador permanece igual. Sin embargo, si se cambia el nombre de un grupo, suele haber al menos un cambio en el ámbito del rol que puede requerir también una actualización del código del servicio móvil.

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


4. Agregue el siguiente código a *rbac.js* para definir la función `isMemberOf` que llama al extremo [IsMemberOf] de la API Graph REST.

    Esta función es un contenedor delgado alrededor del extremo [IsMemberOf] de la API Graph REST. Usa el token de acceso Graph para comprobar si el identificador de objeto de directorio del usuario tiene suscripción en el grupo basándose en dicho identificador.

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

    

7. Agregue la siguiente función `checkGroupMembership` exportada a *rbac.js*.

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

8. Guarde los cambios realizados en *rbac.js*.

##Adición de comprobación del acceso basado en rol a las operaciones de base de datos


Cuando completó el tutorial [Incorporación de autenticación a la aplicación de Servicios móviles], debió haber establecido las operaciones de tabla para solicitar autenticación tal y como se muestra a continuación.

![][3]

Con cada operación de base de datos que requiere autenticación, podemos agregar scripts que usan el objeto de usuario para comprobaciones de acceso.

En los pasos siguientes se muestra cómo implementar el control de acceso basado en roles usando scripts en cada operación de tabla del servicio móvil. Para cada operación de tabla, se ejecuta un script que usa el script *rbac.js* compartido.

1. Agregue un nuevo archivo de script llamado *todoitem.insert.js* al directorio *./service/table/* del repositorio Git local para el servicio móvil. Pegue el siguiente script en ese archivo.

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

2. Agregue un nuevo archivo de script llamado *todoitem.read.js* al directorio *./service/table/* del repositorio Git local para el servicio móvil. Pegue el siguiente script en ese archivo.

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

3. Agregue un nuevo archivo de script llamado *todoitem.update.js* al directorio *./service/table/* del repositorio Git local para el servicio móvil. Pegue el siguiente script en ese archivo.

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

4. Agregue un nuevo archivo de script llamado *todoitem.delete.js* al directorio *./service/table/* del repositorio Git local para el servicio móvil. Pegue el siguiente script en ese archivo.

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

5. En la interfaz de línea de comandos al repositorio Git, agregue un seguimiento para los archivos de script que agregó ejecutando el siguiente comando.

        git add .

6. En la interfaz de línea de comandos al repositorio Git, confirme las actualizaciones ejecutando el siguiente comando.

        git commit -m "Added role based access control to table operations"
  
7. En la interfaz de línea de comandos al repositorio Git, implemente las actualizaciones en el repositorio Git local para el servicio móvil ejecutando el siguiente comando. Este comando asume que hizo las actualizaciones en la rama *master*.

        git push origin master

8. En el [Portal de administración de Azure] debe poder navegar a las operaciones de tabla del servicio móvil y ver las actualizaciones en su lugar tal y como se muestra a continuación.

    ![][4]

##Prueba del acceso del cliente

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../../includes/mobile-services-aad-rbac-test-app.md)]







<!-- Images -->
[0]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/users.png
[1]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/group-membership.png
[2]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group.png
[3]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/table-perms.png
[4]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/insert-table-op-view.png
[5]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/sales-group-id.png
[6]: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/client-id-and-key.png

<!-- URLs. -->
[Incorporación de autenticación a la aplicación de Servicios móviles]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Portal de administración de Azure]: https://manage.windowsazure.com/
[Directory Sync Scenarios]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Almacenamiento de scripts de servidor]: mobile-services-store-scripts-source-control.md
[Registro para usar un inicio de sesión de Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[API Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[referencia de errores de la API Graph]: http://msdn.microsoft.com/library/azure/hh974480.aspx
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
[Acceso a información de Azure Active Directory Graph]: mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info.md

<!---HONumber=August15_HO7-->