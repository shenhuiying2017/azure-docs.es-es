---
title: "Introducción a la aplicación web de Node.js de Azure AD | Microsoft Docs"
description: "Aprenda a crear una aplicación web MVC Express de Node.js que se integre con Azure AD para el inicio de sesión."
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mtillman
editor: 
ms.assetid: 81deecec-dbe2-4e75-8bc0-cf3788645f99
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: a0079c1f4265e1c2564b85247cf5f5e0289799db
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="azure-ad-nodejs-web-app-getting-started"></a>Introducción a la aplicación web de Node.js de Azure AD
Aquí usamos Passport para:

* Iniciar la sesión del usuario en la aplicación con Azure Active Directory (Azure AD).
* Mostrar información sobre el usuario.
* Cerrar la sesión del usuario en la aplicación.

Passport es middleware de autenticación para Node.js. Flexible y modular, Passport puede pasar discretamente a cualquier aplicación web basada en Express o Restify. Un conjunto completo de estrategias admiten la autenticación que usa un nombre de usuario y una contraseña, Facebook, Twitter y mucho más. Hemos desarrollado una estrategia para Microsoft Azure Active Directory. Instalamos este módulo y luego agregaremos el complemento `passport-azure-ad` de Microsoft Azure Active Directory.

Para ello, realice estos pasos:

1. Registrar una aplicación.
2. Configurar la aplicación para que use la estrategia `passport-azure-ad`.
3. Usar Passport para emitir solicitudes de inicio y cierre de sesión en Azure AD.
4. Imprimir datos sobre el usuario.

El código de este tutorial se conserva [en GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS).  Para poder continuar, puede [descargar el esqueleto de la aplicación como un archivo .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) o clonar el esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

La aplicación completa se ofrece también al final de este tutorial.

## <a name="step-1-register-an-app"></a>Paso 1: Registro de una aplicación
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. En el menú que aparece en la parte superior de la página, seleccione su cuenta. En la lista **Directorio** lista, elija el inquilino de Active Directory donde desea registrar la aplicación.

3. Seleccione **Más servicios** en el menú de la izquierda de la pantalla y luego seleccione **Azure Active Directory**.

4. Seleccione **Registros de aplicaciones** y luego seleccione **Agregar**.

5. Siga las indicaciones para crear una **Aplicación web** y/o **API web**.
  * El **nombre** de la aplicación describe la aplicación a los usuarios.

  * La **dirección URL de inicio de sesión** es la dirección URL base de su aplicación.  Es el valor predeterminado del esquema `http://localhost:3000/auth/openid/return``.

6. Después de registrarse, Azure AD asigna a la aplicación un identificador de aplicación único. Necesita este valor en las secciones siguientes, así que cópielo de la página de la aplicación.
7. En la página **Configuración** -> **Propiedades** de la aplicación, actualice el URI del identificador de la aplicación. El **URI de id. de aplicación** es un identificador único de su aplicación. La convención es usar el formato `https://<tenant-domain>/<app-name>`, por ejemplo: `https://contoso.onmicrosoft.com/my-first-aad-app`.

## <a name="step-2-add-prerequisites-to-your-directory"></a>Paso 2: Incorporación de requisitos previos al directorio
1. En la línea de comandos, cambie los directorios a la carpeta raíz si aún no está ahí y luego ejecute los siguientes comandos:

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

2. Además, necesita `passport-azure-ad`:
    * `npm install passport-azure-ad`

Esto instala las bibliotecas de las que depende `passport-azure-ad`.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>Paso 3: Configuración de la aplicación para que use la estrategia passport-node-js
Aquí configuramos Express para usar el protocolo de autenticación OpenID Connect.  Passport se usa para hacer varias cosas, como emitir solicitudes de inicio y cierre de sesión, administrar la sesión del usuario y obtener información sobre el usuario.

1. Para comenzar, abra el archivo `config.js` en la raíz del proyecto y escriba los valores de configuración de la aplicación en la sección `exports.creds`.

  * `clientID` es el **identificador de aplicación** que se asigna a la aplicación en el portal de registro.

  * `returnURL` es el **identificador URI de redireccionamiento** que escribió en el portal.

  * `clientSecret` es el secreto generado en el portal.

2. Abra el archivo `app.js` en la raíz del proyecto. Agregue la siguiente llamada para invocar a la estrategia `OIDCStrategy` que viene con `passport-azure-ad`.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
        name: 'Microsoft OIDC Example Web Application'
    });
    ```

3. Después, use la estrategia a la que acabamos de hacer referencia para administrar las solicitudes de inicio de sesión.

    ```JavaScript
    // Use the OIDCStrategy within Passport. (Section 2)
    //
    //   Strategies in passport require a `validate` function that accepts
    //   credentials (in this case, an OpenID identifier), and invokes a callback
    //   with a user object.
    passport.use(new OIDCStrategy({
        callbackURL: config.creds.returnURL,
        realm: config.creds.realm,
        clientID: config.creds.clientID,
        clientSecret: config.creds.clientSecret,
        oidcIssuer: config.creds.issuer,
        identityMetadata: config.creds.identityMetadata,
        skipUserProfile: config.creds.skipUserProfile,
        responseType: config.creds.responseType,
        responseMode: config.creds.responseMode
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
        if (!profile.email) {
        return done(new Error("No email found"), null);
        }
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
Passport usa un patrón similar para todas sus estrategias (Twitter, Facebook, etc.) al que se ajustan todos los escritores de estrategias. Si se examina la estrategia, se percibe que se pasa una función que tiene un token y un elemento done como parámetros. La estrategia se devuelve después de que realiza su trabajo. Luego almacenaremos el usuario y guardaremos provisionalmente el token con el fin de que no tengamos que volver a pedirlo.

> [!IMPORTANT]
El código anterior toma cualquier usuario que se autentique en el servidor. Esto se conoce como registro automático. Le recomendamos que no permita que nadie se autentique en un servidor de producción sin registrarse primero mediante un proceso que decida. Este suele ser el patrón que se observa en las aplicaciones de consumidor que le permiten registrarse con Facebook, pero que luego piden que proporcione información adicional. Si esta no fuera una aplicación de ejemplo, podríamos haber extraído la dirección del correo electrónico del usuario del objeto de token que se devuelve y, después, haber pedido al usuario que rellenara la información adicional. Puesto que se trata de un servidor de prueba, los agregaremos a la base de datos en memoria.


4. A continuación, vamos a agregar los métodos que nos permitirán realizar un seguimiento de los usuarios con sesión iniciada, tal y como requiere Passport. Estos métodos incluyen la serialización y deserialización de la información del usuario.

    ```JavaScript

            // Passport session setup. (Section 2)

            //   To support persistent sign-in sessions, Passport needs to be able to
            //   serialize users into the session and deserialize them out of the session. Typically,
            //   this is done simply by storing the user ID when serializing and finding
            //   the user by ID when deserializing.
            passport.serializeUser(function(user, done) {
                done(null, user.email);
            });

            passport.deserializeUser(function(id, done) {
                findByEmail(id, function (err, user) {
                    done(err, user);
                });
            });

            // array to hold signed-in users
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

5.  Después agregaremos el código para cargar el motor de Express. Aquí se usa el patrón de /views y /routes predeterminado que proporciona Express.

    ```JavaScript

        // configure Express (section 2)

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

6. Por último, se agregan las rutas que entregan las solicitudes de inicio de sesión reales al motor `passport-azure-ad`:

    ```JavaScript

        // Our Auth routes (section 3)

        // GET /auth/openid
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. The first step in OpenID authentication involves redirecting
        //   the user to their OpenID provider. After authenticating, the OpenID
        //   provider redirects the user back to this application at
        //   /auth/openid/return.
        app.get('/auth/openid',
        passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
        function(req, res) {
            log.info('Authentication was called in the Sample');
            res.redirect('/');
        });

        // GET /auth/openid/return
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. If authentication fails, the user is redirected back to the
        //   sign-in page. Otherwise, the primary route function is called,
        //   which, in this example, redirects the user to the home page.
        app.get('/auth/openid/return',
          passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
          function(req, res) {
            log.info('We received a return from AzureAD.');
            res.redirect('/');
          });

        // POST /auth/openid/return
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. If authentication fails, the user is redirected back to the
        //   sign-in page. Otherwise, the primary route function is called,
        //   which, in this example, redirects the user to the home page.
        app.post('/auth/openid/return',
          passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
          function(req, res) {
            log.info('We received a return from AzureAD.');
            res.redirect('/');
          });
     ```


## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Paso 4: Uso de Passport para emitir solicitudes de inicio y cierre de sesión en Azure AD
Ahora, la aplicación está correctamente configurada para comunicarse con el punto de conexión mediante el protocolo de autenticación de OpenID Connect.  `passport-azure-ad` se ha ocupado de todos los detalles de la creación de mensajes de autenticación, validación de tokens de Azure AD y mantenimiento de las sesiones de usuario. Ya solo falta proporcionar a los usuarios una forma de iniciar y cerrar sesión, y recopilar información adicional sobre los usuarios que han iniciado sesión.

1. En primer lugar, agregue el valor predeterminado, el inicio de sesión, la cuenta y los métodos de cierre de sesión al archivo `app.js`:

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
            log.info('Login was called in the Sample');
            res.redirect('/');
        });

        app.get('/logout', function(req, res){
          req.logout();
          res.redirect('/');
        });

    ```

2.  Analicemos esto con detalle:

  * La ruta `/` realiza la redirección a la vista index.ejs pasando el usuario en la solicitud (si existe).
  * La ruta `/account` primero se *asegura de que estamos autenticados* (lo que se implementará en el ejemplo siguiente) y, seguidamente, pasa el usuario en la solicitud para que podamos obtener información adicional sobre él.
  * La ruta `/login` llama a nuestro autenticador azuread-openidconnect desde `passport-azuread`. Si el resultado de la operación no es satisfactorio, vuelve a redireccionar al usuario a /login.
  * La ruta `/logout` simplemente llama a logout.ejs (y a la ruta), que borra las cookies y devuelve el usuario a index.ejs.

3. Para la última parte de `app.js`, agregaremos el método **EnsureAuthenticated** que se usa en `/account`, como se muestra anteriormente.

    ```JavaScript

        // Simple route middleware to ensure user is authenticated. (section 4)

        //   Use this route middleware on any resource that needs to be protected. If
        //   the request is authenticated (typically via a persistent sign-in session),
        //   the request proceeds. Otherwise, the user is redirected to the
        //   sign-in page.
        function ensureAuthenticated(req, res, next) {
          if (req.isAuthenticated()) { return next(); }
          res.redirect('/login')
        }
    ```

4. Por último, crearemos el propio servidor en `app.js`:

```JavaScript

        app.listen(3000);

```


## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>Paso 5: Creación de las vistas y las rutas en Express para mostrar el usuario en el sitio web
Ahora `app.js` se habrá completado. Solo es preciso agregar las rutas y vistas que muestran la información obtenida al usuario, así como administrar las rutas `/logout` y `/login` creadas.

1. Cree la ruta `/routes/index.js` en el directorio raíz.

    ```JavaScript
                /*
                 * GET home page.
                 */

                exports.index = function(req, res){
                  res.render('index', { title: 'Express' });
                };
    ```

2. Cree la ruta `/routes/user.js` en el directorio raíz.

                ```JavaScript
                /*
                 * GET users listing.
                 */

                exports.list = function(req, res){
                  res.send("respond with a resource");
                };
                ```

 Estas pasan la solicitud a nuestras vistas, incluido el usuario, si está presente.

3. Cree la vista `/views/index.ejs` en el directorio raíz. Se trata de una página sencilla que llama a nuestros métodos de inicio y cierre de sesión y nos permite obtener información de la cuenta. Observe que podemos usar el condicional `if (!user)`, ya que el usuario pasado en la solicitud es una evidencia de que tenemos un usuario con la sesión iniciada.

    ```JavaScript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
        <h2>Hello, <%= user.displayName %>.</h2>
        <a href="/account">Account Info</a></br>
        <a href="/logout">Log Out</a>
    <% } %>
    ```

4. Cree la vista `/views/account.ejs` en el directorio raíz de modo que podamos ver la información adicional que `passport-azuread` ha incluido en la solicitud del usuario.

    ```Javascript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
    <p>displayName: <%= user.displayName %></p>
    <p>givenName: <%= user.name.givenName %></p>
    <p>familyName: <%= user.name.familyName %></p>
    <p>UPN: <%= user._json.upn %></p>
    <p>Profile ID: <%= user.id %></p>
  ##Next steps  <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

5. Vamos a mejorar la apariencia, para lo que agregaremos un diseño. Cree la vista '/views/layout.ejs' en el directorio raíz.

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
                <a href="/login">Log In</a>
                </p>
            <% } else { %>
                <p>
                <a href="/">Home</a> |
                <a href="/account">Account</a> |
                <a href="/logout">Log Out</a>
                </p>
            <% } %>
            <%- body %>
        </body>
    </html>
    ```

##<a name="next-steps"></a>pasos siguientes
Por último, compile y ejecute su aplicación. Ejecute `node app.js` y luego vaya a `http://localhost:3000`.

Inicie sesión con una cuenta de Microsoft personal o con una cuenta profesional o educativa, y observe cómo se refleja la identidad del usuario en la lista /account. Ahora dispone de una aplicación web que está protegida mediante protocolos estándar del sector que pueden autenticar a los usuarios tanto con sus cuentas personales como con sus cuentas profesionales o educativas.

Como referencia, se proporciona el ejemplo completado (sin sus valores de configuración) [en forma de archivo .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Como alternativa, puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Ahora puede pasar a temas más avanzados. Podría interesarle:

[Protección de una API web con Azure AD](active-directory-devquickstarts-webapi-nodejs.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
