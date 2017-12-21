---
title: "Introducción a Azure Mobile Engagement para iOS en Objective C | Microsoft Docs"
description: "Aprenda a usar Azure Mobile Engagement con los análisis y las notificaciones push en aplicaciones iOS."
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 117b5742-522b-41de-98c5-f432da2d98f8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 913a60df4ce7c431b1c260135785972aac00c69d
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>Introducción a Azure Mobile Engagement para aplicaciones iOS en Objective C
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

En este tema se muestra cómo utilizar Azure Mobile Engagement para conocer el uso de las aplicaciones y enviar notificaciones push a usuarios segmentados en una aplicación de iOS.
En este tutorial, puede crear una aplicación iOS vacía que recopile datos básicos y reciba notificaciones push mediante el sistema de notificaciones push de Apple (APN).

Este tutorial requiere lo siguiente:

* XCode 8, que se puede instalar desde la tienda de aplicaciones para Mac
* [SDK de iOS para Mobile Engagement]

Completar este tutorial es un requisito previo para todos los tutoriales de Mobile Engagement para aplicaciones iOS.

> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started).
>
>

## <a id="setup-azme"></a>Configuración de Mobile Engagement para una aplicación iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Conectar la aplicación al backend de Mobile Engagement
En este tutorial se presenta una "integración básica", que es el conjunto mínimo necesario para recopilar los datos y enviar una notificación de inserción. Toda la información sobre la integración se encuentra en la [Integración del SDK de Mobile Engagement para iOS](mobile-engagement-ios-sdk-overview.md)

Crearemos una aplicación básica con XCode para demostrar la integración:

### <a name="create-a-new-ios-project"></a>Creación de un nuevo proyecto iOS
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Conectar la aplicación al backend de Mobile Engagement
1. Descargue el [SDK de iOS para Mobile Engagement].
2. Extraiga el archivo .tar.gz en una carpeta del equipo.
3. Haga clic con el botón derecho en el proyecto y luego seleccione **Agregar archivos a**.

    ![][1]

4. Navegue hasta la carpeta en la que extrajo el SDK, seleccione la carpeta `EngagementSDK`, haga clic en **Options** (Opciones) en la esquina inferior izquierda y asegúrese de que tanto la casilla **Copy items if needed** (Copiar elementos si fuera necesario) y la casilla del destino están activadas y presione **Aceptar**.

    ![][2]

5. Abra la pestaña **Generar fases** y, en el menú **Vincular binario con bibliotecas**, agregue los marcos de trabajo como se muestra a continuación:

    ![][3]

6. Vuelva al Portal de Azure en la página **Información de conexión** de la aplicación y copie la cadena de conexión.

    ![](../../includes/media/mobile-engagement-create-app-in-portal-new/app-connection-info.png)
7. Agregue la siguiente línea de código al archivo **AppDelegate.m** .

    ```obj-c
    #import "EngagementAgent.h"
    ```
8. Pegue la cadena de conexión en el delegado `didFinishLaunchingWithOptions` .

    ```obj-c
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
            [...]   
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
            [...]
    }
    ```

9. `setTestLogEnabled` es una instrucción opcional que habilita los registros del SDK para identificar problemas.

## <a id="monitor"></a>Habilitación de la supervisión en tiempo real
Para comenzar a enviar datos y asegurarse de que los usuarios estén activos, debe enviar al menos una pantalla (Actividad) al back-end de Mobile Engagement.

1. Abra el archivo **ViewController.h** e importe **EngagementViewController.h**:

    ```obj-c
    #import "EngagementViewController.h"
    ```

2. Reemplace ahora la superclase de la interfaz de **ViewController** por `EngagementViewController`:
 
    ```obj-c
   @interface ViewController : EngagementViewController
   ```

## <a id="monitor"></a>Conexión de la aplicación con la supervisión en tiempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Habilitación de las notificaciones push y la mensajería en aplicación
Mobile Engagement permite interactuar y llegar mediante notificaciones push y mensajería en la aplicación en el contexto de las campañas. Este módulo se denomina REACH en el portal de Mobile Engagement.
En las secciones siguientes se instala la aplicación para recibirlos.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Habilitación de la aplicación para recibir notificaciones push silenciosas
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Adición de la biblioteca de cobertura al proyecto
1. Haga clic con el botón derecho en el proyecto.
2. Seleccione **Agregar archivo a**.
3. Navegue hasta la carpeta donde extrajo el SDK.
4. Seleccione la carpeta `EngagementReach` .
5. Haga clic en **Agregar**.

### <a name="modify-your-application-delegate"></a>Modifique su delegado de la aplicación
1. En el archivo **AppDeletegate.m** , importe el módulo Engagement Reach.

    ```obj-c
    #import "AEReachModule.h"
    #import <UserNotifications/UserNotifications.h>
    ```

2. Dentro del método `application:didFinishLaunchingWithOptions` , cree un módulo de Reach y páselo a la línea de inicialización de Engagement existente:

    ```obj-c
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
        [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
        [...]
        return YES;
    }
    ```

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>Habilite su aplicación para recibir notificaciones push de APN.
1. Agregue la siguiente línea al método `application:didFinishLaunchingWithOptions`:

    ```obj-c
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
    ```
2. Agregue el método `application:didRegisterForRemoteNotificationsWithDeviceToken` de la siguiente forma:

    ```obj-c
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
            [[EngagementAgent shared] registerDeviceToken:deviceToken];
        NSLog(@"Registered Token: %@", deviceToken);
    }
    ```

3. Agregue el método `didFailToRegisterForRemoteNotificationsWithError` de la siguiente forma:

    ```obj-c
    - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
    {
        NSLog(@"Failed to get token, error: %@", error);
    }
    ```

4. Agregue el método `didReceiveRemoteNotification:fetchCompletionHandler` de la siguiente forma:

    ```obj-c
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }
    ```
    
[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[SDK de iOS para Mobile Engagement]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
