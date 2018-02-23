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
ms.date: 02/05/2018
ms.author: kumud
ms.openlocfilehash: e1a2b4c281194ec4c70e4d9fe1bc97c5aa61436e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="understanding-outbound-connections-in-azure"></a>Comprender las conexiones salientes en Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]


Azure proporciona conectividad saliente para implementaciones de cliente gracias a varios mecanismos diferentes.  En este artículo se describen cuáles son los escenarios, cuándo se aplican, cómo funcionan y cómo administrarlos.

En una implementación de Azure es posible comunicarse con puntos de conexión fuera de Azure en el espacio de direcciones IP públicas. Cuando una instancia inicia un flujo de salida a un destino en el espacio de direcciones IP públicas, Azure asigna dinámicamente la dirección IP privada a una dirección IP pública.  Una vez creada esta asignación, el tráfico de retorno de este flujo originado de salida también puede comunicarse con la dirección IP privada donde se originó el flujo.

Azure usa la traducción de direcciones de red de origen (SNAT) para realizar esta función.  Cuando se enmascaran varias direcciones IP privadas detrás de una única dirección IP pública, Azure usa la [traducción de direcciones de puerto (PAT)](#pat) para enmascarar las direcciones IP privadas.  En PAT se usan puertos efímeros y se [asignan previamente](#preallocatedports) en función del tamaño del grupo.

Son varios los [escenarios de salida](#scenarios). Estos escenarios se pueden combinar según sea necesario. Revíselos cuidadosamente para comprender las funcionalidades, las restricciones y los patrones que se aplican a su modelo de implementación y escenario de aplicación.  Revise las instrucciones para [administrar estos escenarios](#snatexhaust).

## <a name="scenarios"></a>Información general de los escenarios

Azure cuenta con dos modelos de implementación principales: Azure Resource Manager y el modelo clásico. Cuando se usan [recursos de Azure Resource Manager](#arm), se definen explícitamente el equilibrador de carga y los recursos relacionados.  En las implementaciones clásicas se abstrae el concepto de un equilibrador de carga y se expresa una función similar mediante la definición de los puntos de conexión de un [servicio en la nube](#classic). Los [escenarios](#scenarios) aplicables en su implementación dependen de qué modelo de implementación se use.

### <a name="arm"></a>Azure Resource Manager

Actualmente, Azure proporciona tres métodos diferentes para lograr la conectividad saliente con los recursos de Azure Resource Manager.  Las implementaciones [clásicas](#classic) tienen un subconjunto de estos escenarios.

| Escenario | Método | DESCRIPCIÓN |
| --- | --- | --- |
| [1. Máquina virtual con dirección IP pública en el nivel de instancia (con o sin equilibrador de carga)](#ilpip) | SNAT, no se usa el enmascaramiento de puertos |Azure usa la dirección IP pública asignada a la configuración IP de NIC de. la instancia.  La instancia tiene disponibles todos los puertos efímeros. |
| [2. Equilibrador de carga público asociado a la máquina virtual (ninguna dirección IP pública de nivel de instancia en la instancia)](#lb) | SNAT con enmascaramiento de puertos (PAT) mediante los servidores front-end de Load Balancer |Azure comparte la dirección IP pública de los servidores front-end del equilibrador de carga público con varias direcciones IP privadas y usa puertos efímeros de estos servidores para PAT. |
| [3. Máquina virtual independiente (sin equilibrador de carga, sin dirección IP pública en el nivel de instancia)](#defaultsnat) | SNAT con enmascaramiento de puertos (PAT) | Azure designa automáticamente una dirección IP pública para SNAT, la comparte con varias direcciones IP privadas del conjunto de disponibilidad y usa los puertos efímeros de esta dirección IP pública.  Este escenario es un escenario de reserva para los escenarios 1 y 2 que le preceden y no se recomienda si necesitan visibilidad y control. |

Si no desea que una máquina virtual se comunique con puntos de conexión situados fuera de Azure en el espacio de direcciones IP públicas, puede usar grupos de seguridad de red (NSG) para bloquear el acceso según sea necesario.  El uso de estos grupos se describe con más detalle en [Prevención de las conexiones públicas](#preventoutbound).  No se incluyen en este artículo orientaciones de diseño ni detalles de implementación con respecto a cómo diseñar y administrar una red virtual sin acceso saliente.

### <a name="classic"></a>Clásica (servicios en la nube)

Los escenarios disponibles para las implementaciones clásicas son un subconjunto de los escenarios disponibles para las implementaciones de [Azure Resource Manager](#arm) y Load Balancer básico.

Una máquina virtual clásica tiene los mismos tres escenarios fundamentales que se describen para los recursos de Azure Resource Manager ([1](#ilpip), [2](#lb), [3](#defaultsnat)). Un rol de trabajo web clásico solo tiene dos escenarios ([2](#lb), [3](#defaultsnat)).  Las [estrategias de mitigación](#snatexhaust) también tienen las mismas diferencias.

El algoritmo utilizado para la [asignación previa de puertos efímeros](#ephemeralprots) para PAT en las implementaciones clásicas es el mismo que para las implementaciones de recursos de Azure Resource Manager.  

### <a name="ilpip"></a>Escenario 1: Máquina virtual con una dirección IP pública de nivel de instancia

En este escenario, la máquina virtual tiene una dirección IP pública a nivel de instancia (ILPIP) asignada. En lo que se refiere a las conexiones salientes, no importa si la máquina virtual tiene la carga equilibrada o no.  Este escenario tiene prioridad sobre las demás. Cuando se usa una ILPIP, la máquina virtual la usa en todos los flujos de salida.  

No se usa el enmascaramiento de puertos (PAT) y la máquina dispone de todos los puertos efímeros para su uso.

Si la aplicación inicia muchos flujos de salida y experimenta el agotamiento de puertos SNAT, considere la posibilidad de asignar una [ILPIP para mitigar las restricciones de SNAT](#assignilpip). Revise la sección [Administración de agotamiento de SNAT](#snatexhaust) en su totalidad.

### <a name="lb"></a>Escenario 2: Máquina virtual de carga equilibrada sin dirección IP pública en el nivel de instancia

En este escenario, la máquina virtual forma parte de un grupo de servidores back-end de Load Balancer.  La máquina virtual no tiene ninguna dirección IP pública asignada. El recurso Load Balancer debe configurarse con una regla de equilibrador de carga que cree un vínculo entre el front-end de dirección IP pública y el grupo de back-end. Si no lleva a cabo esta configuración de reglas, el comportamiento es tal y como se describe en la sección para [Máquina virtual independiente sin dirección IP pública en el nivel de instancia](#defaultsnat).  No es necesario que la regla tenga un agente de escucha en funcionamiento en el grupo de servidores back-end o que el sondeo de mantenimiento sea correcto.

Cuando la máquina virtual de carga equilibrada crea un flujo de salida, Azure traduce la dirección IP de origen privado del flujo de salida en una dirección IP pública del front-end público de Load Balancer. Azure usa la traducción de direcciones de red de origen (SNAT) para realizar esta función, así como la [traducción de direcciones de puerto (PAT)](#pat) para enmascarar varias direcciones IP privadas detrás de una dirección IP pública. Para distinguir los flujos individuales que se originan en la máquina virtual, se emplean puertos efímeros del servidor front-end de la dirección IP pública de Load Balancer. Cuando se crean flujos de salida, SNAT usa dinámicamente los [puertos efímeros asignados previamente](#preallocatedports). En este contexto, los puertos efímeros utilizados para SNAT se conocen como puertos SNAT.

Los puertos SNAT se asignan previamente, como se describe en la sección [Descripción de SNAT y PAT](#snat) y son un recurso finito que puede agotarse. Es importante entender cómo se [consumen](#pat). Revise [Administración de agotamiento de SNAT](#snatexhaust) para conocer la información que necesite sobre el diseño y la mitigación.

Cuando hay [varias direcciones IP (públicas) asociadas a un equilibrador de carga básico](load-balancer-multivip-overview.md), cualquiera de estas direcciones IP públicas son [candidatas para los flujos de salida, y se selecciona una](#multivipsnat).  

Puede usar [Log Analytics para Load Balancer](load-balancer-monitor-log.md) y [registros de eventos de alerta para supervisar los mensajes de agotamiento de puertos SNAT](load-balancer-monitor-log.md#alert-event-log) con el fin de inspeccionar el estado de las conexiones salientes con Load Balancer básico.

### <a name="defaultsnat"></a>Escenario 3: Máquina virtual independiente sin una dirección IP pública en el nivel de instancia

La máquina virtual no forma parte de un grupo de Azure Load Balancer y no tiene asignada una dirección IP pública en el nivel de instancia (ILPIP). Cuando la máquina virtual crea un flujo de salida, Azure traduce la dirección IP de origen privado del flujo de salida en una dirección IP de origen público. La dirección IP pública usada para este flujo de salida no es configurable y no cuenta para el límite de recursos de IP pública de la suscripción. Azure usa la traducción de direcciones de red de origen (SNAT) con enmascaramiento de puertos ([PAT](#pat)) para realizar esta función. Este escenario es similar al [escenario 2](#lb), excepto en que no hay ningún control sobre la dirección IP usada.  Se trata de un escenario de reserva para cuando no existan los escenarios 1 y 2.  Este escenario no se recomienda si desea un control sobre la dirección de salida.

Los puertos SNAT se asignan previamente, como se describe en la sección [Descripción de SNAT y PAT](#snat) y son un recurso finito que puede agotarse. Es importante entender cómo se [consumen](#pat). Revise [Administración de agotamiento de SNAT](#snatexhaust) para conocer la información que necesite sobre el diseño y la mitigación.

### <a name="combinations"></a>Combinación de varios escenarios

Los escenarios descritos en las secciones anteriores se pueden combinar para conseguir un resultado concreto.  Cuando hay varios escenarios, se aplica un orden de prioridad: el [escenario 1](#ilpip) tiene prioridad sobre el [escenario 2](#lb) y el [3](#defaultsnat) (solo Azure Resource Manager) y el [escenario 2](#lb) invalida el [escenario 3](#defaultsnat) (Azure Resource Manager y modelo clásico).

Un ejemplo sería una implementación de Azure Resource Manager donde la aplicación depende en gran medida de las conexiones salientes a un número limitado de destinos, peor también recibe flujos de entrada a través de un servidor front-end de Load Balancer. En este caso, podría combinar los escenarios 1 y 2 como ayuda.  Revise [Administración de agotamiento de SNAT](#snatexhaust) para ver patrones adicionales.

### <a name="multivipsnat"></a> Varios servidores front-end para flujos de salida

Load Balancer básico elegirá un único servidor front-end para usarlo con los flujos de salida cuando haya [varios servidores front-end de direcciones IP públicas](load-balancer-multivip-overview.md) que sean candidatos para los flujos de salida.  Esta selección no es configurable y el algoritmo de selección se debe considerar aleatorio.  Puede designar una dirección IP específica para la salida como se describe en los [escenarios combinados](#combinations).

## <a name="snat"></a>Descripción de SNAT y PAT

### <a name="pat"></a>SNAT de enmascaramiento de puertos (PAT)

Cuando un recurso de Load Balancer público está asociado con instancias de máquina virtual, se reescribe cada origen de conexión de salida. El origen se reescribe desde el espacio de direcciones IP privadas de red virtual a la dirección IP pública de servidor front-end del equilibrador de carga. En el espacio de direcciones IP públicas, la tupla de cinco elementos del flujo (dirección IP de origen, puerto de origen, protocolo de transporte IP, dirección IP de destino, puerto de destino) debe ser única.  Para conseguir esto después de volver a escribir la dirección IP de origen privada, se usan puertos efímeros dado que varios flujos se originan desde una única dirección IP pública.  Estos puertos efímeros se denominan puertos SNAT. 

Se consume un puerto SNAT por cada flujo a una sola dirección IP, puerto y protocolo de destino. En el caso de varios flujos a la misma dirección IP, puerto y protocolo de destino, cada flujo consume un solo puerto SNAT. Esto garantiza que los flujos son únicos si se han originado desde la misma dirección IP pública y se dirigen a la misma dirección IP, puerto y protocolo de destino. 

Si hay varios flujos, cada uno de ellos dirigido a una dirección IP, un puerto y un protocolo de destino diferentes, se comparte un solo puerto SNAT. La dirección IP, el puerto y el protocolo de destino hacen que los flujos sean únicos, sin necesidad de usar puertos de origen adicionales para distinguirlos en el espacio de direcciones IP públicas.

Cuando se agotan los recursos de los puertos SNAT, los flujos de salida generan errores hasta que los flujos ya existentes liberan otros puertos SNAT. Load Balancer reclama puertos SNAT cuando el flujo se cierra y usa un [tiempo de espera de inactividad de 4 minutos](#idletimeout) para reclamar puertos SNAT de los flujos inactivos.

Revise la sección [Administración de agotamiento de SNAT](#snatexhaust) para ver los patrones de mitigación de las condiciones que suelen provocar el agotamiento de puertos SNAT.

### <a name="preallocatedports"></a>Preasignación de puertos efímeros para SNAT de enmascaramiento de puertos (PAT)

Para determinar el número de puertos SNAT asignados previamente disponibles, Azure usa un algoritmo basado en el tamaño del grupo de servidores back-end cuando se usa SNAT de enmascaramiento de puertos ([PAT](#pat)).  Los puertos SNAT son puertos efímeros disponibles para una determinada dirección IP de origen pública.

Azure asigna previamente puertos SNAT a la configuración de IP de la NIC de cada máquina virtual. Cuando se agrega una configuración IP al grupo, los puertos SNAT se asignan previamente a esta configuración IP en función del tamaño del grupo de servidores back-end. En el caso de los roles de trabajo web clásicos, la asignación es por instancia de rol.  Cuando se crean flujos de salida, [PAT](#pat) consume dinámicamente (hasta el límite asignado previamente) y libera estos puertos cuando el flujo se cierra o se [agota el tiempo de espera de inactividad](#ideltimeout).

En la tabla siguiente se muestran las asignaciones previas de puertos SNAT para los niveles de tamaño de grupo de servidores back-end:

| Tamaño del grupo (instancias de máquina virtual) | Puertos SNAT asignados previamente por configuración IP|
| --- | --- |
| 1-50 | 1024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801 -1000 | 32 |

Es importante recordar que el número de puertos SNAT disponibles no equivale directamente al número de conexiones. Un solo puerto SNAT se puede reutilizar con varios destinos únicos. Solo se consumen puertos si es necesario hacerlos únicos. Para instrucciones sobre diseño y mitigación, consulte [cómo administrar los recursos agotables](#snatexhaust), así como la sección en la que se describe [PAT](#pat).

Cambiar el tamaño del grupo de servidores de back-end puede afectar a algunos de los flujos establecidos. Si el tamaño del grupo de servidores back-end aumenta y pasa al siguiente nivel, la mitad de los puertos SNAT previamente asignados se reclaman durante la transición al siguiente nivel más grande del grupo de servidores back-end. Los flujos que están asociados a un puerto SNAT reclamado agotan el tiempo de espera y deben restablecerse. Los nuevos intentos de conexión se realizan correctamente de forma inmediata mientras hay puertos previamente asignados disponibles.

Si el tamaño del grupo de servidores back-end se reduce y pasa a un nivel inferior, aumenta el número de puertos SNAT disponibles. En este caso, los puertos SNAT asignados existentes y sus flujos respectivos no se ven afectados.

## <a name="snatexhaust"></a>Administración de agotamiento de puertos SNAT (PAT)

Los [puertos efímeros](#preallocatedports) usados para [PAT](#pat) son un recurso agotable, como se describe en [Máquina virtual independiente sin una dirección IP pública en el nivel de instancia](#defaultsnat) y [Máquina virtual de carga equilibrada sin dirección IP pública en el nivel de instancia](#lb).

Si sabe que se van a iniciar muchas conexiones TCP o UDP salientes a la misma dirección IP y puerto de destino, y observa errores en las conexiones salientes, o el soporte técnico está avisando del agotamiento de los puertos SNAT ([puertos efímeros](#preallocatedports) usados por [PAT](#pat)), tiene varias opciones generales para mitigar este problema.  Revise estas opciones y vea cuál está disponible y resulta mejor para su escenario.  Es posible que una o más opciones ayuden a administrar este escenario.

Si tiene problemas para entender el comportamiento de las conexiones salientes, puede usar las estadísticas de pila IP (netstat) o puede ser útil observar los comportamientos de las conexiones mediante capturas de paquetes.  Estas capturas de paquetes se pueden realizar en el sistema operativo invitado de la instancia o se puede usar [Network Watcher para la captura de paquetes](../network-watcher/network-watcher-packet-capture-manage-portal.md).

### <a name="connectionreuse"></a>Modificación de la aplicación para reutilizar las conexiones 
Puede reducir la demanda de puertos efímeros utilizados para SNAT mediante la reutilización de conexiones en la aplicación.  En especial, eso es así en el caso de protocolos como HTTP/1.1 en el que la reutilización de las conexiones es la opción predeterminada.  Y otros protocolos que usan HTTP como transporte (es decir, REST) se pueden beneficiar a su vez.  La reutilización siempre es mejor que usar conexiones TCP individuales y atómicas para cada solicitud, y da como resultado un mayor rendimiento y transacciones TCP muy eficaces.

### <a name="connection pooling"></a>Modificación de la aplicación para usar la agrupación de conexiones
Puede emplear un esquema de agrupación de conexiones en la aplicación, donde las solicitudes se distribuyan internamente a través de un conjunto fijo de conexiones (cada una reutilizada siempre que sea posible).  Así se limita el número de puertos efímeros en uso y se crea un entorno más predecible.  Y puede aumentar también el rendimiento de las solicitudes al permitir varias operaciones simultáneas cuando una sola conexión bloquea la respuesta de una operación.  La agrupación de conexiones puede existir ya en el marco de trabajo que se use para desarrollar la aplicación o su configuración.  Se puede combinar con la reutilización de las conexiones y con que varias solicitudes consumen un número fijo y predecible de puertos para la misma dirección IP y puerto de destino, al tiempo que también se beneficia de una utilización muy eficaz de las transacciones TCP que reduce la latencia y el uso de recursos.  Las transacciones UDP también pueden beneficiarse dado que al administrar el número de flujos de UDP se pueden evitar a su vez condiciones de agotamiento y administrar el uso de los puertos SNAT.

### <a name="retry logic"></a>Modificación de la aplicación para utilizar la lógica de reintento menos agresiva
Cuando se agotan los [puertos efímeros asignados previamente](#preallocatedports) usados para [PAT](#pat) o se producen errores de la aplicación, los reintentos agresivos o por fuerza bruta sin lógica de retroceso y decadencia causan que se produzca el agotamiento o su persistencia. Puede reducir la demanda de puertos efímeros mediante el uso de una lógica de reintento menos agresiva.   Los puertos efímeros tienen un tiempo de inactividad de 4 minutos (no ajustable) y si los reintentos son demasiado agresivos, el agotamiento no tendrá ninguna oportunidad de solucionarse por sí mismo.  Por lo tanto, tener en cuenta cómo y con qué frecuencia la aplicación vuelve a intentar las transacciones es una consideración fundamental del diseño.

### <a name="assignilpip"></a>Asignación de una dirección IP pública en el nivel de instancia a cada máquina virtual
Esto cambia el escenario a [Dirección IP pública a nivel de instancia a una máquina virtual](#ilpip).  Todos los puertos efímeros de la dirección IP pública que se utilizan en cada máquina virtual están disponibles para la máquina virtual (al contrario que en los escenarios en los que los puertos efímeros de una dirección IP pública se comparten con todos los de la máquina virtual asociada al grupo back-end respectivo).  Existen ventajas e inconvenientes que deben considerarse, como el costo adicional de las direcciones IP y el posible impacto de la creación de listas de permitidos con un gran número de direcciones IP individuales.

>[!NOTE] 
>Esta opción no está disponible para roles de trabajo web.

### <a name="idletimeout"></a>Uso de conexiones persistentes para restablecer el tiempo de espera de inactividad saliente

Las conexiones salientes tienen un tiempo de espera de inactividad de 4 minutos. Este valor no es ajustable. Sin embargo, puede usar conexiones persistentes de transporte (por ejemplo, TCP) o de capa de aplicación para actualizar un flujo de inactividad y restablecer este tiempo de espera de inactividad en caso necesario.

## <a name="discoveroutbound"></a>Detección de la dirección IP pública usada por una máquina virtual determinada
Hay muchas maneras de determinar la dirección IP de origen público de una conexión saliente. OpenDNS proporciona un servicio que puede mostrar la dirección IP pública de la máquina virtual. Mediante el comando nslookup, puede enviar una consulta DNS para el nombre myip.opendns.com para la resolución de OpenDNS. El servicio devuelve la dirección IP de origen que se usó para enviar la consulta. Si ejecuta la siguiente consulta desde la máquina virtual, la respuesta es la dirección IP pública que se utiliza para esa máquina virtual.

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Impedir la conectividad saliente
En ocasiones no es deseable que se le permita a una máquina virtual crear un flujo de salida o puede que haya algún requisito para administrar los destinos a los que pueden llegar con flujos de salida o qué destinos pueden iniciar flujos de entrada. En este caso, utilice los [grupos de seguridad de red (NSG)](../virtual-network/virtual-networks-nsg.md) para administrar los destinos a los que puede llegar la máquina virtual, así como qué destino público puede iniciar flujos entrantes. Cuando se aplica un grupo de seguridad de red a una máquina virtual de carga equilibrada, debe prestar atención a las [etiquetas predeterminadas](../virtual-network/virtual-networks-nsg.md#default-tags) y a las [reglas predeterminadas](../virtual-network/virtual-networks-nsg.md#default-rules).

Debe asegurarse de que la máquina virtual puede recibir solicitudes de sondeo de mantenimiento desde Azure Load Balancer. Si un grupo de seguridad de red bloquea las solicitudes de sondeo de mantenimiento de la etiqueta predeterminada AZURE_LOADBALANCER, se producirá un error en el sondeo de mantenimiento de la máquina virtual y esta se marca como inactiva. Load Balancer dejará de enviar nuevos flujos a esa máquina virtual.

## <a name="next-steps"></a>pasos siguientes

- Aprenda más sobre [Load Balancer básico](load-balancer-overview.md).
- Más información sobre los [grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md).
- Aprenda sobre las demás [funcionalidades de red](../networking/networking-overview.md) clave en Azure.
