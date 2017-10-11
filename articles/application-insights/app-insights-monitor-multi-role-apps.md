---
title: Compatibilidad de Azure Application Insights con varios componentes, microservicios y contenedores | Microsoft Docs
description: "Supervisión de aplicaciones que constan de varios componentes o roles para uso y rendimiento."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/17/2017
ms.author: bwren
ms.openlocfilehash: ca1bb8ee886c4b4e69be9dd653d6a52b874e1f5a
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="monitor-multi-component-applications-with-application-insights-preview"></a>Supervisión de aplicaciones de varios componentes con Application Insights (versión preliminar)

Puede supervisar aplicaciones que consten de varios componentes, roles o servicios de servidor con [Azure Application Insights](app-insights-overview.md). El mantenimiento de los componentes y las relaciones entre ellos se muestran en una sola asignación de aplicaciones. Puede realizar el seguimiento de ciertas operaciones por medio de varios componentes con correlación HTTP automática. El diagnóstico de contenedor se puede integrar y correlacionar con telemetría de la aplicación. Use un único recurso de Application Insights para todos los componentes de la aplicación. 

![Asignación de aplicaciones de varios componentes](./media/app-insights-monitor-multi-role-apps/app-map.png)

El término "componente" se usa aquí para hacer referencia a cualquier parte funcional de una aplicación grande. Por ejemplo, una aplicación empresarial típica puede constar de código de cliente que se ejecuta en exploradores web y que se comunica con uno o varios servicios de aplicaciones web, que a su vez usan servicios back-end. Los componentes del servidor pueden estar hospedados de forma local o en la nube, puede ser roles de trabajo o roles web de Azure, o bien pueden ejecutarse en contenedores como Docker o Service Fabric. 

### <a name="sharing-a-single-application-insights-resource"></a>Uso compartido de un único recurso de Application Insights 

La técnica clave aquí es enviar telemetría desde cada componente de la aplicación al mismo recurso de Application Insights, pero usar la propiedad `cloud_RoleName` para distinguir entre componentes cuando sea necesario. El SDK de Application Insights agrega la propiedad `cloud_RoleName` a los componentes de telemetría para emitir. Por ejemplo, el SDK agregará un nombre de sitio web o un nombre de rol de servicio a la propiedad `cloud_RoleName`. Puede invalidar este valor con un valor de telemetryinitializer. El mapa de la aplicación utiliza la propiedad `cloud_RoleName` para identificar los componentes en el mapa.

Para más información acerca de cómo invalidar la propiedad `cloud_RoleName`, consulte [Incorporación de propiedades: ITelemetryInitializer](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer).  

En algunos casos, puede que esto no sea adecuado y quizás prefiera usar recursos independientes para distintos grupos de componentes. Por ejemplo, podría tener que usar recursos diferentes para administración o facturación. El uso de recursos independientes significa que no verá todos los componentes en una sola asignación de aplicaciones y que no puede realizar consultas entre componentes de [Analytics](app-insights-analytics.md). También tendrá que configurar los recursos independientes.

Con dicha advertencia, se da por supuesto en el resto de este documento que quiere enviar datos desde varios componentes a un recurso de Application Insights.

## <a name="configure-multi-component-applications"></a>Configuración de aplicaciones de varios componentes

Para obtener una asignación de aplicaciones de varios componentes, debe lograr estos objetivos:

* **Instale la versión preliminar más reciente** del paquete de Application Insights en cada componente de la aplicación. 
* **Comparta un único recurso de Application Insights** para todos los componentes de la aplicación.
* **Habilite Asignación de aplicaciones de varios roles** en la hoja Versiones preliminares.

Configure cada componente de la aplicación con el método adecuado para el tipo correspondiente. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md) o [JavaScript](app-insights-javascript.md)).

### <a name="1-install-the-latest-pre-release-package"></a>1. Instalación del paquete de versión preliminar más reciente

Actualice los paquetes de Application Insights o instálelos en el proyecto para cada componente del servidor. Si usa Visual Studio:

1. Haga clic con el botón derecho en un proyecto y seleccione **Administrar paquetes NuGet**. 
2. Seleccione **Incluir versión preliminar**.
3. Si los paquetes de Application Insights aparecen en Actualizaciones, selecciónelos. 

    De lo contrario, busque el paquete adecuado e instálelo:
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric: para aquellos componentes que se ejecutan como archivos ejecutables invitados y contenedores de Docker que se ejecutan en una aplicación de Service Fabric
    * Microsoft.ApplicationInsights.ServiceFabric.Native: para instancias de Reliable Services en las aplicaciones de Service Fabric
    * Microsoft.ApplicationInsights.Kubernetes: para componentes que se ejecutan en Docker en Kubernetes

### <a name="2-share-a-single-application-insights-resource"></a>2. Uso compartido de un único recurso de Application Insights

* En Visual Studio, haga clic con el botón derecho en un proyecto y elija **Configurar Application Insights** o **Application Insights > Configurar**. Para el primer proyecto, use el asistente para crear un recurso de Application Insights. Para proyectos posteriores, seleccione el mismo recurso.
* Si no hay ningún menú Application Insights, configúrelo manualmente:

   1. En [Azure Portal](https://portal,azure.com), abra el recurso de Application Insights que ya creó para otro componente.
   2. En la hoja Información general, abra la pestaña desplegable Essentials y copie el valor de **Clave de instrumentación**.
   3. En el proyecto, abra ApplicationInsights.config e inserte: `<InstrumentationKey>your copied key</InstrumentationKey>`

![Copiar la clave de instrumentación en el archivo .config](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-multi-role-application-map"></a>3. Habilitación de Asignación de aplicaciones de varios roles

En Azure Portal, abra el recurso de la aplicación. En la hoja Versiones preliminares, habilite *Asignación de aplicaciones de varios roles*.

### <a name="4-enable-docker-metrics-optional"></a>4. Habilitación de las métricas de Docker (opcional) 

Si un componente se ejecuta en un elemento de Docker hospedado en una máquina virtual Windows de Azure, puede recopilar métricas adicionales a partir del contenedor. Inserte esto en el archivo de configuración de [Azure Diagnostics](../monitoring-and-diagnostics/azure-diagnostics.md):

```
"DiagnosticMonitorConfiguration": {
        ...
        "sinks": "applicationInsights",
        "DockerSources": {
                "Stats": {
                    "enabled": true,
                    "sampleRate": "PT1M"
                }
            },
        ...
    }
    ...   
    "SinksConfig": {
        "Sink": [{
            "name": "applicationInsights",
            "ApplicationInsights": "<your instrumentation key here>"
        }]
    }
    ...
}

```

## <a name="use-cloudrolename-to-separate-components"></a>Uso de cloud_RoleName para separar componentes

La propiedad `cloud_RoleName` está conectada a toda la telemetría. Identifica el componente (el rol o servicio) que origina la telemetría. (No es lo mismo que cloud_RoleInstance, que separa roles idénticos que se ejecutan en paralelo en varios procesos de servidor o máquinas).

En el portal, puede filtrar o segmentar la telemetría mediante esta propiedad. En este ejemplo, la hoja Errores se filtra para mostrar solo información del servicio web front-end y oculta errores de la API de CRM de back-end:

![Gráfico de métricas segmentadas por Nombre del rol en la nube](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-components"></a>Seguimiento de operaciones entre componentes

Puede realizar el seguimiento de las llamadas realizadas durante el procesamiento de una operación concreta de un componente a otro.


![Mostrar telemetría para la operación](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Haga clic para acceder a una lista correlacionada de telemetría para esta operación en el servidor web front-end y la API de back-end:

![Búsqueda entre componentes](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Pasos siguientes

* [Separación de telemetría de desarrollo, prueba y producción](app-insights-separate-resources.md)
