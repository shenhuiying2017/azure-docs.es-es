---
title: "Introducción a Azure Mobile Engagement para iOS en Swift | Microsoft Docs"
description: "Aprenda a usar Azure Mobile Engagement con los análisis y las notificaciones de inserción para aplicaciones iOS."
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 196c282d-6f2f-4cbc-aeee-6517c5ad866d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: swift
ms.topic: hero-article
ms.date: 09/20/2016
ms.author: piyushjo
ms.openlocfilehash: 1011b9823333e79a52cd2d187df4f8d063b1f799
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>Introducción a Azure Mobile Engagement para aplicaciones iOS en Swift
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

En este tema se muestra cómo utilizar Azure Mobile Engagement para conocer el uso de las aplicaciones y enviar notificaciones push a usuarios segmentados en una aplicación de iOS.
En este tutorial, puede crear una aplicación iOS vacía que recopile datos básicos y reciba notificaciones push mediante el sistema de notificaciones push de Apple (APN).

Este tutorial requiere lo siguiente:

* XCode 8, que se puede instalar desde la tienda de aplicaciones para Mac
* [SDK de Mobile Engagement iOS]
* Certificado de notificaciones push (.p12), que puede obtener en el centro de desarrolladores de Apple.

> [!NOTE]
> En este tutorial se usa Swift, versión 3.0. 
> 
> 

Completar este tutorial es un requisito previo para todos los tutoriales de Mobile Engagement para aplicaciones iOS.

> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started).
> 
> 

## <a id="setup-azme"></a>Configuración de Mobile Engagement para una aplicación iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Conectar la aplicación al backend de Mobile Engagement
En este tutorial se presenta una "integración básica", que es el conjunto mínimo necesario para recopilar los datos y enviar una notificación de inserción. Toda la información sobre la integración se encuentra en la [Integración del SDK de Mobile Engagement para iOS](mobile-engagement-ios-sdk-overview.md)

Crearemos una aplicación básica con XCode para demostrar la integración:

### <a name="create-a-new-ios-project"></a>Creación de un nuevo proyecto iOS
[!INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Conectar la aplicación al back-end de Mobile Engagement
1. Descargue el [SDK de Mobile Engagement iOS]
2. Extraiga el archivo .tar.gz archivo en una carpeta del equipo.
3. Haga clic con el botón derecho del mouse en el proyecto y elija "Agregar archivos a...".
   
    ![][1]
4. Navegue hasta la carpeta donde extrajo el SDK y seleccione la carpeta `EngagementSDK` y, luego, haga clic en Aceptar.
   
    ![][2]
5. Abra la ficha `Build Phases` y en el menú `Link Binary With Libraries` agregue los marcos tal y como se muestra a continuación:
   
    ![][3]
6. Cree un encabezado puente para poder usar las API de Objective-C de SDK mediante la selección de Archivo > Nuevo > Archivo > iOS > Origen > Archivo de encabezado.
   
    ![][4]
7. Edite el archivo de encabezado puente para exponer el código Mobile Engagement Objective-C en el código Swift, agregue las importaciones siguientes:
   
        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"
8. En Configuración de compilación, asegúrese de que la compilación del encabezado puente Objective-C en Compilador de Swift - Generación de código tiene una ruta de acceso a este encabezado. Este es un ejemplo de ruta de acceso: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (en función de la ruta de acceso)**
   
   ![][6]
9. Vuelva al portal de Azure en la página *Información de conexión* de la aplicación y copie la cadena de conexión
   
   ![][5]
10. Pegue la cadena de conexión en el delegado `didFinishLaunchingWithOptions`
    
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
              [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
              [...]
        }

## <a id="monitor"></a>Habilitar supervisión en tiempo real
Para comenzar a enviar datos y asegurarse de que los usuarios estén activos, debe enviar al menos una pantalla (Actividad) al back-end de Mobile Engagement.

1. Abra el archivo **ViewController.swift** y reemplace la clase base de **ViewController** para que sea **EngagementViewController**:
   
    `class ViewController : EngagementViewController {`

## <a id="monitor"></a>Conexión de la aplicación con la supervisión en tiempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Habilitación de las notificaciones de inserción y mensajería en la aplicación
Mobile Engagement permite interactuar y llegar a los usuarios mediante notificaciones de inserción y mensajería en la aplicación en el contexto de las campañas. Este módulo se denomina REACH en el portal de Mobile Engagement.
En las secciones siguientes se instalará la aplicación para recibirlos.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Habilitación de la aplicación para recibir notificaciones push silenciosas
[!INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Adición de la biblioteca de cobertura al proyecto
1. Haga clic con el botón derecho del mouse en el proyecto.
2. Seleccionar `Add file to ...`
3. Navegue hasta la carpeta donde extrajo el SDK.
4. Seleccione la carpeta `EngagementReach`
5. Haga clic en Agregar
6. Edite el archivo de encabezado puente para exponer encabezados Mobile Engagement Objective-C Reach y agregue las importaciones siguientes:
   
        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### <a name="modify-your-application-delegate"></a>Modifique su delegado de la aplicación
1. En el método `didFinishLaunchingWithOptions`, cree un módulo de cobertura y páselo a la línea de inicialización existente de Engagement:
   
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

### <a name="enable-your-app-to-receive-apns-push-notifications"></a>Habilite su aplicación para recibir notificaciones push de APN.
1. Agregue la siguiente línea al método `didFinishLaunchingWithOptions`:
   
        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }
2. Agregue el método `didRegisterForRemoteNotificationsWithDeviceToken` de la siguiente forma:
   
        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }
3. Agregue el método `didReceiveRemoteNotification:fetchCompletionHandler:` de la siguiente forma:
   
        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[SDK de Mobile Engagement iOS]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png
