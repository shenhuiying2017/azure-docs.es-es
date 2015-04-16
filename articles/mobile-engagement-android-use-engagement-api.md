<properties 
	pageTitle="Uso de la API de Engagement en Android" 
	description="Último SDK de Android - Uso de la API de Engagement en Android"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kapiteir" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />

#Uso de la API de Engagement en Android

Este artículo es un complemento del documento [Integración de Engagement en Android](mobile-engagement-android-integrate-engagement.md)y proporciona información detallada sobre el uso de la API de Engagement para notificar estadísticas de la aplicación.

Tenga en cuenta que si solamente desea que Engagement notifique las sesiones, actividades, bloqueos e información técnica de la aplicación, la forma más sencilla es hacer que todas las subclases `Activity` hereden de la clase `EngagementActivity` correspondiente.

Si requiere más tareas, por ejemplo, notificar eventos, errores y trabajos específicos de la aplicación o bien notificar actividades de la aplicación de forma distinta a la implementada en las clases `EngagementActivity`, deberá usar la API de Engagement.

La API de Engagement la proporciona la clase `EngagementAgent`. Para recuperar una instancia de esta clase puede llamarse al método estático `EngagementAgent.getInstance(Context)` (tenga en cuenta que el objeto `EngagementAgent` que se devuelve es un singleton).

##Conceptos de Engagement

A continuación se definen en mayor detalle los [Conceptos de Mobile Engagement](mobile-engagement-concepts.md)para la plataforma Android.

### `Sesión` y `Actividad`

Si el usuario permanece inactivo entre dos *actividades* durante más de unos segundos, su secuencia de *actividades* se divide en dos *sesiones* distintas. Estos segundos se denominan "tiempo de espera de la sesión".

Una *actividad* se asocia normalmente a una pantalla de la aplicación, es decir, la *actividad* se inicia cuando la pantalla aparece y se detiene cuando esta se cierra: este es el caso cuando el SDK de Engagement se integra mediante las clases `EngagementActivity`.

No obstante, las *actividades* también pueden controlarse manualmente con la API de Engagement. Esta permite dividir una pantalla dada en varias subpartes a fin de obtener más detalles sobre el uso de la misma (por ejemplo, saber con qué frecuencia y cuánto tiempo se usan los cuadros de diálogo en esta pantalla).

##Notificación de actividades

> [AZURE.IMPORTANT] Si usa la clase `EngagementActivity` y sus variantes según se explica en el documento Integración de Engagement en Android, no es necesario notificar las actividades de la forma descrita en esta sección.

### El usuario inicia una actividad nueva

			EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
			// Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

Cada vez que cambie la actividad del usuario, debe llamarse a `startActivity()`. La primera llamada a esta función inicia una nueva sesión de usuario.

El lugar idóneo para llamar a esta función es cada devolución de llamada de actividad `onResume`.

### El usuario finaliza la actividad actual

			EngagementAgent.getInstance(this).endActivity();

Cuando el usuario finaliza su última actividad, se debe llamar a `endActivity()` al menos una vez. De esta manera se informa al SDK de Engagement de que el usuario está inactivo y que la sesión del usuario se debe cerrar cuando expire el tiempo de espera de la misma (si se llama a `startActivity()` antes de que expire dicho tiempo de espera, la sesión simplemente se reanuda).

El lugar idóneo para llamar a esta función es cada devolución de llamada de actividad `onPause`.

##Notificación de eventos

### Eventos de sesión

Los eventos de sesión se usan normalmente para notificar las acciones realizadas por el usuario durante su sesión.

**Ejemplo sin datos adicionales:**

			public MyActivity extends EngagementActivity {
			   [...]
			   @Override
			   public boolean onPrepareOptionsMenu(Menu menu) {
			      getEngagementAgent().sendSessionEvent("menu_shown", null);
			   }
			   [...]
			}

**Ejemplo con datos adicionales:**

			public MyActivity extends EngagementActivity {
			  [...]
			  @Override
			  public boolean onMenuItemSelected(int featureId, MenuItem item) {
			    Bundle extras = new Bundle();
			    extras.putInt("id", item.getItemId());
			    getEngagementAgent().sendSessionEvent("menu_selected", extras);
			  }
			  [...]
			}

### Eventos independientes

Al contrario de los eventos de sesión, los eventos independientes pueden producirse fuera del contexto de una sesión.

**Ejemplo:**

Supongamos que desea notificar los eventos que se producen al desencadenarse un receptor de difusión:

			/** Triggered by Intent.ACTION_BATTERY_LOW */
			public BatteryLowReceiver extends BroadcastReceiver {
			  [...]
			  @Override
			  public void onReceive(Context context, Intent intent) {
			    EngagementAgent.getInstance(context).sendEvent("battery_low", null);
			  }
			  [...]
			}

##Notificación de errores

### Errores de sesión

Los errores de sesión normalmente se usan para notificar los errores que afectan al usuario durante la sesión.

**Ejemplo:**

			/** The user has entered invalid data in a form */
			public MyActivity extends EngagementActivity {
			  [...]
			  public void onMyFormSubmitted(MyForm form) {
			    [...]
			    /* The user has entered an invalid email address */
			    getEngagementAgent().sendSessionError("sign_up_email", null);
			    [...]
			  }
			  [...]
			}

### Errores independientes

Al contrario de los errores de sesión, los errores independientes pueden producirse fuera del contexto de una sesión.

**Ejemplo:**

En el ejemplo siguiente se muestra cómo notificar un error siempre que quede poca memoria en el teléfono mientras se ejecuta el proceso de la aplicación.

			public MyApplication extends EngagementApplication {
			
			  @Override
			  protected void onApplicationProcessLowMemory() {
			    EngagementAgent.getInstance(this).sendError("low_memory", null);
			  }
			}

##Notificación de trabajos

### Ejemplo

Supongamos que desea notificar la duración del proceso de inicio de sesión:
			
			[...]
			public void signIn(Context context, ...) {
			
			  /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
			  EngagementAgent engagementAgent = EngagementAgent.getInstance(context);
			
			  /* Report sign in job has started */
			  engagementAgent.startJob("sign_in", null);
			
			  [... sign in ...]
			
			  /* Report sign in job is now ended */
			  engagementAgent.endJob("sign_in");
			}
			[...]

### Notificación de errores durante un trabajo

Los errores pueden estar relacionados con un trabajo en ejecución en lugar de con la sesión de usuario actual.

**Ejemplo:**

Supongamos que desea notificar un error durante el proceso de inicio de sesión:

[...]
public void signIn(Context context, ...) {

			  /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
			  EngagementAgent engagementAgent = EngagementAgent.getInstance(context);
			
			  /* Report sign in job has been started */
			  engagementAgent.startJob("sign_in", null);
			
			  /* Try to sign in */
			  while(true)
			    try {
			      trySignin();
			      break;
			    }
			    catch(Exception e) {
			      /* Report the error to Engagement */
			      engagementAgent.sendJobError("sign_in_error", "sign_in", null);
			
			      /* Retry after a moment */
			      sleep(2000);
			    }
			  [...]
			  /* Report sign in job is now ended */
			  engagementAgent.endJob("sign_in");
			}
			[...]

### Notificación de eventos durante un trabajo

Los eventos pueden estar relacionados con un trabajo en ejecución en lugar de con la sesión de usuario actual.

**Ejemplo:**

Supongamos que tenemos una red social y usamos un trabajo para notificar el tiempo total durante el cual el usuario está conectado al servidor. El usuario puede permanecer conectado en segundo plano incluso cuando utiliza otra aplicación o cuando el teléfono está inactivo, por lo que no hay sesión.

El usuario puede recibir mensajes de sus amigos: esto es un evento de trabajo.
			
			[...]
			public void signin(Context context, ...) {
			  [...Sign in code...]
			  EngagementAgent.getInstance(context).startJob("connection", null);
			}
			[...]
			public void signout(Context context) {
			  [...Sign out code...]
			  EngagementAgent.getInstance(context).endJob("connection");
			}
			[...]
			public void onMessageReceived(Context context) {
			  [...Notify in status bar...]
			  EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
			}
			[...]

##Parámetros adicionales

Pueden asociarse datos arbitrarios a los eventos, errores, actividades y trabajos.

Estos datos se pueden estructurar y usan la clase Bundle de Android (en realidad, funcionan como los parámetros adicionales en los elementos Intent de Android). Tenga en cuenta que una clase Bundle puede contener matrices u otras instancias de Bundle.

> [AZURE.IMPORTANT] Si incluye parámetros de tipo parcelable o serializable, asegúrese de que se implemente su método `toString()` para devolver una cadena en lenguaje natural. Las clases serializables con campos no transitorios de tipo no serializable provocarán un bloqueo de Android cuando se llame a `bundle.putSerializable("key",value);`

> [AZURE.WARNING] No se admiten las matrices ralas en los parámetros adicionales, es decir, no se serializarán como matriz. Deberán convertirse en matrices estándar antes de usarlas en dichos parámetros.

### Ejemplo

			Bundle extras = new Bundle();
			extras.putString("video_id", 123);
			extras.putString("ref_click", "http://foobar.com/blog");
			EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### Límites

#### Claves

Cada clave incluida en `Bundle` debe coincidir con la siguiente expresión regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Esto significa que las claves deben empezar por al menos una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### Tamaño

Los datos adicionales o extras se limitan a **1024** caracteres por llamada (una vez codificados en JSON por el servicio Engagement).

En el ejemplo anterior, el código JSON enviado al servidor tiene una longitud de 58 caracteres:

			{"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##Notificación de información de la aplicación

La información de seguimiento (o cualquier otra información específica de la aplicación) puede notificarse manualmente mediante la función `sendAppInfo()`.

Tenga en cuenta que esta información se puede enviar de forma incremental: solo se conservará el último valor de una clave dada para un dispositivo determinado.

Al igual que los datos adicionales de los eventos, la clase Bundle se usa para resumir la información de la aplicación. Tenga en cuenta que las matrices o subagrupaciones se tratarán como cadenas sin formato (con la serialización JSON).

### Ejemplo

Este es un ejemplo de código para enviar el género y la fecha de nacimiento del usuario:

			Bundle appInfo = new Bundle();
			appInfo.putString("status", "premium");
			appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
			EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### Límites

#### Claves

Cada clave incluida en `Bundle` debe coincidir con la siguiente expresión regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Esto significa que las claves deben empezar por al menos una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### Tamaño

La información de la aplicación se limita a **1024** caracteres por llamada (una vez codificada en JSON por el servicio Engagement).

En el ejemplo anterior, el código JSON enviado al servidor tiene una longitud de 44 caracteres:

			{"expiration":"2016-12-07","status":"premium"}

<!--HONumber=47-->
