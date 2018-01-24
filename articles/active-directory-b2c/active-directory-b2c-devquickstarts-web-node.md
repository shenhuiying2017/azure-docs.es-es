---
title: "Integración del inicio de sesión a una aplicación web de Node.js: Azure Active Directory B2C"
description: "Creación de una aplicación web de Node.js que inicia la sesión de los usuarios con Azure Active Directory B2C."
services: active-directory-b2c
author: PatAltimore
manager: mtillman
editor: dstrockis
ms.custom: seo
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 03/10/2017
ms.author: xerners
ms.openlocfilehash: b4a5db7e6769d7ebb0bcf0287b3a1bfb7932984a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="azure-ad-b2c-add-sign-in-to-a-nodejs-web-app"></a>Azure AD B2C: Agregar inicio de sesión a una aplicación web de Node.js

**Passport** es middleware de autenticación para Node.js. Passport es flexible y modular, y se puede instalar discretamente en cualquier aplicación web basada en Express o Restify. Un conjunto completo de estrategias admite la autenticación mediante un nombre de usuario y contraseña, Facebook, Twitter, etc.

Para Azure Active Directory (Azure AD), se instala este módulo y, después, se agrega el complemento `passport-azure-ad` de Azure AD.

Necesita:

1. Registrar una aplicación mediante Azure AD.
2. Configurar la aplicación para que use el complemento `passport-azure-ad`.
3. Usar Passport para emitir solicitudes de inicio y cierre de sesión en Azure AD.
4. Imprimir los datos de usuario.

El código de este tutorial [se mantiene en GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS). Para continuar, puede [descargar el esqueleto de la aplicación en forma de archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip). También puede clonar el esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

La aplicación completa se ofrece al final de este tutorial.

## <a name="get-an-azure-ad-b2c-directory"></a>Obtener un directorio de Azure AD B2C

Para poder usar Azure AD B2C, debe crear un directorio o inquilino.  Un directorio es un contenedor para todos los usuarios, las aplicaciones, los grupos, etc. Si aún no tiene uno, [cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar con esta guía.

## <a name="create-an-application"></a>Creación de una aplicación

A continuación, debe crear una aplicación en su directorio B2C. Esto proporciona a Azure AD la información que necesita para comunicarse de forma segura con la aplicación. Tanto la aplicación cliente como la API web se representarán mediante un único **identificador de aplicación**, ya que conforman una aplicación lógica. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

- Incluir una **aplicación web**/**API web** en la aplicación.
- Escribir `http://localhost:3000/auth/openid/return` como **Dirección URL de respuesta**. Es la dirección URL predeterminada para este ejemplo de código.
- Crear un **secreto de aplicación** para la aplicación y copiarlo. Lo necesitará más adelante. Tenga en cuenta que para poder usar este valor, es preciso [incluirlo entre secuencias de escape XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape).
- Copiar el **identificador de aplicación** asignado a la aplicación. También lo necesitará más adelante.

## <a name="create-your-policies"></a>Crear sus directivas

En Azure AD B2C, cada experiencia del usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). Esta aplicación contiene tres experiencias de identidad: registro, inicio de sesión e inicio de sesión mediante Facebook. Es necesario crear una directiva así de cada tipo, como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Cuando cree las tres directivas, asegúrese de:

- Elegir el **nombre para mostrar** y los restantes atributos de registro en la directiva de registro.
- Elija las notificaciones de aplicación de **nombre para mostrar** e **id. de objeto** de cada directiva. Puede elegir también otras notificaciones.
- Copiar el **nombre** de cada directiva después de crearla. Debe tener el prefijo `b2c_1_`.  Necesitará estos nombres de directiva más adelante.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Después de crear las tres directivas, está listo para compilar la aplicación.

Tenga en cuenta que este artículo no trata de cómo usar las directivas que acaba de crear. Para obtener información acerca del funcionamiento de las directivas en Azure AD B2C, comience por el [tutorial de introducción a las aplicaciones web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="add-prerequisites-to-your-directory"></a>Incorporación de requisitos previos a un directorio

Desde la línea de comandos, cambie los directorios a la carpeta raíz, en caso de que no se encuentren ya en ella. Ejecute los comandos siguientes:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

Además, hemos usado `passport-azure-ad` para la versión preliminar en el esqueleto de la Guía de inicio rápido.

- `npm install passport-azure-ad`

Así se instalarán las bibliotecas de las que depende `passport-azure-ad`.

## <a name="set-up-your-app-to-use-the-passport-nodejs-strategy"></a>Configuración de una aplicación para que use la estrategia Passport-Node.js
Configure el middleware Express para que use el protocolo de autenticación OpenID Connect. Passport se usará para emitir solicitudes de inicio y cierre de sesión, administrar sesiones de usuario y obtener información sobre los usuarios, entre otras acciones.

Abra el archivo `config.js` en la raíz del proyecto y escriba los valores de configuración de la aplicación en la sección `exports.creds`.
- `clientID`: el **identificador de aplicación** asignado a la aplicación en el portal de registro.
- `returnURL`: el **URI de redirección** que especificó en el portal.
- `tenantName`: el nombre de inquilino de la aplicación, por ejemplo, **contoso.onmicrosoft.com**.

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Abra el archivo `app.js` en la raíz del proyecto. Agregue la siguiente llamada para invocar a la estrategia `OIDCStrategy` que viene con `passport-azure-ad`.


```javascript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

Use la estrategia a la que acaba de hacer referencia para tratar las solicitudes de inicio de sesión.

```javascript
// Use the OIDCStrategy in Passport (Section 2).
//
//   Strategies in Passport require a "validate" function that accepts
//   credentials (in this case, an OpenID identifier), and invokes a callback
//   by using a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
    // asynchronous verification, for effect...
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
Passport utiliza un patrón similar para todas sus estrategias (incluidos Twitter y Facebook). Todos los escritores de estrategias se ajustan a este patrón. Al examinar la estrategia, puede ver que se pasan `function()`, que tiene un token, y `done` como parámetros. La estrategia regresará después de haber realizado todo el trabajo. Almacene el usuario y guarde provisionalmente el token para no tener que volver a solicitarlo.

> [!IMPORTANT]
El código anterior toma todos los usuarios a los autentica el servidor. Esto se conoce como registro automático. Si usa servidores de producción, no desea los usuarios accedan a ellos, a menos que hayan pasado por un proceso de registro que ha configurado. Este patrón se puede ver a menudo en las aplicaciones de consumidor, que le permiten registrarse mediante Facebook, pero luego le pedirán que rellene la información adicional. Si nuestra aplicación no fuera un ejemplo, podríamos extraer una dirección de correo electrónico del objeto de token que se devuelve y luego pedir al usuario que rellene la información adicional. Al tratarse de un servidor de prueba, simplemente agregamos los usuarios a la base de datos en memoria.

Agregue los métodos que le permitan realizar un seguimiento de los usuarios que han iniciado sesión, tal como requiera Passport. Esta operación incluye la serialización y deserialización de la información del usuario:

```javascript

// Passport session setup. (Section 2)

//   To support persistent sign-in sessions, Passport needs to be able to
//   serialize users into and deserialize users out of sessions. Typically,
//   this is as simple as storing the user ID when Passport serializes a user
//   and finding the user by ID when Passport deserializes that user.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// Array to hold users who have signed in
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

Agregue el código para cargar el motor Express. A continuación puede ver que se utiliza el patrón `/views` y `/routes` predeterminado que proporciona Express.

```javascript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware to support
  // persistent sign-in sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

Agregue las rutas de `POST` que entregan las solicitudes de inicio de sesión reales al motor `passport-azure-ad`:

```javascript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request. The first step in OpenID authentication involves redirecting
//   the user to an OpenID provider. After the user is authenticated,
//   the OpenID provider redirects the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it redirects the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## <a name="use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Uso de Passport para emitir solicitudes de inicio y cierre de sesión en Azure AD

Ahora, la aplicación está correctamente configurada para comunicarse con el punto de conexión v2.0 mediante el protocolo de autenticación de OpenID Connect. `passport-azure-ad` se ha ocupado de los detalles de la creación de mensajes de autenticación, validación de tokens de Azure AD y mantenimiento de la sesión de usuario. Ya solo falta dar a los usuarios una forma de iniciar y cerrar sesión, y recopilar información adicional sobre los usuarios que han iniciado sesión.

En primer lugar, agregue el valor predeterminado, el inicio de sesión, la cuenta y los métodos de cierre de sesión al archivo `app.js`:

```javascript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

Para revisar estos métodos detalladamente:
- La ruta `/` se redirige a la vista `index.ejs` pasando el usuario en la solicitud (si existe).
- La ruta `/account` comprueba en primer lugar su autenticación (la implementación de este procedimiento se realiza a continuación). Luego pasa el usuario en la solicitud para que pueda obtener más información sobre el usuario.
- La ruta `/login` llama al autenticador `azuread-openidconnect` desde `passport-azure-ad`. Si el resultado de la operación no es satisfactorio, la ruta redirecciona al usuario a `/login`.
- `/logout` simplemente llama a `logout.ejs` (y a su ruta). Así se borran las cookies y se devuelve al usuario a `index.ejs`.


Para la última parte de `app.js`, agregue el método `EnsureAuthenticated` que se utiliza en la ruta `/account`.

```javascript

// Simple route middleware to ensure that the user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected. If
//   the request is authenticated (typically via a persistent sign-in session),
//   then the request will proceed. Otherwise, the user will be redirected to the
//   sign-in page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

Por último, cree el propio servidor en `app.js`.

```javascript

app.listen(3000);

```


## <a name="create-the-views-and-routes-in-express-to-call-your-policies"></a>Creación de las vistas y las rutas en Express para llamar a sus directivas

`app.js` ya está completa. Solo es preciso agregar las rutas y las vistas que permitan llamar a las directivas de inicio de sesión y registro. También controlan las rutas `/logout` y `/login` que creó.

Cree la ruta `/routes/index.js` en el directorio raíz.

```javascript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

Cree la ruta `/routes/user.js` en el directorio raíz.

```javascript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Estas rutas simples pasan las solicitudes a las vistas. Incluyen el usuario, si hay alguno.

Cree la vista `/views/index.ejs` en el directorio raíz. Se trata de una página simple que llama a las directivas de inicio y cierre de sesión. También puede utilizarla para obtener información de la cuenta. Tenga en cuenta que puede utilizar el operador condicional `if (!user)` cuando el usuario se pasa a través de la solicitud para proporcionar evidencia de que el usuario ha iniciado sesión.

```javascript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login/?p=your facebook policy">Sign in with Facebook</a>
    <a href="/login/?p=your email sign-in policy">Sign in with email</a>
    <a href="/login/?p=your email sign-up policy">Sign up with email</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account info</a></br>
    <a href="/logout">Log out</a>
<% } %>
```

Cree la vista `/views/account.ejs` en el directorio raíz, con el fin de que pueda ver información adicional que `passport-azure-ad` incluyó en la solicitud del usuario.

```javascript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login">Sign in</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claims</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

Ya puede compilar y ejecutar la aplicación.

Ejecute `node app.js` y vaya a `http://localhost:3000`.


Regístrese o inicie sesión en la aplicación mediante el correo electrónico o Facebook. Cierre la sesión y vuelva a iniciarla con otro usuario.

##<a name="next-steps"></a>pasos siguientes

Como referencia, se proporciona el ejemplo completado (sin sus valores de configuración) [en forma de archivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip). También puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

Ahora puede pasar a temas más avanzados. Puede probar:

[Proteger de una API web mediante el modelo B2C en Node.js](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move on to more advanced B2C topics. You might try:

[Call a Node.js web API from a Node.js web app]()

[Customizing the your B2C App's UX >>]()

-->
