---
title: Automatizar informes personalizados con datos de Azure Application Insights
description: Automatizar informes personalizados diarios, semanales o mensuales con datos de Azure Application Insights
services: application-insights
documentationcenter: ''
author: sdash
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: sdash
ms.openlocfilehash: 804e8c7a43d1ab16d11b6075be44599b33b46a3e
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072662"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatizar informes personalizados con datos de Azure Application Insights

Los informes periódicos ayudan a mantener informado al equipo sobre cómo van sus servicios críticos para la empresa. Los desarrolladores, los equipos de DevOps o SRE y sus administradores pueden ser productivos con informes automatizados que proporcionan información de forma fiable sin necesidad de que todos los usuarios inicien sesión en el portal. Estos informes también pueden ayudar a identificar aumentos graduales de las latencias y las velocidades de carga o error que es posible que no desencadenen ninguna regla de alertas.

Cada empresa tiene sus necesidades de informes únicas, como: 

* Agregaciones concretas de percentil de métricas o métricas personalizadas en un informe.
* Tener diferentes informes para los resúmenes diarios, semanales y mensuales de datos para los distintos destinatarios.
* Segmentación por atributos personalizados como la región o el entorno. 
* Agrupar algunos recursos de IA en un único informe, incluso si pueden estar en distintas suscripciones o grupos de recursos, etc.
* Informes independientes que contienen métricas confidenciales que se envían a un público selectivo.
* Informes para las partes interesadas que es posible que no tengan acceso a los recursos del portal.

> [!NOTE] 
> El correo electrónico de resumen semanal de Application Insights no permitía ninguna personalización y se suspenderá en favor de las opciones personalizadas que se muestran a continuación. El último correo electrónico de resumen semanal se enviará el 11 de junio de 2018. Configure una de las opciones siguientes para obtener informes personalizados similares (use la consulta que se sugiere a continuación).

## <a name="to-automate-custom-report-emails"></a>Para automatizar los correos electrónicos de informe personalizados

Puede [consultar mediante programación datos de Application Insights](https://dev.applicationinsights.io/) para generar informes personalizados en una programación. Las opciones siguientes pueden ayudarle a empezar rápidamente:

* [Automatizar informes con Microsoft Flow](app-insights-automate-with-flow.md)
* [Automatizar informes con Logic Apps](automate-with-logic-apps.md)
* Use la plantilla "Resumen programado de Application Insights" de [Azure Functions](https://azure.microsoft.com/services/functions/) en el escenario Supervisión. Esta función usa SendGrid para enviar el correo electrónico. 

    ![Plantilla de Azure Functions](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Consulta de ejemplo para un correo electrónico de resumen semanal
La consulta siguiente muestra la unión de varios conjuntos de datos para un correo electrónico de resumen semanal, por ejemplo, un informe. Personalícela según sea necesario y úsela con cualquiera de las opciones enumeradas anteriormente para automatizar un informe semanal.   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

  
## <a name="next-steps"></a>Pasos siguientes

- Más información sobre cómo crear [consultas de análisis](app-insights-analytics-using.md).
- Obtenga más información sobre [consultar mediante programación los datos de Application Insights](https://dev.applicationinsights.io/).
- Más información acerca de [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
- Más información sobre [Microsoft Flow](https://ms.flow.microsoft.com).


