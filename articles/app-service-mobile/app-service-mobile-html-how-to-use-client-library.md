<properties
	pageTitle="Uso del SDK de JavaScript para Aplicaciones móviles de Azure"
	description="Uso de v para Aplicaciones móviles de Azure"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="adrianha"/>

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

##<a name="register-for-push"></a>Registro de notificaciones push

Instale [phonegap-plugin-push] para administrar las notificaciones push. Este complemento se puede agregar fácilmente mediante el comando `cordova plugin add` en la línea de comandos o por medio del instalador de complementos Git dentro de Visual Studio. El siguiente código de la aplicación de Apache Cordova registrará el dispositivo para notificaciones push:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Use el SDK de Centros de notificaciones para enviar notificaciones push desde el servidor. Nunca debe enviar notificaciones push directamente desde los clientes ya que esta acción podría usarse para desencadenar un ataque por denegación de servicio contra Centros de notificaciones o el PNS.

<!-- URLs. -->
[inicio rápido de Aplicaciones móviles de Azure]: app-service-mobile-cordova-get-started.md
[Introducción a la autenticación]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova Plugin for Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[your first Apache Cordova app]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/es-ES/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0323_2016-->