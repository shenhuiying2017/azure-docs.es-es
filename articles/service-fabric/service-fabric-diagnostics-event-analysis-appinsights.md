---
title: "Análisis de eventos de Azure Service Fabric con Application Insights | Microsoft Docs"
description: "Obtenga información sobre cómo visualizar y analizar eventos con Application Insights para la supervisión y el diagnóstico de clústeres de Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 34f14f42150e46edae2d1352827f96a411117a62
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Análisis y visualización de eventos con Application Insights

Azure Application Insights es una plataforma extensible para la supervisión y el diagnóstico de aplicaciones. Incluye una sólida herramienta de análisis y consulta, visualizaciones y paneles personalizables y otras opciones, como las alertas automáticas. Se trata de la plataforma recomendada para la supervisión y el diagnóstico de servicios y aplicaciones de Service Fabric.

## <a name="setting-up-application-insights"></a>Configuración de Application Insights

### <a name="creating-an-ai-resource"></a>Creación de un recurso de AI

Para crear un recurso de AI, vaya a Azure Marketplace y busque "Application Insights". Debería aparecer como la primera solución (se encuentra en la categoría "Web + Mobile"). Haga clic en **Crear** cuando encuentre el recurso adecuado (confirme que la ruta de acceso coincide con la imagen siguiente).

![Nuevo recurso de Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/create-new-ai-resource.png)

Debe rellenar algunos datos para aprovisionar el recurso correctamente. En el campo *Tipo de aplicación*, use "Aplicación web ASP.NET" si va a usar cualquiera de los modelos de programación de Service Fabric o si va a publicar una aplicación .NET en el clúster. Use "General" si va a implementar contenedores y ejecutables de invitado. En general, debe usar el parámetro predeterminado "Aplicación web ASP.NET" para mantener las opciones abiertas en el futuro. El nombre depende de su preferencia, y el grupo de recursos y la suscripción se pueden modificar después de la implementación del recurso. Se recomienda que el recurso de AI esté en el mismo grupo de recursos que el clúster. Si necesita más información, vea [Creación de recursos en Application Insights](../application-insights/app-insights-create-new-resource.md).

Necesita la clave de instrumentación de AI para configurar el recurso de AI con la herramienta de agregación de eventos. Cuando el recurso de AI ya esté configurado (tarda unos minutos después de que se valida la implementación), vaya a dicho recurso y busque la sección **Propiedades** en la barra de navegación izquierda. Se abre una hoja nueva en la que aparece una *CLAVE DE INSTRUMENTACIÓN*. Si necesita cambiar la suscripción o el grupo de recursos del recurso, estas tareas también se pueden realizar aquí.

### <a name="configuring-ai-with-wad"></a>Configuración de AI con WAD

>[!NOTE]
>Solo se aplica a los clústeres de Windows por ahora.

Hay dos métodos principales para enviar datos de WAD a AI de Azure, algo que se consigue mediante la agregación de un receptor de AI a la configuración de WAD, como se detalla en [este artículo](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Agregar una clave de instrumentación de AI al crear un clúster en Azure Portal

![Adición de una clave de AI](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Si se activa "Diagnostics" al crear un clúster, aparecerá un campo opcional para escribir una clave de instrumentación de Application Insights. Si pega aquí la clave de instrumentación de AI, el receptor de AI se configurará automáticamente en la plantilla de Resource Manager utilizada para implementar el clúster.

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>Adición del receptor de AI a la plantilla de Resource Manager

En el archivo "WadCfg" de la plantilla de Resource Manager, agregue un "receptor" mediante la introducción de estos dos cambios:

1. Agregue la configuración del receptor directamente después de la confirmación de que `DiagnosticMonitorConfiguration` se ha completado:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Incluya el receptor en `DiagnosticMonitorConfiguration`; para ello, agregue la siguiente línea en `DiagnosticMonitorConfiguration` de `WadCfg` (justo antes de que se confirmen `EtwProviders`):

    ```json
    "sinks": "applicationInsights"
    ```

En los dos fragmentos de código anteriores, el nombre "applicationInsights" se usó para describir el receptor. No se trata de un requisito y, siempre que el nombre del receptor se incluya en "sinks", puede establecer el nombre en cualquier cadena.

Actualmente, los registros del clúster se mostrarán como seguimientos en el Visor de registros de AI. Como la mayoría de los seguimientos de la plataforma son de tipo "Información", también puede considerar la opción de cambiar la configuración del receptor para que solo envíe registros de tipo "Crítico" o "Error". Esto puede realizarse mediante la adición de "canales" al receptor, como se demuestra en [este artículo](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

>[!NOTE]
>Si usa una clave de instrumentación de AI incorrecta en el portal o en la plantilla de Resource Manager, deberá cambiar manualmente la clave y actualizar el clúster o volver a implementarlo. 

### <a name="configuring-ai-with-eventflow"></a>Configuración de AI con EventFlow

Si va a usar EventFlow para agregar eventos, asegúrese de importar el paquete NuGet `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`. Lo siguiente debe incluirse en la sección *outputs* de *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        // (replace the following value with your AI resource's instrumentation key)
        "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
]
```

Asegúrese de realizar los cambios necesarios en los filtros, así como de incluir cualquier otra entrada (junto con sus respectivos paquetes NuGet).

## <a name="aisdk"></a>AI.SDK

Por lo general, se recomienda utilizar EventFlow y WAD como soluciones de agregación, porque permiten adoptar un enfoque más modular para diagnósticos y supervisión, es decir, si desea cambiar las salidas de EventFlow, no es necesario realizar ningún cambio en la instrumentación real, sino simplemente una modificación sencilla en el archivo de configuración. Sin embargo, si decide invertir en la utilización de Application Insights y no existe ninguna probabilidad de cambiar a una plataforma distinta, debe considerar la utilización de un SDK nuevo de AI para agregar eventos y enviarlos a AI. Esto significa que ya no tendrá que configurar EventFlow para enviar datos a AI, sino que tendrá que instalar el paquete NuGet de Service Fabric en ApplicationInsight. Puede encontrar información detallada sobre el paquete [aquí](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

En [Application Insights support for Microservices and Containers](https://azure.microsoft.com/app-insights-microservices/) (Application Insights admite microservicios y contenedores), se muestran algunas de las nuevas características en las que se está trabajando (todavía en versión beta), lo que permite disponer de opciones de supervisión de serie enriquecidas con AI. Entre ellas se incluyen la supervisión de dependencias (se usan para compilar una instancia de AppMap de todos los servicios y aplicaciones de un clúster y la comunicación entre ellos) y una correlación mejorada de los seguimientos procedentes de los servicios (facilita la detección de un problema en el flujo de trabajo de una aplicación o de un servicio).

Si va a desarrollar en .NET, existe la probabilidad de usar algunos de los modelos de programación de Service Fabric y desea usar AI como la plataforma para visualizar y analizar datos de eventos y de registro, se recomienda seguir la ruta del SDK de AI como flujo de trabajo de supervisión y diagnóstico. Lea [esto](../application-insights/app-insights-asp-net-more.md) y [esto](../application-insights/app-insights-asp-net-trace-logs.md) a fin de usar AI para recopilar y visualizar los registros.

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Navegación por el recurso de AI en Azure Portal

Después de haber configurado AI como una salida para los eventos y los registros, debe empezar a aparecer información en el recurso de AI en unos minutos. Navegue hasta el recurso AI, que lo llevará al panel de recursos de AI. Haga clic en **Buscar** en la barra de tareas de AI para ver los últimos seguimientos que ha recibido y para poder filtrarlos.

El *Explorador de métricas* es una herramienta útil para crear paneles personalizados basados en métricas sobre las que las aplicaciones, los servicios y el clúster pueden informar. Vea [Exploración de métricas en Application Insights](../application-insights/app-insights-metrics-explorer.md) para configurar algunos gráficos por su cuenta en función de los datos que recopile.

Al hacer clic en **Analytics**, se le remitirá al portal de Analytics en Application Insights, donde puede consultar eventos y seguimientos con un ámbito más amplio y más opciones. Lea más información en [Analytics en Application Insights](../application-insights/app-insights-analytics.md).

## <a name="next-steps"></a>Pasos siguientes

* [Definición de alertas en Application Insights](../application-insights/app-insights-alerts.md) para recibir notificaciones sobre los cambios de rendimiento o de uso
* [Detección inteligente en Application Insights](../application-insights/app-insights-proactive-diagnostics.md), donde se realiza un análisis proactivo de la telemetría enviada a AI para avisar de problemas de rendimiento potenciales