---
title: "Azure AD B2C: Protección de una API web mediante Node.js | Microsoft Docs"
description: "Creación de una API web Node.js que acepta tokens de un inquilino B2C"
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: fc2b9af8-fbda-44e0-962a-8b963449106a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: xerners
ms.openlocfilehash: 3a0249f2f7dfd76d89cbf497376f53fe06c250c3
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="azure-ad-b2c-secure-a-web-api-by-using-nodejs"></a>Azure AD B2C: Protección de una API web mediante Node.js
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Con Azure Active Directory (Azure AD) B2C, puede proteger una API web mediante el uso de tokens de acceso de OAuth 2.0. Estos tokens permiten a las aplicaciones cliente que usan Azure AD B2C autenticarse en la API. En este artículo se muestra cómo crear una API de "lista de tareas pendientes" que permita a los usuarios agregar y enumerar tareas. La API web se protege con Azure AD B2C y solo permite a los usuarios autenticados administrar sus listas de tareas pendientes.

> [!NOTE]
> Este ejemplo se escribió de modo que fuera posible conectarse a él con nuestra [aplicación de ejemplo B2C para iOS](active-directory-b2c-devquickstarts-ios.md). Primero realice el tutorial actual y luego continúe con ese ejemplo.
>
>

**Passport** es middleware de autenticación para Node.js. Passport es flexible y modular, y se puede instalar discretamente en cualquier aplicación web basada en Express o Restify. Un conjunto completo de estrategias admite la autenticación mediante un nombre de usuario y contraseña, Facebook, Twitter, etc. Hemos desarrollado una estrategia para Azure Active Directory (Azure AD). Instale este módulo y, después, agregue el complemento `passport-azure-ad` de Azure AD.

Para seguir este ejemplo, necesita lo siguiente:

1. Registrar una aplicación con Azure AD.
2. Configurar la aplicación para que use el complemento `passport-azure-ad` de Passport.
3. Configurar una aplicación cliente para llamar a la API web "lista de tareas pendientes".

## <a name="get-an-azure-ad-b2c-directory"></a>Obtener un directorio de Azure AD B2C
Para poder usar Azure AD B2C, debe crear un directorio o inquilino.  Un directorio es un contenedor de todos los usuarios, aplicaciones, grupos, etc.  Antes de continuar, si no tiene un directorio B2C, [créelo](active-directory-b2c-get-started.md) .

## <a name="create-an-application"></a>Creación de una aplicación
A continuación, debe crear una aplicación en su directorio de B2C, que ofrece a Azure AD información que necesita para comunicarse de forma segura con su aplicación. En este caso, tanto la aplicación cliente como la API web se representan mediante un **identificador de la aplicación**único, ya que conforman una aplicación lógica. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

* Incluir una **aplicación web/API web** en la aplicación.
* Escribir `http://localhost/TodoListService` en **URL de respuesta**. Es la dirección URL predeterminada para este ejemplo de código.
* Crear un **secreto de aplicación** para la aplicación y copiarlo. Estos datos se necesitarán más adelante. Tenga en cuenta que para poder usar este valor, es preciso [incluirlo entre secuencias de escape XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) .
* Copiar el **identificador de aplicación** asignado a la aplicación. Estos datos se necesitarán más adelante.

## <a name="create-your-policies"></a>Crear sus directivas
En Azure AD B2C, cada experiencia del usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). Esta aplicación contiene dos experiencias de identidad: registrarse e iniciar sesión. Debe crear una directiva de cada tipo, como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md#create-a-sign-up-policy).  Cuando cree las tres directivas, asegúrese de:

* Elegir el **nombre para mostrar** y los restantes atributos de registro en la directiva de registro.
* Elija las notificaciones de aplicación de **nombre para mostrar** e **id. de objeto** de cada directiva.  Puede elegir también otras notificaciones.
* Copiar el **Nombre** de cada directiva después de crearla. Debe tener el prefijo `b2c_1_`.  Los nombres de directiva se necesitarán más adelante.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Una vez creadas las tres directivas, está listo para compilar la aplicación.

Para obtener información acerca del funcionamiento de las directivas en Azure AD B2C, comience por el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Descargar el código
El código de este tutorial [se mantiene en GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS). Para generar el ejemplo a medida que avanza, puede [descargar un proyecto de esqueleto como archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip). También puede clonar el esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

La aplicación completada también estará [disponible como archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip) o en la rama `complete` del mismo repositorio.

## <a name="download-nodejs-for-your-platform"></a>Descarga de Node.js para su plataforma
Para usar correctamente este ejemplo, se necesita una instalación en funcionamiento de Node.js.

Instale Node.js desde [nodejs.org](http://nodejs.org).

## <a name="install-mongodb-for-your-platform"></a>Instalación de MongoDB en la plataforma
Para usar correctamente este ejemplo, se necesita una instalación en funcionamiento de MongoDB. MongoDB se usa para que la API de REST sea persistente en las instancias del servidor.

Instale MongoDB desde [mongodb.org](http://www.mongodb.org).

> [!NOTE]
> En este tutorial se asume que usa la instalación predeterminada y los puntos de conexión de servidor de MongoDB, que en el momento de escribir este artículo es: `mongodb://localhost`.
>
>

## <a name="install-the-restify-modules-in-your-web-api"></a>Instalación de los módulos Restify en su API web
Restify se usa para crear la API de REST. Restify es un marco de aplicación de Node.js mínimo y flexible que deriva de Express. Tiene un sólido conjunto de características para crear API de REST sobre Connect.

### <a name="install-restify"></a>Instalar Restify
En la línea de comandos, cambie el directorio a `azuread`. Si el directorio `azuread` no existe, créelo.

`cd azuread` o `mkdir azuread;`

Escriba el comando siguiente:

`npm install restify`

Este comando instala Restify.

#### <a name="did-you-get-an-error"></a>¿Ha recibido un error?
En algunos sistemas operativos, cuando se utiliza `npm`, se puede recibir el error `Error: EPERM, chmod '/usr/local/bin/..'` y una solicitud de que ejecute la cuenta como administrador. Si aparece este problema, utilice el comando `sudo` para ejecutar `npm` a un nivel de privilegio más elevado.

#### <a name="did-you-get-a-dtrace-error"></a>¿Recibió un error de DTrace?
Al instalar Restify, puede ver algo parecido a esto:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

Restify proporciona un mecanismo eficaz para rastrear llamadas a REST mediante DTrace. Sin embargo, muchos sistemas operativos no tienen DTrace disponible. Puede omitir estos errores con seguridad.

El resultado del comando debe ser similar al siguiente:

    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)

## <a name="install-passport-in-your-web-api"></a>Instalación de Passport en la API web
En la línea de comandos, cambie el directorio a `azuread`, en caso de que no esté en él.

Instale Passport con el comando siguiente:

`npm install passport`

El resultado de este comando debe ser similar al siguiente:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>Incorporación de passport-azuread a la API web
A continuación, agregue la estrategia de OAuth mediante `passport-azuread`, un conjunto de estrategias que conectan Azure AD a Passport. Utilice esta estrategia para los tokens de portador en el ejemplo de API de REST.

> [!NOTE]
> Aunque OAuth2 proporciona un marco de trabajo en el que se puede emitir cualquier tipo de token conocido, solo ciertos tipos de token tienen un uso generalizado. Los tokens para la protección de puntos de conexión se denominan tokens de portador. Estos tipos de tokens son los más emitidos en OAuth2. Muchas implementaciones asumen que los tokens de portador son el único tipo de token emitido.
>
>

En la línea de comandos, cambie el directorio a `azuread`, en caso de que no esté en él.

Instale el módulo `passport-azure-ad` de Passport mediante el siguiente comando:

`npm install passport-azure-ad`

El resultado de este comando debe ser similar al siguiente:

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## <a name="add-mongodb-modules-to-your-web-api"></a>Incorporación de módulos MongoDB a la API web
En este ejemplo se MongoDB como almacén de datos. Para ello, instale Mongoose, un complemento para administrar modelos y esquemas muy utilizados.

* `npm install mongoose`

## <a name="install-additional-modules"></a>Instalar módulos adicionales
A continuación, instale los demás módulos necesarios.

En la línea de comandos, cambie el directorio a `azuread`, en caso de que no esté ya allí:

`cd azuread`

Instale los módulos en el directorio `node_modules` :

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`

## <a name="create-a-serverjs-file-with-your-dependencies"></a>Creación de un archivo server.js con sus dependencias
El archivo `server.js` proporciona la mayor parte de la funcionalidad de su servidor de API web.

En la línea de comandos, cambie el directorio a `azuread`, en caso de que no esté ya allí:

`cd azuread`

Cree un archivo `server.js` en un editor. Agregue la siguiente información:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Guarde el archivo . Volverá a él más adelante.

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>Creación de un archivo config.js para almacenar la configuración de Azure AD
Este archivo de código pasa los parámetros de configuración del portal de Azure AD al archivo `Passport.js` . Creó estos valores de configuración al agregar la API web al portal en la primera parte del tutorial. Explicamos qué incluir en los valores de estos parámetros después de haber copiado el código.

En la línea de comandos, cambie el directorio a `azuread`, en caso de que no esté ya allí:

`cd azuread`

Cree un archivo `config.js` en un editor. Agregue la siguiente información:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>Valores obligatorios
`clientID`: el id. de cliente de la aplicación Web API.

`IdentityMetadata`: aquí es donde `passport-azure-ad` busca los datos de configuración del proveedor de identidades. También busca las claves para validar los JSON web tokens.

`audience`: el identificador uniforme de recursos (URI) del portal que identifica la aplicación que llama.

`tenantName`: su nombre de inquilino (por ejemplo, **contoso.onmicrosoft.com**).

`policyName`: la directiva con la que desea validar los tokens que entran en el servidor. Debe ser la misma directiva que se usa en la aplicación cliente para iniciar sesión.

> [!NOTE]
> De momento, use las mismas directivas en las instalaciones de cliente y de servidor. Si ya realizó un tutorial y creó estas directivas, no necesita volver a hacerlo. Puesto que completó el tutorial, no necesita configurar nuevas directivas para los tutoriales de cliente en el sitio.
>
>

## <a name="add-configuration-to-your-serverjs-file"></a>Incorporación de la configuración a su archivo server.js
Para leer los valores del archivo `config.js` que ha creado, agregue el archivo `.config` como recurso necesario a su aplicación y, a continuación, establezca las variables globales en las del documento `config.js`.

En la línea de comandos, cambie el directorio a `azuread`, en caso de que no esté ya allí:

`cd azuread`

Abra el archivo `server.js` en un editor. Agregue la siguiente información:

```Javascript
var config = require('./config');
```
Agregue una nueva sección a `server.js` que incluya el siguiente código:

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

A continuación, vamos a agregar varios marcadores de posición para los usuarios que recibimos de las aplicaciones que realizan llamadas.

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

Ahora vamos a crear también nuestro registrador.

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Incorporación del modelo MongoDB y la información del esquema mediante Mongoose
La preparación anterior se amortiza al unir estos tres archivos en un servicio de la API de REST.

Para este tutorial, use MongoDB para almacenar las tareas, como se explicó anteriormente.

En el archivo `config.js` , llamó a la base de datos **tasklist**. Este nombre también lo puso al final de la dirección URL de conexión `mongoose_auth_local` . No es necesario crear esta base de datos por anticipado en MongoDB. Crea la base de datos la primera vez que se ejecuta la aplicación de servidor.

Después de indicar al servidor la base de datos MongoDB que se usará, debemos escribir algo de código adicional para crear el modelo y el esquema para las tareas del servidor.

### <a name="expand-the-model"></a>Expansión del modelo
Este modelo de esquema es muy sencillo. Se puede expandir según sea necesario.

`owner`: quién está asignado a la tarea. Este objeto es de tipo **cadena**.  

`Text`: la propia tarea. Este objeto es de tipo **cadena**.

`date`: la fecha en que vence la tarea. Este objeto es de tipo **datetime**.

`completed`: si la tarea está completa. Este objeto es de tipo **booleano**.

### <a name="create-the-schema-in-the-code"></a>Creación del esquema en el código
En la línea de comandos, cambie el directorio a `azuread`, en caso de que no esté ya allí:

`cd azuread`

Abra el archivo `server.js` en un editor. Agregue la siguiente información debajo de la entrada de configuración:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Primero cree el esquema y, después, cree un objeto de modelo que utilice para almacenar los datos en todo el código al definir sus **rutas**.

## <a name="add-routes-for-your-rest-api-task-server"></a>Incorporación de rutas para el servidor de la tarea de API de REST
Ahora que tiene un modelo de base de datos con el que trabajar, agregue las rutas que usa para su servidor de API de REST.

### <a name="about-routes-in-restify"></a>Información acerca de las rutas en Restify
Las rutas funcionan en Restify del mismo modo que funcionan al usar la pila Express. Defina rutas mediante el URI al que espera que llamen las aplicaciones cliente.

Un patrón típico de una ruta Restify es:

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

Restify y Express proporcionan una funcionalidad mucho más profunda, como la definición de tipos de aplicación y la realización de un enrutamiento complejo en distintos puntos de conexión. Para este tutorial, mantenemos estas rutas simples.

#### <a name="add-default-routes-to-your-server"></a>Incorporación de rutas predeterminadas al servidor
Ahora se agregan las rutas CRUD básicas de **create** y **list** para nuestra API de REST. En la rama `complete` del ejemplo se pueden encontrar otras rutas.

En la línea de comandos, cambie el directorio a `azuread`, en caso de que no esté ya allí:

`cd azuread`

Abra el archivo `server.js` en un editor. Debajo de las entradas de la base de datos que creó anteriormente agregue la siguiente información:

```Javascript
/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}
```

```Javascript
/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err)
            return next(err);

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Add one!");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}
```


#### <a name="add-error-handling-for-the-routes"></a>Incorporación del control de errores para las rutas
Agregue cierto control de errores, con el fin de poder comunicar al cliente los problemas que encuentre de una forma que pueda comprender.

Agregue el siguiente código:

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="create-your-server"></a>Creación del servidor
Ahora ha definido la base de datos y colocado las rutas en su sitio. Lo último que debe hacer es agregar la instancia del servidor que administra las llamadas.

Restify y Express proporcionan una personalización más avanzada de un servidor de API de REST, pero aquí se utiliza la configuración más básica.

```Javascript

/**
 * Our Server
 */


var server = restify.createServer({
    name: "Microsoft Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>Incorporación de las rutas al servidor (sin autenticación)
```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

```

## <a name="add-authentication-to-your-rest-api-server"></a>Incorporación de autenticación al servidor de API de REST
Ahora que tiene un servidor de API de REST en ejecución, puede hacer que sea útil en Azure AD.

En la línea de comandos, cambie el directorio a `azuread`, en caso de que no esté ya allí:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Use el elemento OIDCBearerStrategy que se incluye con passport-azure-ad
> [!TIP]
> Al escribir las API, siempre debería vincular los datos a un elemento único del token cuya identidad el usuario no pueda suplantar. Cuando el servidor almacena tareas pendientes, lo hace en función del **oid** del usuario en el token (llamado a través de token.oid) que se coloca en el campo "owner". Este valor garantiza que el usuario es el único que puede acceder a sus propias tareas pendientes. En la API "owner" no queda expuesto, así que un usuario externo puede solicitar los elementos TODO de los demás, incluso si se autentican.
>
>

A continuación, use la estrategia de portador que viene con `passport-azure-ad`.

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var oidcStrategy = new OIDCBearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

Passport utiliza el mismo patrón para todas sus estrategias. Le pasa un elemento `function()` que tiene los parámetros `token` y `done`. La estrategia regresará después de haber realizado todo el trabajo. A continuación, debe almacenar el usuario y guardar el token para que no haga falta volver a solicitarlo.

> [!IMPORTANT]
> El código anterior toma cualquier usuario que se autentique en el servidor. Este proceso se conoce como registro automático. En los servidores de producción, no permita que los usuarios accedan a la API sin hayan pasado antes por un proceso de registro. Este proceso suele ser el patrón que se observa en las aplicaciones de consumidor que permiten registrarse mediante Facebook, pero que luego piden que se rellene información adicional. Si no se tratara de un programa de línea de comandos, podríamos haber extraído el correo electrónico del objeto de token devuelto y, a continuación, haber pedido a los usuarios que rellenaran la información adicional. Dado que este es un ejemplo, se agregará a una base de datos en memoria.
>
>

## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>Ejecute la aplicación de servidor para comprobar que le rechaza
Puede usar `curl` para ver si ahora tiene la protección de OAuth2 en los puntos de conexión. Los encabezados devueltos deberían ser suficientes para indicar que está en el camino correcto.

Asegúrese de que la instancia de MongoDB se esté ejecutando:

    $sudo mongodb

Cambie al directorio y ejecute el servidor:

    $ cd azuread
    $ node server.js

En una nueva ventana de terminal, ejecute `curl`

Pruebe una operación POST básica:

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Un error 401 es la respuesta que desea. Indica que la capa Passport intenta redirigir al punto de conexión de autorización.

## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>Ahora tiene un servicio de API de REST que usa OAuth2
Ha implementado una API de REST mediante Restify y OAuth. Ahora dispone de suficiente código para poder seguir con el desarrollo de su servicio y basarse en este ejemplo. Ha avanzado todo lo que se puede con este servidor sin usar un cliente compatible con OAuth2. Para ese paso, utilice un tutorial adicional, como [Azure AD B2C: Llamada a una API web desde una aplicación iOS mediante una biblioteca de terceros](active-directory-b2c-devquickstarts-ios.md) .

## <a name="next-steps"></a>Pasos siguientes
Ahora puede pasar a temas más avanzados, como:

[Conexión a una API web mediante iOS con B2C](active-directory-b2c-devquickstarts-ios.md)
