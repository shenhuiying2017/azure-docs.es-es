---
title: API de SDK web de Azure Mobile Engagement | Microsoft Docs
description: Actualizaciones y procedimientos más recientes para el SDK web para Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''

ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo

---
# Uso de la API de Azure Mobile Engagement en una aplicación web
Este documento es un complemento al documento que muestra [cómo integrar Mobile Engagement en una aplicación web](mobile-engagement-web-integrate-engagement.md). En él se proporciona información detallada acerca de cómo usar la API de Azure Mobile Engagement para notificar las estadísticas de la aplicación.

El objeto `engagement.agent` proporciona la API de Mobile Engagement. El SDK web de Azure Mobile Engagement predeterminado es `engagement`. Puede volver a definir este alias desde la configuración del SDK.

## Conceptos de Mobile Engagement
En las siguientes secciones se detallan los [conceptos de Mobile Engagement](mobile-engagement-concepts.md) habituales para la plataforma web.

### `Session` y `Activity`
Si el usuario permanece inactivo entre dos actividades durante más de unos segundos, la secuencia de actividades del usuario se divide en dos sesiones distintas. Estos segundos se denominan tiempo de espera de la sesión.

Si la aplicación web no declara el final de las actividades de usuario por sí misma (mediante una llamada a la función `engagement.agent.endActivity`), el servidor de Mobile Engagement hará que la sesión del usuario expire automáticamente en los tres minutos posteriores al cierre de la página de la aplicación. Esto se denomina tiempo de espera del servidor.

### `Crash`
No se crean informes automatizados de excepciones de JavaScript no detectadas de forma predeterminada. No obstante, puede notificar bloqueos manualmente mediante la función `sendCrash` (consulte la sección sobre informes de bloqueos).

## Informes sobre actividades
La creación de informes sobre la actividad del usuario incluye cuando un usuario inicia una nueva actividad y cuando el usuario finaliza la actividad actual.

### El usuario inicia una nueva actividad
    engagement.agent.startActivity("MyUserActivity");

Debe llamar a `startActivity()` cada vez que cambie la actividad de usuario. La primera llamada a esta función inicia una nueva sesión de usuario.

### El usuario finaliza la actividad actual
    engagement.agent.endActivity();

Cuando el usuario finaliza su última actividad, debe llamar a `endActivity()` al menos una vez. Esto informa al SDK web de Mobile Engagement que el usuario está inactivo y que la sesión de usuario debe cerrarse después de que expire el tiempo de espera de la sesión. Si llama a `startActivity()` antes de que expire el tiempo de espera de la sesión, simplemente se reanudará la sesión.

Como no hay ninguna llamada confiable cuando se cierra la ventana del navegador, a menudo resulta difícil o imposible detectar el final de las actividades de usuario dentro de un entorno web. Por esta razón, el servidor de Mobile Engagement hace que la sesión de usuario expire automáticamente en los tres minutos posteriores al cierre de la página de la aplicación.

## Informes de eventos
La creación de informes sobre eventos incluye los eventos de sesión y los eventos independientes.

### Eventos de sesión
Los eventos de sesión se suelen usar para notificar las acciones que realiza el usuario durante la sesión de usuario.

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
A diferencia de los eventos de sesión, los eventos independientes pueden producirse fuera del contexto de una sesión.

Por ello, utilice ``engagement.agent.sendEvent`` en lugar de ``engagement.agent.sendSessionEvent``.

## Informes de errores
La creación de informes sobre errores incluye los errores de sesión y los errores independientes.

### Errores de sesión
Los errores de sesión se usan normalmente para notificar los errores que tienen un impacto en el usuario durante la sesión de usuario.

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
A diferencia de los errores de sesión, los errores independientes pueden producirse fuera del contexto de una sesión.

Por ello, utilice `engagement.agent.sendError` en lugar de `engagement.agent.sendSessionError`.

## Informes de trabajos
La creación de informes sobre trabajos incluye la notificación de errores y eventos que se producen durante un trabajo y la notificación de bloqueos.

**Ejemplo:**

Si desea supervisar una solicitud AJAX, utilizaría lo siguiente:

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

### Informes de errores durante un trabajo
Los errores pueden estar relacionados con un trabajo en ejecución en lugar de la sesión del usuario actual.

**Ejemplo:**

Si desea notificar que se ha producido un error en una solicitud de AJAX:

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

### Informes de eventos durante un trabajo
Los eventos pueden estar relacionados con un trabajo en ejecución en lugar de la sesión del usuario actual gracias a la función `engagement.agent.sendJobEvent`.

Esta función se comporta exactamente como `engagement.agent.sendJobError`.

### Informes de bloqueos
Utilice la función `sendCrash` para notificar bloqueos manualmente.

El argumento `crashid` es una cadena que identifica el tipo de bloqueo. El argumento `crash` es normalmente el seguimiento de la pila del bloqueo en forma de cadena.

    engagement.agent.sendCrash(crashid, crash);

## Parámetros adicionales
Puede adjuntar datos arbitrarios a un evento, error, actividad o trabajo.

Estos datos pueden ser cualquier objeto JSON (pero no de tipo primitivo ni de matriz).

**Ejemplo:**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### Límites
Los límites que se aplican a los parámetros adicionales están en las áreas de expresiones regulares para claves, tipos de valor y tamaño.

#### simétricas
Cada clave del objeto debe coincidir con la siguiente expresión regular:

    ^[a-zA-Z][a-zA-Z_0-9]*

Esto significa que las claves deben empezar con al menos una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### Valores
Los valores solo pueden ser de tipo booleano, de número y de cadena.

#### Tamaño
Los extras están limitados a 1024 caracteres por llamada (después de que el SDK web de Mobile Engagement lo codifique en JSON).

## Información de la aplicación de informes
Puede notificar manualmente la información de seguimiento (o cualquier otro tipo de información específica de la aplicación) mediante la función `sendAppInfo()`.

Tenga en cuenta que esta información se puede enviar de forma incremental. Solo se conservará el último valor de una clave específica para un dispositivo determinado.

Al igual que los extras de evento, puede utilizar cualquier objeto JSON para resumir la información de la aplicación. Tenga en cuenta que las matrices o subobjetos se tratarán como cadenas sin formato (con la serialización de JSON).

**Ejemplo:**

Este es un ejemplo de código para enviar el sexo del usuario y la fecha de nacimiento:

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### Límites
Los límites que se aplican a la información de la aplicación están en las áreas de expresiones regulares para claves y tamaño.

#### simétricas
Cada clave del objeto debe coincidir con la siguiente expresión regular:

    ^[a-zA-Z][a-zA-Z_0-9]*

Esto significa que las claves deben empezar con al menos una letra, seguida de letras, dígitos o caracteres de subrayado (\_).

#### Tamaño
La información de la aplicación está limitada a 1024 caracteres por llamada (después de que el SDK web de Mobile Engagement lo codifique en JSON).

En el ejemplo anterior, el JSON que se envía al servidor tiene una longitud de 44 caracteres:

    {"birthdate":"1983-12-07","gender":"female"}

<!---HONumber=AcomDC_0713_2016-->