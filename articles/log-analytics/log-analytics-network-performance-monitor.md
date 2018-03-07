---
title: "Solución Network Performance Monitor en Azure | Microsoft Docs"
description: Network Performance Monitor en Azure ayuda a supervisar el rendimiento de las redes, casi en tiempo real, para detectar y localizar cuellos de botella.
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
ms.openlocfilehash: 399fe552d5c7d9a96cdabc2a1dfafe99635d4a61
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/23/2018
---
# <a name="network-performance-monitor-solution-in-azure"></a>Solución Network Performance Monitor en Azure

![Símbolo de Network Performance Monitor](./media/log-analytics-network-performance-monitor/npm-symbol.png)


Network Performance Monitor (NPM) es una solución de supervisión de la red, híbrida y basada en la nube, que ayuda a supervisar lo siguiente: el rendimiento de la red entre varios puntos de la infraestructura, la conectividad de red a puntos de conexión de aplicaciones y servicios y el rendimiento del circuito ExpressRoute de Azure.  

NPM detecta problemas de red como el enrutamiento con reglas de filtrado del tráfico, los errores de enrutamiento y problemas imposibles de detectar con los métodos convencionales. La solución genera alertas, notifica cuándo se infringe un umbral en un vínculo de red, garantiza la detección oportuna de problemas de rendimiento de red y localiza el origen del problema para un determinado segmento o dispositivo de red. 

NPM ofrece tres amplias funcionalidades: 

[Monitor de rendimiento](log-analytics-network-performance-monitor-performance-monitor.md): supervise la conectividad de red entre implementaciones en la nube y ubicaciones locales; varios centros de datos y sucursales; y aplicaciones o microservicios de niveles múltiples. Con el Monitor de rendimiento, puede detectar problemas de red antes de recibir quejas de los usuarios.  

[Monitor de puntos de conexión de servicio](log-analytics-network-performance-monitor-service-endpoint.md): puede supervisar la conectividad de los usuarios a los servicios que le interesen, determinar qué infraestructura se encuentra en la ruta de acceso y dónde se producen los cuellos de botella en la red. Conozca las interrupciones antes que sus usuarios y vea el lugar exacto de los problemas a lo largo de la ruta de acceso de la red. 

Esta funcionalidad le ayuda a realizar pruebas basadas en HTTP, HTTPS, TCP e ICMP con el fin de supervisar, casi en tiempo real o históricamente, la disponibilidad y el tiempo de respuesta del servicio y la contribución de la red en la pérdida de paquetes y la latencia. Con el mapa de topología de red, puede aislar las ralentizaciones de la red e identificar las zonas problemáticas a lo largo de la ruta de acceso de la red, desde el nodo al servicio, con datos de latencia en cada salto. Con las pruebas integradas, supervise la conectividad de red a Office 365 y Dynamics CRM sin ninguna configuración previa. Con esta funcionalidad, puede supervisar la conectividad de red a cualquier punto de conexión compatible con TCP, como sitios web, aplicaciones SaaS y PaaS, bases de datos SQL, etc.  

[Supervisión de ExpressRoute](log-analytics-network-performance-monitor-expressroute.md): supervise la conectividad de extremo a extremo y el rendimiento entre sus sucursales y Azure, a través de ExpressRoute de Azure.  
 

## <a name="set-up-and-configure"></a>Instalación y configuración

### <a name="install-and-configure-agents"></a>Instalación y configuración de agentes 

Use los procesos básicos para instalar agentes en [Conexión de equipos Windows a Log Analytics](log-analytics-windows-agents.md) y [Conexión de Operations Manager con Log Analytics](log-analytics-om-agents.md).

### <a name="where-to-install-the-agents"></a>Dónde instalar los agentes 

**Monitor de rendimiento:** instale agentes de OMS en al menos un nodo conectado a cada subred desde la que va a supervisar la conectividad de red a otras subredes.  

Para supervisar un vínculo de red, debe instalar agentes en ambos puntos de conexión del vínculo en cuestión.  Si no está seguro de la topología de la red, instale los agentes en servidores con cargas de trabajo críticas entre las que desee supervisar el rendimiento de la red. Por ejemplo, si quiere supervisar la conexión de red entre un servidor web y un servidor que ejecuta SQL, instale un agente en ambos servidores. Los agentes supervisan la conectividad de red (vínculos) entre los hosts, no los hosts propiamente dichos. 

**Monitor de puntos de conexión de servicio:** instale un agente de OMS en cada nodo desde el que va a supervisar la conectividad de red al punto de conexión de servicio. Por ejemplo, si va a supervisar la conectividad de red a Office 365 desde su oficina O1, O2 y O3, instale al agente de OMS en al menos un nodo cada uno en O1, O2 y O3. 

**Supervisión de ExpressRoute:** instale al menos un agente de OMS en la red virtual de Azure y al menos un agente en la subred local, que se conecta mediante el emparejamiento privado de ExpressRoute.  

### <a name="configure-oms-agents-for-monitoring"></a>Configuración de agentes de OMS para la supervisión  

NPM usa transacciones sintéticas para supervisar el rendimiento de red entre los agentes de origen y de destino. La solución permite elegir entre el protocolo TCP e ICMP para la supervisión en las funcionalidades Monitor de rendimiento y Monitor de puntos de conexión de servicio, mientras que TCP se usa para la Supervisión de ExpressRoute. Asegúrese de que el firewall permita la comunicación entre los agentes de OMS que se usan para supervisar en el protocolo que ha elegido para la supervisión.  

**Protocolo TCP:** si ha elegido el protocolo TCP para la supervisión, abra el puerto de firewall en los agentes que se usan en las funcionalidades Monitor de rendimiento y Supervisión de ExpressRoute, para asegurarse de que estos pueden conectarse entre sí. Para ello, ejecute el script de PowerShell EnableRules.ps1 sin parámetros en una ventana de PowerShell con privilegios administrativos.  

El script crea las claves del Registro que necesita la solución y crea reglas del firewall de Windows para permitir que los agentes establezcan conexiones TCP entre sí. Las claves del Registro que crea el script también especifican si se deben escribir los registros de depuración y la ruta del archivo de registro. Asimismo, define el puerto TCP del agente empleado para establecer la comunicación. El script establece automáticamente los valores de estas claves, por lo que no debe cambiarlas manualmente. El puerto que se abre de forma predeterminada es 8084. Puede utilizar un puerto personalizado especificando el parámetro portNumber en el script. Sin embargo, se debe utilizar el mismo puerto en todos los equipos en los que se ejecute el script. 

>[!NOTE]
> El script configura solo el firewall de Windows localmente. Si tiene un firewall de red, debe asegurarse de que permite el tráfico destinado al puerto TCP que usa NPM. 

>[!NOTE]
> No es necesario ejecutar el script de PowerShell EnableRules.ps1 para el Monitor de puntos de conexión de servicio. 

 

**Protocolo ICMP**: si ha elegido el protocolo ICMP para la supervisión, habilite las reglas de firewall siguientes para usar ICMP de forma confiable: 

 
```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```
 

### <a name="configure-the-solution"></a>Configuración de la solución 

1. Agregue la solución Network Performance Monitor (monitor de rendimiento de red) al área de trabajo desde [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) o mediante el proceso descrito en el artículo sobre [incorporación de soluciones de Log Analytics desde la Galería de soluciones](log-analytics-add-solutions.md). 
2. Abra el área de trabajo de Log Analytics y haga clic en el icono **Overview** (Información general).  
3. Haga clic en el icono denominado **Network Performance Monitor** (Monitor de rendimiento de red)  con el mensaje *Solution requires additional configuration* (La solución necesita una configuración adicional).

    ![Icono de NPM](media/log-analytics-network-performance-monitor/npm-config.png)

3. En la página **Setup** (Configuración), verá la opción para instalar agentes de OMS y configurar los agentes para la supervisión en la vista **Common Settings** (Configuración común). Como se explicó anteriormente, si ya ha instalado y configurado agentes de OMS, haga clic en la vista de **configuración** para configurar la funcionalidad que le interese usar.  

    **Vista de Monitor de rendimiento**: elija qué protocolo se debe usar para las transacciones sintéticas en la regla de monitor de rendimiento predeterminada y haga clic en Save (Guardar) y Continue (Continuar). Esta selección de protocolo contiene únicamente la regla predeterminada generada por el sistema y debe elegir el protocolo cada vez que cree explícitamente una regla de Monitor de rendimiento. Siempre puede mover la configuración de reglas predeterminada en la pestaña Performance Monitor (Monitor de rendimiento) (que aparece después de completar la configuración del día 0) y cambiar el protocolo más adelante. En caso de que no esté interesado en la funcionalidad Monitor de rendimiento, puede deshabilitar la regla predeterminada en la configuración de reglas predeterminada de la pestaña Performance Monitor (Monitor de rendimiento). 

    ![Configuración de NPM](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
    **Vista de Monitor de puntos de conexión de servicio**: esta funcionalidad proporciona pruebas preconfiguradas integradas para supervisar la conectividad de red a Office 365 y Dynamcis365 desde los agentes. Elija los servicios Office365 y Dynamcis365 que tiene interés en supervisar; para ello, active las casillas situadas al lado. Elija los agentes desde los que desea realizar la supervisión y haga clic en el botón Add Agents (Agregar agentes). Si no quiere usar esta funcionalidad o quiere configurarla más tarde, puede decidir omitir este paso y hacer clic directamente en **Save** (Guardar) y **Continue** (Continuar) sin seleccionar nada.  

    ![Configuración de NPM](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

    **Vista de Supervisión de ExpressRoute**: haga clic en el botón **Discover Now** (Detectar ahora) para detectar todos los emparejamientos privados de ExpressRoute que están conectados a las redes virtuales en la suscripción de Azure vinculada a esta área de trabajo de Log Analytics.  


    >[!NOTE] 
    > Actualmente, la solución detecta solo emparejamientos privados de ExpressRoute. 

    >[!NOTE] 
    > Solo se detectan esos emparejamientos privados, que están conectados a las redes virtuales asociadas a la suscripción vinculada con esta área de trabajo de Log Analytics. Si su instancia de ExpressRoute está conectada a redes virtuales situadas fuera de la suscripción vinculada a esta área de trabajo, debe crear un área de trabajo de Log Analytics en esas suscripciones y usar NPM para supervisar esos emparejamientos. 

    ![Configuración de NPM](media/log-analytics-network-performance-monitor/npm-express-route.png)

    Una vez completada la detección, los emparejamientos privados detectados se muestran en una tabla.  

    ![Configuración de NPM](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
    La supervisión en estos emparejamientos está inicialmente en estado deshabilitado. Haga clic en cada emparejamiento que tenga interés en supervisar y configure la supervisión para ellos en la vista de detalles del lado derecho (RHS).  Haga clic en el botón Save (Guardar) para guardar la configuración. Para más información, consulte [Configure ExpressRoute monitoring]() (Configuración de la supervisión de ExpressRoute).  

    Una vez completada la configuración, se tarda entre 30 minutos y una hora en rellenar los datos. Mientras la solución agrega datos de la red, verá *Solution requires additional configuration* (La solución necesita una configuración adicional) en el icono de información general de NPM. Una vez que los datos se recopilan e indexan, el icono de información general cambia y muestra el resumen de mantenimiento de la red. Puede elegir entonces editar la supervisión de los nodos en los que están instalados los agentes de OMS, así como las subredes detectadas desde el entorno 

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Edición de la configuración de supervisión de subredes y nodos 

Todas las subredes que tienen instalado al menos un agente se muestran en la pestaña  Subnetworks (Subredes) de la página de configuración. 


Para habilitar o deshabilitar la supervisión de subredes determinadas, siga estos pasos: 

1. Active o desactive la casilla junto al **identificador de subred** y asegúrese de que **Use for Monitoring** (Usar para la supervisión) está activada o desactivada, según sea adecuado. Puede seleccionar o borrar varias subredes. Cuando esta casilla se desactiva, no se supervisan las subredes, ya que se actualizan los agentes para que dejen de hacer ping a los demás agentes. 
2. Elija los nodos que desee supervisar de esa subred concreta; para ello, selecciónelos de la lista y muévalos entre las listas que contienen los nodos supervisados y los que no se supervisan. Puede agregar una **descripción** personalizada a la subred. 
3. Para guardar la configuración, haga clic en **Guardar**. 

#### <a name="choose-nodes-to-monitor"></a>Elección de los nodos que desee supervisar

En la pestaña **Nodos** se muestran todos los nodos que tienen un agente instalado. 

1. Active o desactive los nodos que desee supervisar o dejar de supervisar. 
2. Active la casilla  **Use for Monitoring** (Usar para la supervisión) o desactívela, según corresponda. 
3. Haga clic en **Save**(Guardar). 


Configure las funcionalidades en las que está interesado: 
- Configure el [Monitor de rendimiento](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- Configure el [Monitor de puntos de conexión de servicio](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- Configure la [Supervisión de ExpressRoute](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Detalles de la recopilación de datos
El Monitor de rendimiento de red usa los paquetes de protocolo de enlace TCP SYN-SYNACK-ACK cuando se elige TCP, e ICMP ECHO ICMP ECHO REPLAY cuando se elige ICMP como protocolo para recopilar información de pérdida y latencia. Traceroute también se usa para obtener información sobre la topología.

En la siguiente tabla se muestran los métodos de recopilación de datos y otros detalles sobre cómo se reúnen los datos para Monitor de rendimiento de red.

| plataforma | Agente directo | Agente de SCOM | Azure Storage | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Protocolos de enlace TCP/mensajes ICMP ECHO cada 5 segundos, datos enviados cada 3 minutos |
 

 
La solución usa transacciones sintéticas para evaluar el estado de la red. Los agentes de OMS instalados en diversos puntos de la red intercambian entre sí paquetes TCP o ICMP Echo (según el protocolo seleccionado para la supervisión). En el proceso, los agentes conocen el tiempo de ida y vuelta y se enteran de la pérdida de paquetes, si la hay. Periódicamente, cada agente también realiza un seguimiento de la ruta hasta los demás agentes para descubrir las diversas rutas de la red que se deben comprobar. Con estos datos, los agentes pueden deducir la latencia de red y las cifras de pérdida de paquetes. Cada cinco segundos los agentes repiten las pruebas y agregan datos en un período de tres minutos antes de cargarlos en el servicio Log Analytics. 



>[!NOTE]
> A pesar de que los agentes se comunican entre sí con frecuencia, no generan una gran cantidad de tráfico de red mientras llevan a cabo las pruebas. Los agentes utilizan exclusivamente los paquetes de protocolo de enlace SYNACK-TCP SYN-ACK para determinar la pérdida y la latencia; es decir, no se intercambia ningún paquete de datos. Durante este proceso, los agentes solo se comunican entre sí cuando resulta necesario, y la topología de comunicación de los agentes está optimizada para reducir el tráfico de red.

## <a name="using-the-solution"></a>Uso de la solución 

### <a name="npm-overview-tile"></a>Icono de información general de NPM 

Una vez que ha habilitado la solución Network Performance Monitor el icono de la solución en la página de información general de OMS ofrece una visión general rápida del mantenimiento de la red. 

 ![Icono de información general de NPM](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Panel Monitor de rendimiento de red 

En la página **Top Network Health Events** (Eventos de mantenimiento de red principales) se proporciona una lista de los eventos de mantenimiento y las alertas más recientes del sistema, así como el tiempo que lleva el evento activo. Cada vez que el valor de la métrica seleccionada (pérdida, latencia, tiempo de respuesta o utilización de ancho de banda) para la regla de supervisión supera el umbral, se genera un evento de mantenimiento o una alerta. 

La página  **Performance Monitor** (Monitor de rendimiento) le proporciona un resumen del mantenimiento de los vínculos de red y de subred que supervisa la solución. El icono de topología indica al número de rutas de acceso de red que se supervisan en la red. Al hacer clic en este icono directamente, se desplaza a la vista de topología. 

La página  **Service Endpoint Monitor** (Monitor de puntos de conexión de servicio) le proporciona un resumen del mantenimiento de las distintas pruebas que ha creado. El icono de topología indica al número de puntos de conexión que se supervisan. Al hacer clic en este icono directamente, se desplaza a la vista de topología.

La página  **ExpressRoute Monitor** (Supervisión de ExpressRoute) le proporciona un resumen del mantenimiento de las diversas conexiones de emparejamiento de ExpressRoute que supervisa la solución. El icono de topología informa del número de rutas de acceso de red mediante los circuitos de ExpressRoute que se supervisan en la red. Al hacer clic en este icono directamente, se desplaza a la vista de topología.

La página  **Common Queries** (Consultas comunes) contiene un conjunto de consultas de búsqueda que capturan directamente los datos de supervisión de la red sin procesar. Puede usar estas consultas como punto de partida a fin de crear las suyas propias para generar informes personalizados. 

![Panel de NPM](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Exploración en profundidad 

Puede hacer clic en los diversos vínculos del panel de la solución para profundizar más en cualquier área de interés. Por ejemplo, cuando vea aparecer en el panel una alerta o un vínculo de red incorrecto, puede hacer clic en ellos para investigar más. Se le dirigirá a una página que enumera todos los vínculos de subred del vínculo de red concreto. Puede ver la pérdida, la latencia y el estado de mantenimiento de cada vínculo de subred e identificar rápidamente aquellos que estén ocasionando el problema. Después, puede hacer clic en  **View node links**  (Ver vínculos de nodo) para ver todos los vínculos de nodo del vínculo de subred incorrecto. Además, puede ver los vínculos individuales de nodo a nodo y encontrar los vínculos de nodo incorrectos. 

Puede hacer clic en  **View topology**  (Ver topología) para ver la topología de salto a salto de las rutas entre los nodos de origen y de destino. Las rutas con un mantenimiento incorrecto se muestran en rojo, y se puede ver la latencia a la que ha contribuido cada salto y así identificar rápidamente el problema para una parte determinada de la red. 

 

### <a name="network-state-recorder"></a>Grabadora de estado de la red 

Cada vista muestra una instantánea del estado de su red en un momento determinado del tiempo. De forma predeterminada, se muestra el estado más reciente. La barra en la parte superior de la página muestra el punto en el tiempo para el que se muestra el estado. Puede elegir retroceder en el tiempo y ver la instantánea del estado de su red haciendo clic en la barra en Actions (Acciones). También puede elegir habilitar o deshabilitar la actualización automática de cualquier página mientras ve el estado más reciente. 

 ![Grabadora de estado de la red](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Gráficos de tendencias 

En cada nivel que se profundiza, se puede ver la tendencia de la métrica aplicable: pérdida, latencia, tiempo de respuesta y utilización de ancho de banda. Puede cambiar el intervalo de tiempo empleado para la tendencia mediante el control del tiempo de la parte superior del gráfico. 

Los gráficos de tendencias muestran una perspectiva histórica del rendimiento de una métrica de rendimiento. Algunos problemas de red son transitorios por naturaleza y, por lo tanto, resultarían difíciles de encontrar examinando únicamente el estado actual de la red. Esto se debe a que los problemas pueden manifestarse rápidamente y desaparecer antes de que nadie se percate para volver a surgir más adelante. Dichos problemas transitorios también pueden resultar difíciles para los administradores de aplicaciones, ya que tales problemas suelen surgir como incrementos inexplicables en el tiempo de respuesta de la aplicación, incluso cuando todos los componentes de ella parecen ejecutarse a la perfección. 

Puede detectar con facilidad estos tipos de problemas si examina un gráfico de tendencias, donde el problema aparecer como un pico repentino de latencia o pérdida de paquetes en la red. Luego, puede investigar el problema mediante la grabadora de estado de la red para ver la instantánea y la topología de red de ese punto en el tiempo en que se produjo el problema. 

 
![Gráficos de tendencias](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mapa de topología 

NPM muestra la topología salto a salto de rutas entre el punto de conexión de origen y de destino, en un mapa de topología interactivo. Para ver el mapa de topología, haga clic en el icono **Topology** (Topología) en el panel de la solución o haga clic en el vínculo **View topology** (Ver topología) en las páginas de exploración en profundidad.  

En el mapa de topología se muestra el número de rutas que existen entre el origen y el destino y cuáles son las rutas que toman los paquetes de datos. También se puede ver la latencia a la que contribuye cada salto de red. Todas las rutas de acceso en las que la latencia total está por encima del umbral (definido en la regla de supervisión correspondiente) se muestran en rojo.  

Cuando haga clic en un nodo o pase el mouse por encima de él encima del mapa de topología, verá las propiedades del nodo, como el FQDN o la dirección IP. Haga clic en un salto para ver su dirección IP. Puede identificar el salto de red problemático si observa la latencia a la que contribuye ese salto. Puede elegir filtrar rutas determinadas mediante los filtros que aparecen en el panel de acciones contraíble. También puede simplificar las topologías de red ocultando los saltos intermedios con el control deslizante del panel de acciones. Además, puede acercar o alejar el mapa de topología con la rueda del mouse. 

Observe que la topología mostrada en el mapa corresponde al nivel 3 y no contiene ningún dispositivo ni conexión de nivel 2. 

 
![Mapa de topología](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Búsqueda de Log Analytics 

Todos los datos que se exponen gráficamente mediante el panel de NPM y las páginas de exploración en profundidad también se encuentran disponibles de forma nativa en la [búsqueda de Log Analytics](log-analytics-log-search-new.md). Puede realizar un análisis interactivo de los datos en el repositorio, correlacionar datos procedentes de distintos orígenes, crear alertas personalizadas, crear vistas personalizadas y exportar los datos a Excel, Power BI o un vínculo que se puede compartir. El área Common Queries (Consultas comunes) del panel incorpora una serie de consultas útiles que puede usar como punto de partida para crear sus propios informes y consultas. 

 

## <a name="provide-feedback"></a>Envío de comentarios 

**UserVoice**: puede publicar sus ideas sobre las características de Network Performance Monitor (monitor de rendimiento de red) en las que le gustaría que trabajásemos. Visite nuestra  [página de UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

**Únase a nuestra cohorte** - siempre estamos interesados en que se unan nuevos clientes a nuestra cohorte. Como parte de todo ello, podrá obtener un acceso anticipado a las nuevas características y ayudarnos a mejorar Network Performance Monitor. Si está interesado en unirse, rellene esta  [encuesta rápida](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>pasos siguientes 
- Más información sobre [Monitor de rendimiento](log-analytics-network-performance-monitor-performance-monitor.md), [Monitor de puntos de conexión de servicio](log-analytics-network-performance-monitor-performance-monitor.md) y [Supervisión de ExpressRoute](log-analytics-network-performance-monitor-expressroute.md). 
