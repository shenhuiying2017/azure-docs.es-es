---
title: "Informes de ubicación para el SDK de Android para Azure Mobile Engagement"
description: "Describe cómo configurar los informes de ubicación para el SDK de Android para Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 6cab5ed1-b767-46ac-9f0b-48a4e249d88c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 777d5719cce505b55dfb61c91dcac7e713b077a9
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Informes de ubicación para el SDK de Android para Azure Mobile Engagement
> [!div class="op_single_selector"]
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Este tema describe cómo realizar informes de ubicación para la aplicación de Android.

## <a name="prerequisites"></a>Requisitos previos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>Informes de ubicación
Si desea que se notifiquen las ubicaciones, deberá agregar algunas líneas de configuración (entre las etiquetas `<application>` y `</application>`).

### <a name="lazy-area-location-reporting"></a>Informes de ubicaciones de áreas diferidas
Los informes diferidos de ubicación permiten notificar el país, la región y la localidad asociados con los dispositivos. Este tipo de informe de ubicación sólo emplea ubicaciones de red (basadas en el identificador del teléfono móvil o en WIFI). El área del dispositivo se notifica como máximo una vez por sesión. El GPS no se utiliza nunca y, por tanto, este tipo de informe de ubicación tiene muy poco impacto en la batería.

Las áreas notificadas se utilizan para elaborar estadísticas geográficas acerca de los usuarios, las sesiones, los eventos y los errores. También se pueden usar como criterios en campañas de cobertura.

Los informes diferidos de ubicación se habilitan mediante la configuración mencionada anteriormente en este procedimiento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

También necesita especificar un permiso de ubicación. Este código usa el permiso ``COARSE`` :

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Si la aplicación lo requiere, puede usar ``ACCESS_FINE_LOCATION`` en su lugar.

### <a name="real-time-location-reporting"></a>Informes de ubicación en tiempo real
Los informes de ubicación en tiempo real permiten notificar la latitud y la longitud asociadas con los dispositivos. Este tipo de informe de ubicación solo emplea ubicaciones de red basadas en el identificador del teléfono móvil o WiFi. Los informes solo están activos cuando la aplicación se ejecuta en primer plano (es decir, durante una sesión).

Las ubicaciones en tiempo real *NO* se usan para calcular estadísticas. Su único objetivo es permitir el uso de criterios de geovallas en tiempo real \<Reach-Audience-geofencing\> en las campañas de cobertura.

Para habilitar los informes de ubicación en tiempo real, agregue una línea de código en la que establece la cadena de conexión de Engagement en la actividad del iniciador. El resultado será similar al siguiente:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>Informes basados en GPS
De forma predeterminada, los informes de ubicación en tiempo real solo emplean ubicaciones de red. Para habilitar el uso de ubicaciones basadas en GPS, que son mucho más precisas, use el objeto de configuración:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Puede que también deba agregar el siguiente permiso si no existe:

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Informes en segundo plano
De forma predeterminada, los informes de ubicación en tiempo real solo están activos cuando la aplicación se ejecuta en primer plano (por ejemplo, durante una sesión). Para habilitar los informes también en segundo plano, use este objeto de configuración:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> Cuando la aplicación se ejecuta en segundo plano, solo se notifican las ubicaciones de red, incluso si ha habilitado el GPS.
> 
> 

Si el usuario reinicia su dispositivo, el informe de ubicación en segundo plano se detiene. Para que se reinicie automáticamente durante el arranque, agregue este código.

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

Puede que también deba agregar el siguiente permiso si no existe:

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Permisos de Android M
A partir de Android M, algunos permisos se administran en tiempo de ejecución y necesitan la aprobación del usuario.

Si su objetivo es la API de Android nivel 23, los permisos en tiempo de ejecución se desactivan de manera predeterminada para las nuevas instalaciones de aplicaciones. De lo contrario, se activan de forma predeterminada.

Se puede habilitar o deshabilitar esos permisos en el menú de configuración del dispositivo. Desactivar los permisos en el menú del sistema termina los procesos en segundo plano de la aplicación; se trata de un comportamiento del sistema y no afecta a la capacidad para recibir inserciones en segundo plano.

En el contexto de los informes de ubicación de Mobile Engagement, los permisos que requieren aprobación en tiempo de ejecución son:

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`

Debe solicitar permiso al usuario mediante un cuadro de diálogo estándar del sistema. Si el usuario acepta, indique a ``EngagementAgent`` que tenga ese cambio en cuenta en tiempo real. De lo contrario, el cambio se procesa la siguiente vez que el usuario inicie la aplicación.

Este es un ejemplo de código para usar en una actividad de la aplicación para solicitar permisos y reenviar el resultado si es positivo a ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
