---
title: "Adición de notificaciones push a una aplicación de Apache Cordova con Azure Mobile Apps | Microsoft Docs"
description: "Obtenga información acerca de cómo usar las aplicaciones móviles de Azure para enviar notificaciones push a su aplicación de Apache Cordova."
services: app-service\mobile
documentationcenter: javascript
manager: adrianha
editor: 
author: ysxu
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 47063276d7bb6bb3b3aac0cca4290dfbea5488f7
ms.openlocfilehash: 99b23de962f7ba338fcf3f9b2e96d58c3dcbe7bc
ms.lasthandoff: 12/01/2016


---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Agregar notificaciones push a su aplicación de Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Información general
En este tutorial, agregará notificaciones push al proyecto de [inicio rápido de Apache Cordova] para que cada vez que se envíe una notificación push al dispositivo, se inserte un registro.

Si no usa el proyecto de servidor de inicio rápido descargado, necesita el paquete de extensión de la notificación de inserción. Para más información, consulte [Trabajar con el SDK de servidor de back-end de .NET para Azure Mobile Apps][1].

## <a name="prerequisites"></a>Requisitos previos
Este tutorial describe el desarrollo de una aplicación de Apache Cordova con Visual Studio 2015 y su ejecución en el emulador de Google Android, un dispositivo con Android, un dispositivo con Windows y un dispositivo con iOS.

Para completar este tutorial, necesita:

* Un equipo con [Visual Studio Community 2015][2] o versiones posteriores.
* [Visual Studio Tools para Apache Cordova][4].
* Una [cuenta activa de Azure][3].
* Un proyecto de [inicio rápido de Apache Cordova][5] completado.
* (Android) Una [cuenta de Google][6] con una dirección de correo electrónico verificada.
* (iOS) La [pertenencia a un programa para desarrolladores de Apple][7] y un dispositivo iOS (el simulador de iOS no admite la inserción).
* (Windows) Una [cuenta de desarrollador para la Tienda Windows][8] y un dispositivo con Windows 10.

## <a name="configure-hub"></a>Configurar un Centro de notificaciones
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Ver un vídeo que muestra los pasos de esta sección][9]

## <a name="update-the-server-project"></a>Actualización del proyecto de servidor
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Modificación de la aplicación de Cordova
Asegúrese de que el proyecto de aplicación de Apache Cordova está listo para controlar las notificaciones push al instalar el complemento de inserción de Cordova además de cualquier servicio de inserción específico de la plataforma.

#### <a name="update-the-cordova-version-in-your-project"></a>Actualización de la versión de Cordova en el proyecto.
Si el proyecto usa una versión anterior a Apache Cordova v6.1.1, actualice el proyecto de cliente. Para actualizar el proyecto:

* Haga clic en `config.xml` para abrir el diseñador de configuración.
* Seleccione la pestaña Plataformas.
* Elija 6.1.1 en el cuadro de texto **Cordova CLI** (CLI de Cordova).
* Elija **Compilar** y luego **Compilar solución** para actualizar el proyecto.

#### <a name="install-the-push-plugin"></a>Instalación del complemento de inserción
Las aplicaciones de Apache Cordova no controlan el dispositivo ni las funcionalidades de red de forma nativa.  Estas funcionalidades las proporcionan los complementos que se publican en [npm][10] o en GitHub.  El complemento `phonegap-plugin-push` se usa para controlar las notificaciones de inserción de la red.

Puede instalar el complemento de inserción push de una de estas formas:

**Desde el símbolo del sistema:**

Ejecute el comando siguiente:

    cordova plugin add phonegap-plugin-push

**Desde Visual Studio:**

1. En el Explorador de soluciones, abra el archivo `config.xml`, haga clic en **Complementos** > **Personalizado**, seleccione **Git** como origen de instalación y escriba `https://github.com/phonegap/phonegap-plugin-push` como origen.

   ![][img1]

2. Haga clic en la flecha que hay junto al origen de la instalación.
3. En **SENDER_ID**, si ya tiene un identificador numérico del proyecto para el proyecto de la consola para desarrolladores de Google, puede agregarlo aquí. En caso contrario, escriba un valor de marcador de posición, como 777777.  Si su objetivo es Android, puede actualizar este valor en el archivo config.xml más adelante.
4. Haga clic en **Agregar**.

Ahora ya está instalado el complemento de inserción.

#### <a name="install-the-device-plugin"></a>Instalación del complemento del dispositivo
Siga el mismo procedimiento que usó para instalar el complemento de inserción.  Agregue el complemento de dispositivo de la lista de complementos principales (haga clic en **Complementos** > **Core** (Principal) para encontrarlo). Este complemento es necesario para obtener el nombre de la plataforma.

#### <a name="register-your-device-on-application-start-up"></a>Registro del dispositivo al inicio de la aplicación
Inicialmente, se incluirá el código mínimo para Android. Más tarde, puede modificar la aplicación para que se ejecute en iOS o en Windows 10.

1. Agregue una llamada a **registerForPushNotifications** durante la devolución de la llamada del proceso de inicio de sesión o en la parte inferior del método **onDeviceReady**:

        // Login to the service.
        client.login('google')
            .then(function () {
                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                    // Added to register for push notifications.
                registerForPushNotifications();

            }, handleError);

    Este ejemplo muestra la llamada a **registerForPushNotifications** después de que la autenticación se realiza correctamente.  Puede llamar a `registerForPushNotifications()` tantas veces como sea necesario.

2. Agregue el nuevo método **registerForPushNotifications** como se indica a continuación:

        // Register for Push Notifications. Requires that phonegap-plugin-push be installed.
        var pushRegistration = null;
        function registerForPushNotifications() {
          pushRegistration = PushNotification.init({
              android: { senderID: 'Your_Project_ID' },
              ios: { alert: 'true', badge: 'true', sound: 'true' },
              wns: {}
          });

        // Handle the registration event.
        pushRegistration.on('registration', function (data) {
          // Get the native platform of the device.
          var platform = device.platform;
          // Get the handle returned during registration.
          var handle = data.registrationId;
          // Set the device-specific message template.
          if (platform == 'android' || platform == 'Android') {
              // Register for GCM notifications.
              client.push.register('gcm', handle, {
                  mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'iOS') {
              // Register for notifications.
              client.push.register('apns', handle, {
                  mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'windows') {
              // Register for WNS notifications.
              client.push.register('wns', handle, {
                  myTemplate: {
                      body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                      headers: { 'X-WNS-Type': 'wns/toast' } }
              });
          }
        });

        pushRegistration.on('notification', function (data, d2) {
          alert('Push Received: ' + data.message);
        });

        pushRegistration.on('error', handleError);
        }
3. (Android) En el código anterior, reemplace `Your_Project_ID` por el identificador numérico del proyecto de la aplicación en la  [consola para desarrolladores de Google][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Opcional) Configuración y ejecución de la aplicación en Android
Complete esta sección para habilitar las notificaciones push en Android.

#### <a name="enable-gcm"></a>Habilitar la mensajería en la nube Firebase
Dado que, en principio, el objetivo es la plataforma Android de Google, es preciso habilitar el Servicio de mensajería en la nube de Firebase.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Configuración del back-end de aplicación móvil para enviar solicitudes push mediante FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Configuración de una aplicación Cordova para Android
En la aplicación de Cordova, abra el archivo config.xml y reemplace `Your_Project_ID` por el identificador numérico del proyecto de la aplicación en la [consola para desarrolladores de Google][18].

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

Abra index.js y actualice el código para usar el identificador numérico del proyecto.

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

#### <a name="configure-device"></a>Configuración de un dispositivo Android para la depuración USB
Antes de implementar su aplicación en su dispositivo Android, debe habilitar la depuración USB.  Realice los pasos siguientes en su teléfono Android:

1. Vaya a **Ajustes** > **Información del teléfono** y pulse **Número de compilación** hasta que se habilite el modo de desarrollador (unas 7 veces).
2. Nuevamente en **Ajustes** > **Developer Options** (Opciones del desarrollador), habilite **USB debugging** (Depuración USB) y conecte el teléfono Android al equipo de desarrollo con un cable USB.

Cuando lo probamos, usamos un dispositivo Google Nexus 5X con Android 6.0 (Marshmallow).  Sin embargo, las técnicas son comunes a cualquier versión moderna de Android.

#### <a name="install-google-play-services"></a>Instalación de Google Play Services
El complemento de inserción se basa en Google Play Services de Android para las notificaciones push.

1. En Visual Studio, haga clic en **Herramientas** > **Android** > **Android SDK Manager**, expanda la carpeta **Extras** y active la casilla para asegurarse de que se instalan los siguientes SDK.

   * Android 2.3 o superior
   * Revisión de Google Repository 27 o superior
   * Google Play Services 9.0.2 o superior

2. Haga clic en **Instalar paquetes** y espere a que finalice la instalación.

Las bibliotecas requeridas actuales se enumeran en la [documentación de instalación de phonegap-plugin-push][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Prueba de las notificaciones push de la aplicación en Android
Ahora puede probar las notificaciones push mediante la ejecución de la aplicación y la inserción de elementos en la tabla TodoItem. Puede hacerlo desde el mismo dispositivo o desde otro dispositivo, siempre que use el mismo back-end. Pruebe la aplicación Cordova en la plataforma Android de una de las siguientes maneras:

* **En un dispositivo físico:** conecte el dispositivo Android al equipo de desarrollo con un cable USB.  En lugar de **Emulador de Android de Google**, seleccione **Dispositivo**. Visual Studio implementa la aplicación en el dispositivo y luego la ejecuta.  Luego podrá interactuar con la aplicación en el dispositivo.

  Mejore su experiencia de desarrollo.  Las aplicaciones de pantalla compartida, como [Mobizen][ 20], pueden ayudarle a desarrollar una aplicación Android.  Mobizen proyecta la pantalla de Android en un explorador web de su equipo.

* **En un emulador de Android:** se requieren pasos de configuración adicionales cuando la ejecución se realiza en un emulador.

    Asegúrese de que va a realizar la implementación en un dispositivo virtual que tenga las API de Google establecidas como destino, como se muestra en el administrador de dispositivo virtual Android (AVD).

    ![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Si desea usar un emulador de x86 más rápido, [instale el controlador de HAXM][11] y configure el emulador para usarlo.

    Agregue una cuenta de Google al dispositivo Android haciendo clic en **Aplicaciones** > **Configuración** > **Agregar cuenta** y luego siga las indicaciones.

    ![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Ejecute la aplicación todolist como antes e inserte un nuevo elemento todo. Esta vez, se muestra un icono de notificación en el área de notificación. Puede abrir el cajón de notificaciones para ver el texto completo de la notificación.

    ![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(Optional) Configuración y ejecución en iOS
En esta sección se explica cómo ejecutar el proyecto Cordova en dispositivos de iOS. Puede omitir esta sección si no está trabajando con dispositivos iOS.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Instalación y ejecución del agente de compilación remoto de iOS en un servicio Mac o en la nube
Para poder ejecutar una aplicación de Cordova en iOS mediante Visual Studio, siga los pasos de la [guía de instalación de iOS][12] para instalar y ejecutar el agente de compilación remoto.

Asegúrese de que puede compilar la aplicación para iOS. Los pasos de la guía son necesarios para compilar para iOS desde Visual Studio. Si no tiene un equipo Mac, puede compilar para iOS mediante el agente de compilación remoto en un servicio como MacInCloud. Para más información, consulte la sección sobre la [ejecución de su aplicación iOS en la nube][21].

> [!NOTE]
> Se requiere XCode 7 o superior para usar el complemento de inserción de iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Búsqueda del identificador que se va a usar como id. de la aplicación
Antes de registrar la aplicación para las notificaciones de inserción, abra config.xml en su aplicación de Cordova, busque el valor del atributo `id` en el elemento de widget y cópielo para su uso posterior. En el siguiente XML, el identificador es `io.cordova.myapp7777777`.

        <widget defaultlocale="en-US" id="io.cordova.myapp7777777"
          version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Posteriormente, utilice este identificador al crear un id. de aplicación en el portal para desarrolladores de Apple. Si crea un identificador de aplicación diferente en el portal para desarrolladores, debe realizar algunos pasos adicionales más adelante en este tutorial. El identificador del elemento widget debe coincidir con el identificador de aplicación del portal para desarrolladores.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registro de la aplicación para notificaciones de inserción en el portal para desarrolladores de Apple
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Ver un vídeo donde se muestren pasos similares](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Configuración de Azure para enviar notificaciones push
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Comprobación de que el id. de aplicación coincide con la aplicación de Cordova
Si el id. de la aplicación que creó en la cuenta para desarrolladores de Apple ya coincide con el identificador del elemento widget de config.xml, puede omitir este paso. Sin embargo, si los identificadores no coinciden, siga estos pasos:

1. Elimine la carpeta platforms del proyecto.
2. Elimine la carpeta plugins del proyecto.
3. Elimine la carpeta node_modules del proyecto.
4. Actualice el atributo id del elemento widget en config.xml para que use el id. de aplicación que creó en la cuenta para desarrolladores de Apple.
5. Vuelva a compilar el proyecto.

##### <a name="test-push-notifications-in-your-ios-app"></a>Prueba de las notificaciones push en su aplicación de iOS
1. En Visual Studio, asegúrese de que **iOS** está seleccionado como destino de implementación y luego elija el **dispositivo**
    que se ejecuta en el dispositivo iOS conectado.

    La ejecución se puede realizar en un dispositivo conectado a un PC mediante iTunes. El simulador de iOS no admite notificaciones push.

2. Pulse el botón **Ejecutar** o presione **F5** en Visual Studio para compilar el proyecto e iniciar la aplicación en un dispositivo iOS. Luego, haga clic en **Aceptar** para aceptar las notificaciones push.

   > [!NOTE]
   > La aplicación solicita confirmación para las notificaciones push durante la primera ejecución.

3. En la aplicación, escriba una tarea y, a continuación, haga clic en el icono de signo de suma (+).
4. Compruebe que se ha recibido la notificación y, a continuación, haga clic en OK (Aceptar) para descartarla.

## <a name="optional-configure-and-run-on-windows"></a>(Optional) Configuración y ejecución en Windows
En esta sección se explica cómo ejecutar el proyecto de la aplicación de Apache Cordova en dispositivos con Windows 10 (el complemento push de PhoneGap es compatible con Windows 10). Puede omitir esta sección si no está trabajando con dispositivos Windows.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registro de la aplicación de Windows para notificaciones push con WNS
Para utilizar las opciones de Tienda en Visual Studio, seleccione un destino de Windows en la lista Plataformas de solución, como **Windows-x64** o **Windows-x86** (evite **Windows-AnyCPU** para las notificaciones de inserción).

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Visualización de un vídeo donde se muestren pasos similares][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Configuración del Centro de notificaciones para WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Configuración de una aplicación de Cordova para que admita notificaciones push de Windows
Abra el Diseñador de configuraciones (haga clic con el botón derecho en el archivo config.xml y seleccione **Diseñador de vistas**), seleccione la pestaña **Windows** y elija **Windows 10** en **Versión de Windows de destino**.

Para admitir las notificaciones push en sus compilaciones (depuración) predeterminadas (depurar), abra el archivo build.json. Copie la configuración de la "versión" en la configuración de depuración.

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

Tras la actualización, el archivo build.json debe contener el código siguiente:

    "windows": {
        "release": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            },
        "debug": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            }
        }

Compile la aplicación y compruebe que no hay errores. Ahora debe registrar la aplicación cliente para las notificaciones desde el back-end de aplicación móvil. Repita esta sección para cada proyecto de Windows de la solución.

#### <a name="test-push-notifications-in-your-windows-app"></a>Prueba de las notificaciones push en su aplicación de Windows
En Visual Studio, asegúrese de que hay una plataforma de Windows seleccionada como destino de implementación, como **Windows-x64** o **Windows-x86**. Para ejecutar la aplicación en un equipo con Windows 10 que hospede Visual Studio, elija **Equipo local**.

Presione el botón Ejecutar para compilar el proyecto e iniciar la aplicación.

En la aplicación, escriba un nombre para un elemento todoitem nuevo y haga clic en el icono del signo más (+) para agregarlo.

Compruebe que se recibe una notificación cuando se agrega el artículo.

## <a name="next-steps"></a>Pasos siguientes
* Lea acerca de [Notification Hubs][17] para obtener información sobre las notificaciones push.
* Si aún no lo ha hecho, siga el tutorial y [agregue autenticación][14] a su aplicación de Apache Cordova.

Obtenga información sobre cómo usar los SDK.

* [SDK de Apache Cordova][15]
* [SDK de servidor ASP.NET][1]
* [SDK de servidor Node.js][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: http://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: http://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: http://taco.visualstudio.com/en-us/docs/build_ios_cloud/

