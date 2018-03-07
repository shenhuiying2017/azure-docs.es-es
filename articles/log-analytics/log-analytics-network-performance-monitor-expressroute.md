---
title: "Solución Monitor de rendimiento de red en Azure Log Analytics | Microsoft Docs"
description: "La función de ExpressRoute Manager en Network Performance Monitor le permite supervisar la conectividad de extremo a extremo y el rendimiento entre sus sucursales y Azure, a través de ExpressRoute de Azure."
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 405bcd7d69e93f838d35b61be489464fcf55ee88
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/23/2018
---
# <a name="expressroute-manager"></a>ExpressRoute Manager

La función de ExpressRoute Manager en [Network Performance Monitor](log-analytics-network-performance-monitor.md) le permite supervisar la conectividad de extremo a extremo y el rendimiento entre sus sucursales y Azure, a través de ExpressRoute de Azure. Las principales ventajas son: 

- Detección automática de circuitos ExpressRoute asociados a una suscripción 
- Seguimiento de uso, pérdida y latencia de ancho de banda en el circuito, emparejamiento a nivel de red virtual para su ExpressRoute 
- Detección de topología de red de los circuitos ExpressRoute 

![Supervisión de ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-intro.png)

## <a name="configuration"></a>Configuración 
Para abrir la configuración de Network Performance Monitor, abra la [solución de Network Performance Monitor](log-analytics-network-performance-monitor.md) y haga clic en el botón **Configurar**.

### <a name="configure-nsg-rules"></a>Configuración de reglas de NSG 
En el caso de los servidores de Azure que se utilizan para la supervisión a través de NPM, debe configurar las reglas del grupo de seguridad de red (NSG) para permitir el tráfico TCP en un puerto utilizado por NPM para las transacciones sintéticas. El puerto predeterminado es 8084. Esto permite que el agente de OMS instalado en la máquina virtual de Azure se comunique con un agente de supervisión local. 

Para más información, consulte  [Grupos de seguridad de red](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 

>[!NOTE]
> Asegúrese de que ha instalado los agentes (tanto el agente de servidor local como el agente de servidor de Azure) y que ha ejecutado el script de PowerShell EnableRules.ps1 antes de continuar con este paso. 

 
### <a name="discover-expressroute-peering-connections"></a>Detección de conexiones de emparejamiento de ExpressRoute 
 
1. Haga clic en la vista **Emparejamientos de ExpressRoute**.  
2. Haga clic en el botón **Detectar ahora** para detectar todos los emparejamientos privados de ExpressRoute que están conectados a las redes virtuales en la suscripción de Azure vinculada a esta área de trabajo de Log Analytics.  

>[!NOTE]  
> Actualmente, la solución detecta solo emparejamientos privados de ExpressRoute. 

>[!NOTE]  
> Solo se detectan esos emparejamientos privados, que están conectados a las redes virtuales asociadas a la suscripción vinculada con esta área de trabajo de Log Analytics. Si su instancia de ExpressRoute está conectada a redes virtuales situadas de fuera de la suscripción vinculada a esta área de trabajo, debe crear un área de trabajo de Log Analytics en esas suscripciones y usar NPM para supervisar esos emparejamientos. 

 ![Configuración de la Supervisión de ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-configure.png)
 
 Una vez completada la detección, las conexiones de los emparejamientos privados detectados se muestran en una tabla. La supervisión en estos emparejamientos estará inicialmente en estado deshabilitado. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Habilitación de la supervisión de las conexiones de emparejamiento de ExpressRoute 

1. Haga clic en la conexión de emparejamiento privado que le interese supervisar.  
2. En el panel RHS, haga clic en la casilla **Supervisar este emparejamiento**. 
3. Si piensa crear eventos de estado para esta conexión, active **Habilitar el seguimiento de estado para este emparejamiento**. 
4. Elija las condiciones de supervisión. Puede establecer umbrales personalizados para la generación de eventos de estado escribiendo valores de umbral. Siempre que el valor de la condición supere el umbral seleccionado para la conexión del emparejamiento, se generará un evento de estado. 
5. Haga clic en **Agregar agentes** para elegir los agentes de supervisión que desea utilizar para la supervisión de esta conexión de emparejamiento. Debe asegurarse de que se agregan agentes en ambos extremos de la conexión, al menos un agente de la red virtual de Azure conectado a este emparejamiento y al menos un agente local conectado a este emparejamiento. 
6. Para guardar la configuración, haga clic en **Guardar**. 

![Configuración de la Supervisión de ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-configure-discovery.png)


Después de habilitar las reglas y de seleccionar los valores y los agentes que desea supervisar, hay una espera entre 30 y 60 minutos aproximadamente para que los valores que se va a empezar a llenarse y los iconos de **Supervisión de ExpressRoute** están disponibles. Cuando se vean los iconos de supervisión, los circuitos ExpressRoute y los recursos de conexión van a estar supervisados por Network Performance Monitor. 

>[!NOTE]
> Esta función funciona de manera confiable en áreas de trabajo que se han actualizado para el nuevo lenguaje de consulta.  

## <a name="walkthrough"></a>Tutorial 

El panel de Network Performance Monitor muestra un resumen del estado de los circuitos de ExpressRoute y las conexiones de emparejamiento. 

![Panel de NPM ExpressRoute](media/log-analytics-network-performance-monitor/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Lista de circuitos 

Para ver una lista de todos los circuitos ExpressRoute supervisados, haga clic en el icono Circuitos ExpressRoute. Puede seleccionar un circuito y ver su estado de mantenimiento, gráficos de tendencias de pérdida de paquetes, uso de ancho de banda y latencia. Los gráficos son interactivos. Puede seleccionar un período de tiempo personalizado para trazar los gráficos. Puede arrastrar el mouse sobre un área del gráfico para acercar y ver los puntos de datos con precisión. 

![Lista de circuitos ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-circuits.png) 

### <a name="trend-of-loss-latency-and-throughput"></a>Tendencia de rendimiento, latencia y pérdida 

Los gráficos de ancho de banda, latencia y pérdida son interactivos. Puede acercar cualquier sección de estos gráficos con los controles de mouse. También puede ver los datos de ancho de banda, latencia y pérdida para otros intervalos; para ello, haga clic en Fecha/hora, que se encuentra debajo del botón Acciones en la parte superior izquierda. 

![Latencia de ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-latency.png) 

### <a name="peerings-list"></a>Lista de emparejamientos 

Al hacer clic en el icono **Emparejamientos privados** en el panel, se muestra una lista de todas las conexiones a redes virtuales a través de emparejamientos privados. Aquí, puede seleccionar una conexión de red virtual y ver su estado de mantenimiento, gráficos de tendencias de pérdida de paquetes, uso de ancho de banda y latencia. 

![Emparejamientos de ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Topología de circuito 

Para ver la topología de circuito, haga clic en el icono **Topología**. Esto le llevará a la vista de topología del circuito o emparejamiento seleccionado. El diagrama de topología proporciona la latencia para cada segmento de la red y cada salto de tres capas se representa mediante un nodo del diagrama. Si se hace clic en un salto, se mostrarán más detalles sobre él. Para aumentar el nivel de visibilidad para incluir saltos locales, mueva el control deslizante debajo de **Filtros**. Si se mueve el control deslizante hacia la izquierda o derecha, se aumenta o disminuye el número de saltos en el gráfico de topología. La latencia por cada segmento está visible, lo que permite un aislamiento más rápido de los segmentos de latencia elevada en la red. 

![Topología de ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-topology.png)  

### <a name="detailed-topology-view-of-a-circuit"></a>Vista de topología detallada de un circuito 

Esta vista muestra conexiones de red virtual. 

![Red virtual de ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-vnet.png)
 

### <a name="diagnostics"></a>Diagnóstico 

Network Performance Monitor le ayuda a diagnosticar diferentes problemas de conectividad en los circuitos. A continuación se enumeran algunos de ellos: 

**El circuito está inactivo.** NPM le avisa cuando se pierde la conectividad entre los recursos locales y las redes virtuales de Azure. Esto le ayuda a adoptar medidas preventivas antes de recibir las escalaciones de usuario y reducir el tiempo de inactividad. 

![Circuito ExpressRoute inactivo](media/log-analytics-network-performance-monitor/expressroute-circuit-down.png)
 

**El tráfico no fluye a través del circuito previsto.** NPM puede notificarle cada vez que el tráfico no fluya de forma inesperada a través del circuito de ExpressRoute previsto. Esto puede ocurrir si el circuito está inactivo y el tráfico fluye a través de la ruta de la copia de seguridad, o si hay un problema de enrutamiento. Esta información le ayuda a administrar de manera proactiva los problemas de configuración en las directivas de enrutamiento y a asegurarse de que se utiliza la ruta más óptima y segura. 

 

**El tráfico no fluye a través del circuito principal.** Esta función le avisa cuando el tráfico fluye a través del circuito de ExpressRoute secundario. Aunque en este caso no experimentará problemas de conectividad, la solución proactiva de los problemas del circuito le ayudan a estar mejor preparado. 

 
![Flujo de tráfico de ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-traffic-flow.png)


**Degradación debido a uso intenso.** Puede poner en correlación la tendencia de utilización del ancho de banda con la tendencia de latencia para identificar si la degradación de la carga de trabajo de Azure se debe a un pico en la utilización del ancho de banda o no, y tomar medidas en consecuencia.  

![Supervisión de ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>pasos siguientes
* [Buscar registros](log-analytics-log-searches.md) para ver datos detallados de rendimiento de red.
