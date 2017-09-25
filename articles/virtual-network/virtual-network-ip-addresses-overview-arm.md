---
title: Tipos de direcciones IP en Azure | Microsoft Docs
description: "Información acerca de direcciones IP públicas y privadas en Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager
ms.assetid: 610b911c-f358-4cfe-ad82-8b61b87c3b7e
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 144f4ea213b8ed0a3530495e185f489155c474c9
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>Tipos de direcciones IP y métodos de asignación en Azure
Puede asignar direcciones IP a los recursos de Azure para que se comuniquen con otros recursos de Azure, la red local e Internet. Hay dos tipos de direcciones IP que puede usar en Azure:

* **Direcciones públicas y privadas**: las direcciones IP públicas se usan para la comunicación con Internet, incluidos los servicios de acceso público de Azure.
* **Direcciones IP privadas**: las direcciones IP privadas se usa para la comunicación dentro de una red virtual (VNet) de Azure y en la red local cuando se usa una puerta de enlace de VPN o un circuito ExpressRoute para ampliar la red a Azure.

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../resource-manager-deployment-model.md).  En este artículo se describe el uso del modelo de implementación de Resource Manager, recomendado por Microsoft para la mayoría de las nuevas implementaciones en lugar del [modelo de implementación clásica](virtual-network-ip-addresses-overview-classic.md).
> 

Si está familiarizado con el modelo de implementación clásica, revise las [diferencias en el direccionamiento IP entre la implementación clásica y Resource Manager](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Direcciones IP públicas
Las direcciones IP públicas permiten que los recursos de Azure se comuniquen con Internet y servicios de acceso público de Azure como [Azure Redis Cache](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [SQL Database](../sql-database/sql-database-technical-overview.md) y [Azure Storage](../storage/common/storage-introduction.md).

En el Administrador de recursos de Azure, una dirección [IP pública](resource-groups-networking.md#public-ip-address) es un recurso que cuenta con propiedades específicas. Puede asociar un recurso de dirección IP pública con cualquiera de los siguientes recursos:

* Máquinas virtuales (VM)
* Equilibradores de carga accesibles desde Internet
* Puertas de enlace de VPN
* Puertas de enlace de aplicaciones

### <a name="allocation-method"></a>Método de asignación
Existen dos métodos en los que se asigna una dirección IP a un recurso de IP *pública*: *dinámico* o *estático*. El predeterminado es el *dinámico*, en el que **no** se asigna ninguna dirección IP en el momento de su creación. En su lugar, se asigna la dirección IP pública cuando se inicia (o crea) el recurso asociado (como una máquina virtual o un equilibrador de carga). La dirección IP se libera cuando se detiene (o elimina) el recurso, lo que hace que la dirección IP cambie en estas situaciones.

Para asegurarse de que la dirección IP para el recurso asociado siga siendo la misma, puede establecer explícitamente el método de asignación en *estático*. En este caso, la dirección IP se asigna de inmediato. Solo se libera cuando se elimina el recurso o se cambia su método de asignación a *dinámico*.

> [!NOTE]
> Incluso cuando se establece el método de asignación en *estático*, no se puede especificar la dirección IP real asignada al *recurso de IP pública*. En su lugar, se asigna desde un grupo de direcciones IP disponibles en la ubicación de Azure cuando se crea el recurso.
>

Las direcciones IP públicas se suelen usar en los escenarios siguientes:

* Los usuarios finales necesitan actualizar las reglas de firewall para comunicarse con los recursos de Azure.
* La resolución de nombres DNS, en la que un cambio de dirección IP requeriría actualizar los registros D.
* Los recursos de Azure se comunican con otras aplicaciones o servicios que utilizan un modelo de seguridad basado en dirección IP.
* Usa certificados SSL vinculados a una dirección IP.

> [!NOTE]
> La lista de intervalos IP desde la que se asignan direcciones IP públicas (dinámicas o estáticas) a recursos de Azure está publicada en [Azure Datacenter IP ranges](https://www.microsoft.com/download/details.aspx?id=41653).
>

### <a name="dns-hostname-resolution"></a>Resolución de nombres de host DNS
Puede especificar una etiqueta de nombre de dominio DNS para un recurso de IP pública, lo que crea una asignación para *etiquetadenombrededominio*.*ubicación.cloudapp.azure.com* en la dirección IP pública de los servidores DNS que administra Azure. Por ejemplo, si crea un recurso de IP pública con **contoso** como *etiquetaDeNombreDeDominio* en la *ubicación* **Oeste de EE. UU.** de Azure, el nombre de dominio completo (FQDN) **contoso.westus.cloudapp.azure.com** se resolverá en la dirección IP pública del recurso. Puede usar este FQDN para crear un registro CNAME de dominio personalizado que apunte a la dirección IP pública en Azure.

> [!IMPORTANT]
> Cada etiqueta de nombre de dominio que se cree debe ser única dentro de su ubicación de Azure.  
>

### <a name="virtual-machines"></a>Máquinas virtuales
Puede asociar una dirección IP pública con una máquina virtual [Windows](../virtual-machines/windows/overview.md) o [Linux](../virtual-machines/virtual-machines-linux-about.md) mediante la asignación a la **interfaz de red**. En el caso de una máquina virtual con varias interfaces de red, puede asignarla solo a la interfaz de red *principal*. Puede asignar una dirección IP pública estática o dinámica a una máquina virtual.

### <a name="internet-facing-load-balancers"></a>Equilibradores de carga accesibles desde Internet
Puede asociar una dirección IP pública con un [Azure Load Balancer](../load-balancer/load-balancer-overview.md)asignándola a la configuración del **front-end** del equilibrador de carga. Esta dirección IP pública actúa como dirección IP virtual (VIP) de carga equilibrada. Puede asignar una dirección IP pública estática o dinámica al front-end de un equilibrador de carga. También le puede asignar varias direcciones IP públicas a un front-end del equilibrador de carga, lo que hace posibles aquellos escenarios con [varias VIP](../load-balancer/load-balancer-multivip.md) , como un entorno de varios inquilinos con sitios web basados en SSL.

### <a name="vpn-gateways"></a>Puertas de enlace de VPN
[puerta de enlace de VPN de Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md) para conectar una red virtual de Azure (VNet) a otras redes virtuales de Azure o a la red local. Debe asignar una dirección IP pública a su **configuración de IP** para habilitar la comunicación con la red remota. Actualmente, solo puede asignar una dirección IP pública *dinámica* a una puerta de enlace de VPN.

### <a name="application-gateways"></a>Puertas de enlace de aplicaciones
Puede asociar una dirección IP pública con una [puerta de enlace de aplicaciones](../application-gateway/application-gateway-introduction.md)de Azure asignándola a la configuración del **front-end** de la puerta de enlace. Esta dirección IP pública actúa como VIP de carga equilibrada. Actualmente, solo se puede asignar un dirección IP pública *dinámica* a una configuración del front-end de la puerta de enlace de aplicaciones.

### <a name="at-a-glance"></a>De un vistazo
La siguiente tabla muestra la propiedad específica a través de la cual una dirección IP pública se puede asociar a un recurso de nivel superior y los métodos de asignación posibles (dinámicos o estáticos) que se pueden usar.

| Recurso de nivel superior | Asociación de dirección IP | dinámico | estático |
| --- | --- | --- | --- |
| Máquina virtual |interfaz de red |Sí |Sí |
| Equilibrador de carga |Configuración de front-end |Sí |Sí |
| Puerta de enlace de VPN |Configuración de dirección IP de puerta de enlace |Sí |No |
| puerta de enlace de aplicaciones |Configuración de front-end |Sí |No |

## <a name="private-ip-addresses"></a>Direcciones IP privadas
Las direcciones IP privadas permiten que los recursos de Azure se comuniquen con otros recursos en una [red virtual](virtual-networks-overview.md) , o en la red local a través de una puerta de enlace de VPN o un circuito ExpressRoute, sin usar una dirección IP accesible desde Internet.

En el modelo de implementación de Azure Resource Manager, una dirección IP privada se asocia a los siguientes tipos de recursos de Azure:

* Máquinas virtuales
* Equilibradores de carga internos (ILB)
* Puertas de enlace de aplicaciones

### <a name="allocation-method"></a>Método de asignación
Se asigna una dirección IP privada del intervalo de direcciones de la subred a la que está conectado el recurso. El intervalo de direcciones de la propia subred forma parte del intervalo de direcciones de la red virtual.

Hay dos métodos de asignación de direcciones IP privadas: *dinámico* o *estático*. El predeterminado es el *dinámico*, en el que la dirección IP se asigna automáticamente desde la subred del recurso (mediante DHCP). Esta dirección IP puede cambiar cando detenga e inicie el recurso.

Puede establecer el método de asignación en *estático* para asegurarse de que la dirección IP siga siendo igual. En este caso, también debe proporcionar una dirección IP válida que forme parte de la subred del recurso.

Las direcciones IP privadas estáticas se suelen usar para:

* Máquinas virtuales que actúan como controladores de dominio o servidores DNS.
* Recursos que requieren reglas de firewall que usan direcciones IP.
* Recursos a los que se accede desde otras aplicaciones o recursos a través de una dirección IP.

### <a name="virtual-machines"></a>Máquinas virtuales
Se asigna una dirección IP privada a la **interfaz de red** de una máquina virtual [Windows](../virtual-machines/windows/overview.md) o [Linux](../virtual-machines/virtual-machines-linux-about.md). En una máquina virtual de interfaz de varias redes, se asigna una dirección IP privada a cada una. Puede especificar el método de asignación como estático o dinámico para una interfaz de red.

#### <a name="internal-dns-hostname-resolution-for-vms"></a>Resolución de nombres de host DNS internos (para máquinas virtuales)
Todas las máquinas virtuales de Azure se configuran con [servidores DNS administrados por Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) de forma predeterminada, a menos que se configuren explícitamente servidores DNS personalizados. Estos servidores DNS proporcionan la resolución de nombres internos para las máquinas virtuales que residen en la misma red virtual.

Cuando se crea una máquina virtual, se agrega a los servidores DNS administrados por Azure una asignación para el nombre de host a su dirección IP privada. En una máquina virtual de interfaz de varias redes, el nombre de host se asigna a la dirección IP privada de la interfaz de red principal.

Las máquinas virtuales que se configuran con servidores DNS administrados por Azure podrán resolver los nombres de host de todas las máquinas virtuales de su red virtual como sus direcciones IP privadas.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Equilibradores de carga internos (ILB) y puertas de enlace de aplicaciones
Puede asignar una dirección IP privada a la configuración del **front-end** de un [equilibrador de carga interno de Azure](../load-balancer/load-balancer-internal-overview.md) (ILB) o [Azure Application Gateway](../application-gateway/application-gateway-introduction.md). Esta dirección IP privada actúa como punto de conexión interno, accesible solo a los recursos en su red virtual y a las redes remotas conectadas a la red virtual. Puede asignar una dirección IP privada estática o dinámica a la configuración del front-end.

### <a name="at-a-glance"></a>De un vistazo
La siguiente tabla muestra la propiedad específica a través de la cual una dirección IP privada se puede asociar a un recurso de nivel superior y los métodos de asignación posibles (dinámicos o estáticos) que se pueden usar.

| Recurso de nivel superior | Asociación de dirección IP | dinámico | estático |
| --- | --- | --- | --- |
| Máquina virtual |interfaz de red |Sí |Sí |
| Equilibrador de carga |Configuración de front-end |Sí |Sí |
| puerta de enlace de aplicaciones |Configuración de front-end |Sí |Sí |

## <a name="limits"></a>Límites
Los límites impuestos en una dirección IP se indican en el conjunto completo de [límites de red](../azure-subscription-service-limits.md#networking-limits) de Azure. Estos límites son por región y suscripción. Puede [ponerse en contacto con el soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar los límites predeterminados hasta alcanzar los límites máximos, según las necesidades empresariales.

## <a name="pricing"></a>Precios
Las direcciones IP públicas pueden tener un precio simbólico. Para más información sobre los precios de las direcciones IP en Azure, revise la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Pasos siguientes
* [Implementar una máquina virtual con una dirección IP pública estática mediante el portal de Azure](virtual-network-deploy-static-pip-arm-portal.md)
* [Implementar una máquina virtual con una dirección IP pública estática mediante una plantilla](virtual-network-deploy-static-pip-arm-template.md)
* [Implemente una VM con una dirección IP privada estática](virtual-networks-static-private-ip-arm-pportal.md)
