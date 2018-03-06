---
title: "Introducción a Azure Network Security | Microsoft Docs"
description: "Obtenga información acerca de las opciones de seguridad para controlar el flujo de tráfico de red entre los recursos de Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: jdial
ms.openlocfilehash: fbf0556cc47bc08a71fcf050b43c2dbbe5d27184
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="network-security"></a>Seguridad de las redes

Puede limitar el tráfico de red a los recursos de una red virtual mediante un grupo de seguridad de red. Un grupo de seguridad de red contiene una lista de reglas de seguridad que permiten o deniegan el tráfico de red entrante o saliente en función de las direcciones IP de origen o destino, el puerto y el protocolo. 

## <a name="network-security-groups"></a>Grupos de seguridad de red

Todas las interfaces de red tienen un grupo de seguridad de red asociado o ninguno. Cada interfaz de red existe en una subred de la [red virtual](virtual-networks-overview.md). Una subred también puede tener un grupo de seguridad de red asociado o ninguno. 

Cuando se aplican a una subred, las reglas de seguridad se aplican a todos los recursos de la subred. Además de interfaces de red, puede que tenga instancias de otros servicios de Azure como HDInsight, conjuntos de escalado de máquinas virtuales y entornos del servicio de aplicaciones implementados en la subred.

La aplicación de los grupos de seguridad de red cuando alguno de ellos está asociado a una interfaz de red y a la subred en la que esta se encuentra, se describe a continuación:

- **Tráfico entrante**: el grupo de seguridad de red asociado a la subred en la que la interfaz de red se encuentra se evalúa primero. Todo el tráfico permitido a través del grupo de seguridad de red asociado a la subred lo evalúa posteriormente el grupo de seguridad de red asociado a la interfaz de red. Por ejemplo, puede que necesite acceso entrante a una máquina virtual a través del puerto 80 de Internet. Si asocia un grupo de seguridad de red a la interfaz de red y a la subred en la que esta se encuentra, el grupo de seguridad de red asociado a la subred y la interfaz de red deberán permitir el puerto 80. Si solo permitió el puerto 80 a través del grupo de seguridad de red asociado a la subred o a la interfaz de red en la que esta se encuentra, la comunicación producirá un error debido a las reglas de seguridad predeterminadas. Consulte las [reglas de seguridad predeterminadas](#default-security-rules) para más información. Si solo se aplica un grupo de seguridad de red a la subred o la interfaz de red, y este contiene una regla que permite el tráfico entrante en el puerto 80, por ejemplo, la comunicación se realizará correctamente. 
- **Tráfico saliente**: el grupo de seguridad de red asociado a la interfaz de red se evalúa primero. Todo el tráfico permitido a través del grupo de seguridad de red asociado a la interfaz de red lo evalúa posteriormente el grupo de seguridad de red asociado a la subred.

Puede que no siempre sepa cuándo se aplican los grupos de seguridad de red a una interfaz de red y a una subred. Puede ver fácilmente las reglas agregadas que se aplican a una interfaz de red mediante la visualización de las [reglas de seguridad vigentes](virtual-network-manage-nsg-arm-portal.md) de una interfaz de red. También puede usar la funcionalidad [Comprobación del flujo de IP](../network-watcher/network-watcher-check-ip-flow-verify-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de Azure Network Watcher para determinar si se permite la comunicación hacia una interfaz de red o desde esta. La herramienta le indica si se permite la comunicación y qué regla de seguridad de red permite o deniega el tráfico.
 
> [!NOTE]
> Los grupos de seguridad de red se asocian a las subredes o a las máquinas virtuales y a los servicios en la nube que se implementan según el modelo de implementación clásica, en vez de a las interfaces de red del modelo de implementación de Resource Manager. Para más información sobre los modelos de implementación de Azure, consulte [Descripción de los modelos de implementación de Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

El mismo grupo de seguridad de red se puede aplicar a tantas interfaces de red y subredes individuales como desee.

## <a name="security-rules"></a>Reglas de seguridad

Un grupo de seguridad de red puede contener cero reglas, o tantas reglas como desee, siempre que esté dentro de los [límites](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) de la suscripción de Azure. Cada regla especifica las siguientes propiedades:

|Propiedad  |Explicación  |
|---------|---------|
|NOMBRE|Un nombre único dentro del grupo de seguridad de red.|
|Prioridad | Un número entre 100 y 4096. Las reglas se procesan en orden de prioridad. Se procesan primero las reglas con los números más bajos ya que estos tienen más prioridad. Si el tráfico coincide con una regla, se detiene el procesamiento. Como resultado, las reglas con menor prioridad (números más altos) que tengan los mismos atributos que las reglas con una prioridad mayor no se procesarán.|
|Origen o destino| Todas, o una dirección IP individual, un bloque CIDR (10.0.0.0/24, por ejemplo), una etiqueta de servicio o un grupo de seguridad de aplicaciones. Obtenga más información sobre [etiquetas de servicio](#service-tags) y [grupos de seguridad de aplicaciones](#application-security-groups). La especificación de un intervalo, una etiqueta de servicio o grupo de seguridad de aplicaciones le permite crear menos reglas de seguridad. La posibilidad de especificar varias direcciones IP individuales e intervalos (no puede especificar varias etiquetas de servicio ni grupos de aplicaciones) en una regla se conoce como reglas de seguridad aumentada. Más información sobre [reglas de seguridad aumentada](#augmented-security-rules). Las reglas de seguridad aumentada solo se pueden generar en los grupos de seguridad de red creados mediante el modelo de implementación de Resource Manager. No puede especificar varias direcciones IP ni intervalos de ellas en grupos de seguridad de red creados mediante el modelo de implementación clásica.|
|Protocolo     | TCP, UDP o Cualquiera, opción que incluye TCP, UDP e ICMP. No se puede especificar ICMP de forma independiente, por lo que si necesita usarlo, deberá utilizar la opción Cualquiera. |
|Dirección| Si la regla se aplica al tráfico entrante o al saliente.|
|Intervalo de puertos     |Puede especificar un puerto individual o un intervalo de puertos. Por ejemplo, puede especificar 80 o 10000-10005. La especificación de intervalos le permite crear menos reglas de seguridad. Las reglas de seguridad aumentada solo se pueden generar en los grupos de seguridad de red creados mediante el modelo de implementación de Resource Manager. No puede especificar varios puertos ni intervalos de ellos en la misma regla de seguridad de los grupos de seguridad de red creados mediante el modelo de implementación clásica.   |
|.     | Permitir o denegar        |

Las reglas de seguridad tienen un estado. Por ejemplo, si especifica una regla de seguridad de salida para cualquier dirección a través del puerto 80, no será necesario especificar una regla de seguridad de entrada para la respuesta al tráfico saliente. Solo debe especificar una regla de seguridad de entrada si la comunicación se inicia de forma externa. Lo contrario también es cierto. Si se permite el tráfico entrante a través de un puerto, no es necesario especificar una regla de seguridad de salida para responder al tráfico a través del puerto. Para obtener información acerca de los límites a la hora de crear reglas de seguridad, consulte los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Reglas de seguridad aumentada

Las reglas aumentadas permiten simplificar la definición de seguridad para las redes virtuales, lo que le permitirá definir directivas de seguridad de red más grandes y complejas, con menos reglas. Puede combinar varios puertos, varias direcciones IP explícitas, etiquetas de servicio y grupos de seguridad de aplicaciones en una única regla de seguridad de fácil comprensión. Use reglas aumentadas en los campos de origen, destino y puerto de una regla. Al crear una regla, puede especificar varias direcciones IP, intervalos de CIDR y puertos explícitos. Para simplificar el mantenimiento de la definición de la regla de seguridad, combine las reglas de seguridad aumentada con etiquetas de servicio o grupos de seguridad de aplicaciones. 

Para obtener información acerca de los límites a la hora de crear reglas de seguridad aumentada, consulte los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="default-security-rules"></a>reglas de seguridad predeterminadas

Si un grupo de seguridad de red no está asociado a una subred o interfaz de red, se permite todo el tráfico entrante hacia la subred o interfaz de red y todo el tráfico saliente procedente de ambos. En cuanto se crea un grupo de seguridad de red, Azure crea las siguientes reglas predeterminadas en el grupo de seguridad de red:

### <a name="inbound"></a>Entrada

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Prioridad|Origen|Puertos de origen|Destino|Puertos de destino|Protocolo|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Todo|PERMITIR|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Prioridad|Origen|Puertos de origen|Destino|Puertos de destino|Protocolo|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Todo|PERMITIR|

#### <a name="denyallinbound"></a>DenyAllInbound

|Prioridad|Origen|Puertos de origen|Destino|Puertos de destino|Protocolo|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Todo|Denegar|

### <a name="outbound"></a>Salida

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Prioridad|Origen|Puertos de origen| Destino | Puertos de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Todo | PERMITIR |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Prioridad|Origen|Puertos de origen| Destino | Puertos de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Todo | PERMITIR |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Prioridad|Origen|Puertos de origen| Destino | Puertos de destino | Protocolo | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Todo | Denegar |

En las columnas **Origen** y **Destino**, *VirtualNetwork*, *AzureLoadBalancer* e *Internet* son [etiquetas de servicios](#tags), en lugar de direcciones IP. En la columna de protocolos, **Todos** abarca TCP, UDP e ICMP. Cuando se crea una regla, puede especificar TCP, UDP o Todos, pero no puede especificar ICMP de forma independiente. Por lo tanto, si la regla requiere ICMP, deberá seleccionar la opción *Todos* para el protocolo. *0.0.0.0/0* en las columnas **Origen** y **Destino** representa todas las direcciones.
 
Las reglas predeterminadas no se pueden quitar, pero puede reemplazarlas con reglas de prioridad más alta.

## <a name="service-tags"></a>Etiquetas de servicio

 Una etiqueta de servicio representa un grupo de prefijos de direcciones IP que ayudan a reducir la complejidad de la creación de reglas de seguridad. No puede crear su propia etiqueta de servicio, ni especificar qué direcciones IP se incluyen dentro de una etiqueta. Microsoft administra los prefijos de direcciones que incluye la etiqueta de servicio y actualiza automáticamente esta a medida que las direcciones cambian. Puede utilizar etiquetas de servicio en lugar de direcciones IP específicas al crear reglas de seguridad. Las siguientes etiquetas de servicio están disponibles para su uso en la definición de reglas de seguridad. Sus nombres pueden variar ligeramente según el [modelo de implementación de Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

* **VirtualNetwork** (Resource Manager) (**VIRTUAL_NETWORK** para el modelo clásico): esta etiqueta incluye el espacio de direcciones de red virtual (todos los intervalos CIDR definidos para la red virtual), todos los espacios de direcciones locales conectados y las redes virtuales [del mismo nivel](virtual-network-peering-overview.md) o redes virtuales conectadas a una [puerta de enlace de red virtual](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** para el modelo clásico): esta etiqueta denota el equilibrador de carga de la infraestructura de Azure. La etiqueta se traducirá en una [dirección IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653) donde se originan los sondeos de mantenimiento de Azure. Si no usa el equilibrador de carga de Azure, puede reemplazar esta regla.
* **Internet** (Resource Manager) (**INTERNET** para el modelo clásico): esta etiqueta denota el espacio de direcciones IP que se encuentra fuera de la red virtual y es accesible mediante la red pública de Internet. El intervalo de direcciones incluye el [espacio de direcciones IP públicas propiedad de Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* **AzureTrafficManager** (solo para Resource Manager): esta etiqueta denota el espacio de direcciones IP de las IP de sondeo de Azure Traffic Manager. En [Preguntas más frecuentes (P+F) sobre Traffic Manager](https://docs.microsoft.com/en-us/azure/traffic-manager/traffic-manager-faqs), puede encontrar más información acerca de las IP de sondeo de Azure Traffic Manager.
* **Storage** (solo para Resource Manager): esta etiqueta denota el espacio de direcciones IP del servicio Azure Storage. Si especifica *Storage* como valor, el tráfico al almacenamiento se permite o se deniega. Si solo desea permitir el acceso al almacenamiento de una determinada [región](https://azure.microsoft.com/regions), puede especificarla. Por ejemplo, si desea permitir el acceso a Azure Storage solo en la región este de EE. UU., puede especificar *Storage.EastUS* como etiqueta de servicio. La etiqueta representa el servicio, no instancias específicas del mismo. Por ejemplo, la etiqueta representa el servicio Azure Storage, pero no una cuenta de específica de este.
* **Sql** (solo para Resource Manager): esta etiqueta denota los prefijos de direcciones de los servicios Azure SQL Database y Azure SQL Data Warehouse. Si especifica *Sql* como valor, el tráfico a Sql se permite o se deniega. Si solo desea permitir el acceso a SQL en una [región](https://azure.microsoft.com/regions) determinada, puede especificarla. Por ejemplo, si desea permitir el acceso a Azure SQL Database solo en la región este de EE. UU., puede especificar *Sql.EastUS* como etiqueta de servicio. La etiqueta representa el servicio, no instancias específicas del mismo. Por ejemplo, la etiqueta representa el servicio Azure SQL Database, pero no una cuenta de un servidor o base de datos SQL específicos.

> [!NOTE]
> Si implementa un [punto de conexión del servicio de red virtual](virtual-network-service-endpoints-overview.md) para un servicio, como Azure Storage o Azure SQL Database, Azure agrega una ruta a una subred de red virtual para el servicio. Los prefijos de dirección de la ruta son los mismos prefijos de dirección, o intervalos CIDR, que la etiqueta de servicio correspondiente.

## <a name="application-security-groups"></a>Grupos de seguridad de aplicaciones

Los grupos de seguridad de aplicaciones le permiten configurar la seguridad de red como una extensión natural de la estructura de una aplicación, lo que le permite agrupar máquinas virtuales y directivas de seguridad de red basadas en esos grupos. Esta característica le permite reutilizar la directiva de seguridad a escala sin mantenimiento manual de direcciones IP explícitas. La plataforma controla la complejidad de las direcciones IP explícitas y de varios conjuntos de reglas, lo que le permite centrarse en su lógica de negocios.

Puede especificar un grupo de seguridad de aplicaciones como origen y destino en una regla de seguridad. Una vez que se define la directiva de seguridad, puede crear máquinas virtuales y asignar las interfaces de red de la máquina virtual a un grupo de seguridad de aplicaciones. La directiva se aplica en función de la pertenencia al grupo de seguridad de aplicaciones de cada interfaz de red de una máquina virtual. En el ejemplo siguiente se muestra cómo puede usar un grupo de seguridad de aplicaciones para todos los servidores web de la suscripción:

1. Cree un grupo de seguridad de aplicaciones denominado *WebServers*.
2. Cree un grupo de seguridad de red denominado *MyNSG*.
3. Cree una regla de seguridad de entrada en el grupo de seguridad de red, especificando la etiqueta de servicio *Internet* como dirección de origen y el grupo de seguridad de aplicaciones *WebServers* como la dirección de destino, y permita los puertos 80 y 443.
4. Implemente una máquina virtual en la que se ejecute una aplicación de servidor web. Especifique que la interfaz de red de la máquina virtual es miembro del grupo de seguridad de aplicaciones denominado *WebServers*. A continuación, se permiten los puertos 80 y 443 para la máquina virtual. Los puertos también se permiten para todos los servidores web posteriores que cree y que convierta en miembros del grupo de seguridad de aplicaciones *WebServers*. 

Si crea otras reglas, especificando otros grupos de seguridad de aplicaciones como destino, esas reglas no se aplicarán a los servidores web del ejemplo anterior. Las reglas que especifican un grupo de seguridad de aplicaciones solo se aplican a las interfaces de red que son miembros de este grupo. Los grupos de seguridad de aplicaciones, junto con las reglas de seguridad aumentada y las etiquetas de servicio, le permiten crear un número mínimo de grupos de seguridad de red para administrar la seguridad de red en la suscripción.
 
Para obtener información acerca de los límites a la hora de crear grupos de seguridad de aplicaciones y especificarlos en las reglas de seguridad, consulte los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Los grupos de seguridad de aplicaciones están disponibles en la versión preliminar. Las características de la versión preliminar no tienen el mismo nivel de disponibilidad y confiabilidad que las características de la versión general. Para poder usar los grupos de seguridad de aplicaciones, primero es preciso registrarse, para lo que hay que seguir los pasos 1 a 5 de las secciones correspondientes a Azure o PowerShell de [Creación de un grupo de seguridad de red con grupos de seguridad de aplicaciones](create-network-security-group-preview.md). Los grupos de seguridad de aplicaciones presentan las siguientes restricciones:

-   Todas las interfaces de red dentro de un grupo de seguridad de aplicaciones deben existir en la misma red virtual. No se pueden agregar interfaces de red de distintas redes virtuales al mismo grupo de seguridad de aplicaciones. La red virtual en la que se encuentra la primera interfaz de red asignada al grupo de seguridad de aplicaciones define la red virtual en la que deben existir todas las subsiguientes interfaces de red asignadas.
- Si especifica grupos de seguridad de aplicaciones como origen y destino de una regla de seguridad, las interfaces de red de ambos grupos de seguridad de aplicaciones deben existir en la misma red virtual. Por ejemplo, si ASG1 contiene interfaces de red de VNet1 y ASG2 contiene interfaces de red de VNet2, no puede asignar ASG1 como origen y ASG2 como destino en una regla; todas las interfaces de red deben existir en VNet1.

## <a name="azure-platform-considerations"></a>Consideraciones de la plataforma Azure

- **IP virtual del nodo de host:** los servicios de infraestructura básica, como DHCP, DNS y supervisión del estado se proporcionan a través de las direcciones IP de host virtualizadas 168.63.129.16 y 169.254.169.254. Estas direcciones IP públicas pertenecen a Microsoft y son la únicas direcciones IP virtualizadas que se usarán en todas las regiones con este fin. Las direcciones se asignan a la dirección IP física del equipo del servidor (nodo de host) que hospeda la máquina virtual. El nodo de host actúa como la retransmisión DHCP, la resolución recursiva de DNS y el origen de sonda del sondeo de mantenimiento del equilibrador de carga y el sondeo de mantenimiento del equipo. La comunicación con estas direcciones IP no constituye un ataque. Si bloquea el tráfico hacia estas direcciones IP o el procedente de ellas, puede que una máquina virtual no funcione correctamente.
- **Licencias (Servicio de administración de claves):** las imágenes de Windows que se ejecutan en máquinas virtuales deben tener licencia. Para garantizar que se usen licencias, se envía una solicitud a los servidores host del Servicio de administración de claves que administran dichas consultas. La solicitud de salida se realiza a través del puerto 1688. Para implementaciones que usan la configuración de la [ruta predeterminada 0.0.0.0/0](virtual-networks-udr-overview.md#default-route), se deshabilitará esta regla de plataforma.
- **Máquinas virtuales en grupos de carga equilibrada**: el puerto y el intervalo de direcciones de origen aplicados proceden del equipo de origen no del equilibrador de carga. El puerto y el intervalo de direcciones de destino son los del equipo de destino, no los del equilibrador de carga.
- **Instancias de servicio de Azure**: instancias de varios servicios de Azure como HDInsight, conjuntos de escalado de máquinas virtuales y entornos del servicio de aplicaciones implementados en subredes de la red virtual. Para ver una lista completa de los servicios que puede implementar en redes virtuales, consulte el artículo sobre la [Red virtual para los servicios de Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Asegúrese de que conoce los requisitos de puertos de cada servicio antes de aplicar un grupo de seguridad de red a la subred en la que se implementa el recurso. Si deniega los puertos que el servicio requiere, este no funcionará correctamente.
- **Envío de correo electrónico saliente**: Microsoft recomienda usar servicios de retransmisión SMTP autenticados (que normalmente se conectan a través del puerto TCP 587, pero a menudo también de otros) para enviar correo electrónico Azure Virtual Machines. Los servicios de retransmisión SMTP se especializan en la reputación del remitente, con el fin de minimizar la posibilidad de que proveedores de correo electrónico de terceros rechacen los mensajes. Estos servicios de retransmisión de SMTP incluyen Exchange Online Protection y SendGrid, pero no se limitan a ellos. El uso de servicios de retransmisión de SMTP no tiene ninguna restricción en Azure, independientemente del tipo de suscripción. 

  Si ha creado la suscripción a Azure antes del 15 de noviembre de 2017, además de poder usar los servicios de retransmisión de SMTP, puede enviar el correo electrónico directamente a través del puerto TCP 25. Si ha creado la suscripción después del 15 de noviembre de 2017, es posible que no pueda enviar correo electrónico directamente a través del puerto 25. El comportamiento de la comunicación saliente a través del puerto 25 depende del tipo de suscripción que tenga, como se indica a continuación:

     - **Contrato Enterprise**: se permite la comunicación saliente a través del puerto 25. Puede enviar el correo electrónico saliente directamente desde las máquinas virtuales a los proveedores de correo electrónico externos, sin las restricciones de la plataforma Azure. 
     - **Pago por uso:** la comunicación saliente a través del puerto 25 está bloqueada en todos los recursos. Si necesita enviar correo electrónico desde una máquina virtual directamente a proveedores de correo electrónico externos (que no usan retransmisión SMTP autenticada), puede realizar una solicitud para quitar la restricción. Las solicitudes se revisan y aprueban a discreción de Microsoft y solo se conceden una vez que se han realizado las comprobaciones contra fraudes. Para realizar una solicitud, abra un caso de soporte técnico con el tipo de problema *Técnico*, *Conectividad de red virtual*, *No se puede enviar correo electrónico (SMTP/puerto 25)*. En su caso de soporte técnico, indique los motivos por los que su suscripción tiene que enviar correo electrónico directamente a los proveedores de correo electrónico, en lugar de pasar por una retransmisión SMTP autenticada. Si la suscripción está exenta, las únicas máquinas virtuales que pueden establecer comunicación saliente a través del puerto 25 son las creadas después de la fecha de exención.
     - **Proveedor de servicios en la nube (CSP), MSDN, Pase para Azure, Azure bajo licencia Open, Education, BizSpark y evaluación gratuita**: la comunicación saliente a través del puerto 25 se bloquea en todos los recursos. No se pueden realizar solicitudes para quitar la restricción, ya que no se conceden solicitudes. Si debe enviar correo electrónico desde una máquina virtual, debe usar un servicio de retransmisión SMTP.

  Si Azure le permite enviar correo electrónico a través del puerto 25, Microsoft no garantiza de proveedores de correo electrónico vayan a aceptar correo electrónico entrante procedente de la máquina virtual. Si un proveedor concreto rechaza el correo de la máquina virtual, tendrá que trabajar directamente con él para resolver los problemas de entrega de mensajes o de filtrado de correo no deseado, o bien utilizar un servicio de retransmisión de SMTP autenticado. 


## <a name="next-steps"></a>pasos siguientes

* Completar el tutorial [Crear un grupo de seguridad de red](virtual-networks-create-nsg-arm-pportal.md)
* Completar el tutorial [Creación de un grupo de seguridad de red con grupos de seguridad de aplicaciones](create-network-security-group-preview.md)
