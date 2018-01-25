---
title: "Azure Service Fabric: configuración de la supervisión con el Agente de OMS | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el Agente de OMS para supervisar los contenedores y los contadores de rendimiento de los clústeres de Azure Service Fabric."
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
ms.date: 10/31/2017
ms.author: dekapur
ms.openlocfilehash: 095db20e7d22bd517337f24fc9a81b84988d1465
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="add-the-oms-agent-to-a-cluster"></a>Incorporación del Agente de OMS a un clúster

En este artículo se describen los pasos para agregar el Agente de OMS como una extensión del conjunto de escalado de máquinas virtuales al clúster y cómo conectarlo a su área de trabajo de Log Analytics de OMS existente. Esto permite recopilar datos de diagnóstico sobre los contenedores, las aplicaciones y la supervisión de rendimiento. Si lo agrega como una extensión, Azure Resource Manager garantiza la instalación en todos los nodos, incluso cuando se ajusta la escala del clúster.

> [!NOTE]
> En este artículo se da por supuesto que tiene un área de trabajo de Log Analytics de OMS que ya ha configurado. De lo contrario, vea [Configuración de Log Analytics (OMS)](service-fabric-diagnostics-oms-setup.md).

## <a name="add-the-agent-extension-via-azure-cli"></a>Incorporación de la extensión del Agente mediante la CLI de Azure

La mejor manera de agregar el Agente de OMS al clúster es a través de las API del conjunto de escalado de máquinas virtuales disponibles con la CLI de Azure. Si no tiene la CLI de Azure configurada aún, diríjase a Azure Portal y abra una instancia de [Cloud Shell](../cloud-shell/overview.md) o [instale la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Tras solicitar Cloud Shell, asegúrese de que trabaja en la misma suscripción que el recurso. Compruébelo con `az account show` y asegúrese de que el valor "nombre" coincide con el de la suscripción del clúster.

2. En Azure Portal, desplácese hasta el grupo de recursos donde se encuentra el área de trabajo de OMS. Haga clic en el recurso Log Analytics (el tipo del recurso será Log Analytics) en el panel de navegación derecho, desplácese hacia abajo y haga clic en **Propiedades**.

    ![Página Propiedades de OMS](media/service-fabric-diagnostics-oms-agent/oms-properties.png)

    Anote el valor de `workspaceId`. 

3. También necesitará `workspaceKey` para implementar el Agente. Para obtener la clave, haga clic en **Configuración avanzada** en la sección *Configuración* del panel de navegación izquierdo. Haga clic en **Servidores Windows** si va a configurar un clúster con Windows y **Servidores Linux** si va a crear un clúster con Linux. Necesitará la *clave principal* que se muestra para implementar los agentes, como `workspaceKey`.

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
 
    La incorporación satisfactoria del Agente a los nodos debería durar menos de 15 minutos. Puede verificar que los agentes se han agregado mediante el uso de la API `az vmss extension list`:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Incorporación del Agente a través de la plantilla de Resource Manager

Las plantillas de ejemplo de Resource Manager que implementan un área de trabajo de Log Analytics de OMS e incorporan un agente a cada uno de los nodos se encuentran disponibles para [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) o [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Puede descargar y modificar esta plantilla para implementar un clúster que satisfaga mejor sus necesidades.

## <a name="next-steps"></a>Pasos siguientes

* Recopile los [contadores de rendimiento](service-fabric-diagnostics-event-generation-perf.md) correspondientes. Para configurar el Agente de OMS para que seleccione contadores de rendimiento específicos, diríjase el Portal de OMS (vínculo disponible en la parte superior del recurso Log Analytics de OMS). Después, haga clic en **Inicio > Configuración > datos > Contadores de rendimiento de Windows** o **Contadores de rendimiento de Linux** y elija los contadores que desea recopilar.
* Configure OMS para configurar [alertas automáticas](../log-analytics/log-analytics-alerts.md) que ayuden en la detección y el diagnóstico.
