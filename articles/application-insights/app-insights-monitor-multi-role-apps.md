---
title: Compatibilidad de Azure Application Insights con varios roles, microservicios y contenedores | Microsoft Docs
description: "Supervisión de aplicaciones que constan de varios componentes o roles para uso y rendimiento."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: dad17277452081427a01d077128a3a137b2190f5
ms.contentlocale: es-es
ms.lasthandoff: 05/17/2017


---
# <a name="monitor-multi-role-applications-with-application-insights-preview"></a>Supervisión de aplicaciones de varios roles con Application Insights (versión preliminar)

Puede supervisar aplicaciones que constan de varios componentes, roles o servicios con [Azure Application Insights](app-insights-overview.md). El mantenimiento de los roles y las relaciones entre ellos se muestran en una sola asignación de aplicaciones. Puede realizar el seguimiento de operaciones individuales por medio de varios roles con correlación HTTP automática. El diagnóstico de contenedor se puede integrar y correlacionar con telemetría de la aplicación. Utilice un único recurso de Application Insights para todos los roles de la aplicación. 

![Asignación de aplicaciones de varios roles](./media/app-insights-monitor-multi-role-apps/app-map.png)

Aquí se usa "rol" en un sentido amplio para indicar cualquier componente de aplicación o servicio que se compila como proyecto independiente. Por ejemplo, una aplicación empresarial típica puede constar de varios roles que se comunican a través de una API de REST. Los roles se pueden hospedar en contenedores como Docker o Service Fabric, o en hosts en la nube o locales. 

### <a name="sharing-a-single-application-insights-resource"></a>Uso compartido de un único recurso de Application Insights 

La técnica clave aquí es enviar telemetría desde cada rol de la aplicación al mismo recurso de Application Insights, pero usar la propiedad `cloud_RoleName` para distinguir entre roles cuando sea necesario. 

En algunos casos, puede que esto no sea adecuado y quizás prefiera usar recursos independientes para distintos grupos de roles. Por ejemplo, podría tener que usar recursos diferentes para administración o facturación. El uso de recursos independientes significa que no ve todos los roles en una sola asignación de aplicaciones y que no puede consultar en todos los roles de [Analytics](app-insights-analytics.md). También tendrá que configurar los recursos independientes.

Con dicha advertencia, se da por supuesto en el resto de este documento que desea enviar datos desde varios roles a un recurso de Application Insights.

## <a name="configure-multi-role-applications"></a>Configuración de aplicaciones de varios roles

Para obtener una asignación de aplicaciones de varios roles, debe lograr estos objetivos:

* **Instale la versión preliminar más reciente** del paquete de Application Insights en cada rol de la aplicación. 
* **Comparta un único recurso de Application Insights** para todos los roles de la aplicación.
* **Habilite Asignación de aplicaciones de varios roles** en la hoja Versiones preliminares.

Configure cada rol de la aplicación con el método adecuado para su tipo. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md))

### <a name="1-install-the-latest-pre-release-package"></a>1. Instalación del paquete de versión preliminar más reciente

Actualice o instale los paquetes de Appication Insights en el proyecto para cada rol. Si usa Visual Studio:

1. Haga clic con el botón derecho en un proyecto y seleccione **Administrar paquetes NuGet**. 
2. Seleccione **Incluir versión preliminar**.
3. Si los paquetes de Application Insights aparecen en Actualizaciones, selecciónelos. 

    De lo contrario, busque e instale el paquete adecuado:
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric: para los roles que se ejecutan como archivos ejecutables invitados y contenedores de Docker que se ejecutan en una aplicación de Service Fabric
    * Microsoft.ApplicationInsights.ServiceFabric.Native: para instancias de Reliable Services en las aplicaciones de Service Fabric
    * Microsoft.ApplicationInsights.Kubernetes: para roles que se ejecutan en Docker en Kubernetes

### <a name="2-share-a-single-application-insights-resource"></a>2. Uso compartido de un único recurso de Application Insights

* En Visual Studio, haga clic con el botón derecho en un proyecto y elija **Configurar Application Insights** o **Application Insights > Configurar**. Para el primer proyecto, use el asistente para crear un recurso de Application Insights. Para proyectos posteriores, seleccione el mismo recurso.
* Si no hay ningún menú Application Insights, configúrelo manualmente:

   1. En [Azure Portal](https://portal,azure.com), abra el recurso de Application Insights que ya creó para otro rol.
   2. En la hoja Información general, abra la pestaña desplegable Essentials y copie el valor de **Clave de instrumentación**.
   3. En el proyecto, abra ApplicationInsights.config e inserte: `<InstrumentationKey>your copied key</InstrumentationKey>`

![Copiar la clave de instrumentación en el archivo .config](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-multi-role-application-map"></a>3. Habilitación de Asignación de aplicaciones de varios roles

En Azure Portal, abra el recurso de la aplicación. En la hoja Versiones preliminares, habilite *Asignación de aplicaciones de varios roles*.

### <a name="4-enable-docker-metrics-optional"></a>4. Habilitación de las métricas de Docker (opcional) 

Si un rol se ejecuta en un contenedor de Docker hospedado en una máquina virtual Windows de Azure, puede recopilar estadísticas adicionales desde el contenedor. Inserte esto en el archivo de configuración de [Azure Diagnostics](../monitoring-and-diagnostics/azure-diagnostics.md):

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

## <a name="use-cloudrolename-to-separate-roles"></a>Uso de cloud_RoleName para separar roles

La propiedad `cloud_RoleName` está conectada a toda la telemetría. Identifica el rol o servicio que origina la telemetría. (No es lo mismo que cloud_RoleInstance, que separa roles idénticos que se ejecutan en paralelo en varios procesos de servidor o máquinas).

En el portal, puede filtrar o segmentar la telemetría mediante esta propiedad. En este ejemplo, la hoja Errores se filtra para mostrar solo información del servicio web front-end y oculta errores de la API de CRM de back-end:

![Gráfico de métricas segmentadas por Nombre del rol en la nube](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-roles"></a>Seguimiento de operaciones entre roles

Puede realizar el seguimiento de las llamadas de un servicio a otro realizadas durante el procesamiento de una operación individual.


![Mostrar telemetría para la operación](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Haga clic para acceder a una lista correlacionada de telemetría para esta operación en el servidor web front-end y la API de back-end:

![Búsqueda en roles](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Pasos siguientes

* [Separación de telemetría de desarrollo, prueba y producción](app-insights-separate-resources.md)

