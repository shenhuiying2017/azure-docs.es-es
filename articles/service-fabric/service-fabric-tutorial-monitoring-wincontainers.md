---
title: "Supervisión y diagnóstico de contenedores de Windows en Azure Service Fabric | Microsoft Docs"
description: "Configure la supervisión y diagnóstico de contenedores de Windows orquestados en Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 8fe3266cfcb7141684f9e1b5dfa74d6569c23b24
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="monitor-windows-containers-on-service-fabric-using-oms"></a>Supervisión de contenedores de Windows en Service Fabric mediante OMS

Esta es la tercera parte de un tutorial y le guía a través de la configuración de OMS para supervisar sus contenedores de Windows orquestados en Service Fabric.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar OMS para su clúster de Service Fabric
> * Usar un área de trabajo de OMS para ver y consultar registros de sus contenedores y nodos
> * Configurar el agente de OMS para elegir métricas de nodo y contenedor

## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar este tutorial, debe:
- Tener un clúster en Azure o [crear uno con este tutorial](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
- [Implementar una aplicación en contenedores en él](service-fabric-host-app-in-a-container.md)

## <a name="setting-up-oms-with-your-cluster-in-the-resource-manager-template"></a>Configuración de OMS con su clúster en la plantilla de Resource Manager

En el caso de que usara la [plantilla proporcionada](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Tutorial) en la primera parte de este tutorial, debe incluir las siguientes incorporaciones a una plantilla de Azure Resource Manager para Service Fabric genérica. En caso de que tenga un clúster que quiera configurar para supervisar contenedores con OMS:
* Realice los siguientes cambios en su plantilla de Resource Manager.
* Impleméntelo mediante PowerShell para actualizar su clúster [implementando la plantilla](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm). Azure Resource Manager es consciente de la existencia del recurso, por lo que lo implementará como una actualización.

### <a name="adding-oms-to-your-cluster-template"></a>Incorporación de OMS a su plantilla de clúster

Realice los siguientes cambios en su *template.json*:

1. Agregue la ubicación y el nombre del área de trabajo de OMS a su sección *parameters*:
    
    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your OMS Log Analytics Workspace"
      }
    },
    "omsRegion": {
      "type": "string",
      "defaultValue": "East US",
      "allowedValues": [
        "West Europe",
        "East US",
        "Southeast Asia"
      ],
      "metadata": {
        "description": "Specify the Azure Region for your OMS workspace"
      }
    }
    ```

    Para cambiar el valor usado para cualquiera, agregue los mismos parámetros a su *template.parameters.json* y cambie los valores utilizados allí.

2. Agregue el nombre de la solución y la solución a sus *variables*: 
    
    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Agregue Microsoft Monitoring Agent de OMS como extensión de máquina virtual. Busque el recurso de conjuntos de escalado de máquinas virtuales: *resources* > *"apiVersion": "[variables('vmssApiVersion')]"*. En *properties* > *virtualMachineProfile* > *extensionProfile* > *extensions*, agregue la siguiente descripción de la extensión en la extensión *ServiceFabricNode*: 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Agregue el área de trabajo de OMS como recurso individual. En *resources*, después del recurso de conjuntos de escalado de máquinas virtuales, agregue lo siguiente:
    
    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

[Esta](https://github.com/ChackDan/Service-Fabric/blob/master/ARM%20Templates/Tutorial/azuredeploy.json) es una plantilla de ejemplo (usada en la primera parte de este tutorial) que tiene todos estos cambios a los que puede hacer referencia según sea necesario. Estos cambios agregarán un área de trabajo de Log Analytics de OMS a su grupo de recursos. El área de trabajo se configurará para elegir eventos de plataforma de Service Fabric de las tablas de almacenamiento configuradas con el agente de [Microsoft Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md). El agente de OMS (Microsoft Monitoring Agent) también se ha agregado a cada uno de los nodos de su clúster como extensión de máquina virtual (esto significa que, a medida que escala el clúster, el agente se configura automáticamente en cada máquina y se enlaza a la misma área de trabajo).

Implemente la plantilla con sus nuevos cambios para actualizar su clúster actual. Debe ver los recursos OMS en su grupo de recursos una vez que este se ha completado. Cuando el clúster esté listo, implemente su aplicación en contenedores en él. En el siguiente paso, configuraremos la supervisión de los contenedores.

## <a name="add-the-container-monitoring-solution-to-your-oms-workspace"></a>Agregar la solución de Supervisión de contenedores a su área de trabajo de OMS

Para configurar la solución Containers en su área de trabajo, busque la *solución de Supervisión de contenedores* y cree un recurso Containers (en la categoría Supervisión y administración).

![Adición de la solución Containers](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

Cuando se le pida el *área de trabajo de OMS*, seleccione el área de trabajo que se creó en su grupo de recursos y haga clic en **Crear**. Se agregará una *solución de Supervisión de contenedores* a su área de trabajo y, de forma automática, el agente de OMS implementado por la plantilla comenzará a recopilar registros y estadísticas de Docker. 

Vuelva a su *grupo de recursos*, donde ahora debería ver la solución de supervisión recién agregada. Si hace clic en él, la página de aterrizaje debe mostrar el número de imágenes de contenedor que tiene en ejecución. 

*Tenga en cuenta que ejecuté cinco instancias de mi contenedor fabrikam de la [segunda parte](service-fabric-host-app-in-a-container.md) del tutorial*

![Página de aterrizaje de la solución Containers](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

Al hacer clic en la **solución de Supervisión de contenedores**, obtendrá acceso a un panel más detallado, lo que le permite desplazarse por varios paneles, así como ejecutar consultas en Log Analytics. 

*Tenga en cuenta que, a partir de septiembre de 2017, la solución cuenta con algunas actualizaciones (haga caso omiso de cualquier error que pueda obtener sobre eventos de Kubernetes a medida que trabajamos en la integración de varios orquestadores en la misma solución).*

Como el agente elige registros de Docker, se muestran de forma predeterminada *stdout* y *stderr*. Si se desplaza a la derecha, verá el inventario de imágenes de contenedor, el estado, la métrica y consultas de ejemplo que podría ejecutar para obtener datos más útiles. 

![Panel de soluciones Containers](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Al hacer clic en cualquiera de estos paneles, obtendrá acceso a la consulta de Log Analytics que genera el valor mostrado. Cambie la consulta por *\** para ver todos los diversos tipos de registros que se eligen. A partir de aquí, puede consultar o filtrar para el rendimiento del contenedor, los registros, o bien puede echar un vistazo a los eventos de plataforma de Service Fabric. Sus agentes también emiten constantemente un latido de cada nodo, al que puede echar un vistazo para asegurarse de que los datos siguen recopilándose a partir de todas sus máquinas si cambia la configuración del clúster.   

![Consulta del contenedor](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-oms-agent-to-pick-up-performance-counters"></a>Configurar el agente de OMS para elegir contadores de rendimiento

Otra ventaja de usar el agente de OMS es la capacidad de cambiar los contadores de rendimiento que desea elegir a través de la experiencia de IU de OMS, en lugar de tener que configurar el agente de Azure Diagnostics y hacer una actualización basada en la plantilla de Resource Manager cada vez. Para ello, haga clic en **Portal de OMS** en la página de aterrizaje de su solución de Supervisión de contenedores (o Service Fabric).

![Portal de OMS](./media/service-fabric-tutorial-monitoring-wincontainers/oms-portal.png)

Este le llevará a su área de trabajo en el portal de OMS, donde puede ver sus soluciones, crear paneles personalizados, así como configurar el agente de OMS. 
* Haga clic en la **rueda dentada** en la esquina superior derecha de su pantalla para abrir el menú *Configuración*.
* Haga clic en **Orígenes conectados** > **Servidores de Windows** para comprobar que dispone de *5 equipos con Windows conectados*.
* Haga clic en **Datos** > **Contadores de rendimiento de Windows** para buscar y agregar nuevos contadores de rendimiento. Aquí verá una lista de recomendaciones de OMS para contadores de rendimiento que podría recopilar, así como la opción de búsqueda de otros contadores. Haga clic en **Agregar los contadores de rendimiento seleccionados** para comenzar a recopilar las métricas sugeridas.

    ![Contadores de rendimiento](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters.png)

De nuevo en Azure Portal, **actualice** su solución de Supervisión de contenedores en unos minutos y debería comenzar a ver cómo llegan datos de *rendimiento del equipo*. Esto le ayudará a comprender cómo se usan sus recursos. También puede usar estas métricas para tomar decisiones adecuadas sobre el escalado de su clúster, o bien para confirmar si un clúster equilibra su carga como se espera. 

*Nota: Asegúrese de que sus filtros de tiempo se establecen de forma adecuada para que consuma estas métricas.* 

![Contadores de rendimiento 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Configurar OMS para su clúster de Service Fabric
> * Usar un área de trabajo de OMS para ver y consultar registros de sus contenedores y nodos
> * Configurar el agente de OMS para elegir métricas de nodo y contenedor

Ahora que ha configurado la supervisión de su aplicación en contenedores, pruebe lo siguiente:

* Configure OMS para un clúster de Linux, siguiendo pasos similares a los anteriores. Haga referencia a [esta plantilla](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) para realizar cambios en su plantilla de Resource Manager.
* Configure OMS para configurar [alertas automáticas](../log-analytics/log-analytics-alerts.md) que ayuden en la detección y el diagnóstico.
* Explore la lista de Service Fabric de [contadores de rendimiento recomendados](service-fabric-diagnostics-event-generation-perf.md) para configurar sus clústeres.
* Familiarícese con las funciones de [búsqueda de registros y consulta](../log-analytics/log-analytics-log-searches.md) que se ofrecen como parte de Log Analytics.