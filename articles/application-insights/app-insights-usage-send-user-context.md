---
title: "Envío de id. de contexto de usuario para habilitar experiencias de uso en Azure Application Insights | Microsoft Docs"
description: "Realice un seguimiento de cómo los usuarios navegan por el servicio mediante la asignación a cada uno de ellos de una cadena de identificador única y persistente en Application Insights."
services: application-insights
documentationcenter: 
author: abgreg
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: article
ms.date: 08/02/2017
ms.author: mbullwin
ms.openlocfilehash: fe4481cf851fc021b3073a6d9d6254f546218785
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
#  <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Enviar identificadores contextuales de usuario para habilitar las experiencias de uso en Azure Application Insights

## <a name="tracking-users"></a>Seguimiento de usuarios

Application Insights le permite supervisar y realizar un seguimiento de los usuarios a través de un conjunto de herramientas de uso del producto: 
* [Usuarios, sesiones, eventos](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
* [Embudos](https://docs.microsoft.com/azure/application-insights/usage-funnels)
* [Retención](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention)
* Cohortes
* [Libros](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Para realizar un seguimiento de lo que hace un usuario a lo largo del tiempo, Application Insights necesita un identificador para cada usuario o sesión. Incluya los siguientes identificadores en cada evento personalizado o vista de página.
- Usuarios, embudos, retención y cohortes: incluya el identificador de usuario.
- Sesiones: incluya el identificador de sesión.

Si la aplicación se integra con el [SDK de JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript#set-up-application-insights-for-your-web-page), se podrá hacer el seguimiento del identificador de usuario de forma automática.

## <a name="choosing-user-ids"></a>Elección de los identificadores de usuario

Los identificadores de usuario se deben conservar entre las sesiones de usuario para realizar un seguimiento de cómo se comportan los usuarios con el tiempo. Hay varios enfoques para conservar el identificador.
- Una definición de un usuario que ya tiene en su servicio.
- Si el servicio tiene acceso a un explorador, puede pasar a este una cookie con un identificador en ella. El identificador se conservará siempre que la cookie permanezca en el explorador del usuario.
- Si es necesario, puede usar un nuevo identificador en cada sesión, pero los resultados sobre los usuarios serán limitados. Por ejemplo, no podrá ver cómo cambia el comportamiento de un usuario con el tiempo.

El identificador debe ser un GUID u otra cadena lo suficientemente compleja como para identificar de forma única a cada usuario. Por ejemplo, podría ser un número aleatorio largo.

Si el identificador contiene información de identificación personal sobre el usuario, no es un valor adecuado para enviarlo a Application Insights como identificador de usuario. Puede enviar un identificador de ese tipo como [identificador de usuario autenticado](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users), pero no cumplirá el requisito de identificador de usuario para los escenarios de uso.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>Aplicaciones ASP.NET: Establecer el contexto de usuario en un inicializador de telemetría

Cree un inicializador de telemetría, tal y como se describe detalladamente [aquí](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer) y establezca el Context.User.Id y el Context.Session.Id.

Este ejemplo establece el identificador de usuario en un identificador que expira después de la sesión. Si es posible, use un identificador de usuario que se conserve entre sesiones.

```csharp

    using System;
    using System.Web;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that sets the user ID.
       *
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            // For a full experience, track each user across sessions. For an incomplete view of user 
            // behavior within a session, store user ID on the HttpContext Session.
            // Set the user ID if we haven't done so yet.
            if (HttpContext.Current.Session["UserId"] == null)
            {
                HttpContext.Current.Session["UserId"] = Guid.NewGuid();
            }

            // Set the user id on the Application Insights telemetry item.
            telemetry.Context.User.Id = (string)HttpContext.Current.Session["UserId"];

            // Set the session id on the Application Insights telemetry item.
            telemetry.Context.Session.Id = HttpContext.Current.Session.SessionID;
        }
      }
    }
```

## <a name="next-steps"></a>pasos siguientes
- Para habilitar las experiencias de uso, empiece por enviar [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) o [vistas de páginas](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Si ya ha enviado eventos personalizados o vistas de página, explore las herramientas de uso para obtener información sobre cómo los usuarios utilizan el servicio.
    * [Información general del uso](app-insights-usage-overview.md)
    * [Usuarios, sesiones y eventos](app-insights-usage-segmentation.md)
    * [Embudos](usage-funnels.md)
    * [Retención](app-insights-usage-retention.md)
    * [Libros](app-insights-usage-workbooks.md)
