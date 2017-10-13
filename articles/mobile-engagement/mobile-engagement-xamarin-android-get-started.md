---
title: "Introducción a Azure Mobile Engagement para aplicaciones Xamarin.Android"
description: "Aprenda a usar Azure Mobile Engagement con los análisis y las notificaciones push para aplicaciones Xamarin.Android."
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: 
ms.assetid: fb68cf98-08a2-41b5-8e59-757469de3fe7
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/16/2016
ms.author: piyushjo
ms.openlocfilehash: 7b3d01b32c2d5a40448fc22861cd45f612238f2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>Introducción a Azure Mobile Engagement para aplicaciones Xamarin.Android
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

En este tema se muestra cómo usar Azure Mobile Engagement para comprender el uso de su aplicación y cómo enviar notificaciones push a usuarios segmentados de una aplicación Xamarin.Android.
En este tutorial se demuestra el escenario de difusión sencillo con Mobile Engagement. En él, creará una aplicación vacía de Xamarin.Android que recopile datos básicos y reciba notificaciones push con Google Cloud Messaging (GCM).

> [!NOTE]
> El servicio Azure Mobile Engagement se retirará en marzo de 2018 y actualmente solo está disponible para los clientes existentes. Para más información, consulte [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Este tutorial requiere lo siguiente:

* [Xamarin Studio](http://xamarin.com/studio). También puede utilizar Visual Studio con Xamarin, pero en este tutorial se usa Xamarin Studio. Consulte [Configuración e instalación](https://msdn.microsoft.com/library/mt613162.aspx)para obtener instrucciones.
* [SDK de Mobile Engagement Xamarin](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).
> 
> 

## <a id="setup-azme"></a>Configuración de Mobile Engagement para una aplicación Android
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Conectar la aplicación al backend de Mobile Engagement
En este tutorial se presenta una "integración básica", que es el conjunto mínimo necesario para recopilar los datos y enviar una notificación de inserción. 

Crearemos una aplicación básica con Xamarin Studio para demostrar la integración.

### <a name="create-a-new-xamarinandroid-project"></a>Creación de un nuevo proyecto de Xamarin.Android
1. Inicie **Xamarin Studio**, vaya a **File** -> **New** -> **Solution** (Archivo -> Nuevo -> Solución). 
   
    ![][1]
2. Seleccione **Android App** (Aplicación Android), asegúrese de que el lenguaje seleccionado sea **C#** y haga clic en **Next** (Siguiente).
   
    ![][2]
3. Rellene los valores de **App Name** (Nombre de la aplicación) y **Organization Identifier** (Identificador de organización). Asegúrese de marcar **Google Play Services** y luego haga clic en **Next** (Siguiente). 
   
    ![][3]
4. Actualice los valores de **Project Name** (Nombre de proyecto), **Solution Name** (Nombre de solución) y **Location** (Ubicación) si es necesario y haga clic en **Create** (Crear).
   
    ![][4]

Xamarin Studio crea la aplicación en la que se integrará Mobile Engagement. 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Conectar la aplicación al back-end de Mobile Engagement
1. Haga clic con el botón derecho en la carpeta **Packages** de las ventanas de la solución y seleccione **Add Packages...** (Agregar paquetes).
   
    ![][5]
2. Busque el **SDK de Xamarin para Microsoft Azure Mobile Engagement** y agréguelo a la solución.  
   
    ![][6]
3. Abra **MainActivity.cs** y agregue las siguientes instrucciones using:
   
        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;
4. En el método `OnCreate` , agregue el siguiente código para inicializar la conexión con el back-end de Mobile Engagement. Asegúrese de agregar su elemento **ConnectionString**. 
   
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

### <a name="add-permissions-and-a-service-declaration"></a>Agregar permisos y una declaración de servicio
1. Abra el archivo **Manifest.xml** de la carpeta Properties (Propiedades). Seleccione la pestaña Source (Origen) para actualizar directamente el origen XML.
2. Agregue estos permisos al archivo Manifest.xml (que se puede encontrar en la carpeta **Properties**) de su proyecto inmediatamente antes o después de la etiqueta `<application>`:
   
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
3. Agregue lo siguiente entre las etiquetas `<application>` y `</application>` para declarar el servicio del agente:
   
        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
4. En el código que acaba de pegar, reemplace `"<Your application name>"` en la etiqueta. Esto es lo que se muestra en el menú **Configuración** donde los usuarios pueden ver los servicios que se están ejecutando en el dispositivo. Puede agregar la palabra "Servicio" a la etiqueta, por ejemplo.

### <a name="send-a-screen-to-mobile-engagement"></a>Enviar una pantalla a Mobile Engagement
Para comenzar a enviar datos y asegurarse de que los usuarios estén activos, debe enviar al menos una pantalla al back-end de Mobile Engagement. Para ello, asegúrese de que `MainActivity` hereda de `EngagementActivity`, en lugar de `Activity`.

    public class MainActivity : EngagementActivity

Como alternativa, si no puede heredar de `EngagementActivity`, deberá agregar los métodos `.StartActivity` y `.EndActivity` en `OnResume` y `OnPause`, respectivamente.  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }

            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

## <a id="monitor"></a>Conexión de la aplicación con la supervisión en tiempo real
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Habilitar las notificaciones push y la mensajería en la aplicación
Mobile Engagement permite interactuar y llegar por REACH a los usuarios mediante notificaciones de inserción y mensajería en la aplicación en el contexto de las campañas. Este módulo se denomina REACH en el portal de Mobile Engagement.
En las secciones siguientes se instala la aplicación para recibirlos.

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png
