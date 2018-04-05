---
title: Conexiones salientes en Azure (clásico) | Microsoft Docs
description: En este artículo se explica cómo posibilita Azure que los servicios en la nube puedan comunicarse con servicios de la red Internet pública.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: ec13109173f89b53e32f903febcec13c7f38c574
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="outbound-connections-classic"></a>Conexiones salientes (clásico)

Azure proporciona conectividad saliente para implementaciones de cliente gracias a varios mecanismos diferentes. En este artículo se describen cuáles son los escenarios, cuándo se aplican, cómo funcionan y cómo administrarlos.

>[!NOTE]
>Este artículo solo trata el modelo de implementación clásico.  Revise [Conexiones salientes](load-balancer-outbound-connections.md) para todos los escenarios de implementación de Resource Manager en Azure.

En una implementación de Azure es posible comunicarse con puntos de conexión externos a Azure en el espacio de direcciones IP públicas. Cuando una instancia inicia un flujo de salida a un destino del espacio de direcciones IP públicas, Azure asigna dinámicamente la dirección IP privada a una dirección IP pública. Una vez creada esta asignación, el tráfico de retorno de este flujo originado de salida también puede comunicarse con la dirección IP privada donde se originó el flujo.

Azure usa la traducción de direcciones de red de origen (SNAT) para realizar esta función. Cuando se enmascaran varias direcciones IP privadas detrás de una única dirección IP pública, Azure usa la [traducción de direcciones de puerto (PAT)](#pat) para enmascarar las direcciones IP privadas. En PAT se usan puertos efímeros y se [asignan previamente](#preallocatedports) en función del tamaño del grupo.

Son varios los [escenarios de salida](#scenarios). Puede combinar estos escenarios según sea necesario. Revíselos cuidadosamente para comprender las funcionalidades, las restricciones y los patrones que se aplican a su modelo de implementación y escenario de aplicación. Revise las instrucciones para [administrar estos escenarios](#snatexhaust).

## <a name="scenarios"></a>Información general de los escenarios

Azure ofrece tres métodos diferentes para lograr la conectividad saliente en las implementaciones clásicas.  No todas las implementaciones clásicas tienen los tres escenarios a su disposición:

| Escenario | Método | DESCRIPCIÓN | Rol de trabajo web | IaaS | 
| --- | --- | --- | --- | --- |
| [1. Máquina virtual con una dirección IP pública de nivel de instancia](#ilpip) | SNAT, no se usa el enmascaramiento de puertos |Azure utiliza la dirección IP pública asignada a la máquina virtual. La instancia tiene disponibles todos los puertos efímeros. | Sin  | Sí |
| [2. Punto de conexión con equilibrio de carga público](#publiclbendpoint) | SNAT con enmascaramiento de puertos (PAT) al punto de conexión público |Azure comparte el punto de conexión público de dirección IP pública con varios puntos de conexión privados. Azure usa puertos efímeros del punto de conexión público para PAT. | Sí | Sí |
| [3. Máquina virtual independiente](#defaultsnat) | SNAT con enmascaramiento de puertos (PAT) | Azure designa automáticamente una dirección IP pública para SNAT, la comparte con toda la implementación y usa puertos efímeros de la dirección IP del punto de conexión público para PAT. Se trata de un escenario de reserva para los escenarios anteriores. No es aconsejable si necesita visibilidad y control. | Sí | Sí|

Se trata de un subconjunto de las funcionalidades de conexiones de salida disponibles para las implementaciones de Resource Manager en Azure.  

Las distintas implementaciones en el modelo clásico tienen una funcionalidad diferente:

| Implementación clásica | Funcionalidad disponible | 
| --- | --- |
| Máquina virtual | escenarios [1](#ilpip), [2](#publiclbendpoint) o [3](#defaultsnat) |
| Rol de trabajo web | solo escenarios [2](#publiclbendpoint) y [3](#defaultsnat) | 

Las [estrategias de mitigación](#snatexhaust) también tienen las mismas diferencias.

El [algoritmo utilizado para la asignación previa de puertos efímeros](#ephemeralports) para PAT en las implementaciones clásicas es el mismo que para las implementaciones de recursos de Azure Resource Manager.

### <a name="ilpip"></a>Escenario 1: Máquina virtual con una dirección IP pública de nivel de instancia

En este escenario, la máquina virtual tiene una dirección IP pública a nivel de instancia (ILPIP) asignada. En lo que se refiere a las conexiones salientes, no importa si la máquina virtual tiene un punto de conexión con equilibrio de carga o no. Este escenario tiene prioridad sobre las demás. Cuando se usa una ILPIP, la máquina virtual la usa en todos los flujos de salida.  

Una dirección IP pública asignada a una máquina virtual es una relación 1:1 (en lugar de 1:muchos) y se implementa como NAT de 1:1 sin estado.  No se usa el enmascaramiento de puertos (PAT) y la máquina dispone de todos los puertos efímeros para su uso.

Si la aplicación inicia muchos flujos de salida y experimenta el agotamiento de puertos SNAT, considere la posibilidad de asignar una [ILPIP para mitigar las restricciones de SNAT](#assignilpip). Revise la sección [Administración de agotamiento de SNAT](#snatexhaust) en su totalidad.

### <a name="publiclbendpoint"></a>Escenario 2: Punto de conexión con equilibrio de carga público

En este escenario, la máquina virtual o el rol de trabajo web están asociados a una dirección IP pública a través del punto de conexión con equilibrio de carga. La máquina virtual no tiene ninguna dirección IP pública asignada. 

Cuando la máquina virtual con equilibrio de carga crea un flujo de salida, Azure traduce la dirección IP de origen privada del flujo de salida a la dirección IP pública del punto de conexión con equilibrio de carga público. Azure usa SNAT para realizar esta función. También usa [PAT](#pat) para enmascarar varias direcciones IP privadas detrás de una dirección IP pública. 

Para distinguir los flujos individuales que se originan en la máquina virtual, se emplean puertos efímeros del servidor front-end de la dirección IP pública de Load Balancer. Cuando se crean flujos de salida, SNAT usa dinámicamente los [puertos efímeros asignados previamente](#preallocatedports). En este contexto, los puertos efímeros usados para SNAT se conocen como puertos SNAT.

Los puertos SNAT se asignan previamente, como se describe en la sección [Descripción de SNAT y PAT](#snat). Estos puertos son un recurso finito que puede agotarse. Es importante entender cómo se [consumen](#pat). Para comprender cómo realizar el diseño de cara a este consumo y solucionar los posibles problemas, revise la sección [Administración de agotamiento de SNAT](#snatexhaust).

Cuando hay [varios puntos de conexión con equilibrio de carga públicos](load-balancer-multivip.md), cualquiera de las direcciones IP públicas es [candidata para los flujos de salida](#multivipsnat) y se selecciona una de un modo aleatorio.  

### <a name="defaultsnat"></a>Escenario 3: Ninguna dirección IP pública asociada

En este escenario, la máquina virtual o el rol de trabajo web no forma parte de un punto de conexión con equilibrio de carga público.  Y, en el caso de una máquina virtual, no tiene una dirección ILPIP asignada. Cuando la máquina virtual crea un flujo de salida, Azure traduce la dirección IP de origen privado del flujo de salida en una dirección IP de origen público. La dirección IP pública usada para este flujo de salida no es configurable y no cuenta para el límite de recursos de IP pública de la suscripción.  Azure asigna automáticamente esta dirección.

Para realizar esta función, Azure usa SNAT con enmascaramiento de puertos ([PAT](#pat)). Este escenario es similar al [escenario 2](#lb), excepto en que no hay ningún control sobre la dirección IP usada. Se trata de un escenario de reserva para cuando no existan los escenarios 1 y 2. No se recomienda este escenario si desea tener control sobre la dirección de salida. Si las conexiones salientes son una parte fundamental de la aplicación, debe elegir otro escenario.

Los puertos SNAT se asignan previamente, como se describe en la sección [Descripción de SNAT y PAT](#snat).  El número de máquinas virtuales o roles de trabajo web que comparten la dirección IP pública determina el número de puertos efímeros preasignados.   Es importante entender cómo se [consumen](#pat). Para comprender cómo realizar el diseño de cara a este consumo y solucionar los posibles problemas, revise la sección [Administración de agotamiento de SNAT](#snatexhaust).

## <a name="snat"></a>Descripción de SNAT y PAT

### <a name="pat"></a>SNAT de enmascaramiento de puertos (PAT)

Cuando una implementación realiza una conexión de salida, se reescribe cada origen de la conexión de salida. El origen se reescribe desde el espacio de direcciones IP privadas a la dirección IP pública asociada con la implementación (según los escenarios descritos anteriormente). En el espacio de direcciones IP públicas, la tupla de cinco elementos del flujo (dirección IP de origen, puerto de origen, protocolo de transporte IP, dirección IP de destino, puerto de destino) debe ser única.  

Para conseguir esto, se usan puertos efímeros (puertos SNAT) después de volver a escribir la dirección IP de origen privada, dado que varios flujos se originan desde una única dirección IP pública. 

Se consume un puerto SNAT por cada flujo a una sola dirección IP, puerto y protocolo de destino. En el caso de varios flujos a la misma dirección IP, puerto y protocolo de destino, cada flujo consume un solo puerto SNAT. Esto garantiza que los flujos son únicos si se han originado desde la misma dirección IP pública y se dirigen a la misma dirección IP, puerto y protocolo de destino. 

Si hay varios flujos, cada uno de ellos dirigido a una dirección IP, un puerto y un protocolo de destino diferentes, se comparte un solo puerto SNAT. La dirección IP, el puerto y el protocolo de destino hacen que los flujos sean únicos, sin necesidad de usar puertos de origen adicionales para distinguirlos en el espacio de direcciones IP públicas.

Cuando se agotan los recursos de los puertos SNAT, los flujos de salida generan errores hasta que los flujos ya existentes liberan puertos SNAT. Load Balancer reclama puertos SNAT cuando el flujo se cierra y usa un [tiempo de espera de inactividad de 4 minutos](#idletimeout) para reclamar puertos SNAT de los flujos inactivos.

Revise la sección [Administración de agotamiento de SNAT](#snatexhaust) para conocer los patrones de mitigación de las condiciones que suelen provocar el agotamiento de puertos SNAT.

### <a name="preallocatedports"></a>Preasignación de puertos efímeros para SNAT de enmascaramiento de puertos (PAT)

Para determinar el número de puertos SNAT asignados previamente disponibles, Azure usa un algoritmo basado en el tamaño del grupo de servidores back-end cuando se usa SNAT de enmascaramiento de puertos ([PAT](#pat)). Los puertos SNAT son puertos efímeros disponibles para una determinada dirección IP de origen pública.

Azure preasigna los puertos SNAT cuando se implementa una instancia en función de cuántas máquinas virtuales o instancias del rol de trabajo web compartan una determinada dirección IP pública.  Cuando se crean flujos de salida, [PAT](#pat) consume dinámicamente (hasta el límite asignado previamente) y libera estos puertos cuando el flujo se cierra o se [agota el tiempo de espera de inactividad](#ideltimeout).

En la tabla siguiente se muestran las asignaciones previas de puertos SNAT para los niveles de tamaño de grupo de servidores back-end:

| Instancias | Puertos SNAT preasignados por instancia |
| --- | --- |
| 1-50 | 1024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

Recuerde que el número de puertos SNAT disponibles no equivale directamente al número de flujos. Un solo puerto SNAT se puede reutilizar con varios destinos únicos. Solo se consumen puertos si es necesario que los flujos sean únicos. Para instrucciones sobre diseño y mitigación, consulte [cómo administrar este recurso agotable](#snatexhaust), así como la sección en la que se describe [PAT](#pat).

El cambio de tamaño de la implementación puede afectar a algunos de los flujos establecidos. Si el tamaño del grupo de servidores back-end aumenta y pasa al siguiente nivel, la mitad de los puertos SNAT previamente asignados se reclaman durante la transición al siguiente nivel más grande del grupo de servidores back-end. Los flujos que están asociados a un puerto SNAT reclamado agotan el tiempo de espera y deben restablecerse. Si se intenta un nuevo flujo, el flujo se inicia inmediatamente siempre y cuando los puertos asignados previamente estén disponibles.

Si el tamaño de la implementación se reduce y pasa a un nivel inferior, aumenta el número de puertos SNAT disponibles. En este caso, los puertos SNAT asignados existentes y sus flujos respectivos no se ven afectados.

Las asignaciones de puertos SNAT son específicas del protocolo de transporte IP (TCP y UDP se mantienen por separado) y se liberan en las siguientes condiciones:

### <a name="tcp-snat-port-release"></a>Liberación del puerto TCP SNAT

- Si tanto el cliente como el servidor envían FIN/ACK, el puerto SNAT se liberará después de 240 segundos.
- Si se ve un RST, el puerto SNAT se liberará después de 15 segundos.
- se alcanzó el tiempo de espera inactivo

### <a name="udp-snat-port-release"></a>Liberación del puerto UDP SNAT

- se alcanzó el tiempo de espera inactivo

## <a name="problemsolving"></a> Solución de problemas 

Esta sección está diseñada para ayudar a mitigar el agotamiento de SNAT y otros escenarios que pueden producirse con las conexiones salientes en Azure.

### <a name="snatexhaust"></a>Administración del agotamiento de puertos SNAT (PAT)
Los [puertos efímeros](#preallocatedports) utilizados para [PAT](#pat) son un recurso agotable, como se describe en [sin dirección IP pública asociada](#defaultsnat) y [punto de conexión con equilibrio de carga público](#publiclbendpoint).

Si sabe que se van a iniciar muchas conexiones TCP o UDP salientes a la misma dirección IP y puerto de destino, y observa errores en las conexiones salientes, o el soporte técnico está avisando del agotamiento de los puertos SNAT ([puertos efímeros](#preallocatedports) asignados previamente usados por [PAT](#pat)), dispone de varias opciones generales para mitigar este problema. Revise estas opciones y vea cuál está disponible y resulta mejor para su escenario. Es posible que una o varias de ellas puedan ayudar a administrar este escenario.

Si tiene problemas para entender el comportamiento de las conexiones de salida, puede usar las estadísticas de pila IP (netstat). O bien, puede ser útil observar el comportamiento de la conexión mediante capturas de paquetes.

#### <a name="connectionreuse"></a>Modificación de la aplicación para reutilizar las conexiones 
Puede reducir la demanda de puertos efímeros utilizados para SNAT mediante la reutilización de conexiones en la aplicación. Esto se aplica especialmente en el caso de protocolos como HTTP/1.1 donde la reutilización de las conexiones es la opción predeterminada. Y, a su vez, se pueden beneficiar otros protocolos que usan HTTP como transporte (por ejemplo, REST). 

La reutilización es siempre preferible a conexiones individuales atómicas para cada solicitud. La reutilización tiene como resultado transacciones TCP muy eficientes y con un mayor rendimiento.

#### <a name="connection pooling"></a>Modificación de la aplicación para usar la agrupación de conexiones
Puede emplear un esquema de agrupación de conexiones en la aplicación, donde las solicitudes se distribuyan internamente a través de un conjunto fijo de conexiones (cada una reutilizada siempre que sea posible). Este esquema limita el número de puertos efímeros en uso y crea un entorno más predecible. También puede aumentar el rendimiento de las solicitudes al permitir varias operaciones simultáneas cuando una sola conexión bloquea la respuesta de una operación.  

La agrupación de conexiones puede existir ya en el marco de trabajo que se use para desarrollar la aplicación o su configuración. Se puede combinar la agrupación de conexiones con la reutilización de conexiones. Así, sus múltiples conexiones consumen un número fijo y predecible de puertos para la misma dirección IP y el mismo puerto de destino. Las solicitudes también se benefician del uso eficiente de transacciones TCP que reducen la latencia y la utilización de recursos. Las transacciones UDP también pueden beneficiarse, dado que administrar el número de flujos de UDP puede evitar, a su vez, condiciones de administración del agotamiento de los puertos SNAT.

#### <a name="retry logic"></a>Modificación de la aplicación para utilizar lógica de reintento menos agresiva
Cuando se agotan los [puertos efímeros asignados previamente](#preallocatedports) usados para [PAT](#pat) o se producen errores de la aplicación, los reintentos agresivos o por fuerza bruta sin lógica de retroceso y decadencia causan que se produzca el agotamiento o su persistencia. Puede reducir la demanda de puertos efímeros mediante el uso de una lógica de reintento menos agresiva. 

Los puertos efímeros tienen un tiempo de inactividad de 4 minutos (no ajustable). Si los reintentos son demasiado agresivos, el agotamiento no tiene ninguna oportunidad de solucionarlo por sí mismo. Por lo tanto, una parte fundamental del diseño es considerar cómo y con qué frecuencia la aplicación vuelve a intentar las transacciones.

#### <a name="assignilpip"></a>Asignación de una dirección IP pública en el nivel de instancia a cada máquina virtual
La asignación de una ILPIP cambia el escenario a [Dirección IP pública en el nivel de instancia para una máquina virtual](#ilpip). Todos los puertos efímeros de la dirección IP pública que se usan para cada máquina virtual están disponibles para la máquina virtual. (En contraste con los escenarios donde los puertos efímeros de una dirección IP pública se comparten con todas las máquinas virtuales asociadas a la respectiva implementación). Hay ventajas e inconvenientes que deben considerarse, como el posible impacto de la creación de listas de permitidos con un gran número de direcciones IP individuales.

>[!NOTE] 
>Esta opción no está disponible para roles de trabajo web.

### <a name="idletimeout"></a>Uso de conexiones persistentes para restablecer el tiempo de espera de inactividad saliente

Las conexiones salientes tienen un tiempo de espera de inactividad de 4 minutos. Este tiempo de espera no es ajustable. Sin embargo, puede usar conexiones persistentes de transporte (por ejemplo, TCP) o de capa de aplicación para actualizar un flujo de inactividad y restablecer este tiempo de espera de inactividad en caso necesario.  Compruebe con el proveedor de cualquier software empaquetado si se admite o cómo habilitarlo.  Generalmente, solo un lado debe generar conexiones persistentes para restablecer el tiempo de espera de inactividad. 

## <a name="discoveroutbound"></a>Detección de la dirección IP pública que usa una máquina virtual
Hay muchas maneras de determinar la dirección IP de origen público de una conexión saliente. OpenDNS proporciona un servicio que puede mostrar la dirección IP pública de la máquina virtual. 

Mediante el comando nslookup, puede enviar una consulta DNS del nombre myip.opendns.com para la resolución de OpenDNS. El servicio devuelve la dirección IP de origen que se usó para enviar la consulta. Si ejecuta la siguiente consulta desde la máquina virtual, la respuesta es la dirección IP pública que se usa para esa máquina virtual:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre [Load Balancer](load-balancer-overview.md), utilizado en las implementaciones de Resource Manager.
- Conozca más sobre los escenarios de [conexión saliente](load-balancer-outbound-connections.md) disponibles en las implementaciones de Resource Manager.
