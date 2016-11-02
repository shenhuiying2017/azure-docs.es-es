<properties
    pageTitle="Uso del SDK de JavaScript para Aplicaciones móviles de Azure"
    description="Uso de v para Aplicaciones móviles de Azure"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>


# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Uso de la biblioteca de cliente de JavaScript para Aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

En esta guía descubrirá cómo realizar tareas comunes usando el último [SDK de JavaScript para Aplicaciones móviles de Azure]. Si no está familiarizado con Aplicaciones móviles de Azure, complete primero el [inicio rápido de Aplicaciones móviles de Azure] para crear un back-end y una tabla. En esta guía, nos centramos en usar el back-end móvil en aplicaciones web HTML/JavaScript.

## <a name="supported-platforms"></a>Plataformas compatibles

Limitamos la compatibilidad del explorador con la versión actual y con las más recientes de los principales exploradores: Google Chrome, Microsoft Edge, Microsoft Internet Explorer y Mozilla Firefox.  El SDK debería funcionar con cualquier explorador relativamente moderno.

El paquete se distribuye como un módulo de JavaScript universal, por lo que es compatible con formatos globales, AMD y CommonJS.

##<a name="<a-name="setup"></a>setup-and-prerequisites"></a><a name="Setup"></a>Configuración y requisitos previos

En esta guía se asume que ha creado un back-end con una tabla. En esta guía se asume que la tabla tiene el mismo esquema que las tablas de dichos tutoriales.

Se puede instalar el SDK de JavaScript para Aplicaciones móviles de Azure con el comando `npm` :

```
npm install azure-mobile-apps-client --save
```

Una vez instalado, la biblioteca se encuentra en `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.min.js`.  Copie este archivo en su área web.

```
<script src="path/to/MobileServices.Web.min.js"></script>
```

La biblioteca también puede utilizarse como un módulo ES2015, en entornos de CommonJS, como Browserify y Webpack, y como una biblioteca AMD.  Por ejemplo:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="<a-name="auth"></a>how-to:-authenticate-users"></a><a name="auth"></a>Autenticación de usuarios

Azure App Service es compatible con la autenticación y autorización de los usuarios de aplicaciones mediante diversos proveedores de identidades externos: Facebook, Google, Cuenta Microsoft y Twitter. Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar la identidad de usuarios autenticados para implementar reglas de autorización en scripts del servidor. Para obtener más información, consulte el tutorial [Introducción a la autenticación] .

Se admiten dos flujos de autenticación: un flujo de servidor y un flujo de cliente.  El flujo de servidor ofrece la experiencia de autenticación más simple, ya que se basa en la interfaz de autenticación web del proveedor. El flujo de cliente permite una mayor integración con funcionalidades específicas del dispositivo, como el inicio de sesión único, ya que se basa en SDK específicos del proveedor.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="<a-name="configure-external-redirect-urls"></a>how-to:-configure-your-mobile-app-service-for-external-redirect-urls."></a><a name="configure-external-redirect-urls"></a>Cómo configurar su servicio de aplicaciones móviles para URL de redireccionamiento externas

Varios tipos de aplicaciones de JavaScript utilizan funcionalidades de bucle invertido para controlar los flujos de la interfaz de usuario de OAuth.  Estas son algunas de ellas:

* Ejecución del servicio de manera local
* Uso de la característica Live Reload con Ionic Framework
* Redirección a App Service para la autenticación 

La ejecución local puede ocasionar problemas porque, de forma predeterminada, la autenticación de App Service solo está configurada para permitir el acceso desde el back-end de la aplicación móvil. Realice los pasos siguientes para cambiar la configuración de App Service con el fin de permitir la autenticación desde el servidor de manera local:

1. Inicie sesión en el [Portal de Azure]
2. Vaya al back-end de la aplicación móvil.
3. Seleccione **Explorador de recursos** en el menú **HERRAMIENTAS DE DESARROLLO**.
4. Haga clic en **Ir** para abrir el Explorador de recursos del back-end de su aplicación móvil en una nueva pestaña o ventana.
5. Expanda el nodo **config** > **authsettings** de la aplicación.
6. Haga clic en el botón **Editar** para habilitar la edición del recurso.
7. Busque el elemento **allowedExternalRedirectUrls** , que debe tener el valor null. Agregue las direcciones URL en una matriz:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Reemplace las direcciones URL de la matriz por las de su servicio; en este ejemplo, la URL es `http://localhost:3000` para el servicio de ejemplo de Node.js local. También podría usar `http://localhost:4400` para el servicio Ripple o alguna otra dirección URL, según cómo esté configurada la aplicación.

8. En la parte superior de la página, haga clic en **Lectura/escritura** y, luego, haga clic en **PUT** para guardar las actualizaciones.

También tiene que agregar las mismas direcciones URL de bucle invertido a la configuración de lista blanca de CORS:

1. Vuelva a [Azure Portal].
2. Vaya al back-end de la aplicación móvil.
3. Haga clic en **CORS** en el menú **API**.
4. Escriba cada dirección URL en el cuadro de texto **Orígenes permitidos** vacío.  Se crea un nuevo cuadro de texto.
5. Haga clic en **GUARDAR**
    
Cuando el back-end se actualice, podrá usar las nuevas direcciones URL de bucle invertido en la aplicación.

<!-- URLs. -->
[inicio rápido de Aplicaciones móviles de Azure]: app-service-mobile-cordova-get-started.md
[Introducción a la autenticación]: app-service-mobile-cordova-get-started-users.md
[Incorporación de la autenticación a una aplicación]: app-service-mobile-cordova-get-started-users.md

[Portal de Azure]: https://portal.azure.com/
[SDK de JavaScript para Aplicaciones móviles de Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[Documentación del objeto Consulta]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx




<!--HONumber=Oct16_HO2-->


