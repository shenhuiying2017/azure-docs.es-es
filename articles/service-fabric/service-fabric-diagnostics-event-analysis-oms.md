---
title: "Análisis de eventos de Azure Service Fabric con OMS | Microsoft Docs"
description: "Obtenga información sobre cómo visualizar y analizar eventos con OMS para la supervisión y el diagnóstico de clústeres de Azure Service Fabric."
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
ms.openlocfilehash: 977c5d64a32157b39aa6b618196dde20c4c3cc8e
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="event-analysis-and-visualization-with-oms"></a>Análisis y visualización de eventos con OMS

Operations Management Suite (OMS) es una colección de servicios de administración que simplifican la supervisión y el diagnóstico de aplicaciones y servicios hospedados en la nube. Para obtener información general más detallada sobre OMS y lo que ofrece, vea [¿Qué es OMS?](../operations-management-suite/operations-management-suite-overview.md)

## <a name="log-analytics-and-the-oms-workspace"></a>Log Analytics y el área de trabajo de OMS

Log Analytics recopila datos de recursos administrados, incluidos un agente o una tabla de almacenamiento de Azure, y los mantiene en un repositorio central. Estos datos pueden utilizarse posteriormente para análisis, alertas, visualizaciones y tareas ulteriores de exportación. Log Analytics admite eventos, datos de rendimiento u otros datos personalizados.

Cuando se configura OMS, podrá acceder a un *área de trabajo OMS* determinada, donde se pueden consultar o visualizar los datos en paneles.

Después de que Log Analytics recibe los datos, OMS dispone de varias *soluciones de administración* que son soluciones preempaquetadas para supervisar los datos entrantes, personalizados para varios escenarios. Puede tratarse de una solución de *Service Fabric Analytics* y una solución de *Containers*, que son las dos opciones más importantes para diagnosticar y supervisar el uso de los clústeres de Service Fabric. Hay otras que merece la pena examinar, y OMS también permite crear soluciones personalizadas, sobre las que puede leer más información [aquí](../operations-management-suite/operations-management-suite-solutions.md). Cada solución que decida usar para un clúster se puede configurar en la misma área de trabajo de OMS, junto con Log Analytics. Las áreas de trabajo admiten los paneles de trabajo y la visualización de datos, y permiten realizar modificaciones en los datos que desea recopilar, procesar y analizar.

## <a name="setting-up-an-oms-workspace-with-the-service-fabric-analytics-solution"></a>Configuración de un área de trabajo de OMS con la solución Service Fabric Analytics
Se recomienda que incluya la solución de Service Fabric en el área de trabajo de OMS, incluye un panel útil donde se muestran los distintos canales de registro de entrada desde el nivel de la aplicación y la plataforma, además de permitir realizar consultas de registros específicos de Service Fabric. A continuación se muestra la apariencia de una solución Service Fabric relativamente sencilla, con una única aplicación implementada en el clúster:

![Solución SF en OMS](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

Vea [Configuración de Log Analytics (OMS)](service-fabric-diagnostics-oms-setup.md) para empezar a usar esto para el clúster.

## <a name="using-the-oms-agent"></a>Uso del agente de OMS

Se recomienda utilizar EventFlow y WAD como soluciones de agregación porque permiten adoptar un enfoque más modular de diagnóstico y supervisión. Por ejemplo, si desea cambiar las salidas de EventFlow, no es necesario realizar ningún cambio en la instrumentación real, sino tan solo una sencilla modificación del archivo de configuración. Si, sin embargo, decide invertir en usar Log Analytics de OMS, debe configurar el [Agente de OMS](../log-analytics/log-analytics-windows-agent.md). También debe utilizar al agente de OMS al implementar contenedores en el clúster, tal y como se comenta a continuación. 

Vaya a [Incorporación del Agente de OMS a un clúster](service-fabric-diagnostics-oms-agent.md) para seguir los pasos correspondientes.

Las ventajas que esto aporta son las siguientes:

* Datos enriquecidos sobre las métricas y los contadores de rendimiento.
* Se recopilan del clúster métricas que se configuran fácilmente y sin necesidad de actualizar la configuración de este. Se pueden realizar cambios en la configuración del agente desde el portal de OMS; el agente se reiniciará automáticamente para que coincida la configuración necesaria. Para configurar el agente de OMS para seleccionar contadores de rendimiento específicos, vaya al área de trabajo **Inicio > Configuración > Datos > Contadores de rendimiento de Windows** y seleccione los datos que desea recopilar.
* Los datos se muestran más rápido que si se tuvieran que almacenar antes de que OMS o Log Analytics los seleccionen.
* La supervisión de contenedores es mucho más sencilla, ya que puede seleccionar registros de Docker (como stdout o stderr) y estadísticas (métricas de rendimiento de los niveles de contenedor y nodo).

Lo primero que hay que considerar en este caso es que como el agente se implementará en el clúster junto con las aplicaciones, puede afectar al rendimiento de estas.

## <a name="monitoring-containers"></a>Supervisión de contenedores

Al implementar contenedores en un clúster de Service Fabric, se recomienda que el clúster se configure con el agente de OMS y que la solución Containers se agregue al área de trabajo de OMS para habilitar la supervisión y el diagnóstico. Este es el aspecto de la solución Containers en un área de trabajo:

![Panel de OMS básico](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

El agente habilita la recopilación de varios registros específicos de contenedores que se pueden consultar en OMS o se usan para los indicadores de rendimiento visualizados. Los tipos de registro que se recopilan son:

* ContainerInventory: muestra información acerca de la ubicación, nombre e imágenes del contenedor
* ContainerImageInventory: información acerca de las imágenes implementadas, lo que incluye sus identificadores o tamaños
* ContainerLog: registros de error concretos, registros de docker (stdout, etc.) y otras entradas
* ContainerServiceLog: comandos de demonio de docker que se han ejecutado
* Perf: contadores de rendimiento, entre los que se incluyen la cpu del contenedor, la memoria, el tráfico de red, la e/s de disco y métricas personalizadas de los equipos host

En el artículo [Supervisión de contenedores con Log Analytics de OMS](service-fabric-diagnostics-oms-containers.md) se tratan los pasos necesarios para configurar la supervisión del contenedor para el clúster. Para más información acerca de la solución Containers de OMS, consulte su [documentación](../log-analytics/log-analytics-containers.md).

## <a name="next-steps"></a>Pasos siguientes

Explore las siguientes herramientas y opciones de OMS para personalizar un área de trabajo en función de sus necesidades:

* Si se trata de clústeres locales, OMS ofrece una puerta de enlace (proxy de reenvío HTTP) que puede usarse para enviar datos a OMS. Obtenga más información en [Conexión de equipos sin acceso a Internet a OMS mediante OMS Gateway](../log-analytics/log-analytics-oms-gateway.md).
* Configure OMS para configurar [alertas automáticas](../log-analytics/log-analytics-alerts.md) que ayuden en la detección y el diagnóstico.
* Familiarícese con las funciones de [búsqueda de registros y consulta](../log-analytics/log-analytics-log-searches.md) que se ofrecen como parte de Log Analytics