---
title: "Introducción a Azure Mobile Engagement para la implementación de Unity para iOS"
description: "Aprenda a usar Azure Mobile Engagement con los análisis y las notificaciones push para aplicaciones de Unity que se implementan en dispositivos de iOS."
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: 
ms.assetid: 7ddfbac3-8d13-4ebe-b061-c865f357297f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: c8f50404771965ec636065346ac04e059d264c3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-unity-ios-deployment"></a>Introducción a Azure Mobile Engagement para la implementación de Unity para iOS
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

En este tema se muestra cómo usar Azure Mobile Engagement para comprender el uso de su aplicación y cómo enviar notificaciones push a usuarios segmentados de una aplicación de Unity durante la implementación en un dispositivo de iOS.
Este tutorial utiliza el tutorial de iniciación clásico de Unity como punto de partida. Debe seguir los pasos de este [tutorial](mobile-engagement-unity-roll-a-ball.md) antes de continuar con la integración de Mobile Engagement que se muestra en el tutorial siguiente. 

Este tutorial requiere lo siguiente:

* [Editor de Unity](http://unity3d.com/get-unity)
* [SDK de Unity para Mobile Engagement](https://aka.ms/azmeunitysdk)
* Editor de XCode

> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started).
> 
> 

## <a id="setup-azme"></a>Configuración de Mobile Engagement para una aplicación iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Conectar la aplicación al backend de Mobile Engagement
### <a name="import-the-unity-package"></a>Importación del paquete de Unity
1. Descargue el [paquete de Unity para Mobile Engagement](https://aka.ms/azmeunitysdk) y guárdelo en el equipo local. 
2. Vaya a **Assets -> Import Package -> Custom Package** (Recursos -> Importar paquete -> Paquete personalizado) y seleccione el paquete que descargó en el paso anterior. 
   
    ![][70] 
3. Asegúrese de que se seleccionan todos los archivos y haga clic en el botón **Import** (Importar). 
   
    ![][71] 
4. Una vez que la importación termina correctamente, verá los archivos del SDK importados en el proyecto.  
   
    ![][72] 

### <a name="update-the-engagementconfiguration"></a>Actualización de EngagementConfiguration
1. Abra el archivo de script **EngagementConfiguration** desde la carpeta del SDK y actualice **IOS\_CONNECTION\_STRING** con la cadena de conexión que obtuvo anteriormente en Azure Portal.  
   
    ![][73]
2. Guarde el archivo . 

### <a name="configure-the-app-for-basic-tracking"></a>Configuración de la aplicación para un seguimiento básico
1. Abra el script **Playercontroller** asociado al objeto Player para su edición. 
2. Agregue la siguiente instrucción using:
   
        using Microsoft.Azure.Engagement.Unity;
3. Agregue lo siguiente al método `Start()`.
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>Implementación y ejecución de la aplicación
1. Conecte un dispositivo iOS al equipo. 
2. Abra **File -> Build Settings** (Archivo -> Configuración de compilación). 
   
    ![][40]
3. Seleccione **iOS** y, después, haga clic en **Switch Platform** (Cambiar plataforma).
   
    ![][41]
   
    ![][42]
4. Haga clic en **Player settings** (Configuración del Reproductor) y proporcione un identificador de agrupación de trabajos válido. 
   
    ![][53]
5. Por último, haga clic en **Build And Run**
   
    ![][54]
6. Se le pedirá que proporcione un nombre de carpeta para almacenar el paquete de iOS. 
   
    ![][43]
7. Si todo va bien, se compilará el proyecto y se abrirá en la aplicación de XCode. 
8. Asegúrese de que el **identificador de la agrupación de trabajos** es correcto en el proyecto.  
   
    ![][75]
9. Ejecute la aplicación en XCode para que el paquete se implemente en su dispositivo conectado y con eso debería ver ya el juego de Unity en el teléfono. 

## <a id="monitor"></a>Conexión de la aplicación con la supervisión en tiempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Habilitación de las notificaciones push y la mensajería en aplicación
Mobile Engagement permite interactuar y llegar mediante notificaciones push y mensajería en la aplicación en el contexto de las campañas. Este módulo se denomina REACH en el portal de Mobile Engagement.
No tiene que realizar ninguna configuración adicional en la aplicación para recibir notificaciones ya que ya está configurado para ello.

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png
