---
title: "Introducción a Azure Mobile Engagement para Cordova/Phonegap"
description: "Aprenda a usar Azure Mobile Engagement con los análisis y las notificaciones push para aplicaciones de Cordova/Phonegap."
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 54fe9113-e239-4ed7-9fd1-a502d7ac7f47
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-phonegap
ms.devlang: js
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: d7a761310782faab1dda023785f93cf90742e2ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Introducción a Azure Mobile Engagement para Cordova/Phonegap
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

En este tema se muestra cómo usar Azure Mobile Engagement para comprender el uso que hace de las aplicaciones y enviar notificaciones push a los usuarios segmentados de una aplicación móvil desarrollada con Cordova.

En este tutorial, se creará una aplicación de Cordova vacía usando Mac y, después, se integrará el SDK de Mobile Engagement. Recopila datos de análisis básicos y recibe notificaciones push usando Apple Push Notification System (APNS) para iOS y Google Cloud Messaging (GCM) para Android. Se implementará en un dispositivo iOS o Android para las pruebas. 

> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).
> 
> 

Este tutorial requiere lo siguiente:

* XCode, que puede instalar desde Mac App Store (para la implementación en iOS).
* [Emulador y SDK de Android](http://developer.android.com/sdk/installing/index.html) (para su implementación en Android).
* Certificado de notificaciones push (.p12), que puede obtener en el centro de desarrolladores de Apple para APNS.
* Número de proyecto de GCM, que puede obtener en la consola de Google para desarrolladores para GCM.
* [Complemento Cordova para Mobile Engagement](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [!NOTE]
> Encontrará el código fuente y el archivo Léame del complemento Cordova en [GitHub](https://github.com/Azure/azure-mobile-engagement-cordova)
> 
> 

## <a id="setup-azme"></a>Configuración de Mobile Engagement para una aplicación Cordova
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Conectar la aplicación al back-end de Mobile Engagement
En este tutorial se presenta una "integración básica», que es el conjunto mínimo necesario para recopilar los datos y enviar una notificación de inserción. 

Crearemos una aplicación básica con Cordova para demostrar la integración:

### <a name="create-a-new-cordova-project"></a>Creación de un nuevo proyecto de Cordova
1. Inicie la ventana *Terminal* en Mac y escriba lo siguiente para crear un nuevo proyecto de Cordova a partir de la plantilla predeterminada. Asegúrese de que el perfil de publicación que usará para implementar la aplicación de iOS usa 'com.mycompany.myapp' como ID de aplicación. 
   
        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova
2. Para configurar el proyecto para **iOS** , ejecute lo siguiente en el simulador de iOS:
   
        $ cordova platform add ios 
        $ cordova run ios
3. Para configurar el proyecto para **Android**, ejecutarlo en el emulador de Android, ejecute las siguientes líneas. Asegúrese de que la configuración del emulador de Android SDK tiene su destino como Google API (Google Inc.) con CPU/ABI como ARM de las API de Google.  
   
        $ cordova platform add android
        $ cordova run android
4. Agregue el complemento Cordova Console. 

    ```
    $ cordova plugin add cordova-plugin-console
    ``` 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Conectar la aplicación al back-end de Mobile Engagement
1. Instale el complemento Cordova para Azure Mobile Engagement y proporcione los valores de las variables para configurar el complemento:
   
        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
             --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Icono de cobertura de Android* : debe ser el nombre del recurso sin ninguna extensión, ni prefijo dibujable (por ejemplo, mynotificationicon), y el archivo de icono se debe copiar en su proyecto Android (platforms/android/res/drawable)

*Icono de cobertura de iOS*: debe ser el nombre del recurso con su extensión (por ejemplo, mynotificationicon.png) y el archivo de icono debe agregarse al proyecto de iOS con XCode (mediante el menú Agregar archivos)

## <a id="monitor"></a>Habilitar supervisión en tiempo real
1. En el proyecto Cordova, edite **www/js/index.js** para agregar la llamada a Mobile Engagement para declarar una nueva actividad después de recibir el evento *deviceReady* .
   
         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }
2. Ejecute la aplicación:
   
   * **Para iOS**
     
       En la ventana `Terminal` , ejecute lo siguiente para iniciar la aplicación en una nueva instancia del simulador:
     
           cordova run ios
   * **Para Android**
     
       En la ventana `Terminal` , ejecute lo siguiente para iniciar la aplicación en una nueva instancia del emulador:
     
           cordova run android
3. Puede ver lo siguiente en los registros de la consola:
   
        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

## <a id="monitor"></a>Conexión de la aplicación con la supervisión en tiempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Habilitación de las notificaciones de inserción y mensajería en la aplicación
Mobile Engagement permite interactuar con los usuarios mediante notificaciones push y mensajería dentro de la aplicación en el contexto de las campañas. Este módulo se denomina REACH en el portal de Mobile Engagement.
En las secciones siguientes se instalará la aplicación para recibirlos.

### <a name="configure-push-credentials-for-mobile-engagement"></a>Configuración de las credenciales Push para Mobile Engagement
Para que Mobile Engagement envíe notificaciones push en su nombre, deberá concederle acceso al certificado de Apple iOS o a la clave de la API del servidor de GCM. 

1. Desplácese hasta el portal de Mobile Engagement. Asegúrese de que se encuentra en la aplicación que estamos usando para este proyecto y, luego, haga clic en el botón **Interactuar** en la parte inferior:
   
    ![][1]
2. Accederá a la página de configuración en el Portal de Engagement. Allí, haga clic en la sección **Inserción nativa** :
   
    ![][2]
3. Configuración del certificado de iOS o la clave de API del servidor de GCM
   
    **[iOS]**
   
    a. Elija el certificado .p12, cárguelo y escriba la contraseña:
   
    ![][3]
   
    **[Android]**
   
    a. Haga clic en el icono de edición de delante de **API Key** (Clave de API), en la sección GCM Settings (Configuración de GCM) y en el cuadro emergente que se muestra, pegue la clave del servidor GCM y haga clic en **OK** (Aceptar). 
   
    ![][4]

### <a name="enable-push-notifications-in-the-cordova-app"></a>Habilitación de notificaciones push en la aplicación de Cordova
Edite **www/js/index.js** para agregar la llamada a Mobile Engagement para solicitar notificaciones push y declarar un controlador:

     onDeviceReady: function() {
           Engagement.initializeReach(  
                 // on OpenUrl  
                 function(_url) {   
                 alert(_url);   
                 });  
            Engagement.startActivity("myPage",{});  
        }

### <a name="run-the-app"></a>Ejecución de la aplicación
**[iOS]**

1. Usaremos XCode para compilar e implementar la aplicación en el dispositivo para probar las notificaciones push porque iOS solo permite notificaciones push a un dispositivo real. Vaya a la ubicación donde se creará el proyecto Cordova y navegue a la ubicación **...\platforms\ios**. Abra el archivo .xcodeproj nativo en XCode. 
2. Compile e implemente la aplicación de Cordova en el dispositivo iOS usando la cuenta que tiene el perfil de aprovisionamiento que contiene el certificado que acaba de cargar al portal de Mobile Engagement y el identificador de la aplicación que coincide con el que proporcionó al crear la aplicación de Cordova. Puede consultar el *identificador de agrupación* en el archivo **Resources\*-info.plist** en XCode para que coincidan. 
3. Verá la ventana emergente de iOS estándar en el dispositivo que indica que la aplicación solicita permiso para enviar notificaciones. Conceda el permiso. 

**[Android]**

Puede usar el emulador para ejecutar la aplicación de Android porque las notificaciones de GCM se admiten en el emulador de Android. 

    cordova run android

## <a id="send"></a>Enviar una notificación a su aplicación
Ahora crearemos una campaña sencilla de notificación push que enviará una inserción a la aplicación que se ejecuta en el dispositivo:

1. Vaya a la pestaña **Cobertura** en el portal de Mobile Engagement.
2. Haga clic en **Nuevo anuncio** para crear la campaña de inserción.
   
    ![][6]
3. Proporcione entradas para crear la campaña **[Android]**
   
   * Proporcione un **Nombre** para la campaña. 
   * En **Delivery Type** (Tipo de entrega), seleccione *System notification* (Notificación del sistema) *Simple*
   * Seleccione el **Plazo de entrega** como *En cualquier momento*
   * Especifique un **Título** para la notificación que será la primera línea en la inserción.
   * Especifique un **Mensaje** para la notificación que servirá como el cuerpo del mensaje. 
     
     ![][11]
4. Proporcione entradas para crear la campaña **[iOS]**
   
   * Proporcione un **Nombre** para la campaña. 
   * Seleccione el **Plazo de entrega** como *Solo fuera de la aplicación*
   * Especifique un **Título** para la notificación que será la primera línea en la inserción.
   * Especifique un **Mensaje** para la notificación que servirá como el cuerpo del mensaje. 
     
     ![][12]
5. Desplácese hacia abajo y en la sección Contenido, seleccione **Solo notificación**
   
    ![][8]
6. [Opcional] También puede proporcionar la dirección URL de una acción. Asegúrese de que usa un esquema de URL proporcionado al configurar la variable **AZME\_REDIRECT\_URL** del complemento, por ejemplo, *myapp://test*.  
7. Ya ha terminado de configurar la campaña más básica posible. Ahora, desplácese de nuevo hacia abajo y haga clic en el botón **Crear** para guardar la campaña.
8. Por último, **active** la campaña.
   
    ![][10]
9. Ahora verá una notificación push en el dispositivo o en el emulador como parte de esta campaña. 

## <a id="next-steps"></a>Pasos siguientes
[Información general de todos los métodos disponibles con el SDK de Mobile Engagement para Cordova](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

