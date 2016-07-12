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
	ms.date="06/29/2016"
	ms.author="adrianha;ricksal"/>

# Uso de la biblioteca de cliente de JavaScript para Aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

En esta guía descubrirá cómo realizar tareas comunes usando el último [SDK de JavaScript para Aplicaciones móviles de Azure]. Si no está familiarizado con Aplicaciones móviles de Azure, complete primero el [inicio rápido de Aplicaciones móviles de Azure] para crear un back-end y una tabla. En esta guía, nos centramos en usar el back-end móvil en aplicaciones web HTML/JavaScript.

##<a name="Setup"></a>Configuración y requisitos previos

En esta guía se asume que ha creado un back-end con una tabla. En esta guía se asume que la tabla tiene el mismo esquema que las tablas de dichos tutoriales.

Se puede instalar el SDK de JavaScript para Aplicaciones móviles de Azure con el comando `npm`:

```
npm install azure-mobile-apps-client --save
```

Una vez instalado, la biblioteca se encuentra en `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.min.js`. Copie este archivo en su área web.

```
<script src="path/to/MobileServices.Web.min.js"></script>
```

La biblioteca también puede utilizarse como un módulo ES2015, en entornos de CommonJS, como Browserify y Webpack, y como una biblioteca AMD. Por ejemplo:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>Autenticación de usuarios

El Servicio de aplicaciones de Azure es compatible con la autenticación y la autorización de los usuarios de aplicaciones mediante diversos proveedores de identidades externos: Facebook, Google, Cuenta Microsoft y Twitter. Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar la identidad de usuarios autenticados para implementar reglas de autorización en scripts del servidor. Para obtener más información, consulte el tutorial [Introducción a la autenticación].

Se admiten dos flujos de autenticación: un flujo de servidor y un flujo de cliente. El flujo de servidor ofrece la experiencia de autenticación más simple, ya que se basa en la interfaz de autenticación web del proveedor. El flujo de cliente permite una mayor integración con funcionalidades específicas del dispositivo, como el inicio de sesión único, ya que se basa en SDK específicos del proveedor.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Configuración del servicio Aplicaciones móviles para direcciones URL de redirección externa

Varios tipos de aplicaciones JavaScript usan una funcionalidad de bucle invertido para administrar los flujos de la interfaz de usuario de OAuth, por ejemplo, al ejecutar el servicio localmente, al usar la recarga activa en Ionic Framework o al redirigir la autenticación al Servicio de aplicaciones. Esto puede ocasionar problemas porque, de forma predeterminada, la autenticación del Servicio de aplicaciones solo está configurada para permitir el acceso desde el back-end de aplicaciones móviles.

Utilice los pasos siguientes para cambiar la configuración del Servicio de aplicaciones para permitir la autenticación desde el host local:

1. Inicie sesión en el [Portal de Azure], vaya a su back-end de aplicaciones móviles y haga clic en **Herramientas** > **Explorador de recursos** > **Ir** para abrir una nueva ventana del explorador de recursos para su back-end de aplicaciones móviles (sitio).

2. Expanda el nodo **config** de la aplicación, luego haga clic en **authsettings** > **Editar**, busque el elemento **allowedExternalRedirectUrls**, que debe ser nulo, y cámbielo por lo siguiente:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Reemplace las direcciones URL de la matriz por las de su servicio; en este ejemplo, la dirección URL es `http://localhost:3000` para el servicio de ejemplo de Node.js local. También podría usar `http://localhost:4400` para el servicio Ripple o alguna otra dirección URL, según cómo esté configurada su aplicación.
    
3. En la parte superior de la página, haga clic en **Read/Write** (Lectura y escritura) y haga clic en **PUT** para guardar las actualizaciones.

    Aún deberá agregar las mismas direcciones URL de bucle invertido a la configuración de lista blanca de CORS:

4. De nuevo en el [Portal de Azure] en el back-end de aplicaciones móviles, haga clic en **Toda la configuración** > **CORS**, agregue las direcciones URL de bucle invertido y luego haga clic en **Guardar**.

Cuando el back-end se actualice, podrá usar las nuevas direcciones URL de bucle invertido en la aplicación.

<!-- URLs. -->
[inicio rápido de Aplicaciones móviles de Azure]: app-service-mobile-cordova-get-started.md
[Introducción a la autenticación]: app-service-mobile-cordova-get-started-users.md
[Incorporación de autenticación a la aplicación de Servicios móviles]: app-service-mobile-cordova-get-started-users.md

[SDK de JavaScript para Aplicaciones móviles de Azure]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/es-ES/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0629_2016-->