---
title: Envío de notificaciones push a aplicaciones iOS mediante Azure Notification Hubs | Microsoft Docs
description: En este tutorial aprenderá a usar Azure Notification Hubs para enviar notificaciones push a una aplicación iOS.
services: notification-hubs
documentationcenter: ios
keywords: notificación push,notificaciones push,notificaciones push de ios
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 083b0c956055ab5b54a4af2eec57f096613cbe65
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Tutorial: Envío de notificaciones push a aplicaciones iOS mediante Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

En este tutorial se usa Azure Notification Hubs para enviar notificaciones push a una aplicación iOS. Cree una aplicación iOS vacía que reciba notificaciones push mediante [Apple Push Notification Service (APN)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1). 

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Generación del archivo de solicitud de firma de certificado
> * Solicite en su aplicación notificaciones push
> * Creación de un perfil de aprovisionamiento para la aplicación
> * Configuración de un Centro de notificaciones para notificaciones push de iOS
> * Conexión de la aplicación iOS a Notification Hubs
> * Prueba de envío de las notificaciones push
> * Compruebe que la aplicación recibe notificaciones

El código completo de este tutorial se puede encontrar [en GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

## <a name="prerequisites"></a>requisitos previos

- Una cuenta de Azure activa. En caso de no tenerla, puede crear una [cuenta de evaluación gratuita](https://azure.microsoft.com/free) en tan solo unos minutos. 
- [Microsoft Azure Messaging Framework]
- La versión más reciente de [Xcode]
- Un dispositivo compatible con iOS 10 (o una versión posterior)
- [programa para desarrolladores de Apple](https://developer.apple.com/programs/)
  
  > [!NOTE]
  > Debido a los requisitos de configuración de las notificaciones push, debe implementar y probar estas en un dispositivo iOs físico (iPhone o iPad) en lugar de en el simulador de iOS.
  
La realización de este tutorial es un requisito previo para todos los demás tutoriales de Notification Hubs para aplicaciones iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Configuración del Centro de notificaciones para notificaciones push de iOS
En esta sección se crea un Centro de notificaciones y se configura la autenticación con APNS mediante el certificado push **.p12** que creó anteriormente. Si desea usar un centro de notificaciones ya creado, puede omitir los pasos hasta el paso 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-your-notification-hub-with-apns-information"></a>Configuración de un Centro de notificaciones con información de APNS

1. En **Notification Services** (Servicios de notificación), seleccione **Apple (APNS)**. 
2. Seleccione **Certificado**.
3. Seleccione el **icono del archivo**.
4. Seleccione el archivo **.p12** que exportó antes.
5. Especifique la **contraseña** correcta.
6. Seleccione el modo **Espacio aislado**. Use el modo **Producción** únicamente si desea enviar notificaciones de inserción a los usuarios que compraron la aplicación en la tienda.

    ![Configuración del certificado de APNs en Azure Portal][7]

Su centro de notificaciones ya está configurado con APNs, y tiene las cadenas de conexión para registrar su aplicación y enviar notificaciones push.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Conexión de la aplicación iOS a Notification Hubs
1. En XCode, cree un nuevo proyecto iOS y seleccione la plantilla **Single View Application** (Aplicación de vista sencilla).
   
    ![Xcode: aplicación de vista única][8]
    
2. Al configurar las opciones para su nuevo proyecto, asegúrese de usar el **nombre de producto** y el **identificador de organización** que usó al establecer el identificador de conjunto en el portal de desarrollo de Apple.
   
    ![Xcode: opciones de proyecto][11]
    
3. En Project Navigator (Explorador de proyectos), haga clic en el nombre del proyecto, luego en la pestaña **General** y busque **Signing** (Firma). Asegúrese de seleccionar el equipo adecuado para la cuenta de desarrollador de Apple. XCode debe desplegar automáticamente el perfil de aprovisionamiento que creó anteriormente según en el identificador del conjunto.
   
    Si no ve el nuevo perfil de aprovisionamiento que creó en Xcode, intente actualizar los perfiles de la identidad de firma. Haga clic en **Xcode** en la barra de menús, en **Preferences** (Preferencias), en la pestaña **Account** (Cuenta), en el botón **View Details** (Ver detalles), en la identidad de firma y, por último, en el botón Refresh (Actualizar) en la esquina inferior derecha.

    ![Xcode: perfil de aprovisionamiento][9]

4. Seleccione la pestaña **Funcionalidades** y asegúrese de habilitar Notificaciones push.

    ![Xcode: funcionalidades push][12]
   
5. Descargue [Microsoft Azure Messaging Framework] y descomprima el archivo. En XCode, haga clic con el botón derecho en el proyecto y haga clic en la opción **Add Files to** (Agregar archivos a) para agregar la carpeta **WindowsAzureMessaging.framework** al proyecto de XCode. Seleccione **Options** (Opciones) y asegúrese de que **Copy items if needed** (Copiar elementos si es necesario) esté seleccionado. A continuación, haga clic en **Add** (Agregar).

    ![Descompresión del SDK de Azure][10]

6. Agregue un nuevo archivo de encabezado al proyecto denominado **HubInfo.h**. Este archivo contiene las constantes del Centro de notificaciones. Agregue las siguientes definiciones y reemplace los marcadores de posición de literal de cadena por su *nombre del centro* y el valor de *DefaultListenSharedAccessSignature* que anotó anteriormente.

    ```obj-c
        #ifndef HubInfo_h
        #define HubInfo_h
   
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
   
        #endif /* HubInfo_h */
    ```
    
7. Abra el archivo **AppDelegate.h** y agregue las siguientes directivas de importación:

    ```obj-c
        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
        #import <UserNotifications/UserNotifications.h> 
        #import "HubInfo.h"
    ```
8. En su **archivo AppDelegate.m**, agregue el código siguiente en el método **didFinishLaunchingWithOptions**, en función de su versión de iOS. Este código registra el identificador de dispositivo en APNS:

    ```obj-c
        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
            UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];
   
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```
   
9. En el mismo archivo, agregue los siguientes métodos:

    ```obj-c
         - (void) application:(UIApplication *) application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
           SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];
   
            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
               if (error != nil) {
                   NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                   [self MessageBox:@"Registration Status" message:@"Registered"];
              }
          }];
         }
   
        -(void)MessageBox:(NSString *) title message:(NSString *)messageText
        {
         UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }
    ```

    Este código se conecta al centro de notificaciones usando la información de conexión especificada en HubInfo.h. Luego, proporciona el token del dispositivo al centro de notificaciones para que este pueda enviar notificaciones.

10. En el mismo archivo, agregue el siguiente método para mostrar una **UIAlert** si la notificación se recibe mientras la aplicación está activa:

    ```obj-c
            - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
               NSLog(@"%@", userInfo);
               [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
           }
    ```

11. Para asegurarse de que no haya errores, compile y ejecute la aplicación en el dispositivo.

## <a name="send-test-push-notifications"></a>Prueba de envío de las notificaciones push
Puede probar de recibir notificaciones en la aplicación con la opción *Envío de prueba* en [Azure Portal]. Envía una notificación push de prueba al dispositivo.

![Azure Portal: envío de prueba][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="verify-that-your-app-receives-push-notifications"></a>Compruebe que la aplicación recibe notificaciones push
Para probar las notificaciones push en iOS, debe implementar la aplicación en un dispositivo iOS físico. No puede enviar notificaciones push de Apple con el simulador de iOS.

1. Ejecute la aplicación y compruebe que el registro se realiza correctamente, luego presione **OK**(Aceptar).
   
    ![Prueba de registro de notificación push de aplicación iOS][33]
2. Luego, envío una notificación push de prueba desde [Azure Portal], como se ha descrito en la sección anterior. 

3. La notificación push se envía a todos los dispositivos registrados para recibir las notificaciones desde el Centro de notificaciones concreto.
   
    ![Prueba de recepción de notificación push de aplicación iOS][35]

## <a name="next-steps"></a>Pasos siguientes
En este sencillo ejemplo, se difunden notificaciones push a todos los dispositivos iOS registrados. Para aprender a enviar notificaciones push a dispositivos iOS específicos, pase al siguiente tutorial: 

> [!div class="nextstepaction"]
>[Envío de notificaciones push a dispositivos específicos](notification-hubs-ios-xplat-segmented-apns-push-notification.md)


<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Microsoft Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure Portal]: https://portal.azure.com
