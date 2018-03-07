---
title: "Solución Monitor de rendimiento de red en Azure Log Analytics | Microsoft Docs"
description: "La funcionalidad Administrador de puntos de conexión de servicio en Network Performance Monitor permite supervisar la conectividad de red con cualquier punto de conexión que tiene un puerto TCP abierto."
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
ms.openlocfilehash: ba19a4fc24668bff27c961b5b415f840d1132a34
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="service-endpoint-monitor"></a>Monitor de puntos de conexión de servicio

La funcionalidad Monitor de puntos de conexión de servicio en [Network Performance Monitor](log-analytics-network-performance-monitor.md) permite supervisar la conectividad de red con cualquier punto de conexión que tiene un puerto TCP abierto. Dichos puntos de conexión incluyen sitios web, aplicaciones SaaS, aplicaciones PaaS y bases de datos SQL. 

Puede realizar las funciones siguientes con **Monitor de puntos de conexión**: 

- Supervisar la conectividad de red con las aplicaciones y los servicios de red (como Office 365, Dynamics CRM, aplicaciones de línea de negocio internas, base de datos SQL, etc.) desde varias sucursales o ubicaciones. 
- Integrar pruebas para supervisar la conectividad de red con puntos de conexión de Office365 y Dynamics365. 
- Determinar el tiempo de respuesta, la latencia de la red y la pérdida de paquetes al conectarse con el punto de conexión. 
- Determinar si el rendimiento de una aplicación es deficiente debido a la red o porque hay algún problema en el extremo del proveedor de la aplicación. 
- Identificar las zonas activas de la red que podrían estar generando un rendimiento deficiente de la aplicación mediante la visualización de la latencia aportada por cada salto en un mapa de topología. 


![Monitor de puntos de conexión de servicio](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>Configuración 
Para abrir la configuración de Network Performance Monitor, abra la [solución de Network Performance Monitor](log-analytics-network-performance-monitor.md) y haga clic en el botón **Configurar**.

![Configuración de Network Performance Monitor](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-oms-agents-for-the-monitoring"></a>Configure los agentes de OMS para la supervisión.  
Habilite las reglas de firewall siguientes en los nodos que se usan para la supervisión de manera que la solución pueda detectar la topología desde los nodos al punto de conexión de servicio: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action 
```

### <a name="create-service-endpoint-monitor-tests"></a>Creación de pruebas de Monitor de puntos de conexión de servicio 

Comience a crear las pruebas para supervisar la conectividad de red con los puntos de conexión de servicio 

1. Haga clic en la pestaña **Monitor de puntos de servicio**.
2. Haga clic en **Agregar prueba** y escriba el nombre y la descripción de la prueba. 
3. Seleccione el tipo de prueba:<br>Seleccione **Prueba web** si supervisa la conectividad con un servicio que responde a las solicitudes HTTP/S, como outlook.office365.com, bing.com.<br>Seleccione **Prueba de red** si supervisa la conectividad con un servicio que responde a una solicitud TCP pero no a una solicitud HTTP/S, como SQL Server, servidor FTP, puerto SSH, etc. 
4. Si no quiere hacer medidas en la red (latencia de la red, pérdida de paquetes, detección de topología), desactive el cuadro de texto. Se recomienda que lo mantenga activado para aprovechar al máximo la funcionalidad. 
5. Escriba la dirección IP, el FQDN o la dirección URL de destino en que desea supervisar la conectividad de red.  
6. Escriba el número de puerto del servicio de destino. 
7. Escriba la frecuencia con que desea que se ejecute la prueba. 
8. Seleccione los nodos desde los cuales quiere supervisar la conectividad de red con el servicio. 

    >[!NOTE]
    > En el caso de los nodos basados en Windows Server, la funcionalidad usa solicitudes basadas en TCP para hacer las medidas en la red. En el caso de los nodos basados en el cliente Windows, la funcionalidad usa solicitudes basadas en ICMP para hacer las medidas en la red. En algunos casos, la aplicación de destino bloquea la solicitud basada en ICMP entrante debido a que cuando los nodos se basan en el cliente Windows, la solución no puede hacer medidas en la red. Por tanto, en esos casos se recomienda usar los nodos basados en Windows Server. 

9. Si no quiere crear eventos de estado para los elementos que seleccionó, desactive la opción **Enable health monitoring on the targets covered by this test** (Habilitar el seguimiento de estado en los destinos que abarca esta prueba). 
10. Elija las condiciones de supervisión. Puede establecer umbrales personalizados para la generación de eventos de estado escribiendo valores de umbral. Siempre que el valor de la condición supere el umbral seleccionado para el par de red/subred seleccionado, se generará un evento de estado. 
11. Para guardar la configuración, haga clic en **Guardar**. 

 ![Configuración del Monitor de puntos de conexión de servicio](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Tutorial 

Vaya a la vista del panel Network Performance Monitoring y observe la página **Service Endpoint Monitor** (Monitor de puntos de conexión de servicio) para ver un resumen del estado de las distintas pruebas que creó.  

![Página del Monitor de puntos de conexión de servicio](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

Haga clic en el icono para explorar en profundidad y ver los detalles de las pruebas en la página **Pruebas**. En la tabla LHS, puede ver el estado en un momento dado y el valor del tiempo de respuesta del servicio, la latencia de red y la pérdida de paquetes en todas las pruebas. Puede usar el control Grabadora de estado de la red para ver una instantánea de la red en otro momento anterior. Haga clic en la prueba de la tabla que desea investigar. Puede ver la tendencia histórica de los valores de pérdida, latencia y tiempo de respuesta en los gráficos del panel RHS. Haga clic en el vínculo Detalles de la prueba para ver el rendimiento de cada nodo. 

![Pruebas del Monitor de puntos de conexión de servicio](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

En la vista **Test Nodes** (Nodos de prueba), puede observar la conectividad de red desde cada nodo. Haga clic en el nodo que presenta una degradación del rendimiento.  Este es el nodo desde se observa que la aplicación se ejecuta lentamente. 

Determine si el rendimiento de una aplicación es deficiente debido a la red o porque hay algún problema en el extremo del proveedor de la aplicación mediante la observación de la correlación entre el tiempo de respuesta de la aplicación y la latencia de red. 

**Problema de aplicación:** si hay un pico en el tiempo de respuesta, pero la latencia de red es coherente, se sugiere que la red funciona correctamente y el problema se debe a un error en el extremo de la aplicación.  

![Problema de aplicación del Monitor de puntos de conexión de servicio](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

**Problema de red:** si se produce un pico en el tiempo de respuesta junto con un pico correspondiente en la latencia de la red, se sugiere que el aumento en el tiempo de respuesta se debe a un aumento en la latencia de la red.  

![Problema de red del Monitor de puntos de conexión de servicio](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

Una vez que determina que el problema se debe a la red, puede hacer clic en el vínculo de vista **Topología** para identificar el salto problemático en el mapa de topología. Por ejemplo, en la imagen siguiente puede ver que, de una latencia total de 105 ms entre el nodo y el punto de conexión de la aplicación, 96 ms se debe al salto marcado en rojo. Una vez que identifica el salto problemático, puede realizar alguna acción correctiva.  

![Pruebas del Monitor de puntos de conexión de servicio](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnóstico 

Si observa un comportamiento anormal, siga estos pasos:

Si el tiempo de respuesta del servicio, la pérdida de paquetes y la latencia aparecen como N/D, puede deberse a una o varias de las razones siguientes:
- La aplicación está inactiva.
- El nodo que se usa para comprobar la conectividad de red con el servicio está inactivo.
- El destino que se escribió en la configuración de prueba no es correcto.
- El nodo no tiene conectividad de red ninguna.

Si aparece un tiempo de respuesta del servicio válido, pero tanto la pérdida de datos como la latencia aparecen como N/D, lo que puede deberse a una o varias de las razones siguientes:
- Si el nodo que se usa para comprobar la conectividad de red con el servicio es un equipo cliente Windows, independientemente de si el servicio de destino bloquea las solicitudes ICMP o un firewall de red bloquea las solicitudes ICMP que se originan del nodo.
- La casilla de verificación **Perform network measurements** (Hacer medidas en la red) se desactivó en la configuración de prueba. 

Si el tiempo de respuesta del servicio es N/D, pero tanto la pérdida de paquetes como la latencia son válidas, por lo que se sugiere que el servicio de destino no sea una aplicación web. Edite la configuración de prueba y elija el tipo de prueba como Prueba de red en lugar de Prueba web. 

Si la aplicación se ejecuta demasiado lento, determinar si el rendimiento de una aplicación es deficiente debido a la red o porque hay algún problema en el extremo del proveedor de la aplicación.


## <a name="next-steps"></a>pasos siguientes
* [Buscar registros](log-analytics-log-searches.md) para ver datos detallados de rendimiento de red.
