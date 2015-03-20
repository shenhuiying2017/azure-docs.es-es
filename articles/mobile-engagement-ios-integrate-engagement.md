<properties 
	pageTitle="Integración del SDK de iOS de Azure Mobile Engagement" 
	description="Actualizaciones más recientes y procedimientos para el SDK de iOS para Azure Mobile Engagement"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />


<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store">Tienda Windows</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS" class="current">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>


#Integración de Engagement en iOS

> [AZURE.IMPORTANT] El SDK de Engagement requiere iOS4+: el destino de implementación de la aplicación debe ser como mínimo iOS 4.

Este procedimiento describe la manera más sencilla de activar las funciones de supervisión y análisis de Engagement en su aplicación iOS.

Los siguientes pasos son suficientes para activar el informe de registros necesarios para calcular todas las estadísticas relacionadas con los usuarios, las sesiones, las actividades, los bloqueos y las cuestiones técnicas. El informe de registros necesarios para calcular otras estadísticas (como las tareas, los errores y los eventos) debe realizarse manualmente mediante la API de Engagement (consulte ios-sdk-engagement-advanced)), ya que estas estadísticas dependen de la aplicación.

##Incrustación del SDK de Engagement en su proyecto de iOS

Agregue el SDK de Engagement al proyecto de iOS: en Xcode 4, haga clic con el botón derecho en el proyecto, elija **"Agregar archivos a..."** y elija la carpeta `EngagementSDK`.

Engagement requiere la contratación de marcos adicionales para trabajar: en el Explorador de proyectos, abra el panel de proyectos y elija el destino correcto. A continuación, abra la pestaña **"Fases de compilación"** en el menú **"Enlace binario con bibliotecas"** y agregue estos marcos:

> -   `AdSupport.framework` - configure el vínculo como  `Opcional`
> -   `SystemConfiguration.framework`
> -   `CoreTelephony.framework`
> -   `CFNetwork.framework`
> -   `CoreLocation.framework`
> -   `libxml2.dylib`

> [AZURE.NOTE] El marco de trabajo AdSupport se puede quitar. Engagement necesita este marco para recopilar el IDFA. No obstante, la recopilación de IDFA se puede deshabilitar \<ios-sdk-engagement-idfa\> para cumplir con la nueva directiva de Apple con respecto a este identificador.

##Inicialización del SDK de Engagement

Debe modificar el delegado de la aplicación:

-   En la parte superior del archivo de implementación, importe el agente de Engagement.

			[...]
			#import "EngagementAgent.h"

-   Inicialice Engagement dentro del método '**applicationDidFinishLaunching:**' o '**application:didFinishLaunchingWithOptions:**':

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
			  [...]
			}

##Informes básicos

### Método recomendado: sobrecargar las clases `UIViewController` 

Si desea que se generen todos los registros que requiere Engagement para calcular los usuarios, las sesiones, las actividades, los bloqueos y las estadísticas técnicas, simplemente puede hacer que todas las subclases de `UIViewController` hereden de las clases `EngagementViewController` (misma regla para `UITableViewController` -\ > `EngagementTableViewController`).

**Sin Engagement:**

			#import <UIKit/UIKit.h>
			
			@interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
			  UITextField* myTextField1;
			  UITextField* myTextField2;
			}
			
			@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
			@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Con Engagement:**

			#import <UIKit/UIKit.h>
			#import "EngagementViewController.h"
			
			@interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
			  UITextField* myTextField1;
			  UITextField* myTextField2;
			}
			
			@property (nonatomic, retain) IBOutlet UITextField* myTextField1;
			@property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### Método alternativo: ejecute `startActivity()` manualmente

Si no puede o no desea sobrecargar las clases `UIViewController`, en su lugar, puede iniciar sus actividades mediante una llamada directa a los métodos de `EngagementAgent`.

> [AZURE.IMPORTANT] El SDK de iOS llama automáticamente al método `endActivity()` cuando se cierra la aplicación. Por lo tanto, se recomienda *encarecidamente* que use el método `startActivity` cada vez que cambie la actividad del usuario y que  *nunca* se haga una llamada al método `endActivity`, ya que ello provoca la finalización de la sesión actual.

##Informes de ubicación

Las condiciones de servicio de Apple no permiten a las aplicaciones utilizar el seguimiento de ubicación con fines de estadísticas únicamente. Por lo tanto, se recomienda habilitar los informes de ubicación solo si la aplicación también utiliza el seguimiento de ubicación por otro motivo.

A partir de iOS 8, debe proporcionar una descripción del modo en que su aplicación utiliza los servicios de ubicación estableciendo una cadena para la clave [NSLocationWhenInUseUsageDescription] o [NSLocationAlwaysUsageDescription] en el archivo Info.plist de la aplicación. Si desea registrar la ubicación en segundo plano con Engagement, agregue la clave NSLocationAlwaysUsageDescription. En los demás casos, agregue la clave NSLocationWhenInUseUsageDescription.

### Informes de ubicaciones de áreas diferidas

Los informes de ubicación de área diferida permiten notificar el país, la región y la localidad asociados con los dispositivos. Este tipo de informe de ubicación sólo emplea ubicaciones de red (basadas en el identificador del teléfono móvil o en WIFI). El área del dispositivo se notifica como máximo una vez por sesión. El GPS no se utiliza nunca y, por tanto, este tipo de informe de ubicación tiene muy poco impacto (por no decir ninguno) en la batería.

Las áreas notificadas se utilizan para elaborar estadísticas geográficas acerca de los usuarios, las sesiones, los eventos y los errores. También se pueden usar como criterios en campañas de cobertura. La última área conocida registrada para un dispositivo se puede recuperar gracias a la [API del dispositivo].

Para habilitar los informes de ubicación de área diferida, agregue la siguiente línea después de inicializar el agente de Engagement:

			- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
			{
			  [...]
			  [[EngagementAgent shared] setLazyAreaLocationReport:YES];
			  [...]
			}

### Informes de ubicación en tiempo real

Los informes de ubicación en tiempo real permiten notificar la latitud y la longitud asociadas con los dispositivos. De forma predeterminada, este tipo de informes de ubicación solo emplea ubicaciones de red (basadas en el identificador del teléfono móvil o en WIFI), y los informes solo están activos cuando la aplicación se ejecuta en primer plano (por ejemplo, durante una sesión).

Las ubicaciones en tiempo real  *no* se utilizan para calcular las estadísticas. Su única finalidad es permitir el uso de criterios de
barreras geográficas \<Reach-Audience-geofencing\> en tiempo real en las campañas de cobertura.

Para habilitar los informes de ubicación en tiempo real, agregue la siguiente línea después de inicializar el agente de Engagement:

			[[EngagementAgent shared] setRealtimeLocationReport:YES];

#### Informes basados en GPS

De forma predeterminada, los informes de ubicación en tiempo real solo emplean ubicaciones de red. Para habilitar el uso de ubicaciones basadas en GPS (que son mucho más precisas), agregue:

			[[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### Informes en segundo plano

De forma predeterminada, los informes de ubicación en tiempo real solo están activos cuando la aplicación se ejecuta en primer plano (es decir, durante una sesión). Para habilitar los informes también en segundo plano, agregue:

			[[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE] Cuando la aplicación se ejecuta en segundo plano, solo se notifican las ubicaciones de red, incluso si ha habilitado el GPS.

La implementación de esta función efectuará una llamada a [startMonitoringSignificantLocationChanges] cuando la aplicación pase a un segundo plano. Tenga en cuenta que la aplicación se reiniciará automáticamente en segundo plano si se produce un nuevo evento de ubicación.

##Informes avanzados

De manera opcional, si desea notificar eventos, errores y trabajos específicos de la aplicación, deberá usar la API de Engagement a través de los métodos de la clase `EngagementAgent`. Un objeto de esta clase se puede recuperar mediante una llamada al método estático`[EngagementAgent shared]`.

La API de Engagement permite usar todas las capacidades avanzadas de Engagement, las cuales se detallan en la documentación sobre el uso de la
API de Engagement en iOS (así como en la documentación técnica de la clase `EngagementAgent`).

##Deshabilitación de la recopilación de IDFA

De forma predeterminada, Engagement utilizará [IDFA] para identificar a un usuario de forma exclusiva. Pero si no está utilizando publicidad en otra parte de la aplicación, es posible que el proceso de revisión de la tienda de aplicaciones le rechace. La colección de IDFA se puede deshabilitar mediante la adición de la macro de preprocesador `ENGAGEMENT_DISABLE_IDFA` en el archivo pch (o en la  `configuración de compilación` de la aplicación). Así se asegurará de que no haya ninguna referencia a `ASIdentifierManager`, `advertisingIdentifier` ni a `isAdvertisingTrackingEnabled` en la compilación de la aplicación.

Integración en el archivo **prefix.pch**:

			#define ENGAGEMENT_DISABLE_IDFA
			...

Puede verificar que la colección IDFA está correctamente deshabilitada en la aplicación comprobando los registros de prueba de Engagement. Consulte la documentación de las pruebas de integración <ios-sdk-engagement-test-idfa\> para obtener más información.

##Deshabilitación de los informes de registro

### Mediante una llamada al método

Si desea que Engagement deje de enviar registros, puede efectuar una llamada:

			[[EngagementAgent shared] setEnabled:NO];

Esta llamada es persistente: usa `NSUserDefaults` para almacenar la información.

Puede habilitar la creación de registros mediante una llamada a la misma función con `YES`.

### Integración en la agrupación de configuraciones

En lugar de llamar a esta función, también puede integrar esta configuración directamente en el archivo `Settings.bundle` existente. La cadena `engagement_agent_enabled` debe utilizarse como un identificador de preferencia y debe estar asociada a un modificador para alternar (`PSToggleSwitchSpecifier`).

El siguiente ejemplo de `Settings.bundle` muestra el proceso de implementación:

			<dict>
			    <key>PreferenceSpecifiers</key>
			    <array>
			        <dict>
			            <key>DefaultValue</key>
			            <true/>
			            <key>Key</key>
			            <string>engagement_agent_enabled</string>
			            <key>Title</key>
			            <string>Log reporting enabled</string>
			            <key>Type</key>
			            <string>PSToggleSwitchSpecifier</string>
			        </dict>
			    </array>
			    <key>StringsTable</key>
			    <string>Root</string>
			</dict>

<!-- URLs. -->
[API de dispositivos]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier

<!--HONumber=47-->
