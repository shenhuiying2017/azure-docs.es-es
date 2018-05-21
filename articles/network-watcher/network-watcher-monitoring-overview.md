---
title: Azure Network Watcher | Microsoft Docs
description: Obtenga información sobre las funcionalidades de supervisión, diagnóstico, métricas y registro de Azure Network Watcher para los recursos de una red virtual.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
Customer intent: As someone with basic Azure network experience, I want to understand how Azure Network Watcher can help me resolve some of the network-related problems I've encountered and provide insight into how I use Azure networking.
ms.assetid: 14bc2266-99e3-42a2-8d19-bd7257fec35e
ms.service: network-watcher
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 6b01a4c88f3dbb4d24566e514fd5989cda11005a
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
---
# <a name="what-is-azure-network-watcher"></a>¿Qué es Azure Network Watcher?

Azure Network Watcher proporciona herramientas para supervisar, diagnosticar, ver las métricas y habilitar o deshabilitar registros de recursos en una red virtual de Azure.

## <a name="monitoring"></a>Supervisión

### <a name = "connection-monitor"></a>Supervisión de la comunicación entre una máquina virtual y un punto de conexión

Los puntos de conexión pueden ser otra máquina virtual (VM), un nombre de dominio completo (FQDN), un identificador uniforme de recursos (URI) o la dirección IPv4. La funcionalidad *monitor de conexión* supervisa la comunicación a intervalos regulares y le informa de los cambios de disponibilidad, latencia y topología de red entre la máquina virtual y el punto de conexión. Por ejemplo, podría tener una máquina virtual de servidor web que se comunica con una máquina virtual de servidor de base de datos. Alguna persona de su organización puede, sin su conocimiento, aplicar una ruta personalizada o una regla de seguridad de red a la máquina de servidor web o de servidor de base de datos o a la subred.

Si un punto de conexión queda inaccesible, la solución de problemas de conexión le informa de la razón. Las razones posibles son un problema de resolución de nombres DNS, la CPU, memoria o firewall en el sistema operativo de una máquina virtual o el tipo de salto de una ruta personalizada o una regla de seguridad para la máquina virtual o la subred de la conexión saliente. Obtenga más información sobre las [reglas de seguridad](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#security-rules) y los [tipos de salto de enrutamiento](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) en Azure.

Monitor de conexión también proporciona la latencia mínima, media y máxima observada con el tiempo. Después de conocer la latencia de una conexión, es posible que usted pueda reducir la latencia moviendo los recursos de Azure a diferentes regiones de Azure. Obtenga más información sobre cómo determinar las [latencias relativas entre las regiones de Azure y los proveedores de acceso a Internet](#determine-relative-latencies-between-azure- regions-and-internet-service-providers) y cómo supervisar la comunicación entre una máquina virtual y un punto de conexión con [monitor de conexión](connection-monitor.md). Si, no obstante, prefiere probar una conexión en un punto determinado en el tiempo, en lugar de supervisar la conexión con el tiempo, como se hace con monitor de conexión, use la funcionalidad [solución de problemas de conexión](#connection-troubleshoot).

### <a name="view-resources-in-a-virtual-network-and-their-relationships"></a>Visualización de recursos de una red virtual y sus relaciones

A medida que se agregan recursos a una red virtual, puede resultar difícil saber qué recursos se encuentran en una red virtual y cómo se relacionan entre sí. La funcionalidad *topología* le permite generar un diagrama visual de los recursos de una red virtual y las relaciones existentes entre los recursos. En la imagen siguiente se muestra un diagrama de topología de ejemplo de una red virtual que tiene tres subredes, dos máquinas virtuales, interfaces de red, direcciones IP públicas, grupos de seguridad de red, tablas de rutas y las relaciones entre los recursos:

![Vista de la topología](./media/network-watcher-monitoring-overview/topology.png)

Puede descargar una versión editable de la imagen en formato svg. Obtenga más información sobre la [vista de la topología](view-network-topology.md).

## <a name="diagnostics"></a>Diagnóstico

### <a name="diagnose-network-traffic-filtering-problems-to-or-from-a-vm"></a>Diagnóstico de problemas de filtrado del tráfico de red hacia o desde una máquina virtual

Cuando se implementa una máquina virtual, Azure aplica varias reglas de seguridad de forma predeterminada a la máquina virtual que permiten o deniegan el tráfico hacia o desde la máquina virtual. Puede invalidar las reglas predeterminadas de Azure o crear reglas adicionales. En algún momento, puede que una máquina virtual no pueda comunicarse con otros recursos, debido a una regla de seguridad. La funcionalidad *Comprobación de flujo de IP* permite especificar una dirección IPv4 de origen y destino, el puerto, el protocolo (TCP o UDP) y la dirección de tráfico (entrante y saliente). La funcionalidad Comprobación de flujo de IP entonces comprueba la comunicación y le informa si la conexión es correcta o si genera algún error. Si se produce algún error en la conexión, la funcionalidad Comprobación de flujo de IP indica qué regla de seguridad permitió o denegó la comunicación, para poder resolver el problema. Obtenga más información sobre la funcionalidad [Comprobación de flujo de IP](network-watcher-ip-flow-verify-overview.md).

### <a name="diagnose-network-routing-problems-from-a-vm"></a>Diagnóstico de problemas de enrutamiento de red desde una máquina virtual

Cuando se crea una red virtual, Azure crea varias rutas de salida predeterminadas para el tráfico de red. El tráfico saliente de todos los recursos, como las máquinas virtuales, implementados en una red virtual, se enruta por las rutas predeterminadas de Azure. Puede invalidar las rutas predeterminadas de Azure o crear rutas adicionales. Puede que una máquina virtual ya no pueda comunicarse con otros recursos debido a una ruta específica. La funcionalidad *Próximo salto* permite especificar una dirección IPv4 de origen y destino. La funcionalidad Próximo salto después comprueba la comunicación y le informa del tipo de próximo salto utilizado para enrutar el tráfico. A continuación, puede quitar, cambiar o agregar una ruta, para resolver un problema de enrutamiento. Obtenga más información sobre la funcionalidad [próximo salto](network-watcher-next-hop-overview.md?).

### <a name="connection-troubleshoot"></a>Diagnóstico de conexiones de salida desde una máquina virtual

La funcionalidad *Solución de problemas de conexión* permite probar una conexión entre una máquina virtual y otra máquina virtual, un FQDN, un URI o una dirección IPv4. La prueba devuelve información similar a la que se devuelve al usar la funcionalidad [monitor de conexión](#connection-monitor), pero prueba la conexión en un momento dado, en lugar de supervisarla con el tiempo, como bien hace la funcionalidad monitor de supervisión. Obtenga más información sobre cómo solucionar problemas de conexión con [connection-troubleshoot](network-watcher-connectivity-overview.md).

### <a name="capture-packets-to-and-from-a-vm"></a>Captura de paquetes hacia y desde una máquina virtual

Las opciones de filtrado avanzadas y controles optimizados, con los que se pueden establecer límites de tiempo y de tamaño, proporcionan una gran versatilidad. La captura se puede almacenar en Azure Storage, en el disco de máquina virtual o en ambos. A continuación, puede analizar el archivo de captura con varias herramientas de análisis de captura de red estándar. Obtenga más información sobre la [captura de paquetes](network-watcher-packet-capture-overview.md).

### <a name="diagnose-problems-with-an-azure-virtual-network-gateway-and-connections"></a>Diagnóstico de problemas con conexiones y la puerta de enlace de una red virtual de Azure

Las puertas de enlace de red virtual proporcionan conectividad entre recursos locales y redes virtuales de Azure. La supervisión de puertas de enlace y sus conexiones es esencial para garantizar la continuidad de la comunicación. La funcionalidad *Diagnósticos de VPN* ofrece la posibilidad de diagnosticar puertas de enlace y conexiones. Diagnósticos de VPN diagnostica el estado de la puerta de enlace o de la conexión de la puerta de enlace y le informa de la disponibilidad de estas. Si la puerta de enlace o la conexión no están disponibles, la funcionalidad Diagnósticos de VPN le indica el motivo, para poder resolver el problema. Obtenga más información sobre [Diagnósticos de VPN](network-watcher-troubleshoot-overview.md).

### <a name="determine-relative-latencies-between-azure-regions-and-internet-service-providers"></a>Determinación de latencias relativas entre las regiones de Azure y los proveedores de acceso a Internet

Puede consultar Network Watcher para obtener información sobre las latencias entre las regiones de Azure y entre los proveedores de acceso a Internet. Si conoce las latencias entre las regiones de Azure y entre los proveedores de acceso a Internet, puede implementar recursos de Azure para optimizar el tiempo de respuesta de la red. Obtenga más información sobre las [latencias relativas](view-relative-latencies.md).

### <a name="view-security-rules-for-a-network-interface"></a>Visualización de las reglas de seguridad para una interfaz de red

Las reglas de seguridad vigentes de una interfaz de red son una combinación de todas las reglas de seguridad aplicadas a la interfaz de red y la subred en que se encuentra la interfaz de red.  La funcionalidad *Vista de grupo de seguridad* muestra todas las reglas de seguridad aplicadas a la interfaz de red, la subred en que se encuentra la interfaz de red y el agregado de ambas. Si sabe qué reglas se aplican a una interfaz de red, puede agregar, eliminar o cambiar las reglas, en caso de que permitan o denieguen el tráfico que desea cambiar. Obtenga más información sobre [Vista de grupo de seguridad](network-watcher-security-group-view-overview.md).

## <a name="metrics"></a>Métricas

Hay [límites](../azure-subscription-service-limits.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#azure-resource-manager-virtual-networking-limits) al número de recursos de red que se pueden crear dentro de una suscripción y región de Azure. Si se alcanzan tales límites, ya no podrá crear más recursos dentro de la suscripción o región. La funcionalidad *Límite de suscripción de red* ofrece un resume de cuántos recursos de red se han implementado en una suscripción y región, y cuál es el límite para el recurso. En la siguiente imagen se muestra el resultado parcial de los recursos de red implementados en la región Este de EE. UU. para una suscripción de ejemplo:

![Límites de suscripción](./media/network-watcher-monitoring-overview/subscription-limit.png)

La información es útil para planear futuras implementaciones de recursos.

## <a name="logs"></a>Registros

### <a name="analyze-traffic-to-or-from-a-network-security-group"></a>Análisis del tráfico hacia y desde un grupo de seguridad de red

Los grupos de seguridad de red (NSG) permiten o deniegan el tráfico entrante o saliente de una interfaz de red de una máquina virtual. La funcionalidad *Registro de flujos de NSG* permite registrar la dirección IP de origen y destino, el puerto, el protocolo y si un NSG ha permitido o denegado el tráfico. Puede analizar los registros con distintas herramientas, como PowerBI y la funcionalidad *análisis de tráfico*. El análisis de tráfico ofrece visualizaciones enriquecidas de los datos escritos en registros de flujos de NSG. En la imagen siguiente se muestra parte de la información y las visualizaciones que el análisis de tráfico presenta de los datos de registros de flujos de NSG.

![Análisis del tráfico](./media/network-watcher-monitoring-overview/traffic-analytics.png)

Obtenga más información sobre [registros de flujos de NSG](network-watcher-nsg-flow-logging-overview.md) y [análisis de tráfico](traffic-analytics.md).

### <a name="view-diagnostic-logs-for-network-resources"></a>Visualización de registros de diagnóstico de recursos de red

Puede habilitar el registro de diagnóstico de recursos de red de Azure, como grupos de seguridad de red, direcciones IP públicas, equilibradores de carga, puertas de enlace de red virtual y puertas de enlace de aplicación. La funcionalidad *Registros de diagnóstico* proporciona una única interfaz para habilitar y deshabilitar los registros de diagnóstico de recursos de red de cualquier recurso de red existente que genera un registro de diagnóstico. Puede ver registros de diagnóstico con herramientas como Microsoft Power BI y Azure Log Analytics. Para obtener más información sobre el análisis de registros de diagnóstico de red de Azure, vea [Soluciones de supervisión de redes de Azure en Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Pasos siguientes

Ya dispone de información general sobre Azure Network Watcher. Para empezar a utilizar Network Watcher, diagnostique un problema de comunicación común hacia y desde una máquina virtual con la funcionalidad Comprobación de flujo de IP. Para saber cómo, vea la guía de inicio rápido [Diagnóstico de problemas al filtrar el tráfico de red de las máquinas virtuales](diagnose-vm-network-traffic-filtering-problem.md).