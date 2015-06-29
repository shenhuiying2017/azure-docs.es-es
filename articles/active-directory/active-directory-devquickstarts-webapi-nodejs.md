<properties
	pageTitle="Introducción a NodeJS de Azure AD | Microsoft Azure"
	description="Cómo crear una API web Node.js que se integra con Azure AD para su autenticación."
	services="active-directory"
	documentationCenter="nodejs"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="brandwe"/>

# Introducción a WEB-API para Node

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Este tutorial le proporcionará una forma rápida y fácil de configurar un servicio API REST que se integra con Azure Active Directory para la protección de API mediante el protocolo OAuth2. El servidor de ejemplo incluido en la descarga está diseñado para ejecutarse en cualquier plataforma excepto en Linux y OSX de destino.

Al final de este tutorial, debe poder crear un servidor de API de REST en ejecución con las siguientes características:

* Un servidor node.js que ejecuta una interfaz API de REST con JSON mediante MongoDB como almacenamiento persistente
* API de REST que aprovechan la protección de API de OAuth2 con tokens portadores mediante Azure Active Directory


Hemos publicado todo el código fuente para este ejemplo en ejecución en GitHub bajo una licencia de Apache 2.0, así que no dude en clonar (o mejor aún, bifurcar) y proporcionar comentarios y solicitudes de extracción.

## Acerca de módulos Node.js

Vamos a usar módulos Node.js en este tutorial. Los módulos son paquetes JavaScript que pueden cargarse y que proporcionan una funcionalidad específica para su aplicación. Suele instalar módulos con la herramienta de línea de comandos NPM de Node.js en el directorio de instalación de NPM, pero se incluyen algunos módulos, como HTTP, en el paquete Node.js principal. Los módulos instalados se guardan en el directorio node_modules en la raíz de su directorio de instalación de Node.js. Los módulos del directorio node_modules mantienen sus propios directorios node_modules que contienen los módulos de los que dependen y cada módulo requerido tiene un directorio node_modules. Esta estructura de directorios recursiva representa la cadena de dependencia.

Esta estructura de cadenas de dependencia se traduce en un mayor espacio de utilización de la aplicación, pero garantiza el cumplimiento de todas las dependencias y el uso de la versión de los módulos empleados en desarrollo también en producción. Esto hace que el comportamiento de la aplicación de producción sea más predecible y evita los problemas de control de versiones que pueden afectar a los usuarios.

## Paso 1: registrar un inquilino de Azure AD

Para usar este ejemplo, será necesario un inquilino de Azure Active Directory. Si no está seguro de qué es un inquilino o cómo se puede obtener uno, consulte [Cómo obtener un inquilino de Azure AD](active-directory-howto-tenant.md).

## Paso 2: agregar una API web a su inquilino

Después de obtener el inquilino de Azure Active Directory, agregue esta aplicación de ejemplo a su inquilino de modo que la pueda usar para proteger la API.

Para habilitar su aplicación a fin de autenticar a los usuarios, primero deberá registrar una nueva aplicación en su inquilino.

- Inicie sesión en el Portal de administración de Azure.
- En el panel de navegación izquierdo, haga clic en **Active Directory**.
- Seleccione el inquilino donde desea registrar la aplicación.
- Haga clic en la pestaña **Aplicaciones** y en Agregar en el cajón inferior.
- Siga las indicaciones y cree una nueva **Aplicación web y/o API web**.
    - El **nombre** de la aplicación describirá su aplicación a los usuarios finales
    -	La **dirección URL de inicio de sesión** es la dirección URL base de su aplicación. El valor predeterminado del esquema es `https://localhost:8888`.
    - El **URI de id. de aplicación** es un identificador único de su aplicación. La convención consiste en usar `https://<tenant-domain>/<app-name>`, p. ej. `https://contoso.onmicrosoft.com/my-first-aad-app`
- Una vez que haya completado el registro, AAD asignará a su aplicación un identificador de cliente único. Necesitará este valor en las secciones siguientes, de modo que cópielo desde la pestaña Configurar.

## Paso 3: descargar node.js para su plataforma
Para usar correctamente este ejemplo, debe disponer de una instalación en funcionamiento de Node.js.

Instale Node.js desde [http://nodejs.org](http://nodejs.org).

## Paso 4: instalar MongoDB en su plataforma

Para usar correctamente este ejemplo, debe disponer de una instalación en funcionamiento de MongoDB. Usaremos MongoDB para que nuestra API de REST sea persistente en instancias de servidor.

Instale MongoDB desde [http://mongodb.org](http://www.mongodb.org).

**NOTA:** en este tutorial se asume que usa la instalación predeterminada y los extremos de servidor de MongoDB, que en el momento de escribir este artículo es: mongodb://localhost


## Paso 5: instalar los módulos Restify en su API web

Vamos a usar Restify para crear nuestra API de REST. Restify es un marco de trabajo de la aplicación de Node.js mínimo y flexible de Express que cuenta con un sólido conjunto de características para basar API de REST en Connect.

### Instalar Restify

Desde la línea de comandos, cambie los directorios por el directorio azuread. Si el directorio **azuread** no existe, créelo.

`cd azuread - or- mkdir azuread; cd azuread`

Escriba el siguiente comando:

`npm install restify`

Este comando instala Restify.

#### ¿HA RECIBIDO UN ERROR?

Cuando se usa npm en algunos sistemas operativos, es posible recibir un error Error: EPERM, chmod '/ usr/local/bin/..' y que se solicite ejecutar la cuenta como administrador. Si esto ocurre, utilice el comando sudo para ejecutar npm en un nivel de privilegio más elevado.

#### ¿HA RECIBIDO UN ERROR RELACIONADO CON DTRACE?

Es posible que vea algo parecido a esto al instalar Restify:

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


## Paso 6: instalar Passport.js en su API web

[Passport](http://passportjs.org/) es middleware de autenticación para Node.js. Muy flexible y modular, Passport puede pasar discretamente a cualquier aplicación web basada en Express o Restify. Un conjunto completo de estrategias admite la autenticación mediante un nombre de usuario y contraseña, Facebook, Twitter y mucho más. Hemos desarrollado una estrategia para Azure Active Directory. Instalaremos este módulo y, a continuación, agregaremos el complemento de la estrategia de Azure Active Directory.

Desde la línea de comandos, cambie los directorios por el directorio azuread.

Escriba el siguiente comando para instalar passport.js.

`npm install passport`

El resultado del comando debe ser similar al siguiente:

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## Paso 7: incorporar la compatibilidad del token portador Passport.js a su API web

A continuación, agregaremos la estrategia portadora mediante passport-bearer-http, un controlador portador para [Passport](http://passportjs.org/). También incorporaremos la compatibilidad del controlador de token JWT mediante node-jwt.

**NOTA:** aunque OAuth2 proporciona un marco de trabajo en el que se puede emitir cualquier tipo de token conocido, solo ciertos tipos de token gozan de uso generalizado. Para la protección de extremos, que han resultado ser tokens portadores. Los tokens portadores son el tipo de token más emitido de OAuth2 y muchas implementaciones asumen que son el único tipo de token emitido.

Desde la línea de comandos, cambie los directorios por el directorio **azuread**.

Escriba el siguiente comando para instalar módulos Passport.js.

- `npm install passport-oauth`
- `npm install passport-http-bearer`
- `npm install node-jwt`

El resultado del comando debe ser similar al siguiente:

	ms-passport-wsfed-saml2@0.3.8 node_modules\passport-oauth  
	├── xtend@2.0.3
	├── xml-crypto@0.0.9
	├── xmldom@0.1.13
	└── xml2js@0.1.14 (sax@0.5.2)


## Paso 8: agregar módulos MongoDB para su API web

Vamos a usar MongoDB como nuestro almacén de datos. Por ese motivo, debemos instalar tanto el complemento ampliamente usado para administrar modelos y esquemas llamado Mongoose como el controlador de base de datos para MongoDB, también llamado MongoDB.


* `npm install mongoose`
* `npm install mongodb`

## Paso 9: instalar módulos adicionales

A continuación, instalaremos los módulos necesarios restantes.


Desde la línea de comandos, cambie los directorios por la carpeta **azuread** si no se encuentra ya en ella:

`cd azuread`


Escriba los comandos siguientes para instalar los módulos siguientes en su directorio node_modules:

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


## Paso 10: crear server.js con sus dependencias

El archivo server.js proporcionará la mayor parte de nuestra funcionalidad para nuestro servidor de API web. Agregaremos la mayor parte de nuestro código a este archivo. Con fines de producción, rediseñaría la funcionalidad para que estuviera en archivos más pequeños, como controladores y rutas independientes. En esta demostración, usaremos server.js para esta funcionalidad.

Desde la línea de comandos, cambie los directorios por la carpeta **azuread** si no se encuentra ya en ella:

`cd azuread`

Cree un archivo `server.js` en nuestro editor favorito y agregue la siguiente información:

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
```

Guarde el archivo . Volveremos a él en breve.

## Paso 11: crear un archivo de configuración para almacenar la configuración de Azure AD

Este archivo de código pasa los parámetros de configuración desde el Portal de Azure Active Directory a Passport.js. Creó estos valores de configuración al agregar la API web al portal en la primera parte del tutorial. Explicaremos qué incluir en los valores de estos parámetros después de haber copiado el código.


Desde la línea de comandos, cambie los directorios por la carpeta **azuread** si no se encuentra ya en ella:

`cd azuread`

Cree un archivo `config.js` en nuestro editor favorito y agregue la siguiente información:

```Javascript
// Don't commit this file to your public repos
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
    openid_configuration: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
    openid_keys: 'https://login.microsoftonline.com/common/discovery/keys', // For using Microsoft you should never need to change this. If absent will attempt to get from openid_configuration
}

```



**NOTA:** probablemente nunca tendrá que cambiar estos valores.

**NOTA:** distribuimos nuestras claves con frecuencia. Asegúrese de extraer siempre de la dirección URL "openid_keys" y de que la aplicación pueda tener acceso a Internet.


## Paso 12: agregar configuración a su archivo server.js

Debemos leer estos valores del archivo de configuración que acaba de crear en nuestra aplicación. Para ello, basta con que agreguemos el archivo .config como recurso necesario en nuestra aplicación y, a continuación, establezcamos las variables globales en las del documento config.js

Desde la línea de comandos, cambie los directorios por la carpeta **azuread** si no se encuentra ya en ella:

`cd azuread`

Abra su archivo `server.js` en nuestro editor favorito y agregue la siguiente información:

```Javascript
var config = require('./config');
```
A continuación, agregue una nueva sección a `server.js` con el código siguiente:

```Javascript
/**
* Setup some configuration
*/
var mongoose = require('mongoose/');
var serverPort = process.env.PORT || 8888;
var serverURI = ( process.env.PORT ) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

```
## Paso 13: crear un archivo auxiliar metadata.js para ayudar a analizar metadatos/tokens

Dado que el objetivo es mantener solo la lógica de aplicación en el archivo server.js, tiene sentido incluir algunos métodos auxiliares en un archivo independiente. Estos métodos simplemente nos ayudan a analizar los metadatos OpenID Connect y no están relacionados con el escenario principal. Es mejor eliminarlos. Agregaremos más a este archivo a medida que avancemos por el tutorial.

***NOTA:*** observará que este archivo metadata.js analiza XML para SAML y WS-Fed, así como JSON para OpenID Connect. Eso se realiza por diseño, pues usará este archivo en nuestros otros ejemplos también. Puede omitirlo con seguridad.

Desde la línea de comandos, cambie los directorios por la carpeta **azuread** si no se encuentra ya en ella:

`cd azuread`

Cree un archivo `metadata.js` en nuestro editor favorito y agregue la siguiente información:

```Javascript

'use strict';

var xml2js = require('xml2js');
var request = require('request');
var aadutils = require('./aadutils');
var async = require('async');

// Logging

var bunyan = require('bunyan');
var log = bunyan.createLogger({name: 'Microsoft OpenID Connect Passport Strategy'});

var Metadata = function (url, authtype) {


  if(!url) {
    throw new Error("Metadata: url is a required argument");
  }
  if(!authtype) {
    throw new Error('OIDCBearerStrategy requires an authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"');
  }

  this.url = url;
  this.metadata = null;
  this.authtype = authtype;
  log.info(authtype, 'Metadata requested for authentication type');
};

Object.defineProperty(Metadata, 'url', {
  get: function () {
    return this.url;
  }
});

Object.defineProperty(Metadata, 'saml', {
  get: function () {
    return this.saml;
  }
});

Object.defineProperty(Metadata, 'wsfed', {
  get: function () {
    return this.wsfed;
  }
});

Object.defineProperty(Metadata, 'oidc', {
  get: function () {
    return this.oidc;
  }
});


Object.defineProperty(Metadata, 'metadata', {
  get: function () {
    return this.metadata;
  }
});

Metadata.prototype.updateSamlMetadata = function(doc, next) {
  log.info('Request to update the SAML Metadata');
  try {

    this.saml = {};

    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var idp = aadutils.getElement(entity, 'IDPSSODescriptor');
    var signOn = aadutils.getElement(idp[0], 'SingleSignOnService');
    var signOff = aadutils.getElement(idp[0], 'SingleLogoutService');
    var keyDescriptor = aadutils.getElement(idp[0], 'KeyDescriptor');
    this.saml.loginEndpoint = signOn[0].$.Location;
    this.saml.logoutEndpoint = signOff[0].$.Location;

    // copy the x509 certs from the metadata
    this.saml.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.saml.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid SAMLP Federation Metadata ' + e.message));
  }
};

Metadata.prototype.updateOidcMetadata = function(doc, next) {
  log.info('Request to update the Open ID Connect Metadata');
  try {
    this.oidc = {};

    var issuer = doc['issuer'];
    var keyDescriptor = aadutils.getElement(idp[0], 'keys');

    // copy the x509 certs from the metadata
    this.oidc.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.oidc.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid Open ID Connect Federation Metadata ' + e.message));
  }
};


Metadata.prototype.updateWsfedMetadata = function(doc, next) {
  log.info('Request to update the WS Federation Metadata');
  try {
    this.wsfed = {};
    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var roles = aadutils.getElement(entity, 'RoleDescriptor');
    for(var i = 0; i < roles.length; i++) {
      var role = roles[i];
      if(role['fed:SecurityTokenServiceEndpoint']) {
        var endpoint = role['fed:SecurityTokenServiceEndpoint'];
        var endPointReference = aadutils.getFirstElement(endpoint[0],'EndpointReference');
        this.wsfed.loginEndpoint = aadutils.getFirstElement(endPointReference,'Address');

        var keyDescriptor = aadutils.getElement(role, 'KeyDescriptor');
        // copy the x509 certs from the metadata
        this.wsfed.certs = [];
        for (var j=0;j<keyDescriptor.length;j++) {
          this.wsfed.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
        }
        break;
      }
    }

    return next(null);
  } catch (e) {
    next(new Error('Invalid WSFED Federation Metadata ' + e.message));
  }
};

Metadata.prototype.fetch = function(callback) {
  var self = this;
  log.info("Fetching metadata from the provided metadata URL: " + self.url);
  async.waterfall([
    // fetch the Federation metadata for the AAD tenant
    function(next){
      request(self.url, function (err, response, body) {
        if(err) {
          next(err);
        } else if(response.statusCode !== 200) {
          next(new Error("Error:" + response.statusCode +  " Cannot get AAD Federation metadata from " + self.url));
        } else {
          log.info(body, "retreived");
          next(null, body);
        }
      });
    },
    function(body, next){
      // parse the AAD Federation metadata xml

      if(self.authtype == "saml" || self.authtype == "wsfed") {
      log.info(body, "Parsing XML retreived from the endpoint");
      var parser = new xml2js.Parser({explicitRoot:true});
      // Note: xml responses from Azure AAD have a leading \ufeff which breaks xml2js parser!
      parser.parseString(body.replace("\ufeff", ""), function (err, data) {
        self.metatdata = data;
        next(err);

      });
    } else if(self.authtype == "oidc") {
      log.info(body, "Parsing JSON retreived from the endpoint");
      JSON.parse(body, function (err, data) {
        self.metatdata = data;
        next(err);
      });

    } else {

       next(new Error("Error: No Authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"));
    }

    },

    function(next){
      if(self.authtype = "saml") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateSamlMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "wsfed") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateWsfedMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "oidc") {
      self.updateOidcMetadata(self.metadata, next);
    }},
  ], function (err) {
    // return err or success (err === null) to callback
    callback(err);
  });
};

exports.Metadata = Metadata;
```
Como puede ver en el código, simplemente toma la dirección URL openid que pasó a `config.js` y, a continuación, la analiza para obtener información que usaremos en el archivo `server.js`. Es más que bienvenido a la investigación de este código y su expansión en caso necesario.

### Cargar el archivo metadata.js en su server.js

Debemos indicar a nuestro servidor dónde obtener los métodos que acaba de escribir.

Desde la línea de comandos, cambie los directorios por la carpeta **azuread** si no se encuentra ya en ella:

`cd azuread`

Abra su archivo `server.js` en nuestro editor favorito y agregue la siguiente información:

```Javascript
var metadata = require('./metadata);
```
A continuación, agregue al final de la sección `Configuration` esta llamada para enviar el documento de metadatos en nuestro `config.js` al analizador al que acabamos de escribir:

```Javascript
this.aadutils = new var Metadata = require('./metadata').Metadata;
```

## Paso 14: agregar el modelo MongoDB y la información del esquema mediante Mongoose

Ahora toda esta preparación va a empezar a dar sus frutos al incluir juntos estos tres archivos en un servicio API REST.

En este tutorial, usaremos MongoDB para almacenar nuestras tareas como se describe en ***Paso 4***.

Como se indicó en el archivo `config.js` que creamos en ***Paso 11***, llamamos a nuestra base de datos `tasklist`, ya que era lo que incluimos al final de nuestra dirección URL de conexión mongoose_auth_local. No es necesario crear de antemano esta base de datos en MongoDB, pues la creará por nosotros la primera vez que ejecutemos nuestra aplicación de servidor (asumiendo que aún no exista).

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
/**
*
* Connect to MongoDB
*/

global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;  
```
Se conectará al servidor de MongoDB y nos devolverá un objeto de esquema.

#### Con el esquema, cree nuestro modelo en el código

Debajo del código que escribió anteriormente, agregue el código siguiente:

```Javascript
/**
/ Here we create a schema to store our tasks. Pretty simple schema for now.
*/

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

## Paso 15: agregar nuestras rutas para el servidor de API de REST de nuestra tarea

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
 * APIs
 */

function createTask(req, res, next) {

	// Resitify currently has a bug which doesn't allow you to set default headers
  	// This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
  var _task = new Task();

        if (!req.params.task) {
                req.log.warn('createTask: missing task');
                next(new MissingTaskError());
                return;
        }


  _task.owner = req.params.owner;
   _task.task = req.params.task;
   _task.date = new Date();

  _task.save(function (err) {
  	if (err) {
        req.log.warn(err, 'createTask: unable to save');
        next(err);
    } else {
    res.send(201, _task);

			}
  });

  return next();

}


/**
 * Deletes a Task by name
 */
function removeTask(req, res, next) {

        Task.remove( { task:req.params.task }, function (err) {
                if (err) {
                        req.log.warn(err,
                                     'removeTask: unable to delete %s',
                                     req.params.task);
                        next(err);
                } else {
                        res.send(204);
                        next();
                }
        });
}

/**
 * Deletes all Tasks. A wipe
 */
function removeAll(req, res, next) {
        Task.remove();
        res.send(204);
        return next();
}    });
}


/**
 *
 *
 *
 */
function getTask(req, res, next) {


        Task.find(req.params.name, function (err, data) {
                if (err) {
                        req.log.warn(err, 'get: unable to read %s', req.params.name);
                        next(err);
                        return;
                }

                res.json(data);
        });

        return next();
}


/**
 * Simple returns the list of TODOs that were loaded.
 *
 */

function listTasks(req, res, next) {
  // Resitify currently has a bug which doesn't allow you to set default headers
  // This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

  console.log("server getTasks");

  Task.find().limit(20).sort('date').exec(function (err,data) {

    if (err)
      return next(err);

    if (data.length > 0) {
            console.log(data);
        }

    if (!data.length) {
            console.log('there was a problem');
            console.log(err);
            console.log("There is no tasks in the database. Did you initalize the database as stated in the README?");
        }

    else {

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


function TaskExistsError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 409,
                restCode: 'TaskExists',
                message: name + ' already exists',
                constructorOpt: TaskExistsError
        });

        this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 404,
                restCode: 'TaskNotFound',
                message: name + ' was not found',
                constructorOpt: TaskNotFoundError
        });

        this.name = 'TaskNotFoundError';
}

util.inherits(TaskNotFoundError, restify.RestError);
```


## Paso 16: crear su servidor

Tenemos nuestra base de datos definida, nuestras rutas preparadas y lo último que se debe hacer es agregar la instancia de nuestro servidor que administrará nuestras llamadas.

Restify (y Express) tienen una personalización muy profunda que puede desarrollar para un servidor de API de REST, pero una vez más usaremos la configuración más básica para nuestros propósitos.

```Javascript
/**
 * Our Server
 */


var server = restify.createServer({
        name: "Azure Active Directroy TODO Server",
    version: "1.0.0",
    formatters: {
        'application/json': function(req, res, body){
            if(req.params.callback){
                var callbackFunctionName = req.params.callback.replace(/[^A-Za-z0-9_.]/g, '');
                return callbackFunctionName + "(" + JSON.stringify(body) + ");";
            } else {
                return JSON.stringify(body);
            }
        },
        'text/html': function(req, res, body){
            if (body instanceof Error)
                        return body.stack;

                      if (Buffer.isBuffer(body))
                        return body.toString('base64');

                return util.inspect(body);
        },
        'application/x-www-form-urlencoded': function(req, res, body){
            if (body instanceof Error) {
                    res.statusCode = body.statusCode || 500;
                    body = body.message;
            } else if (typeof (body) === 'object') {
                body = body.task || JSON.stringify(body);
            } else {
                body = body.toString();
            }

        res.setHeader('Content-Length', Buffer.byteLength(body));
        return (body);
        }
    }
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

        // This lets us push JSON to the REST API endpoint as well. Maps x: y as /name:value

        server.use(restify.bodyParser({ mapParams: false }));

        /// Now the real handlers. Here we just CRUD

        server.get('/tasks', listTasks);
        server.head('/tasks', listTasks);
        server.get('/tasks/:name', getTask);
        server.head('/tasks/:name', getTask);
        server.post('/tasks/:name/:task', createTask);
        server.del('/tasks/:name/:task', removeTask);
        server.del('/tasks/:name', removeTask);
        server.del('/tasks', removeAll, function respond(req, res, next) { res.send(204); next(); });


        // Register a default '/' handler

        server.get('/', function root(req, res, next) {
                var routes = [
                        'GET     /',
                        'POST    /tasks/:name/:task',
                        'GET     /tasks',
                        'PUT     /tasks/:name',
                        'GET     /tasks/:name',
                        'DELETE  /tasks/:name/:task'
                ];
                res.send(200, routes);
                next();
        });

  server.listen(serverPort, function() {

  var consoleMessage = '\n Azure Active Directory Tutorial'
  consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++'
  consoleMessage += '\n %s server is listening at %s';
  consoleMessage += '\n Open your browser to %s/tasks\n';
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n'
  consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n'
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n'  

  console.log(consoleMessage, server.name, server.url, server.url, server.url);

});
```

## Paso 17: antes de incorporar la compatibilidad de OAuth, ejecutemos el servidor.

Es una buena idea asegurarnos de no haber cometido ningún error antes de continuar con la parte de OAuth del tutorial.

La forma más fácil de hacerlo es con `curl` en una línea de comandos. Antes de hacerlo, necesitamos una utilidad sencilla que nos permita analizar el resultado como JSON. Para ello, instale la herramienta [json](https://github.com/trentm/json), pues todos los ejemplos siguientes la usan.

	$npm install -g jsontool

De esta forma, se instala la herramienta JSON globalmente. Ahora que eso ya lo hemos hecho, practiquemos con el servidor:

En primer lugar, asegúrese de que se ejecute la instancia de MongoDB...

	$sudo mongod

A continuación, cambie al directorio y empiece el curvado...

	$ cd azuread
	$ node server.js

	$ curl -isS http://127.0.0.1:8888 | json
	HTTP/1.1 200 OK
	Connection: close
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 145
	Date: Wed, 29 Jan 2014 03:41:24 GMT

	[
  	"GET     /",
  	"POST    /tasks/:owner/:task",
  	"GET     /tasks",
  	"DELETE  /tasks",
  	"PUT     /tasks/:owner",
  	"GET     /tasks/:owner",
  	"DELETE  /tasks/:task"
	]

A continuación, podemos agregar una tarea de este modo:

	$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello

La respuesta debe ser:

	HTTP/1.1 201 Created
	Connection: close
	Access-Control-Allow-Origin: *
	Access-Control-Allow-Headers: X-Requested-With
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 5
	Date: Tue, 04 Feb 2014 01:02:26 GMT

	Hello

Además, podemos mostrar tareas de Brandon así:

	$ curl -isS http://127.0.0.1:8888/tasks/brandon/

Si todo esto funciona, estamos listos para agregar OAuth al servidor de API de REST.

## Paso 18: agregar código Passport.js a nuestro servidor de API de REST

Ahora que tenemos una API de REST en ejecución (enhorabuena, por cierto), hagamos que funcione en Azure AD.

Desde la línea de comandos, cambie los directorios por la carpeta **azuread** si no se encuentra ya en ella:

`cd azuread`

### Paso 1: agregar nuestros módulos Passport

Abra su archivo `server.js` en nuestro editor favorito y agregue la siguiente información debajo de donde indicó anteriormente los módulos que debían cargarse. Se encuentra cerca de la parte superior del archivo y debe estar justo después de la importación de `var aadutils = require('./aadutils');`.

```Javascript
/*
*
* Load our old friend Passport for OAuth2 flows
*/

var passport = require('passport')
  , OAuth2Strategy = require('passport-oauth').OAuth2Strategy;
```

### 2. Indicar a nuestro servidor que usamos la autenticación

Abra su archivo `server.js` en nuestro editor favorito y agregue la siguiente información **debajo de server.get()** donde definió sus rutas, pero encima del método **server.listen()**.


Es necesario indicar a Restify que comience a usar su `authorizationParser()` y examinar el contenido del encabezado de autorización.

```Javascript
        server.use(restify.authorizationParser());


```


### 3. Agregar el módulo Passport OAuth2 a nuestro código

Aquí usamos los parámetros OAuth2 específicos que agregamos al archivo config.js. Si nuestro archivo `aadutils.js` hizo su trabajo al analizar nuestro documento de metadatos de federación, todos estos valores deben rellenarse para nosotros incluso si están en blanco en el archivo config.js.

```Javascript
// Now our own handlers for authentication/authorization
// Here we only use Oauth2 from Passport.js

passport.use('provider', new OAuth2Strategy({
    authorizationURL: authEndpoint,
    tokenURL: tokenEndpoint,
    clientID: clientID,
    clientSecret: clientSecret,
    callbackURL: callbackURL
  },
  function(accessToken, refreshToken, profile, done) {
    User.findOrCreate({ UserId: profile.id }, function(err, user) {
      done(err, user);
    });
  }
));

// Let's start using Passport.js

server.use(passport.initialize());

```
### Paso 4: agregar rutas para la autenticación de OAuth

```Javascript
// Redirect the user to the OAuth 2.0 provider for authentication.  When
// complete, the provider will redirect the user back to the application at
//     /auth/provider/callback

server.get('/auth/provider', passport.authenticate('provider'));

// The OAuth 2.0 provider has redirected the user back to the application.
// Finish the authentication process by attempting to obtain an access
// token.  If authorization was granted, the user will be logged in.
// Otherwise, authentication has failed.

server.get('/auth/provider/callback',
  passport.authenticate('provider', { successRedirect: '/',
                                      failureRedirect: '/login' }));
```

### Paso 5: agregar un método auxiliar IsAuthenticated() a las rutas

```Javascript
// Simple route middleware to ensure user is authenticated.
//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.

var ensureAuthenticated = function(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }
  res.redirect('/login');
};

```

### Paso 6: agregar un mecanismo de almacenamiento en caché para las cookies

```Javascript
// Passport session setup.
//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});
```
### Paso 7: por último, proteger algunos extremos

Protege extremos mediante la especificación de la llamada passport.authenticate() con el protocolo que desea usar.

Editemos nuestra ruta en nuestro código de servidor para hacer algo más interesante:

```Javascript
server.get('/tasks', passport.authenticate('provider', { session: false }), listTasks);
```


## Paso 19: ejecutar de nuevo su aplicación de servidor y asegurarse de que le rechaza

Usemos `curl` nuevamente para ver si ahora tenemos protección de OAuth2 contra nuestros extremos. Haremos esto antes de ejecutar cualquiera de nuestros SDK de cliente en este extremo. Los encabezados devueltos deben bastar para indicarnos si estamos en la ruta de acceso correcta.

En primer lugar, asegúrese de que se ejecute la instancia de MongoDB...

	$sudo mongod

A continuación, cambie al directorio y empiece el curvado...

	$ cd azuread
	$ node server.js

Pruebe una operación GET básica:

	$ curl -isS http://127.0.0.1:8888/tasks/
	HTTP/1.1 302 Moved Temporarily
	Connection: close
	Location: https://login.windows.net/468a75f4-f9a7-4dc4-a527-4f4522734790/oauth2/authorize?response_type=code&redirect_uri=&client_id=123
	Content-Length: 0
	Date: Tue, 04 Feb 2014 02:15:14 GMT


302 es la respuesta que busca aquí, ya que indica que la capa Passport intenta realizar la redirección al extremo Authorize, que es exactamente lo que desea.

## ¡Enhorabuena! Tiene un servicio API REST mediante OAuth2.

Llegue tan lejos como pueda con este servidor sin usar un cliente compatible con OAuth2. Tendrá que seguir un tutorial adicional.

Si solo buscaba información sobre cómo implementar una API de REST mediante Restify y OAuth2, tendrá código de sobra para seguir desarrollando su servicio y aprender a crear en este ejemplo.

Si le interesan los próximos pasos en su recorrido por ADAL, a continuación hay algunos clientes ADAL compatibles que le recomendamos para que siga trabajando:

Solo tiene que clonar su equipo del desarrollador y configurarlo como se indica en el tutorial.

[ADAL para iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL para Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[ADAL para .Net](http://msdn.microsoft.com/library/windowsazure/jj573266.aspx)
 

<!---HONumber=58_postMigration-->