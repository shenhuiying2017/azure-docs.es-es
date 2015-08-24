<properties
	pageTitle="Introducción a Azure Mobile Engagement para iOS en Objective C"
	description="Aprenda a usar Azure Mobile Engagement con los análisis y las notificaciones de inserción para aplicaciones iOS."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="MehrdadMzfr"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="08/05/2015"
	ms.author="MehrdadMzfr" />

# Introducción a Azure Mobile Engagement para aplicaciones iOS en Objective C

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

En este tema se muestra cómo utilizar Azure Mobile Engagement para conocer el uso de las aplicaciones y enviar notificaciones push a los usuarios segmentados a una aplicación de iOS. En este tutorial, puede crear una aplicación iOS vacía que recopile datos básicos y reciba notificaciones push mediante el sistema de notificaciones push de Apple (APN). Cuando haya terminado, podrá difundir notificaciones de inserción a todos los dispositivos o dirigirse a usuarios específicos en función de las propiedades de sus dispositivos.

En este tutorial se demuestra el escenario de difusión sencillo con Mobile Engagement. Asegúrese de seguir el tutorial siguiente para ver cómo usar Mobile Engagement para dirigirse a usuarios y grupos de dispositivos específicos.

Este tutorial requiere lo siguiente:

+ XCode, que se puede instalar desde la tienda de aplicaciones MAC.
+ [SDK de Mobile Engagement iOS]
+ Certificado de notificaciones push (.p12), que puede obtener en el centro de desarrolladores de Apple.

Completar este tutorial es un requisito previo para todos los tutoriales de Mobile Engagement para aplicaciones iOS.

> [AZURE.IMPORTANT]Completar este tutorial es un requisito previo para los demás tutoriales de Mobile Engagement para aplicaciones iOS. Para completarlo, debe tener una cuenta activa de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure</a>.

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>Configurar Mobile Engagement para su aplicación

1. Inicie sesión en el Portal de administración de Azure y, luego, haga clic en **+NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, en **Mobile Engagement** y, luego, en **Crear**.

	![][7]

3. En el menú emergente que aparece, escriba la siguiente información:

   ![][8]

   - **Nombre de la aplicación**: escriba el nombre de la aplicación. Puede usar cualquier carácter.
   - **Plataforma**: seleccione la plataforma de destino (**iOS**) para la aplicación (si la aplicación tiene como destino varias plataformas, repita este tutorial para cada plataforma).
   - **Nombre del recurso de la aplicación**: este es el nombre por el que la aplicación será accesible a través de las API y las direcciones URL. Solo debe usar caracteres convencionales de dirección URL. El nombre que se genera automáticamente debería proporcionar una sugerencia sólida. También debe anexar el nombre de la plataforma para evitar cualquier conflicto de nombres, ya que este debe ser único.
   - **Ubicación**: seleccione el centro de datos donde se alojará esta aplicación y, más importante aún, su colección.
   - **Colección**: si ya ha creado una aplicación, seleccione una colección creada anteriormente; en caso contrario, seleccione una colección nueva.
   - **Nombre de la colección**: esto representa el grupo de aplicaciones. También garantizará que todas las aplicaciones se encuentren en un grupo que permitirá cálculos agregados de métricas. Debe usar el nombre de la empresa o departamento aquí si es aplicable.

4. Seleccione la aplicación que acaba de crear en la pestaña **Aplicación**:

5. Haga clic en **Información de conexión** para mostrar la configuración de la conexión que se debe incluir en la integración de SDK en su aplicación móvil.

	![][10]

6. Copie la **Cadena de conexión**, la necesitará para identificar esta aplicación en el código de aplicación y conectar con Mobile Engagement desde la aplicación de teléfono.

	![][11]

##<a id="connecting-app"></a>Conectar la aplicación al back-end de Mobile Engagement

En este tutorial se presenta una "integración básica», que es el conjunto mínimo necesario para recopilar los datos y enviar una notificación de inserción. Toda la información sobre la integración se encuentra en [documentación del SDK Mobile Engagement para iOS]

Crearemos una aplicación básica con XCode para demostrar la integración:

###Creación de un nuevo proyecto iOS

Puede omitir este paso si ya tiene una aplicación y está familiarizado con el desarrollo de iOS.

1. Inicie Xcode y, en el menú emergente, seleccione **Crear un nuevo proyecto en Xcode**.

	![][12]

2. Seleccione **Aplicación de vista sencilla** y haga clic en Siguiente.

	![][14]

3. Rellene el **Nombre del producto**, **Nombre de la organización** y **Identificador de organización**. Asegúrese de que ha seleccionado **Objective-C** en el lenguaje.

	![][13]

Xcode creará la aplicación de demostración en la que se integrará Mobile Engagement.

###Conectar la aplicación al back-end de Mobile Engagement

1. Descargue el [SDK de iOS para Mobile Engagement].
2. Extraiga el archivo .tar.gz archivo en una carpeta del equipo.
3. Haga clic con el botón derecho del mouse en el proyecto y elija "Agregar archivos a...".

	![][17]

4. Navegue hasta la carpeta donde extrajo el SDK y seleccione la carpeta `EngagementSDK` y, luego, haga clic en Aceptar.

	![][18]

5. Abra la ficha `Build Phases` y en el menú `Link Binary With Libraries` agregue los marcos tal y como se muestra a continuación:

	![][19]

6. Vuelva al portal de Azure en la página *Información de conexión* de la aplicación y copie la cadena de conexión

	![][11]

7. Abra el archivo de implementación del delegado de la aplicación y agregue la siguiente línea de código.

		#import "EngagementAgent.h"

8. Pegue la cadena de conexión en el delegado `didFinishLaunchingWithOptions`

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

##<a id="monitor"></a>Habilitar supervisión en tiempo real

Para comenzar a enviar datos y asegurarse de que los usuarios estén activos, debe enviar al menos una pantalla (Actividad) al back-end de Mobile Engagement.

- Abra el archivo `ViewController.h`, importe `EngagementViewController.h` y sustituya la superclase de la interfaz `ViewController` de `EngagementViewController` como se muestra a continuación:

![][22]

###Asegúrese de que la aplicación está conectada con la supervisión en tiempo real

En esta sección se muestra cómo asegurarse de que la aplicación se conecta al back-end de Mobile Engagement mediante la característica de supervisión en tiempo real de Mobile Engagement.

1. Acceda al portal de Mobile Engagement.

	En el portal de Azure, asegúrese de que se encuentra en la aplicación que estamos utilizando para este proyecto y, a continuación, haga clic en el botón "Interactuar" en la parte inferior:

	![][26]

2. Accederá a la página de configuración del portal de Engagement de la aplicación. Desde ahí, haga clic en la pestaña "Supervisar":

	![][30]

3. El monitor está listo para mostrar en tiempo real cualquier dispositivo que inicie la aplicación:

	![][31]

4. En Xcode, inicie la aplicación en el simulador o en un dispositivo conectado.

5. Si funcionó, ahora debería ver una sesión en el monitor.

**¡Enhorabuena!** El primer paso de este tutorial se realizó correctamente. Tiene una aplicación que se conecta al back-end de Mobile Engagement y que ya está enviando datos

6. Al hacer clic en el botón principal del simulador, el número de sesiones en el monitor volverá a ser 0, como se muestra arriba.

	![][33]

##<a id="integrate-push"></a>Habilitación de las notificaciones de inserción y mensajería en la aplicación

Mobile Engagement permite interactuar y llegar a los usuarios mediante notificaciones de inserción y mensajería en la aplicación en el contexto de las campañas. Este módulo se denomina REACH en el portal de Mobile Engagement. En las secciones siguientes se instalará la aplicación para recibirlos.

### Habilitación de la aplicación para recibir notificaciones push silenciosas

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]


### Adición de la biblioteca de cobertura al proyecto

1. Haga clic con el botón derecho del mouse en el proyecto.
2. Seleccionar `Add file to ...`
3. Navegue hasta la carpeta donde extrajo el SDK.
4. Seleccione la carpeta `EngagementReach`
5. Haga clic en Agregar

### Modifique su delegado de la aplicación

1. En la parte superior del archivo de implementación, importe el módulo de cobertura de Engagement.

		#import "AEReachModule.h"

2. Dentro de `application:didFinishLaunchingWithOptions`, cree un módulo de cobertura y páselo a la línea existente de inicialización de Engagement:

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
			AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
			[...]
			return YES;
		}

###Habilite su aplicación para recibir notificaciones push de APN.

1. Agregue la siguiente línea al método `application:didFinishLaunchingWithOptions`:

		if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
			[application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
			[application registerForRemoteNotifications];
		}
		else {

			[application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
		}

2. Agregue el método `application:didRegisterForRemoteNotificationsWithDeviceToken` de la siguiente forma:

		- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
		{
 			[[EngagementAgent shared] registerDeviceToken:deviceToken];
		}

3. Agregue el método `didReceiveRemoteNotification:fetchCompletionHandler` de la siguiente forma:

		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
		{
			[[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
		}

###Conceda acceso al certificado push para Mobile Engagement.

Para permitir que Mobile Engagement envíe notificaciones push en su nombre, deberá concederle acceso al certificado. Para ello, hay que configurar y registrar su certificado en el portal de Mobile Engagement. Asegúrese de obtener el certificado. p12, tal y como se explica en la documentación de Apple.

1. Desplácese hasta el portal de Mobile Engagement. Asegúrese de que se encuentra en la aplicación que estamos usando para este proyecto y, luego, haga clic en el botón "Interactuar" en la parte inferior:

	![][26]

2. Accederá a la página de configuración en el Portal de Engagement. Desde allí, haga clic en la sección "Inserción nativa" para cargar el certificado p12:

	![][27]

3. Elija el certificado p12, cárguelo y escriba la contraseña:

	![][28]

4. Agregue ahora su perfil de aprovisionamiento y compile la aplicación para un dispositivo de destino.

Estamos listos: ahora comprobaremos que esta integración básica se realizó correctamente.

##<a id="send"></a>Enviar una notificación a su aplicación

Ahora crearemos una campaña sencilla de notificación push que enviará una inserción a nuestra aplicación:

1. Vaya a la pestaña Cobertura en el portal de Mobile Engagement.

2. Haga clic en **Nuevo anuncio** para crear la campaña de inserción

	![][35]

3. Configure el primer campo de la campaña:

	![][36]

	- 	Asigne el nombre deseado a la campaña
	- 	Especifique la hora de entrega como "Fuera de aplicación solo": este es el tipo de notificación de inserción sencilla de Apple que incluye texto.
	- 	En el texto de notificación, escriba primero el título que será la primera línea del anuncio push.
	- 	A continuación, escriba el mensaje que será la segunda línea.


4. Desplácese hacia abajo. En la sección del contenido, elija "Solo notificación".

	![][37]

5. Ha terminado de configurar la campaña más básica posible. Ahora, desplácese hacia abajo de nuevo y cree la campaña para guardarla. ![][38]

6. Último paso: active la campaña ![][39]

7. Debería ver una notificación push en el dispositivo.

<!-- URLs. -->
[SDK de Mobile Engagement iOS]: http://go.microsoft.com/?linkid=9864553
[SDK de iOS para Mobile Engagement]: http://go.microsoft.com/?linkid=9864553
[Mobile Engagement Android SDK documentation]: http://go.microsoft.com/?linkid=9874682

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-ios-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[26]: ./media/mobile-engagement-ios-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[30]: ./media/mobile-engagement-ios-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-get-started/monitor.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-get-started/campaign-activate.png

<!---HONumber=August15_HO7-->