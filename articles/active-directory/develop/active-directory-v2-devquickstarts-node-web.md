---
title: "Inicio de sesión en aplicaciones web de Node.js de Azure Active Directory v2.0 | Microsoft Docs"
description: "Aprenda a crear una aplicación web Node.js en la que un usuario pueda iniciar sesión tanto con una cuenta personal como con una cuenta profesional o educativa de Microsoft."
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mtillman
editor: 
ms.assetid: 1b889e72-f5c3-464a-af57-79abf5e2e147
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 230d8ad16dc62564f3c1149443dd59fbb9974db5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="add-sign-in-to-a-nodejs-web-app"></a>Agregar inicio de sesión a una aplicación web Node.js

> [!NOTE]
> No todos los escenarios y las características de Azure Active Directory son compatibles con el punto de conexión v2.0. Para determinar si debe usar el punto de conexión v2.0 o el punto de conexión v1.0, obtenga información sobre las [limitaciones de la versión 2.0](active-directory-v2-limitations.md).
> 

En este tutorial, se usa Passport para realizar las tareas siguientes:

* En una aplicación web, el usuario inicia sesión mediante Azure Active Directory (Azure AD) y el punto de conexión v2.0.
* Mostrar información sobre el usuario.
* Cerrar la sesión del usuario en la aplicación.

**Passport** es middleware de autenticación para Node.js. Passport, flexible y modular, se puede usar discretamente en cualquier aplicación web de Restify o basada en Express. En esta herramienta, un conjunto completo de estrategias admite la autenticación mediante nombre de usuario y contraseña, Facebook, Twitter y mucho más. Hemos desarrollado una estrategia para Azure AD. En este artículo se muestra cómo instalar el módulo y luego agregar el complemento `passport-azure-ad` de Azure AD.

## <a name="download"></a>Descargar
El código de este tutorial se conserva [en GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs). A efectos de seguimiento del tutorial, puede [descargar el esqueleto de la aplicación como archivo .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) o clonar el esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

También puede obtener la aplicación completa al final de este tutorial.

## <a name="1-register-an-app"></a>1: Registro de una aplicación
Cree una aplicación en [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) o siga estos [pasos detallados](active-directory-v2-app-registration.md) para registrar una aplicación. Asegúrese de lo siguiente:

* Copie el **identificador de aplicación** asignado a la aplicación. Lo necesitará en este tutorial.
* Agregar la plataforma **web** para su aplicación.
* Copie el **URI de redireccionamiento** del portal. Debe usar el valor del URI predeterminado de `urn:ietf:wg:oauth:2.0:oob`.

## <a name="2-add-prerequisities-to-your-directory"></a>2: Incorporación de requisitos previos al directorio
En un símbolo del sistema, cambie de directorio para ir a la carpeta raíz, en caso de que no se encuentra ya en ella. Ejecute los comandos siguientes:

* `npm install express`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install restify`
* `npm install mongoose`
* `npm install bunyan`
* `npm install assert-plus`
* `npm install passport`
* `npm install webfinger`
* `npm install body-parser`
* `npm install express-session`
* `npm install cookie-parser`

Además, usamos `passport-azure-ad` en el esqueleto del inicio rápido:

* `npm install passport-azure-ad`

Esto instala las bibliotecas que usa `passport-azure-ad`.

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3: Configuración de la aplicación para que use la estrategia passport-node-js
Configure el middleware Express para que use el protocolo de autenticación OpenID Connect. Use Passport para emitir solicitudes de inicio y cierre de sesión, administrar la sesión del usuario y obtener información sobre el usuario, entre otras cosas.

1.  En la raíz del proyecto, abra el archivo Config.js. En la sección `exports.creds`, escriba los valores de configuración de la aplicación.
  
  * `clientID`: **identificador de aplicación** asignado a su aplicación en Azure Portal.
  * `returnURL`: **URI de redirección** que escribió en el portal.
  * `clientSecret`: secreto generado en el portal.

2.  En la raíz del proyecto, abra el archivo App.js. Para invocar la estrategia OIDCStrategy, incluida con `passport-azure-ad`, agregue la llamada siguiente:

  ```JavaScript
  var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

  // Add some logging
  var log = bunyan.createLogger({
      name: 'Microsoft OIDC Example Web Application'
  });
  ```

3.  Para tratar las solicitudes de inicio de sesión, use la estrategia a la que acaba de hacer referencia:

  ```JavaScript
  // Use the OIDCStrategy within Passport (section 2)
  //
  //   Strategies in Passport require a `validate` function. The function accepts
  //   credentials (in this case, an OpenID identifier), and invokes a callback
  //   with a user object.
  passport.use( new OIDCStrategy({
      callbackURL: config.creds.returnURL,
      realm: config.creds.realm,
      clientID: config.creds.clientID,
      clientSecret: config.creds.clientSecret,
      oidcIssuer: config.creds.issuer,
      identityMetadata: config.creds.identityMetadata,
      responseType: config.creds.responseType,
      responseMode: config.creds.responseMode,
      skipUserProfile: config.creds.skipUserProfile
      scope: config.creds.scope
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
      log.info('Example: Email address we received was: ', profile.email);
      // Asynchronous verification, for effect...
      process.nextTick(function () {
        findByEmail(profile.email, function(err, user) {
          if (err) {
            return done(err);
          }
          if (!user) {
            // "Auto-registration"
            users.push(profile);
            return done(null, profile);
          }
          return done(null, user);
        });
      });
    }
  ));
  ```

Passport usa un patrón similar para todas sus estrategias (Twitter, Facebook, etc.). Todos los escritores de estrategias se ajustan a este patrón. Pase a la estrategia una función `function()` que use un token y `done` como parámetros. La estrategia se devuelve cuando hace todo su trabajo. Almacene el usuario y guarde provisionalmente el token para no tener que volver a solicitarlo.

  > [!IMPORTANT]
  > El código anterior adopta cualquier usuario que pueda autenticarse en el servidor. Esto se conoce como registro automático. En un servidor de producción, no debe permitir que ningún usuario acceda sin pasar antes por el proceso de registro que se elija. Este suele ser el patrón que se ven en las aplicaciones para consumidores. Puede que la aplicación le permita registrarse con Facebook, pero luego le pedirá que escriba información adicional. Si no usa un programa de línea de comandos para este tutorial, podría extraer el correo electrónico del objeto de token que se devuelve. Luego, puede pedir al usuario que escriba información adicional. Puesto que se trata de un servidor de prueba, el usuario se agrega directamente a la base de datos en memoria.
  > 
  > 

4.  Agregue los métodos que emplee para realizar un seguimiento de los usuarios que han iniciado sesión, tal y como requiere Passport. Esta operación incluye la serialización y deserialización de la información del usuario:

  ```JavaScript

  // Passport session setup (section 2)

  //   To support persistent login sessions, Passport needs to be able to
  //   serialize users into, and deserialize users out of, the session. Typically,
  //   this is as simple as storing the user ID when serializing, and finding
  //   the user by ID when deserializing.
  passport.serializeUser(function(user, done) {
    done(null, user.email);
  });

  passport.deserializeUser(function(id, done) {
    findByEmail(id, function (err, user) {
      done(err, user);
    });
  });

  // Array to hold signed-in users
  var users = [];

  var findByEmail = function(email, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
      var user = users[i];
      log.info('we are using user: ', user);
      if (user.email === email) {
        return fn(null, user);
      }
    }
    return fn(null, null);
  };
  ```

5.  Agregue el código que carga el motor de Express. Aquí se usa el patrón de /views y /routes predeterminado que proporciona Express:

  ```JavaScript

  // Set up Express (section 2)

  var app = express();

  app.configure(function() {
    app.set('views', __dirname + '/views');
    app.set('view engine', 'ejs');
    app.use(express.logger());
    app.use(express.methodOverride());
    app.use(cookieParser());
    app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
    app.use(bodyParser.urlencoded({ extended : true }));
    // Initialize Passport!  Also use passport.session() middleware, to support
    // persistent login sessions (recommended).
    app.use(passport.initialize());
    app.use(passport.session());
    app.use(app.router);
    app.use(express.static(__dirname + '/../../public'));
  });

  ```

6.  Agregue las rutas POST que entregan las solicitudes de inicio de sesión reales al motor de `passport-azure-ad`:

  ```JavaScript

  // Auth routes (section 3)

  // GET /auth/openid
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. The first step in OpenID authentication involves redirecting
  //   the user to the user's OpenID provider. After authenticating, the OpenID
  //   provider redirects the user back to this application at
  //   /auth/openid/return.

  app.get('/auth/openid',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Authentication was called in the sample');
      res.redirect('/');
    });

  // GET /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called.
  //   In this example, it redirects the user to the home page.
  app.get('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });

  // POST /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called. 
  //   In this example, it redirects the user to the home page.

  app.post('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });
  ```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4: Uso de Passport para emitir solicitudes de inicio y cierre de sesión en Azure AD
Ahora, la aplicación está configurada para comunicarse con el punto de conexión v2.0 mediante el protocolo de autenticación OpenID Connect. La estrategia `passport-azure-ad` se ocupa de todos los detalles de la creación de mensajes de autenticación, validación de tokens de Azure AD y mantenimiento de la sesión de usuario. Ya solo falta dar a los usuarios una forma de iniciar y cerrar sesión, y recopilar más información sobre el usuario que ha iniciado sesión.

1.  Agregue los métodos **default**, **login**, **account** y **logout** al archivo App.js:

  ```JavaScript

  //Routes (section 4)

  app.get('/', function(req, res){
    res.render('index', { user: req.user });
  });

  app.get('/account', ensureAuthenticated, function(req, res){
    res.render('account', { user: req.user });
  });

  app.get('/login',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Login was called in the sample');
      res.redirect('/');
  });

  app.get('/logout', function(req, res){
    req.logout();
    res.redirect('/');
  });

  ```

  Estos son los detalles:
    
    * La ruta `/` redirige a la vista de index.ejs. Pasa el usuario en la solicitud (si existe).
    * La ruta `/account` primero *se asegura de que se ha autenticado* (eso se implementa en el código siguiente). Luego, pasa el usuario en la solicitud. Esto es así para que pueda obtener más información sobre el usuario.
    * La ruta `/login` llama al autenticador `azuread-openidconnect` desde `passport-azuread`. Si el resultado no es satisfactorio, vuelve a redireccionar el usuario a `/login`.
    * La ruta `/logout` llama a la vista (y la ruta) de logout.ejs. Así se borran las cookies y se devuelve el usuario a index.ejs.

2.  Agregue el método **EnsureAuthenticated** que usó antes en `/account`:

  ```JavaScript

  // Route middleware to ensure the user is authenticated (section 4)

  //   Use this route middleware on any resource that needs to be protected. If
  //   the request is authenticated (typically via a persistent login session),
  //   the request proceeds. Otherwise, the user is redirected to the
  //   sign-in page.
  function ensureAuthenticated(req, res, next) {
    if (req.isAuthenticated()) { return next(); }
    res.redirect('/login')
  }

  ```

3.  En App.js, cree el servidor:

  ```JavaScript

  app.listen(3000);

  ```


## <a name="5-create-the-views-and-routes-in-express-that-you-show-your-user-on-the-website"></a>5: Creación de las vistas y rutas de Express que se muestran al usuario en el sitio web
Agregue las rutas y vistas que muestran información al usuario. Las rutas y vistas controlan también las rutas `/logout` y `/login` que ha creado.

1. En el directorio raíz, cree la ruta `/routes/index.js`.

  ```JavaScript

  /*
  * GET home page.
  */

  exports.index = function(req, res){
    res.render('index', { title: 'Express' });
  };
  ```

2.  En el directorio raíz, cree la ruta `/routes/user.js`.

  ```JavaScript

  /*
  * GET users listing.
  */

  exports.list = function(req, res){
    res.send("respond with a resource");
  };
  ```

  `/routes/index.js` y `/routes/user.js` son rutas sencillas que pasan la solicitud a las vistas, incluido el usuario, si está presente.

3.  En el directorio raíz, cree la vista `/views/index.ejs`. Esta página llama a los métodos **login** y **logout**. Use también la vista `/views/index.ejs` para capturar información de la cuenta. Puede usar el atributo condicional `if (!user)` como el usuario que se pasa por la solicitud. Se evidencia que hay un usuario con sesión iniciada.

  ```JavaScript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
      <h2>Hello, <%= user.displayName %>.</h2>
      <a href="/account">Account info</a></br>
      <a href="/logout">Sign out</a>
  <% } %>
  ```

4.  En el directorio raíz, cree la vista `/views/account.ejs`. La vista `/views/account.ejs` le permite ver información adicional que `passport-azuread` coloca en la solicitud del usuario.

  ```Javascript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
  <p>displayName: <%= user.displayName %></p>
  <p>givenName: <%= user.name.givenName %></p>
  <p>familyName: <%= user.name.familyName %></p>
  <p>UPN: <%= user._json.upn %></p>
  <p>Profile ID: <%= user.id %></p>
  <p>Full Claimes</p>
  <%- JSON.stringify(user) %>
  <p></p>
  <a href="/logout">Sign out</a>
  <% } %>
  ```

5.  Agregue un diseño. En el directorio raíz, cree la vista `/views/layout.ejs`.

  ```HTML

  <!DOCTYPE html>
  <html>
      <head>
          <title>Passport-OpenID Example</title>
      </head>
      <body>
          <% if (!user) { %>
              <p>
              <a href="/">Home</a> |
              <a href="/login">Sign in</a>
              </p>
          <% } else { %>
              <p>
              <a href="/">Home</a> |
              <a href="/account">Account</a> |
              <a href="/logout">Sign out</a>
              </p>
          <% } %>
          <%- body %>
      </body>
  </html>
  ```

6.  Para compilar y ejecutar la aplicación, ejecute `node app.js`. Luego, vaya a `http://localhost:3000`.

7.  Inicie sesión con una cuenta personal, profesional o educativa de Microsoft. Observe que la identidad del usuario se refleja en la lista /account. 

Ahora tiene una aplicación web que está protegida mediante protocolos estándar del sector. Puede autenticar a los usuarios de la aplicación con sus cuentas personales y profesionales o educativas.

## <a name="next-steps"></a>Pasos siguientes
Como referencia, se ofrece el ejemplo completado (sin sus valores de configuración) [en forma de archivo .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip). También puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

A continuación, puede pasar a temas más avanzados. Podría interesarle:

[Proteger una API web de Node.js con el punto de conexión v2.0](active-directory-v2-devquickstarts-node-api.md)

Estos son algunos recursos adicionales:

* [Guía del desarrollador de Azure AD v2.0](active-directory-appmodel-v2-overview.md)
* [Etiqueta "azure-active-directory" de Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Obtención de actualizaciones de seguridad para nuestros productos
Le recomendamos suscribirse para recibir una notificación cuando se produzcan incidentes de seguridad. En la página [Notificaciones técnicas de seguridad de Microsoft](https://technet.microsoft.com/security/dd252948), suscríbase a las alertas de documentos informativos de seguridad.

