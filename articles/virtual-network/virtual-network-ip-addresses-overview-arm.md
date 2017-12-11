---
title: Tipos de direcciones IP en Azure | Microsoft Docs
description: "Información acerca de direcciones IP públicas y privadas en Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 610b911c-f358-4cfe-ad82-8b61b87c3b7e
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/16/2017
ms.author: jdial
ms.openlocfilehash: e3baedba814cf6ac19df72c49e1c95ea2cd5cf73
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>Tipos de direcciones IP y métodos de asignación en Azure

Puede asignar direcciones IP a los recursos de Azure para que se comuniquen con otros recursos de Azure, la red local e Internet. Hay dos tipos de direcciones IP que puede usar en Azure:

* **Direcciones IP públicas**: las direcciones IP públicas se usan para la comunicación con Internet, incluidos los servicios de acceso público de Azure.
* **Direcciones IP privadas**: las direcciones IP privadas se usan para la comunicación dentro de una red virtual (VNet) de Azure y en la red local, cuando se usa una puerta de enlace de VPN o un circuito ExpressRoute para ampliar la red a Azure.

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  En este artículo se describe el uso del modelo de implementación de Resource Manager, recomendado por Microsoft para la mayoría de las nuevas implementaciones en lugar del [modelo de implementación clásica](virtual-network-ip-addresses-overview-classic.md).
> 

Si está familiarizado con el modelo de implementación clásica, revise las [diferencias en el direccionamiento IP entre la implementación clásica y Resource Manager](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Direcciones IP públicas

Las direcciones IP públicas permiten a los recursos de Internet la comunicación entrante a los recursos de Azure. También habilitan los recursos de Azure para la comunicación saliente a Internet y los servicios de Azure orientados al público con una dirección IP asignada al recurso. Hasta que cancele la asignación, la dirección estará dedicada al recurso. Si una dirección IP pública no se asigna a un recurso, este puede seguir comunicándose hacia Internet, pero Azure asigna dinámicamente una dirección IP disponible que no esté dedicada a los recursos. Para más información acerca de las conexiones salientes en Azure, consulte [Comprender las conexiones salientes en Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

En el Administrador de recursos de Azure, una dirección [IP pública](virtual-network-public-ip-address.md) es un recurso que cuenta con propiedades específicas. Estos son algunos de los recursos con los que puede asociar un recurso de dirección IP pública:

* Interfaces de red de máquinas virtuales
* Equilibradores de carga accesibles desde Internet
* Puertas de enlace de VPN
* Puertas de enlace de aplicaciones

### <a name="ip-address-version"></a>Versión de la dirección IP

Las direcciones IP públicas se crean con una dirección IPv4 o IPv6. Las direcciones IPv6 públicas solo se pueden asignar a los equilibradores de carga accesibles desde Internet.

### <a name="sku"></a>SKU

Las direcciones IP públicas se crean con una de las SKU siguientes:

#### <a name="basic"></a>Básica

Todas las direcciones IP públicas creadas antes de la introducción de SKU son direcciones IP públicas de SKU básica. Con la introducción de SKU, tiene la opción de especificar qué SKU le gustaría que fuera la dirección IP pública. Las direcciones de SKU básica:

- Se asignan con el método de asignación estática o el de asignación dinámica.
- Se asignan a cualquier recurso de Azure al que se pueda asignar una dirección IP pública, como interfaces de red, instancias de VPN Gateway, Application Gateway y equilibradores de carga accesibles desde Internet.
- Se pueden asignar a una zona específica.
- No tienen redundancia de zona. Para más información sobre las zonas de disponibilidad, consulte [Introducción a las zonas de disponibilidad](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="standard"></a>Standard

Las direcciones IP públicas de SKU estándar:

- Solo se asignan con el método de asignación estática.
- Se asignan a interfaces de red o equilibradores de carga estándar accesibles desde Internet. Para más información sobre las SKU de los equilibradores de carga de Azure, consulte [Azure load balancer standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (SKU estándar de equilibrador de carga de Azure).
- Tienen redundancia de zona de forma predeterminada. Se pueden crear de forma zonal y garantizada en una zona de disponibilidad específica.  Para más información sobre las zonas de disponibilidad, consulte [Introducción a las zonas de disponibilidad](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
 
> [!NOTE]
> Cuando asigna una dirección IP pública de SKU estándar a la interfaz de red de una máquina virtual, debe permitir explícitamente el tráfico previsto con un [grupo de seguridad de red](security-overview.md#network-security-groups).  Para evitar que se produzca un error en la comunicación con el recurso, debe crear un grupo de seguridad de red, asociarlo y permitir explícitamente el tráfico deseado.

La SKU estándar se encuentra en versión preliminar. Antes de crear una dirección IP pública de SKU estándar, primero debe registrarse para la versión preliminar y crear la dirección en una ubicación admitida. Para registrarse para la versión preliminar, consulte [Registro para la versión preliminar de la SKU estándar](virtual-network-public-ip-address.md#register-for-the-standard-sku-preview). Para una lista de ubicaciones (regiones) admitidas, consulte [Disponibilidad de regiones](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region-availability) y supervise la página [Actualizaciones de Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network) para conocer la compatibilidad con regiones adicionales.


### <a name="allocation-method"></a>Método de asignación

Existen dos métodos en los que se asigna una dirección IP a un recurso de dirección IP pública: *dinámico* o *estático*. El predeterminado es el *dinámico*, en el que **no** se asigna ninguna dirección IP en el momento de su creación. En su lugar, se asigna la dirección IP pública cuando se inicia (o crea) el recurso asociado (como una máquina virtual o un equilibrador de carga). La dirección IP se libera cuando se detiene (o elimina) el recurso, Después de liberarse del recurso A, por ejemplo, la dirección IP se puede asignar a un recurso diferente. Si la dirección IP se asigna a un recurso diferente mientras está detenido el recurso A, al reiniciar este recurso, se asignará una dirección IP diferente.

Para asegurarse de que la dirección IP para el recurso asociado siga siendo la misma, puede establecer explícitamente el método de asignación en *estático*. Una dirección IP estática se asigna de inmediato. La dirección se libera cuando se elimina el recurso o se cambia su método de asignación a *dinámico*.

> [!NOTE]
> Incluso cuando se establece el método de asignación en *estático*, no se puede especificar la dirección IP real asignada al recurso de dirección IP pública. Azure asigna la dirección IP desde un grupo de direcciones IP disponibles en la ubicación de Azure cuando se crea el recurso.
>

Las direcciones IP públicas se suelen usar en los escenarios siguientes:

* Cuando debe actualizar las reglas de firewall para comunicarse con los recursos de Azure.
* La resolución de nombres DNS, en la que un cambio de dirección IP requeriría actualizar los registros D.
* Los recursos de Azure se comunican con otras aplicaciones o servicios que utilizan un modelo de seguridad basado en dirección IP.
* Usa certificados SSL vinculados a una dirección IP.

> [!NOTE]
> Azure asigna direcciones IP públicas a partir de un rango único para cada región de Azure. Para más información, consulte [Intervalos de direcciones IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653).
>

### <a name="dns-hostname-resolution"></a>Resolución de nombres de host DNS
Puede especificar una etiqueta de nombre de dominio DNS para un recurso de IP pública, lo que crea una asignación para *etiquetadenombrededominio*.*ubicación.cloudapp.azure.com* en la dirección IP pública de los servidores DNS que administra Azure. Por ejemplo, si crea un recurso de IP pública con **contoso** como *etiquetaDeNombreDeDominio* en la *ubicación* **Oeste de EE. UU.** de Azure, el nombre de dominio completo (FQDN) **contoso.westus.cloudapp.azure.com** se resolverá en la dirección IP pública del recurso. Puede usar el FQDN para crear un registro CNAME de dominio personalizado que apunte a la dirección IP pública en Azure. En lugar de usar la etiqueta de nombre DNS con el sufijo predeterminado, o además de ello, puede usar el servicio Azure DNS para configurar un nombre DNS con un sufijo personalizado que se resuelva en la dirección IP pública. Para obtener más información, vea los detalles relativos al [uso de Azure DNS con una dirección IP pública de Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).

> [!IMPORTANT]
> Cada etiqueta de nombre de dominio que se cree debe ser única dentro de su ubicación de Azure.  
>

### <a name="virtual-machines"></a>Máquinas virtuales

Puede asociar una dirección IP pública con una máquina virtual [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) mediante la asignación a su **interfaz de red**. Puede asignar una dirección IP pública estática o dinámica a una máquina virtual. Más información sobre [asignación de direcciones IP a interfaces de red](virtual-network-network-interface-addresses.md).

### <a name="internet-facing-load-balancers"></a>Equilibradores de carga accesibles desde Internet

Puede asociar una dirección IP pública creada con una [SKU](#SKU) o con una instancia de [Azure Load Balancer](../load-balancer/load-balancer-overview.md) asignándola a la configuración del **front-end** del equilibrador de carga. La dirección IP pública actúa como dirección IP virtual (VIP) de carga equilibrada. Puede asignar una dirección IP pública estática o dinámica al front-end de un equilibrador de carga. También le puede asignar varias direcciones IP públicas a un front-end del equilibrador de carga, lo que hace posibles aquellos escenarios con [varias VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , como un entorno de varios inquilinos con sitios web basados en SSL. Para más información sobre las SKU de los equilibradores de carga de Azure, consulte [Azure load balancer standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (SKU estándar de equilibrador de carga de Azure).

### <a name="vpn-gateways"></a>Puertas de enlace de VPN

Una instancia de [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) permite conectar una red virtual de Azure a otras redes virtuales de Azure o a una red local. Se asigna una dirección IP pública a la instancia de VPN Gateway para habilitar la comunicación con la red remota. Solo puede asignar una dirección IP pública *dinámica* a una puerta de enlace de VPN.

### <a name="application-gateways"></a>Puertas de enlace de aplicaciones

Puede asociar una dirección IP pública con una [puerta de enlace de aplicaciones](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)de Azure asignándola a la configuración del **front-end** de la puerta de enlace. Esta dirección IP pública actúa como VIP de carga equilibrada. Solo se puede asignar una dirección IP pública *dinámica* a una configuración del front-end de la puerta de enlace de aplicaciones.

### <a name="at-a-glance"></a>De un vistazo
La siguiente tabla muestra la propiedad específica a través de la cual una dirección IP pública se puede asociar a un recurso de nivel superior y los métodos de asignación posibles (dinámicos o estáticos) que se pueden usar.

| Recurso de nivel superior | Asociación de dirección IP | dinámico | estático |
| --- | --- | --- | --- |
| Máquina virtual |interfaz de red |Sí |Sí |
| Equilibrador de carga accesible desde Internet |Configuración de front-end |Sí |Sí |
| Puerta de enlace de VPN |Configuración de dirección IP de puerta de enlace |Sí |No |
| puerta de enlace de aplicaciones |Configuración de front-end |Sí |No |

## <a name="private-ip-addresses"></a>Direcciones IP privadas
Las direcciones IP privadas permiten que los recursos de Azure se comuniquen con otros recursos en una [red virtual](virtual-networks-overview.md) , o en la red local a través de una puerta de enlace de VPN o un circuito ExpressRoute, sin usar una dirección IP accesible desde Internet.

En el modelo de implementación de Azure Resource Manager, una dirección IP privada se asocia a los siguientes tipos de recursos de Azure:

* Interfaces de red de máquinas virtuales
* Equilibradores de carga internos (ILB)
* Puertas de enlace de aplicaciones

### <a name="ip-address-version"></a>Versión de la dirección IP

Las direcciones IP privadas se crean con una dirección IPv4 o IPv6. Las direcciones IPv6 privadas solo se pueden asignar con el método de asignación dinámico. No se puede comunicar entre las direcciones IPv6 privadas de una red virtual. Se permite la comunicación entrante con una dirección IPv6 privada desde Internet, a través de un equilibrador de carga accesible desde Internet. Consulte [Creación de un equilibrador de carga accesible desde Internet con IPv6](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para más información.

### <a name="allocation-method"></a>Método de asignación

Se asigna una dirección IP privada del intervalo de direcciones de la subred de la red virtual en la que se implementa un recurso. Azure reserva las cuatro primeras direcciones en cada intervalo de direcciones de subred, de modo que las direcciones no se pueden asignar a los recursos. Por ejemplo, si el intervalo de direcciones de la subred es 10.0.0.0/16, las direcciones 10.0.0.0 a 10.0.0.3 no se pueden asignar a los recursos. Las direcciones IP dentro del intervalo de direcciones de la subred solo pueden asignarse a un recurso a la vez. 

Hay dos métodos de asignación de direcciones IP privadas:

- **Dinámica**: Azure asigna la siguiente dirección IP sin asignar o no reservada disponible en el intervalo de direcciones de la subred. Por ejemplo, Azure asigna 10.0.0.10 a un nuevo recurso, si las direcciones 10.0.0.4 a 10.0.0.9 ya están asignadas a otros. Este es el método de asignación predeterminado. Una vez asignadas, las direcciones IP dinámicas solo se liberan si se elimina una interfaz de red y se asigna a otra subred diferente de la misma red virtual, o bien el método de asignación se cambia a estática y se especifica otra dirección IP. De forma predeterminada, cuando se cambia el método de asignación de dinámica a estática, Azure asigna la dirección asignada dinámicamente anterior como dirección estática.
- **Estática**: seleccione y asigne cualquier dirección IP sin asignar o no reservada en el intervalo de direcciones de la subred. Por ejemplo, si el intervalo de direcciones de una subred es 10.0.0.0/16 y las direcciones 10.0.0.4 a 10.0.0.9 ya están asignadas a otros recursos, puede asignar cualquier dirección entre 10.0.0.10 y 10.0.255.254. Las direcciones estáticas solo se liberan cuando se elimina la interfaz de red. Si cambia el método de asignación a dinámica, Azure asigna dinámicamente como dirección dinámica la dirección IP estática asignada anteriormente, aunque no sea la siguiente disponible en el intervalo de direcciones de la subred. La dirección también cambia si la interfaz de red se asigna a otra subred de la misma red virtual, pero para ello, antes hay que cambiar el método de asignación de estática a dinámica. Una vez que ha asignado la interfaz de red a otra subred, puede volver a cambiar el método de asignación a estática y asignar una dirección IP del intervalo de direcciones de la nueva subred.

### <a name="virtual-machines"></a>Máquinas virtuales

Se asignan una o varias direcciones IP privadas a una o varias **interfaces de red** de una máquina virtual [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). En todas las direcciones IP privadas se puede especificar el método de asignación como estática o dinámica.

#### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Resolución de nombres de host DNS internos (para máquinas virtuales)

Todas las máquinas virtuales de Azure se configuran con [servidores DNS administrados por Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) de forma predeterminada, a menos que se configuren explícitamente servidores DNS personalizados. Estos servidores DNS proporcionan la resolución de nombres internos para las máquinas virtuales que residen en la misma red virtual.

Cuando se crea una máquina virtual, se agrega a los servidores DNS administrados por Azure una asignación para el nombre de host a su dirección IP privada. Si una máquina virtual tiene varias interfaces de red o varias configuraciones de IP para una interfaz de red, el nombre de host se asigna a la dirección IP privada de la configuración de IP principal de la interfaz de red principal.

Las máquinas virtuales que se configuran con servidores DNS administrados por Azure podrán resolver los nombres de host de todas las máquinas virtuales de su red virtual como sus direcciones IP privadas. Para resolver los nombres de host de las máquinas virtuales en redes virtuales conectadas, es preciso usar un servidor DNS personalizado.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Equilibradores de carga internos (ILB) y puertas de enlace de aplicaciones

Puede asignar una dirección IP privada a la configuración del **front-end** de un [equilibrador de carga interno de Azure](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB) o [Azure Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Esta dirección IP privada actúa como punto de conexión interno, accesible solo a los recursos en su red virtual y a las redes remotas conectadas a la red virtual. Puede asignar una dirección IP privada estática o dinámica a la configuración de front-end.

### <a name="at-a-glance"></a>De un vistazo
La siguiente tabla muestra la propiedad específica a través de la cual una dirección IP privada se puede asociar a un recurso de nivel superior y los métodos de asignación posibles (dinámicos o estáticos) que se pueden usar.

| Recurso de nivel superior | Asociación de dirección IP | dinámico | estático |
| --- | --- | --- | --- |
| Máquina virtual |interfaz de red |Sí |Sí |
| Equilibrador de carga |Configuración de front-end |Sí |Sí |
| puerta de enlace de aplicaciones |Configuración de front-end |Sí |Sí |

## <a name="limits"></a>Límites
Los límites impuestos en una dirección IP se indican en el conjunto completo de [límites de red](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) de Azure. Los límites son por región y suscripción. Puede [ponerse en contacto con el servicio de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar los límites predeterminados hasta alcanzar los límites máximos, según las necesidades empresariales.

## <a name="pricing"></a>Precios
Las direcciones IP públicas pueden tener un precio simbólico. Para más información sobre los precios de las direcciones IP en Azure, revise la página [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Pasos siguientes
* [Implementar una máquina virtual con una dirección IP pública estática mediante el portal de Azure](virtual-network-deploy-static-pip-arm-portal.md)
* [Implementar una máquina virtual con una dirección IP pública estática mediante una plantilla](virtual-network-deploy-static-pip-arm-template.md)
* [Implemente una VM con una dirección IP privada estática](virtual-networks-static-private-ip-arm-pportal.md)
