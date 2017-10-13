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
ms.date: 05/26/2017
ms.author: dekapur
ms.openlocfilehash: f52988f2a67f2cafe93a7e0a358c599658d068b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="event-analysis-and-visualization-with-oms"></a>Análisis y visualización de eventos con OMS

Operations Management Suite (OMS) es una colección de servicios de administración que simplifican la supervisión y el diagnóstico de aplicaciones y servicios hospedados en la nube. Para obtener información general más detallada sobre OMS y lo que ofrece, vea [¿Qué es OMS?](../operations-management-suite/operations-management-suite-overview.md)

## <a name="log-analytics-and-the-oms-workspace"></a>Log Analytics y el área de trabajo de OMS

Log Analytics recopila datos de recursos administrados, incluidos un agente o una tabla de almacenamiento de Azure, y los mantiene en un repositorio central. Estos datos pueden utilizarse posteriormente para análisis, alertas, visualizaciones y tareas ulteriores de exportación. Log Analytics admite eventos, datos de rendimiento u otros datos personalizados.

Cuando se configura OMS, podrá acceder a un *área de trabajo OMS* determinada, donde se pueden consultar o visualizar los datos en paneles.

Después de que Log Analytics recibe los datos, OMS dispone de varias *soluciones de administración* que son soluciones preempaquetadas para supervisar los datos entrantes, personalizados para varios escenarios. Puede tratarse de una solución de *Service Fabric Analytics* y una solución de *Containers*, que son las dos opciones más importantes para diagnosticar y supervisar el uso de los clústeres de Service Fabric. Hay otras que merece la pena examinar, y OMS también permite crear soluciones personalizadas, sobre las que puede leer más información [aquí](../operations-management-suite/operations-management-suite-solutions.md). Cada solución que decida usar para un clúster se configurará en la misma área de trabajo de OMS, junto con Log Analytics. Las áreas de trabajo admiten los paneles de trabajo y la visualización de datos, y permiten realizar modificaciones en los datos que desea recopilar, procesar y analizar.

## <a name="setting-up-an-oms-workspace-with-the-service-fabric-solution"></a>Configuración de un área de trabajo de OMS con la solución Service Fabric

Se recomienda que incluya la solución Service Fabric en el área de trabajo de OMS, ya que ofrece un panel útil donde se muestran los distintos canales de registro de entrada desde el nivel de infraestructura y aplicación, además de permitir realizar consultas de registros específicos de Service Fabric. A continuación se muestra la apariencia de una solución Service Fabric relativamente sencilla, con una única aplicación implementada en el clúster:

![Solución SF en OMS](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

Hay dos formas de aprovisionar y configurar el área de trabajo de OMS, bien a través de una plantilla de Resource Manager o directamente desde Azure Marketplace. Use la primera opción al implementar un clúster y la última si ya tiene un clúster implementado con Diagnostics habilitado.

### <a name="deploying-oms-using-a-resource-management-template"></a>Implementación de OMS con una plantilla de Resource Manager

Esto ocurre en la fase de creación del clúster; al implementar un clúster con una plantilla de Resource Manager, esta última también puede crear una nueva área de trabajo de OMS, agregar a ella la solución Service Fabric y configurarla para leer datos de las tablas de almacenamiento apropiadas.

>[!NOTE]
>Para ello, Diagnostics tiene que estar habilitado para que haya tablas de almacenamiento de Azure en las que OMS o Log Analytics puedan leer información.

[Aquí](https://azure.microsoft.com/resources/templates/service-fabric-oms/) hay una plantilla de ejemplo que puede usar y modificar según sea necesario, donde se realizan las acciones anteriores. En caso de que desee considerar otras alternativas, existen algunas plantillas más que ofrecen diferentes opciones en función del punto en que se encuentre en el proceso de configuración de un área de trabajo de OMS; se encuentran disponibles en la página de [plantillas de Service Fabric y OMS](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

### <a name="deploying-oms-using-through-azure-marketplace"></a>Implementación de OMS a través de Azure Marketplace

Si desea agregar un área de trabajo de OMS después de haber implementado un clúster, diríjase a Azure Marketplace y busque *"Service Fabric Analytics"*. Solo debe aparecer un único recurso, en la categoría "Supervisión y administración", como se muestra a continuación:

![SF Analytics en OMS con Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

Al hacer clic en **Crear**, se le pedirá que especifique un área de trabajo de OMS. Haga clic en **Seleccione un área de trabajo** y después en **Crear un área de trabajo**. Rellene las entradas necesarias; el único requisito en este caso es que la suscripción del clúster de Service Fabric sea la misma que la del área de trabajo de OMS. Después de haber validado las entradas, el área de trabajo de OMS se implementará en unos minutos. Mientras finaliza la implementación, la hoja para crear la solución Service Fabric permanecerá abierta. Asegúrese de que la misma área de trabajo aparece en *Área de trabajo de OMS* y haga clic en **Crear** en la parte inferior, para agregar la solución Service Fabric al área de trabajo.

## <a name="using-the-oms-agent"></a>Uso del agente de OMS

Se recomienda utilizar EventFlow y WAD como soluciones de agregación porque permiten adoptar un enfoque más modular de diagnóstico y supervisión. Por ejemplo, si desea cambiar las salidas de EventFlow, no es necesario realizar ningún cambio en la instrumentación real, sino tan solo una sencilla modificación del archivo de configuración. No obstante, si decide invertir en el uso de OMS y desea continuar utilizándolo para análisis de eventos (no tiene que ser la única plataforma que utilice, pero sí una de las que use), se recomienda explorar la configuración del [agente de OMS](../log-analytics/log-analytics-windows-agents.md). También debe utilizar al agente de OMS al implementar contenedores en el clúster, tal y como se comenta a continuación.

El proceso para hacer esto es relativamente fácil, ya que solo tiene que agregar el agente como extensión del conjunto de escalado de máquinas virtuales a la plantilla de Resource Manager, asegurándose de que se instala en cada uno de los nodos. Para clústeres que ejecutan [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) o [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux), puede encontrar una plantilla de ejemplo de Resource Manager que implementa el área de trabajo de OMS con la solución Service Fabric (como se ha explicado anteriormente) y agrega el agente a los nodos.

Las ventajas que esto aporta son las siguientes:

* Datos enriquecidos sobre las métricas y los contadores de rendimiento.
* Fácil configuración de los datos recopilados del clúster e introducción de cambios sencilla en dichos datos sin tener que reimplementar las aplicaciones ni el clúster, ya que los cambios de configuración del agente se pueden realizar desde el área de trabajo de OMS, y el agente se restablecerá automáticamente. Para configurar el agente de OMS para seleccionar contadores de rendimiento específicos, vaya al área de trabajo **Inicio > Configuración > Datos > Contadores de rendimiento de Windows** y seleccione los datos que desea recopilar.
* Los datos se muestran más rápido que si se tuvieran que almacenar antes de que OMS o Log Analytics los seleccionen.
* La supervisión de contenedores es mucho más sencilla, ya que puede seleccionar registros de Docker (como stdout o stderror) y estadísticas (métricas de rendimiento de los niveles de contenedor y nodos).

Lo primero que hay que considerar en este caso es que, al tratarse de un agente, se implementará en el clúster junto con todas las aplicaciones, por lo que se producirá un impacto mínimo en el rendimiento de las aplicaciones del clúster.

## <a name="monitoring-containers"></a>Supervisión de contenedores

Al implementar contenedores en un clúster de Service Fabric, se recomienda que el clúster se configure con el agente de OMS y que la solución Containers se agregue al área de trabajo de OMS para habilitar la supervisión y el diagnóstico. Este es el aspecto de la solución Containers en un área de trabajo:

![Panel de OMS básico](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

El agente habilita la recopilación de varios registros específicos de contenedores que se pueden consultar en OMS o se usan para los indicadores de rendimiento visualizados. Los tipos de registro que se recopilan son:

* ContainerInventory: muestra información acerca de la ubicación, nombre e imágenes del contenedor
* ContainerImageInventory: información acerca de las imágenes implementadas, lo que incluye sus identificadores o tamaños
* ContainerLog: registros de error concretos, registros de docker (stdout, etc.) y otras entradas
* ContainerServiceLog: comandos de demonio de docker que se han ejecutado
* Perf: contadores de rendimiento, entre los que se incluyen la cpu del contenedor, la memoria, el tráfico de red, la e/s de disco y métricas personalizadas de los equipos host

En este artículo se tratan los pasos necesarios para configurar la supervisión del contenedor para el clúster. Para más información acerca de la solución Containers de OMS, consulte su [documentación](../log-analytics/log-analytics-containers.md).

Para configurar la solución Containers en el área de trabajo, asegúrese de que el agente de OMS esté implementado en los nodos del clúster; para ello, siga los pasos mencionados anteriormente. Cuando el clúster esté listo, implemente un contenedor en él. Tenga en cuenta que la primera vez que una imagen del contenedor se implementa en un clúster, se tardan varios minutos en descargar la imagen según su tamaño.

En Azure Marketplace, busque la *solución de Supervisión de contenedores* y cree el resultado de la **solución de Supervisión de contenedores** que debería aparecer, en la categoría Supervisión y administración.

![Adición de la solución Containers](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

En el paso de creación, solicita un área de trabajo de OMS. Seleccione el que se creó con la implementación anterior. Este paso agrega una solución Containers al área de trabajo de OMS y la detecta automáticamente el agente de OMS implementado por la plantilla. El agente empezará a recopilar datos sobre los procesos de contenedores del clúster y, en menos de 15 minutos, debería ver que aparecen los datos, como se muestra en la imagen del panel anterior.


## <a name="next-steps"></a>Pasos siguientes

Explore las siguientes herramientas y opciones de OMS para personalizar un área de trabajo en función de sus necesidades:

* Si se trata de clústeres locales, OMS ofrece una puerta de enlace (proxy de reenvío HTTP) que puede usarse para enviar datos a OMS. Obtenga más información en [Conexión de equipos sin acceso a Internet a OMS mediante OMS Gateway](../log-analytics/log-analytics-oms-gateway.md).
* Configure OMS para configurar [alertas automáticas](../log-analytics/log-analytics-alerts.md) que ayuden en la detección y el diagnóstico.
* Familiarícese con las funciones de [búsqueda de registros y consulta](../log-analytics/log-analytics-log-searches.md) que se ofrecen como parte de Log Analytics