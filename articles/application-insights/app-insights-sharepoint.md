---
title: "Supervisión de un sitio de SharePoint con Application Insights"
description: "Inicio de la supervisión de una nueva aplicación con una nueva clave de instrumentación"
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: 2bfe5910-d673-4cf6-a5c1-4c115eae1be0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/24/2016
ms.author: mbullwin
ms.openlocfilehash: 9c07ba125e0f9eae2b8f94661abf6dc1efc0cdad
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Supervisión de un sitio de SharePoint con Application Insights
Azure Application Insights le permite supervisar la disponibilidad, el rendimiento y el uso de sus aplicaciones. Aquí aprenderá a configurarlo para un sitio de SharePoint.

## <a name="create-an-application-insights-resource"></a>Creación de recursos en Application Insights
En el [portal de Azure](https://portal.azure.com), cree un nuevo recurso de Application Insights. Elija ASP.NET como el tipo de aplicación.

![Haga clic en Propiedades, seleccione la clave y presione ctrl + C.](./media/app-insights-sharepoint/01-new.png)

En la hoja que se abre podrá ver los datos de uso y rendimiento sobre la aplicación. Para volver a ella la próxima vez que inicie sesión en Azure, encontrará un icono correspondiente en la pantalla de inicio. También puede hacer clic en Examinar para buscarla.

## <a name="add-our-script-to-your-web-pages"></a>Agregar nuestro script a las páginas web
En Inicio rápido, obtenga el script para páginas web:

![](./media/app-insights-sharepoint/02-monitor-web-page.png)

Inserte el script justo antes de la etiqueta &lt;/head&gt; de cada página que desea seguir. Si su sitio web tiene una página maestra, puede colocar el script allí. Por ejemplo, en un proyecto de ASP.NET MVC, lo colocaría en View\Shared\\_Layout.cshtml.

El script contiene la clave de instrumentación que dirige los datos de telemetría al recurso de Application Insights.

### <a name="add-the-code-to-your-site-pages"></a>Agregar el código a las páginas del sitio
#### <a name="on-the-master-page"></a>En la página maestra
Si se puede editar la página maestra del sitio, dispondrá de supervisión para cada página del sitio.

Desproteja y edite la página maestra mediante SharePoint Designer o cualquier otro editor.

![](./media/app-insights-sharepoint/03-master.png)

Agregue el código justo antes de la etiqueta </head> . 

![](./media/app-insights-sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>O, en páginas individuales
Para supervisar un conjunto limitado de páginas, agregue el script por separado a cada página. 

Inserte un elemento web e incruste el fragmento de código en él.

![](./media/app-insights-sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Visualización de los datos de la aplicación
Vuelva a implementar la aplicación.

Vuelva a la hoja de la aplicación en el [Portal de Azure](https://portal.azure.com).

Los primeros eventos aparecerán en Búsqueda. 

![](./media/app-insights-sharepoint/09-search.png)

Si espera más datos, haga clic en Actualizar después de unos segundos.

En la hoja de introducción, haga clic en **Análisis de uso** para ver a los gráficos de los usuarios, sesiones y vistas de páginas:

![](./media/app-insights-sharepoint/06-usage.png)

Haga clic en cualquier gráfico para ver más detalles: por ejemplo, Vistas de página:

![](./media/app-insights-sharepoint/07-pages.png)

O Usuarios:

![](./media/app-insights-sharepoint/08-users.png)

## <a name="capturing-user-id"></a>Captura del identificador de usuario
El fragmento de código de una página web estándar no captura el identificador de usuario de SharePoint, pero una pequeña modificación le permitirá hacerlo.

1. Copie la clave de instrumentación de la aplicación de la lista desplegable Essentials en Application Insights . 

    ![](./media/app-insights-sharepoint/02-props.png)

1. En el siguiente fragmento, sustituya la clave de instrumentación por "XXXX". 
2. Inserte el script en la aplicación de SharePoint en lugar del fragmento de código que obtenga desde el portal.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>Pasos siguientes
* [Pruebas web](app-insights-monitor-web-app-availability.md) para supervisar la disponibilidad de su sitio.
* [Application Insights](app-insights-overview.md) para otros tipos de aplicación.

<!--Link references-->


