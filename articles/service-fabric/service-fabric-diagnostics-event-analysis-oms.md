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
ms.openlocfilehash: f0cefab15a115719ea9c378546a7e6004bd06187
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
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

Hay dos formas de aprovisionar y configurar el área de trabajo de OMS, bien a través de una plantilla de Resource Manager o directamente desde Azure Marketplace. Use la primera opción al implementar un clúster y la última si ya tiene un clúster implementado con Diagnostics habilitado.

### <a name="deploying-oms-using-a-resource-management-template"></a>Implementación de OMS con una plantilla de Resource Manager

Al implementar un clúster con una plantilla de Resource Manager, esta última también puede crear una nueva área de trabajo de OMS, agregar a ella la solución de Service Fabric y configurarla para leer datos de las tablas de almacenamiento apropiadas.

>[!NOTE]
>Para ello, Diagnostics tiene que estar habilitado para que haya tablas de almacenamiento de Azure en las que OMS o Log Analytics puedan leer información.

[Aquí](https://azure.microsoft.com/resources/templates/service-fabric-oms/) hay una plantilla de ejemplo que puede usar y modificar según sea necesario, donde se realizan las acciones anteriores. En caso de que desee considerar otras alternativas, existen algunas plantillas más que ofrecen diferentes opciones en función del punto en que se encuentre en el proceso de configuración de un área de trabajo de OMS; se encuentran disponibles en la página de [plantillas de Service Fabric y OMS](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

### <a name="deploying-oms-using-through-azure-marketplace"></a>Implementación de OMS a través de Azure Marketplace

Si desea agregar un área de trabajo de OMS después de haber implementado un clúster, diríjase a Azure Marketplace y busque *"Service Fabric Analytics"*.

![SF Analytics en OMS con Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

* Haga clic en **Crear**.
* En la ventana de creación de Service Fabric Analytics, haga clic en **Seleccione un área de trabajo** para el campo *Área de trabajo de OMS* y haga clic en **Create a new workspace** (Crear un área de trabajo). Rellene las entradas necesarias; el único requisito en este caso es que la suscripción del clúster de Service Fabric sea la misma que la del área de trabajo de OMS. Después de haber validado las entradas, el área de trabajo de OMS comenzará la implementación. Esto solo tardará unos minutos.
* Cuando termine, haga clic en **Crear** de nuevo en la parte inferior de la ventana de creación de Service Fabric Analytics. Asegúrese de que la nueva área de trabajo aparece en *Área de trabajo de OMS*. Esto agregará la solución al área de trabajo que acaba de crear.


Aunque así se agrega la solución al área de trabajo, el área de trabajo debe conectarse a los datos de diagnóstico del clúster. Vaya al grupo de recursos donde creó la solución Service Fabric Analytics. Debería ver *ServiceFabric (\<nombreDelÁreaDeTrabajoDeOMS\>)*.

* Haga clic en la solución para la página de introducción, desde donde podrá cambiar la configuración de la solución y del área de trabajo, y vaya al portal de OMS.
* En el menú de navegación izquierdo, haga clic en **Registros de las cuentas de almacenamiento**, de *Orígenes de datos del área de trabajo*.

    ![Solución Service Fabric Analytics en el portal](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-portal.png)

* En la página *Registros de las cuentas de almacenamiento*, haga clic en **Agregar** en la parte superior para agregar los registros del clúster al área de trabajo.
* Haga clic en **cuenta de Storage** para agregar la cuenta correspondiente que se creó en el clúster. Si utiliza el nombre predeterminado, la cuenta de Storage se denomina *sfdg\<resourceGroupName\>*. También puede confirmarlo al comprobar la plantilla de Azure Resource Manager utilizada para implementar el clúster; para ello, compruebe el valor de `applicationDiagnosticsStorageAccountName`. Quizá tenga que desplazarse hacia abajo y hacer clic en **Cargar más**, si no aparece el nombre de la cuenta. Haga clic en el nombre de cuenta de almacenamiento correcto cuando aparezca para seleccionarlo.
* A continuación tendrá que especificar el *Tipo de datos*, que debe ser **Eventos de Service Fabric**.
* El *Origen* se establecerá automáticamente en *WADServiceFabric\*EventTable*.
* Haga clic en **Aceptar** para conectar el área de trabajo a los registros del clúster.

    ![Incorporación de registros de la cuenta de Storage a OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

* La cuenta debería aparecer ahora como parte de los *registros de la cuenta de Storage* en los orígenes de datos del área de trabajo.

Con esto ya se ha agregado la solución Service Fabric Analytics a un área de trabajo de OMS Log Analytics correctamente conectada ahora a la plataforma del clúster y a la tabla de registros de la aplicación. Puede agregar orígenes adicionales al área de trabajo de la misma manera.

## <a name="using-the-oms-agent"></a>Uso del agente de OMS

Se recomienda utilizar EventFlow y WAD como soluciones de agregación porque permiten adoptar un enfoque más modular de diagnóstico y supervisión. Por ejemplo, si desea cambiar las salidas de EventFlow, no es necesario realizar ningún cambio en la instrumentación real, sino tan solo una sencilla modificación del archivo de configuración. No obstante, si decide invertir en el uso de OMS y desea continuar utilizándolo para análisis de eventos (no tiene que ser la única plataforma que utilice, pero sí una de las que use), se recomienda explorar la configuración del [agente de OMS](../log-analytics/log-analytics-windows-agents.md). También debe utilizar al agente de OMS al implementar contenedores en el clúster, tal y como se comenta a continuación.

El proceso para hacer esto es relativamente fácil, ya que solo tiene que agregar el agente como extensión del conjunto de escalado de máquinas virtuales a la plantilla de Resource Manager, asegurándose de que se instala en cada uno de los nodos. Para clústeres que ejecutan [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) o [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux), puede encontrar una plantilla de ejemplo de Resource Manager que implementa el área de trabajo de OMS con la solución Service Fabric (como se ha explicado anteriormente) y agrega el agente a los nodos.

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