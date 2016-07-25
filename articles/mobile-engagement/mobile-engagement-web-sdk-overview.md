<properties
	pageTitle="Información general de SDK web de Azure Mobile Engagement | Microsoft Azure"
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


# SDK web para Azure Mobile Engagement

Comience aquí a obtener todos los detalles sobre cómo integrar Azure Mobile Engagement en una aplicación web. Si desea probarlo antes de comenzar con su propia aplicación web, consulte nuestro [tutorial de 15 minutos](mobile-engagement-web-app-get-started.md).

## Procedimientos de integración
1. Aprenda a [integrar Mobile Engagement en su aplicación web](mobile-engagement-web-integrate-engagement.md).

2. Para la implementación del plan de etiquetas: aprenda a [usar la API de etiquetado avanzado de Mobile Engagement en su aplicación web](mobile-engagement-web-use-engagement-api.md).

## Notas de la versión

### 2\.0.1 (6/10/2016)

-   Se ha deshabilitado el SDK web para Mobile Engagement en Internet Explorer 8 e Internet Explorer 9.
-   Se ha reparado la detección del explorador web Opera.

Para todas las versiones, consulte [todas las notas de la versión](mobile-engagement-web-release-notes.md).

## Procedimientos de actualización

### Actualizar desde 1.2.1 a 2.0.0

En las siguientes secciones se describe cómo migrar una integración de SDK web de Mobile Engagement desde el servicio de Capptain, ofrecido por Capptain SAS, a una aplicación de Azure Mobile Engagement. Si va a migrar desde una versión anterior a la 1.2.1, consulte el sitio web de Capptain para migrar a 1.2.1 en primer lugar y luego aplique los siguientes procedimientos.

Esta versión del SDK web de Mobile Engagement no es compatible con Samsung Smart TV, Opera TV, webOS o la característica Reach.

>[AZURE.IMPORTANT] Capptain y Azure Mobile Engagement no son el mismo servicio, y los procedimientos siguientes destacan únicamente cómo migrar la aplicación cliente. La migración del SDK web de Mobile Engagement en la aplicación no migrará los datos desde un servidor Capptain a un servidor Mobile Engagement.

#### Archivos de JavaScript

Reemplace el archivo capptain-sdk.js por el archivo azure-engagement.js y luego actualice las importaciones del script en consecuencia.

#### Eliminación de la cobertura de Capptain

Esta versión del SDK web de Mobile Engagement no admite la característica Reach. Si ha integrado Capptain Reach en su aplicación, debe quitarlo.

Quite la importación de CSS de Reach de su página y elimine el archivo .css relacionado (de forma predeterminada, capptain-reach.css).

Elimine los siguientes recursos de Reach: la imagen de cierre (de forma predeterminada, capptain-close.png) y el icono de marca (de forma predeterminada, capptain-notification-icon).

Quite la interfaz de usuario de Reach para las notificaciones de la aplicación. El diseño predeterminado tiene el siguiente aspecto:

	<!-- capptain notification -->
	<div id="capptain_notification_area" class="capptain_category_default">
	  <div class="icon">
	    <img src="capptain-notification-icon.png" alt="icon" />
	  </div>
	  <div class="content">
	    <div class="title" id="capptain_notification_title"></div>
	    <div class="message" id="capptain_notification_message"></div>
	  </div>
	  <div id="capptain_notification_image"></div>
	  <div>
	    <button id="capptain_notification_close">Close</button>
	  </div>
	</div>

Quite el texto, los anuncios web y los sondeos de la interfaz de usuario de Reach. El diseño predeterminado tiene el siguiente aspecto:

	<div id="capptain_overlay" class="capptain_category_default">
	  <button id="capptain_overlay_close">x</button>
	  <div id="capptain_overlay_title"></div>
	  <div id="capptain_overlay_body"></div>
	  <div id="capptain_overlay_poll"></div>
	  <div id="capptain_overlay_buttons">
	    <button id="capptain_overlay_exit"></button>
	    <button id="capptain_overlay_action"></button>
	  </div>
	</div>

Quite el objeto `reach` de la configuración, si existe. Su aspecto es similar a este:

	window.capptain = {
	  [...]
	  reach: {
	    [...]
	  }
	}

Quite cualquier otra personalización de Reach, como las categorías.

#### Eliminación de las API en desuso

Algunas API de Capptain están en desuso en el SDK web de Mobile Engagement.

Quite cualquier llamada a las API siguientes: `agent.connect`, `agent.disconnect`, `agent.pause` y `agent.sendMessageToDevice`.

Quite todas las siguientes devoluciones de llamada de la configuración de Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived` y `onPushMessageReceived`.

#### Configuración

Mobile Engagement usa una cadena de conexión para configurar los identificadores del SDK, como el identificador de aplicación.

Reemplace el identificador de la aplicación por la cadena de conexión. Tenga en cuenta que el objeto global para la configuración del SDK cambia de `capptain` a `azureEngagement`.

Antes de la migración:

	window.capptain = {
	  appId: ...,
	  [...]
	};

Después de la migración:

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  [...]
	};

La cadena de conexión de la aplicación se muestra en el Portal de Azure.

#### API de JavaScript

El objeto global de JavaScript `window.capptain` ha cambiado de nombre a `window.azureEngagement`, pero puede usar el alias `window.engagement` para las llamadas de API. No se puede utilizar este alias para definir la configuración del SDK.

Por ejemplo: `capptain.deviceId` se convierte en `engagement.deviceId`, `capptain.agent.startActivity` se convierte en `engagement.agent.startActivity`, etc.

Si ya ha integrado una versión anterior del SDK web de Azure Mobile Engagement en su aplicación, consulte los [procedimientos de actualización](mobile-engagement-web-upgrade-procedure.md).

<!---HONumber=AcomDC_0713_2016-->