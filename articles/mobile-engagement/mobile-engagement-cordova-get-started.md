<properties
	pageTitle="Introducción a Azure Mobile Engagement para Cordova/Phonegap"
	description="Aprenda a usar Azure Mobile Engagement con los análisis y las notificaciones push para aplicaciones de Cordova/Phonegap."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-phonegap"
	ms.devlang="js"
	ms.topic="get-started-article" 
	ms.date="07/02/2015"
	ms.author="piyushjo" />

# Introducción a Azure Mobile Engagement para Cordova/Phonegap

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

En este tema se muestra cómo usar Azure Mobile Engagement para comprender el uso que hace de las aplicaciones y enviar notificaciones push a los usuarios segmentados de una aplicación móvil desarrollada con Cordova.

En este tutorial, se creará una aplicación de Cordova vacía usando Mac y, después, se integrará el SDK de Mobile Engagement. Recopila datos de análisis básicos y recibe notificaciones push usando Apple Push Notification System (APNS) para iOS y Google Cloud Messaging (GCM) para Android. Se implementará en un dispositivo iOS o Android para las pruebas.

> [AZURE.IMPORTANT]Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure￼</a>.

Este tutorial requiere lo siguiente:

+ XCode, que puede instalar desde Mac App Store (para la implementación en iOS).
+ [Emulador y SDK de Android](http://developer.android.com/sdk/installing/index.html) (para la implementación en Android).
+ Certificado de notificaciones push (.p12), que puede obtener en el centro de desarrolladores de Apple para APNS.
+ Número de proyecto de GCM, que puede obtener en la consola de Google para desarrolladores para GCM.
+ [Complemento Cordova para Mobile Engagement](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE]Encontrará el código fuente y el archivo Léame del complemento Cordova en [Github](https://github.com/Azure/azure-mobile-engagement-cordova)

##<a id="setup-azme"></a>Configurar Mobile Engagement para su aplicación

1. Inicie sesión en el Portal de administración de Azure y, luego, haga clic en **+NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, en **Mobile Engagement** y, luego, en **Crear**.

   	![][1]

3. En el menú emergente que aparece, escriba la siguiente información:

   	![][2]

	- **Nombre de la aplicación**: nombre de la aplicación. 
	- **Plataforma**: plataforma de destino de la aplicación (elija **iOS** o **Android** según donde vaya a implementar su aplicación de Cordova).
	- **Nombre del recurso de aplicación**: este es el nombre por el que la aplicación será accesible mediante API y direcciones URL. 
	- **Ubicación**: centro de datos donde se hospedará esta aplicación y la colección de aplicaciones.
	- **Colección**: seleccione una colección creada anteriormente o seleccione 'Nueva colección'.
	- **Nombre de la colección**: representa el grupo de aplicaciones. Esto garantiza que todas las aplicaciones están en un grupo que permitirá realizar cálculos agregados de métricas. Debe usar el nombre de la empresa o departamento aquí si es aplicable.

4. Seleccione la aplicación que acaba de crear en la pestaña **Aplicación**:

5. Haga clic en **Información de conexión** para mostrar la configuración de la conexión que se debe incluir en la integración de SDK en su aplicación móvil.

   	![][3]

6. Copie la **Cadena de conexión**, la necesitará para identificar esta aplicación en el código de aplicación y conectar con Mobile Engagement desde la aplicación de teléfono.

   	![][4]

##<a id="connecting-app"></a>Conectar la aplicación al back-end de Mobile Engagement

En este tutorial se presenta una "integración básica», que es el conjunto mínimo necesario para recopilar los datos y enviar una notificación de inserción.

Crearemos una aplicación básica con Cordova para demostrar la integración:

###Creación de un nuevo proyecto de Cordova

1. Inicie la ventana *Terminal* en su Mac y escriba lo siguiente para crear un nuevo proyecto de Cordova a partir de la plantilla predeterminada:

		$ cordova create azme-cordova com.mycompany.myapp
		$ cd azme-cordova

> [AZURE.IMPORTANT]Asegúrese de que el perfil de publicación que usará para implementar la aplicación de iOS usa 'com.mycompany.myapp' como ID de aplicación.

2. Para configurar el proyecto para **iOS**, ejecute lo siguiente en el simulador de iOS:

		$ cordova platform add ios 
		$ cordova run ios

3. Para configurar el proyecto para **iOS**, ejecute lo siguiente en el simulador de Android:

		$ cordova platform add android
		$ cordova run android

4. 	Agregue el complemento Cordova Console.

		$ cordova plugin add cordova-plugin-console 

###Conectar la aplicación al back-end de Mobile Engagement

1. Instale el complemento Cordova para Azure Mobile Engagement y proporcione los valores de las variables para configurar el complemento:

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
			--variable AZME_IOS_COLLECTION=<iOSAppCollectionName>.device.mobileengagement.windows.net
	        --variable AZME_IOS_SDKKEY=... 
	        --variable AZME_IOS_APPID=... 
	        --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
	        --variable AZME_ANDROID_COLLECTION=<AndroidAppCollectionName>.device.mobileengagement.windows.net
	        --variable AZME_ANDROID_SDKKEY=...
	        --variable AZME_ANDROID_APPID=...
			--variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
	        --variable AZME_REDIRECT_URL=... (URL scheme which triggers the app for deep linking)
	        --variable AZME_ENABLE_LOG=true|false

	Por ejemplo:

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement   
			--variable AZME_IOS_COLLECTION=apps-Collection.device.mobileengagement.windows.net
	        --variable AZME_IOS_SDKKEY=26dxxxxxxxxxxxxb794 
	        --variable AZME_IOS_APPID=piyxxxxxx
	        --variable AZME_IOS_REACH_ICON=icon.png 
			--variable AZME_ANDROID_COLLECTION=apps-Collection.device.mobileengagement.windows.net
	        --variable AZME_ANDROID_SDKKEY=c3d296xxxxxxxxxxc6540
	        --variable AZME_ANDROID_APPID=piyxxxxxxx
			--variable AZME_ANDROID_REACH_ICON=icon   
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=393xxxxxxx718
			--variable AZME_REDIRECT_URL=myapp 
			--variable AZME_ENABLE_LOG=true

> [AZURE.TIP]Pueden recuperar los valores de AppId, SDKKey y Collection de la cadena de conexión **Endpoint={YourAppCollection.Domain};SdkKey={YourSDKKey};AppId={YourAppId}**

##<a id="monitor"></a>Habilitar supervisión en tiempo real

1. En el proyecto Cordova, edite **www/js/index.js** para agregar la llamada a Mobile Engagement para declarar una nueva actividad después de recibir el evento *deviceReady*.

		 onDeviceReady: function() {
		        app.receivedEvent('deviceready');
		        AzureEngagement.startActivity("myPage",{});
		    }

2. Ejecute la aplicación:
		
	- **Para iOS**
	
		En la ventana `Terminal`, ejecute lo siguiente para iniciar la aplicación en una nueva instancia del simulador:

			cordova run ios

	- **Para Android**
		
		En la ventana `Terminal`, ejecute lo siguiente para iniciar la aplicación en una nueva instancia del emulador:

			cordova run android

3. Puede ver lo siguiente en los registros de la consola:

		[Engagement] Agent: Session started
		[Engagement] Agent: Activity 'myPage' started
		[Engagement] Connection: Established
		[Engagement] Connection: Sent: appInfo
		[Engagement] Connection: Sent: startSession
		[Engagement] Connection: Sent: activity name='myPage'

###Asegúrese de que la aplicación está conectada con la supervisión en tiempo real

En esta sección se muestra cómo asegurarse de que la aplicación se conecta al back-end de Mobile Engagement mediante la característica de supervisión en tiempo real de Mobile Engagement.

1. Acceda al portal de Mobile Engagement.

	En el portal de Azure, asegúrese de que se encuentra en la aplicación que estamos utilizando para este proyecto y, a continuación, haga clic en el botón **Interactuar** en la parte inferior:

	![][6]

2. Accederá a la página de configuración de la aplicación en el **portal de Engagement**. Desde ahí, haga clic en la pestaña **Supervisar**:

	![][7]

3. Si la aplicación que se ejecuta en el emulador está configurada correctamente, verá que se registra una sesión en tiempo real mientras se ejecuta su aplicación:

	![][8]

##<a id="integrate-push"></a>Habilitación de las notificaciones de inserción y mensajería en la aplicación

Mobile Engagement permite interactuar con los usuarios mediante notificaciones push y mensajería dentro de la aplicación en el contexto de las campañas. Este módulo se denomina REACH en el portal de Mobile Engagement. En las secciones siguientes se instalará la aplicación para recibirlos.

###Configuración de las credenciales Push para Mobile Engagement

Para que Mobile Engagement envíe notificaciones push en su nombre, deberá concederle acceso al certificado de Apple iOS o a la clave de la API del servidor de GCM.
	
1. Desplácese hasta el portal de Mobile Engagement. Asegúrese de que se encuentra en la aplicación que estamos usando para este proyecto y, luego, haga clic en el botón **Interactuar** en la parte inferior:
	
	![][10]
	
2. Accederá a la página de configuración en el Portal de Engagement. Allí, haga clic en la sección **Inserción nativa**:
	
	![][11]

3. Configuración del certificado de iOS o la clave de API del servidor de GCM

	**[iOS]**

	a. Elija el certificado .p12, cárguelo y escriba la contraseña:
	
	![][12]

	**[Android]**

	a. Haga clic en el icono de edición delante de **Clave de API**, en la sección Configuración de GCM, tal y como se muestra a continuación:
		
	![][20]
	
	b. En el menú emergente, pegue la clave del servidor de GCM, a continuación, haga clic en **Aceptar**.
	
	![][21]

###Habilitación de notificaciones push en la aplicación de Cordova

Edite **www/js/index.js** para agregar la llamada a Mobile Engagement para solicitar notificaciones push y declarar un controlador:

	 onDeviceReady: function() {
	        app.receivedEvent('deviceready');
	        AzureEngagement.registerForPushNotification();
	        AzureEngagement.onOpenURL(function(_url) { alert(_url); });
	        AzureEngagement.startActivity("myPage",{});
	    }

###Ejecución de la aplicación

**[iOS]**

1. Usaremos XCode para compilar e implementar la aplicación en el dispositivo para probar las notificaciones push porque iOS solo permite notificaciones push a un dispositivo real. Vaya a la ubicación donde se creará el proyecto Cordova y vaya a la ubicación **...\\platforms\\ios**. Abra el archivo .xcodeproj nativo en XCode. 
	
2. Compile e implemente la aplicación de Cordova en el dispositivo iOS usando la cuenta que tiene el perfil de aprovisionamiento que contiene el certificado que acaba de cargar al portal de Mobile Engagement y el identificador de la aplicación que coincide con el que proporcionó al crear la aplicación de Cordova. Puede consultar el *identificador del paquete* en su archivo **Resources*-info.plist** en XCode para que coincidan.

3. Verá la ventana emergente de iOS estándar en el dispositivo que indica que la aplicación solicita permiso para enviar notificaciones. Conceda el permiso.

**[Android]**

Puede usar el emulador para ejecutar la aplicación de Android porque las notificaciones de GCM se admiten en el emulador de Android.

	cordova run android

##<a id="send"></a>Enviar una notificación a su aplicación

Ahora crearemos una campaña sencilla de notificación push que enviará una inserción a la aplicación que se ejecuta en el dispositivo:

1. Vaya a la pestaña Cobertura en el portal de Mobile Engagement.

2. Haga clic en **Nuevo anuncio** para crear la campaña de inserción.

	![][13]

3. Proporcione entradas para crear la campaña:

	![][14]

	- 	Proporcione un nombre para la campaña. 
	- 	**[Android]** Seleccione el **Tipo de entrega** *Notificación del sistema* - *Simple*
	- 	Seleccione la hora de entrega como 
		- 	Para **iOS**: *"Solo fuera de la aplicación"*
		- 	Para **Android**: *"En cualquier momento"*
		
		Este es un tipo de notificación push sencillo que incluye un texto.
	- 	En el texto de notificación, escriba primero el título que será la primera línea del anuncio push.
	- 	A continuación, escriba el mensaje que será la segunda línea.

4. Desplácese hacia abajo y, en la sección de contenido, seleccione **Solo notificación**.

	![][15]

5. [Opcional] También puede proporcionar la dirección URL de una acción. Asegúrese de que usa un esquema de URL proporcionado al configurar la variable **AZME REDIRECT URL** del complemento, por ejemplo, *myapp://test*. 

5. Ha terminado de configurar la campaña más básica posible. Ahora, desplácese hacia abajo de nuevo y **cree** la campaña para guardarla.
	
	![][16]

6. Por último, **active** la campaña.
	
	![][17]

7. Ahora verá una notificación push en el dispositivo o en el emulador como parte de esta campaña.

##<a id="next-steps"></a>Pasos siguientes
[Información general de todos los métodos disponibles con el SDK de Mobile Engagement para Cordova](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553

<!-- Images. -->
[1]: ./media/mobile-engagement-cordova-get-started/create-mobile-engagement-app.png
[2]: ./media/mobile-engagement-cordova-get-started/create-azme-popup.png
[3]: ./media/mobile-engagement-cordova-get-started/app-main-page-select-connection-info.png
[4]: ./media/mobile-engagement-cordova-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[7]: ./media/mobile-engagement-cordova-get-started/clic-monitor-tab.png
[8]: ./media/mobile-engagement-cordova-get-started/monitor.png
[10]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[11]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[12]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[13]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[14]: ./media/mobile-engagement-cordova-get-started/campaign-first-params.png
[15]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[16]: ./media/mobile-engagement-cordova-get-started/campaign-create.png
[17]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[18]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[19]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[20]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[21]: ./media/mobile-engagement-cordova-get-started/api-key.png

<!---HONumber=July15_HO2-->