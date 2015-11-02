<properties 
   pageTitle="Información general sobre el Equilibrador de carga de Azure | Microsoft Azure"
   description="Información general sobre las características, la arquitectura y la implementación del Equilibrador de carga de Azure Ayuda a comprender cómo funciona el equilibrador de carga y a sacarle provecho en la nube."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/16/2015"
   ms.author="joaoma" />


# Información general sobre el equilibrador de carga 
El Equilibrador de carga de Azure proporciona una alta disponibilidad y un elevado rendimiento de red para sus aplicaciones. Es un equilibrador de carga de tipo Capa 4 (TCP, UDP) que distribuye el tráfico entrante entre las instancias de servicio correctas de los servicios en la nube o las máquinas virtuales definidas en un conjunto de equilibrio de carga.
 
Se puede configurar para:

- Equilibrar la carga del tráfico entrante de Internet entre las máquinas virtuales. A esto se le conoce como [equilibrio de carga accesible desde Internet](load-balancer-internet-overview.md).
- Equilibrar la carga del tráfico entre máquinas virtuales de una red virtual, entre máquinas virtuales de servicios en la nube o entre equipos locales y máquinas virtuales de una red virtual local. A esto se le conoce como [equilibrio de carga interno (ILB)](load-balancer-internal-overview.md).
- 	Enrutar el tráfico externo a una instancia específica de máquina virtual.


## Características del equilibrador de carga

### Distribución basada en hash del equilibrador de carga de Capa 4

El Equilibrador de carga de Azure usa un algoritmo de distribución basado en hash. De forma predeterminada, usa un hash de 5-tupla (IP de origen, puerto de origen, IP de destino, puerto de destino, tipo de protocolo) para asignar el tráfico a los servidores disponibles. Dicho algoritmo solo proporciona adherencia dentro de una sesión de transporte. Los paquetes de la misma sesión TCP o UDP se dirigirán a la misma instancia de IP del centro de datos (DIP) tras el extremo con equilibrio de carga. Cuando el cliente se cierra y vuelve a abrir la conexión o inicia una nueva sesión desde la misma IP de origen, el puerto de origen cambia. Esto puede provocar que el tráfico vaya a un extremo DIP diferente.


Para obtener más detalles sobre el [modo de distribución de equilibrio de carga](load-balancer-distribution-mode.md)

![equilibrador de carga basado en hash](./media/load-balancer-overview/load-balancer-distribution.png)

### Enrutamiento de puerto

El Equilibrador de carga de Azure le proporciona control sobre cómo se administra la comunicación entrante, como el tráfico iniciado desde hosts de Internet o máquinas virtuales de otros servicios en la nube o redes virtuales. Este control se representa mediante un extremo (también conocido como extremo de entrada).

Un extremo escucha en un puerto público y enruta el tráfico a un puerto interno. Puede asignar los mismos puertos para un extremo interno o externo o usar un puerto diferente para ellos. Por ejemplo, puede tener un servidor web configurado para escuchar en el puerto 81, mientras que la asignación del extremo público es el puerto 80. La creación de un extremo público desencadena la creación de un Equilibrador de carga de Azure.

El uso y la configuración predeterminados de extremos en una máquina virtual que cree con el Portal de administración de Azure son el Protocolo de Escritorio remoto y el tráfico de sesión de Windows PowerShell remoto. Estos extremos le permiten administrar la máquina virtual de manera remota a través de Internet.


### Reconfiguración automática en escala horizontal o reducción vertical

El Equilibrador de carga de Azure se reconfigura solo instantáneamente cuando escala horizontalmente o reduce verticalmente las instancias (debido al aumento del recuento de instancias de rol web o de trabajo o a que se han colocado máquinas virtuales adicionales en el mismo conjunto con equilibrio de carga).


### Supervisión de servicios
El Equilibrador de carga de Azure ofrece la capacidad de sondear las diversas instancias de servidor para comprobar su estado. Cuando un sondeo no responde, el Equilibrador de carga de Azure deja de enviar una nueva conexión a las instancias incorrectas. Las conexiones existentes no se ven afectadas.

Se admiten tres tipos de sondeos:
 
- Sondeo de agente invitado (solo en máquinas virtuales PaaS). El Equilibrador de carga de Azure usa al agente invitado que hay dentro de la máquina virtual, escucha y responde con una respuesta HTTP 200 OK solo cuando la instancia está en estado preparado (es decir, no está en los estados ocupado, reciclando, deteniendo, etc.). Si el agente invitado no responde con HTTP 200 OK, el Equilibrador de carga de Azure marca la instancia como sin respuesta y deja de enviar tráfico a esa instancia. El Equilibrador de carga de Azure continúa haciendo ping a la instancia y, si el agente invitado responde con un HTTP 200, envía de nuevo el tráfico a esa instancia. Al usar un rol web, el código de su sitio web se ejecuta normalmente en w3wp.exe. El agente invitado o el tejido de Azure no supervisan este código, lo que significa que los errores en w3wp.exe (por ejemplo, respuestas HTTP 500) no se notificarán al agente invitado y que el equilibrador de carga no sabrá excluir esa instancia de la rotación.

- Sondeos HTTP personalizados El sondeo personalizado del equilibrador de carga invalida el sondeo del agente invitado predeterminado y le permite crear su propia lógica personalizada para determinar el estado de la instancia de rol. El equilibrador de carga sondeará periódicamente el extremo (de forma predeterminada cada 15 segundos) y la instancia se considerará en rotación si responde con un TCP ACK o HTTP 200 dentro del período de tiempo de espera (31 segundos de forma predeterminada). Esto puede resultar útil para implementar su propia lógica con el fin de quitar instancias de la rotación del equilibrador de carga. Por ejemplo, si la CPU de la instancia está por encima del 90 %, se devolvería un estado no 200. Para los roles web que usan w3wp.exe, esto también significa que dispone de supervisión automática de su sitio web puesto que los errores en el código de su sitio web devolverán un estado no 200 al sondeo del equilibrador de carga.

- Sondeos TCP personalizados Los sondeos TCP se basan en el establecimiento correcto de sesiones TCP para un puerto de sondeo definido.

Para obtener más información, consulte [Sondeo de estado del equilibrador de carga](https://msdn.microsoft.com/library/azure/jj151530.aspx).

### NAT de origen (SNAT)


Todo el tráfico saliente a Internet procedente de su servicio es con NAT de origen (SNAT) usando la misma dirección VIP que para el tráfico entrante. SNAT proporciona ventajas importantes:

- Permite la actualización y la recuperación ante desastres de servicios de forma fácil, puesto que la dirección VIP puede asignarse dinámicamente a otra instancia del servicio.

- Facilita la administración de ACL puesto que la ACL se puede expresar en términos de direcciones VIP y, por lo tanto, no cambia a medida que los servicios se escalan horizontalmente, se reducen verticalmente o se implementan de nuevo.

La configuración del Equilibrador de carga de Azure admite NAT de cono completo para UDP. NAT de cono completo es un tipo de NAT en el que el puerto permite conexiones entrantes desde cualquier host externo (en respuesta a una solicitud saliente).

![snat](./media/load-balancer-overview/load-balancer-snat.png)


>[AZURE.NOTE]Tenga en cuenta que para cada nueva conexión saliente iniciada por una máquina virtual, el Equilibrador de carga de Azure también asigna un puerto saliente. El host externo verá el tráfico entrante como VIP: puerto asignado. Si los escenarios requieren un gran número de conexiones salientes, se recomienda que las máquinas virtuales usen IP públicas de nivel de instancia para que tengan una IP saliente dedicada para la traducción de direcciones de red de origen (SNAT). Esto reducirá el riesgo de agotamiento de puertos.
>
>El número máximo de puertos que VIP o ILPIP pueden usar es 64.000. Se trata de una limitación estándar de TCP.


**Compatibilidad con varias IP con equilibrio de carga para máquinas virtuales**

Puede tener más de una dirección IP pública con equilibrio de carga asignada a un conjunto de máquinas virtuales. Con esta capacidad, puede hospedar varios sitios web SSL o varios agentes de escucha de grupo de disponibilidad SQL AlwaysOn en el mismo conjunto de máquinas virtuales. Encontrará más información en [Varias direcciones VIP por servicio en la nube](load-balancer-multivip.md)

**Implementaciones basadas en plantillas con el Administrador de recursos de Azure (vista previa pública)**. El Administrador de recursos de Azure (ARM) es el nuevo marco de administración de servicios de Azure. El Equilibrador de carga de Azure puede administrarse ahora mediante las herramientas y las API basadas en el Administrador de recursos de Azure. Para obtener más información sobre el Administrador de recursos de Azure, consulte [Iaas simplemente más fácil con el Administrador de recursos de Azure.](http://azure.microsoft.com/blog/2015/04/29/iaas-just-got-easier-again/)


## Pasos siguientes

[Información general sobre el equilibrador de carga accesible desde Internet](load-balancer-internet-overview.md)

[Información general sobre el equilibrador de carga interno](load-balancer-internal-overview.md)

[Introducción al equilibrador de carga accesible desde Internet](load-balancer-internet-getstarted.md)
 

<!---HONumber=Oct15_HO4-->