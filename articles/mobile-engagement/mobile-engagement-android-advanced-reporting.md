<properties
	pageTitle="Opciones avanzadas de informes para el SDK de Azure Mobile Engagement para Android"
	description="Opciones avanzadas de informes para el SDK de Azure Mobile Engagement para Android"
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
	ms.date="05/04/2016"
	ms.author="piyushjo;ricksal" />

# Opciones de informes con Engagement en Android

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-advanced-reporting.md)

Este tema describe los escenarios de informes adicionales en la aplicación Android. Estas son opciones que puede aplicar a la aplicación que creó en el tutorial [Introducción](mobile-engagement-android-get-started.md).

## Requisitos previos

[AZURE.INCLUDE [Requisitos previos](../../includes/mobile-engagement-android-prereqs.md)]

El tutorial que realizó era deliberadamente directo y sencillo, pero hay una serie de opciones entre las que puede elegir.

## Compilación con ProGuard

Si compila su paquete de aplicación con ProGuard, deberá mantener algunas clases. Puede usar el siguiente snippet de configuración:


			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			<methods>;
		 	}

## Etiquetas del archivo AndroidManifest.xml

En la etiqueta de servicio del archivo AndroidManifest.xml, el atributo `android:label` le permite elegir el nombre del servicio Engagement que aparecerá para los usuarios finales en la pantalla "Running services" (Servicios en ejecución) de su teléfono. Se recomienda establecer este atributo en `"<Your application name>Service"` (por ejemplo, `"AcmeFunGameService"`).

La especificación del atributo `android:process` garantiza que el servicio Engagement se ejecutará en su propio proceso (al ejecutarse Engagement en el mismo proceso que su aplicación, el subproceso principal y de interfaz de usuario podría tener menos capacidad de respuesta).

## Uso de Application.onCreate()

Cualquier código que coloque en `Application.onCreate()` y en otras devoluciones de llamada de aplicaciones se ejecutará para todos los procesos de la aplicación, incluido el servicio Engagement. Esto puede tener efectos secundarios no deseados, como asignaciones innecesarias de memoria y subprocesos en el proceso de Engagement o receptores o servicios de difusión duplicados.

Si anula `Application.onCreate()`, se recomienda que agregue el siguiente snippet de código al comienzo de su función `Application.onCreate()`:

			 public void onCreate()
			 {
			   if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
			     return;

			   ... Your code...
			 }

Puede hacer lo mismo para `Application.onTerminate()`, `Application.onLowMemory()` y `Application.onConfigurationChanged(...)`.

También puede ampliar `EngagementApplication` en lugar de `Application`: la devolución de llamada `Application.onCreate()` solo realiza el proceso y llama a `Application.onApplicationProcessCreate()` si el proceso actual no es el que hospeda el servicio Engagement; las mismas reglas se aplican para las demás devoluciones de llamada.

## Modificación de sus clases `Activity`

En el [tutorial de introducción](mobile-engagement-android-get-started.md), todo lo que tenía que hacer era que las subclases `*Activity` heredaran las clases `Engagement*Activity` correspondientes (por ejemplo, si su actividad heredada ampliaba `ListActivity`, podía hacer que ampliara `EngagementListActivity`).

> [AZURE.IMPORTANT] Al usar `EngagementListActivity` o `EngagementExpandableListActivity`, asegúrese de que cualquier llamada a `requestWindowFeature(...);` se realice antes que la llamada a `super.onCreate(...);`, de lo contrario se producirá un bloqueo.

Puede encontrar estas clases en la carpeta `src` y puede copiarlas en su proyecto. Las clases también están en **JavaDoc**.

## Método alternativo: llamar a `startActivity()` y `endActivity()` manualmente

Si no puede o no quiere sobrecargar sus clases `Activity`, puede iniciar y finalizar sus actividades llamando a los métodos `EngagementAgent` directamente.

> [AZURE.IMPORTANT] El SDK de Android nunca llama al método `endActivity()`, incluso cuando la aplicación está cerrada (en Android, las aplicaciones nunca se cierran realmente). Por tanto, es *MUY* recomendable llamar al método `startActivity()` en la devolución de llamada `onResume` de *TODAS* las actividades y al método `endActivity()` en la devolución de llamada `onPause()` de *TODAS* las actividades. Esta es la única manera de asegurarse de que las sesiones no se pierdan. Si una sesión se pierde, el servicio Engagement nunca se desconectará del back-end de Engagement (dado que el servicio permanece conectado mientras una sesión esté pendiente).

Aquí tiene un ejemplo:

			public class MyActivity extends Some3rdPartyActivity
			{
			  @Override
			  protected void onResume()
			  {
			    super.onResume();
			    String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
			    EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
			  }

			  @Override
			  protected void onPause()
			  {
			    super.onPause();
			    EngagementAgent.getInstance(this).endActivity();
			  }
			}

Este ejemplo es muy parecido a la clase `EngagementActivity` y sus variantes, cuyo código fuente se proporciona en la carpeta `src`.

<!---HONumber=AcomDC_0511_2016-->