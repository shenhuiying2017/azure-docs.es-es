---
title: Solución Azure Networking Analytics de Log Analytics | Microsoft Docs
description: Puede usar la solución Azure Networking Analytics en Log Analytics para revisar los registros de los grupos de seguridad de red de Azure y los registros de Azure Application Gateway.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: richrund

---
# <a name="azure-networking-analytics-(preview)-solution-in-log-analytics"></a>Solución Azure Networking Analytics (versión preliminar) en Log Analytics
> [!NOTE]
> Se trata de una [solución preliminar](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).
> 
> 

Puede usar la solución Azure Networking Analytics en Log Analytics para revisar los registros de los grupos de seguridad de red de Azure y los registros de Azure Application Gateway.

Puede habilitar el registro para los registros de Azure Application Gateway y los grupos de seguridad de red de Azure. Estos registros se escriben en Blob Storage, donde Log Analytics los puede indexar para la búsqueda y el análisis.

Para Application Gateway se admiten los siguientes registros:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog

Para los grupos de seguridad de red se admiten los siguientes registros:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

## <a name="install-and-configure-the-solution"></a>Instalación y configuración de la solución
Para instalar y configurar la solución Azure Networking Analytics, siga estas instrucciones:

1. Habilite el registro de diagnóstico para los recursos que desea supervisar:
   * [Puerta de enlace de aplicaciones](../application-gateway/application-gateway-diagnostics.md)
   * [Grupo de seguridad de red](../virtual-network/virtual-network-nsg-manage-log.md)
2. Configure Log Analytics para leer los registros de Blob Storage mediante el proceso que se describe en el artículo sobre [archivos JSON en Blob Storage](log-analytics-azure-storage-json.md).
3. Habilite la solución Azure Networking Analytics mediante el proceso que se describe en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md).  

Si no habilita el registro de diagnóstico para un tipo de recurso en particular, las hojas de panel para ese recurso estarán en blanco.

## <a name="review-azure-networking-analytics-data-collection-details"></a>Revisión de los detalles de recopilación de datos de Azure Networking Analytics
La solución Azure Networking Analytics recopila registros de diagnóstico de Azure Blob Storage para Azure Application Gateways y grupos de seguridad de red.
Para la recopilación de datos no se necesita ningún agente.

En la siguiente tabla se muestran los métodos de recopilación de datos y otros detalles sobre cómo se reúnen los datos para Azure Networking Analytics.

| Plataforma | Agente directo | Agente System Center Operations Manager (SCOM) | Almacenamiento de Azure | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
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

<!--HONumber=Oct16_HO2-->


