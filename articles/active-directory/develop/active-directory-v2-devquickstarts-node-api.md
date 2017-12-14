---
title: "Protección de una API web de Azure Active Directory 2.0 mediante Node.js | Microsoft Docs"
description: "En este artículo se describe cómo crear una API web de Node.js que acepta tokens de una cuenta Microsoft personal y de cuentas profesionales o educativas."
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mtillman
editor: 
ms.assetid: 0b572fc1-2aaf-4cb6-82de-63010fb1941d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f07e421feedf3c82da7be16434891cdbe6069038
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="secure-a-web-api-by-using-nodejs"></a>Protección de una API web mediante Node.js
> [!NOTE]
> No todas las características y escenarios de Azure Active Directory son compatibles con la versión 2.0 del punto de conexión. Para determinar si debe usar la versión 2.0 o 1.0 del punto de conexión, obtenga información sobre las [limitaciones de esta versión](active-directory-v2-limitations.md).
> 
> 

Cuando se utiliza el punto de conexión de la versión 2.0 de Azure Active Directory (Azure AD), puede usar tokens de acceso de [OAuth 2.0](active-directory-v2-protocols.md) para proteger su API web. Con los tokens de OAuth 2.0, los usuarios que tienen una cuenta Microsoft personal, y cuentas profesionales o educativas, pueden acceder de forma segura a su API web.

*Passport* es middleware de autenticación para Node.js. Passport, flexible y modular, se puede usar discretamente en cualquier aplicación web de Restify o basada en Express. En esta herramienta, un conjunto completo de estrategias admite la autenticación mediante nombre de usuario y contraseña, Facebook, Twitter y mucho más. Hemos desarrollado una estrategia para Azure AD. En este artículo se muestra cómo instalar el módulo y luego agregar el complemento `passport-azure-ad` de Azure AD.

## <a name="download"></a>Descargar
El código de este tutorial se conserva [en GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs). Para seguir el tutorial, puede [descargar el esqueleto de la aplicación como un archivo .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip) o clonar el esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

La aplicación completa se ofrece al final de este tutorial.

## <a name="1-register-an-app"></a>1: Registro de una aplicación
Cree una aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) o siga estos [pasos detallados](active-directory-v2-app-registration.md) para registrar una aplicación. Asegúrese de lo siguiente:

* Copie el **identificador de aplicación** asignado a la aplicación. Lo necesitará para este tutorial.
* Agregar la plataforma **Móvil** a la aplicación.
* Copie el **URI de redireccionamiento** del portal. Debe usar el valor del URI predeterminado de `urn:ietf:wg:oauth:2.0:oob`.

## <a name="2-install-nodejs"></a>2: Instalación de Node.js
Para utilizar el ejemplo en este tutorial, debe [instalar Node.js](http://nodejs.org).

## <a name="3-install-mongodb"></a>3: Instalación de MongoDB
Para usar correctamente este ejemplo, debe [instalar MongoDB](http://www.mongodb.org). En este ejemplo, se usa para que la API de REST sea persistente en las instancias del servidor.

> [!NOTE]
> En este artículo, se da por hecho que utiliza los puntos de conexión predeterminados de instalación y servidor para MongoDB: mongodb://localhost.
> 
> 

## <a name="4-install-the-restify-modules-in-your-web-api"></a>4: Instalación de los módulos Restify en su API web
Vamos a usar Restify para crear nuestra API de REST. Restify es un marco de trabajo de la aplicación de Node.js mínimo y flexible que se deriva de Express. Restify tiene un conjunto robusto de características que puede usar para compilar las API de REST en Connect.

### <a name="install-restify"></a>Instalación de Restify
1.  En el símbolo del sistema, cambie el directorio a **azuread**:

    `cd azuread`

    Si el directorio **azuread** no existe, créelo:

    `mkdir azuread`

2.  Instale Restify:

    `npm install restify`

    El resultado de este comando debe tener este aspecto:

    ```
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
    └── bunyan@0.22.0(mv@0.0.5)
    ```

#### <a name="did-you-get-an-error"></a>¿Ha recibido un error?
En algunos sistemas operativos, cuando se usa el comando `npm`, verá este mensaje: `Error: EPERM, chmod '/usr/local/bin/..'`. Además, una sugerencia de que trate de ejecutar la cuenta como administrador. Si aparece este problema, utilice el comando `sudo` para ejecutar `npm` en un nivel de privilegio más elevado.

#### <a name="did-you-get-an-error-related-to-dtrace"></a>¿Ha recibido un error relacionado con DTrace?
Al instalar Restify, podría aparecer este mensaje:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: two
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

Restify proporciona un mecanismo eficaz para rastrear llamadas REST con DTrace. Sin embargo, DTrace no está disponible en muchos sistemas operativos. Puede omitir estos errores.


## <a name="5-install-passportjs-in-your-web-api"></a>Paso 5: Instalación de Passport.js en su API web
1.  En el símbolo del sistema, cambie al directorio **azuread**.

2.  Instale Passport.js:

    `npm install passport`

    El resultado del comando anterior debe tener el siguiente aspecto:

    ```
     passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3
    ```

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6: Incorporación de passport-azure-ad a su API web
Después, agregue la estrategia de OAuth usando passport-azuread. `passport-azuread` es un conjunto de estrategias que conectan Azure AD a Passport. En este ejemplo de API de REST, esta estrategia se usa para los tokens de portador.

> [!NOTE]
> Aunque OAuth 2.0 proporciona un marco en el que se puede emitir cualquier tipo de token conocido, habitualmente se usan determinados tipos de token. Los tokens de portador se suelen utilizar para proteger los puntos de conexión. Además, son el tipo de token más emitido en OAuth 2.0. Muchas implementaciones de OAuth 2.0 dan por hecho que los tokens de portador son el único tipo de token emitido.
> 
> 

1.  En el símbolo del sistema, cambie los directorios a **azuread**.

    `cd azuread`

2.  Instale el módulo `passport-azure-ad` de Passport.js :

    `npm install passport-azure-ad`

    El resultado del comando anterior debe tener el siguiente aspecto:

    ```
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
    ```

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7: Incorporación de módulos MongoDB a su API web
En este ejemplo, utilizamos MongoDB como el almacén de datos. 

1.  Para ello, instale Mongoose, un complemento muy utilizado para administrar modelos y esquemas: 

    `npm install mongoose`

2.  Instale el controlador de base de datos para MongoDB (que también se denomina MongoDB):

    `npm install mongodb`

## <a name="8-install-additional-modules"></a>8: Instalar módulos adicionales
Instale los demás módulos necesarios.

1.  En el símbolo del sistema, cambie el directorio a **azuread**:

    `cd azuread`

2.  Escriba los siguientes comandos. Los comandos instalan los módulos siguientes en su directorio node_modules:

    *   `npm install crypto`
    *   `npm install assert-plus`
    *   `npm install posix-getopt`
    *   `npm install util`
    *   `npm install path`
    *   `npm install connect`
    *   `npm install xml-crypto`
    *   `npm install xml2js`
    *   `npm install xmldom`
    *   `npm install async`
    *   `npm install request`
    *   `npm install underscore`
    *   `npm install grunt-contrib-jshint@0.1.1`
    *   `npm install grunt-contrib-nodeunit@0.1.2`
    *   `npm install grunt-contrib-watch@0.2.0`
    *   `npm install grunt@0.4.1`
    *   `npm install xtend@2.0.3`
    *   `npm install bunyan`
    *   `npm update`

## <a name="9-create-a-serverjs-file-for-your-dependencies"></a>9: Creación de un archivo server.js con sus dependencias
El archivo Server.js proporciona la mayor parte de la funcionalidad de su servidor de API web. La mayoría del código se agrega a este archivo. Con fines de producción, puede rediseñar la funcionalidad para que estuviera en archivos más pequeños, como controladores y rutas independientes. En este artículo, utilizamos Server.js para este fin.

1.  En el símbolo del sistema, cambie el directorio a **azuread**:

    `cd azuread`

2.  Con el editor que prefiera, cree un archivo Server.js. Agregue la siguiente información al archivo:

    ```Javascript
    'use strict';
    /**
    * Module dependencies.
    */
    var util = require('util');
    var assert = require('assert-plus');
    var mongoose = require('mongoose/');
    var bunyan = require('bunyan');
    var restify = require('restify');
    var config = require('./config');
    var passport = require('passport');
    var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
    ```

3.  Guarde el archivo . Volveremos a él en breve.

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10: Crear un archivo de configuración para almacenar la configuración de Azure AD
Este archivo de código pasa los parámetros de configuración del portal de Azure AD al archivo Passport.js. Estos valores de configuración se crearon al agregar la API web al portal en la primera parte del artículo. Explicamos qué incluir en los valores de estos parámetros después de haber copiado el código.

1.  En el símbolo del sistema, cambie el directorio a **azuread**:

    `cd azuread`

2.  En un editor, cree un archivo Config.js. Agregue la siguiente información:

    ```Javascript
    // Don't commit this file to your public repos. This config is for first-run.
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your Mongo auth URI goes here.
    issuer: 'https://sts.windows.net/**<your application id>**/',
    audience: '<your redirect URI>',
    identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For Microsoft, you should never need to change this.
    };

    ```



### <a name="required-values"></a>Valores obligatorios

*   **IdentityMetadata**: es donde `passport-azure-ad` buscará los datos de configuración del proveedor de identidades (IdP), así como las claves para validar los tokens JSON Web Token (JWT). Si utiliza Azure AD, probablemente no querrá cambiar esto.

*   **audience**: el URI de redirección desde el portal.

> [!NOTE]
> Distribuimos sus claves con frecuencia. Asegúrese de extraer siempre de la dirección URL "openid_keys" y de que la aplicación pueda tener acceso a Internet.
> 
> 

## <a name="11-add-the-configuration-to-your-serverjs-file"></a>11: Incorporación de configuración a su archivo Server.js
La aplicación debe leer estos valores del archivo de configuración que acaba de crear. Para ello, agregamos el archivo .config como un recurso necesario en su aplicación. Establezca las variables globales en las que se encuentran en Config.js.

1.  En el símbolo del sistema, cambie el directorio **azuread**:

    `cd azuread`

2.  En un editor, abra Server.js. Agregue la siguiente información:

    ```Javascript
    var config = require('./config');
    ```

3.  Agregue una nueva sección a Server.js:

    ```Javascript
    // Pass these options in to the ODICBearerStrategy.
    var options = {
    // The URL of the metadata document for your app. Put the keys for token validation from the URL found in the jwks_uri tag in the metadata.
    identityMetadata: config.creds.identityMetadata,
    issuer: config.creds.issuer,
    audience: config.creds.audience
    };
    // Array to hold signed-in users and the current signed-in user (owner).
    var users = [];
    var owner = null;
    // Your logger
    var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
    });
    ```

## <a name="12-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>12: Incorporación del modelo MongoDB y la información del esquema mediante Mongoose
Después, conecte estos tres archivos en un servicio de API de REST.

En este artículo, utilizamos MongoDB para almacenar las tareas. Se explican en el *paso 4*.

En el archivo Config.js creado en el paso 11, la base de datos se llama *tasklist*. Eso es lo que colocaría al final de la dirección URL de conexión de mongoose_auth_local. No es necesario crear esta base de datos por anticipado en MongoDB. La base de datos se crea en la primera ejecución de la aplicación de servidor (dando por hecho que la base de datos ya no existe).

Indicó al servidor qué base de datos de MongoDB usar. Después, tiene que escribir código adicional para crear el modelo y el esquema para las tareas de su servidor.

### <a name="the-model"></a>El modelo
El modelo de esquema es muy básico. Puede expandirlo si lo necesita. 

El modelo de esquema tiene estos valores:

*   **NAME**. La persona a quien se ha asignado la tarea. Se trata de un valor de **cadena**.
*   **TASK**. El nombre del servidor. Se trata de un valor de **cadena**.
*   **DATE**. La fecha en que vence la tarea. Se trata de un **datetime** valor.
*   **COMPLETED**. Si se ha completado la tarea. Se trata de un elemento **booleano**.

### <a name="create-the-schema-in-the-code"></a>Creación del esquema en el código
1.  En el símbolo del sistema, cambie el directorio a **azuread**:

    `cd azuread`

2.  En el editor, abra Server.js. Agregue la siguiente información debajo de la entrada de configuración:

    ```Javascript
    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');
    ```

Este código se conecta al servidor de MongoDB. También devuelve un objeto de esquema.

#### <a name="using-the-schema-create-your-model-in-the-code"></a>Con el esquema, cree el modelo en el código.
En el código anterior, agregue el siguiente código:

```Javascript
// Create a basic schema to store your tasks and users.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model.
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```

Como puede imaginar por el código, primero creará un esquema. Después, un objeto de modelo. Usará el objeto de modelo para almacenar los datos en todo el código cuando defina las **rutas**.

## <a name="13-add-your-routes-for-your-task-rest-api-server"></a>13: Incorporación de las rutas para el servidor de API de REST de su tarea
Ahora que tiene un modelo de base de datos con el que trabajar, agregue las rutas que usará para el servidor de API de REST.

### <a name="about-routes-in-restify"></a>Información de las rutas en Restify
Las rutas de Restify funcionan exactamente del mismo modo que cuando se usa la pila de Express. Defina rutas mediante el URI al que espera que llamen las aplicaciones cliente. Normalmente, las rutas se definen en un archivo independiente. En este tutorial, colocamos nuestra rutas en Server.js. Se recomienda diseñarlas en su propio archivo para que se puedan usar en producción.

Un patrón típico de una ruta Restify es:

```Javascript
function createObject(req, res, next) {
// Do work on object.
_object.name = req.params.object; // Passed value is in req.params under object.
///...
return next(); // Keep the server going.
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```


Este es el patrón en su nivel más básico. Restify (y Express) proporcionan una funcionalidad mucho más profunda, como definir los tipos de aplicación y proporcionar un enrutamiento complejo en distintos puntos de conexión.

#### <a name="add-default-routes-to-your-server"></a>Incorporación de rutas predeterminadas al servidor
Agregue las rutas CRUD básicas: **create**, **retrieve**, **update** y **delete**.

1.  En el símbolo del sistema, cambie el directorio a **azuread**:

    `cd azuread`

2.  En un editor, abra Server.js. Debajo de las entradas de la base de datos que creó anteriormente, agregue la siguiente información:

    ```Javascript
    /**
    *
    * APIs for your REST task server
    */
    // Create a task.
    function createTask(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow you to use MongoDB Server as your response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    // Create a new task model, fill it, and save it to MongoDB.
    var _task = new Task();
    if (!req.params.task) {
    req.log.warn({
    params: p
    }, 'createTodo: missing task');
    next(new MissingTaskError());
    return;
    }
    _task.owner = owner;
    _task.task = req.params.task;
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
    // Delete a task by name.
    function removeTask(req, res, next) {
    Task.remove({
    task: req.params.task,
    owner: owner
    }, function(err) {
    if (err) {
    req.log.warn(err,
    'removeTask: unable to delete %s',
    req.params.task);
    next(err);
    } else {
    log.info('Deleted task:', req.params.task);
    res.send(204);
    next();
    }
    });
    }
    // Delete all tasks.
    function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
    }
    // Get a specific task based on name.
    function getTask(req, res, next) {
    log.info('getTask was called for: ', owner);
    Task.find({
    owner: owner
    }, function(err, data) {
    if (err) {
    req.log.warn(err, 'get: unable to read %s', owner);
    next(err);
    return;
    }
    res.json(data);
    });
    return next();
    }
    /// Returns the list of TODOs that were loaded.
    function listTasks(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow us to use MongoDB Server as our response to any origin.
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
    log.warn(err, "There are no tasks in the database. Add one!");
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

### <a name="add-error-handling-for-the-routes"></a>Incorporación del control de errores para las rutas
Agregue algunos controles de errores de forma que pueda comunicar al cliente el problema que se ha producido.

Agregue el código siguiente debajo del código, que ya ha escrito:

```Javascript
///--- Errors for communicating something more information back to the client.
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


## <a name="14-create-your-server"></a>14: Creación del servidor
La última tarea consiste en agregar la instancia del servidor. La instancia del servidor administra las llamadas.

Restify (y Express) tiene una personalización más avanzada que puede usar con un servidor de API de REST. En este tutorial, usaremos la configuración más básica.

```Javascript
/**
* Your server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that you don't drop data on uploads.
server.pre(restify.pre.pause());
// Clean up imprecise paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());
// Set a per-request Bunyan logger (with requestid filled in).
server.use(restify.requestLogger());
// Allow 5 requests/second by IP address, and burst to 10.
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use common commands, such as:
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## <a name="15-add-the-routes-without-authentication-for-now"></a>15: Incorporación de las rutas (sin autenticación por ahora)
```Javascript
/// Use CRUD to add the real handlers.
/**
/*
/* Each of these handlers is protected by your Open ID Connect Bearer strategy. Invoke 'oidc-bearer'
/* in the pasport.authenticate() method. Because REST is stateless, set 'session: false'. You 
/* don't need to maintain session state. You can experiment with removing API protection.
/* To do this, remove the passport.authenticate() method:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## <a name="16-run-the-server"></a>16: Ejecución del servidor
Se recomienda probar el servidor antes de agregar la autenticación.

La forma más fácil de hacerlo es usando curl en una línea de comandos. Para ello, necesita una utilidad simple que pueda usar para analizar el resultado como JSON. 

1.  Instale la herramienta JSON que usaremos en los ejemplos siguientes:

    `$npm install -g jsontool`

    De esta forma, se instala la herramienta JSON globalmente.

2.  Asegúrese de que la instancia de MongoDB se esté ejecutando:

    `$sudo mongod`

3.  Cambie el directorio a **azuread** y después ejecute curl:

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 2.0OK
    Connection: close
    Content-Type: application/json
    Content-Length: 171
    Date: Tue, 14 Jul 2015 05:43:38 GMT
    [
    "GET /",
    "POST /tasks/:owner/:task",
    "POST /tasks (for JSON body)",
    "GET /tasks",
    "PUT /tasks/:owner",
    "GET /tasks/:owner",
    "DELETE /tasks/:owner/:task"
    ]
    ```

4.  Para agregar una tarea, siga estos pasos:

    `$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

    La respuesta debe ser:

    ```Shell
    HTTP/1.1 201 Created
    Connection: close
    Access-Control-Allow-Origin: *
    Access-Control-Allow-Headers: X-Requested-With
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 5
    Date: Tue, 04 Feb 2014 01:02:26 GMT
    Hello
    ```

5.  Lista de tareas de Brandon:

    `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Si todos estos comandos se ejecutan sin errores, está listo para agregar OAuth al servidor de la API de REST.

*Ahora tiene un servidor de API de REST con MongoDB.*

## <a name="17-add-authentication-to-your-rest-api-server"></a>17: Incorporación de autenticación al servidor de API de REST
Ahora que tiene una API de REST de ejecución, configurarlo para usar con Azure AD.

En el símbolo del sistema, cambie el directorio a **azuread**:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-thats-included-with-passport-azure-ad"></a>Uso del elemento oidcbearerstrategy que se incluye con passport-azure-ad
Hasta ahora hemos creado un servidor típico de TODO de REST sin ningún tipo de autorización. Ahora, agregue la autenticación.

En primer lugar, indique que quiere usar Passport. Incluya esto justo después de la configuración del servidor anterior:

```Javascript
// Start using Passport.js.

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [!TIP]
> Al escribir las API, se recomienda vincular los datos a un elemento único del token cuya identidad el usuario no pueda suplantar. Cuando este servidor almacena elementos TODO, los guarda en función del identificador de suscripción del usuario en el token (llamado a través de token.sub), que se coloca en el campo "owner". Esto garantiza que ese usuario sea el único que puede acceder a sus tareas pendientes. Nadie más puede tener acceso a las tareas pendientes que se especificaron. No hay ninguna exposición en la API para el "propietario". Un usuario externo puede solicitar las tareas pendientes de otros usuarios, incluso si estos se autentican.
> 
> 

Después, vamos a usar la estrategia de portador de OpenID Connect que se incluye con `passport-azure-ad`. Colóquela después de lo que pegó anteriormente:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users.
/*
/* Because of the Passport pattern, you need to manage users and info tokens
/* with a FindorCreate() method. The method must be provided by the implementor.
/* In the following code, you autoregister any user and implement a FindById().
/* It's a good idea to do something more advanced.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
log.info('Found user: ', user);
return fn(null, user);
}
}
return fn(null, null);
};
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retrieved');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically, because they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport usa un patrón similar para todas sus estrategias (Twitter, Facebook, etc.) Todos los escritores de estrategias se ajustan a este patrón. Pase a la estrategia una función `function()` que use un token y `done` como parámetros. La estrategia se devuelve cuando hace todo su trabajo. Almacene el usuario y guarde provisionalmente el token para no tener que volver a solicitarlo.

> [!IMPORTANT]
> El código anterior adopta cualquier usuario que pueda autenticarse en el servidor. Esto se conoce como registro automático. En un servidores de producción, no debería permitir que acceda cualquier usuario sin que antes pase por el proceso de registro que decida. Esto suele ser el patrón que se ven en las aplicaciones para consumidores. La aplicación podría permitir que se registre con Facebook, pero, luego, le pide que escriba información adicional. Si no usa un programa de línea de comandos para este tutorial, podría extraer el correo electrónico del objeto de token que se devuelve. Luego, puede pedir al usuario que escriba información adicional. Puesto que se trata de un servidor de prueba, agregará el usuario directamente a la base de datos en memoria.
> 
> 

### <a name="protect-endpoints"></a>Protección de los puntos de conexión
Proteja los puntos de conexión mediante la especificación de la llamada **passport.authenticate()** con el protocolo que desea usar.

Puede editar la ruta en el código del servidor para ver una opción más avanzada:

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again"></a>18: Reejecución de la aplicación de servidor
Use curl para ver si ahora tiene la protección de OAuth 2.0 en los puntos de conexión. Haga esto antes de ejecutar cualquiera de nuestros SDK de cliente en este punto de conexión. Los encabezados devueltos deben indicar si la autenticación está funcionando correctamente.

1.  Asegúrese de que la instancia de MongoDB se esté ejecutando:

    `$sudo mongod`

2.  Cambie al directorio **azuread** y, luego, use curl:

    `$ cd azuread`

    `$ node server.js`

3.  Pruebe una operación POST básica:

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

Una respuesta 401 indica que la capa de Passport intenta realizar la redirección al punto de conexión autorizado, que es exactamente lo que desea.

*Ahora tiene un servicio de API de REST que usa OAuth 2.0.*

Ha avanzado todo lo posible con este servidor sin usar un cliente compatible con OAuth 2.0. Para ello, debe revisar un tutorial adicional.

## <a name="next-steps"></a>Pasos siguientes
Como referencia, se ofrece el ejemplo completado (sin sus valores de configuración) [en forma de archivo .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip). También puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Ahora puede pasar a temas más avanzados. Podría probar este: [Proteger una aplicación web de Node.js con el punto de conexión v2.0](active-directory-v2-devquickstarts-node-web.md).

Estas son algunos recursos adicionales:

* [Guía del desarrollador de Azure AD v2.0](active-directory-appmodel-v2-overview.md)
* [Etiqueta "azure-active-directory" de Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Obtención de actualizaciones de seguridad para nuestros productos
Le recomendamos suscribirse para recibir una notificación cuando se produzcan incidentes de seguridad. En la página [Notificaciones técnicas de seguridad de Microsoft](https://technet.microsoft.com/security/dd252948), suscríbase a las alertas de documentos informativos de seguridad.

