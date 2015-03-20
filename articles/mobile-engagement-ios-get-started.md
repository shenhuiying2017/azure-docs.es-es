<properties 
	pageTitle="Introducción a Azure Mobile Engagement para iOS" 
	description="Aprenda a usar Azure Mobile Engagement con los análisis y las notificaciones push de iOS" 
	services="mobile-engagement" 
	documentationCenter="Mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />

# Introducción a Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store">Tienda Windows</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS"  class="current">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

En este tema se muestra cómo utilizar Azure Mobile Engagement para conocer el uso de las aplicaciones y enviar notificaciones push a los usuarios segmentados a una aplicación de iOS. 
En este tutorial, puede crear una aplicación iOS vacía que recopile datos básicos y reciba notificaciones push mediante el sistema de notificaciones push de Apple (APN). Cuando haya finalizado, podrá difundir las notificaciones push a todos los dispositivos o dirigirse a usuarios específicos en función de las propiedades de los dispositivos.

En este tutorial, se expone un escenario de difusión sencillo con Mobile Engagement. Asegúrese de seguir estas indicaciones junto con las que aparecen en el próximo tutorial para saber cómo usar Mobile Engagement para abordar usuarios y grupos de dispositivos específicos. 

Este tutorial requiere lo siguiente:

+ Xcode, que se puede instalar desde la tienda de aplicaciones MAC.
+ [SDK de Mobile Engagement iOS]
+ Certificado de notificaciones push (.p12), que puede obtener en el centro de desarrolladores de Apple.

Completar este tutorial es un requisito previo para todos los tutoriales de Mobile Engagement para aplicaciones iOS. 

<div class="dev-callout"><strong>Nota</strong> <p>para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener información más detallada, consulte <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fes-es%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure.</a></p></div>

<!--
##<a id="register"></a>Enable Apple Push Notification Service

[WACOM.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]
-->

##<a id="setup-azme"></a>Configuración de Mobile Engagement para su aplicación

1. Inicie sesión en el [Portal de administración de Azure] y, a continuación, haga clic en **+NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, después en **Mobile Engagement** y, por último, en **Crear**.

   	![][7]

3. En el menú emergente que aparece, hay que rellenar varios campos:
 
   	![][8]

	1. *Nombre de la aplicación*: especifique el nombre de la aplicación. Puede utilizar cualquier carácter.
	2. *Plataforma*: Elija la plataforma de destino para esa aplicación (si la aplicación está destinada a usarse en varias plataformas, repita este tutorial para cada plataforma).
	3. *Nombre de recurso de aplicación*: Este es el nombre por el que estará accesible esta aplicación a través de las API y las direcciones URL. Le recomendamos que utilice solo caracteres convencionales de dirección URL: el nombre generado automáticamente puede servirle como base segura. También recomendamos anexar el nombre de la plataforma para evitar cualquier conflicto de nombres, ya que este nombre debe ser único.
	4. *Ubicación*: Elija el centro de datos donde se alojará esta aplicación y, más importante aún, su colección (consulte la siguiente información).
	5. *Colección*: Si ya ha creado una aplicación, elija una colección creada anteriormente; en caso contrario, elija una colección nueva.


	Cuando haya terminado, haga clic en el botón de comprobación para finalizar la creación de la aplicación.

4. Ahora haga clic en la aplicación que acaba de crear (o selecciónela) en la pestaña Aplicación:
 
   	![][9]

5. A continuación, haga clic en "Información de conexión" para mostrar la configuración de la conexión que se debe incluir en la integración de SDK:
 
   	![][10]

6. Por último, anote la "Cadena de conexión", que es lo que necesitará para identificar esta aplicación desde el código de aplicación:

   	![][11]

	>[AZURE.TIP] Puede usar el icono "Copiar" situado a la derecha de la cadena de conexión para copiarla en el Portapapeles cómodamente.

##<a id="connecting-app"></a>Conexión de una aplicación al back-end de Mobile Engagement

Este tutorial presenta una "integración básica", que es el conjunto mínimo establecido necesario para recopilar los datos y enviar una notificación push. Toda la información sobre la integración se encuentra en la [documentación del SDK de iOS de Mobile Engagement].

Crearemos una aplicación básica con Android Studio para demostrar la integración.

###Creación de un nuevo proyecto iOS

Puede omitir este paso si ya tiene una aplicación y está familiarizado con el desarrollo de iOS.

1. Inicie Xcode y, en el menú emergente, elija "Crear un nuevo proyecto en Xcode".

   	![][12]

2. Escriba el nombre de la aplicación, así como el de la compañía y el identificador. Anote estos datos, ya que los necesitará más adelante. Haga clic en Siguiente.

   	![][13]

3. Ahora elija Vista sencilla de aplicación y haga clic en Siguiente.

   	![][14]


Xcode creará la aplicación de demostración en la que se integrará Mobile Engagement.

###Incluya la biblioteca SDK en el proyecto.

Descargue e integre la biblioteca de SDK.

1. Descargue el [SDK de iOS para Mobile Engagement].
2. Extraiga el archivo .tar.gz archivo en una carpeta del equipo.
3. Haga clic con el botón derecho del mouse en el proyecto y elija "Agregar archivos a...".

	![][17]

4. Navegue hasta la carpeta donde extrajo el SDK y elija la carpeta  `EngagementSDK`. A continuación, haga clic en Aceptar.

	![][18]

5. Abra la pestaña  `Fases de la compilación` y, en el menú  `Vincular binarios con bibliotecas`, agregue los marcos de trabajo, tal y como se muestra a continuación:

	![][19]


###Conecte su aplicación al back-end de Mobile Engagement con la cadena de conexión.

1. Copie la siguiente línea de código en la parte superior del archivo de implementación del delegado de la aplicación.

		#import "EngagementAgent.h"

2. Vuelva al portal de Azure en la página  *Información de conexión* de la aplicación y copie la cadena de conexión.

	![][11]

3. Péguela en el delegado `didFinishLaunchingWithOptions`.		

		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
  			[...]
  			[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
  			[...]
		}

##Envíe una pantalla a Mobile Engagement.

Para iniciar el envío de datos y asegurarse de que los usuarios estén activos, debe enviar al menos 1 pantalla (actividad) al back-end de Mobile Engagement. Conseguiremos esto mediante la sobrecarga de la clase `UIViewController` con el `EngagementViewController` que proporciona nuestro SDK.
Para ello, abra el archivo `ViewController.h`, importe `EngagementViewController.h` y sustituya la superclase de la interfaz de `ViewController` por `EngagementViewController`, tal y como se muestra a continuación:

![][22]

##<a id="monitor"></a>Comprobación de que la aplicación está conectada a la supervisión en tiempo real

En esta sección, se muestra cómo asegurarse de que la aplicación esté conectada al back-end de Mobile Engagement usando la característica de supervisión en tiempo real de Mobile Engagement.

1. Acceda al portal de Mobile Engagement.

	En el portal de Azure, asegúrese de que se encuentra en la aplicación que estamos utilizando para este proyecto y, a continuación, haga clic en el botón "Interactuar" en la parte inferior:

	![][26]

2. Accederá a la página de configuración del portal de Engagement de la aplicación. Desde ahí, haga clic en la pestaña "Supervisar":

	![][30]

3. El monitor está listo para mostrar en tiempo real cualquier dispositivo que inicie la aplicación:

	![][31]

4. En Xcode, inicie la aplicación en el simulador o en un dispositivo conectado.

5. Si funciona, ahora debería ver 1 sesión en el monitor. 

**Bien.** El primer paso de este tutorial se realizó correctamente. Tiene una aplicación que se conecta al back-end de Mobile Engagement y que ya está enviando datos.

6. Al hacer clic en el botón principal del simulador, el número de sesiones en el monitor volverá a ser 0, como se muestra arriba.

	![][33]



##<a id="integrate-push"></a>Habilitación de las notificaciones push y la mensajería dentro de la aplicación

Mobile Engagement le permite interactuar con los usuarios y conocer su cobertura con notificaciones push y mensajería dentro de la aplicación en el contexto de las campañas. Este módulo se denomina "módulo de cobertura" en el portal de Mobile Engagement.
Las secciones siguientes configurarán la aplicación para recibirlos.

### Adición de la biblioteca de cobertura al proyecto

1. Haga clic con el botón derecho del mouse en el proyecto.
2. Elija `Agregar archivo a...`
3. Navegue hasta la carpeta donde extrajo el SDK.
4. Elija la carpeta  `EngagementReach`.
5. Haga clic en Agregar

### Modificación del delegado de una aplicación

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

3. Agregue el método `didReceiveRemoteNotification` de la siguiente forma:
4. 
		- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
		{
		    [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo];
		}

###Conceda acceso al certificado push para Mobile Engagement.

Para permitir que Mobile Engagement envíe notificaciones push en su nombre, deberá concederle acceso al certificado. Para ello, hay que configurar y registrar su certificado en el portal de Mobile Engagement. Asegúrese de obtener el certificado. p12, tal y como se explica en la documentación de Apple.

1. Acceda al portal de Mobile Engagement.

	En el portal de Azure, asegúrese de que se encuentra en la aplicación que estamos utilizando para este proyecto y, a continuación, haga clic en el botón "Interactuar" en la parte inferior:

	![][26]

2. Accederá a la página de configuración en el Portal de Engagement. Desde allí, haga clic en la sección "Inserción nativa" para cargar el certificado p12:

	![][27]

3. Elija el certificado p12, cárguelo y escriba la contraseña:

	![][28]

4. Agregue ahora su perfil de aprovisionamiento y compile la aplicación para un dispositivo de destino.

Estamos listos: ahora comprobaremos que esta integración básica se realizó correctamente.

##<a id="send"></a>Envío de una notificación a la aplicación

Ahora crearemos una campaña sencilla de notificación push que enviará una inserción a nuestra aplicación:

1. Vaya a la pestaña Cobertura en el portal de Mobile Engagement.

2. Haga clic en "Nuevo anuncio" para crear la campaña push.
	
	![][35]

3. Configure el primer campo de la campaña:

	![][36]

	1. Asigne el nombre que desee a la campaña
	2. Especifique la hora de entrega como "Fuera de aplicación solo": este es el tipo de notificación push sencilla de Apple que incluye texto.
	3. En el texto de notificación, escriba primero el título que será la primera línea del anuncio push.
	4. A continuación, escriba el mensaje que será la segunda línea.

4. Desplácese hacia abajo. En la sección del contenido, elija "Solo notificación".

	![][37]

5. Ya ha terminado de configurar la campaña más básica posible. Ahora, desplácese hacia abajo de nuevo y cree la campaña para guardarla.
![][38]

6. Último paso: active la campaña.
![][39]

7. Debería ver una notificación push en el dispositivo.

<!-- URLs. -->
[SDK de iOS para Mobile Engagement]: http://go.microsoft.com/?linkid=9864553
[Documentación de SDK de Android de Mobile Engagement]: http://go.microsoft.com/?linkid=9874682
[Portal de administración de Azure]: https://manage.windowsazure.com

<!-- Images. -->
[7]: ./media/mobile-engagement-ios-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-ios-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-ios-get-started/select-app.png
[10]: ./media/mobile-engagement-ios-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-ios-get-started/xcode-new-project.png
[13]: ./media/mobile-engagement-ios-get-started/xcode-project-props.png
[14]: ./media/mobile-engagement-ios-get-started/xcode-simple-view.png
[17]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[18]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[19]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[22]: ./media/mobile-engagement-ios-get-started/xcode-view-controller.png
[23]: ./media/mobile-engagement-ios-get-started/copy-resources.png
[24]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[25]: ./media/mobile-engagement-ios-get-started/paste-resources.png
[26]: ./media/mobile-engagement-ios-get-started/engage-button.png
[27]: ./media/mobile-engagement-ios-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-ios-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-ios-get-started/api-key.png
[30]: ./media/mobile-engagement-ios-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-ios-get-started/monitor.png
[32]: ./media/mobile-engagement-ios-get-started/launch.png
[33]: ./media/mobile-engagement-ios-get-started/monitor-0.png
[35]: ./media/mobile-engagement-ios-get-started/new-announcement.png
[36]: ./media/mobile-engagement-ios-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-ios-get-started/campaign-content.png
[38]: ./media/mobile-engagement-ios-get-started/campaign-create.png
[39]: ./media/mobile-engagement-ios-get-started/campaign-activate.png

<!--HONumber=47-->
