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

Comience aquí a obtener todos los detalles sobre cómo integrar Azure Mobile Engagement en una aplicación de web. Si primero quiere probarlo, asegúrese de seguir nuestro [tutorial de 15 minutos](mobile-engagement-web-app-get-started.md)

## Procedimientos de integración
1. Comience aquí: [How to integrate Mobile Engagement in your Web app](mobile-engagement-web-integrate-engagement.md) (Cómo integrar Mobile Engagement en su aplicación web)

2. Implementación del plan de etiquetas: [How to use the advanced Mobile Engagement tagging API in your Web app](mobile-engagement-web-use-engagement-api.md) (Uso de la API de etiquetado avanzado de Mobile Engagement en su aplicación de web)

## Notas de la versión

### 2\.0.1 (06/10/2016)

-   SDK deshabilitado en IE8 y IE9.
-   Detección fija de explorador web Opera.

Para todas las versiones, consulte [todas las notas de la versión](mobile-engagement-web-release-notes.md).

## Procedimientos de actualización

### De 1.2.1 a 2.0.0

A continuación se describe cómo migrar una integración del SDK desde el servicio Capptain que ofrece Capptain SAS en una aplicación con la tecnología de Azure Mobile Engagement. Si va a migrar desde una versión anterior, consulte el sitio web de Capptain para migrar a 1.2.1 en primer lugar y luego aplique el siguiente procedimiento.

Esta versión del SDK web de Engagement no es compatible con samsung-tv, OperaTV, webOS ni la característica de cobertura.

>[AZURE.IMPORTANT] Capptain y Mobile Engagement no son los mismos servicios, y el procedimiento que se indica a continuación destaca únicamente cómo migrar la aplicación cliente. La migración del SDK en la aplicación NO migrará los datos desde los servidores Capptain a los servidores Mobile Engagement.

#### Archivos de JavaScript

Reemplace el archivo `capptain-sdk.js` por el archivo `azure-engagement.js` y actualice las importaciones de scripts como corresponda.

#### Eliminación de la cobertura de Capptain

Esta versión del SDK web de Engagement no es compatible con la característica de cobertura, de modo que, si ha integrado la cobertura de Capptain en su aplicación, deberá quitarla.

Quite la importación de css de Cobertura de su página y elimine el archivo css relacionado (de forma predeterminada, capptain-reach.css).

Elimine los recursos de cobertura: la imagen de cierre (de forma predeterminada, capptain-close.png) y el icono de marca (de forma predeterminada, capptain-notification-icon).

Quite la interfaz de usuario de Cobertura para las notificaciones en aplicación. El diseño predeterminado será similar al siguiente:

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

Quite la interfaz de usuario de cobertura de los anuncios y sondeos web y de texto. El diseño predeterminado será similar al siguiente:

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

Quite de la configuración cualquier objeto `reach`, que será similar al siguiente:

	window.capptain = {
	  [...]
	  reach: {
	    [...]
	  }
	}

Quite cualquier otra personalización de cobertura, como las categorías.

#### Eliminación de las API en desuso

Algunas de las API de Capptain están en desuso en la versión de Engagement del SDK.

Quite cualquier llamada a las API siguientes: `agent.connect`, `agent.disconnect`, `agent.pause` y `agent.sendMessageToDevice`.

Quite las siguientes devoluciones de llamada, si las hubiera, de la configuración de Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived` y `onPushMessageReceived`.

#### Configuración

Engagement ahora usa una cadena de conexión para configurar los identificadores del SDK, como el identificador de aplicación.

Reemplace el identificador de aplicación por la cadena de conexión y tenga en cuenta también que el objeto global de la configuración del SDK se trasladará de `capptain` a `azureEngagement`.

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

Se ha cambiado el nombre del objeto JavaScript global `window.capptain` a `window.azureEngagement`, pero puede usar el alias `window.engagement` para las llamadas API (este alias no se puede utilizar para definir la configuración del SDK).

Por ejemplo: `capptain.deviceId` se convierte en `engagement.deviceId`, `capptain.agent.startActivity` se convierte en `engagement.agent.startActivity`, etc.

Si ya ha integrado una versión anterior de nuestro SDK en su aplicación, consulte [Procedimientos de actualización](mobile-engagement-web-upgrade-procedure.md).

<!---HONumber=AcomDC_0615_2016-->