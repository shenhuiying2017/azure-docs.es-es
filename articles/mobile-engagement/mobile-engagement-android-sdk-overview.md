<properties 
	pageTitle="Integración del SDK de Android para Azure Mobile Engagement" 
	description="Procedimientos y actualizaciones más recientes para el SDK de Android para Azure Mobile Engagement"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="kapiteir" />


#SDK de Android para Azure Mobile Engagement

Comience aquí para obtener todos los detalles sobre cómo integrar Azure Mobile Engagement en una aplicación Android. Si primero quiere probarlo, asegúrese de seguir nuestro [tutorial de 15 minutos](mobile-engagement-android-get-started.md).

Haga clic para ver el [contenido del SDK](mobile-engagement-android-sdk-content.md).

##Procedimientos de integración
1. Comience aquí: [Integración de Mobile Engagement en su aplicación Android](mobile-engagement-android-integrate-engagement.md)

2. Para las notificaciones: [Integración de cobertura (notificaciones) en su aplicación Android](mobile-engagement-android-integrate-engagement-reach.md)
	1. Google Cloud Messaging (GCM): [Integración de GCM con Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
	2. Amazon Device Messaging (ADM): [Integración de ADM con Mobile Engagement](mobile-engagement-android-adm-integrate.md)

3. Implementación del plan de etiquetas: [Uso de la API de etiquetado de Mobile Engagement avanzada en su aplicación Android](mobile-engagement-android-use-engagement-api.md)


##Notas de la versión

###4.0.0 (07/06/2015)

-   Cambios en el protocolo interno para que la inserción y los análisis sean más confiables.
-   La inserción nativa (GCM/ADM) ahora también se usa para notificaciones dentro de la aplicación, por lo que debe configurar las credenciales de inserción nativas para cualquier tipo de campaña de inserción.
-   Corrección de la notificación con imagen grande: se mostraban solo 10 s después de insertarse.
-   Corrección del clic en un vínculo dentro de un anuncio web que tiene una dirección URL de acción predeterminada.
-   Corrección de un bloqueo excepcional relacionado con la administración del almacenamiento local.
-   Corrección de la administración dinámica de cadenas de configuración.
-   Actualización del CLUF.

Para la versión anterior, consulte las [notas de la versión completas](mobile-engagement-android-release-notes.md).

##Procedimientos de actualización

Si ya integró una versión anterior de nuestro SDK en la aplicación, debería tener en cuenta los siguientes puntos a la hora de actualizar el SDK.

Es posible que tenga que seguir varios procedimientos si se perdió varias versiones del SDK, consulte la sección completa [Procedimientos de actualización](mobile-engagement-android-upgrade-procedure.md). Por ejemplo, si migra de la versión 1.4.0 a la versión 1.6.0, primero tiene que seguir el procedimiento "de la versión 1.4.0 a la versión 1.5.0" y, a continuación, el procedimiento "de la versión 1.5.0 a la versión 1.6.0".

Sea cual sea la versión desde la que actualice, debe reemplazar `mobile-engagement-VERSION.jar` por el nuevo.

###De 3.0.0 a 4.0.0

#### Inserción nativa

La inserción nativa (GCM/ADM) ahora también se usa para notificaciones dentro de la aplicación, por lo que debe configurar las credenciales de inserción nativas para cualquier tipo de campaña de inserción.

Si aún no lo hizo, siga [este procedimiento](mobile-engagement-android-integrate-engagement-reach.md#native-push).

#### AndroidManifest.xml

Se modificó la integración de Reach en ``AndroidManifest.xml``.

Reemplazar esto:

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>

Por

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

Posiblemente, ahora hay una pantalla de carga al hacer clic en un anuncio (con texto y contenido web) o una encuesta. Debe agregar esto para que las campañas funcionen en 4.0.0:

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

#### Recursos

Inserte el nuevo archivo `res/layout/engagement_loading.xml` en su proyecto.

<!---HONumber=July15_HO2-->