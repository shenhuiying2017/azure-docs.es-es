<properties
	pageTitle="API de SDK web de Azure Mobile Engagement | Microsoft Azure"
	description="Actualizaciones y procedimientos más recientes para el SDK web para Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="web"
	ms.devlang="js"
	ms.topic="article"
	ms.date="06/07/2016"
	ms.author="piyushjo" />

# Uso de la API de Engagement en una aplicación web

Este artículo es un complemento del documento [How to Integrate Engagement on a Web application](mobile-engagement-web-integrate-engagement.md) (Integración de Engagement en una aplicación web). En él se proporciona información detallada acerca de cómo usar la API de Engagement para informar de las estadísticas de la aplicación.

El objeto `engagement.agent` proporciona la API de Engagement. `engagement` es el alias predeterminado del SDK de Engagement, que puede redefinirse en la configuración del SDK.

## Conceptos de Engagement

En las siguientes secciones se detallan los [conceptos de Mobile Engagement](mobile-engagement-concepts.md) habituales para la plataforma web.

### `Session` y `Activity`

Si el usuario permanece inactivo entre dos *actividades* durante más de unos segundos, su secuencia de *actividades* se divide en dos *sesiones* distintas. Estos segundos se denominan *tiempo de espera de la sesión*.

Si la aplicación web no declara el final de las actividades de usuario por sí misma (mediante una llamada a la función `engagement.agent.endActivity`), el servidor de Engagement hará que la sesión del usuario expire automáticamente en los 3 minutos posteriores al cierre de la página de la aplicación. Este comportamiento se denomina *tiempo de espera* del servidor.

### `Crash`

No existe ningún informe automatizado de excepciones no detectadas de JavaScript. No obstante, puede notificar bloqueos manualmente mediante la función `sendCrash` (ver abajo).

## Informes sobre actividades

### El usuario inicia una nueva actividad

	engagement.agent.startActivity("MyUserActivity");

Debe llamar a `startActivity()` cada vez que cambie la actividad de usuario. La primera llamada a esta función inicia una nueva sesión de usuario.

### El usuario finaliza su actividad actual

	engagement.agent.endActivity();

Cuando el usuario finaliza su última actividad, se debe llamar a `endActivity()` al menos una vez. De esta manera se informa al SDK de Engagement de que el usuario está inactivo y que la sesión del usuario se debe cerrar cuando expire el tiempo de espera de la misma (si se llama a `startActivity()` antes de que expire dicho tiempo de espera, la sesión simplemente se reanuda).

A menudo resulta difícil o imposible detectar el final de las actividades de usuario dentro de entornos web (las llamadas no son confiables cuando se cierra la ventana del navegador). Por esta razón, el servidor Engagement hace que la sesión del usuario expire automáticamente en los 3 minutos posteriores al cierre de la página de la aplicación.

## Informes de eventos

### Eventos de sesión

Los eventos de sesión se suelen usar para notificar las acciones que realiza el usuario durante su sesión.

**Ejemplo sin datos adicionales:**

	loginButton.onclick = function() {
	  engagement.agent.sendSessionEvent('login');
	  // [...]
	}

**Ejemplo con datos adicionales:**

	loginButton.onclick = function() {
	  engagement.agent.sendSessionEvent('login', {user: 'alice'});
	  // [...]
	}

### Eventos independientes

Al contrario de los eventos de sesión, los eventos independientes pueden producirse fuera del contexto de una sesión.

Por ello, utilice ``engagement.agent.sendEvent`` en lugar de ``engagement.agent.sendSessionEvent``.

## Informes de errores

### Errores de sesión

Los errores de sesión suelen usarse para notificar los errores que afectan al usuario durante su sesión.

**Ejemplo sin datos adicionales:**

	var validateForm = function() {
	  // [...]
	  if (password.length < 6) {
	    engagement.agent.sendSessionError('password_too_short');
	  }
	  // [...]
	}

**Ejemplo con datos adicionales:**

	var validateForm = function() {
	  // [...]
	  if (password.length < 6) {
	    engagement.agent.sendSessionError('password_too_short', {length: 4});
	  }
	  // [...]
	}

### Errores independientes

Al contrario de los errores de sesión, los errores independientes se pueden producir fuera del contexto de una sesión.

Por ello, utilice `engagement.agent.sendError` en lugar de `engagement.agent.sendSessionError`.

## Informes de trabajos

### Ejemplo

Suponga que desea supervisar una solicitud de Ajax:
			
	// [...]
	xhr.onreadystatechange = function() {
	  if (xhr.readyState == 4) {
	  // [...]
	    engagement.agent.endJob('publish');
	  }
	}
	engagement.agent.startJob('publish');
	xhr.send();
	// [...]

### Notificación de errores durante un trabajo

Los errores pueden estar relacionados con un trabajo en ejecución en lugar de la sesión del usuario actual.

**Ejemplo:**

Suponga que desea notificar que se ha producido un error en una solicitud de Ajax:

	// [...]
	xhr.onreadystatechange = function() {
	  if (xhr.readyState == 4) {
	    // [...]
	    if (xhr.status == 0 || xhr.status >= 400) {
	      engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
	    }
	    engagement.agent.endJob('publish');
	  }
	}
	engagement.agent.startJob('publish');
	xhr.send();
	// [...]

### Notificación de eventos durante un trabajo

Los eventos pueden estar relacionados con un trabajo en ejecución en lugar de la sesión del usuario actual gracias a la función `engagement.agent.sendJobEvent`.

Esta función se comporta exactamente como `engagement.agent.sendJobError`.

### Informes de bloqueos

La función `sendCrash` se utiliza para notificar bloqueos manualmente.

El argumento `crashid` es una cadena que se utiliza para identificar el tipo de bloqueo. El argumento `crash` normalmente es el seguimiento de la pila del bloqueo como una cadena.

	engagement.agent.sendCrash(crashid, crash);

## Parámetros adicionales

Es posible adjuntar datos arbitrarios a un evento, un error, una actividad o un trabajo.

Estos datos pueden ser cualquier objeto JSON (no de tipo primitivo ni de matriz).

**Ejemplo**

	var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
	engagement.agent.sendEvent("video_clicked", extras);

### Límites

#### simétricas

Cada clave del objeto debe coincidir con la siguiente expresión regular:

	^[a-zA-Z][a-zA-Z_0-9]*

Esto significa que las claves deben empezar con al menos una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### Valores

Los valores solo pueden ser de tipo booleano, de número y de cadena.

#### Tamaño

Los extras están limitados a **1024** caracteres por llamada (una vez codificados en JSON por el SDK).

## Información de la aplicación de informes

Puede notificar manualmente la información de seguimiento (o cualquier otro tipo de información específica de la aplicación) mediante la función `sendAppInfo()`.

Tenga en cuenta que esta información se puede enviar de forma incremental: para un dispositivo dado solo se conservará el último valor de una clave determinada.

Al igual que los extras de evento, se puede usar cualquier objeto JSON para resumir la información de la aplicación. Tenga en cuenta que las matrices o subobjetos se tratarán como cadenas sin formato (con la serialización de JSON).

### Ejemplo

Este es un ejemplo de código para enviar el género y la fecha de nacimiento del usuario:

	var appInfos = {"birthdate":"1983-12-07","gender":"female"};
	engagement.agent.sendAppInfo(appInfos);

### Límites

#### simétricas

Cada clave del objeto debe coincidir con la siguiente expresión regular:

	^[a-zA-Z][a-zA-Z_0-9]*

Esto significa que las claves deben empezar con al menos una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### Tamaño

La información de la aplicación está limitada a **1024** caracteres por llamada (una vez codificados en JSON por el SDK).

En el ejemplo anterior, el JSON que se envía al servidor tiene una longitud de 44 caracteres:

	{"birthdate":"1983-12-07","gender":"female"}
 

<!---HONumber=AcomDC_0615_2016-->