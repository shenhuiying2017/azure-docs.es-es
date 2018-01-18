---
title: Comprender las conexiones salientes en Azure | Microsoft Docs
description: "Este artículo explica cómo Azure habilita las máquinas virtuales para que se puedan comunicar con servicios de Internet públicos."
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: b8e225ba4374c73dbabac3dddab9ba37fa798a5a
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="understanding-outbound-connections-in-azure"></a>Comprender las conexiones salientes en Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Una máquina virtual (VM) de Azure puede comunicarse con puntos de conexión fuera de Azure en el espacio de direcciones IP públicas. Cuando una máquina virtual inicia un flujo de salida a un destino en el espacio de direcciones IP públicas, Azure asigna la dirección IP privada de la máquina virtual a una dirección IP pública y permite que el tráfico de retorno llegue a la máquina virtual.

Azure ofrece tres métodos diferentes para lograr la conectividad saliente. Cada método tiene sus propias funcionalidades y restricciones. Revise cada método cuidadosamente para elegir cuál satisface sus necesidades.

| Escenario | Método | Nota: |
| --- | --- | --- |
| Máquina virtual independiente (ningún equilibrador de carga, ninguna dirección IP pública a nivel de instancia) |Valor predeterminado de SNAT |Azure asocia una dirección IP pública para SNAT |
| Máquina virtual de carga equilibrada (ninguna dirección IP pública a nivel de instancia en la máquina virtual) |SNAT con uso del equilibrador de carga |Azure utiliza una dirección IP pública de Load Balancer para SNAT |
| Máquina virtual con dirección IP pública a nivel de instancia (con o sin equilibrador de carga) |No se utiliza SNAT |Azure utiliza la dirección IP pública asignada a la máquina virtual |

Si no desea que una máquina virtual se comunique con puntos de conexión fuera de Azure en el espacio de direcciones IP públicas, puede usar grupos de seguridad de red (NSG) para bloquear el acceso. El uso de estos grupos se describe con más detalle en [Prevención de las conexiones públicas](#preventing-public-connectivity).

## <a name="standalone-vm-with-no-instance-level-public-ip-address"></a>Máquina virtual independiente con ninguna dirección IP pública a nivel de instancia

En este escenario, la máquina virtual no forma parte de un grupo de Azure Load Balancer y no tiene una dirección IP pública a nivel de instancia (ILPIP) asignada a ella. Cuando la máquina virtual crea un flujo de salida, Azure traduce la dirección IP de origen privado del flujo de salida en una dirección IP de origen público. La dirección IP pública usada para este flujo de salida no es configurable y no cuenta para el límite de recursos de IP pública de la suscripción. Azure usa la traducción de direcciones de red de origen (SNAT) para realizar esta función. Se utilizan puertos efímeros de la dirección IP pública para distinguir flujos individuales que la máquina virtual originó. SNAT asigna dinámicamente puertos efímeros cuando se crean flujos. En este contexto, los puertos efímeros utilizados para SNAT se conocen como puertos SNAT.

Los puertos SNAT son un recurso finito que puede agotarse. Es importante entender cómo se consumen. Se consume un puerto SNAT por cada flujo a una sola dirección IP de destino. En el caso de varios flujos a la misma dirección IP y puerto de destino, cada flujo consume un solo puerto SNAT. Esto garantiza que los flujos son únicos si se han originado desde la misma dirección IP pública y se dirigen a la misma dirección IP y puerto de destino. Si hay varios flujos, cada uno de ellos dirigido a una dirección IP y puerto de destino diferentes, se comparte un solo puerto SNAT. La dirección IP y el puerto de destino hacen que los flujos sean únicos.

Puede usar [Log Analytics para Load Balancer](load-balancer-monitor-log.md) y [Registros de eventos de alerta para supervisar los mensajes de agotamiento de los puertos SNAT](load-balancer-monitor-log.md#alert-event-log) para supervisar el estado de las conexiones salientes. Cuando se agotan los recursos de los puertos SNAT, los flujos de salida generan errores hasta que los flujos ya existentes liberan otros puertos SNAT. Load Balancer utiliza un tiempo de espera de inactividad de 4 minutos para reclamar puertos SNAT.  Revise la sección siguiente [Máquina virtual con dirección IP pública a nivel de instancia (con o sin Load Balancer)](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer), así como [Administración de agotamiento de SNAT](#snatexhaust).

## <a name="load-balanced-vm-with-no-instance-level-public-ip-address"></a>Máquina virtual de carga equilibrada sin ninguna dirección IP pública a nivel de instancia

En este escenario, la máquina virtual forma parte de un grupo de Azure Load Balancer.  La máquina virtual no tiene ninguna dirección IP pública asignada. El recurso Load Balancer debe configurarse con una regla de equilibrador de carga que cree un vínculo entre el front-end de dirección IP pública y el grupo de back-end. Si no lleva a cabo esta configuración, el comportamiento es tal y como se describe en la sección anterior para [Máquina virtual independiente con ninguna dirección IP pública a nivel de instancia](load-balancer-outbound-connections.md#standalone-vm-with-no-instance-level-public-ip-address).

Cuando la máquina virtual de carga equilibrada crea un flujo de salida, Azure traduce la dirección IP de origen privado del flujo de salida en una dirección IP pública del front-end público de Load Balancer. Azure usa la traducción de direcciones de red de origen (SNAT) para realizar esta función. Se utilizan puertos efímeros de la dirección IP pública de Load Balancer para distinguir flujos individuales que la máquina virtual originó. SNAT asigna dinámicamente puertos efímeros cuando se crean flujos de salida. En este contexto, los puertos efímeros utilizados para SNAT se conocen como puertos SNAT.

Los puertos SNAT son un recurso finito que puede agotarse. Es importante entender cómo se consumen. Se consume un puerto SNAT por cada flujo a una sola dirección IP y puerto de destino. En el caso de varios flujos a la misma dirección IP y puerto de destino, cada flujo consume un solo puerto SNAT. Esto garantiza que los flujos son únicos si se han originado desde la misma dirección IP pública y se dirigen a la misma dirección IP y puerto de destino. Si hay varios flujos, cada uno de ellos dirigido a una dirección IP y puerto de destino diferentes, se comparte un solo puerto SNAT. La dirección IP y el puerto de destino hacen que los flujos sean únicos.

Puede usar [Log Analytics para Load Balancer](load-balancer-monitor-log.md) y [Registros de eventos de alerta para supervisar los mensajes de agotamiento de los puertos SNAT](load-balancer-monitor-log.md#alert-event-log) para supervisar el estado de las conexiones salientes. Cuando se agotan los recursos de los puertos SNAT, los flujos de salida generan errores hasta que los flujos ya existentes liberan otros puertos SNAT. Load Balancer utiliza un tiempo de espera de inactividad de 4 minutos para reclamar puertos SNAT.  Revise la sección siguiente, así como [Administración de agotamiento de SNAT](#snatexhaust).

## <a name="vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer"></a>Máquina virtual con dirección IP pública a nivel de instancia (con o sin Load Balancer)

En este escenario, la máquina virtual tiene una dirección IP pública a nivel de instancia (ILPIP) asignada. No importa si la máquina virtual es de carga equilibrada o no. Cuando se usa una ILPIP, no se utiliza la traducción de direcciones de red de origen (SNAT). La máquina virtual usa la ILPIP para todos los flujos de salida. Si la aplicación inicia muchos flujos de salida y experimenta el agotamiento de SNAT, considere la posibilidad de asignar una ILPIP para mitigar las restricciones de SNAT.

## <a name="discovering-the-public-ip-used-by-a-given-vm"></a>Detección de la dirección IP pública usada por una máquina virtual determinada

Hay muchas maneras de determinar la dirección IP de origen público de una conexión saliente. OpenDNS proporciona un servicio que puede mostrar la dirección IP pública de la máquina virtual. Mediante el comando nslookup, puede enviar una consulta DNS para el nombre myip.opendns.com para la resolución de OpenDNS. El servicio devuelve la dirección IP de origen que se usó para enviar la consulta. Si ejecuta la siguiente consulta desde la máquina virtual, la respuesta es la dirección IP pública que se utiliza para esa máquina virtual.

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-public-connectivity"></a>Prevención de las conexiones públicas

En ocasiones no es deseable que se le permita a una máquina virtual crear un flujo de salida o puede que haya algún requisito para administrar los destinos a los que pueden llegar con flujos de salida o qué destinos pueden iniciar flujos de entrada. En este caso, utilice los [grupos de seguridad de red (NSG)](../virtual-network/virtual-networks-nsg.md) para administrar los destinos a los que puede llegar la máquina virtual, así como qué destino público puede iniciar flujos entrantes. Cuando se aplica un grupo de seguridad de red a una máquina virtual de carga equilibrada, debe prestar atención a las [etiquetas predeterminadas](../virtual-network/virtual-networks-nsg.md#default-tags) y a las [reglas predeterminadas](../virtual-network/virtual-networks-nsg.md#default-rules).

Debe asegurarse de que la máquina virtual puede recibir solicitudes de sondeo de mantenimiento desde Azure Load Balancer. Si un grupo de seguridad de red bloquea las solicitudes de sondeo de mantenimiento de la etiqueta predeterminada AZURE_LOADBALANCER, se producirá un error en el sondeo de mantenimiento de la máquina virtual y esta se marca como inactiva. Load Balancer dejará de enviar nuevos flujos a esa máquina virtual.

## <a name="snatexhaust"></a>Administración de agotamiento de SNAT

Los puertos efímeros utilizados para SNAT son un recurso que se puede agotar, tal y como se describe en [Máquina virtual independiente con ninguna dirección IP pública a nivel de instancia](#standalone-vm-with-no-instance-level-public-ip-address) y [Máquina virtual de carga equilibrada sin ninguna dirección IP pública a nivel de instancia](#standalone-vm-with-no-instance-level-public-ip-address).

Si sabe que se pueden iniciar muchas conexiones TCP o UDP salientes a la misma dirección IP y puerto de destino, si observa errores en las conexiones salientes, o si el soporte técnico está avisando del agotamiento de los puertos SNAT, tiene varias opciones generales mitigar este problema.  Revise estas opciones y decida lo que resulte mejor para su escenario.  Es posible que una o más opciones ayuden a administrar este escenario.

### <a name="modify-application-to-reuse-connections"></a>Modificación de la aplicación para reutilizar las conexiones 
Puede reducir la demanda de puertos efímeros utilizados para SNAT mediante la reutilización de conexiones en la aplicación.  En especial, eso es así en el caso de protocolos como HTTP/1.1 en el que la reutilización de las conexiones es la opción predeterminada.  Y otros protocolos que usan HTTP como transporte (es decir, REST) se pueden beneficiar a su vez.  La reutilización siempre es mejor que usar conexiones TCP individuales y atómicas para cada solicitud, y da como resultado un mayor rendimiento y transacciones TCP muy eficaces.

### <a name="modify-application-to-use-connection-pooling"></a>Modificación de la aplicación para usar la agrupación de conexiones
Puede emplear un esquema de agrupación de conexiones en la aplicación, donde las solicitudes se distribuyan internamente a través de un conjunto fijo de conexiones (cada una reutilizada siempre que sea posible).  Así se limita el número de puertos efímeros en uso y se crea un entorno más predecible.  Y puede aumentar también el rendimiento de las solicitudes al permitir varias operaciones simultáneas cuando una sola conexión bloquea la respuesta de una operación.  La agrupación de conexiones puede existir ya en el marco de trabajo que se use para desarrollar la aplicación o su configuración.  Se puede combinar con la reutilización de las conexiones y con que varias solicitudes consumen un número fijo y predecible de puertos para la misma dirección IP y puerto de destino, al tiempo que también se beneficia de una utilización muy eficaz de las transacciones TCP que reduce la latencia y el uso de recursos.

### <a name="modify-application-to-use-less-aggressive-retry-logic"></a>Modificación de la aplicación para utilizar lógica de reintento menos agresiva
Cuando se agotan los puertos efímeros utilizados para SNAT o se producen errores de la aplicación, los reintentos agresivos o por fuerza bruta sin lógica de retroceso y decadencia causan que se produzca el agotamiento o su persistencia. Puede reducir la demanda de puertos efímeros mediante el uso de una lógica de reintento menos agresiva.   Los puertos efímeros tienen un tiempo de inactividad de 4 minutos (no ajustable) y si los reintentos son demasiado agresivos, el agotamiento no tendrá ninguna oportunidad de solucionarse por sí mismo.  Por lo tanto, tener en cuenta cómo y con qué frecuencia la aplicación vuelve a intentar las transacciones es una consideración fundamental del diseño.

### <a name="assign-an-instance-level-public-ip-to-each-vm"></a>Asignación de una dirección IP pública a nivel de instancia para cada máquina virtual
Esto cambia el escenario a [Dirección IP pública a nivel de instancia a una máquina virtual](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer).  Todos los puertos efímeros de la dirección IP pública que se utilizan en cada máquina virtual están disponibles para la máquina virtual (al contrario que en los escenarios en los que los puertos efímeros de una dirección IP pública se comparten con todos los de la máquina virtual asociada al grupo back-end respectivo).  Hay ventajas e inconvenientes que deben considerarse, como el costo adicional de las direcciones IP y el posible impacto de la creación de listas de permitidos con un gran número de direcciones IP individuales.

## <a name="limitations"></a>Limitaciones

Si hay [varias direcciones IP (públicas) asociadas a un equilibrador de carga](load-balancer-multivip-overview.md), ninguna de estas direcciones IP públicas serán candidatas para los flujos de salida.

Azure utiliza un algoritmo para determinar el número de puertos SNAT disponibles en función del tamaño del grupo.  Esto no es configurable en este momento.

Las conexiones salientes tienen un tiempo de espera de inactividad de 4 minutos.  Este valor no es ajustable.

Es importante recordar que el número de puertos SNAT disponibles no equivale directamente al número de conexiones. Consulte las secciones anteriores para conocer cuándo y cómo se asignan los puertos SNAT y el [modo de administrar este recurso limitado](#snatexhaust).
