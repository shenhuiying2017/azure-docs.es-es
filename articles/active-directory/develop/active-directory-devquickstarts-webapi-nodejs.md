---
title: "Introducción a Node.js de Azure AD | Microsoft Docs"
description: "Compilación de una API web de REST de Node.js que se integre con Azure AD para su autenticación."
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 4f58177f540c14172d7ece8b4bc8c8a2b9787f8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-web-apis-for-nodejs"></a>Introducción a las API web para Node.js
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

*Passport* es middleware de autenticación para Node.js. Passport es flexible y modular, y se puede usar discretamente en cualquier aplicación web de Restify o basada en Express. Un conjunto completo de estrategias da soporte a la autenticación con nombre de usuario y contraseña, Facebook, Twitter, etc. Hemos desarrollado una estrategia para Microsoft Azure Active Directory (Azure AD). Instalamos este módulo y luego agregaremos el complemento `passport-azure-ad` de Microsoft Azure Active Directory.

Para ello, necesita lo siguiente:

1. Registrar una aplicación con Azure AD.
2. Configurar la aplicación para que use el complemento `passport-azure-ad` de Passport.
3. Configurar una aplicación cliente para llamar a la API web de la Lista de tareas pendientes.

El código de este tutorial se conserva [en GitHub](https://github.com/Azure-Samples/active-directory-node-webapi).

> [!NOTE]
> En este artículo no se trata la implementación de la administración de registros, inicios de sesión y perfiles con Azure AD B2C. Se centra en la llamada a las API web después de que el usuario ya está autenticado.  Para aprender los fundamentos de Azure Active Directory, se recomienda empezar por el documento [Integración con Azure Active Directory](active-directory-how-to-integrate.md).
>
>

Hemos publicado todo el código fuente de este ejemplo en GitHub bajo una licencia de MIT, así que no dude en clonarlo (o mejor aún, bifurcarlo) y realizar cualquier comentarios y solicitudes de incorporación de cambios.

## <a name="about-nodejs-modules"></a>Acerca de los módulos Node.js
En este tutorial se usan módulos Node.js. Los módulos son paquetes JavaScript que pueden cargarse y que proporcionan una funcionalidad específica para su aplicación. Normalmente, para instalar los módulos se usa una herramienta de línea de comandos de NPM de Node.js en el directorio de instalación de NPM. Sin embargo, algunos módulos, como el módulo de HTTP, se incluyen en el paquete principal de Node.js.

Los módulos instalados se guardan en el directorio **node_modules**, en la raíz del directorio de instalación de Node.js. En el directorio **node_modules**, cada módulo mantiene sus propio directorio **node_modules**, que contiene los módulos de los que depende. Además, cada módulo requerido tiene un directorio **node_modules**. Esta estructura de directorios recursiva representa la cadena de dependencias.

Esta estructura de la cadena de dependencias da como resultado una mayor superficie de la aplicación. Pero también garantiza que se cumplen todas las dependencias y que la versión de los módulos que se usa en el desarrollo también se utiliza en producción. Esto hace que el comportamiento de la aplicación de producción sea más predecible y evita los problemas de control de versiones que pueden afectar a los usuarios.

## <a name="step-1-register-an-azure-ad-tenant"></a>Paso 1: Registro de un inquilino de Azure AD
Para usar este ejemplo, se necesita un inquilino de Azure Active Directory. Si no está seguro de qué es un inquilino o de cómo obtener uno, consulte [Obtención de un inquilino de Azure Active Directory](active-directory-howto-tenant.md).

## <a name="step-2-create-an-application"></a>Paso 2: Creación de una aplicación
A continuación, cree una aplicación en su directorio que proporcione a Azure AD la información que necesita para comunicarse de forma segura con su aplicación.  Tanto la aplicación cliente como la API web se representan mediante un **identificador de aplicación** individual, ya que conforman una aplicación lógica.  Para crear una aplicación, siga [estas instrucciones](active-directory-how-applications-are-added.md). Si va a crear una aplicación de línea de negocio [es posible que estas instrucciones adicionales le resulten útiles](../active-directory-applications-guiding-developers-for-lob-applications.md).

Para crear una aplicación:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el menú superior, seleccione su cuenta. Luego, en la lista **Directorio**, elija el inquilino de Active Directory en el que desee registrar la aplicación.

3. En el menú de la izquierda, seleccione **Más servicios** y, después, seleccione **Azure Active Directory**.

4. Seleccione **Registros de aplicaciones** y luego seleccione **Agregar**.

5. Siga las indicaciones para crear una **Aplicación web o API web**.

      * El **nombre** de la aplicación describe la aplicación a los usuarios finales.

      * La **dirección URL de inicio de sesión** es la dirección URL base de su aplicación.  La dirección URL predeterminada del código de ejemplo es `https://localhost:8080`.

6. Después de registrarse, Azure AD asigna a su aplicación un identificador de aplicación individual. Este valor se necesita en las siguientes secciones, así que cópielo de la página de aplicación.

7. En la página **Configuración** -> **Propiedades** de la aplicación, actualice el URI del identificador de la aplicación. El **URI de id. de aplicación** es un identificador único de su aplicación. La convención consiste en usar `https://<tenant-domain>/<app-name>`, por ejemplo: `https://contoso.onmicrosoft.com/my-first-aad-app`.

8. Cree un **clave** para la aplicación desde la página **Configuración** y cópiela en otro lugar. La necesitará en breve.

## <a name="step-3-download-nodejs-for-your-platform"></a>Paso 3: Descarga de Node.js para la plataforma
Para usar correctamente este ejemplo, debe disponer de una instalación en funcionamiento de Node.js.

Instale Node.js desde [http://nodejs.org](http://nodejs.org).

## <a name="step-4-install-mongodb-on-your-platform"></a>Paso 4: Instalación de MongoDB en la plataforma
Para usar correctamente este ejemplo, debe disponer de una instalación en funcionamiento de MongoDB. MongoDB se usa para que la API de REST sea persistente en las distintas instancias del servidor.

Instale MongoDB desde [http://mongodb.org](http://www.mongodb.org).

> [!NOTE]
> En este tutorial se da por supuesto que usa la instalación predeterminada y los puntos de conexión de servidor para MongoDB, que en el momento de escribir este artículo es mongodb://localhost.
>
>

## <a name="step-5-install-the-restify-modules-in-your-web-api"></a>Paso 5: Instalación de los módulos Restify en su API web
Vamos a usar Restify para compilar nuestra API de REST. Restify es un marco de trabajo de la aplicación de Node.js mínimo y flexible que se deriva de Express. Tiene un sólido conjunto de características para crear API de REST sobre Connect.

### <a name="install-restify"></a>Instalar Restify
1. Desde la línea de comandos, cambie al directorio **azuread**. Si el directorio **azuread** no existe, créelo.

        `cd azuread - or- mkdir azuread; cd azuread`

2. Escriba el siguiente comando:

    `npm install restify`

    Este comando instala Restify.

#### <a name="did-you-get-an-error"></a>¿Ha recibido un error?
Si se usa NPM en algunos sistemas operativos, se puede que recibir el siguiente mensaje **Error: EPERM, chmod "/usr/local/bin/.."** y una sugerencia de que intente ejecutar la cuenta como administrador. Si esto ocurre, utilice el comando sudo para ejecutar NPM en un nivel de privilegios mayor.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>¿Ha recibido un error relacionado con DTRACE?
Al instalar Restify puede que vea un error similar al siguiente:

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
Restify proporciona un mecanismo eficaz para rastrear llamadas a REST mediante DTrace. Sin embargo, muchos sistemas operativos no tienen DTrace. Puede omitir estos errores con seguridad.

La salida de este comando debe ser similar a esta:

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


## <a name="step-6-install-passportjs-in-your-web-api"></a>Paso 6: Instalación de Passport.js en su API web
[Passport](http://passportjs.org/) es middleware de autenticación para Node.js. Passport es flexible y modular, y se puede usar discretamente en cualquier aplicación web de Restify o basada en Express. Un conjunto completo de estrategias da soporte a la autenticación con nombre de usuario y contraseña, Facebook, Twitter, etc.

Hemos desarrollado una estrategia para Azure Active Directory. Instalamos este módulo y, después, agregamos el complemento de la estrategia de Azure Active Directory.

1. Desde la línea de comandos, cambie al directorio **azuread**.

2. Para instalar passport.js, escriba el siguiente comando:

    `npm install passport`

    La salida del comando debe ser similar a esta:

``
        passport@0.1.17 node_modules\passport
        ├── pause@0.0.1
        └── pkginfo@0.2.3
``

## <a name="step-7-add-passport-azure-ad-to-your-web-api"></a>Paso 7: Incorporación de Passport-Azure-AD a su API web
A continuación, se agrega la estrategia de OAuth mediante `passport-azure-ad`, un conjunto de estrategias que conectan Azure Active Directory a Passport. En este ejemplo de API de REST, esta estrategia se usa para los tokens de portador.

> [!NOTE]
> Aunque OAuth2 proporciona un marco en el que se puede emitir cualquier tipo de token conocido, habitualmente solo se usan determinados tipos de token. Los tokens de portador son los que más se usan para proteger los puntos de conexión. Son el tipo de token más emitido en OAuth2. Muchas implementaciones asumen que los tokens de portador son el único tipo de token que se emite.
>
>

Desde la línea de comandos, cambie al directorio **azuread**.

Escriba el siguiente comando para instalar el módulo `passport-azure-ad module` de Passport.js:

`npm install passport-azure-ad`

La salida del comando debe ser similar a esta:


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



## <a name="step-8-add-mongodb-modules-to-your-web-api"></a>Paso 8: Incorporación de módulos MongoDB a su API web
MongoDB se usa como almacén de datos. Por eso, es preciso instalar el complemento ampliamente utilizado denominado Mongoose para administrar los modelos y esquemas. También es necesario instalar al controlador de base de datos para MongoDB (que también se denomina MongoDB).

 `npm install mongoose`

## <a name="step-9-install-additional-modules"></a>Paso 9: Instalación de módulos adicionales
A continuación, se instalaran los restantes módulos requeridos.

1. Desde la línea de comandos, cambie el directorio a la carpeta **azuread**, en caso de que no se encuentre ya en ella.

    `cd azuread`

2. Escriba los siguientes comandos para instalar estos módulos en su directorio **node_modules**:

    * `npm install assert-plus`
    * `npm install bunyan`
    * `npm update`

## <a name="step-10-create-a-serverjs-with-your-dependencies"></a>Paso 10: Creación de server.js con sus dependencias
El archivo server.js proporciona la mayor parte de la funcionalidad de nuestro servidor de API web. La mayoría del código se agrega a este archivo. Para la producción, se recomienda refactorizar la funcionalidad en archivos más pequeños, como controladores y rutas independientes. En esta demostración, se usa server.js para esta funcionalidad.

1. Desde la línea de comandos, cambie el directorio a la carpeta **azuread**, en caso de que no se encuentre ya en ella.

    `cd azuread`

2. Cree un archivo `server.js` en el editor que prefiera y agregue la siguiente información:

    ```Javascript
        'use strict';

        /**
         * Module dependencies.
         */

        var fs = require('fs');
        var path = require('path');
        var util = require('util');
        var assert = require('assert-plus');
        var bunyan = require('bunyan');
        var getopt = require('posix-getopt');
        var mongoose = require('mongoose/');
        var restify = require('restify');
        var passport = require('passport');
      var BearerStrategy = require('passport-azure-ad').BearerStrategy;
    ```

3. Guarde el archivo . Volveremos a él en breve.

## <a name="step-11-create-a-config-file-to-store-your-azure-ad-settings"></a>Paso 11: Creación de un archivo de configuración para almacenar la configuración de Azure AD
Este archivo de código pasa los parámetros de configuración desde el Portal de Azure Active Directory a Passport.js. Estos valores de configuración se crearon al agregar la API web al portal en la primera parte del tutorial. Explicamos qué incluir en los valores de estos parámetros después de haber copiado el código.

1. Desde la línea de comandos, cambie el directorio a la carpeta **azuread**, en caso de que no se encuentre ya en ella.

    `cd azuread`

2. Cree un archivo `config.js` en el editor que prefiera y agregue la siguiente información:

    ```Javascript
         exports.creds = {
             mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
             clientID: 'your client ID',
             audience: 'your application URL',
            // you cannot have users from multiple tenants sign in to your server unless you use the common endpoint
          // example: https://login.microsoftonline.com/common/.well-known/openid-configuration
             identityMetadata: 'https://login.microsoftonline.com/<your tenant id>/.well-known/openid-configuration',
             validateIssuer: true, // if you have validation on, you cannot have users from multiple tenants sign in to your server
             passReqToCallback: false,
             loggingLevel: 'info' // valid are 'info', 'warn', 'error'. Error always goes to stderr in Unix.

         };
    ```
3. Guarde el archivo .

## <a name="step-12-add-configuration-values-to-your-serverjs-file"></a>Paso 12: Incorporación de valores de configuración a su archivo server.js
Estos valores se deben leer del archivo .config que creó en nuestra aplicación. Para ello, agregamos el archivo .config como un recurso necesario a nuestra aplicación. Luego, establecemos las variables globales que coincidan con las variables del documento config.js.

1. Desde la línea de comandos, cambie el directorio a la carpeta **azuread**, en caso de que no se encuentre ya en ella.

    `cd azuread`

2. Abra el archivo `server.js` en el editor que prefiera y agregue la siguiente información:

    ```Javascript
    var config = require('./config');
    ```
3. Después, agregue una nueva sección a `server.js` con el código siguiente:

    ```Javascript
    var options = {
        // The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
        identityMetadata: config.creds.identityMetadata,
        clientID: config.creds.clientID,
        validateIssuer: config.creds.validateIssuer,
        audience: config.creds.audience,
        passReqToCallback: config.creds.passReqToCallback,
        loggingLevel: config.creds.loggingLevel

    };

    // Array to hold logged in users and the current logged in user (owner).
    var users = [];
    var owner = null;

    // Our logger.
    var log = bunyan.createLogger({
        name: 'Azure Active Directory Bearer Sample',
             streams: [
            {
                stream: process.stderr,
                level: "error",
                name: "error"
            },
            {
                stream: process.stdout,
                level: "warn",
                name: "console"
            }, ]
    });

      // If the logging level is specified, switch to it.
      if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    ```

4. Guarde el archivo .

## <a name="step-13-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Paso 13: Incorporación del modelo MongoDB y la información del esquema mediante Mongoose
Ahora toda esta preparación va a empezar a dar sus frutos cuando combinemos estos tres archivos en un servicio API de REST.

En este tutorial, usamos MongoDB para almacenar nuestras tareas, como se describe en el Paso 4.

En el archivo `config.js`, que creamos en el Paso 11, llamamos a nuestra base de datos `tasklist`, ya que fue lo que incluimos al final de nuestra dirección URL de conexión **mongoose_auth_local**. No es necesario crear esta base de datos por anticipado en MongoDB. En su lugar, MongoDB la crea automáticamente la primera vez que se ejecuta nuestra aplicación de servidor (en caso de que la base de datos no exista).

Ahora que hemos indicado al servidor la base de datos MongoDB que deseamos usar, debemos escribir código adicional para crear el modelo y esquema de las tareas de nuestro servidor.

### <a name="discussion-of-the-model"></a>Análisis del modelo
Nuestro modelo de esquema es muy sencillo. Expándalo si fuera necesario.

NAME: el nombre de la persona a la que se asigna a la tarea. Un valor de **cadena**.

TASK: la propia tarea. Un valor de **cadena**.

DATE: la fecha en que vence la tarea. Un valor **DATETIME**.

COMPLETED: si la tarea se ha completado, o no. Un valor **booleano**.

### <a name="creating-the-schema-in-the-code"></a>Creación del esquema en el código
1. Desde la línea de comandos, cambie el directorio a la carpeta **azuread**, en caso de que no se encuentre ya en ella.

    `cd azuread`

2. Abra el archivo `server.js` en el editor que prefiera y agregue la siguiente información debajo de la entrada de configuración:

    ```Javascript
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');

    // Here we create a schema to store our tasks and users. It's a fairly simple schema for now.
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
Como puede imaginar por el código, primero se crea un esquema. Después se crea un objeto de modelo que se usa para almacenar los datos en el código cuando se definen las **rutas**.

## <a name="step-14-add-our-routes-for-our-task-rest-api-server"></a>Paso 14: Adición de rutas para el servidor de API de REST de la tarea
Ahora que tenemos un modelo de base de datos con el que trabajar, agreguemos las rutas que vamos a usar para nuestro servidor de API de REST.

### <a name="about-routes-in-restify"></a>Información acerca de las rutas en Restify
Las rutas funcionan en Restify de la misma forma que en la pila de Express. Defina rutas mediante el URI al que espera que llamen las aplicaciones cliente. Normalmente, las rutas se definen en un archivo independiente. Para nuestros propósitos, las rutas las ponemos en el archivo server.js. Se recomienda factorizarlas en su propio archivo para que se puedan usar en producción.

Este es un patrón típico de una ruta de Restify:

```Javascript
function createObject(req, res, next) {

// Do work on object.

 _object.name = req.params.object; // passed value is in req.params under object

 ///...

return next(); // Keep the server going.
}

....

server.post('/service/:add/:object', createObject); // Calls createObject on routes that match this.

```


Este es el patrón en su nivel más básico. Restify (y Express) proporcionan una funcionalidad mucho más profunda, como definir los tipos de aplicación y proporcionar un enrutamiento complejo en distintos puntos de conexión. Estas rutas van a ser simples.

### <a name="add-default-routes-to-our-server"></a>Agregar rutas predeterminadas a nuestro servidor
Ahora se agregan las rutas CRUD básicas de Create, Retrieve, Update y Delete.

1. Desde la línea de comandos, cambie el directorio a la carpeta **azuread**, en caso de que no se encuentre ya en ella:

    `cd azuread`

2. Abra el archivo `server.js` en el editor que prefiera y agregue la siguiente información debajo de las anteriores entradas de base de datos que creó:

```Javascript

/**
 *
 * APIs for our REST Task server.
 */

// Create a task.

function createTask(req, res, next) {

    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it, and save it to Mongodb.
    var _task = new Task();

    if (!req.params.task) {
        req.log.warn('createTodo: missing task');
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

/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err) {
            return next(err);
        }

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Did you initialize the database as stated in the README?");
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

### <a name="add-error-handling-in-our-apis"></a>Incorporación del control de errores a nuestras API
```

///--- Errors for communicating something interesting back to the client.

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


## <a name="step-15-create-your-server"></a>Paso 15: Creación de un servidor
Hemos definido la base de datos y las rutas están en su lugar. Lo último que hay que hacer es agregar la instancia del servidor que administra las llamadas.

En Restify (y Express) se puede personalizar mucho un servidor de API de REST, pero vamos a volver a usar la configuración más básica.

```Javascript
/**
 * Our server.
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads.
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());

// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in).
server.use(restify.requestLogger());

// Allow five requests per second by IP, and burst to 10.
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want.
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allow for JSON mapping to REST.
```

## <a name="step-16-add-the-routes-to-the-server-without-authentication-for-now"></a>Paso 16: Adición de las rutas al servidor (sin autenticación por ahora)
```Javascript
/// Now the real handlers. Here we just CRUD.
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'.
/* In the pasport.authenticate() method. We set 'session: false' because REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method as follows:
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
// Register a default '/' handler.
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

## <a name="step-17-run-the-server-before-adding-oauth-support"></a>Paso 17: Ejecución del servidor (antes de agregar la compatibilidad con OAuth)
Pruebe el servidor antes de agregar la autenticación.

La forma más fácil de probarlo es usar curl en una línea de comandos. Antes de hacerlo, necesitamos una utilidad que nos permita analizar el resultado como JSON.

1. Instale la siguiente herramienta JSON (en todos los ejemplos siguientes se usa esta herramienta):

    `$npm install -g jsontool`

    De esta forma, se instala la herramienta JSON globalmente. Ahora que ya lo hemos hecho, practiquemos con el servidor:

2. En primer lugar, asegúrese de que se ejecuta la instancia de MongoDB:

    `$sudo mongod`

3. Después, cambie al directorio e inicie curl:

    `$ cd azuread``$ node server.js`

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

4. A continuación, podemos agregar una tarea de este modo:

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

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

Tiene un servidor de API de REST con MongoDB

## <a name="step-18-add-authentication-to-our-rest-api-server"></a>Paso 18: Incorporación de la autenticación al servidor de API de REST
Ahora que tenemos una API de REST en ejecución, podemos empezar a darle una utilidad con Azure AD.

Desde la línea de comandos, cambie el directorio a la carpeta **azuread**, en caso de que no se encuentre ya en ella.

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Use el elemento OIDCBearerStrategy que se incluye con passport-azure-ad
Hasta ahora hemos creado un servidor típico de TODO de REST sin ningún tipo de autorización. Finalmente, empezamos a prepararlo.

1. En primer lugar, necesitamos indicar que queremos usar Passport. Incluya esto justo después de la otra configuración del servidor:

    ```Javascript
            // Let's start using Passport.js.

            server.use(passport.initialize()); // Starts passport.
            server.use(passport.session()); // Provides session support.
    ```
    > [!TIP]
    > Al escribir las API, se recomienda vincular siempre los datos a un elemento único del token cuya identidad el usuario no pueda suplantar. Cuando este servidor almacena tareas pendiente, lo hace en función del id. de objeto del usuario en el token (al que se llama a través de token.oid), que se colocó en el campo "owner". Esto garantiza que ese usuario es el único que puede acceder a sus tareas pendientes. En la API "owner" no queda expuesto, por lo que un usuario externo puede solicitar las tareas pendientes de otros, aunque estén autenticados.                    

2. A continuación, vamos a usar la estrategia de portador que viene con `passport-azure-ad`. Por el momento veamos el código y el resto se explicará en breve. Colóquelo después de lo que pegó anteriormente:

```Javascript
    /**
    /*
    /* Calling the OIDCBearerStrategy and managing users.
    /*
    /* Passport pattern provides the need to manage users and info tokens
    /* with a FindorCreate() method that must be provided by the implementor.
    /* Here we just auto-register any user and implement a FindById().
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


    var bearerStrategy = new BearerStrategy(options,
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

    passport.use(bearerStrategy);
```

Passport usa un patrón similar para todas sus estrategias (Twitter, Facebook, etc.) al que se ajustan todos los escritores de estrategias. Si se examina la estrategia, se percibe que se pasan los parámetros function, que tiene un token, y done. La estrategia se devuelve después de que realiza su trabajo. Después de que lo haga, almacenamos el usuario y guardamos provisionalmente el token, con el fin de que no sea preciso volver a pedirlo.

> [!IMPORTANT]
> El código anterior toma cualquier usuario que se autentique en el servidor. Esto se conoce como registro automático. En los servidores de producción, se recomienda no permitir que acceda nadie que no haya pasado por el proceso de registro que decida. Este suele ser el patrón que se observa en las aplicaciones de consumidor que le permiten registrarse con Facebook, pero que luego piden que se rellene información adicional. Si no se tratara de un programa de línea de comandos, el correo electrónico se podría haber extraído del objeto de token devuelto y, a continuación, haber pedido al usuario que rellenara la información adicional. Puesto que se trata de un servidor de prueba, simplemente los agregaremos a la base de datos en memoria.
>
>

### <a name="protect-some-endpoints"></a>Protección de puntos de conexión
Para proteger los puntos de conexión, especifique la llamada `passport.authenticate()` con el protocolo que desea utilizar.

Para que nuestro código servidor haga algo más interesante, vamos a editar la ruta.

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="step-19-run-your-server-application-again-and-ensure-it-rejects-you"></a>Paso 19: ejecutar de nuevo su aplicación de servidor y asegurarse de que le rechaza
Usemos `curl` nuevamente para ver si ahora tenemos protección de OAuth2 contra nuestros extremos. Esta prueba se realiza antes de ejecutar cualquiera de nuestros SDK de cliente en este punto de conexión. Los encabezados que se devuelven deberían ser suficientes para indicarnos si seguimos el camino correcto.

1. En primer lugar, asegúrese de que se ejecuta la instancia de MongoDB:

    `$sudo mongod`

2. Después, cambie al directorio e inicie curl.

      `$ cd azuread``$ node server.js`

3. Pruebe una operación POST básica.

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

Aquí se busca la respuesta 401. Esta respuesta indica que la capa de Passport intenta realizar la redirección al punto de conexión autorizado, que es exactamente lo que desea.

## <a name="next-steps"></a>Pasos siguientes
Ha avanzado todo lo posible con este servidor sin usar un cliente compatible con OAuth2. Tendrá que seguir un tutorial adicional.

Ya ha aprendido cómo implementar una API de REST mediante Restify y OAuth2. También tiene más que suficiente código para continuar el desarrollo de su servicio y aprender a basarse en este ejemplo.

Si le interesan los próximos pasos en su recorrido por ADAL, a continuación hay algunos clientes ADAL compatibles con los que le recomendamos que siga trabajando.

Clona su equipo de desarrollador y configúrelo como se describe en el tutorial.

[ADAL para iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL para Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
