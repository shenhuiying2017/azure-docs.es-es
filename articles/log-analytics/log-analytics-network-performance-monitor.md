---
title: "Solución Monitor de rendimiento de red en Azure Log Analytics | Microsoft Docs"
description: El Monitor de rendimiento de red de Azure Log Analytics le ayuda a supervisar el rendimiento de las redes en tiempo real con la finalidad de detectar y encontrar cuellos de botella en el rendimiento de la red.
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.openlocfilehash: c6568e491429f6046ab164ab5eacd0ae5846e201
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="network-performance-monitor-solution-in-log-analytics"></a>Solución Monitor de rendimiento de red de Log Analytics

![Símbolo de Network Performance Monitor](./media/log-analytics-network-performance-monitor/npm-symbol.png)

En este documento se describe la instalación y el uso de la solución Monitor de rendimiento de red de Log Analytics, que le ayuda a supervisar el rendimiento de sus redes, prácticamente en tiempo real, para detectar y encontrar los cuellos de botella en el rendimiento de la red. Con la solución Monitor de rendimiento de red, puede supervisar la pérdida y la latencia entre dos redes, subredes o servidores. Monitor de rendimiento de red detecta posibles problemas de red, como enrutamiento del tráfico a "agujeros negros", errores de enrutamiento y problemas que los métodos de supervisión de red convencionales no pueden encontrar. Monitor de rendimiento de red genera alertas y notifica cómo y cuándo se supera un umbral en un vínculo de red. El sistema puede aprender automáticamente estos umbrales, o bien puede configurarlos para usar reglas de alerta personalizadas. Monitor de rendimiento de red garantiza la detección oportuna de los problemas de rendimiento de red y localiza el origen del problema en un segmento o dispositivo de red determinado.

Puede encontrar problemas de red con el panel de la solución, que muestra información resumida sobre la red, incluidos recientes eventos relativos al estado de la red, vínculos de red incorrectos y vínculos de subred que afrontan una elevada pérdida de paquetes y latencia. Puede explorar un vínculo de red en profundidad para ver el estado actual de vínculos de subred, así como los de nodo a nodo. También puede ver la tendencia histórica de pérdida y la latencia en los niveles de red, subred y nodo a nodo. Puede encontrar problemas de red transitorios visualizando gráficos de tendencias históricas de pérdida de paquetes y latencia, y buscar cuellos de botella de red en un mapa de topología. El gráfico interactivo de la topología le permite visualizar las rutas de red de salto a salto y determinar el origen del problema. Al igual que otras soluciones, puede utilizar la búsqueda de registros para distintos requisitos de análisis con el objetivo de crear informes personalizados basados en los datos recopilados por Monitor de rendimiento de red.

Esta solución emplea transacciones sintéticas como el mecanismo principal para encontrar errores en la red. Por ello, puede utilizarla con independencia del modelo o el proveedor del dispositivo de red. Funciona en entornos locales, híbridos y en la nube (IaaS). La solución detecta automáticamente la topología y las diversas rutas de su red.

Los productos de supervisión de red habituales se centran en la supervisión del estado de los dispositivos de red (enrutadores, conmutadores, etc.), pero no aportan información sobre la calidad real de conectividad de red entre dos puntos, una ventaja que sí ofrece Monitor de rendimiento de red.

### <a name="using-the-solution-standalone"></a>Uso de la solución de forma independiente
Si desea supervisar la calidad de las conexiones de red entre sus cargas de trabajo, redes, centros de datos u oficinas cruciales, puede usar la solución Monitor de rendimiento de red por sí sola para vigilar el estado de la conectividad entre:

* varios centros de datos u oficinas que estén conectados con una red pública o privada;
* cargas de trabajo que ejecuten aplicaciones de línea de negocio;
* servicios en la nube pública, como Microsoft Azure o Amazon Web Services (AWS) y redes locales, si tiene a su disposición IaaS (VM) y ha configurado las puertas de enlace para permitir la comunicación entre las redes locales y en la nube;
* redes locales y de Azure cuando utilice ExpressRoute.

### <a name="using-the-solution-with-other-networking-tools"></a>Uso de la solución con otras herramientas de redes
Si desea supervisar una aplicación de línea de negocio, puede usar la solución Monitor de rendimiento de red como un complemento a otras herramientas de red. Una red lenta puede conllevar que las aplicaciones se ralenticen; Monitor de rendimiento de red puede ayudarlo a investigar los problemas de rendimiento de las aplicaciones que se deban a defectos de red subyacentes. Como la solución no precisa ningún tipo de acceso a los dispositivos de red, el administrador de aplicaciones no tiene que depender de que un equipo de redes le facilite información sobre cómo la red afecta a las aplicaciones.

Además, si ya ha invertido en otras herramientas de supervisión de red, nuestra solución puede actuar como un complemento para estas, porque la mayoría de las soluciones convencionales de supervisión de red no ofrecen información sobre las métricas de rendimiento de red de un extremo a otro, como la pérdida y la latencia.  La solución Monitor de rendimiento de red puede ayudar a suplir esa laguna.

## <a name="installing-and-configuring-agents-for-the-solution"></a>Instalación y configuración de agentes para la solución
Use los procesos básicos para instalar agentes en [Conexión de equipos Windows a Log Analytics](log-analytics-windows-agents.md) y [Conexión de Operations Manager con Log Analytics](log-analytics-om-agents.md).

> [!NOTE]
> Debe instalar al menos 2 agentes si desea disponer de suficientes datos para detectar y supervisar los recursos de red. De lo contrario, la solución permanecerá en un estado de configuración hasta que instale y configure más agentes.
>
>

### <a name="where-to-install-the-agents"></a>Dónde instalar los agentes
Antes de instalar los agentes, tenga en cuenta la topología de la red y qué partes de esta última desea supervisar. Se recomienda instalar más de un agente para cada subred que desee supervisar. Dicho de otro modo, para cada subred que desee supervisar, elija dos o más servidores o máquinas virtuales e instale el agente en ellos.

Si no está seguro de la topología de la red, instale los agentes en servidores con cargas de trabajo críticas donde desee supervisar el rendimiento de la red. Por ejemplo, es posible que desee efectuar un seguimiento de una conexión de red entre un servidor web y uno en el que se ejecute SQL Server. En este ejemplo, podría instalar un agente en ambos servidores.

Los agentes supervisan la conectividad de red (los vínculos) entre los hosts, no los propios hosts. Por lo tanto, para supervisar un vínculo de red, debe instalar agentes en ambos puntos de conexión del vínculo en cuestión.

### <a name="configure-agents"></a>Configuración de los agentes

Si piensa usar el protocolo ICMP para las transacciones sintéticas, tiene que habilitar las siguientes reglas de firewall para utilizar ICMP de forma confiable:

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow
```


Si planea usar el protocolo TCP, tiene que abrir los puertos de firewall en esos equipos a fin de asegurarse de que los agentes puedan comunicarse. Tiene que descargar y ejecutar el [script de PowerShell EnableRules.ps1](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) sin parámetros en una ventana de PowerShell con privilegios administrativos.

El script crea las claves del Registro que necesita el Monitor de rendimiento de red y genera reglas del firewall de Windows para permitir que los agentes establezcan conexiones TCP entre sí. Las claves del Registro que crea el script también especifican si se deben escribir los registros de depuración y la ruta del archivo de registro. Asimismo, define el puerto TCP del agente empleado para establecer la comunicación. El script establece automáticamente los valores de estas claves, por lo que no debe cambiarlas manualmente.

El puerto que se abre de forma predeterminada es 8084. Puede utilizar un puerto personalizado especificando el parámetro `portNumber` en el script. Sin embargo, se debe utilizar el mismo puerto en todos los equipos en los que se ejecute el script.

> [!NOTE]
> El script EnableRules.ps1 solo configura reglas del firewall de Windows en el equipo donde lo ejecute. Si tiene un firewall de red, debe asegurarse de que permite el tráfico destinado al puerto TCP que esté utilizando Monitor de rendimiento de red.
>
>

## <a name="configuring-the-solution"></a>Configuración de la solución
Utilice la siguiente información para instalar y configurar la solución.

1. La solución Monitor de rendimiento de red adquiere datos de equipos que ejecuten Windows Server 2008 SP 1 o posterior, o bien Windows 7 SP1 o posterior, que son los mismos requisitos de Microsoft Monitoring Agent (MMA). Los agentes NPM se pueden ejecutar también en sistemas operativos escritorio/cliente de Windows (Windows 10, Windows 8.1, Windows 8 y Windows 7).
    >[!NOTE]
    >Los agentes para sistemas operativos de servidor de Windows admiten TCP e ICMP como protocolos de transacción sintética. Pero los agentes para sistemas operativos de cliente de Windows admiten solo ICMP como protocolo para la transacción sintética.

2. Agregue la solución Network Performance Monitor (monitor de rendimiento de red) al área de trabajo desde [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) o mediante el proceso descrito en el artículo sobre [incorporación de soluciones de Log Analytics desde la Galería de soluciones](log-analytics-add-solutions.md).  
   ![Símbolo del Network Performance Monitor](./media/log-analytics-network-performance-monitor/npm-symbol.png)
3. En el portal OMS, verá un icono nuevo titulado **Monitor de rendimiento de red** con el mensaje *La solución necesita una configuración adicional*. Debe configurar la solución para que agregue redes según las subredes y los nodos detectados por los agentes. Haga clic en **Monitor de rendimiento de red** para empezar a configurar la red predeterminada.  
   ![La solución necesita una configuración adicional](./media/log-analytics-network-performance-monitor/npm-config.png)

### <a name="configure-the-solution-with-a-default-network"></a>Configuración de la solución con una red predeterminada
En la página de configuración, verá una única red denominada **Predeterminada**. Si no ha definido ninguna red, todas las subredes detectadas automáticamente se colocan en la red Predeterminada.

Siempre que cree una red, se agrega una subred en ella y se quita dicha subred de la red Predeterminada. Si elimina una red, se devuelven automáticamente todas sus subredes a la red Predeterminada.

Dicho de otro modo, la red Predeterminada es el contenedor de todas las subredes que no se incluyan en alguna red definida por el usuario. No puede editar ni eliminar la red Predeterminada. Siempre permanece en el sistema. Sin embargo, puede crear todas las redes que necesite.

En la mayoría de los casos, las subredes de la organización estarán organizadas en más de una red y, por tanto, deberá crear una o más redes para agrupar las subredes de forma lógica.

### <a name="create-new-networks"></a>Creación de nuevas redes
En el Monitor de rendimiento de red, una red es un contenedor de subredes. Puede crear una red con cualquier nombre que desee y agregarle subredes. Por ejemplo, puede crear una red denominada "*Edificio1*" y, después, agregar subredes, o bien puede crear una red denominada *DMZ* y, luego, agregar a ella todas las subredes que pertenezcan a la red perimetral.

#### <a name="to-create-a-new-network"></a>Para crear una nueva red
1. Haga clic en **Agregar red** y, después, escriba el nombre de red y la descripción.
2. Seleccione una o varias subredes y, luego, haga clic en **Agregar**.
3. Para guardar la configuración, haga clic en **Guardar**.  
   ![Agregar red](./media/log-analytics-network-performance-monitor/npm-add-network.png)

### <a name="wait-for-data-aggregation"></a>Tiempo de espera hasta que se agreguen los datos
Tras guardar la configuración por primera vez, la solución empezará a recopilar información de latencia y pérdida de paquetes de la red entre los nodos en los que se hayan instalado los agentes. Este proceso puede tardar bastante, en ocasiones más de 30 minutos. Durante este estado, en el icono Monitor de rendimiento de red de la página Información general se muestra un mensaje que dice *Data aggregation in process* (Agregación de datos en curso).

![Agregación de datos en curso](./media/log-analytics-network-performance-monitor/npm-aggregation.png)

Cuando se hayan cargado los datos, verá que en el icono Monitor de rendimiento de red se muestra la información actualizada.

![Icono Monitor de rendimiento de red](./media/log-analytics-network-performance-monitor/npm-tile.png)

Haga clic en el icono para ver el panel Monitor de rendimiento de red.

![Panel Monitor de rendimiento de red](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="edit-monitoring-settings-for-subnets"></a>Edición de la configuración de supervisión de subredes
Se muestran todas las subredes donde se instaló al menos un agente en la pestaña **Subredes** de la página de configuración.

#### <a name="to-enable-or-disable-monitoring-for-particular-subnetworks"></a>Para habilitar o deshabilitar la supervisión de subredes determinadas
1. Active o desactive la casilla junto a la **Id. de subred** y, luego, asegúrese de que **Usar para la supervisión** esté activada o desactivada, según corresponda. Puede seleccionar o borrar varias subredes. Cuando esta casilla se desactiva, no se supervisan las subredes, ya que se actualizarán los agentes para que dejen de hacer ping a los demás agentes.
2. Elija los nodos que desee supervisar de esa subred concreta seleccionándola de la lista y moviendo los nodos pertinentes entre las listas que contienen los nodos supervisados y los que no se supervisan.
   Puede agregar una **descripción** personalizada a la subred, si así lo desea.
3. Para guardar la configuración, haga clic en **Guardar**.  
   ![Editar la subred](./media/log-analytics-network-performance-monitor/npm-edit-subnet.png)

### <a name="choose-nodes-to-monitor"></a>Elección de los nodos que desee supervisar
En la pestaña **Nodos** se muestran todos los nodos que tienen un agente instalado.

#### <a name="to-enable-or-disable-monitoring-for-nodes"></a>Para habilitar o deshabilitar la supervisión de nodos
1. Active o desactive los nodos que desee supervisar o dejar de supervisar.
2. Active la casilla **Usar para la supervisión** o desactívela, según corresponda.
3. Haga clic en **Guardar**.  
   ![Habilitar la supervisión de nodos](./media/log-analytics-network-performance-monitor/npm-enable-node-monitor.png)

### <a name="set-monitoring-rules"></a>Conjunto de reglas de supervisión
Monitor de rendimiento de red genera eventos de estado sobre la conectividad entre un par de nodos o vínculos de red o subred cuando se supera un umbral. El sistema puede aprender automáticamente estos umbrales, o bien puede configurarlos para usar reglas de alerta personalizadas.

El sistema crea la *regla predeterminada*, así como un evento de estado siempre que la pérdida o la latencia entre cualquier par de vínculos de red o subred superen el umbral aprendido por el sistema. Puede optar por deshabilitar la regla personalizada y crear reglas de supervisión personalizadas.

#### <a name="to-create-custom-monitoring-rules"></a>Para crear reglas de supervisión personalizadas
1. Haga clic en **Agregar regla** en la pestaña **Supervisión** y escriba el nombre y la descripción de la regla.
2. Seleccione en la lista el par de vínculos de red o subred que desee supervisar.
3. Primero, seleccione la red que contenga las subredes de interés en el menú desplegable de red y, después, elija estas últimas en el menú desplegable de subredes correspondiente.
   Seleccione **Todas las subredes** Si desea supervisar todas las subredes de un vínculo de red. De forma similar, seleccione las demás subredes que le interesen. Asimismo, puede hacer clic en **Agregar excepción** para excluir de la supervisión determinados vínculos de red de la selección que haya realizado.
4. Elija entre los protocolos ICMP y TCP para ejecutar transacciones sintéticas.
5. Si no desea crear eventos de estado para los elementos que ha seleccionado, desactive **Habilitar Seguimiento de estado en los vínculos que abarca esta regla**.
6. Elija las condiciones de supervisión.
   Puede establecer umbrales personalizados para la generación de eventos de estado escribiendo valores de umbral. Siempre que el valor de la condición supere el umbral seleccionado para el par de red/subred seleccionado, se generará un evento de estado.
7. Para guardar la configuración, haga clic en **Guardar**.  
   ![Crear una regla de supervisión personalizada](./media/log-analytics-network-performance-monitor/npm-monitor-rule.png)

Después de guardar una regla de supervisión, puede integrar esa regla con Administración de alertas haciendo clic en **Crear alerta**. Se crea automáticamente una regla de alerta con la consulta de búsqueda y se rellenan automáticamente otros parámetros necesarios. Mediante una regla de alerta, puede recibir alertas de correo electrónico, además de las alertas existentes dentro de NPM. Las alertas también pueden desencadenar acciones correctoras con runbooks o se pueden integrar con soluciones de administración de servicios existentes mediante webhooks. Puede hacer clic en **Administrar alerta** para editar la configuración de alertas.

### <a name="choose-the-right-protocol-icmp-or-tcp"></a>Selección del protocolo adecuado: ICMP o TCP

Monitor de rendimiento de red (NPM) utiliza transacciones sintéticas para calcular métricas de rendimiento de red, como la latencia de vínculo y la pérdida de paquetes. Para comprender esto mejor, piense en un agente de NPM conectado a un extremo de un vínculo de red. Este agente de NPM envía paquetes de sondeo a un segundo agente de NPM conectado a otro extremo de la red. El segundo agente responde con paquetes de respuesta. Este proceso se repite varias veces. Al medir el número de respuestas y el tiempo necesario para recibir cada respuesta, el primer agente de NPM evalúa la latencia del vínculo y la pérdida de paquetes.

El formato, el tamaño y la secuencia de estos paquetes se determinan mediante el protocolo que elige al crear reglas de supervisión. En función del protocolo de los paquetes, los dispositivos de red intermedios (enrutadores, conmutadores, etc.) pueden procesar estos paquetes de forma diferente. Por consiguiente, la elección del protocolo afecta a la precisión de los resultados. Asimismo, el protocolo que escoja también determinará si es necesario completar algunos pasos manuales más tras implementar la solución NPM.

NPM le permite elegir entre los protocolos ICMP y TCP para ejecutar transacciones sintéticas.
Si elige ICMP al crear una regla de transacciones sintéticas, los agentes de NPM utilizan mensajes de eco ICMP para calcular la latencia de red y la pérdida de paquetes. El eco ICMP usa el mismo mensaje que se envía mediante la utilidad de ping convencional. Cuando se usa TCP como protocolo, los agentes de NPM envían un paquete TCP SYN a través de la red. A continuación, se completa un protocolo de enlace TCP y, posteriormente, se elimina la conexión con paquetes RST.

#### <a name="points-to-consider-before-choosing-the-protocol"></a>Puntos que se deben tener en cuenta antes de seleccionar el protocolo
Antes de elegir un protocolo, tenga en cuenta la siguiente información:

##### <a name="discovering-multiple-network-routes"></a>Detección de varias rutas de red
TCP es más preciso cuando se detectan varias rutas y necesita menos agentes en cada subred. Por ejemplo, uno o dos agentes que utilicen TCP pueden detectar todas las rutas redundantes entre subredes. Sin embargo, se necesitarán varios agentes que utilicen ICMP para lograr resultados similares. Con ICMP, si tiene una cantidad *N* de rutas entre dos subredes, necesitará más de 5 veces *N* agentes en una subred de origen o de destino.

##### <a name="accuracy-of-results"></a>Precisión de los resultados
Los enrutadores y conmutadores tienden a asignar una prioridad menor a paquetes de eco ICMP en comparación con los paquetes TCP. En determinadas situaciones, cuando los dispositivos de red están muy cargados, los datos obtenidos mediante TCP reflejan más fielmente la pérdida y la latencia que experimentan las aplicaciones. Esto ocurre porque la mayoría del tráfico de las aplicaciones fluye a través de TCP. En tales casos, ICMP proporciona resultados menos precisos en comparación con TCP.

##### <a name="firewall-configuration"></a>Configuración del firewall
El protocolo TCP requiere que los paquetes TCP se envíen a un puerto de destino. El puerto predeterminado que usan los agentes de NPM es el 8084, si bien puede cambiarse al configurar los agentes. Por lo tanto, debe asegurarse de que los firewalls de red o las reglas de grupo de seguridad de red (en Azure) permitan el tráfico en el puerto. También debe asegurarse de que el firewall local en los equipos en los que están instalados los agentes esté configurado para permitir el tráfico en este puerto.

Puede utilizar scripts de PowerShell para configurar reglas de firewall en los equipos que ejecutan Windows, sin bien debe configurar manualmente el firewall de red.

En cambio, ICMP no utiliza ningún puerto. En la mayoría de los escenarios empresariales, se permite el tráfico ICMP a través de los firewalls para posibilitar el uso de herramientas de diagnóstico de red, como la utilidad de ping. Por lo tanto, si puede hacer ping a un equipo desde otro, podrá usar el protocolo ICMP sin necesidad de configurar manualmente los firewalls.

> [!NOTE]
> En caso de duda sobre qué protocolo utilizar, elija ICMP para empezar. Si no está satisfecho con los resultados, siempre podrá cambiar a TCP más adelante.


#### <a name="how-to-switch-the-protocol"></a>Cambio de protocolo

Si decide utilizar ICMP durante la implementación, puede cambiar a TCP en cualquier momento editando la regla de supervisión predeterminada.

##### <a name="to-edit-the-default-monitoring-rule"></a>Edición de la regla de supervisión predeterminada
1.  Vaya a **Network Performance** (Rendimiento de red)  > **Monitor** > **Configurar** > **Monitor** y, a continuación, haga clic en **Regla predeterminada**.
2.  Desplácese hasta la sección **Protocolo** y seleccione el protocolo que desee utilizar.
3.  Haga clic en **Guardar** para aplicar el cambio.

Aunque la regla predeterminada use un protocolo específico, puede crear nuevas reglas con un protocolo diferente. Incluso puede crear una combinación de reglas en la que algunas usen ICMP y otras utilicen TCP.




## <a name="data-collection-details"></a>Detalles de la recopilación de datos
El Monitor de rendimiento de red usa los paquetes de protocolo de enlace TCP SYN-SYNACK-ACK cuando se elige TCP, e ICMP ECHO ICMP ECHO REPLAY cuando se elige ICMP como protocolo para recopilar información de pérdida y latencia. Traceroute también se usa para obtener información sobre la topología.

En la siguiente tabla se muestran los métodos de recopilación de datos y otros detalles sobre cómo se reúnen los datos para Monitor de rendimiento de red.

| plataforma | Agente directo | Agente de SCOM | Azure Storage | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Protocolos de enlace TCP/mensajes ICMP ECHO cada 5 segundos, datos enviados cada 3 minutos |

La solución usa transacciones sintéticas para evaluar el estado de la red. Los agentes de OMS instalados en diversos puntos de la red intercambian entre sí paquetes TCP o ICMP Echo (según el protocolo seleccionado para la supervisión). En el proceso, los agentes conocen el tiempo de ida y vuelta y se enteran de la pérdida de paquetes, si la hay. Periódicamente, cada agente también realiza un seguimiento de la ruta hasta los demás agentes para descubrir las diversas rutas de la red que se deben comprobar. Con estos datos, los agentes pueden deducir la latencia de red y las cifras de pérdida de paquetes. Cada cinco segundos los agentes repiten las pruebas y agregan datos en un período de tres minutos antes de cargarlos en el servicio Log Analytics.

> [!NOTE]
> A pesar de que los agentes se comunican entre sí con frecuencia, no generan una gran cantidad de tráfico de red mientras llevan a cabo las pruebas. Los agentes utilizan exclusivamente los paquetes de protocolo de enlace SYNACK-TCP SYN-ACK para determinar la pérdida y la latencia; es decir, no se intercambia ningún paquete de datos. Durante este proceso, los agentes solo se comunican entre sí cuando resulta necesario, y la topología de comunicación de los agentes está optimizada para reducir el tráfico de red.
>
>

## <a name="using-the-solution"></a>Uso de la solución
En esta sección se explican todas las funciones del panel y cómo utilizarlas.

### <a name="solution-overview-tile"></a>Icono Información general del servicio
Una vez que haya habilitado la solución Monitor de rendimiento de red, el icono de la solución en la página Información general de OMS ofrecerá una visión general rápida del estado de la red. En él verá un gráfico de anillos que muestra el número de vínculos de subred correctos e incorrectos. Al hacer clic en el icono, se abrirá el panel de la solución.

![Icono Monitor de rendimiento de red](./media/log-analytics-network-performance-monitor/npm-tile.png)

### <a name="network-performance-monitor-solution-dashboard"></a>Panel de la solución Monitor de rendimiento de red
En la hoja **Resumen de red**, verá un resumen de las redes, junto con su tamaño relativo. Esto va seguido de iconos que muestran el número total de vínculos de red, vínculos de subred y rutas de acceso del sistema (una ruta de acceso se compone de las direcciones IP de dos hosts con agentes y todos los saltos entre ellos).

En la hoja **Eventos de estado de red principales** se proporciona una lista de los eventos de estado y las alertas más recientes del sistema, así como el tiempo que lleva el evento activo. Siempre que la pérdida de paquetes o la latencia de un vínculo de red o subred superan un umbral, se genera una alerta o un evento de estado.

En la hoja **Vínculos de red incorrectos principales** se muestra una lista de vínculos de red incorrectos. Estos son los vínculos de red que presentan uno o más eventos de estado negativos en ese momento.

En las hojas **Vínculos de subred con mayor pérdida** y **Vínculos de subred con mayor latencia** se muestran los principales vínculos de red por pérdida de paquetes y por latencia, respectivamente. Puede esperar una latencia alta o cierta cantidad de pérdida de paquetes en determinados vínculos de red. Estos vínculos aparecen en las listas de los diez principales, pero no se marcan como incorrectos.

La hoja **Consultas comunes** contiene un conjunto de consultas de búsqueda que recuperan directamente datos sin procesar de supervisión de red. Puede usar estas consultas como punto de partida a fin de crear las suyas propias para generar informes personalizados.

![Panel Monitor de rendimiento de red](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="drill-down-for-depth"></a>Exploración en profundidad
Puede hacer clic en los diversos vínculos del panel de la solución para profundizar más en cualquier área de interés. Por ejemplo, cuando vea aparecer en el panel una alerta o un vínculo de red incorrecto, puede hacer clic en ellos para investigar más. Se le dirigirá a una página que enumera todos los vínculos de subred del vínculo de red concreto. Podrá ver la pérdida, la latencia y el estado de cada vínculo de subred e identificar rápidamente aquellos que estén ocasionando el problema. Después, puede hacer clic en **Ver vínculos de nodo** para ver todos los vínculos de nodo del vínculo de subred incorrecto. Además, puede ver los vínculos individuales de nodo a nodo y encontrar los vínculos de nodo incorrectos.

Puede hacer clic en **Ver topología** para ver la topología de salto a salto de las rutas entre los nodos de origen y de destino. Las rutas o los saltos incorrectos se muestran en rojo para que pueda acotar rápidamente el problema a una parte concreta de la red.

![Datos en profundidad](./media/log-analytics-network-performance-monitor/npm-drill.png)

### <a name="network-state-recorder"></a>Grabadora de estado de la red

Cada vista muestra una instantánea del estado de su red en un momento determinado del tiempo. De forma predeterminada, se muestra el estado más reciente. La barra en la parte superior de la página muestra el punto en el tiempo para el que se muestra el estado. Puede elegir retroceder en el tiempo y ver la instantánea del estado de su red haciendo clic en la barra en **Acciones**. También puede elegir habilitar o deshabilitar la actualización automática de cualquier página mientras ve el estado más reciente.

![estado de la red](./media/log-analytics-network-performance-monitor/network-state.png)

#### <a name="trend-charts"></a>Gráficos de tendencias
En cada nivel que desglose, podrá ver la tendencia de pérdida y latencia de un vínculo de red. También tiene a su disposición gráficos de tendencias para vínculos de subred y nodo. Puede cambiar el intervalo de tiempo empleado en el gráfico mediante la opción de control del tiempo situada en la parte superior del gráfico.

Los gráficos de tendencias muestran una perspectiva histórica del rendimiento de un vínculo de red. Algunos problemas de red son transitorios por naturaleza y, por lo tanto, resultarían difíciles de encontrar examinando únicamente el estado actual de la red. Esto se debe a que los problemas pueden manifestarse rápidamente y desaparecer antes de que nadie se percate para volver a surgir más adelante. Dichos problemas transitorios también pueden resultar difíciles para los administradores de aplicaciones, ya que tales problemas suelen surgir como incrementos inexplicables en el tiempo de respuesta de la aplicación, incluso cuando todos los componentes de ella parecen ejecutarse a la perfección.

Puede encontrar con facilidad estos tipos de problemas examinando un gráfico de tendencias, donde el problema aparecerá como un pico repentino de latencia o pérdida de paquetes en la red.

![Gráfico de tendencias](./media/log-analytics-network-performance-monitor/npm-trend.png)

#### <a name="hop-by-hop-topology-map"></a>Mapa de topología de salto a salto
Monitor de rendimiento de red le muestra la topología de salto a salto de las rutas comprendidas entre dos nodos en un mapa de topología interactivo. Puede ver el mapa de topología, seleccione un vínculo del nodo y, después, haga clic en **Ver topología**. Asimismo, puede ver el mapa de topología haciendo clic en el icono **Rutas de acceso** del panel. Al hacer clic en **Rutas de acceso** en el panel, tendrá que seleccionar los nodos de origen y destino en el panel izquierdo y, después, hacer clic en **Trazar** para trazar las rutas comprendidas entre ambos nodos.

En el mapa de topología se muestra el número de rutas que existen entre los dos nodos y las rutas de acceso que recorren los paquetes de datos. Los cuellos de botella de rendimiento de red se marcan en rojo en el mapa de topología. Puede encontrar una conexión o dispositivo de red defectuosos examinando los elementos de color rojo presentes en el mapa de topología.

Cuando haga clic en un nodo o pase el mouse por encima de él encima del mapa de topología, verá las propiedades del nodo, como el FQDN o la dirección IP. Haga clic en un salto para ver su dirección IP. Puede elegir filtrar rutas determinadas mediante los filtros que aparecen en el panel de acciones contraíble. Y también puede simplificar las topologías de red ocultando los saltos intermedios con el control deslizante del panel de acciones. Además, puede acercar o alejar el mapa de topología con la rueda del mouse.

Observe que la topología mostrada en el mapa corresponde al nivel 3 y no contiene ningún dispositivo ni conexión de nivel 2.

![Mapa de topología de salto a salto](./media/log-analytics-network-performance-monitor/npm-topology.png)

#### <a name="fault-localization"></a>Localización de defectos
Monitor de rendimiento de red puede hallar los cuellos de botella de la red sin conectarse a los dispositivos de red. A partir de los datos que reúna de la red y mediante la aplicación de algoritmos avanzados en el gráfico de red, Monitor de rendimiento de red efectúa una estimación probabilística de las partes de la red con más probabilidades de ser la causa del problema.

Este enfoque resulta de utilidad para determinar los cuellos de botella de red cuando no es posible acceder a los saltos, ya que, de este modo, no se precisa reunir datos de los dispositivos de red como enrutadores o conmutadores. Esto también es útil cuando los saltos entre dos nodos no se encuentran bajo su control administrativo. Por ejemplo, los saltos pueden ser enrutadores del ISP.

### <a name="log-analytics-search"></a>Búsqueda de Log Analytics
Todos los datos expuestos mediante gráficos en las páginas de exploración en profundidad y en el panel de Monitor de rendimiento de red también se encuentran disponibles de forma nativa en la búsqueda de Log Analytics. Puede consultar los datos mediante el lenguaje de consulta de búsqueda y crear informes personalizados exportando la información a Excel o Power BI. La hoja **Consultas comunes** del panel incorpora una serie de consultas útiles que puede usar como punto de partida para crear sus propios informes y consultas.

![Consultas de búsqueda](./media/log-analytics-network-performance-monitor/npm-queries.png)

## <a name="investigate-the-root-cause-of-a-health-alert"></a>Investigación de la causa principal de una alerta de estado
Ahora que se ha familiarizado con Monitor de rendimiento de red, veamos una investigación sencilla de la causa principal de un evento de estado.

1. En la página Información general, podrá obtener una instantánea rápida del estado de la red con solo observar el icono **Monitor de rendimiento de red**. Observe que, de los 6 vínculos de subred que se están supervisando, 2 son incorrectos. Esta situación se debe investigar. Haga clic en el icono para ver el panel de la solución.  
   ![Icono Monitor de rendimiento de red](./media/log-analytics-network-performance-monitor/npm-investigation01.png)
2. En la imagen de ejemplo siguiente, se puede observar que hay un evento de estado en un vínculo de red con un estado incorrecto. Supongamos que decide investigar el problema y hace clic en el vínculo de red **DMZ2-DMZ1** para descubrir su causa.  
   ![Ejemplo de vínculo de red incorrecto](./media/log-analytics-network-performance-monitor/npm-investigation02.png)
3. En la página de exploración en profundidad se muestran todos los vínculos de subred del vínculo de red **DMZ2-DMZ1**. Observará que, en el caso de ambos vínculos de subred, la latencia ha superado el umbral, lo que ha hecho que el vínculo de red esté incorrecto. También puede ver las tendencias de latencia de ambos vínculos de subred. Puede utilizar la opción para seleccionar el tiempo del gráfico a fin de centrarse en el intervalo de tiempo pertinente. Puede ver la hora del día en la que la latencia ha alcanzado su punto máximo. Después, puede buscar en los registros este periodo para investigar el problema. Haga clic en **Ver vínculos de nodo** para profundizar aún más.  
   ![Ejemplo de vínculos de subred incorrectos](./media/log-analytics-network-performance-monitor/npm-investigation03.png)
4. Como sucedía en la página anterior, en la página de exploración en profundidad del vínculo de subred concreto se enumeran los vínculos de nodo que lo componen. Aquí puede efectuar acciones similares a las realizadas en el paso anterior. Haga clic en **Ver topología** para ver la topología entre los dos nodos.  
   ![Ejemplo de vínculos de nodo incorrectos](./media/log-analytics-network-performance-monitor/npm-investigation04.png)
5. Todas las rutas de acceso entre los dos nodos seleccionados estarán trazadas en el mapa de topología. Puede visualizar la topología de salto a salto de las rutas entre dos nodos en el mapa de topología. Ofrece una perspectiva clara de cuántas rutas existen entre los dos nodos y qué rutas de acceso emplean los paquetes de datos. Los cuellos de botella de rendimiento de red se marcan en rojo. Puede encontrar una conexión o dispositivo de red defectuosos examinando los elementos de color rojo presentes en el mapa de topología.  
   ![Ejemplo de la vista de topología incorrecta](./media/log-analytics-network-performance-monitor/npm-investigation05.png)
6. Es posible consultar la pérdida, la latencia y el número de saltos de cada ruta de acceso en el panel **Acción**. Utilice la barra de desplazamiento para ver los detalles de esas rutas de acceso incorrectas.  Use los filtros para seleccionar las rutas de acceso con el salto incorrecto de modo que se pueda trazar la topología únicamente de las rutas de acceso seleccionadas. Puede utilizar la rueda del mouse para acercar o alejar el mapa de topología.

   En la siguiente imagen puede ver con claridad la causa principal de las áreas problemáticas de la sección específica de la red con solo observar las rutas de acceso y los saltos marcados en color rojo. Si hace clic en un nodo en el mapa de topología, se mostrarán las propiedades del nodo, incluido el FQDN y la dirección IP. Si se hace clic en un salto, se mostrará su dirección IP.  
   ![Ejemplo de topología incorrecta con detalles sobre las rutas de acceso](./media/log-analytics-network-performance-monitor/npm-investigation06.png)

## <a name="provide-feedback"></a>Envío de comentarios

- **UserVoice**: puede publicar sus ideas sobre las características de Network Performance Monitor (monitor de rendimiento de red) en las que le gustaría que trabajásemos. Visite nuestra [página UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring).
- **Únase a nuestra cohortes**: siempre estamos interesados en que se unan nuevos clientes a nuestra cohorte. Como parte de todo ello, podrá obtener un acceso anticipado a las nuevas características y ayudarnos a mejorar Network Performance Monitor. Si está interesado en unirse, rellene esta [encuesta rápida](https://aka.ms/npmcohort).

## <a name="next-steps"></a>Pasos siguientes
* [Buscar registros](log-analytics-log-searches.md) para ver datos detallados de rendimiento de red.
