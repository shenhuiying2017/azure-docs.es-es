<properties
	pageTitle="Uso del complemento de Apache Cordova para Aplicaciones móviles de Azure"
	description="Uso del complemento de Apache Cordova para Aplicaciones móviles de Azure"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="adrianha"/>

# Uso de una biblioteca de cliente de Apache Cordova para Aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

En esta guía se muestran algunos escenarios comunes del uso del último [complemento de Apache Cordova para Aplicaciones móviles de Azure]. Si no está familiarizado con Aplicaciones móviles de Azure, realice primero el tutorial [Creación de una aplicación de Apache Cordova] para crear un back-end, crear una tabla y descargar un proyecto de Apache Cordova previamente compilado. En esta guía nos centramos en el complemento de Apache Cordova del lado cliente.

##<a name="Setup"></a>Configuración y requisitos previos

En esta guía se asume que ha creado un back-end con una tabla. En esta guía se asume que la tabla tiene el mismo esquema que las tablas de dichos tutoriales. En esta guía también se supone que ha agregado el complemento de Apache Cordova al código. Si no lo ha hecho, puede agregar el complemento Apache Cordova al proyecto en la línea de comandos:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Para más información sobre la creación de [su primera aplicación de Apache Cordova], consulte su documentación.

[AZURE.INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>Autenticación de usuarios

El Servicio de aplicaciones de Azure es compatible con la autenticación y la autorización de los usuarios de aplicaciones mediante diversos proveedores de identidades externos: Facebook, Google, Cuenta Microsoft y Twitter. Puede establecer permisos en tablas para restringir el acceso a operaciones específicas solo a usuarios autenticados. También puede usar la identidad de usuarios autenticados para implementar reglas de autorización en scripts del servidor. Para obtener más información, consulte el tutorial [Introducción a la autenticación].

Al utilizar la autenticación en una aplicación de Apache Cordova, los siguientes complementos de Cordova deben estar disponibles:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Se admiten dos flujos de autenticación: un flujo de servidor y un flujo de cliente. El flujo de servidor ofrece la experiencia de autenticación más simple, ya que se basa en la interfaz de autenticación web del proveedor. El flujo de cliente permite una mayor integración con capacidades específicas del dispositivo, como el inicio de sesión único, ya que se basa en SDK específicos del dispositivo y específicos del proveedor.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

##<a name="register-for-push"></a>Registro de notificaciones push

Instale [phonegap-plugin-push] para administrar las notificaciones push. Este complemento se puede agregar fácilmente mediante el comando `cordova plugin add` en la línea de comandos, o por medio del instalador de complementos Git dentro de Visual Studio. El siguiente código de la aplicación de Apache Cordova registrará el dispositivo para notificaciones push:

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
[Creación de una aplicación de Apache Cordova]: app-service-mobile-cordova-get-started.md
[Introducción a la autenticación]: app-service-mobile-cordova-get-started-users.md
[Incorporación de autenticación a la aplicación de Servicios móviles]: app-service-mobile-cordova-get-started-users.md

[complemento de Apache Cordova para Aplicaciones móviles de Azure]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[su primera aplicación de Apache Cordova]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[documentación de objetos de consulta]: https://msdn.microsoft.com/es-ES/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0309_2016-->
