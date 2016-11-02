<properties
	pageTitle="Configuración avanzada para el SDK de Android para Azure Mobile Engagement"
	description="Describe las opciones de configuración avanzada, incluido el manifiesto de Android con el SDK de Android para Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/02/2016"
	ms.author="piyushjo;ricksal" />

# Configuración avanzada para el SDK de Android para Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Windows universal](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-logging.md)

Este procedimiento describe cómo configurar diversas opciones de configuración de aplicaciones de Android para Azure Mobile Engagement.

## Requisitos previos

[AZURE.INCLUDE [Requisitos previos](../../includes/mobile-engagement-android-prereqs.md)]

## Requisitos de permiso
Algunas opciones requieren permisos específicos, las cuales se enumeran aquí para su referencia e integradas en las características en cuestión. Agregue estos permisos al archivo AndroidManifest.xml del proyecto inmediatamente antes o después de la etiqueta `<application>`.

El código de permiso debe ser similar al siguiente, donde se rellena el permiso adecuado a partir de la tabla siguiente.

	<uses-permission android:name="android.permission.[specific permission]"/>


| Permiso | Cuándo se usa |
| ---------- | --------- |
| INTERNET | Obligatorio. Para informes básicos |
| ACCESS\_NETWORK\_STATE | Obligatorio. Para informes básicos |
| RECEIVE\_BOOT\_COMPLETED | Obligatorio. Para mostrar el centro de notificaciones tras el reinicio del dispositivo |
| WAKE\_LOCK | Se recomienda su uso. Habilita la recopilación de datos cuando se usa la conexión WiFi o cuando la pantalla está apagada |
| VIBRATE | Opcional. Habilita la vibración cuando se reciben las notificaciones |
| DOWNLOAD\_WITHOUT\_NOTIFICATION | Opcional. Habilita la notificación de panorama general de Android |
| WRITE\_EXTERNAL\_STORAGE | Opcional. Habilita la notificación de panorama general de Android |
| ACCESS\_COARSE\_LOCATION | Opcional. Habilita los informes de ubicación en tiempo real |
| ACCESS\_FINE\_LOCATION | Opcional. Habilita los informes de ubicación basados en GPS |

A partir de Android M, [algunos permisos se administran en tiempo de ejecución](mobile-engagement-android-location-reporting.md#Android-M-Permissions).

Si ya usa ``ACCESS_FINE_LOCATION``, no necesita usar también ``ACCESS_COARSE_LOCATION``.

## Opciones de configuración del manifiesto de Android

### Informe de bloqueos

Si desea deshabilitar los informes de bloqueo, agregue este código entre las etiquetas `<application>` y `</application>`:

	<meta-data android:name="engagement:reportCrash" android:value="false"/>

### Umbral de ráfaga

De forma predeterminada, el servicio de Engagement informa los registros en tiempo real. Si su aplicación notifica registros con mucha frecuencia, es mejor almacenar en búfer los registros y notificarlos todos a la vez de manera periódica (esto se conoce como "modo de ráfaga"). Para ello, agregue este código entre las etiquetas `<application>` y `</application>`:

	<meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

El modo de ráfaga aumenta ligeramente la duración de la batería, pero afecta al monitor de Engagement: la duración de todas las sesiones y trabajos se redondeará al umbral de ráfaga (por lo tanto, es posible que las sesiones y los trabajos más cortos que el umbral de ráfaga no sean visibles). El umbral de ráfaga no debe ser superior a 30 000 (30 segundos).

### Tiempo de espera de sesión

 Puede finalizar una actividad presionando la tecla **Inicio** o **Atrás**, poniendo el teléfono inactivo o yendo a otra aplicación. De forma predeterminada, una sesión finaliza diez segundos después de su última actividad. Esto evita que una sesión se divida cada vez que el usuario sale de la aplicación y vuelve a ella rápidamente, lo cual puede suceder cuando se selecciona una imagen, se comprueba una notificación, etc. Puede que desee modificar este parámetro. Para ello, agregue este código entre las etiquetas `<application>` y `</application>`:

	<meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## Deshabilitación de los informes de registro

### Mediante una llamada al método

Si desea que Engagement deje de enviar registros, puede efectuar una llamada:

	EngagementAgent.getInstance(context).setEnabled(false);

Esta llamada es persistente: usa un archivo de preferencias compartido.

Si Engagement está activo cuando llama a esta función, puede que el servicio tarde un minuto en detenerse. Sin embargo, no se iniciará el servicio la próxima vez que inicie la aplicación.

Puede habilitar de nuevo los informes de registro mediante la llamada a la misma función con `true`.

### Integración en su propia `PreferenceActivity`

En lugar de llamar a esta función, también puede integrar este valor directamente en su `PreferenceActivity` existente.

Puede configurar Engagement para usar su archivo de preferencias (con el modo deseado) en el archivo `AndroidManifest.xml` con `application meta-data`:

-   La clave `engagement:agent:settings:name` se usa para definir el nombre del archivo de preferencias compartido.
-   La clave `engagement:agent:settings:mode` se usa para definir el nombre del archivo de preferencias compartido. Use el mismo modo que en su `PreferenceActivity`. El modo se debe pasar como un número: si usa una combinación de indicadores constantes en su código, compruebe el valor total.

Engagement siempre usa la clave booleana `engagement:key` dentro del archivo de preferencias para administrar este valor.

En el siguiente ejemplo de `AndroidManifest.xml` se muestran los valores predeterminados:

	<application>
	    [...]
	    <meta-data
	      android:name="engagement:agent:settings:name"
	      android:value="engagement.agent" />
	    <meta-data
	      android:name="engagement:agent:settings:mode"
	      android:value="0" />

Luego, puede agregar un `CheckBoxPreference` a su diseño de preferencias como el siguiente:

	<CheckBoxPreference
	  android:key="engagement:enabled"
	  android:defaultValue="true"
	  android:title="Use Engagement"
	  android:summaryOn="Engagement is enabled."
	  android:summaryOff="Engagement is disabled." />

<!---HONumber=AcomDC_0817_2016-->