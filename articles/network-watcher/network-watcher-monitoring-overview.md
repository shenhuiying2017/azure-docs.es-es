---
title: "Introducción a Azure Network Watcher | Microsoft Docs"
description: "Esta página proporciona una introducción al servicio Network Watcher que se usa para supervisar y visualizar los recursos conectados en Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 14bc2266-99e3-42a2-8d19-bd7257fec35e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: jdial
ms.openlocfilehash: eecb20d4a53478471c238018d8fbd5a5f9cb79d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-network-monitoring-overview"></a>Información general sobre la supervisión de red de Azure

Los clientes pueden crear una red integral en Azure mediante la orquestación y composición de varios recursos de red individuales como, por ejemplo, redes virtuales, ExpressRoute, Application Gateway, equilibradores de carga y muchos más. Se puede supervisar cada uno de los recursos de la red. Nos referimos a esta supervisión como supervisión en el nivel de recurso.

La red integral puede tener configuraciones complejas e interacciones entre los recursos, lo cual crea escenarios complejos que necesitan una supervisión basada en escenarios mediante Network Watcher.

Este artículo trata la supervisión en los niveles de escenario y de recurso. La supervisión de red en Azure es completa y cubre dos amplias categorías:

* [**Network Watcher**](#network-watcher): este servicio ofrece las características de supervisión basada en el escenario. Este servicio incluye captura de paquetes, próximo salto, comprobación de flujo de IP, vista de grupos de seguridad y registros de flujo de NSG. La supervisión en el nivel de escenario, ofrece una visión global de los recursos de la red que contrasta con la supervisión de recursos de red individuales.
* [**Supervisión de recursos**](#network-resource-level-monitoring): la supervisión en el nivel de recurso consta de cuatro funciones: registros de diagnóstico, métricas, solución de problemas y mantenimiento de recursos. Todas estas funciones se integran en el nivel de recursos de red.

## <a name="network-watcher"></a>Network Watcher

Network Watcher es un servicio regional que permite supervisar y diagnosticar problemas en un nivel de escenario de red mediante Azure. Las herramientas de visualización y diagnóstico de red que incluye Network Watcher le ayudan a conocer, diagnosticar y obtener información acerca de cualquier red de Azure.

En la actualidad, Network Watcher dispone de las siguientes funcionalidades:

* **[Topología](network-watcher-topology-overview.md)**: proporciona una vista de nivel de red que muestra las diversas interconexiones y asociaciones entre los recursos de red de un grupo de recursos.
* **[Captura de paquetes variable](network-watcher-packet-capture-overview.md)**: captura datos de paquetes dentro y fuera de una máquina virtual. Opciones de filtrado avanzadas y controles optimizados con los que se pueden establecer límites de tiempo y de tamaño, lo cual proporciona una gran versatilidad. Los datos de paquete se pueden almacenar en un almacén de blobs o en el disco local en formato .cap.
* **[Comprobación de flujo de IP](network-watcher-ip-flow-verify-overview.md)**: comprueba si un paquete está permitido o no en función de los parámetros de paquete de 5-tuplas de información del flujo (IP de destino, IP de origen, puerto de destino, puerto de origen y protocolo). Si un grupo de seguridad deniega un paquete, se devuelve el nombre de la regla y del grupo que lo deniega.
* **[Próximo salto](network-watcher-next-hop-overview.md)**: determina el próximo salto para los paquetes que se enrutan en el tejido de red de Azure, lo que le permite diagnosticar cualquier ruta definida por el usuario que se haya configurado incorrectamente.
* **[Vista de grupos de seguridad](network-watcher-security-group-view-overview.md)**: obtiene las reglas de seguridad eficaces que se aplican en una máquina virtual.
* **[Registro de flujo de NSG](network-watcher-nsg-flow-logging-overview.md)**: los registros de flujo de los grupos de seguridad de red permiten capturar registros relacionados con el tráfico que están permitidos o no por las reglas de seguridad del grupo. El flujo se define mediante una información de 5-tuplas: IP de origen, IP de destino, puerto de origen, puerto de destino y protocolo.
* **[Solución de problemas de la puerta de enlace de Virtual Network y de las conexiones](network-watcher-troubleshoot-manage-rest.md)**: ofrece la capacidad para solucionar problemas con las puertas de enlace de Virtual Network y las conexiones.
* **[Límites de suscripción de red](#network-subscription-limits)**: le permite ver el uso de los recursos de la red en comparación con los límites.
* **[Configuración del registro de diagnósticos](#diagnostic-logs)**: proporciona un único panel para habilitar o deshabilitar los registros de diagnóstico de los recursos de red de un grupo de recursos.
* **[Conectividad (versión preliminar)](network-watcher-connectivity-overview.md)**: comprueba si se puede establecer una conexión TCP directa desde una máquina virtual a un punto de conexión determinado.

### <a name="role-based-access-control-rbac-in-network-watcher"></a>Control de acceso basado en rol (RBAC) en Network Watcher

Network Watcher usa el [modelo de control de acceso de basado en rol (RBAC) de Azure](../active-directory/role-based-access-control-what-is.md). Network Watcher requiere los siguientes permisos. Es importante asegurarse de que el rol que se usa para iniciar las API de Network Watcher, o mediante Network Watcher desde el portal, tenga el acceso requerido.

|Recurso| Permiso|
|---|---| 
|Microsoft.Storage/ |Lectura|
|Microsoft.Authorization/| Lectura| 
|Microsoft.Resources/subscriptions/resourceGroups/| Lectura|
|Microsoft.Storage/storageAccounts/listServiceSas/ | Acción|
|Microsoft.Storage/storageAccounts/listAccountSas/ |Acción|
|Microsoft.Storage/storageAccounts/listKeys/ | Acción|
|Microsoft.Compute/virtualMachines/ |Lectura|
|Microsoft.Compute/virtualMachines/ |Escritura|
|Microsoft.Compute/virtualMachineScaleSets/ |Lectura|
|Microsoft.Compute/virtualMachineScaleSets/ |Escritura|
|Microsoft.Network/networkWatchers/packetCaptures/ |Lectura|
|Microsoft.Network/networkWatchers/packetCaptures/| Escritura|
|Microsoft.Network/networkWatchers/packetCaptures/| Eliminar|
|Microsoft.Network/networkWatchers/ |Escritura |
|Microsoft.Network/networkWatchers/| Lectura |
|Microsoft.Insights/alertRules/ |*|
|Microsoft.Support/ | *|

### <a name="network-subscription-limits"></a>Límites de suscripción de red

Los límites de suscripción de red proporcionan detalles del uso de cada uno de los recursos de red en una suscripción de una región en comparación con el número máximo de recursos disponibles.

![límite de suscripción de red][nsl]

## <a name="network-resource-level-monitoring"></a>Supervisión en el nivel de recursos de red

Las siguientes características están disponibles para la supervisión en el nivel de recurso:

### <a name="audit-log"></a>Registro de auditoría

Se registran las operaciones realizadas como parte de la configuración de redes. Estos registros se pueden ver en Azure Portal o se pueden recuperar mediante herramientas de Microsoft como Power BI o herramientas de terceros. Los registros de auditoría están disponibles a través del portal, PowerShell, la CLI y la API de Rest. Para más información sobre los registros de auditoría, consulte [Operaciones de auditoría con Resource Manager](../resource-group-audit.md).

Hay registros de auditoría disponibles para las operaciones realizadas en todos los recursos de red.

### <a name="metrics"></a>Métricas

Las métricas son medidas de rendimiento y contadores recopilados durante un período de tiempo. Las métricas están disponibles actualmente para Application Gateway. Las métricas se pueden utilizar para desencadenar alertas basadas en umbrales. Consulte [Diagnósticos de Application Gateway](../application-gateway/application-gateway-diagnostics.md) para ver cómo se pueden utilizar las métricas para crear alertas.

![vista de métricas][metrics]

### <a name="diagnostic-logs"></a>Registros de diagnóstico

Los recursos de red crean eventos periódicos y espontáneos y estos se registran en las cuentas de almacenamiento, se envían a un centro de eventos o a Log Analytics. Estos registros proporcionan información acerca del estado de un recurso. Estos registros se pueden ver en herramientas como Power BI y Log Analytics. Para aprender a ver registros de diagnóstico, visite [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md).

Los registros de diagnóstico están disponibles para el [equilibrador de carga](../load-balancer/load-balancer-monitor-log.md), los [grupos de seguridad de red](../virtual-network/virtual-network-nsg-manage-log.md), las rutas y [Application Gateway](../application-gateway/application-gateway-diagnostics.md).

Network Watcher proporciona una vista de los registros de diagnóstico. Esta vista contiene todos los recursos de las redes que admiten el registro de diagnóstico. Desde esta vista, puede habilitar y deshabilitar los recursos de red de forma cómoda y rápida.

![logs][logs]

### <a name="troubleshooting"></a>Solución de problemas

La hoja de solución de problemas, una experiencia del portal, se proporciona actualmente en los recursos de red para diagnosticar problemas comunes asociados a un recurso individual. Esta experiencia está disponible para los siguientes recursos de red: ExpressRoute, VPN Gateway, Application Gateway, registros de seguridad de red, rutas, DNS, Load Balancer y Traffic Manager. Para más información acerca de la solución de problemas en el nivel de recurso, visite [Diagnose and resolve issues with Azure Troubleshooting](https://azure.microsoft.com/blog/azure-troubleshoot-diagonse-resolve-issues/) (Diagnóstico y resolución de problemas con la solución de problemas de Azure).

![información de solución de problemas][TS]

### <a name="resource-health"></a>Estado de los recursos

El estado de un recurso de red se proporciona de forma periódica. Estos recursos incluyen VPN Gateway y el túnel de VPN. Se puede acceder al estado de los recursos desde Azure Portal. Para más información sobre el estado de los recursos, visite [Información general sobre Resource Health](../resource-health/resource-health-overview.md).

## <a name="next-steps"></a>Pasos siguientes

Después de conocer Network Watcher, puede aprender a:

Realizar una captura de paquetes en su máquina virtual visitando [Captura de paquetes en Azure Portal](network-watcher-packet-capture-manage-portal.md)

Realizar una supervisión y unos diagnósticos proactivos mediante la [captura de paquetes desencadenada mediante alertas](network-watcher-alert-triggered-packet-capture.md).

Detectar vulnerabilidades de seguridad con el [Análisis de captura de paquetes con Wireshark](network-watcher-deep-packet-inspection.md), mediante herramientas de código abierto.

Obtenga información sobre las demás [funcionalidades de red](../networking/networking-overview.md) clave de Azure.

<!--Image references-->
[TS]: ./media/network-watcher-monitoring-overview/troubleshooting.png
[logs]: ./media/network-watcher-monitoring-overview/logs.png
[metrics]: ./media/network-watcher-monitoring-overview/metrics.png
[nsl]: ./media/network-watcher-monitoring-overview/nsl.png











