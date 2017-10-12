---
title: "Integración de la cobertura del SDK de iOS de Azure Mobile Engagement | Microsoft Docs"
description: "Actualizaciones y procedimientos más recientes para el SDK de iOS para Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1f5f5857-867c-40c5-9d76-675a343a0296
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 12/13/2016
ms.author: piyushjo
ms.openlocfilehash: ba74e0c442ac10f096d465f989e03d2ceae8cd88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-engagement-reach-on-ios"></a>Cómo integrar la cobertura de Engagement en iOS
Debe seguir el procedimiento de integración que se describe en el documento [Integración de Engagement en iOS](mobile-engagement-ios-integrate-engagement.md) antes de seguir esta guía.

Esta documentación requiere XCode 8. Si realmente depende de XCode 7, puede usar el [SDK v3.2.4 de Engagement para iOS](https://aka.ms/r6oouh). Existe un problema conocido en esta versión anterior cuando se ejecuta en dispositivos iOS 10: las notificaciones del sistema no se ejecutan. Para solucionarlo, tendrá que implementar la API `application:didReceiveRemoteNotification:` en desuso en su delegado de aplicación de la manera siguiente:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [!IMPORTANT]
> **No se recomienda esta solución** ya que este comportamiento puede cambiar en la próxima actualización de la versión de iOS (por menor que sea) porque esta API de iOS está en desuso. Debe cambiar a XCode 8 tan pronto como sea posible.
>
>

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Habilitación de la aplicación para recibir notificaciones push silenciosas
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

## <a name="integration-steps"></a>Pasos de integración
### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>Inserte el SDK de cobertura de Engagement en su proyecto de iOS
* Agregue el sdk de cobertura en su proyecto de Xcode. En Xcode, vaya a **Proyecto \>Agregar a un proyecto** y elija la carpeta `EngagementReach`.

### <a name="modify-your-application-delegate"></a>Modifique su delegado de la aplicación
* En la parte superior del archivo de implementación, importe el módulo de cobertura de Engagement:

      [...]
      #import "AEReachModule.h"
* Dentro del método `applicationDidFinishLaunching:` o `application:didFinishLaunchingWithOptions:`, cree un módulo de cobertura y páselo a la línea existente de inicialización de Engagement:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
        [...]

        return YES;
      }
* Modifique la cadena **'icon.png'** con el nombre de la imagen que desee como icono de la notificación.
* Si desea utilizar la opción *Actualizar valor de notificación* en las campañas de cobertura o si desea usar campañas de inserción nativa \</SaaS/Reach API/Campaign format/Native Push\>, deberá permitir que el módulo de cobertura administre el propio icono de la notificación (borrará automáticamente la notificación de la aplicación y también restablecerá el valor almacenado por Engagement cada vez que se inicie o establezca la aplicación en primer plano). Para ello, agregue la línea siguiente después de la inicialización del módulo de cobertura:

      [reach setAutoBadgeEnabled:YES];
* Si desea controlar la inserción de datos de cobertura, debe permitir que el delegado de la aplicación se ajuste al protocolo `AEReachDataPushDelegate` . Agregue la siguiente línea después de la inicialización del módulo de alcance:

      [reach setDataPushDelegate:self];
* A continuación, puede implementar los métodos `onDataPushStringReceived:` y `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` en el delegado de la aplicación:

      -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
      {
         NSLog(@"String data push message with category <%@> received: %@", category, body);
         return YES;
      }

      -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
      {
         NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
         // Do something useful with decodedBody like updating an image view
         return YES;
      }

### <a name="category"></a>Categoría
El parámetro de categoría es opcional cuando se crea una campaña de inserción de datos y permite filtrar los datos que inserta. Esto es útil si desea insertar distintos tipos de datos `Base64` y desea identificar su tipo antes de analizarlos.

**La aplicación ya está lista para recibir y mostrar el contenido de la cobertura.**

## <a name="how-to-receive-announcements-and-polls-at-any-time"></a>Cómo recibir anuncios y sondeos en cualquier momento
Engagement puede enviar notificaciones de cobertura a los usuarios finales en cualquier momento utilizando el servicio de notificación de inserción de Apple.

Para habilitar esta funcionalidad, tendrá que preparar su aplicación para notificaciones de inserción de Apple y modificar al delegado de la aplicación.

### <a name="prepare-your-application-for-apple-push-notifications"></a>Preparar la aplicación para notificaciones de inserción de Apple
Siga la guía indicada a continuación: [Cómo preparar la aplicación para las notificaciones de inserción de Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>Agregue el código de cliente necesario
*En este momento la aplicación debe tener un certificado de inserción de Apple registrado en el frontend de Engagement.*

Si no lo ha hecho ya, deberá registrar su aplicación para recibir notificaciones de inserción.

* Importe la plataforma `User Notification` :

        #import <UserNotifications/UserNotifications.h>
* Agregue la siguiente línea al iniciarse la aplicación (normalmente, en `application:didFinishLaunchingWithOptions:`):

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

A continuación, deberá proporcionar a Engagement el token de dispositivo devuelto por servidores de Apple. Esto se hace en el método llamado `application:didRegisterForRemoteNotificationsWithDeviceToken:` del delegado de aplicación:

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

Por último, debe informar al SDK de Engagement cuando la aplicación reciba una notificación remota. Para ello, simplemente llame al método `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` en el delegado de aplicación:

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [!IMPORTANT]
> De forma predeterminada, Engagement Reach controla completionHandler. Si desea responder manualmente al bloque `handler` del código, se puede pasar nil para el argumento `handler` y controlar el bloque de finalización personalmente. Consulte el tipo `UIBackgroundFetchResult` para obtener una lista de valores posibles.
>
>

### <a name="full-example"></a>Ejemplo completo
Este es un ejemplo completo de integración:

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

### <a name="resolve-unusernotificationcenter-delegate-conflicts"></a>Resolución de conflictos de delegado de UNUserNotificationCenter

*Si ni la aplicación ni una de las bibliotecas de terceros implementa `UNUserNotificationCenterDelegate`, puede pasar por alto esta parte.*

El SDK usa un delegado de `UNUserNotificationCenter` para supervisar el ciclo de vida de las notificaciones de Engagement en dispositivos que se ejecutan en iOS 10 o superior. El SDK tiene su propia implementación del protocolo `UNUserNotificationCenterDelegate`, pero solo puede haber un delegado de `UNUserNotificationCenter` por aplicación. Cualquier otro delegado que se agrega al objeto `UNUserNotificationCenter` entrará en conflicto con Engagement. Si el SDK detecta su delegado o cualquier otro de terceros, no usará su propia implementación para ofrecerle una oportunidad de resolver los conflictos. Tendrá que agregar la lógica de Engagement a su propio delegado con el fin de resolver los conflictos.

Hay dos formas de hacerlo:

Propuesta 1: reenviar las llamadas del delegado al SDK:

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

O propuesta 2: heredándola de la clase `AEUserNotificationHandler`.

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [!NOTE]
> Puede determinar si una notificación proviene de Engagement o no pasando su diccionario `userInfo` al método de clase `isEngagementPushPayload:` del agente.

Asegúrese de que el delegado del objeto `UNUserNotificationCenter` se establece en su delegado en los métodos `application:willFinishLaunchingWithOptions:` o `application:didFinishLaunchingWithOptions:` del delegado de aplicación.
Por ejemplo, si implementa la propuesta anterior 1:

      - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        // Any other code

        [UNUserNotificationCenter currentNotificationCenter].delegate = self;
        return YES;
      }

## <a name="how-to-customize-campaigns"></a>Personalización de las campañas
### <a name="notifications"></a>Notificaciones
Hay dos tipos de notificaciones: las notificaciones del sistema y de aplicaciones.

Las notificaciones del sistema se controlan con iOS y no se pueden personalizar.

Las notificaciones de las aplicaciones se realizan con una vista que se agrega dinámicamente a la ventana de la aplicación actual. Esto se denomina superposición de notificaciones. Las superposiciones de notificaciones son ideales para una integración rápida porque no requieren que modifique cualquier vista de la aplicación.

#### <a name="layout"></a>Diseño
Para modificar la apariencia de las notificaciones de la aplicación, simplemente puede modificar el archivo `AENotificationView.xib` de acuerdo con sus necesidades, siempre y cuando mantenga los valores de las etiquetas y los tipos de las subvistas existentes.

De forma predeterminada, las notificaciones de aplicaciones se presentan en la parte inferior de la pantalla. Si prefiere mostrarlas en la parte superior de la pantalla, edite las `AENotificationView.xib` proporcionadas y cambie la propiedad `AutoSizing` de la vista principal para mantenerla en la parte superior de la supervista.

#### <a name="categories"></a>Categorías
Cuando se modifica el diseño proporcionado, se modifica el aspecto de todas las notificaciones. Las categorías permiten definir varios destinos objetivo (posiblemente comportamientos) para las notificaciones. Las categorías pueden especificarse cuando se crea una campaña de cobertura. Tenga en cuenta que las categorías también permiten personalizar anuncios y sondeos, aspectos que se describen más adelante en este documento.

Para registrar un controlador de categoría para las notificaciones, deberá agregar una llamada una vez inicializado el módulo de cobertura.

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

`myNotifier` debe ser una instancia de un objeto que se ajusta al protocolo `AENotifier`.

Puede implementar los métodos de protocolo por sí mismo o puede volver a implementar la clase existente `AEDefaultNotifier` que ya realiza la mayoría del trabajo.

Por ejemplo, si desea volver a definir la vista de notificación para una categoría específica, puede seguir este ejemplo:

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

En este ejemplo simple de categoría, se supone que tiene un archivo denominado `MyNotificationView.xib` en el paquete de aplicación principal. Si el método no es capaz de encontrar un `.xib`correspondiente, no se mostrará la notificación y Engagement mostrará un mensaje en la consola.

El archivo nib proporcionado debe respetar las siguientes reglas:

* Solo debe contener una vista.
* Las subvistas deben ser de los mismos tipos que las de dentro del archivo nib proporcionado denominado `AENotificationView.xib`
* Las subvistas deben tener las mismas etiquetas que las de dentro del archivo nib proporcionado denominado `AENotificationView.xib`

> [!TIP]
> Simplemente copie el archivo nib proporcionado, denominado `AENotificationView.xib`, y empiece a trabajar desde allí. Pero tenga cuidado, la vista dentro del archivo nib está asociada a la clase `AENotificationView`. Esta clase redefinió el método `layoutSubViews` para mover y cambiar el tamaño de sus subvistas según el contexto. Puede reemplazarla por una `UIView` o su clase de vista personalizada.
>
>

Si necesita una personalización más profunda de las notificaciones (si desea, por ejemplo, cargar la vista directamente desde el código), se recomienda echar un vistazo al código fuente y a la documentación de la clase de `Protocol ReferencesDefaultNotifier` y `AENotifier`.

Tenga en cuenta que puede usar el mismo notificador para varias categorías.

También puede volver a definir el notificador predeterminado de este modo:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>Manejo de notificaciones
Al utilizar la categoría predeterminada, se llama a algunos métodos de ciclo de vida en el objeto `AEReachContent` para mostrar estadísticas y actualizar el estado de la campaña:

* Cuando la notificación se muestra en la aplicación, se llama al método `displayNotification` (que notifica estadísticas) por parte de `AEReachModule` si `handleNotification:` devuelve `YES`.
* Si se descarta la notificación, se llama al método `exitNotification`, se notifica la estadística y ahora se pueden procesar las campañas siguientes.
* Si se hace clic en la notificación, se llama a `actionNotification` , se notifica la estadística y se lleva a cabo la acción asociada.

Si su implementación de `AENotifier` pasa por alto el comportamiento predeterminado, tiene que llamar a estos métodos de ciclo de vida por sí mismo. Los ejemplos siguientes muestran algunos casos donde se omite el comportamiento predeterminado:

* No se extienden `AEDefaultNotifier`, por ejemplo, se implementa el control de categoría desde cero.
* Anuló `prepareNotificationView:forContent:`, asegúrese de asignar al menos `onNotificationActioned` o `onNotificationExited` a uno de sus controles de I.U.

> [!WARNING]
> Si `handleNotification:` produce una excepción, el contenido se elimina y se llama a `drop`, esto se indica en las estadísticas y ahora se pueden procesar las campañas siguientes.
>
>

#### <a name="include-notification-as-part-of-an-existing-view"></a>Incluir notificaciones como parte de una vista existente
Las superposiciones son ideales para una integración rápida, pero a veces pueden no ser convenientes o pueden tener efectos secundarios no deseados.

Si no está satisfecho con el sistema de superposición en algunas de las vistas, puede personalizarlo para estas vistas.

Puede decidir incluir nuestro diseño de notificación en las vistas existentes. Para ello, hay dos estilos de implementación:

1. Agregar la vista de notificación mediante el generador de interfaz

   * Abrir el *generador de la interfaz*
   * Coloque un 320 x 60 (o si se encuentra en un iPad, un 768 x 60) `UIView` en el lugar en el que desee que aparezca la notificación
   * Establezca el valor de etiqueta para esta vista en: **36822491**
2. Agregue la vista de notificación mediante programación. Simplemente agregue el siguiente código cuando se haya inicializado la vista:

       UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
       notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
       [self.view addSubview:notificationView];

La macro `NOTIFICATION_AREA_VIEW_TAG` puede encontrarse en `AEDefaultNotifier.h`.

> [!NOTE]
> El notificador predeterminado detecta automáticamente que el diseño de notificación está incluido en esta vista y no se agregará una superposición para él.
>
>

### <a name="announcements-and-polls"></a>Anuncios y sondeos
#### <a name="layouts"></a>Diseños
Puede modificar los archivos `AEDefaultAnnouncementView.xib` y `AEDefaultPollView.xib` siempre y cuando mantenga los valores y tipos de las etiquetas de las subvistas existentes.

#### <a name="categories"></a>Categorías
##### <a name="alternate-layouts"></a>Diseños alternativos
Como con las notificaciones, la categoría de la campaña puede utilizarse para tener diseños alternativos para los anuncios y sondeos.

Para crear una categoría para un anuncio, debe extender **AEAnnouncementViewController** y registrarlo una vez que se ha inicializado el módulo de cobertura:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [!NOTE]
> Cada vez que un usuario haga clic en una notificación para un anuncio con la categoría "my\_category", el controlador de vista registrado (en ese caso `MyCustomAnnouncementViewController`) se inicializará llamando al método `initWithAnnouncement:` y la vista se agregará a la ventana de la aplicación actual.
>
>

En la implementación de la clase `AEAnnouncementViewController` tendrá que leer la propiedad `announcement` para inicializar sus subvistas. Considere el ejemplo siguiente, donde dos etiquetas se inicializan mediante las propiedades `title` y `body` de la clase `AEReachAnnouncement`:

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

Si no desea cargar las vistas usted mismo pero simplemente desea reutilizar el diseño predeterminado de la vista del anuncio, puede provocar que el controlador de vista personalizada amplíe la clase `AEDefaultAnnouncementViewController`suministrada. En tal caso, duplique el archivo nib `AEDefaultAnnouncementView.xib` y cámbiele el nombre para que pueda ser cargado por el controlador de vista personalizado (para un controlador denominado `CustomAnnouncementViewController`, deberá llamar a su archivo nib `CustomAnnouncementView.xib`).

Para reemplazar la categoría predeterminada de anuncios, simplemente hay que registrar el controlador de vista personalizada para la categoría definida en `kAEReachDefaultCategory`:

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Los sondeos pueden personalizarse de la misma manera:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

Esta vez, el `MyCustomPollViewController` proporcionado debe extender `AEPollViewController`. O bien puede ampliar desde el controlador predeterminado: `AEDefaultPollViewController`.

> [!IMPORTANT]
> No se olvide de llamar a `action` (`submitAnswers:` para los controladores de vista de sondeo personalizado) o al método `exit` antes de que se descarte el controlador de vista. De lo contrario, las estadísticas no se enviará (es decir, ningún análisis de la campaña) y, lo que es más importante, no se notificará a las campañas siguientes hasta que se reinicie el proceso de aplicación.
>
>

##### <a name="implementation-example"></a>Ejemplo de implementación
En esta implementación, la vista de anuncio personalizada se carga desde un archivo xib externo.

Al igual que para la personalización de notificación avanzada, se recomienda mirar el código fuente de la implementación estándar.

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end
