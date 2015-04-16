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
	ms.date="02/12/2015" 
	ms.author="kapiteir" />


#SDK de Android para Azure Mobile Engagement v3.0.0
Comience aquí para obtener todos los detalles sobre cómo integrar Azure Mobile Engagement en una aplicación Android. Si desea probar primero, asegúrese de realizar nuestro [tutorial de 15 minutos](mobile-engagement-android-get-started.md).

Haga clic para ver el [contenido del SDK](mobile-engagement-android-sdk-content.md)

##Procedimientos de integración
1. Comience aquí: [Integración de Mobile Engagement en su aplicación Android](mobile-engagement-android-integrate-engagement.md)

2. Para las notificaciones: [Integración de cobertura (notificaciones) en su aplicación Android](mobile-engagement-android-integrate-engagement-reach.md)
	1. Servicio de mensajería en la nube de Google (GCM): [Integración de GCM con Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
	2. Amazon Device Messaging (ADM): [Integración de ADM con Mobile Engagement](mobile-engagement-android-adm-integrate.md)

3. Implementación del plan de etiqueta: [Uso de la API de etiquetado de Mobile Engagement avanzada en su aplicación Android](mobile-engagement-android-use-engagement-api.md)


##Notas de la versión

###3.0.0 (17/02/2015)

-   Versión inicial de Azure Mobile Engagement
-   La configuración de appID se reemplaza por una configuración de cadena de conexión.
-   API eliminada para enviar y recibir mensajes XMPP arbitrarios desde entidades XMPP arbitrarias.
-   API eliminada para enviar y recibir mensajes entre dispositivos.
-   Mejoras de seguridad.
-   Seguimiento de SmartAd y Google Play eliminados.

En caso de necesitar información sobre una versión anterior, consulte las [notas de versión completas](mobile-engagement-android-release-notes.md)

##Procedimientos de actualización

Si ya ha integrado una versión anterior de nuestro SDK en su aplicación, deberá tener en cuenta los siguientes puntos al actualizar el SDK.

Es posible que tenga que seguir varios procedimientos si se omitieron varias versiones del SDK. Consulte [Procedimientos de actualización] completos(mobile-engagement-android-upgrade-procedure.md). Por ejemplo, si migra de la versión 1.4.0 a la versión 1.6.0, primero tiene que seguir el procedimiento "de la versión 1.4.0 a la versión 1.5.0" y, a continuación, el procedimiento "de la versión 1.5.0 a la versión 1.6.0".

Sea cual sea la versión desde la que actualice, debe sustituir todos los `mobile-engagement-VERSION.jar` por los nuevos.

###De la versión 2.4.0 a la versión 3.0.0

A continuación se describe cómo migrar una integración del SDK desde el servicio Capptain que ofrece Capptain SAS en una aplicación con tecnología de Azure Mobile Engagement. 

>[AZURE.IMPORTANT] Capptain y Mobile Engagement no son los mismos servicios y el procedimiento dado a continuación solo resalta cómo migrar la aplicación cliente. La migración del SDK en la aplicación NO migrará los datos desde los servidores de Capptain a los servidores de Mobile Engagement.

#### Archivo JAR

Sustituya `capptain.jar` por `mobile-engagement-VERSION.jar` en su carpeta `libs`.

#### Archivos de recursos

Cada archivo de recursos que hemos proporcionado (con el prefijo `capptain_`) se debe reemplazar por los nuevos (con el prefijo `engagement_`).

Si ha personalizado esos archivos, debe volver a aplicar la personalización en los archivos nuevos y **también se cambió el nombre de todos los identificadores en los archivos de recursos**.

#### Identificador de aplicación

Engagement ahora usa una cadena de conexión para configurar los identificadores del SDK, como el identificador de aplicación.

Debe usar el método `EngagementAgent.init` en la actividad del iniciador, de la siguiente manera:

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
			EngagementAgent.getInstance(this).init(engagementConfiguration);

La cadena de conexión de la aplicación aparece en el Portal de Azure.

Quite toda llamada a `CapptainAgent.configure`, puesto que `EngagementAgent.init` reemplaza ese método.

El `appId` ya no se puede configurar mediante `AndroidManifest.xml`.

Quite esta sección de su `AndroidManifest.xml` si la tiene:

			<meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

#### API de Java

Cada llamada a cualquier clase Java de nuestro SDK se ha de cambiar de nombre, por ejemplo `CapptainAgent.getInstance(this)` debe tener ahora el nombre `EngagementAgent.getInstance(this)`, `extends CapptainActivity` debe tener ahora el nombre `extends EngagementActivity` etc...

Si estuviera integrado con archivos de preferencia del agente predeterminados, el nombre de archivo predeterminado ahora es `engagement.agent` y la clave es `engagement:agent`.

Al crear anuncios web, el enlazador de Javascript ahora es `engagementReachContent`.

#### AndroidManifest.xml

Muchos cambios se han producido ahí, el servicio ya no se comparte y muchos receptores ya no son exportables.

La declaración de servicio ahora es más simple: quite el filtro de intento y todos los metadatos que contenga y agregue `exportable=false`.

Además, todo cambia de nombre para usar Engagement.

Ahora se debe parecer a esto:

			<service
			  android:name="com.microsoft.azure.engagement.service.EngagementService"
			  android:exported="false"
			  android:label="<Your application name>Service"
			  android:process=":Engagement"/>

Cuando desee habilitar los registros de prueba, ahora los metadatos se movieron a la etiqueta de aplicación y ahora se llaman:

			<application>
			
			  <meta-data android:name="engagement:log:test" android:value="true" />
			
			  <service/>
			
			</application>

Todos los demás metadatos han cambiado de nombre; esta es la lista completa (naturalmente, cambie de nombre solo los que use):

			<meta-data
			  android:name="engagement:reportCrash"
			  android:value="true"/>
			<meta-data
			  android:name="engagement:sessionTimeout"
			  android:value="10000"/>
			<meta-data
			  android:name="engagement:burstThreshold"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:connection:delay"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:locationReport:lazyArea"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime:background"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime:fine"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:agent:settings:name"
			  android:value="engagement.agent"/>
			<meta-data
			  android:name="engagement:agent:settings:mode"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:gcm:sender"
			  android:value="<YOUR_PROJECT_NUMBER>\n"/>
			<meta-data
			  android:name="engagement:adm:register"
			  android:value="true"/>
			<meta-data
			  android:name="engagement:reach:notification:icon"
			  android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>
			
			<activity android:name="SomeActivityWithoutReachOverlay">
			  <meta-data
			    android:name="engagement:notification:overlay"
			    android:value="false"/>
			</activity>

El seguimiento de SmartAd y Google Play se quitó del SDK; solo debe quitar esto sin reemplazarlo:

			<meta-data 
				android:name="capptain:track:installReferrerForwardList"
				android:value="com.class1,com.class2"/>
			<meta-data
				android:name="capptain:track:adservers"
				android:value="smartad" />

Las actividades de cobertura se declaran ahora de esta forma:

			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			    <data android:mimeType="text/plain"/>
			  </intent-filter>
			</activity>
			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			    <data android:mimeType="text/html"/>
			  </intent-filter>
			</activity>
			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			  </intent-filter>
			</activity>
			
Si tiene actividades de cobertura personalizadas, solo necesita cambiar las acciones preventivas para que coincidan con `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` o con `com.microsoft.azure.engagement.reach.intent.action.POLL`.

Los receptores de difusión han cambiado de nombre; además, ahora nosotros agregamos `exported=false`. Esta es la lista completa de los receptores con su nueva especificación (por supuesto, cambie el nombre solo de las que usa):

			<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
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
			
			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
			  android:permission="com.google.android.c2dm.permission.SEND">
			  <intent-filter>
			    <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
			    <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
			    <category android:name="<your_package_name>"/>
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
			  android:permission="com.amazon.device.messaging.permission.SEND">
			  <intent-filter>
			    <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
			    <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
			    <category android:name="<your_package_name>"/>
			  </intent-filter>
			</receiver>
			
			<receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
			   android:exported="false">
			   <intent-filter>
			      <action android:name="android.intent.action.BOOT_COMPLETED" />
			   </intent-filter>
			</receiver>
			
			<receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
			    <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
			  </intent-filter>
			</receiver>

Se quitó el receptor de seguimiento, por lo que debe quitar esta sección:

		  <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
		    <intent-filter>
		      <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
		      <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
		    </intent-filter>
		  </receiver>

Tenga en cuenta que la declaración de su implementación del receptor de difusión **EngagementMessageReceiver** ha cambiado en el `AndroidManifest.xml`. Esto se debe a que la API para enviar y eliminar mensajes XMPP arbitrarios de entidades XMPP arbitrarias y la API para enviar y recibir mensajes entre dispositivos se han eliminado. Por lo tanto, también tiene que eliminar las siguientes devoluciones de llamada de su implementación de **EngagementMessageReceiver**:

			protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

y

			protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

luego elimine toda llamada en **EngagementAgent** para:

			sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

y

			sendXMPPMessage(android.os.Bundle msg)

#### Proguard

La configuración de Proguard se puede ver afectada por la renovación de la marca; las reglas ahora se ven de la siguiente manera:

			-dontwarn android.**
			-keep class android.support.v4.** { *; }
			
			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			  <methods>;
			}

<!--HONumber=47-->
