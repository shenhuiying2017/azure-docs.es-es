---
title: "Integración del SDK de Android para Azure Mobile Engagement"
description: "Procedimientos y actualizaciones más recientes para el SDK de Android para Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: d72b5014-a22b-4a7f-a470-d2b8145b5b86
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/10/2016
ms.author: piyushjo
ms.openlocfilehash: 0282abbf44406cac89c13520bc2a4e375817ed1f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-integrate-gcm-with-mobile-engagement"></a>Integración de GCM con Mobile Engagement
> [!IMPORTANT]
> Debe seguir el procedimiento de integración descrito en el documento Integración de Engagement en Android antes de seguir con esta guía.
> 
> Este documento es útil solo si ya integró el módulo de cobertura y el plan para insertar dispositivos de Google Play. Para integrar campañas de Reach en la aplicación, lea primero Integración de Engagement Reach en Android.
> 
> 

## <a name="introduction"></a>Introducción
La integración de GCM permite realizar inserciones en su aplicación.

Las cargas GCM insertadas en el SDK siempre contienen la clave `azme` en el objeto de datos. Por lo tanto, si utiliza GCM para otra finalidad en la aplicación, puede filtrar las inserciones basándose en esa clave.

> [!IMPORTANT]
> Solo los dispositivos que ejecuten Android 2.2 o versiones posteriores, con Google Play instalado y con una conexión de fondo de Google habilitada, se pueden insertar mediante GCM; sin embargo, puede integrar este código de forma segura en los dispositivos no compatibles (simplemente usa representaciones).
> 
> 

## <a name="create-a-google-cloud-messaging-project-with-api-key"></a>Creación de un proyecto del Servicio de mensajería en la nube de Google con clave de API
[!INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

## <a name="sdk-integration"></a>Integración de SDK
### <a name="managing-device-registrations"></a>Administración de registros de dispositivos
Cada dispositivo debe enviar un comando de registro a los servidores de Google; en caso contrario, no se puede establecer la conexión.

También puede anular el registro de un dispositivo de las notificaciones de GCM (el dispositivo se elimina automáticamente si se desinstala la aplicación).

Si no usa el [SDK de Google Play] o no ha enviado aún el intento de registro, puede hacer que Engagement registre el dispositivo automáticamente.

Para habilitar esta opción, agregue lo siguiente al archivo de `AndroidManifest.xml`, dentro de la etiqueta `<application/>`:

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Comunicar el identificador de registro al servicio de inserción de Engagement y recibir notificaciones
Para comunicar el identificador de registro del dispositivo al servicio de inserción de Engagement y recibir notificaciones, agregue lo siguiente al archivo de `AndroidManifest.xml`, dentro de la etiqueta `<application/>` (aunque administre registros de dispositivos):

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

Asegúrese de tener los permisos siguientes en `AndroidManifest.xml` (después de la etiqueta `</application>`).

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

## <a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Conceder acceso de Mobile Engagement a la clave de API de GCM
Siga [esta guía](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key) para conceder acceso de Mobile Engagement a la clave de API de GCM.

[SDK de Google Play]:https://developers.google.com/cloud-messaging/android/start
