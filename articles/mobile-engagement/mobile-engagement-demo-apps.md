---
title: "Aplicación de demostración de Azure Mobile Engagement | Microsoft Docs"
description: "Explica dónde puede usar la aplicación de demostración de Azure Mobile Engagement, cómo usarla y qué ventajas implica su uso"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: f624d5aa-254b-4ad0-96a3-f00e6c3a2c97
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2016
ms.author: piyushjo
ms.openlocfilehash: 8381edb569e19a85c1259f7791b477cfa6e51ea3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement-demo-app"></a>Aplicación de demostración de Azure Mobile Engagement
Se ha publicado una aplicación de demostración de Azure Mobile Engagement para las plataformas **iOS**, **Android** y **Windows** para ayudarle a encontrar recursos útiles y obtener más información acerca de Mobile Engagement.

La aplicación le ayudará a:

* Encontrar fácilmente vínculos útiles a recursos de Mobile Engagement como vídeos, documentación, foros de soporte técnico y sitios dónde debe ir para generar solicitudes de características.
* Experimentar notificaciones de ejemplo compatibles con Mobile Engagement para obtener ideas para sus propias aplicaciones móviles.
* Use una implementación de referencia para estudiar cómo implementar Mobile Engagement en su propia aplicación. Puede aprender a:
  
  * Recopilar datos de análisis.
  * Implementar escenarios avanzados de notificación del tipo *Pantalla completa intersticial* o *Emergente*.
  * Implementar encuestas y sondeos.
  * Implementar escenarios de inserción silenciosa o inserción de datos.   

## <a name="app-installation"></a>Instalación de la aplicación
Esta aplicación está disponible en las siguientes tiendas de aplicaciones:

* **Aplicación de demostración universal de Windows**:
  
  * Descargue la aplicación en la [Tienda de aplicaciones Windows](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2).
  * La aplicación se desarrolló como una aplicación universal de Windows 10. El código fuente está disponible en [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows).
* **Aplicación de demostración de iOS**:
  
  * Descargue la aplicación en la [Tienda Apple](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090).
  * La aplicación se desarrolló en iOS Swift. El código fuente está disponible en [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios).
* **Aplicación de demostración de Android**:
  
  * Descargue la aplicación en [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement).
  * El código fuente está disponible en [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android).

![Aplicación de demostración universal de Windows][1]

![aplicación de demostración de iOS][2]
![aplicación de demostración de Android][3]

## <a name="usage"></a>Uso
Puede usar esta aplicación de las siguientes maneras:

**Descargue las aplicaciones en el dispositivo mediante los vínculos de las tiendas de aplicaciones (proporcionados anteriormente):**

> [!IMPORTANT]
> No necesita ninguna cuenta de Azure ni conectar la aplicación a un back-end. La aplicación funciona de forma independiente.
> 
> 

* Una vez que tenga la aplicación en el dispositivo podrá recorrer los vínculos en el menú de la izquierda para buscar los recursos útiles acerca de Mobile Engagement.
* Hemos agregado la [fuente RSS del servicio](https://aka.ms/azmerssfeed) a esta aplicación para que siempre esté al corriente de las actualizaciones de producto más recientes.
* También puede ir a través de los escenarios de notificación de ejemplo para experimentar el tipo de notificaciones compatibles con Mobile Engagement para cada plataforma. Estas notificaciones se pueden experimentar localmente, es decir, puede hacer clic en los botones de las pantallas para que le muestren una experiencia de las notificaciones que es idéntica al envío de notificaciones desde la plataforma de Mobile Engagement.

![Menú de aplicación para Windows][4]

![Menú de aplicación para iOS][5]
![Menú de la aplicación para Android][6]

**Descargue el código fuente mediante los vínculos de GitHub (proporcionados anteriormente):**

* Una vez que haya descargado el código fuente, puede abrirlo en los respectivos entornos de desarrollo, XCode en el caso de iOS, Android Studio para Android y Visual Studio para Windows.
* A continuación debe seguir los [pasos de integración básicos de SDK](mobile-engagement-windows-store-dotnet-get-started.md) para que pueda conectar esta aplicación con su propia instancia de back-end de Mobile Engagement.
  * Debe configurar una cadena de conexión en la aplicación.
  * También debe configurar la plataforma de notificaciones push de la aplicación.
* Observará que la propia aplicación cuenta con Mobile Engagement. Por lo tanto, cuando se abre la aplicación después de conectarse al back-end, podrá ver la sesión de usuario, las actividades, los eventos etc., en la pestaña **Monitor** .
* También podrá enviar notificaciones a esta aplicación desde su propia instancia de Mobile Engagement en lugar de usar notificaciones locales.
  
  * Aquí puede agregar el dispositivo como un dispositivo de prueba mediante el elemento de menú **Get the Device ID** (Obtener el id. de dispositivo) en la aplicación. Esto le proporciona un id. de dispositivo que, a continuación, registra como un dispositivo de prueba con la instancia de back-end de la plataforma.
    
    ![Id. de dispositivo en Windows][7]
    
    ![Id. de dispositivo en iOS][8]
    ![Id. de dispositivo en Android][9]

## <a name="key-features-of-the-demo-app"></a>Características principales de la aplicación de demostración
* Como se mencionó anteriormente, con esta aplicación, tendrá los recursos principales de Mobile Engagement a mano. Puede recorrer los vínculos en el menú de la izquierda.
* Puede experimentar notificaciones fuera de aplicación para cada plataforma. Estas notificaciones se pueden enviar como **solo notificación** en cuyo caso, al hacer clic en la notificación, simplemente se abrirá una pantalla nativa de la aplicación (mediante **vinculación en profundidad**) o como **anuncio web**, en cuyo caso podrá enviar contenido HTML adicional desde el back-end de Mobile Engagement que aparece cuando se hace clic en la notificación.
  
    ![Notificaciones fuera de la aplicación][29]
* En iOS, tendrá que cerrar la aplicación para ver las notificaciones push fuera de la aplicación o sistema. Puede ver aquí la implementación para agregar **botones de acción** como los que se han agregado a esta notificación fuera de la aplicación para recibir *comentarios* y *compartir* de forma que el usuario pueda realizar acciones directamente desde la notificación.
  
    ![Notificaciones fuera de la aplicación en iOS][11] ![Visualización de notificaciones fuera de la aplicación en iOS][14]
* En Android, las opciones admitidas son la adición de un texto de varias líneas (**Texto grande**) o la adición de una imagen en la notificación (**Imagen grande**), junto con los **botones de acción** (tal y como se admiten en iOS).
  
    ![Notificaciones fuera de la aplicación en Android][12] ![Visualización de notificaciones fuera de la aplicación en Android][15]
* En Windows 10, puede ver qué aspecto tendrán las notificaciones en el equipo. Esta notificación también se mostrará en el **Centro de notificaciones**de Windows 10. De momento, no se pueden agregar **botones de acción** con el SDK de Windows.
  
    ![Notificaciones fuera de la aplicación en Windows][10] ![Visualización fuera de la aplicación en Windows][13]
* Puede probar las notificaciones en aplicación para cada plataforma. Se trata de una experiencia de dos pasos donde se muestra primera una ventana de **notificación** . Al hacer clic en ella, se abre un **anuncio**de pantalla completa, como se muestra en la captura de pantalla siguiente. El contenido de este anuncio procede de la instancia de back-end de Mobile Engagement. El SDK tiene las plantillas para las notificaciones y los anuncios. Puede personalizarlas fácilmente, tal como se muestra en esta aplicación de demostración con la adición de nuestro logotipo y el color.  
  
    ![Notificaciones en aplicación en Windows][16]
  
    ![Notificaciones en aplicación en iOS][17]  ![Notificaciones en aplicación en Android][18]
  
    **iOS**, **Android**
* También puede utilizar la característica **Categoría** de Mobile Engagement para personalizar esta experiencia predeterminada. En la aplicación de demostración, hemos mostrado dos formas habituales de cambiar la experiencia de las notificaciones. Tenga en cuenta que la característica Categoría no se ha admitido en el SDK de Windows.
  
    **Pantalla completa intersticial:**
  
    ![Notificación en aplicación: categoría intersticial][30]
  
    ![Categoría intersticial en iOS][21]     ![Categoría intersticial en Android][22]
  
    **Notificación emergente:**
  
    ![Notificación en aplicación: categoría emergente][31]
  
    ![Notificación emergente en iOS][19]    ![Notificación emergente en Android][20]

**iOS**, **Android**

* Mobile Engagement también admite un tipo especializado de notificación de la aplicación denominada **Sondeos**. Esto le permite enviar encuestas rápidas a los usuarios segmentados de la aplicación. Puede agregar preguntas y opciones para cada pregunta como en la captura de pantalla siguiente. Estas se mostrarán como una notificación en la aplicación para el usuario de la aplicación.   
  
    ![Notificaciones de sondeo][32]
  
    ![Encuesta sobre Windows][26]
  
    ![Encuesta sobre iOS][27]   ![Encuesta sobre Android][28]

**iOS**, **Android**

* Mobile Engagement también admite el envío de notificaciones de **inserción de datos** silenciosas. Con estas notificaciones, puede enviar algunos datos de su servicio (como los datos JSON del ejemplo siguiente), que puede controlar en la aplicación y en los que puede realizar alguna acción. Un ejemplo es cómo estamos cambiando el precio de un artículo selectivamente mediante las notificaciones push de datos.
  
    ![Notificación push de datos][33]
  
    ![Notificación push de datos en Windows][23]
  
    ![Notificación push de datos en iOS][24]  ![Notificación push de datos en Android][25]

**iOS**, **Android**

> [!NOTE]
> Puede ver instrucciones por pasos detalladas para cualquiera de estas notificaciones haciendo clic en **Haga clic aquí para obtener instrucciones sobre cómo enviar estas notificaciones desde la plataforma Mobile Engagement** en cualquier pantalla de ejemplo de notificación.
> 
> 

[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
