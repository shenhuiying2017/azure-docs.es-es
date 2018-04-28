---
title: 'Azure Service Fabric: supervisión del rendimiento con Log Analytics | Microsoft Docs'
description: Obtenga información sobre cómo configurar el Agente de OMS para supervisar los contenedores y los contadores de rendimiento de los clústeres de Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: 6e1c870458f43bcc5d6d40f0e40e2b2a95bee2af
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="performance-monitoring-with-log-analytics"></a>Supervisión del rendimiento con Log Analytics

En este artículo se describen los pasos para agregar Log Analytics, también conocido como OMS, como una extensión del conjunto de escalado de máquinas virtuales al clúster y cómo conectarlo a su área de trabajo de Azure Log Analytics existente. Esto permite recopilar datos de diagnóstico sobre los contenedores, las aplicaciones y la supervisión de rendimiento. Si lo agrega como una extensión al recurso del conjunto de escalado de máquinas virtuales, Azure Resource Manager garantiza la instalación en todos los nodos, incluso cuando se ajusta la escala del clúster.

> [!NOTE]
> En este artículo se da por supuesto que tiene un área de trabajo de Azure Log Analytics que ya ha configurado. De lo contrario, vea [Configuración de Azure Log Analytics](service-fabric-diagnostics-oms-setup.md).

## <a name="add-the-agent-extension-via-azure-cli"></a>Incorporación de la extensión del Agente mediante la CLI de Azure

La mejor manera de agregar el Agente de OMS al clúster es a través de las API del conjunto de escalado de máquinas virtuales disponibles con la CLI de Azure. Si no tiene la CLI de Azure configurada aún, diríjase a Azure Portal y abra una instancia de [Cloud Shell](../cloud-shell/overview.md) o [instale la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Tras solicitar Cloud Shell, asegúrese de que trabaja en la misma suscripción que el recurso. Compruébelo con `az account show` y asegúrese de que el valor "nombre" coincide con el de la suscripción del clúster.

2. En Azure Portal, desplácese hasta el grupo de recursos donde se encuentra el área de trabajo de Log Analytics. Haga clic en el recurso de Log Analytics (el tipo del recurso será Log Analytics). Una vez que esté en la página de información general de recursos, haga clic en **Configuración avanzada** en la sección Configuración del menú izquierdo.

    ![Página de propiedades de Log Analytics](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. Haga clic en **Servidores Windows** si va a configurar un clúster con Windows y **Servidores Linux** si va a crear un clúster con Linux. Esta página le mostrará `workspace ID` y `workspace key` (que se muestran como Clave principal en el portal). Los necesitará para el siguiente paso.

4. Ejecute el comando para instalar el Agente de OMS en el clúster, usando la API `vmss extension set` en Cloud Shell:

    En un clúster con Windows:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    En un clúster con Linux:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Este es un ejemplo de incorporación del Agente de OMS a un clúster con Windows.

    ![Comando de la CLI para el Agente de OMS](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. La incorporación satisfactoria del Agente a los nodos debería durar menos de 15 minutos. Puede verificar que los agentes se han agregado mediante el uso de la API `az vmss extension list`:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Incorporación del Agente a través de la plantilla de Resource Manager

Las plantillas de ejemplo de Resource Manager que implementan un área de trabajo de Azure Log Analytics e incorporan un agente a cada uno de los nodos se encuentran disponibles para [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) o [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Puede descargar y modificar esta plantilla para implementar un clúster que satisfaga mejor sus necesidades.

## <a name="view-performance-counters-in-the-log-analytics-portal"></a>Visualización de contadores de rendimiento en el portal de Log Analytics

Ahora que ha agregado el agente de OMS, vaya al portal de Log Analytics para elegir los contadores de rendimiento que desea recopilar. 

1. En Azure Portal, vaya al grupo de recursos donde creó la solución Service Fabric Analytics. Seleccione **ServiceFabric\<nameOfOMSWorkspace\>** y vaya a su página de información general. En la parte superior, haga clic en el vínculo para ir al portal de OMS.

2. Cuando se encuentre en el portal, verá un icono en forma de grafo para cada una de las soluciones habilitadas, entre ellos uno para Service Fabric. Haga clic en este para ir a la solución Service Fabric Analytics. 

3. Ahora verá algunos iconos con grafos para los eventos de canal operativo y Reliable Services. Haga clic con el botón derecho en el icono de engranaje para ir a la página de configuración.

    ![Configuración de OMS](media/service-fabric-diagnostics-oms-agent/oms-solutions-settings.png)

4. En la página de configuración, haga clic en Datos y elija Contadores de rendimiento de Windows o Linux. Existe una lista con algunos predeterminados que puede elegir para habilitar, y también puede establecer el intervalo para la recopilación. También puede agregar [contadores de rendimiento adicionales](service-fabric-diagnostics-event-generation-perf.md) para recopilar. En este [artículo](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx) puede comprobar el formato adecuado.

Una vez que estén configurados los contadores, regrese a la página de soluciones y pronto verá que los datos fluyen y se muestran en los grafos bajo **Métricas de nodo**. También puede consultar los datos del contador de rendimiento de forma similar a los eventos de clúster y filtrar en los nodos, el nombre del contador de rendimiento y los valores mediante el lenguaje de consulta Kusto. 

![Consulta de contador de rendimiento de OMS](media/service-fabric-diagnostics-oms-agent/oms-perf-counter-query.png)

## <a name="next-steps"></a>Pasos siguientes

* Recopile los [contadores de rendimiento](service-fabric-diagnostics-event-generation-perf.md) correspondientes. Para configurar el agente de OMS para recopilar contadores de rendimiento específicos, revise [Configuración orígenes de datos](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Configure Log Analytics para configurar [alertas automáticas](../log-analytics/log-analytics-alerts.md) que ayuden en la detección y el diagnóstico.
* Como alternativa, puede recopilar los contadores de rendimiento a través de la [extensión de Azure Diagnostics y enviarlos a Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template).