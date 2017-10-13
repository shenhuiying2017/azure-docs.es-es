---
title: "Información general sobre Azure Load Balancer | Microsoft Docs"
description: "Información general sobre las características, la arquitectura y la implementación del Equilibrador de carga de Azure Obtenga información acerca de cómo funciona el equilibrador de carga y aproveche sus ventajas en la nube."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 0f313dc0-f007-4cee-b2b9-f542357925a3
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ecf1fc38d2b9fd54fe5b00db616224a0848179fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-load-balancer-overview"></a>Información general sobre el Equilibrador de carga de Azure

Equilibrador de carga de Azure proporciona una alta disponibilidad y un elevado rendimiento de red para sus aplicaciones. Se trata de un equilibrador de carga de nivel 4 (TCP y UDP) que distribuye el tráfico entrante entre las instancias de servicio correctas de los servicios que se definen en un conjunto de carga equilibrada.

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure Load Balancer puede configurarse para lo siguiente:

* Equilibrar la carga del tráfico entrante de Internet entre las máquinas virtuales. Esta configuración se conoce como " [equilibrio de carga con conexión a Internet](load-balancer-internet-overview.md)".
* Equilibrar la carga del tráfico entre máquinas virtuales de una red virtual, entre máquinas virtuales de servicios en la nube o entre equipos locales y máquinas virtuales de una red virtual entre entornos locales. Esta configuración se conoce como " [equilibrio de carga interno](load-balancer-internal-overview.md)".
* Reenviar el tráfico externo a una máquina virtual determinada.

Todos los recursos en la nube necesitan una dirección IP pública para ser accesibles desde Internet. La infraestructura de nube de Azure usa direcciones IP no enrutables para sus recursos. Asimismo, usa traducción de direcciones de red (NAT) con direcciones IP públicas para comunicarse con Internet.

## <a name="load-balancer-features"></a>Características del equilibrador de carga

* Distribución basada en hash

    El Equilibrador de carga de Azure usa un algoritmo de distribución basado en hash. De forma predeterminada, usa un hash de 5-tupla (IP de origen, puerto de origen, IP de destino, puerto de destino y tipo de protocolo) para asignar el tráfico a los servidores disponibles. Dicho algoritmo solo proporciona adherencia *dentro* de una sesión de transporte. Los paquetes de la misma sesión TCP o UDP se dirigirán a la misma instancia tras el punto de conexión de carga equilibrada. Cuando el cliente cierra y vuelve a abrir la conexión o inicia una nueva sesión desde la misma IP de origen, el puerto de origen cambia. Esto puede provocar que el tráfico vaya a un punto de conexión diferente en un centro de datos distinto.

    Para obtener más detalles, consulte [Modo de distribución del equilibrador de carga (afinidad de IP de origen)](load-balancer-distribution-mode.md). En el siguiente gráfico se muestra la distribución basada en hash:

    ![Distribución basada en hash](./media/load-balancer-overview/load-balancer-distribution.png)

    Ilustración: Distribución basada en Hash

* Enrutamiento de puerto

    El Equilibrador de carga de Azure le permite controlar cómo se administra la comunicación entrante. Estas comunicación incluye tráfico iniciado desde hosts de Internet o máquinas virtuales de otros servicios en la nube o redes virtuales. Este control se representa mediante un punto de conexión (también conocido como punto de conexión de entrada).

    Un punto de conexión de entrada escucha en un puerto público y enruta el tráfico a un puerto interno. Puede asignar los mismos puertos para un extremo interno o externo o usar un puerto diferente para ellos. Por ejemplo, puede tener un servidor web configurado para escuchar el puerto 81, mientras que la asignación del punto de conexión público corresponda al puerto 80. La creación de un punto de conexión público desencadena la generación de una instancia del equilibrador de carga.

    Cuando se hace mediante el Portal de Azure, se crean automáticamente puntos de conexión a la máquina virtual del Protocolo de escritorio remoto (RDP) y el tráfico de sesión remota de Windows PowerShell. Estos puntos de conexión le permiten administrar la máquina virtual de manera remota a través de Internet.

* Reconfiguración automática

    El Equilibrador de carga de Azure se reconfigura inmediatamente al escalar instancias horizontalmente o reducirlas verticalmente. Por ejemplo, esta reconfiguración ocurre cuando se aumenta el recuento de instancias para los roles web o de trabajador en un servicio en la nube, o al agregar máquinas virtuales adicionales al mismo conjunto de carga equilibrada.

* Supervisión de servicios

    Azure Load Balancer puede sondear el estado de las distintas instancias de servidor. Cuando un sondeo no responde, el equilibrador de carga deja de enviar nuevas conexiones a las instancias incorrectas. Las conexiones existentes no se ven afectadas.

    Se admiten tres tipos de sondeos:

    + **Sonda de agente invitado (solo en máquinas virtuales de PaaS)**: el equilibrador de carga utiliza el agente invitado en la máquina virtual. El agente invitado solo escucha y contesta con una respuesta HTTP 200 OK cuando la instancia está lista (es decir, cuando su estado no es ocupada, en reciclaje o en detención). Si no responde con HTTP 200 OK, el equilibrador de carga marca la instancia como sin respuesta y deja de enviarle tráfico. El equilibrador de carga continúa para hacer ping a la instancia. Si el agente invitado responde con HTTP 200, el equilibrador de carga le enviará tráfico nuevamente. Cuando se usa un rol web, el código de sitio web normalmente se ejecuta en w3wp.exe, que no está supervisado por el agente invitado o el tejido de Azure. Esto significa que los errores de w3wp.exe (por ejemplo, las respuestas HTTP 500) no se notificarán al agente invitado, y el equilibrador de carga no sabrá sacar esa instancia de la rotación.
    + **Sonda personalizada de HTTP:** esta invalida la sonda predeterminado (del agente invitado) predeterminada. Puede usarlo para crear su propia lógica personalizada para determinar el estado de la instancia de rol. El equilibrador de carga sondeará periódicamente el punto de conexión (de forma predeterminada, cada 15 segundos). La instancia se considera en rotación si responde con un TCP ACK o HTTP 200 dentro del periodo de espera (el valor predeterminado es de 31 segundos). Esto resulta útil para implementar su propia lógica con el fin de quitar instancias de la rotación del equilibrador de carga. Por ejemplo, puede configurar la instancia para devolver un estado no 200 si la instancia está por encima del 90% de la CPU. En el caso de los roles web que usan w3wp.exe, también dispondrá de supervisión automática de su sitio web, puesto que los errores del código de este devuelven a la sonda un estado distinto a 200.
    + **Sonda personalizada de TCP:** esta se basa en el establecimiento correcto de sesiones TCP para un puerto de sondeo definido.

    Para obtener más información, consulte [LoadBalancerProbe schema](https://msdn.microsoft.com/library/azure/jj151530.aspx)(Esquema LoadBalancerProbe).

* NAT de origen

    Todo el tráfico saliente a Internet procedente de su servicio pasa por un proceso de NAT de origen (SNAT) mediante el uso de la misma dirección VIP que para el tráfico entrante. SNAT proporciona ventajas importantes:

    + Permite la actualización y la recuperación ante desastres de servicios de forma fácil, puesto que la dirección VIP puede asignarse dinámicamente a otra instancia del servicio.
    + Facilita la administración de la lista de control de acceso (ACL). Las ACL que se expresan en términos de VIP no cambian cuando los servicios se escalan verticalmente, se reducen horizontalmente o se vuelven a implementar.

    La configuración del equilibrador de carga admite NAT de cono completo para UDP. NAT de cono completo es un tipo de NAT en el que el puerto permite conexiones entrantes desde cualquier host externo (en respuesta a una solicitud saliente).

    Tenga en cuenta que, para cada nueva conexión saliente que inicie una máquina virtual, el equilibrador de carga también asigna un puerto saliente. El host externo verá el tráfico como un puerto IP virtual (VIP) asignado. Para los escenarios que requieren un gran número de conexiones salientes, se recomienda que las máquinas virtuales usen [direcciones IP públicas a nivel de instancia](../virtual-network/virtual-networks-instance-level-public-ip.md) para que tengan una dirección IP saliente específica para SNAT. Esto reduce el riesgo de agotamiento de puertos.

    Consulte el artículo sobre [conexiones salientes](load-balancer-outbound-connections.md) para obtener más información acerca de este tema.

### <a name="support-for-multiple-load-balanced-ip-addresses-for-virtual-machines"></a>Compatibilidad con varias direcciones IP con equilibrio de carga para máquinas virtuales
Puede asignar más de una dirección IP pública de carga equilibrada a un conjunto de máquinas virtuales. Gracias a esta posibilidad, puede hospedar varios sitios web SSL o varios agentes de escucha de Grupo de disponibilidad AlwaysOn de SQL Server en el mismo conjunto de máquinas virtuales. Para obtener más información, consulte [Varias direcciones VIP por servicio en la nube](load-balancer-multivip.md).

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="limitations"></a>Limitaciones

Los grupos de back-end de Load Balancer pueden contener cualquier SKU de máquina virtual, excepto nivel Básico.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el [equilibrador de carga accesible desde Internet](load-balancer-internet-overview.md)

- Para más información, consulte [Información general sobre el equilibrador de carga interno](load-balancer-internal-overview.md)

- Cree un [equilibrador de carga accesible desde Internet](load-balancer-get-started-internet-portal.md)

- Información acerca de las demás [funcionalidades de red](../networking/networking-overview.md) clave de Azure.

