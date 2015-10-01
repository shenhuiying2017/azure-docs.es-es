<properties
	pageTitle="Vista previa de B2C: Protección de una API web mediante node.js | Microsoft Azure"
	description="Creación de una API web NodeJS que acepta tokens de un inquilino B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="brandwe"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
  	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="brandwe"/>

# Vista previa de B2C: Protección de una API web mediante node.js

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
	Este artículo no trata de la implementación de la administración de registros, inicios de sesión y perfiles con Azure AD B2C. Se centra en la llamada a las API web después de que el usuario ya está autenticado.
Si no lo ha hecho ya, debe comenzar con el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para obtener información sobre los conceptos básicos de Azure AD B2C.

> [AZURE.NOTE]	Este ejemplo se escribió para estar conectados con nuestra [aplicación de ejemplo B2C para iOS.](active-directory-b2c-devquickstarts-ios.md) Primero realice este tutorial y luego continúe con ese ejemplo.

**Passport** es middleware de autenticación para Node.js. Muy flexible y modular, Passport puede pasar discretamente a cualquier aplicación web basada en Express o Restify. Un conjunto completo de estrategias admite la autenticación mediante un nombre de usuario y contraseña, Facebook, Twitter y mucho más. Hemos desarrollado una estrategia para Microsoft Azure Active Directory. Instalaremos este módulo y, luego, agregaremos el complemento `passport-azure-ad` de Microsoft Azure Active Directory.

Para llevar a cabo esta tarea, deberá hacer lo siguiente:

1. Registrar una aplicación con Azure AD
2. Configurar la aplicación para que use el complemento passport-azure-ad de Passport.
3. Configurar una aplicación cliente para llamar a la API web Lista de tareas pendientes

El código de este tutorial se conserva [en GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs). Para continuar, puede [descargar el esqueleto de la aplicación como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/skeleton.zip) o clonar el esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```

The completed application is provided at the end of this tutorial as well.

> [AZURE.WARNING] 	For our B2C Preview you must use the same client ID/Application ID and policies for both the Web-API task server and the client that connects to it. This is true for our iOS and Android tutorials. If you have previously created an application in either of those quickstarts, please use those values instead of creating new ones below.


## 1. Get an Azure AD B2C directory

Before you can use Azure AD B2C, you must create a directory, or tenant.  A directory is a container for all your users, apps, groups, and so on.  If you don't have
one already, go [create a B2C directory](active-directory-b2c-get-started.md) before moving on.

## 2. Create an application

Now you need to create an app in your B2C directory, which gives Azure AD some information that it needs to securely communicate with your app.  Both the client app and web API will be represented by a single **Application ID** in this case, since they comprise one logical app.  To create an app,
follow [these instructions](active-directory-b2c-app-registration.md).  Be sure to

- Include a **web app/web api** in the application
- Enter `http://localhost/TodoListService` as a **Reply URL** - it is the default URL for this code sample.
- Create an **Application Secret** for your application and copy it down.  You will need it shortly.
- Copy down the **Application ID** that is assigned to your app.  You will also need it shortly.

## 3. Create your policies

In Azure AD B2C, every user experience is defined by a [**policy**](active-directory-b2c-reference-policies.md).  This app contains three 
identity experiences - sign-up, sign-in, and sign-in with Facebook.  You will need to create one policy of each type, as described in the 
[policy reference article](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).  When creating your three policies, be sure to:

- Choose the **Display Name** and a few other sign-up attributes in your sign-up policy.
- Choose the **Display Name** and **Object ID** application claims in every policy.  You can choose other claims as well.
- Copy down the **Name** of each policy after you create it.  It should have the prefix `b2c_1_`.  You'll need those policy names shortly. 

Once you have your three policies successfully created, you're ready to build your app.

Note that this article does not cover how to use the policies you just created.  If you want to learn about how policies work in Azure AD B2C,
you should start with the [.NET Web App getting started tutorial](active-directory-b2c-devquickstarts-web-dotnet.md).

## 4: Download node.js for your platform
To successfully use this sample, you must have a working installation of Node.js.

Install Node.js from [http://nodejs.org](http://nodejs.org).

## 5: Install MongoDB on to your platform

To successfully use this sample, you must have a working installation of MongoDB. We will use MongoDB to make our REST API persistant across server instances.

Install MongoDB from [http://mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] This walkthrough assumes that you use the default installation and server endpoints for MongoDB, which at the time of this writing is: mongodb://localhost

## 6: Install the Restify modules in to your Web API

We will be using Resitfy to build our REST API. Restify is a minimal and flexible Node.js application framework derived from Express that has a robust set of features for building REST APIs on top of Connect.

### Install Restify

From the command-line, change directories to the azuread directory. If the **azuread** directory does not exist, create it.

`cd azuread` - or- `mkdir azuread;`

Type the following command:

`npm install restify`

This command installs Restify.

#### Did you get an error?

When using npm on some operating systems, you may receive an error of Error: EPERM, chmod '/usr/local/bin/..' and a request to try running the account as an administrator. If this occurs, use the sudo command to run npm at a higher privilege level.

#### Did you get an error regarding DTrace?

You may see something like this when installing Restify:

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


Restify proporciona un mecanismo eficaz para rastrear llamadas REST con DTrace. Sin embargo, muchos sistemas operativos no tienen DTrace disponible. Puede omitir estos errores con seguridad.


El resultado de este comando debe ser similar al siguiente:


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


## 7: Instalar Passport.js en la API web

[Passport](http://passportjs.org/) es middleware de autenticación para Node.js. Muy flexible y modular, Passport puede pasar discretamente a cualquier aplicación web basada en Express o Restify. Un conjunto completo de estrategias admite la autenticación mediante un nombre de usuario y contraseña, Facebook, Twitter y mucho más. Hemos desarrollado una estrategia para Azure Active Directory. Instalaremos este módulo y, a continuación, agregaremos el complemento de la estrategia de Azure Active Directory.

Desde la línea de comandos, cambie los directorios por el directorio azuread.

Escriba el siguiente comando para instalar passport.js.

`npm install passport`

El resultado del comando debe ser similar al siguiente:

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## 8: Agregar Passport-Azure-AD a la API web

A continuación, agregaremos la estrategia OAuth usando passport-azuread, un conjunto de estrategias que se conectan a Azure Active Directory con Passport. Usaremos esta estrategia para los tokens de portador en este ejemplo de API de REST.

> [AZURE.NOTE]Aunque OAuth2 proporciona un marco de trabajo en el que se puede emitir cualquier tipo de token conocido, solo ciertos tipos de token gozan de uso generalizado. Para la protección de extremos, que han resultado ser tokens portadores. Los tokens portadores son el tipo de token más emitido de OAuth2 y muchas implementaciones asumen que son el único tipo de token emitido.

Desde la línea de comandos, cambie al directorio azuread.

Escriba el siguiente comando para instalar el módulo passport-azure-ad de Passport.js:

`npm install passport-azure-ad`

El resultado del comando debe ser similar al siguiente:

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

## 9: Agregar módulos MongoDB a la API web

Vamos a usar MongoDB como nuestro almacén de datos. Por ese motivo, debemos instalar tanto el complemento ampliamente usado para administrar modelos y esquemas llamado Mongoose como el controlador de base de datos para MongoDB, también llamado MongoDB.


* `npm install mongoose`
* `npm install mongodb`

## 10: Instalar módulos adicionales

A continuación, instalaremos los módulos necesarios restantes.


Desde la línea de comandos, cambie los directorios por la carpeta **azuread** si no se encuentra ya en ella:

`cd azuread`


Escriba los comandos siguientes para instalar los módulos siguientes en su directorio node\_modules:

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## 11: Crear server.js con sus dependencias

El archivo server.js proporcionará la mayor parte de nuestra funcionalidad para nuestro servidor de API web. Agregaremos la mayor parte de nuestro código a este archivo. Con fines de producción, rediseñaría la funcionalidad para que estuviera en archivos más pequeños, como controladores y rutas independientes. En esta demostración, usaremos server.js para esta funcionalidad.

Desde la línea de comandos, cambie los directorios por la carpeta **azuread** si no se encuentra ya en ella:

`cd azuread`

Cree un archivo `server.js` en nuestro editor favorito y agregue la siguiente información:

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

Guarde el archivo . Volveremos a él en breve.

## 12: Crear un archivo de configuración para almacenar la configuración de Azure AD

Este archivo de código pasa los parámetros de configuración desde el Portal de Azure Active Directory a Passport.js. Creó estos valores de configuración al agregar la API web al portal en la primera parte del tutorial. Explicaremos qué incluir en los valores de estos parámetros después de haber copiado el código.


Desde la línea de comandos, cambie los directorios por la carpeta **azuread** si no se encuentra ya en ella:

`cd azuread`

Cree un archivo `config.js` en nuestro editor favorito y agregue la siguiente información:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
issuer: 'https://sts.windows.net/**<your application id>**/',
audience: '<your redirect URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For using Microsoft you should never need to change this.
tenantName:'<tenant name>',
policyName:'<policy>',
};

```

### Valores obligatorios

*IdentityMetadata*: es donde passport-azure-ad buscará los datos de configuración para IdP, así como las claves para validar los tokens JWT. Probablemente no debería cambiarlo si usa Azure Active Directory.

*audience*: el URI de redirección desde el portal. En nuestro ejemplo se usa: `http://localhost/TodoListService`

*tenantName*: el nombre del inquilino (por ejemplo, contoso.microsoftonline.com).

*policyName*: la directiva con la que desea validar los tokens que entran en el servidor. Debe ser la misma directiva que usaría en la aplicación cliente.

> [AZURE.NOTE]Distribuimos nuestras claves con frecuencia. Asegúrese de extraer siempre de la dirección URL "openid\_keys" y de que la aplicación pueda tener acceso a Internet.


## 11: Agregar configuración a su archivo server.js

Debemos leer estos valores del archivo de configuración que acaba de crear en nuestra aplicación. Para ello, basta con que agreguemos el archivo .config como recurso necesario en nuestra aplicación y, a continuación, establezcamos las variables globales en las del documento config.js

Desde la línea de comandos, cambie los directorios por la carpeta **azuread** si no se encuentra ya en ella:

`cd azuread`

Abra su archivo `server.js` en nuestro editor favorito y agregue la siguiente información:

```Javascript
var config = require('./config');
```
A continuación, agregue una nueva sección a `server.js` con el código siguiente:

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience
};
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## 12: Agregar el modelo MongoDB y la información del esquema mediante Mongoose

Ahora toda esta preparación va a empezar a dar sus frutos al incluir juntos estos tres archivos en un servicio API REST.

En este tutorial, usaremos MongoDB para almacenar nuestras tareas como se describe en ***Paso 4***.

Como se indicó en el archivo config.js que creamos en el paso 11, llamamos a nuestra base de datos *tasklist*, ya que era lo que incluimos al final de nuestra dirección URL de conexión mongoose\_auth\_local. No es necesario crear de antemano esta base de datos en MongoDB, pues la creará por nosotros la primera vez que ejecutemos nuestra aplicación de servidor (asumiendo que aún no exista).

Ahora que hemos indicado al servidor la base de datos MongoDB que nos gustaría usar, debemos escribir algo de código adicional para crear el modelo y esquema para las tareas de nuestro servidor.

#### Análisis del modelo

Nuestro modelo de esquema es muy sencillo y lo expande según sea necesario.

NAME: el nombre de quien se asigna a la tarea. Una ***cadena***

TASK: la propia tarea. Una ***cadena***

DATE: la fecha en que vence la tarea. ***DATETIME***

COMPLETED: si la tarea se ha completado o no. ***BOOLEAN***

#### Creación del esquema en el código


Desde la línea de comandos, cambie los directorios por la carpeta **azuread** si no se encuentra ya en ella:

`cd azuread`

Abra su archivo `server.js` en nuestro editor favorito y agregue la siguiente información debajo de la entrada de configuración:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Se conectará al servidor de MongoDB y nos devolverá un objeto de esquema.

#### Con el esquema, cree nuestro modelo en el código

Debajo del código que escribió anteriormente, agregue el código siguiente:

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Como se deduce del código, creamos nuestro esquema y, a continuación, creamos un objeto de modelo que usaremos para almacenar nuestros datos en todo el código cuando definamos nuestras ***rutas***.

## 13: Agregar nuestras rutas para el servidor de API de REST de nuestra tarea

Ahora que tenemos un modelo de base de datos con el que trabajar, agreguemos las rutas que usaremos para nuestro servidor de API de REST.

### Acerca de las rutas en Restify

Las rutas funcionan en Restify exactamente igual que con la pila Express. Defina rutas mediante el URI al que espera que llamen las aplicaciones cliente. Normalmente, las rutas se definen en un archivo independiente. Para nuestros propósitos, incluiremos nuestras rutas en el archivo server.js. Se recomienda incluir estas en su propio archivo para el uso de producción.

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


Este es el patrón en su nivel más básico. Restify (y Express) proporcionan una funcionalidad mucho más profunda como la definición de tipos de aplicación y la realización de un enrutamiento complejo en distintos extremos. Para nuestros propósitos, simplificaremos estas rutas.

#### Agregar rutas predeterminadas a nuestro servidor

Ahora agregaremos las rutas CRUD básicas de Create, Retrieve, Update y Delete.

Desde la línea de comandos, cambie los directorios por la carpeta **azuread** si no se encuentra ya en ella:

`cd azuread`

Abra su archivo `server.js` en nuestro editor favorito y agregue la siguiente información debajo de las entradas de configuración que creó anteriormente:

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
// Delete a task by name
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
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
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

### Agregar control de errores para las rutas

Tiene sentido agregar control de errores para que podamos comunicar al cliente el problema que experimentamos de una forma que pueda entender.

Agregue el código siguiente debajo del código que ha escrito anteriormente:

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


## Paso 14: Crear su servidor

Tenemos nuestra base de datos definida, nuestras rutas preparadas y lo último que se debe hacer es agregar la instancia de nuestro servidor que administrará nuestras llamadas.

Restify (y Express) tienen una personalización muy profunda que puede desarrollar para un servidor de API de REST, pero una vez más usaremos la configuración más básica para nuestros propósitos.

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
}));
```
## 15: Agregar las rutas (sin autenticación por ahora)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers are protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the pasport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiement removing API protection
/* by removing the passport.authenticate() method like so:
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
## 16: Antes de incorporar la compatibilidad con OAuth, ejecutemos el servidor

Prueba del servidor antes de agregar la autenticación

La forma más fácil de hacerlo es usar curl en una línea de comandos. Antes de hacerlo, necesitamos una utilidad sencilla que nos permita analizar el resultado como JSON. Para ello, instale la herramienta json, pues todos los ejemplos siguientes la usan.

`$npm install -g jsontool`

De esta forma, se instala la herramienta JSON globalmente. Ahora que ya lo hemos hecho, practiquemos con el servidor:

En primer lugar, asegúrese de que se ejecute la instancia de MongoDB...

`$sudo mongod`

A continuación, cambie al directorio y empiece el curvado...

`$ cd azuread` `$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 200 OK
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

A continuación, podemos agregar una tarea de este modo:

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
Además, podemos mostrar tareas de Brandon así:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Si todo esto funciona, estamos listos para agregar OAuth al servidor de API de REST.

**Tiene un servidor de API de REST con MongoDB**

## 17: Agregar autenticación al servidor de API de REST

Ahora que tenemos una API de REST en ejecución (enhorabuena, por cierto), hagamos que funcione en Azure AD.

Desde la línea de comandos, cambie los directorios por la carpeta **azuread** si no se encuentra ya en ella:

`cd azuread`

### 1: Use la oidcbearerstrategy que se incluye con passport-azure-ad

Hasta ahora hemos creado un servidor típico de TODO de REST sin ningún tipo de autorización. Finalmente, empezamos a prepararlo.

En primer lugar, necesitamos indicar que queremos usar Passport. Incluya esto justo después de la otra configuración del servidor:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]Al escribir las API, siempre debería vincular los datos a un elemento único del token cuya identidad el usuario no pueda suplantar. Cuando este servidor almacena elementos TODO, los almacena en función del Id. de suscripción del usuario en el token (llamado a través de token.sub) que se coloca en el campo "owner". Esto garantiza que ese usuario sea el único que pueda acceder a sus TODO y nadie más tenga acceso a los TODO especificados. En la API "owner" no queda expuesto, así que un usuario externo puede solicitar los TODO de los demás, incluso si se autentican.

A continuación, vamos a usar la estrategia de portador de OpenID Connect que se incluye con passport-azure-ad. Por ahora veamos el código y lo explicaré en breve. Incluya esto después de lo anterior:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementor.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
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
log.info(token, 'was the token retreived');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically as they were new. Their sub is: ', token.sub);
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

Passport usa un patrón similar para todas sus estrategias (Twitter, Facebook, etc.) al que se ajustan todos los escritores de estrategias. Si se examina la estrategia, se percibe que se pasa function(), que tiene un token y un done como parámetros. La estrategia volverá diligentemente a nosotros una vez que haya finalizado su labor. Una vez que lo haga, almacenaremos el usuario y guardaremos provisionalmente el token, con el fin de que no tengamos que volver a pedirlo.

> [AZURE.IMPORTANT]El código anterior toma cualquier usuario que se autentique en el servidor. Esto se conoce como registro automático. En los servidores de producción, no debería permitir que acceda cualquier usuario sin que antes pase por el proceso de registro que decida. Este suele ser el patrón que se observa en las aplicaciones de consumidor que permiten registrarse con Facebook, pero que luego piden que se rellene información adicional. Si no se tratara de un programa de línea de comandos, podríamos haber extraído el correo electrónico del objeto de token que se devuelve y, a continuación, haber pedido al usuario que rellenara la información adicional. Puesto que se trata de un servidor de prueba, simplemente los agregaremos a la base de datos en memoria.

### 2\. Por último, proteja algunos extremos

Protege extremos mediante la especificación de la llamada passport.authenticate() con el protocolo que desea usar.

Editemos nuestra ruta en el código de servidor para que haga algo más interesante:

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

## 18: Ejecutar de nuevo su aplicación de servidor y asegurarse de que le rechaza

Usemos `curl` nuevamente para ver si ahora tenemos protección de OAuth2 contra nuestros extremos. Haremos esto antes de ejecutar cualquiera de nuestros SDK de cliente en este extremo. Los encabezados devueltos deben bastar para indicarnos si estamos en la ruta de acceso correcta.

En primer lugar, asegúrese de que se ejecute la instancia de MongoDB...

	$sudo mongod

A continuación, cambie al directorio y empiece el curvado...

	$ cd azuread
	$ node server.js

Pruebe una operación POST básica:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

La respuesta que quiere ver aquí es 401, que indica que la capa Passport está intentado realizar la redirección al extremo Authorize, exactamente lo que desea.


## ¡Enhorabuena! Tiene un servicio API REST mediante OAuth2.

Llegue tan lejos como pueda con este servidor sin usar un cliente compatible con OAuth2. Tendrá que seguir un tutorial adicional.

Si solo buscaba información sobre cómo implementar una API de REST mediante Restify y OAuth2, tendrá código de sobra para seguir desarrollando su servicio y aprender a crear en este ejemplo.

Como referencia, el ejemplo finalizado (sin sus valores de configuración) [se proporciona en forma de archivo .zip aquí](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/complete.zip), aunque también puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```


## Pasos siguientes

Ahora puede pasar a temas más avanzados. También puede probar lo siguiente:

[Conexión a una API web mediante iOS con B2C >>](active-directory-b2c-devquickstarts-ios.md)

<!----HONumber=Sept15_HO3-->