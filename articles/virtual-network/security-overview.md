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
ms.openlocfilehash: 98559cbb0acab91c4b2c30c6d0129e955eef85f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
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
|Nombre|Un nombre único dentro del grupo de seguridad de red.|
|Prioridad | Un número entre 100 y 4096. Las reglas se procesan en orden de prioridad. Se procesan primero las reglas con los números más bajos ya que estos tienen más prioridad. Si el tráfico coincide con una regla, se detiene el procesamiento. Como resultado, las reglas con menor prioridad (números más altos) que tengan los mismos atributos que las reglas con una prioridad mayor no se procesarán.|
|Origen o destino| Todas, o una dirección IP individual, un bloque CIDR (10.0.0.0/24, por ejemplo), una etiqueta de servicio o un grupo de seguridad de aplicaciones. Obtenga más información sobre [etiquetas de servicio](#service-tags) y [grupos de seguridad de aplicaciones](#application-security-groups). La especificación de un intervalo, una etiqueta de servicio o grupo de seguridad de aplicaciones le permite crear menos reglas de seguridad. La posibilidad de especificar varias direcciones IP individuales o intervalos de estas (no puede especificar varias etiquetas de servicio ni grupos de aplicaciones) en una regla se encuentra en la fase de versión preliminar y se conoce como reglas de seguridad aumentada. Las reglas de seguridad aumentada solo se pueden generar en los grupos de seguridad de red creados mediante el modelo de implementación de Resource Manager. No puede especificar varias direcciones IP ni intervalos de ellas en grupos de seguridad de red creados mediante el modelo de implementación clásica.|
|Protocol     | TCP, UDP o Cualquiera, opción que incluye TCP, UDP e ICMP. No se puede especificar ICMP de forma independiente, por lo que si necesita usarlo, deberá utilizar la opción Cualquiera. |
|Dirección| Si la regla se aplica al tráfico entrante o al saliente.|
|Intervalo de puertos     |Puede especificar un puerto individual o un intervalo de puertos. Por ejemplo, puede especificar 80 o 10000-10005. La especificación de intervalos le permite crear menos reglas de seguridad. La posibilidad de especificar varios puertos individuales o intervalos de ellos en una regla se encuentra en la fase de versión preliminar y se conoce como reglas de seguridad aumentada. Antes de usar las reglas de seguridad aumentada, lea la sección [Características en versión preliminar](#preview-features) para obtener información importante. Las reglas de seguridad aumentada solo se pueden generar en los grupos de seguridad de red creados mediante el modelo de implementación de Resource Manager. No puede especificar varios puertos ni intervalos de ellos en la misma regla de seguridad de los grupos de seguridad de red creados mediante el modelo de implementación clásica.   |
|Acción     | Permitir o denegar        |

**Consideraciones**

- **IP virtual del nodo de host:** los servicios de infraestructura básica, como DHCP, DNS y supervisión del estado se proporcionan a través de las direcciones IP de host virtualizadas 168.63.129.16 y 169.254.169.254. Estas direcciones IP públicas pertenecen a Microsoft y son la únicas direcciones IP virtualizadas que se usarán en todas las regiones con este fin. Las direcciones se asignan a la dirección IP física del equipo del servidor (nodo de host) que hospeda la máquina virtual. El nodo de host actúa como la retransmisión DHCP, la resolución recursiva de DNS y el origen de sonda del sondeo de mantenimiento del equilibrador de carga y el sondeo de mantenimiento del equipo. La comunicación con estas direcciones IP no constituye un ataque. Si bloquea el tráfico hacia estas direcciones IP o el procedente de ellas, puede que una máquina virtual no funcione correctamente.
- **Licencias (Servicio de administración de claves):** las imágenes de Windows que se ejecutan en máquinas virtuales deben tener licencia. Para garantizar que se usen licencias, se envía una solicitud a los servidores host del Servicio de administración de claves que administran dichas consultas. La solicitud de salida se realiza a través del puerto 1688.
- **Máquinas virtuales en grupos de carga equilibrada**: el puerto y el intervalo de direcciones de origen aplicados proceden del equipo de origen no del equilibrador de carga. El puerto y el intervalo de direcciones de destino son los del equipo de destino, no los del equilibrador de carga.
- **Instancias de servicio de Azure**: instancias de varios servicios de Azure como HDInsight, conjuntos de escalado de máquinas virtuales y entornos del servicio de aplicaciones implementados en subredes de la red virtual. Asegúrese de que conoce los requisitos de puertos de cada servicio antes de aplicar un grupo de seguridad de red a la subred en la que se implementa el recurso. Si deniega los puertos que el servicio requiere, este no funcionará correctamente. 

Las reglas de seguridad tienen un estado. Por ejemplo, si especifica una regla de seguridad de salida para cualquier dirección a través del puerto 80, no será necesario especificar una regla de seguridad de entrada para la respuesta al tráfico saliente. Solo debe especificar una regla de seguridad de entrada si la comunicación se inicia de forma externa. Lo contrario también es cierto. Si se permite el tráfico entrante a través de un puerto, no es necesario especificar una regla de seguridad de salida para responder al tráfico a través del puerto. Para obtener información acerca de los límites a la hora de crear reglas de seguridad, consulte los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Reglas de seguridad aumentada

Las reglas aumentadas permiten simplificar la definición de seguridad para las redes virtuales, lo que le permitirá definir directivas de seguridad de red más grandes y complejas, con menos reglas. Puede combinar varios puertos, varias direcciones IP explícitas, etiquetas de servicio y grupos de seguridad de aplicaciones en una única regla de seguridad de fácil comprensión. Use reglas aumentadas en los campos de origen, destino y puerto de una regla. Al crear una regla, puede especificar varias direcciones IP, intervalos de CIDR y puertos explícitos. Para simplificar el mantenimiento de la definición de la regla de seguridad, combine las reglas de seguridad aumentada con etiquetas de servicio o grupos de seguridad de aplicaciones. 

Las reglas de seguridad aumentada están disponibles en versión preliminar. Para obtener información acerca de los límites a la hora de crear reglas de seguridad aumentada, consulte los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Las características de la versión preliminar no tienen el mismo nivel de disponibilidad y confiabilidad que las características de la versión general. Las características solo están disponibles en las siguientes regiones: Este de EE. UU., Oeste de EE. UU., Oeste de EE. UU. 2, oeste del Centro de EE. UU., Este de Australia, Sudeste de Australia y Sur de Reino Unido.

## <a name="default-security-rules"></a>reglas de seguridad predeterminadas

Si un grupo de seguridad de red no está asociado a una subred o interfaz de red, se permite todo el tráfico entrante hacia la subred o interfaz de red y todo el tráfico saliente procedente de ambos. En cuanto se crea un grupo de seguridad de red, Azure crea las siguientes reglas predeterminadas en el grupo de seguridad de red:

### <a name="inbound"></a>Entrada

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Prioridad|Origen|Puertos de origen|Destino|Puertos de destino|Protocol|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Todo|PERMITIR|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Prioridad|Origen|Puertos de origen|Destino|Puertos de destino|Protocol|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Todo|PERMITIR|

#### <a name="denyallinbound"></a>DenyAllInbound

|Prioridad|Origen|Puertos de origen|Destino|Puertos de destino|Protocol|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Todo|DENEGAR|

### <a name="outbound"></a>Salida

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Prioridad|Origen|Puertos de origen| Destino | Puertos de destino | Protocol | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Todo | PERMITIR |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Prioridad|Origen|Puertos de origen| Destino | Puertos de destino | Protocol | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Todo | PERMITIR |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Prioridad|Origen|Puertos de origen| Destino | Puertos de destino | Protocol | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Todo | DENEGAR |

En las columnas **Origen** y **Destino**, *VirtualNetwork*, *AzureLoadBalancer* e *Internet* son [etiquetas de servicios](#tags), en lugar de direcciones IP. En la columna de protocolos, **Todos** abarca TCP, UDP e ICMP. Cuando se crea una regla, puede especificar TCP, UDP o Todos, pero no puede especificar ICMP de forma independiente. Por lo tanto, si la regla requiere ICMP, deberá seleccionar la opción *Todos* para el protocolo. *0.0.0.0/0* en las columnas **Origen** y **Destino** representa todas las direcciones.
 
Las reglas predeterminadas no se pueden quitar, pero puede reemplazarlas con reglas de prioridad más alta.

## <a name="service-tags"></a>Etiquetas de servicio

 Una etiqueta de servicio representa un grupo de prefijos de direcciones IP que ayudan a reducir la complejidad de la creación de reglas de seguridad. No puede crear su propia etiqueta de servicio, ni especificar qué direcciones IP se incluyen dentro de una etiqueta. Microsoft administra los prefijos de direcciones que incluye la etiqueta de servicio y actualiza automáticamente esta a medida que las direcciones cambian. Puede utilizar etiquetas de servicio en lugar de direcciones IP específicas al crear reglas de seguridad. Las siguientes etiquetas de servicio están disponibles para su uso en la definición de reglas de seguridad. Sus nombres pueden variar ligeramente según el [modelo de implementación de Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

* **VirtualNetwork** (*Resource Manager) (**VIRTUAL_NETWORK** para el modelo clásico): esta etiqueta incluye el espacio de direcciones de red virtual (todos los intervalos CIDR definidos para la red virtual), todos los espacios de direcciones locales conectados y las [](virtual-network-peering-overview.md)redes virtuales o redes virtuales conectadas a una [puerta de enlace de red virtual](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) del mismo nivel.
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** para el modelo clásico): esta etiqueta denota el equilibrador de carga de la infraestructura de Azure. La etiqueta se traducirá en una [dirección IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653) donde se originan los sondeos de mantenimiento de Azure. Si no usa el equilibrador de carga de Azure, puede reemplazar esta regla.
* **Internet** (Resource Manager) (**INTERNET** para el modelo clásico): esta etiqueta denota el espacio de direcciones IP que se encuentra fuera de la red virtual y es accesible mediante la red pública de Internet. El intervalo de direcciones incluye el [espacio de direcciones IP públicas propiedad de Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* **AzureTrafficManager** (solo para Resource Manager): esta etiqueta denota el espacio de direcciones IP del servicio Azure Traffic Manager.
* **Storage** (solo para Resource Manager): esta etiqueta denota el espacio de direcciones IP del servicio Azure Storage. Si especifica *Storage* como valor, el tráfico al almacenamiento se permite o se deniega. Si solo desea permitir el acceso al almacenamiento de una determinada [región](https://azure.microsoft.com/regions), puede especificarla. Por ejemplo, si desea permitir el acceso a Azure Storage solo en la región este de EE. UU., puede especificar *Storage.EastUS* como etiqueta de servicio. Etiquetas de servicio regionales adicionales disponibles: Storage.AustraliaEast, Storage.AustraliaSoutheast, Storage.EastUS, Storage.UKSouth, Storage.WestCentralUS, Storage.WestUS y Storage.WestUS2. La etiqueta representa el servicio, no instancias específicas del mismo. Por ejemplo, la etiqueta representa el servicio Azure Storage, pero no una cuenta de específica de este.
* **Sql** (solo para Resource Manager): esta etiqueta denota los prefijos de direcciones de los servicios Azure SQL Database y Azure SQL Data Warehouse. Solo se pueden especificar regiones específicas para esta etiqueta de servicio. Por ejemplo, si desea permitir el acceso a Azure SQL Database solo en la región este de EE. UU., puede especificar *Sql.EastUS* como etiqueta de servicio. No se puede especificar Sql solo para todas las regiones de Azure, debe especificar las regiones individualmente. Otras etiquetas de servicios regionales disponibles: Sql.AustraliaEast, Sql.AustraliaSoutheast, Sql.EastUS, Sql.UKSouth, Sql.WestCentralUS, Sql.WestUS y Sql.WestUS2. La etiqueta representa el servicio, no instancias específicas del mismo. Por ejemplo, la etiqueta representa el servicio Azure SQL Database, pero no una cuenta de específica de este.

> [!WARNING]
> Las etiquetas de servicio AzureTrafficManager, Storage y Sql están disponibles en versión preliminar. Las características de la versión preliminar no tienen el mismo nivel de disponibilidad y confiabilidad que las características de la versión general. Las etiquetas de servicio solo están disponibles en las siguientes regiones: Este de EE. UU., Oeste de EE. UU., Oeste de EE. UU. 2, oeste del Centro de EE. UU., Este de Australia, Sudeste de Australia y Sur de Reino Unido.

> [!NOTE]
> Si implementa un punto de conexión del servicio de red virtual para un servicio, como Azure Storage o Azure SQL Database, Azure agregará una ruta a una subred de red virtual para el servicio. Los prefijos de dirección de la ruta son los mismos prefijos de dirección, o intervalos CIDR, que la etiqueta de servicio correspondiente.

## <a name="application-security-groups"></a>Grupos de seguridad de aplicaciones

Los grupos de seguridad de aplicaciones le permiten configurar la seguridad de red como una extensión natural de la estructura de una aplicación, lo que le permite agrupar máquinas virtuales y directivas de seguridad de red basadas en esos grupos. Esta característica le permite reutilizar la directiva de seguridad a escala sin mantenimiento manual de direcciones IP explícitas. La plataforma controla la complejidad de las direcciones IP explícitas y de varios conjuntos de reglas, lo que le permite centrarse en su lógica de negocios.

Puede especificar un grupo de seguridad de aplicaciones como origen y destino en una regla de seguridad. Una vez que se define la directiva de seguridad, puede crear máquinas virtuales y asignar las interfaces de red de la máquina virtual a un grupo de seguridad de aplicaciones. La directiva se aplica en función de la pertenencia al grupo de seguridad de aplicaciones de cada interfaz de red de una máquina virtual. En el ejemplo siguiente se muestra cómo puede usar un grupo de seguridad de aplicaciones para todos los servidores web de la suscripción:

1. Cree un grupo de seguridad de aplicaciones denominado *WebServers*.
2. Cree un grupo de seguridad de red denominado *MyNSG*.
3. Cree una regla de seguridad de entrada en el grupo de seguridad de red, especificando la etiqueta de servicio *Internet* como dirección de origen y el grupo de seguridad de aplicaciones *WebServers* como la dirección de destino, y permita los puertos 80 y 443.
4. Implemente una máquina virtual en la que se ejecute una aplicación de servidor web. Especifique que la interfaz de red de la máquina virtual es miembro del grupo de seguridad de aplicaciones denominado *WebServers*. A continuación, se permiten los puertos 80 y 443 para la máquina virtual. Los puertos también se permiten para todos los servidores web posteriores que cree y que convierta en miembros del grupo de seguridad de aplicaciones *WebServers*. 

Si crea otras reglas, especificando otros grupos de seguridad de aplicaciones como destino, esas reglas no se aplicarán a los servidores web del ejemplo anterior. Las reglas que especifican un grupo de seguridad de aplicaciones solo se aplican a las interfaces de red que son miembros de este grupo. Los grupos de seguridad de aplicaciones, junto con las reglas de seguridad aumentada y las etiquetas de servicio, le permiten crear un número mínimo de grupos de seguridad de red para administrar la seguridad de red en la suscripción.
 
Para obtener información acerca de los límites a la hora de crear grupos de seguridad de aplicaciones y especificarlos en las reglas de seguridad, consulte los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

Los grupos de seguridad de aplicaciones están disponibles en la versión preliminar. Para poder usar los grupos de seguridad de aplicaciones, antes es preciso registrarse, para lo que hay que seguir los pasos 1 a 5 de [Creación de un grupo de seguridad de red con grupos de seguridad de aplicaciones](create-network-security-group-preview.md#powershell) y leer la sección [Características en la versión preliminar](#preview-features), donde encontrará información importante. Durante la versión preliminar, los grupos de seguridad de aplicaciones se limitan al ámbito de la red virtual. No se aplican las redes virtuales emparejadas con referencias cruzadas a los grupos de seguridad de aplicaciones de un grupo de seguridad de red. 

Las características de la versión preliminar no tienen el mismo nivel de disponibilidad y confiabilidad que las características de la versión general. Antes de usar grupos de seguridad de aplicaciones, primero debe registrarse. Las características están disponibles solamente en esta región: Oeste del centro de EE. UU.

## <a name="next-steps"></a>Pasos siguientes

* Completar el tutorial [Crear un grupo de seguridad de red](virtual-networks-create-nsg-arm-pportal.md)
* Completar el tutorial [Creación de un grupo de seguridad de red con grupos de seguridad de aplicaciones](create-network-security-group-preview.md)
