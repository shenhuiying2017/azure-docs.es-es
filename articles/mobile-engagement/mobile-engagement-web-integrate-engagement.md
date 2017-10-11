---
title: "Integración del SDK web de Azure Mobile Engagement | Microsoft Docs"
description: "Actualizaciones y procedimientos más recientes para el SDK web de Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: b5daa2a2-942b-489d-aa1d-568c3b25e56f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 02/29/2016
ms.author: piyushjo
ms.openlocfilehash: 7d8eaa180e277741a583522ee62d68f5247b92bb
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="integrate-azure-mobile-engagement-in-a-web-application"></a>Integración de Azure Mobile Engagement en una aplicación web
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

En este procedimiento se describe la manera más sencilla de activar las funciones de análisis y supervisión de Azure Mobile Engagement en su aplicación web.

Siga los pasos para activar los informes de registro necesarios para calcular todas las estadísticas acerca de los usuarios, las sesiones, las actividades, los bloqueos y los aspectos técnicos. Para las estadísticas que dependen de la aplicación, tales como eventos, errores y trabajos, debe activar los informes de registro manualmente mediante la API de Azure Mobile Engagement. Para más información, aprenda a [usar la API de etiquetado de Mobile Engagement en una aplicación web](mobile-engagement-web-use-engagement-api.md).

## <a name="introduction"></a>Introducción
[Descargue el SDK web de Azure Mobile Engagement](http://aka.ms/P7b453).
El SDK web de Mobile Engagement se distribuye como un único archivo de JavaScript llamado azure-engagement.js, que se debe incluir en cada página del sitio o aplicación web.

> [!IMPORTANT]
> Antes de ejecutar este script, debe ejecutar un fragmento de código o script que escriba para configurar Mobile Engagement para su aplicación.
> 
> 

## <a name="browser-compatibility"></a>Compatibilidad con el explorador
El SDK web de Mobile Engagement usa codificación y descodificación JSON nativas, además de solicitudes AJAX entre dominios (basadas en la especificación CORS del W3C). Es compatible con los siguientes exploradores:

* Microsoft Edge 12+
* Internet Explorer 10+
* Firefox 3.5+
* Chrome 4+
* Safari 6+
* Opera 12+

## <a name="configure-mobile-engagement"></a>Configuración de Mobile Engagement
Escriba un script que cree un objeto JavaScript `azureEngagement` global como en el siguiente ejemplo. Como el sitio puede tener varias páginas, en el ejemplo se da por hecho que el script se incluye en cada página. En el ejemplo, el objeto JavaScript se llama `azure-engagement-conf.js`.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

El valor de `connectionString` para la aplicación se muestra en el Portal de Azure.

> [!NOTE]
> `appVersionName` y `appVersionCode` son opcionales. Sin embargo, se recomienda configurarlos para que el análisis pueda procesar la información acerca de la versión.
> 
> 

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>Incorporación de scripts de Mobile Engagement en las páginas
Agregue scripts de Mobile Engagement a las páginas de una de las siguientes maneras:

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

O bien:

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias
Una vez cargado el script del SDK web de Mobile Engagement, crea el alias **engagement** para obtener acceso a las API del SDK. Este alias no se puede usar para definir la configuración del SDK. El alias se usa como referencia en esta documentación.

Tenga en cuenta que si el alias predeterminado entra en conflicto con otra variable global de la página, puede volver a definirlo en la configuración antes de cargar el SDK web de Mobile Engagement, de la siguiente manera:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>Informes básicos
Los informes básicos de Mobile Engagement incluyen estadísticas de nivel de sesión, tales como estadísticas sobre usuarios, sesiones, actividades y bloqueos.

### <a name="session-tracking"></a>Seguimiento de sesión
Las sesiones de Mobile Engagement se dividen en secuencias de actividades que se identifican por el nombre.

En un sitio web clásico, se recomienda declarar una actividad diferente en cada página del sitio. Para un sitio web o aplicación web en el que la página actual nunca cambia, puede realizar un seguimiento de las actividades en una escala menor, por ejemplo, dentro de la página.

En cualquier caso, para iniciar o cambiar la actividad del usuario actual, llame a la función `engagement.agent.startActivity` . Por ejemplo:

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

El servidor de Mobile Engagement finaliza una sesión abierta 3 minutos después de que se cierre la página de la aplicación.

También puede finalizar una sesión manualmente mediante una llamada a `engagement.agent.endActivity`. Esto establece la actividad del usuario actual en ‘Idle’ (inactiva).  La sesión finalizará 10 segundos después, a menos que una nueva llamada a `engagement.agent.startActivity` reanude la sesión.

Puede configurar el retraso de 10 segundos en el objeto engagement global, de la siguiente manera:

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [!NOTE]
> No puede usar `engagement.agent.endActivity` en la devolución de llamada `onunload` porque no se pueden realizar llamadas AJAX en esta etapa.
> 
> 

## <a name="advanced-reporting"></a>Informes avanzados
De manera opcional, si desea notificar eventos, errores y trabajos específicos de la aplicación, deberá usar la API de Mobile Engagement. Se obtiene acceso a la API de Mobile Engagement mediante el objeto `engagement.agent` .

Puede acceder a todas las funcionalidades avanzadas de Mobile Engagement en la API de Mobile Engagement. La API se describe con más detalle en el artículo sobre [cómo usar la API de etiquetado avanzada de Mobile Engagement en una aplicación web](mobile-engagement-web-use-engagement-api.md).

## <a name="customize-the-urls-used-for-ajax-calls"></a>Direcciones URL personalizadas para las llamadas AJAX
Puede personalizar las direcciones URL que usa el SDK web de Mobile Engagement. Por ejemplo, para volver a definir la dirección URL de registro (punto de conexión del SDK para el registro), puede invalidar la configuración de la siguiente manera:

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

Si las funciones de la dirección URL devuelven una cadena que empieza por `/`, `//`, `http://` o `https://`, no se usa el esquema predeterminado. De forma predeterminada, para esas direcciones URL se usa el esquema `https://` . Si quiere personalizar el esquema predeterminado, invalide la configuración de la siguiente manera:

    window.azureEngagement = {
      ...
      urls: {
        ...         
        scheme: '//'
      }
    };
