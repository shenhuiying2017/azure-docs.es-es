---
title: "Introducción a la API web de Node.js de Azure AD | Microsoft Docs"
description: "Compilación de una API web de REST de Node.js que se integre con Azure AD para su autenticación."
services: active-directory
documentationcenter: nodejs
author: craigshoemaker
manager: mtillman
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: cshoe
ms.custom: aaddev
ms.openlocfilehash: 2ed0874b79601976e0d5a73fe82c7c03331d9bea
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-nodejs-web-api-getting-started"></a>Introducción a la API web de Node.js de Azure AD

En este artículo se indica cómo proteger un punto de conexión de API de [Restify](http://restify.com/) con [Passport](http://passportjs.org/) mediante el módulo [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) para controlar la comunicación con Azure Active Directory (AAD). 

Dentro de este tutorial se describen las cuestiones relacionadas con la protección de los puntos de conexión de API. Las cuestiones relacionadas con el inicio de sesión y la conservación de los tokens de autenticación no se implementan aquí y son responsabilidad de una aplicación cliente. Para más información sobre una implementación de cliente, revise [Inicio y cierre de sesión de aplicación web de Node.js con Azure AD](active-directory-devquickstarts-openidconnect-nodejs.md).

El código de ejemplo completo asociado a este artículo está disponible en [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="create-the-sample-project"></a>Creación del proyecto de ejemplo
Esta aplicación de servidor requiere que algunas dependencias de paquetes sean compatibles con Restify y Passport, así como la información de la cuenta que se pasa a AAD.

Para empezar, agregue el código siguiente en un archivo denominado `package.json`:

```Shell
{
  "name": "node-aad-demo",
  "version": "0.0.1",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "passport": "0.4.0",
    "passport-azure-ad": "3.0.8",
    "restify": "6.0.1",
    "restify-plugins": "1.6.0"
  }
}
```

Una vez que se haya creado `package.json`, ejecute `npm install` en el símbolo del sistema para instalar las dependencias de paquetes. 

### <a name="configure-the-project-to-use-active-directory"></a>Configuración del proyecto que va a usar Active Directory

Para empezar a configurar la aplicación, hay algunos valores específicos de la cuenta que se pueden obtener de la CLI de Azure. La manera más fácil de empezar a trabajar con la CLI es usar Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

En Cloud Shell, escriba los comandos siguientes: 

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

Entre los [argumentos](/cli/azure/ad/app?view=azure-cli-latest#az_ad_app_create) del comando `create` se incluyen:

| Argumento  | DESCRIPCIÓN |
|---------|---------|
|`display-name` | Nombre descriptivo del registro |
|`homepage` | Dirección URL donde los usuarios pueden iniciar sesión para utilizar la aplicación |
|`identifier-uris` | Identificadores URI únicos separados por espacios que puede usar Azure AD para esta aplicación |

Para poder conectarse a Azure Active Directory, necesita la siguiente información:

| NOMBRE  | DESCRIPCIÓN | Nombre de la variable en el archivo de configuración |
| ------------- | ------------- | ------------- |
| Nombre del inquilino  | El [nombre del inquilino](active-directory-howto-tenant.md) que desea usar para la autenticación | `tenantName`  |
| Id. de cliente  | El identificador de cliente es el término de OAuth que se usa para el _identificador de la aplicación_ de AAD. |  `clientID`  |

En la respuesta de registro en Azure Cloud Shell, copie el valor `appId` y cree un nuevo archivo denominado `config.js`. A continuación, agregue el código siguiente y reemplace los valores por los tokens entre corchetes:

```JavaScript
const tenantName    = //<YOUR_TENANT_NAME>;
const clientID      = //<YOUR_APP_ID_FROM_CLOUD_SHELL>;
const serverPort    = 3000;

module.exports.serverPort = serverPort;

module.exports.credentials = {
  identityMetadata: `https://login.microsoftonline.com/${tenantName}.onmicrosoft.com/.well-known/openid-configuration`, 
  clientID: clientID
};
```
Para más información sobre las opciones de configuración individuales, revise la documentación del módulo [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage).

## <a name="implement-the-server"></a>Implementación del servidor
El módulo [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) ofrece dos estrategias de autenticación: las estrategias [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) y de [portador](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy). El servidor que se implementa en este artículo utiliza la estrategia de portador para proteger el punto de conexión de la API.

### <a name="step-1-import-dependencies"></a>Paso 1: Dependencias de importación
Cree un nuevo archivo denominado `app.js` y pegue el texto siguiente:

```JavaScript
const
      restify = require('restify')
    , restifyPlugins = require('restify-plugins')
    , passport = require('passport')
    , BearerStrategy = require('passport-azure-ad').BearerStrategy
    , config = require('./config')
    , authenticatedUserTokens = []
    , serverPort = process.env.PORT || config.serverPort
;
```

En esta sección de código:

- Se hace referencia a los módulos `restify` y `restify-plugins` para configurar un servidor Restify.

- Los módulos `passport` y `passport-azure-ad` son responsables de la comunicación con AAD.

- La variable `config` se inicializa con los valores del archivo `config.js` que se creó en el paso anterior.

- Se crea una matriz para `authenticatedUserTokens` para almacenar los tokens de usuario que se pasan a los puntos de conexión seguros.

- El `serverPort` se define desde el puerto del entorno de proceso o desde el archivo de configuración.

### <a name="step-2-instantiate-an-authentication-strategy"></a>Paso 2: Creación de una instancia de una estrategia de autenticación
Cuando protege un punto de conexión, debe proporcionar una estrategia que es la responsable de determinar si la solicitud actual se origina a partir de un usuario autenticado o no. En este caso la variable `authenticatonStrategy` es una instancia de la clase `passport-azure-ad` `BearerStrategy`. Agregue el siguiente código después de las instrucciones `require`.

```JavaScript
const authenticationStrategy = new BearerStrategy(config.credentials, (token, done) => {
    let currentUser = null;

    let userToken = authenticatedUserTokens.find((user) => {
        currentUser = user;
        user.sub === token.sub;
    });

    if(!userToken) {
        authenticatedUserTokens.push(token);
    }

    return done(null, currentUser, token);
});
```
Esta implementación usa el registro automático mediante la adición de tokens de autenticación en la matriz `authenticatedUserTokens` si aún no existen.

Una vez que se crea una nueva instancia de la estrategia, debe pasarla a Passport mediante el método `use`. Agregue el código siguiente a `app.js` para utilizar la estrategia en Passport.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>Paso 3: Configuración del servidor
Con la estrategia de autenticación definida, ahora puede configurar el servidor de Restify con algunas opciones básicas y establecer que use Passport para la seguridad.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directroy with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Este servidor se inicializa y se configura para que analice encabezados de autorización y, a continuación, se configura para que use Passport.


### <a name="step-4-define-routes"></a>Paso 4: Definición de rutas
Ahora se pueden definir rutas y decidir cuáles se van a proteger con AAD. Este proyecto incluye dos rutas en las que el nivel raíz está abierto y la ruta `/api` está configurada para solicitar la autenticación.

En `app.js` agregue el código siguiente para la ruta de nivel raíz:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

La ruta raíz permite todas las solicitudes a través de ella y devuelve un mensaje que incluye un comando para probar la ruta `/api`. Por el contrario, la ruta `/api` está bloqueada mediante [`passport.authenticate`](http://passportjs.org/docs/authenticate). Agregue el siguiente código después de la ruta raíz.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Esta configuración solo permite las solicitudes autenticadas que incluyen un acceso de token de portador a `/api`. La opción de `session: false` se utiliza para deshabilitar las sesiones y solicitar que se pase un token con cada solicitud a la API.

Por último, el servidor se configura para que escuche en el puerto configurado mediante una llamada al método `listen`.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Ejecución del ejemplo

Ahora que se ha implementado el servidor, puede iniciarlo abriendo un símbolo del sistema y escribiendo:

```Shell
npm start
```

Con el servidor en ejecución, puede enviar una solicitud a este para probar los resultados. Para mostrar la respuesta de la ruta raíz, abra un shell de Bash y escriba el código siguiente:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/
```

Si ha configurado el servidor correctamente, la respuesta debe ser parecida a esta:

```Shell
HTTP/1.1 200 OK
Server: Azure Active Directroy with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

A continuación, puede probar la ruta que requiere autenticación. Para ello, escriba el siguiente comando en el shell de bash:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/api
```

Si ha configurado el servidor correctamente, el servidor debería responder con un estado `Unauthorized`.

```Shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directroy with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```
Ahora que ha creado una API segura, puede implementar un cliente que puede pasar tokens de autenticación a la API.

## <a name="next-steps"></a>pasos siguientes
Como se mencionó en la introducción, debe implementar un cliente homólogo para conectarse al servidor que controla el inicio de sesión, el cierre de sesión y la administración de tokens. Para obtener ejemplos basados en código, puede hacer referencia a las aplicaciones de cliente en [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) y [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android). Para obtener un tutorial paso a paso, consulte el artículo siguiente:

> [!div class="nextstepaction"]
> [Inicio y cierre de sesión de aplicación web de Node.js con Azure AD](active-directory-devquickstarts-openidconnect-nodejs.md)