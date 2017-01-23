---
title: "Solución Azure Networking Analytics de Log Analytics | Microsoft Docs"
description: "Puede usar la solución Azure Networking Analytics en Log Analytics para revisar los registros de los grupos de seguridad de red de Azure y los registros de Azure Application Gateway."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/1/2016
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: a86819102797b0e243d28cd9ddb3d2c88c74bfca
ms.openlocfilehash: 7ea593885c1b380236a49ec030c00ad19097e2fa


---
# <a name="azure-networking-analytics-preview-solution-in-log-analytics"></a>Solución Azure Networking Analytics (versión preliminar) en Log Analytics

Puede usar la solución Azure Networking Analytics de Log Analytics para revisar lo siguiente:

* Registros de Azure Application Gateway
* Métricas de Azure Application Gateway 
* Registros del grupo de seguridad de red de Azure

> [!NOTE]
> Azure Networking Analytics es una [solución en versión preliminar](log-analytics-add-solutions.md#preview-management-solutions-and-features).
> 
> 

Para usar la solución, habilite los diagnósticos para registros de Azure Application Gateway y los grupos de seguridad de red de Azure y dirigir los diagnósticos a un área de trabajo de Log Analytics. No se requiere escribir los registros en Azure Blob Storage.

Para Application Gateway se admiten los siguientes registros:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Las métricas siguientes son compatibles con Application Gateway:

* Rendimiento de 5 minutos

Para los grupos de seguridad de red se admiten los siguientes registros:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter
* NetworkSecurityGroupFlowEvent

## <a name="install-and-configure-the-solution"></a>Instalación y configuración de la solución
Para instalar y configurar la solución Azure Networking Analytics, siga estas instrucciones:

1. Habilite el registro de diagnóstico para los recursos que desea supervisar:
   * [Application Gateway](../application-gateway/application-gateway-diagnostics.md)
   * [Grupo de seguridad de red](../virtual-network/virtual-network-nsg-manage-log.md)
2. Habilite la solución Azure Networking Analytics mediante el proceso que se describe en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md).  

El siguiente script de PowerShell proporciona un ejemplo de cómo habilitar el registro de diagnóstico para puertas de enlace de aplicaciones y grupos de seguridad de red. 
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```


Si no habilita el registro de diagnóstico para un tipo de recurso en particular, las hojas de panel para ese recurso estarán en blanco.

## <a name="review-azure-networking-analytics-data-collection-details"></a>Revisión de los detalles de recopilación de datos de Azure Networking Analytics
La solución de administración Azure Networking Analytics recopila registros de diagnóstico directamente de Azure Application Gateways y grupos de seguridad de red. No es necesario escribir los registros en Azure Blob Storage y no se requiere ningún agente para la recopilación de datos.

En la siguiente tabla se muestran los métodos de recopilación de datos y otros detalles sobre cómo se reúnen los datos para Azure Networking Analytics.

| Plataforma | Agente directo | Agente System Center Operations Manager | Azure | ¿Se requiere Operations Manager? | Se envían los datos del agente de Operations Manager a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Las tablas de Azure |![No](./media/log-analytics-azure-networking/oms-bullet-red.png) |![No](./media/log-analytics-azure-networking/oms-bullet-red.png) |![Sí](./media/log-analytics-azure-networking/oms-bullet-green.png) |![No](./media/log-analytics-azure-networking/oms-bullet-red.png) |![No](./media/log-analytics-azure-networking/oms-bullet-red.png) |10 minutos |

## <a name="use-azure-networking-analytics"></a>Uso de Azure Networking Analytics
Después de instalar la solución, puede ver el resumen de errores de cliente y servidor de las puertas de enlace de las aplicaciones supervisadas mediante el icono de **Azure Networking Analytics** de la página **Overview** (Información general) de Log Analytics.

![imagen del icono de Azure Networking Analytics](./media/log-analytics-azure-networking/log-analytics-azurenetworking-tile.png)

Tras hacer clic en el icono de **Overview** (Información general), puede ver resúmenes de los registros y desplazarse hasta los detalles de las categorías siguientes:

* Registros de acceso de Application Gateway
  * Errores de cliente y servidor de los registros de acceso de Application Gateway
  * Solicitudes por hora para cada Application Gateway
  * Solicitudes fallidas por hora para cada Application Gateway
  * Errores por agente de usuario de las puertas de enlace de las aplicaciones
* Rendimiento de Application Gateway
  * Estado de host de Application Gateway
  * Percentil 95 y máximo para las solicitudes fallidas de Application Gateway
* Flujos bloqueados de grupos de seguridad de red
  * Reglas de los grupos de seguridad de red con flujos bloqueados
  * Direcciones MAC con flujos bloqueados
* Flujos permitidos en los grupos de seguridad de red
  * Reglas de los grupos de seguridad de red con flujos permitidos
  * Direcciones MAC con flujos permitidos

![imagen del panel de Azure Networking Analytics](./media/log-analytics-azure-networking/log-analytics-azurenetworking01.png)

![imagen del panel de Azure Networking Analytics](./media/log-analytics-azure-networking/log-analytics-azurenetworking02.png)

![imagen del panel de Azure Networking Analytics](./media/log-analytics-azure-networking/log-analytics-azurenetworking03.png)

![imagen del panel de Azure Networking Analytics](./media/log-analytics-azure-networking/log-analytics-azurenetworking04.png)

### <a name="to-view-details-for-any-log-summary"></a>Visualización de detalles en cualquier resumen de registro
1. En la página **Overview** (Información general), haga clic en el icono de **Azure Networking Analytics**.
2. En el panel **Azure Networking Analytics**, revise la información de resumen en una de las hojas y haga clic en una para obtener información detallada acerca de esta en la página de búsqueda de registros.
   
    En cualquiera de las páginas de búsqueda de registros, puede ver los resultados por tiempo, resultados detallados y el historial de búsqueda de registros. También puede filtrar por las facetas para restringir los resultados.

## <a name="next-steps"></a>Pasos siguientes
* Use [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para ver datos detallados de Azure Networking Analytics.




<!--HONumber=Dec16_HO1-->


