---
title: "Evaluación de aplicaciones de Service Fabric con Log Analytics en Azure Portal | Microsoft Docs"
description: "Puede usar la solución de Service Fabric con Log Analytics en Azure Portal para evaluar el riesgo y el estado de las aplicaciones, los microservicios, los nodos y los clústeres de Service Fabric."
services: log-analytics
documentationcenter: 
author: niniikhena
manager: jochan
editor: 
ms.assetid: 9c91aacb-c48e-466c-b792-261f25940c0c
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: nini
ms.openlocfilehash: 8c564c0dcbb2f9be286917b2f4d8a40da5406fae
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="assess-service-fabric-applications-and-micro-services-with-the-azure-portal"></a>Evaluación de aplicaciones y microservicios de Service Fabric con Azure Portal

> [!div class="op_single_selector"]
> * [Resource Manager](log-analytics-service-fabric-azure-resource-manager.md)
> * [PowerShell](log-analytics-service-fabric.md)
>
>

![Símbolo de Service Fabric](./media/log-analytics-service-fabric/service-fabric-assessment-symbol.png)

En este artículo se describe cómo utilizar la solución de Service Fabric en Log Analytics para ayudar a identificar y solucionar problemas en su clúster de Service Fabric.

La solución de Service Fabric utiliza datos de Diagnósticos de Azure de las máquinas virtuales de Service Fabric recopilando estos datos de las tablas WAD de Azure. Posteriormente, Log Analytics lee los eventos del marco de Service Fabric, incluidos los **eventos de servicios de confianza**, los **eventos de actor**, los **eventos operativos** y los **eventos ETW personalizados**. Con el panel de la solución, se pueden ver los eventos pertinentes y problemas importantes de su entorno de Service Fabric.

Para empezar a trabajar con la solución, debe conectar su clúster de Service Fabric a un área de trabajo de Log Analytics. Hay tres escenarios principales que se han de tener en cuenta:

1. Si no ha implementado su clúster de Service Fabric, siga los pasos del artículo sobre cómo ***implementar un clúster de Service Fabric conectado a un área de trabajo de Log Analytics*** para implementar un nuevo clúster y configúrelo para que envíe informes a Log Analytics.
2. Si tiene que recopilar contadores de rendimiento de los hosts para usar otras soluciones de OMS como la de seguridad en su clúster de Service Fabric, siga los pasos del artículo sobre cómo ***implementar un clúster de Service Fabric conectado a un área de trabajo de Log Analytics con la extensión de máquina virtual instalada***.
3. Si ya ha implementado el clúster de Service Fabric y quiere conectarse a Log Analytics, siga los pasos del artículo sobre cómo ***agregar una cuenta de almacenamiento existente a Log Analytics***.

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Implemente un clúster de Service Fabric conectado a un área de trabajo de Log Analytics.
Esta plantilla hace lo siguiente:

1. Implementa un clúster de Service Fabric de Azure ya conectado a un área de trabajo de Log Analytics. Tiene la opción de crear una nueva área de trabajo durante la implementación de la plantilla, o especificar el nombre de un área de trabajo de Log Analytics ya existente.
2. Agrega la cuenta de almacenamiento de información de diagnóstico al área de trabajo de Log Analytics.
3. Permite usar la solución de Service Fabric en el área de trabajo de Log Analytics.

[![Implementación en Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

Cuando selecciona el botón Implementar anterior, Azure Portal se abre con parámetros que puede editar. Asegúrese de crear un nuevo grupo de recursos si escribe un nuevo nombre de área de trabajo de Log Analytics:

![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

Acepte los términos legales y haga clic en **Crear** para iniciar la implementación. Una vez completada la implementación, debería ver la nueva área de trabajo y el clúster creado, así como las tablas WADServiceFabric*Event, WADWindowsEventLogs y WADETWEvent agregadas:

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace-with-vm-extension-installed"></a>Implemente un clúster de Service Fabric conectado a un área de trabajo de Log Analytics con la extensión de máquina virtual instalada.

Esta plantilla hace lo siguiente:

1. Implementa un clúster de Service Fabric de Azure ya conectado a un área de trabajo de Log Analytics. Puede crear una tabla o usar una existente.
2. Agrega las cuentas de almacenamiento de información de diagnóstico al área de trabajo de Log Analytics.
3. Permite usar la solución de Service Fabric en el área de trabajo de Log Analytics.
4. Instala la extensión del agente de MMA en cada conjunto de escalado de máquinas virtuales del clúster de Service Fabric. Con el agente de MMA instalado, puede ver las métricas de rendimiento de los nodos.

[![Implementación en Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

Siguiendo los mismos pasos, escriba los parámetros necesarios e inicie una implementación. De nuevo, verá creados la nueva área de trabajo, el clúster y las tablas WAD:

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>Visualización de datos de rendimiento

Para ver datos de rendimiento de los nodos, siga estos pasos:


[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

- Inicie el área de trabajo de Log Analytics desde Azure Portal.
  ![Service Fabric](./media/log-analytics-service-fabric/6.png)
- Vaya a la configuración del panel izquierdo y seleccione Datos >> Contadores de rendimiento de Windows >> Agregar los contadores de rendimiento seleccionados: ![Service Fabric](./media/log-analytics-service-fabric/7.png)
- En Búsqueda de registros, utilice las siguientes consultas para profundizar en las métricas clave de los nodos:

    a. Compare el uso medio de CPU en todos los nodos en la última hora para ver los que tienen problemas y el intervalo de tiempo en que un nodo tenía un pico:

    ```
    Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    b. Vea gráficos de líneas similares para la memoria disponible en cada nodo con esta consulta:

    ```
    Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    Para ver una lista de todos los nodos, que muestra el valor medio exacto de MB disponibles para cada nodo, utilice esta consulta:

    ```
    Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)

    c. En el caso de que desee ver los detalles de un nodo específico examinando el promedio mínimo, máximo y percentil 75 por hora de uso de CPU, puede hacerlo mediante esta consulta (sustituya el campo Equipo):

    ```
    Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

Lea más información sobre las métricas de rendimiento de Log Analytics en el [blog de Operations Management Suite](https://blogs.technet.microsoft.com/msoms/tag/metrics/).


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>Adición de una cuenta de almacenamiento existente a Log Analytics

Esta plantilla simplemente agrega las cuentas de almacenamiento existentes a un área de trabajo de Log Analytics nueva o existente.

[![Implementación en Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> Al seleccionar un grupo de recursos, si está trabajando con un área de trabajo de Log Analytics ya existente, seleccione "Usar existente" y busque el grupo de recursos que contiene el área de trabajo de Log Analytics. En caso contrario, cree uno nuevo.
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
>
>

Una vez implementada esta plantilla, podrá ver la cuenta de almacenamiento conectada a su área de trabajo de Log Analytics. En este caso, agregamos otra cuenta de almacenamiento al área de trabajo de Exchange que creó anteriormente.
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Visualización de eventos de Service Fabric

Una vez que se completan las implementaciones y se ha habilitado la solución de Service Fabric en el área de trabajo, seleccione el icono de **Service Fabric** en el portal de Log Analytics para iniciar el panel de Service Fabric. El panel incluye las columnas de la tabla siguiente. Cada columna muestra los 10 principales eventos por recuento que coinciden con los criterios de esa columna para el intervalo de tiempo especificados. Puede ejecutar una búsqueda de registros que proporcione toda la lista haciendo clic en **Ver todo** en la parte inferior derecha de la columna o haciendo clic en el encabezado de columna.

| **Evento de Service Fabric** | **descripción** |
| --- | --- |
| Problemas importantes |Una pantalla con problemas como RunAsyncFailures RunAsynCancellations y nodos inactivos. |
| Eventos operativos |Eventos operativos importantes como implementaciones y actualizaciones de aplicaciones. |
| Eventos de servicios de confianza |Eventos de servicios de confianza importantes como Runasyncinvocations. |
| Eventos de actor |Eventos de actor importantes generados por los microservicios, como las excepciones que inician un método de actor, activaciones y desactivaciones de actor, etc. |
| Eventos de aplicación |Todos los eventos ETW personalizados generados por las aplicaciones. |

![Panel de Service Fabric](./media/log-analytics-service-fabric/sf3.png)

![Panel de Service Fabric](./media/log-analytics-service-fabric/sf4.png)

La siguiente tabla muestra los métodos de recolección de datos y otros detalles sobre cómo se recopilan los datos para Service Fabric.

| plataforma | Agente directo | Agente de Operations Manager | Almacenamiento de Azure | ¿Se requiere Operations Manager? | Se envían los datos del agente de Operations Manager a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  |  | &#8226; |  |  |10 minutos |

> [!NOTE]
> Puede cambiar el ámbito de estos eventos en la solución de Service Fabric haciendo clic **Data based on last 7 days** (Datos basados en los últimos 7 días) en la parte superior del panel. También puede mostrar los eventos generados en los últimos 7 días, 1 día o 6 horas, o bien seleccionar **Personalizado** y especificar un intervalo de fechas personalizado.
>
>

## <a name="next-steps"></a>Pasos siguientes

* Use [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para ver datos detallados sobre los datos de eventos de Service Fabric.
