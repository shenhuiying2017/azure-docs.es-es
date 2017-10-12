---
title: "Procedimientos de actualización del SDK web de Azure Mobile Engagement | Microsoft Docs"
description: "Actualizaciones y procedimientos más recientes para el SDK web para Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a20529b4-ec8d-4503-8ae9-09b5f0846d5b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: afa8037dcb7a53042fa606e2c4014b442d4be326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Procedimientos de actualización del SDK web de Azure Mobile Engagement
Si ya ha integrado una versión anterior del SDK web de Azure Mobile Engagement en su aplicación web, debe considerar los siguientes puntos al actualizar el SDK.

Si omite varias versiones del SDK web de Mobile Engagement, quizá deba completar varios procedimientos durante el proceso de actualización. Por ejemplo, si migra de la versión 1.4.0 a la versión 1.6.0, primero siga los procedimientos para actualizar de la versión 1.4.0 a la versión 1.5.0. A continuación, siga los procedimientos para actualizar de la versión 1.5.0 a la versión 1.6.0.

Independientemente de la versión desde la que actualice, sustituya cualquier versión anterior del archivo azure-engagement.js por la versión más reciente del archivo.

## <a name="upgrade-from-121-to-200"></a>Actualizar desde 1.2.1 a 2.0.0
En esta sección se describe cómo migrar una integración de SDK web de Mobile Engagement desde el servicio de Capptain, ofrecido por Capptain SAS, a una aplicación de Azure Mobile Engagement. Si va a migrar desde una versión anterior, consulte el sitio web de Capptain para migrar a 1.2.1 en primer lugar y luego aplique el siguiente procedimiento.

Esta versión del SDK web de Mobile Engagement no es compatible con Samsung Smart TV, Opera TV, webOS o la característica Reach.

> [!IMPORTANT]
> Capptain y Azure Mobile Engagement no son el mismo servicio. El procedimiento siguiente destaca únicamente cómo migrar la aplicación cliente. La migración del SDK web de Mobile Engagement en la aplicación no migrará los datos desde un servidor Capptain a un servidor Mobile Engagement.
> 
> 

### <a name="javascript-files"></a>Archivos de JavaScript
Reemplace el archivo capptain-sdk.js por el archivo azure-engagement.js y luego actualice las importaciones del script en consecuencia.

### <a name="remove-capptain-reach"></a>Eliminación de la cobertura de Capptain
Esta versión del SDK web de Mobile Engagement no admite la característica Reach. Si integró Capptain Reach en su aplicación, debe quitarlo.

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

### <a name="remove-deprecated-apis"></a>Eliminación de las API en desuso
Algunas API de Capptain están en desuso en el SDK web de Mobile Engagement.

Quite cualquier llamada a las API siguientes: `agent.connect`, `agent.disconnect`, `agent.pause` y `agent.sendMessageToDevice`.

Quite las instancias de las siguientes devoluciones de llamada de la configuración de Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived` y `onPushMessageReceived`.

### <a name="configuration"></a>Configuración
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

### <a name="javascript-apis"></a>API de JavaScript
El objeto global de JavaScript `window.capptain` ha cambiado de nombre a `window.azureEngagement`, pero puede usar el alias `window.engagement` para las llamadas de API. No se puede utilizar el alias para definir la configuración del SDK.

Por ejemplo: `capptain.deviceId` se convierte en `engagement.deviceId`, `capptain.agent.startActivity` se convierte en `engagement.agent.startActivity`, etc.

