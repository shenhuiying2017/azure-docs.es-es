<properties
	pageTitle="Informes de ubicación para el SDK de Android para Azure Mobile Engagement"
	description="Describe cómo configurar los informes de ubicación para el SDK de Android para Azure Mobile Engagement"
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
	ms.date="05/12/2016"
	ms.author="piyushjo;ricksal" />

# Informes de ubicación para el SDK de Android para Azure Mobile Engagement

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-integrate-engagement.md)

Este tema describe cómo realizar los informes de ubicación para la aplicación de Android.

## Requisitos previos

[AZURE.INCLUDE [Requisitos previos](../../includes/mobile-engagement-android-prereqs.md)]

## Informes de ubicación

Si desea que se notifiquen las ubicaciones, deberá agregar algunas líneas de configuración (entre las etiquetas `<application>` y `</application>`).

### Informes de ubicaciones de áreas diferidas

Los informes de ubicación de área diferida permiten notificar el país, la región y la localidad asociados con los dispositivos. Este tipo de informe de ubicación sólo emplea ubicaciones de red (basadas en el identificador del teléfono móvil o en WIFI). El área del dispositivo se notifica como máximo una vez por sesión. El GPS no se utiliza nunca y, por tanto, este tipo de informe de ubicación tiene muy poco impacto (por no decir ninguno) en la batería.

Las áreas notificadas se utilizan para elaborar estadísticas geográficas acerca de los usuarios, las sesiones, los eventos y los errores. También se pueden usar como criterios en campañas de cobertura.

Para habilitar los informes diferidos de ubicación, puede hacerlo mediante la configuración anteriormente mencionada en este procedimiento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Puede que también deba agregar el siguiente permiso si no existe:

	<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

O bien, puede seguir usando ``ACCESS_FINE_LOCATION`` si ya lo usa en la aplicación.

### Informes de ubicación en tiempo real

Los informes de ubicación en tiempo real permiten notificar la latitud y la longitud asociadas con los dispositivos. De forma predeterminada, este tipo de informes de ubicación solo emplea ubicaciones de red (basadas en el identificador del teléfono móvil o en WIFI), y los informes solo están activos cuando la aplicación se ejecuta en primer plano (por ejemplo, durante una sesión).

Las ubicaciones en tiempo real *NO* se usan para calcular las estadísticas. Su único objetivo es permitir el uso de criterios de geovallas en tiempo real <Reach-audiencia-geovallas> en las campañas de Reach.

Para habilitar los informes de ubicación en tiempo real, agregue una línea de código en la que establece la cadena de conexión de Engagement en la actividad del iniciador. El resultado tendrá este aspecto:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Puede que también deba agregar el siguiente permiso si no existe:

	<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

O bien, puede seguir usando ``ACCESS_FINE_LOCATION`` si ya lo usa en la aplicación.

#### Informes basados en GPS

De forma predeterminada, los informes de ubicación en tiempo real solo emplean ubicaciones de red. Para habilitar el uso de ubicaciones basadas en GPS (que son mucho más precisas), use el objeto de configuración:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Puede que también deba agregar el siguiente permiso si no existe:

	<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### Informes en segundo plano

De forma predeterminada, los informes de ubicación en tiempo real solo están activos cuando la aplicación se ejecuta en primer plano (es decir, durante una sesión). Para habilitar los informes también en segundo plano, use este objeto de configuración:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Cuando la aplicación se ejecuta en segundo plano, solo se notifican las ubicaciones de red, incluso si ha habilitado el GPS.

El informe de ubicación en segundo plano se detendrá si el usuario reinicia su dispositivo; puede agregar esto para hacer que se reinicie automáticamente en el momento de inicio:

	<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
		   android:exported="false">
		<intent-filter>
		    <action android:name="android.intent.action.BOOT_COMPLETED" />
		</intent-filter>
	</receiver>

Puede que también deba agregar el siguiente permiso si no existe:

	<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## Permisos de Android M

A partir de Android M, algunos permisos se administran en tiempo de ejecución y se necesita la aprobación del usuario.

Los permisos en tiempo de ejecución se desactivan de manera predeterminada para las nuevas instalaciones de aplicaciones con destino la API de Android nivel 23. De lo contrario, se activan de forma predeterminada.

El usuario puede habilitar o deshabilitar esos permisos en el menú de configuración del dispositivo. Desactivar los permisos en el menú del sistema termina los procesos en segundo plano de la aplicación; se trata de un comportamiento del sistema y no afecta a la capacidad para recibir notificaciones push en segundo plano.

En el contexto de los informes de ubicación de Mobile Engagement, los permisos que requieren aprobación en tiempo de ejecución son:

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`

Debe solicitar permiso al usuario mediante un cuadro de diálogo estándar del sistema. Si el usuario lo aprueba, debe indicar a ``EngagementAgent`` que tenga en cuenta ese cambio en tiempo real (de lo contrario,el cambio se procesará la próxima vez que el usuario inicie la aplicación).

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
         * Request location permission, but this won't explain why it is needed to the user.
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

<!---HONumber=AcomDC_0518_2016-->