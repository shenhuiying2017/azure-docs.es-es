---
title: "Característica Monitor de rendimiento en la solución Network Performance Monitor en Azure Log Analytics | Microsoft Docs"
description: "La función Monitor de rendimiento en Network Performance Monitor le ayuda a supervisar la conectividad de la red en varios puntos de su red, como implementaciones en la nube y ubicaciones locales, varios centros de datos y sucursales y aplicaciones o microservicios multiinquilino críticos."
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
ms.openlocfilehash: a90ab3bc857b704d9d94daf96d17611844abb1a6
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/23/2018
---
# <a name="network-performance-monitor-solution---performance-monitoring"></a>Solución Network Performance Monitor: supervisión del rendimiento

La función Monitor de rendimiento en [Network Performance Monitor](log-analytics-network-performance-monitor.md) le ayuda a supervisar la conectividad de la red en varios puntos de su red, como implementaciones en la nube y ubicaciones locales, varios centros de datos y sucursales y aplicaciones o microservicios multiinquilino críticos. Con el Monitor de rendimiento, puede detectar problemas de red antes de recibir quejas de los usuarios. Las principales ventajas son: 

- Supervisar la latencia y la pérdida a través de varias redes virtuales y establecer alertas 
- Supervisar todas las rutas de acceso (incluidas las rutas de acceso redundantes) en la red 
- Solucionar problemas de red transitorios y a un momento dado que son difíciles de replicar 
- Determinar un segmento específico de la red que es responsable de la degradación del rendimiento 
- Supervisar el estado de la red, sin necesidad de SNMP 


![Monitor de rendimiento de red](media/log-analytics-network-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Configuración
Para abrir la configuración de Network Performance Monitor, abra la [solución de Network Performance Monitor](log-analytics-network-performance-monitor.md) y haga clic en el botón **Configurar**.

![Configuración de Network Performance Monitor](media/log-analytics-network-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Creación de nuevas redes

Una red de NPM es un contenedor lógico para las subredes. Le ayuda a organizar la supervisión de la infraestructura de red según sus necesidades de supervisión. Puede crear una red con un nombre descriptivo y agregar subredes según la lógica de negocios. Por ejemplo, puede crear una red denominada Londres y agregar a ella todas las subredes del centro de datos de Londres, o una red denominada *ContosoFrontEnd* y agregar a ella todas las subredes que atienden al front-end de la aplicación denominada Contoso. La solución crea automáticamente una red predeterminada que contiene todas las subredes detectadas en su entorno. Siempre que cree una red, se agrega una subred en ella y se quita dicha subred de la red Predeterminada. Si elimina una red, se devuelven automáticamente todas sus subredes a la red Predeterminada. Así pues, la red Predeterminada actúa como contenedor de todas las subredes que no se incluyan en alguna red definida por el usuario. No puede editar ni eliminar la red Predeterminada. Siempre permanece en el sistema. Sin embargo, puede crear todas las redes personalizadas que necesite. En la mayoría de los casos, las subredes de la organización están organizadas en más de una red y, por tanto, debe crear una o varias redes para agrupar las subredes de acuerdo con la lógica de negocios.

Para crear una nueva red:


1. Haga clic en la **pestaña Redes**.
1. Haga clic en  **Agregar red** y, después, escriba el nombre de red y la descripción. 
2. Seleccione una o varias subredes y, luego, haga clic en **Agregar**. 
3. Para guardar la configuración, haga clic en **Guardar**. 


### <a name="create-monitoring-rules"></a>Creación de reglas de supervisión 

El Monitor de rendimiento genera eventos de estado cuando se supera el umbral del rendimiento de las conexiones de red entre dos subredes o entre dos redes. El sistema puede aprender automáticamente estos umbrales, o bien puede especificar umbrales personalizados. El sistema crea también automáticamente la regla predeterminada, que genera un evento de estado siempre que la pérdida o la latencia entre cualquier par de vínculos de red o subred supere el umbral aprendido por el sistema. Esto ayuda a la solución a supervisar la infraestructura de red hasta que no haya creado ninguna regla de supervisión explícitamente. Si está habilitada la **regla predeterminada**, todos los nodos envían transacciones sintéticas al resto de nodos que se han habilitado para la supervisión. La regla predeterminada es útil en el caso de redes pequeñas, por ejemplo, en un escenario donde tiene un número pequeño de servidores que ejecutan un microservicio y desea asegurarse de que todos los servidores tengan conectividad entre sí. 

>[!NOTE]
> Es muy recomendable que deshabilite la **regla predeterminada** y cree reglas de supervisión personalizadas, especialmente en el caso de las redes de gran tamaño en las que usa un número grande de nodos para la supervisión. Esto reduce el tráfico generado por la solución y ayuda a organizar la supervisión de la red. 

Cree reglas de supervisión de acuerdo con la lógica de negocios. Por ejemplo, si desea supervisar el rendimiento de la conectividad de red de dos oficinas con la sede, agrupe todas las subredes de la oficina 1 en la red O1, todas las subredes de la oficina 2 en la red O2 y todas las subredes de la sede en la red S. Cree dos reglas de supervisión: una entre O1 y S y otra entre O2 y S. 

Para crear reglas de supervisión personalizadas:

1. Haga clic en **Agregar regla** en la pestaña **Supervisión** y escriba el nombre y la descripción de la regla. 
2. Seleccione en la lista el par de vínculos de red o subred que desee supervisar. 
3. Primero, seleccione la red que contenga las subredes de interés en el menú desplegable de red y, después, elija estas últimas en el menú desplegable de subredes correspondiente. Seleccione **Todas las subredes** Si desea supervisar todas las subredes de un vínculo de red. De forma similar, seleccione las demás subredes que le interesen. Asimismo, puede hacer clic en **Agregar excepción** para excluir de la supervisión determinados vínculos de red de la selección que haya realizado. 
4. Elija entre los protocolos ICMP y TCP para ejecutar transacciones sintéticas. 
5. Si no desea crear eventos de estado para los elementos que ha seleccionado, desactive **Habilitar Seguimiento de estado en los vínculos que abarca esta regla**. 
6. Elija las condiciones de supervisión. Puede establecer umbrales personalizados para la generación de eventos de estado escribiendo valores de umbral. Siempre que el valor de la condición supere el umbral seleccionado para el par de red/subred seleccionado, se generará un evento de estado. 
7. Para guardar la configuración, haga clic en **Guardar**. 

Después de guardar una regla de supervisión, puede integrar esa regla con Administración de alertas haciendo clic en **Crear alerta**. Se crea automáticamente una regla de alerta con la consulta de búsqueda y se rellenan automáticamente otros parámetros necesarios. Mediante una regla de alerta, puede recibir alertas de correo electrónico, además de las alertas existentes dentro de NPM. Las alertas también pueden desencadenar acciones correctoras con runbooks o se pueden integrar con soluciones de administración de servicios existentes mediante webhooks. Puede hacer clic en **Administrar alerta** para editar la configuración de alertas. 

Ahora puede crear más reglas del Monitor de rendimiento o trasladarse al panel de la solución para comenzar a usar esta función. 

### <a name="choose-the-protocol"></a>Selección del protocolo

Monitor de rendimiento de red (NPM) utiliza transacciones sintéticas para calcular métricas de rendimiento de red, como la latencia de vínculo y la pérdida de paquetes. Para comprender esto mejor, piense en un agente de NPM conectado a un extremo de un vínculo de red. Este agente de NPM envía paquetes de sondeo a un segundo agente de NPM conectado a otro extremo de la red. El segundo agente responde con paquetes de respuesta. Este proceso se repite varias veces. Al medir el número de respuestas y el tiempo necesario para recibir cada respuesta, el primer agente de NPM evalúa la latencia del vínculo y la pérdida de paquetes. 

El formato, el tamaño y la secuencia de estos paquetes se determinan mediante el protocolo que elige al crear reglas de supervisión. En función del protocolo de los paquetes, los dispositivos de red intermedios (enrutadores, conmutadores, etc.) pueden procesar estos paquetes de forma diferente. Por consiguiente, la elección del protocolo afecta a la precisión de los resultados. Asimismo, el protocolo que escoja también determinará si es necesario completar algunos pasos manuales más tras implementar la solución NPM. 

NPM le permite elegir entre los protocolos ICMP y TCP para ejecutar transacciones sintéticas. Si elige ICMP al crear una regla de transacciones sintéticas, los agentes de NPM utilizan mensajes de eco ICMP para calcular la latencia de red y la pérdida de paquetes. El eco ICMP usa el mismo mensaje que se envía mediante la utilidad de ping convencional. Cuando se usa TCP como protocolo, los agentes de NPM envían un paquete TCP SYN a través de la red. A continuación, se completa un protocolo de enlace TCP y, posteriormente, se elimina la conexión con paquetes RST. 

Antes de elegir un protocolo, tenga en cuenta la siguiente información: 

**Detección de varias rutas de red.**  TCP es más preciso cuando se detectan varias rutas y necesita menos agentes en cada subred. Por ejemplo, uno o dos agentes que utilicen TCP pueden detectar todas las rutas redundantes entre subredes. Sin embargo, se necesitarán varios agentes que utilicen ICMP para lograr resultados similares. Con ICMP, si tiene una cantidad de rutas entre dos subredes, necesitará una cantidad 5 veces superior de agentes en una subred de origen o de destino. 

**Precisión de los resultados.** Los enrutadores y conmutadores tienden a asignar una prioridad menor a paquetes de eco ICMP en comparación con los paquetes TCP. En determinadas situaciones, cuando los dispositivos de red están muy cargados, los datos obtenidos mediante TCP reflejan más fielmente la pérdida y la latencia que experimentan las aplicaciones. Esto ocurre porque la mayoría del tráfico de las aplicaciones fluye a través de TCP. En tales casos, ICMP proporciona resultados menos precisos en comparación con TCP. 

**Configuración del firewall.** El protocolo TCP requiere que los paquetes TCP se envíen a un puerto de destino. El puerto predeterminado que usan los agentes de NPM es el 8084, si bien puede cambiarse al configurar los agentes. Por lo tanto, debe asegurarse de que los firewalls de red o las reglas de grupo de seguridad de red (en Azure) permitan el tráfico en el puerto. También debe asegurarse de que el firewall local en los equipos en los que están instalados los agentes esté configurado para permitir el tráfico en este puerto. Puede utilizar scripts de PowerShell para configurar reglas de firewall en los equipos que ejecutan Windows, sin bien debe configurar manualmente el firewall de red. En cambio, ICMP no utiliza ningún puerto. En la mayoría de los escenarios empresariales, se permite el tráfico ICMP a través de los firewalls para posibilitar el uso de herramientas de diagnóstico de red, como la utilidad de ping. Por lo tanto, si puede hacer ping a un equipo desde otro, podrá usar el protocolo ICMP sin necesidad de configurar manualmente los firewalls. 

>[!NOTE] 
> Algunos firewall pueden bloquear ICMP, lo cual podría provocar una retransmisión que produciría un gran número de eventos en el sistema de administración de eventos e información de seguridad. Asegúrese de que el protocolo que elija no esté bloqueado por un firewall de red o un grupo de seguridad de red; en caso contrario, NPM no puede supervisar el segmento de red. Por este motivo, se recomienda usar TCP para la supervisión. Debe usar ICMP en escenarios donde no sea posible utilizar TCP, como los siguientes: 
>
> - Utiliza nodos basados en cliente de Windows, dado que los sockets TCP sin formato no se permiten en el cliente de Windows.
> - El firewall de red o el grupo de seguridad de red bloquea TCP.
> - Cambio de protocolo 

Si decide utilizar ICMP durante la implementación, puede cambiar a TCP en cualquier momento editando la regla de supervisión predeterminada:

1. Vaya a **Rendimiento de la red** > **Monitor** > **Configurar** > **Monitor** y, a continuación, haga clic en  **Regla predeterminada**. 
2. Desplácese hasta la sección **Protocolo** y seleccione el protocolo que desee utilizar. 
3. Haga clic en **Guardar** para aplicar el cambio. 

Aunque la regla predeterminada use un protocolo específico, puede crear nuevas reglas con un protocolo diferente. Incluso puede crear una combinación de reglas en la que algunas usen ICMP y otras utilicen TCP. 

## <a name="walkthrough"></a>Tutorial 

Ahora que se ha familiarizado con el Monitor de rendimiento, veamos una investigación sencilla de la causa principal de un evento de estado.  

En el panel de la solución, se puede observar que hay un evento de estado: un vínculo de red con un estado incorrecto. Decide investigar el problema y hace clic en el icono **Vínculos de red que se están supervisando**.

En la página de exploración en profundidad, observa que el vínculo de red **DMZ2 DMZ1** está en mal estado. Hace clic en el vínculo **View subnet links**** (Ver los vínculos de subred) para este vínculo de red. 


En la página de exploración en profundidad se muestran todos los vínculos de subred del vínculo de red **DMZ2-DMZ1**. Observará que, en el caso de ambos vínculos de subred, la latencia ha superado el umbral, lo que ha hecho que el vínculo de red esté incorrecto. También puede ver las tendencias de latencia de ambos vínculos de subred. Puede utilizar la opción para seleccionar el tiempo del gráfico a fin de centrarse en el intervalo de tiempo pertinente. Puede ver la hora del día en la que la latencia ha alcanzado su punto máximo. Después, puede buscar en los registros este periodo para investigar el problema. Haga clic en **Ver vínculos de nodo** para profundizar aún más. 
 
 ![Vínculos de subred](media/log-analytics-network-performance-monitor/subnetwork-links.png) 

Como sucedía en la página anterior, en la página de exploración en profundidad del vínculo de subred concreto se enumeran los vínculos de nodo que lo componen. Aquí puede efectuar acciones similares a las realizadas en el paso anterior. Haga clic en **Ver topología** para ver la topología entre los dos nodos. 
 
 ![Vínculos de nodos](media/log-analytics-network-performance-monitor/node-links.png) 

Todas las rutas de acceso entre los dos nodos seleccionados estarán trazadas en el mapa de topología. Puede visualizar la topología de salto a salto de las rutas entre dos nodos en el mapa de topología. Ofrece una perspectiva clara de cuántas rutas existen entre los dos nodos y qué rutas de acceso emplean los paquetes de datos. Los cuellos de botella de rendimiento de red se marcan en rojo. Puede encontrar una conexión o dispositivo de red defectuosos examinando los elementos de color rojo presentes en el mapa de topología. 

 ![Panel de topología](media/log-analytics-network-performance-monitor/topology-dashboard.png) 

Es posible consultar la pérdida, la latencia y el número de saltos de cada ruta de acceso en el panel **Acción**. Utilice la barra de desplazamiento para ver los detalles de esas rutas de acceso incorrectas. Use los filtros para seleccionar las rutas de acceso con el salto incorrecto de modo que se pueda trazar la topología únicamente de las rutas de acceso seleccionadas. Puede utilizar la rueda del mouse para acercar o alejar el mapa de topología. 

En la siguiente imagen puede ver con claridad la causa principal de las áreas problemáticas de la sección específica de la red con solo observar las rutas de acceso y los saltos marcados en color rojo. Si hace clic en un nodo en el mapa de topología, se mostrarán las propiedades del nodo, incluido el FQDN y la dirección IP. Si se hace clic en un salto, se mostrará su dirección IP. 
 
![Panel de topología](media/log-analytics-network-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>pasos siguientes
* [Buscar registros](log-analytics-log-searches.md) para ver datos detallados de rendimiento de red.
