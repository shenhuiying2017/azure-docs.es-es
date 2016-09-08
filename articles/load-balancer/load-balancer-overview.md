<properties
   pageTitle="Información general sobre el Equilibrador de carga de Azure | Microsoft Azure"
   description="Información general sobre las características, la arquitectura y la implementación del Equilibrador de carga de Azure Obtenga información acerca de cómo funciona el equilibrador de carga y aproveche sus ventajas en la nube."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/19/2016"
   ms.author="sewhee" />


# Información general sobre el Equilibrador de carga de Azure

Azure Load Balancer proporciona una alta disponibilidad y un elevado rendimiento de red para sus aplicaciones. Se trata de un equilibrador de carga de nivel 4 (TCP y UDP) que distribuye el tráfico entrante entre las instancias de servicio correctas de los servicios en la nube o las máquinas virtuales que se definen en un conjunto de carga equilibrada.

Se puede configurar para:

- Equilibrar la carga del tráfico entrante de Internet entre las máquinas virtuales. Se trata de un proceso conocido como [equilibrio de carga accesible desde Internet](load-balancer-internet-overview.md).
- Equilibrar la carga del tráfico entre máquinas virtuales de una red virtual, entre máquinas virtuales de servicios en la nube o entre equipos locales y máquinas virtuales de una red virtual entre entornos locales. Se trata de un proceso conocido como [equilibrio de carga interna](load-balancer-internal-overview.md).
- Reenviar el tráfico externo a una máquina virtual determinada.

Todos los recursos en la nube necesitan una dirección IP pública para ser accesibles desde Internet. La infraestructura de nube de Microsoft Azure usa direcciones IP no enrutables para sus recursos. Usa traducción de direcciones de red (NAT) con direcciones IP públicas para comunicarse con Internet.

Es importante comprender las diferencias entre los [modelos de implementación](../resource-manager-deployment-model.md) clásico de Azure y de Resource Manager, porque el equilibrador de carga se configura de forma diferente en cada uno de ellos.

### modelo de implementación clásica de Azure

En este modelo, una dirección IP pública y un FQDN se asignan a un servicio en la nube. Las máquinas virtuales que se implementan dentro de los límites de un servicio en la nube pueden agruparse para usar un equilibrador de carga. El equilibrador de carga se ocupará de la traducción de puertos y de equilibrar la carga del tráfico de red aprovechando la dirección IP pública para el servicio en la nube.

Dicha traducción se realiza mediante puntos de conexión que establecen una relación uno a uno entre el puerto público asignado de la dirección IP pública y el puerto local asignado para enviar tráfico a una máquina virtual específica. El equilibrio de carga se realiza mediante puntos de conexión del equilibrador de carga. Dichos puntos de conexión establecen una relación uno a varios entre la dirección IP pública y los puertos locales asignados a todas las máquinas virtuales del servicio en la nube que responden al tráfico de red de carga equilibrada.

La etiqueta de dominio de la dirección IP pública que usa el equilibrador de carga en este modelo de implementación es <nombre de servicio en la nube>.cloudapp.net. El siguiente gráfico muestra Azure Load Balancer en este modelo.

![Azure Load Balancer en el modelo de implementación clásica](./media/load-balancer-overview/asm-lb.png)

### modelo de implementación de Azure Resource Manager

En este modelo, no hay necesidad alguna de crear un servicio en la nube para equilibrar la carga del tráfico dirigido a las máquinas virtuales, pero el equilibrador de carga se debe crear de forma explícita.

Una dirección IP pública es su propio recurso y se puede asociar a una etiqueta de dominio o nombre DNS. En este caso, la dirección IP pública está asociada al recurso de equilibrador de carga. Así, las reglas de este último y las de NAT de entrada usarán la dirección IP pública como el punto de conexión de Internet para los recursos que reciban tráfico de red de carga equilibrada.

Se asigna una dirección IP privada o pública al recurso de interfaz de red asociado a una máquina virtual. Una vez agregada una interfaz de red a un grupo de direcciones IP de back-end del equilibrador de carga, este empezará a enviar tráfico de red de carga equilibrada en función de las reglas de carga equilibrada creadas.

El siguiente gráfico muestra Azure Load Balancer en este modelo:

![Azure Load Balancer en Resource Manager](./media/load-balancer-overview/arm-lb.png)

## Características del equilibrador de carga

### Distribución basada en hash

El equilibrador de carga usa un algoritmo de distribución basado en hash. De forma predeterminada, usa un hash de 5-tupla (IP de origen, puerto de origen, IP de destino, puerto de destino y tipo de protocolo) para asignar el tráfico a los servidores disponibles. Dicho algoritmo solo proporciona adherencia *dentro* de una sesión de transporte. Los paquetes de la misma sesión TCP o UDP se dirigirán a la misma instancia de IP del centro de datos tras el punto de conexión de carga equilibrada. Cuando el cliente cierra y vuelve a abrir la conexión o inicia una nueva sesión desde la misma IP de origen, el puerto de origen cambia. Esto puede provocar que el tráfico vaya a un punto de conexión IP del centro de datos diferente.

Para obtener más detalles, consulte [Modo de distribución del equilibrador de carga (afinidad de IP de origen)](load-balancer-distribution-mode.md). El siguiente gráfico muestra la distribución basada en hash:

![Distribución basada en hash](./media/load-balancer-overview/load-balancer-distribution.png)

### Enrutamiento de puerto

El equilibrador de carga le permite controlar cómo se administra la comunicación entrante. Estas comunicación puede incluir tráfico iniciado desde hosts de Internet o máquinas virtuales de otros servicios en la nube o redes virtuales. Este control se representa mediante un punto de conexión (también conocido como punto de conexión de entrada).

Un extremo escucha en un puerto público y enruta el tráfico a un puerto interno. Puede asignar los mismos puertos para un extremo interno o externo o usar un puerto diferente para ellos. Por ejemplo, puede tener un servidor web configurado para escuchar el puerto 81, mientras que la asignación del punto de conexión público corresponda al puerto 80. La creación de un punto de conexión público desencadena la generación de una instancia del equilibrador de carga.

El uso y la configuración predeterminados de puntos de conexión en una máquina virtual que cree con el Portal de Azure van al Protocolo de Escritorio remoto y el tráfico de sesión remota de Windows PowerShell. Estos puntos de conexión le permiten administrar la máquina virtual de manera remota a través de Internet.


### Reconfiguración automática

El equilibrador de carga se reconfigura inmediatamente al escalar o reducir instancias verticalmente. Por ejemplo, esta reconfiguración puede ocurrir cuando se aumenta el recuento de instancias para los roles web o de trabajador en un servicio en la nube, o al agregar máquinas virtuales adicionales al mismo conjunto de carga equilibrada.


### Supervisión de servicios

El equilibrador de carga puede sondear el estado de las distintas instancias de servidor. Cuando un sondeo no responde, el equilibrador de carga deja de enviar nuevas conexiones a las instancias incorrectas. Las conexiones existentes no se ven afectadas.

Se admiten tres tipos de sondeos:

- **Sonda de agente invitado (solo en máquinas virtuales de PaaS)****:** el equilibrador de carga utiliza el agente invitado en la máquina virtual. Solo escucha y contesta con una respuesta HTTP 200 OK cuando la instancia está lista (es decir, cuando su estado no es ocupada, en reciclaje o en detención). Si el agente invitado no responde con HTTP 200 OK, el equilibrador de carga marca la instancia como sin respuesta y deja de enviarle tráfico. El equilibrador de carga continuará haciendo ping a la instancia. Si el agente invitado responde con HTTP 200, el equilibrador de carga le enviará tráfico nuevamente. Cuando se usa un rol web, el código de sitio web normalmente se ejecuta en w3wp.exe, que no está supervisado por el agente invitado o el tejido de Azure. Esto significa que los errores de w3wp.exe (por ejemplo, las respuestas HTTP 500) no se notificarán al agente invitado, y el equilibrador de carga no sabrá sacar esa instancia de la rotación.
- **Sonda personalizada de HTTP:** esta invalida la sonda predeterminado (del agente invitado) predeterminada. Puede usarlo para crear su propia lógica personalizada para determinar el estado de la instancia de rol. El equilibrador de carga sondeará periódicamente el punto de conexión (de forma predeterminada, cada 15 segundos). La instancia se considerará en rotación si responde con un TCP ACK o HTTP 200 dentro del período de tiempo de espera (valor predeterminado, 31 segundos). Esto puede resultar útil para implementar su propia lógica con el fin de quitar instancias de la rotación del equilibrador de carga. Por ejemplo, puede configurar la instancia para devolver un estado no 200 si la instancia está por encima del 90% de la CPU. En el caso de los roles web que usan w3wp.exe, también dispondrá de supervisión automática de su sitio web, puesto que los errores del código de este devolverán a la sonda un estado distinto a 200.
- **Sonda personalizada de TCP:** esta se basa en el establecimiento correcto de sesiones TCP para un puerto de sondeo definido.

Para obtener más información, consulte [LoadBalancerProbe schema](https://msdn.microsoft.com/library/azure/jj151530.aspx) (Esquema LoadBalancerProbe).

### NAT de origen


Todo el tráfico saliente a Internet procedente de su servicio pasa por un proceso de NAT de origen (SNAT) mediante el uso de la misma dirección VIP que para el tráfico entrante. SNAT proporciona ventajas importantes:

- Permite la actualización y la recuperación ante desastres de servicios de forma fácil, puesto que la dirección VIP puede asignarse dinámicamente a otra instancia del servicio.
- Facilita la administración de listas de control de acceso (ACL), puesto que estas últimas se pueden expresar en términos de direcciones VIP y, por lo tanto, no cambian a medida que los servicios se escalan o reducen verticalmente o se implementan de nuevo.

La configuración del equilibrador de carga admite NAT de cono completo para UDP. NAT de cono completo es un tipo de NAT en el que el puerto permite conexiones entrantes desde cualquier host externo (en respuesta a una solicitud saliente).


>[AZURE.NOTE] Tenga en cuenta que, para cada nueva conexión saliente que inicie una máquina virtual, el equilibrador de carga también asigna un puerto saliente. El host externo verá el tráfico como un puerto IP virtual (VIP) asignado. Si los escenarios requieren un gran número de conexiones salientes, se recomienda que las máquinas virtuales usen [direcciones IP públicas a nivel de instancia](../virtual-network/virtual-networks-instance-level-public-ip.md) para que tengan una dirección IP saliente dedicada para SNAT. Esto reducirá el riesgo de agotamiento de puertos.
>
>El número máximo de puertos que pueden usar las direcciones VIP o PIP (direcciones IP públicas) a nivel de instancia es 64 000. Se trata de una limitación estándar de TCP.


####Compatibilidad con varias direcciones IP con equilibrio de carga para máquinas virtuales

Puede tener más de una dirección IP pública con equilibrio de carga asignada a un conjunto de máquinas virtuales. Gracias a esta posibilidad, puede hospedar varios sitios web SSL o varios agentes de escucha de Grupo de disponibilidad AlwaysOn de SQL Server en el mismo conjunto de máquinas virtuales. Encontrará más información en [Varias direcciones VIP por servicio en la nube](load-balancer-multivip.md).

####Implementaciones basadas en plantilla a través del Administrador de recursos de Azure####

El equilibrador de carga puede administrarse mediante las herramientas y las API basadas en Resource Manager. Para obtener más información sobre Resource Manager, consulte [Información general de Azure Resource Manager](../resource-group-overview.md).

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## Pasos siguientes

[Información general sobre el equilibrador de carga accesible desde Internet](load-balancer-internet-overview.md)

[Información general sobre el equilibrador de carga interno](load-balancer-internal-overview.md)

[Introducción a la creación de un equilibrador de carga accesible desde Internet](load-balancer-get-started-internet-arm-ps.md)

<!---HONumber=AcomDC_0824_2016-->