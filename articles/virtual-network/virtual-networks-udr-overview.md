---
title: "Enrutamiento del tráfico de redes virtuales de Azure | Microsoft Docs"
description: "Aprenda la forma en que Azure enruta el tráfico de las redes virtuales y cómo se puede personalizar el enrutamiento de Azure."
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
ms.date: 10/26/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 85be79261d5fc214ab4b46fa5d7b4d0a5b13db27
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="virtual-network-traffic-routing"></a>Enrutamiento del tráfico de redes virtuales

Aprenda la forma en que Azure enruta el tráfico entre los recursos locales y de Internet de Azure. Azure crea automáticamente una tabla de rutas para cada subred de una red virtual de Azure y agrega las rutas predeterminadas del sistema a la tabla. Para más información acerca de las redes y subredes virtuales, consulte [Red virtual](virtual-networks-overview.md). Puede reemplazar algunas de las rutas del sistema de Azure por [rutas personalizadas](#custom-routes) y agregar rutas personalizadas adicionales a las tablas de rutas. Azure enruta el tráfico saliente desde una subred en función de las rutas de la tabla de rutas de una subred.

## <a name="system-routes"></a>Rutas del sistema

Azure crea automáticamente rutas del sistema y las asigna a todas las subredes de una red virtual. No puede crear ni eliminar rutas del sistema, pero puede reemplazar algunas de ellas por [rutas personalizadas](#custom-routes). Azure crea rutas del sistema predeterminadas para cada subred y agrega [rutas predeterminadas opcionales](#optional-default-routes) adicionales a determinadas subredes, o a todas las subredes, al usar funcionalidades específicas de Azure.

### <a name="default"></a>Valor predeterminado

Cada ruta contiene un prefijo de dirección y el tipo de próximo salto. Cuando el tráfico que sale de una subred se envía a una dirección IP que está dentro del prefijo de la dirección de ruta, la ruta que contiene el prefijo es la que utiliza Azure. Obtenga más información acerca de [la selección de rutas por parte de Azure](#how-azure-selects-a-route) cuando varias rutas contienen los mismos prefijos o cuando los prefijos se solapan. Cuando se crea una red virtual, Azure crea automáticamente los siguientes rutas del sistema predeterminadas para cada subred de la red virtual:


|Origen |Prefijos de dirección                                        |Tipo de próximo salto  |
|-------|---------                                               |---------      |
|Valor predeterminado|Único para la red virtual                           |Red virtual|
|Valor predeterminado|0.0.0.0/0                                               |Internet       |
|Valor predeterminado|10.0.0.0/8                                              |None           |
|Valor predeterminado|172.16.0.0/12                                           |None           |
|Valor predeterminado|192.168.0.0/16                                          |None           |
|Valor predeterminado|100.64.0.0/10                                           |None           |

Los tipos de próximo salto enumerados en la tabla anterior representan la forma en que Azure enruta el tráfico destinado al prefijo de dirección enumerado. Estas son las explicaciones de los tipos de próximo salto:

- **Red virtual**: enruta el tráfico entre los intervalos de direcciones del [espacio de direcciones](virtual-network-manage-network.md#add-address-spaces) de una red virtual. Azure crea una ruta con un prefijo de dirección que corresponde a cada intervalo de direcciones definido en el espacio de direcciones de una red virtual. Si el espacio de direcciones de la red virtual tiene varios intervalos de direcciones definidos, Azure crea una ruta individual para cada intervalo de direcciones. Azure automáticamente enruta el tráfico entre subredes mediante las rutas que se crea para cada intervalo de direcciones. No es preciso definir las puertas de enlace de Azure para enrutar el tráfico entre subredes. Aunque una red virtual contiene subredes y cada una de ellas tiene un intervalo de direcciones definido, Azure *no* crea rutas predeterminadas para los intervalos de direcciones de la subred, ya que cada uno de ellos se encuentra dentro de un intervalo de direcciones del espacio de direcciones de una red virtual.

- **Internet**: enruta a Internet el tráfico que especifica el prefijo de dirección. La ruta predeterminada del sistema especifica el prefijo de dirección 0.0.0.0/0. Si no se invalidan las rutas predeterminadas de Azure, Azure enruta a Internet el tráfico de todas las direcciones que no se haya especificada un intervalo de direcciones en una red virtual, con una excepción. Si la dirección de destino es para uno de los servicios de Azure, este enruta el tráfico directamente al servicio a través de la red troncal de Azure, en lugar de enrutarlo a Internet. El tráfico entre los servicios de Azure no atraviesa Internet, independientemente de la región de Azure en que exista la red virtual o de la región de Azure en que se implementa una instancia del servicio de Azure. Puede reemplazar la ruta del sistema predeterminada de Azure predeterminado para el prefijo de dirección 0.0.0.0/0 por un [ruta personalizada](#custom-routes).

- **No**: el tráfico que se enruta al tipo de próximo salto **No** se elimina, en lugar de enrutarlo fuera de la subred. Azure crea automáticamente las rutas predeterminadas para los siguientes prefijos de dirección:
    - **10.0.0.0/8, 172.16.0.0/12 y 192.168.0.0/16**: reservadas para el uso privado en el RFC 1918.
    - **100.64.0.0/10**: reservada en el RFC 6598.

    Si asigna cualquiera de los intervalos de direcciones anteriores del espacio de direcciones de una red virtual, Azure cambia automáticamente el tipo de próximo salto de la ruta de **No** a **Red virtual**. Si asigna un intervalo de direcciones al espacio de direcciones de una red virtual que incluya uno de los cuatro prefijos de direcciones reservadas, pero no es lo mismo que ellas, Azure quita la ruta del prefijo y agrega una ruta para el prefijo de dirección que agregó, con **Virtual red** como el tipo de próximo salto.

### <a name="optional-default-routes"></a>Rutas predeterminadas opcionales

Azure agrega rutas del sistema predeterminadas adicionales de sistema para diferentes funcionalidades de Azure, pero solo si se habilitan dichas funcionalidades. En función de la funcionalidad, Azure agrega las rutas predeterminadas opcionales a subredes concretas de la red virtual o a todas las subredes de una red virtual. Estos son las rutas del sistema adicionales y los tipos de próximo salto que Azure puede agregar al habilitar diferentes funcionalidades:

|Origen                 |Prefijos de dirección                       |Tipo de próximo salto|La subred de red virtual a la que se agrega la ruta|
|-----                  |----                                   |---------                    |--------|
|Valor predeterminado                |Único para la red virtual, por ejemplo: 10.1.0.0/16|Emparejamiento de VNET                 |Todo|
|Puerta de enlace de red virtual|Prefijos anunciados desde el entorno local a través de BGP, o bien configurados en la puerta de enlace de red local     |Puerta de enlace de red virtual      |Todo|
|Valor predeterminado                |Múltiple                               |VirtualNetworkServiceEndpoint|Solo la subred para la que se habilita un punto de conexión de servicio.|

- **Emparejamiento de red virtual (VNet)**: al crear un emparejamiento entre dos redes virtuales, se agrega una ruta para cada intervalo de direcciones en el espacio de direcciones de cada red virtual para la que se crea un emparejamiento. Más información acerca del [emparejamiento de red virtual](virtual-network-peering-overview.md).  
- **Puerta de enlace de red virtual**: cuando una puerta de enlace de red virtual se agrega a una red virtual, se agregan también una o varias rutas en las que *Puerta de enlace de red virtual* aparece como el tipo de próximo salto. El origen es también una *puerta de enlace de red virtual*, ya que la puerta de enlace agrega las rutas a la subred. Si la puerta de enlace de red local intercambia las rutas del protocolo Border Gateway Protocol ([BGP](#border-gateway-protocol) con una puerta de enlace de red virtual de Azure, se agrega una ruta por cada ruta que se propaga desde la puerta de enlace de red local. Se recomienda resumir las rutas locales en los intervalos de direcciones mayores posibles, con el fin de que se propague el menor número de rutas a una puerta de enlace de red virtual de Azure. Hay límites en el número de rutas que se pueden propagar a una puerta de enlace de red virtual de Azure. Para más información, consulte el artículo acerca de los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).
- **VirtualNetworkServiceEndpoint**: Azure agrega las direcciones IP públicas de determinados servicios a la tabla de rutas cuando se habilita un punto de conexión para el servicio. Los puntos de conexión de servicio se habilitan para las subredes individuales de una red virtual, por lo que la ruta solo se agrega a la tabla de rutas de una subred para la que haya algún punto de conexión de servicio habilitado. Las direcciones IP públicas de los servicios de Azure cambian periódicamente. Azure administra automáticamente las direcciones en la tabla de rutas cuando cambian. Obtenga más información acerca de los [puntos de conexión de servicio una red virtual](virtual-network-service-endpoints-overview.md) y los servicios para los que se pueden crear puntos de conexión de servicio. 

> [!NOTE]
> El tipos de próximo salto **Emparejamiento de VNet** y **VirtualNetworkServiceEndpoint** solo se agregan a las tablas de rutas de las subredes de las redes virtuales creadas a través del modelo de implementación de Azure Resource Manager. Los tipos de próximo salto no se agregan a las tablas de rutas asociadas a las subredes de redes virtuales creadas mediante el modelo de implementación clásica. Obtenga más información acerca de los [modelos de implementación de Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="custom-routes"></a>Rutas personalizadas

Para crear rutas personalizadas, cree rutas [definidas por el usuario](#user-defined) o intercambie rutas del protocolo [Border Gateway Protocol](#border-gateway-protocol) (BGP) entre su puerta de enlace de red local y una puerta de enlace de red virtual de Azure. 
 
### <a name="user-defined"></a>Definidas por el usuario

En Azure se pueden crear rutas personalizadas o definidas por el usuario para reemplazar las rutas de sistema predeterminadas o para agregar rutas adicionales a una tabla de rutas de una subred. En Azure, cree una tabla de rutas y asóciela a cero o más subredes de red virtual. Cada subred puede tener cero o una ruta de tablas de ruta asociada. Para obtener información acerca del número máximo de rutas que puede agregar a una tabla de rutas y del número máximo de tablas de rutas definidas por el usuario que se pueden crear por suscripción de Azure, consulte el artículo acerca de los [límites de Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Si crea una tabla de rutas y la asocia a una subred, las rutas que contiene se combinan con las rutas predeterminadas que Azure agrega a una subred de forma predeterminada, o bien las reemplazan.

Puede especificar los siguientes tipos de próximo salto al crear una ruta definida por el usuario:

- **Aplicación virtual**: una aplicación virtual es una máquina virtual que habitualmente ejecuta una aplicación de red como, por ejemplo, un firewall. Para más información acerca de las aplicaciones virtuales de red preconfiguradas que se pueden implementar en una red, consulte [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Al crear una ruta con el tipo de salto de **aplicación virtual**, especifique también una dirección IP del próximo salto. La dirección IP puede ser:

    - La [dirección IP privada](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) de una interfaz de red conectada a una máquina virtual. Cualquier interfaz de red conectada a una máquina virtual que reenvíe el tráfico de red a una dirección que no sea la suya propia debe tener la opción *Habilitar reenvío de IP* habilitada. El valor deshabilita la comprobación que realiza Azure del origen y destino en una interfaz de red. Obtenga más información acerca de cómo [habilitar el reenvío IP en una interfaz de red](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Aunque *Habilitar reenvío de IP* es un valor de Azure, es posible que también necesite habilitar el reenvío IP en el sistema operativo de la máquina virtual para que el dispositivo desvíe el tráfico entre las direcciones IP privadas asignadas a interfaces de red de Azure. Si el dispositivo debe enrutar el tráfico a una dirección IP pública, debe hacer de proxy ante el tráfico o traducir la dirección IP privada del origen a su propia dirección IP privada y Azure la traducirá a una dirección IP pública antes de enviar el tráfico a Internet. Para determinar la configuración necesaria en la máquina virtual, consulte la documentación del sistema operativo o la aplicación de red. Para obtener información acerca de las conexiones salientes en Azure, consulte [Información acerca de las conexiones salientes](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

    > [!NOTE]
    > Implemente una aplicación virtual en una subred diferente la que se encuentran los recursos que enrutan a través de la aplicación virtual. La implementación de la aplicación virtual en la misma subred y la posterior aplicación de una tabla de rutas en la subred que enruta el tráfico a través de la aplicación virtual pueden provocar bucles de enrutamiento, en los que el tráfico nunca sale de la subred.

    - La dirección IP privada de un [equilibrador de carga interno](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de Azure. A menudo se usa un equilibrador de carga como parte de una [estrategia de alta disponibilidad para aplicaciones virtuales de red](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).

    Puede definir una ruta con 0.0.0.0/0 como prefijo de dirección y un tipo de próximo salto de la aplicación virtual, lo que permite a la aplicación inspeccionar el tráfico y determinar si el tráfico se debe reenviar o eliminar. Si tiene intención de crear una definida por el usuario que contenga el prefijo de dirección 0.0.0.0/0, consulte antes el apartado [0.0.0.0/0 address prefix](#default-route) (Prefijo de dirección 0.0.0.0/0).

- **Puerta de enlace de red virtual**: se especifica cuando se desea que el tráfico destinado a prefijos de dirección se enrute a una puerta de enlace de red virtual. La puerta de enlace de red virtual debe crearse con el tipo **VPN**. No se puede especificar una puerta de enlace de red virtual creada como el tipo **ExpressRoute** en una ruta definida por el usuario porque con ExpressRoute se debe usar [BGP](#border-gateway-protocol-routes) para las rutas personalizadas. Puede definir una ruta que dirige el tráfico destinado al prefijo de dirección 0.0.0.0/0 a una puerta de enlace de red virtual [basada en ruta](../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#vpntype). En un entorno local, puede tener un dispositivo que compruebe el tráfico y determine si se reenvía o se elimina. Si tiene intención de crear una definida por el usuario para el prefijo de dirección 0.0.0.0/0, consulte antes el apartado [0.0.0.0/0 address prefix](#default-route) (Prefijo de dirección 0.0.0.0/0). En lugar de configurar una ruta definida por el usuario para el prefijo de dirección 0.0.0.0/0, es posible anunciar una ruta con el prefijo 0.0.0.0/0 a través de BGP, si ha [habilitado BGP para una puerta de enlace de red virtual de VPN](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- **No**: se especifica cuándo se desea colocar tráfico en un prefijo de dirección, en lugar de reenviar el tráfico a un destino. Si no ha configurado completamente una funcionalidad, Azure puede enumerar *No* para algunas de las rutas de opcional del sistema. Por ejemplo, si ve *No* en **Dirección IP del próximo salto** con un **Tipo de próximo salto** de *Puerta de enlace de red virtual* o *Aplicación virtual*, puede deberse a que el dispositivo no funciona o no está completamente configurado. Azure crea [rutas predeterminadas](#default) del sistema para los prefijos de direcciones reservados con **No** como tipo de próximo salto.
- **Red virtual**: especifíquelo si desea reemplazar el enrutamiento predeterminado en una red virtual. Consulte [Ejemplo de enrutamiento](#routing-example), para ver un ejemplo de por qué puede crear una ruta con el tipo de salto **Red virtual**.
- **Internet**: especifíquelo si desea enrutar explícitamente el tráfico destinado a un prefijo de dirección a Internet, o si desea que el tráfico destinado a los servicios de Azure con direcciones IP públicas se conserve dentro de la red troncal de Azure.

En las rutas definidas por el usuario, no se pueden especificar **Emparejamiento de VNet** o **VirtualNetworkServiceEndpoint** como tipo de próximo salto. Las rutas con los tipos de próximo salto **Emparejamiento de VNet** o **VirtualNetworkServiceEndpoint** solo las crea Azure, al configurar un emparejamiento de red virtual o un punto de conexión de servicio.

**Tipos de próximo salto en las herramientas de Azure**

El nombre que se muestra y al que hace referencia en los tipos de próximo salto es diferente entre Azure Portal y las herramientas de línea de comandos y los modelos de implementación clásico y mediante Azure Resource Manager. En la siguiente tabla se enumeran los nombres que se usan para hacer referencia a cada tipo de próximo salto con las diferentes herramientas y los [modelos de implementación](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json):

|Tipo de próximo salto                   |CLI de Azure 2.0 y PowerShell (Resource Manager) |CLI de Azure 1.0 y PowerShell (clásico)|
|-------------                   |---------                                       |-----|
|Puerta de enlace de red virtual         |VirtualNetworkGateway                           |VPNGateway|
|Red virtual                 |VNetLocal                                       |VNETLocal (no disponible en la CLI 1.0 en modo asm)|
|Internet                        |Internet                                        |Internet (no disponible en la CLI 1.0 en modo asm)|
|Aplicación virtual               |VirtualAppliance                                |VirtualAppliance|
|None                            |None                                            |Nulo (no disponible en la CLI 1.0 en modo asm)|
|Emparejamiento de redes virtuales de Azure         |Emparejamiento de VNET                                    |No aplicable|
|Puntos de conexión de servicio Virtual Network|VirtualNetworkServiceEndpoint                   |No aplicable|

### <a name="border-gateway-protocol"></a>Border Gateway Protocol

Una puerta de enlace de red local puede intercambiar rutas con una puerta de enlace de red virtual de Azure mediante el protocolo Border Gateway Protocol (BGP). El uso de BGP con una puerta de enlace de red virtual de Azure depende del tipo seleccionado al crear la puerta de enlace. Si el tipo seleccionado es:

- **ExpressRoute**: debe usar BGP para anunciar rutas locales para el enrutador perimetral de Microsoft. Si implementa una puerta de enlace de red virtual como del tipo ExpressRoute, no puede crear rutas definidas por el usuario para forzar el tráfico a la puerta de enlace de red virtual ExpressRoute. Puede usar las rutas definidas por el usuario para forzar el tráfico de Express Route a, por ejemplo, un dispositivo virtual de red. 
- **VPN**: opcionalmente, puede usar BGP. Para más información, consulte [Información general de BGP con puertas de enlace de VPN de Azure](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Al intercambiar rutas con Azure mediante BGP, se agrega una ruta independiente a la tabla de rutas de todas las subredes de una red virtual para cada prefijo anunciado. La ruta se agrega con *Puerta de enlace de red virtual* como origen y tipo de próximo salto. 
 
## <a name="how-azure-selects-a-route"></a>Selección de rutas por parte de Azure

Cuando se envía tráfico saliente desde una subred, Azure selecciona una ruta en función de la dirección IP de destino y se usa el algoritmo de coincidencia de prefijo más largo. Por ejemplo, una tabla de rutas tiene dos rutas: una ruta especifica el prefijo de dirección 10.0.0.0/24, mientras que la otra especifica el prefijo de dirección 10.0.0.0/16. Azure enruta el tráfico destinado a 10.0.0.5, al tipo de próximo salto especificado en la ruta con el prefijo de dirección 10.0.0.0/24, porque 10.0.0.0/24 es un prefijo más largo que 10.0.0.0/16, aunque 10.0.0.5 esté dentro de ambos prefijos. Azure enruta el tráfico destinado a 10.0.1.5, al tipo de próximo salto especificado en la ruta con el prefijo de dirección 10.0.0.0/16, porque 10.0.1.5 no está incluido en el prefijo de dirección 10.0.0.0/24, por consiguiente, la ruta con el prefijo de dirección 10.0.0.0/16 es el prefijo más largo que coincide.

Si varias rutas contienen el mismo prefijo de dirección, Azure selecciona el tipo de ruta, en función de la siguiente prioridad:

1. Ruta definida por el usuario
2. Ruta BGP
3. Ruta del sistema

Por ejemplo, una tabla de rutas contiene las rutas siguientes:


|Origen   |Prefijos de dirección  |Tipo de próximo salto           |
|---------|---------         |-------                 |
|Valor predeterminado  | 0.0.0.0/0        |Internet                |
|Usuario     | 0.0.0.0/0        |Puerta de enlace de red virtual |

Cuando el destino del tráfico es una dirección IP que está fuera de los prefijos de dirección de las otras rutas de la tabla de rutas, Azure selecciona la ruta con el origen **Usuario**, porque las rutas definidas por el usuario tienen mayor prioridad que las rutas predeterminadas del sistema.

Para ver una tabla de enrutamiento completa con explicaciones de las rutas, consulte el [ejemplo de enrutamiento](#routing-example).

## <a name="default-route"></a>Prefijo de dirección 0.0.0.0/0

Una ruta con el prefijo de dirección 0.0.0.0/0 indica a Azure cómo enrutar el tráfico destinado a una dirección IP que no está dentro del prefijo de dirección de cualquier otra ruta en la tabla de rutas de una subred. Cuando se crea una subred, Azure crea un ruta [predeterminada](#default) para el prefijo de dirección 0.0.0.0/0, con el tipo de próximo salto **Internet**. Si no reemplaza esta ruta, Azure enruta a Internet todo el tráfico destinado a direcciones IP no incluidas en el prefijo de dirección de otra ruta. La excepción es que el tráfico dirigido a las direcciones IP públicas de los servicios de Azure permanece en la red troncal de Azure y no se enruta a Internet. Si reemplaza esta ruta por una ruta [personalizado](#custom-routes), el tráfico cuyo destino sean direcciones que no estén dentro de los prefijos de dirección de cualquier otra ruta de la tabla de rutas se envía a una aplicación virtual de red o a una puerta de enlace de red virtual, en función de lo que se especifique en una ruta personalizada.

Si se reemplaza el prefijo de dirección 0.0.0.0/0, además del tráfico de salida de la subred que fluye a través de la puerta de enlace de red virtual o de la aplicación virtual, se producen los siguientes cambios en el enrutamiento predeterminado de Azure: 

- Azure envía todo el tráfico al tipo de próximo salto especificado en la ruta, lo que incluye el tráfico destinado a las direcciones IP públicas de los servicios de Azure. Cuando el tipo de próximo salto de la ruta con el prefijo de dirección 0.0.0.0/0 es **Internet**, el tráfico de la subred destinado a las direcciones IP públicas de los servicios de Azure no sale de la red troncal de Azure, independientemente de la región de Azure en que existan la red virtual o un recurso de servicio de Azure. Cuando se crea una ruta BGP o definida por el usuario con un tipo de próximo salto de **Puerta de enlace de red virtual** o **Aplicación virtual**; sin embargo, todo el tráfico, lo que incluye el tráfico enviado a las direcciones IP públicas de los servicios de Azure en el que no se hayan habilitado [puntos de conexión de servicio](virtual-network-service-endpoints-overview.md), se envía al tipo de próximo salto especificado en la ruta. Si ha habilitado un punto de conexión para un servicio, el tráfico dirigido a dicho servicio no se enruta al tipo de próximo salto en una ruta con el prefijo de dirección 0.0.0.0/0, porque los prefijos de dirección del servicio se especifican en la ruta que Azure crea cuando se habilita el punto de conexión de servicio y los prefijos de dirección del servicio son mayores que 0.0.0.0/0.
- No puede acceder directamente a los recursos de la subred desde Internet. Puede acceder indirectamente a los recursos de la subred desde Internet si el tráfico entrante pasa por el dispositivo especificado por el tipo de próximo salto para una ruta con el prefijo de dirección 0.0.0.0/0 antes de llegar al recurso en la red virtual. Si la ruta contiene los siguientes valores del tipo de salto próximo:
    - **Aplicación virtual**: el dispositivo debe:
        - Ser accesible desde Internet.
        - Tener una dirección IP pública asignada.
        - No tener una regla del grupo de seguridad de red asociada a él que impide la comunicación con el dispositivo.
        - No denegar la comunicación.
        - Poder traducir y reenviar direcciones de red, o desviar el tráfico a través de un servidor proxy al recurso de destino de la subred y devolver el tráfico a Internet. 
    - **Puerta de enlace de red virtual**: si la puerta de enlace es una puerta de enlace de red virtual ExpressRoute, un dispositivo local conectado a Internet puede traducir y reenviar direcciones de red, o bien desviar el tráfico a través de un servidor proxy al recurso de destino de la subred a través del [emparejamiento privado](../expressroute/expressroute-circuit-peerings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-private-peering) de ExpressRoute. 

  Para obtener información acerca de las puertas de enlace de redes virtuales y aplicaciones virtuales entre Internet y Azure, consulte [Red perimetral entre Azure y un centro de datos local](/architecture/reference-architectures/dmz/secure-vnet-hybrid?toc=%2fazure%2fvirtual-network%2ftoc.json) y [Red perimetral entre Azure e Internet](/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="routing-example"></a>Ejemplo de enrutamiento

Para ilustrar los conceptos de este artículo, en las siguientes secciones se describen:
- Un escenario con requisitos.
- Las rutas personalizadas necesarias para cumplir los requisitos.
- La tabla de rutas que existe para una subred que incluye las rutas predeterminadas y personalizadas necesarias para cumplir los requisitos.

> [!NOTE]
> Este ejemplo no pretende ser una implementación recomendada ni unas prácticas recomendadas. Más bien se proporciona únicamente para ilustrar los conceptos de este artículo.

### <a name="requirements"></a>Requisitos

1. Implementar dos redes virtuales en la misma región de Azure y habilitar los recursos necesarios para la comunicación entre las redes virtuales.
2. Habilitar una red local para comunicarse de forma segura con ambas redes virtuales mediante un túnel VPN a través de Internet. *Como alternativa, puede utilizarse una conexión ExpressRoute, pero en este ejemplo se usa una conexión VPN.*
3. Para una subred de una red virtual:
 
    - Forzar que todo el tráfico saliente de la subred, excepto el que va a Azure Storage y el de dentro de la subred, fluya a través de una aplicación virtual de red, para su inspección y registro.
    - No inspeccionar el tráfico entre las direcciones IP privadas de la subred; permitir que el tráfico fluya directamente entre todos los recursos. 
    - Eliminar todo el tráfico saliente destinado a la otra red virtual.
    - Habilitar el tráfico saliente a Azure Storage para que fluya directamente al almacenamiento, sin hacerle pasar por una aplicación virtual de red.

4. Permitir todo el tráfico entre las restantes subredes y las redes virtuales.

### <a name="implementation"></a>Implementación

La siguiente imagen muestra una implementación a través del modelo de implementación de Azure Resource Manager que cumple los requisitos anteriores:

![Diagrama de red](./media/virtual-networks-udr-overview/routing-example.png)

Las flechas muestran el flujo de tráfico. 

### <a name="route-tables"></a>Tablas de ruta

#### <a name="subnet1"></a>Subnet1

La tabla de rutas de *Subnet1* en la imagen contiene las rutas siguientes:

|ID  |Origen |Estado  |Prefijos de dirección    |Tipo de próximo salto          |Dirección IP de siguiente salto|Nombre de ruta definida por el usuario| 
|----|-------|-------|------              |-------                |--------           |--------      |
|1   |Valor predeterminado|No válida|10.0.0.0/16         |Red virtual        |                   |              |
|2   |Usuario   |Active |10.0.0.0/16         |Aplicación virtual      |10.0.100.4         |Within-VNet1  |
|3   |Usuario   |Active |10.0.0.0/24         |Red virtual        |                   |Within-Subnet1|
|4   |Valor predeterminado|No válida|10.1.0.0/16         |Emparejamiento de VNET           |                   |              |
|5   |Valor predeterminado|No válida|10.2.0.0/16         |Emparejamiento de VNET           |                   |              |
|6   |Usuario   |Active |10.1.0.0/16         |None                   |                   |ToVNet2-1-Drop|
|7   |Usuario   |Active |10.2.0.0/16         |None                   |                   |ToVNet2-2-Drop|
|8   |Valor predeterminado|No válida|10.10.0.0/16        |Puerta de enlace de red virtual|[X.X.X.X]          |              |
|9   |Usuario   |Active |10.10.0.0/16        |Aplicación virtual      |10.0.100.4         |To-On-Prem    |
|10  |Valor predeterminado|Active |[X.X.X.X]           |VirtualNetworkServiceEndpoint    |         |              |
|11  |Valor predeterminado|No válida|0.0.0.0/0           |Internet|              |                   |              |
|12  |Usuario   |Active |0.0.0.0/0           |Aplicación virtual      |10.0.100.4         |Default-NVA   |

A continuación encontrará una explicación de cada identificador de ruta:

1. Azure ha agregado automáticamente esta ruta a todas las subredes de *Virtual-network-1*, ya que 10.0.0.0/16 es el único intervalo de direcciones definido en el espacio de direcciones de la red virtual. Si la ruta definida por el usuario en la ruta ID2 no se ha creado, el tráfico enviado a cualquier dirección entre 10.0.0.1 y 10.0.255.254 se enrutarían en la red virtual, ya que el prefijo es mayor que 0.0.0.0/0 y no se encuentra dentro de los prefijos de direcciones de cualquiera de las otras rutas. Azure ha cambiado automáticamente el estado de *Activo* a *No válido*, cuando se ha agregado ID2, una ruta definida por el usuario, puesto que tiene el mismo prefijo que la ruta predeterminada y las rutas definidas por el usuario reemplazan a las rutas predeterminadas. El estado de esta ruta sigue siendo *Activo* en *Subnet2*, ya que la tabla de rutas en la que se encuentra la ruta definida por el usuario, ID2, no está asociada a *Subnet2*.
2. Azure agregó esta ruta cuando una ruta definida por el usuario para el prefijo de red 10.0.0.0/16 se asoció a la subred *Subnet1* de la red virtual *Virtual-network-1*. La ruta definida por el usuario especifica 10.0.100.4 como dirección IP de la aplicación virtual, porque la dirección es la dirección IP privada asignada a la máquina virtual de la aplicación virtual. La tabla de rutas en la que existe esta ruta no está asociada a *Subnet2*, por lo que no aparece en la tabla de rutas de *Subnet2*. Esta ruta reemplaza la ruta predeterminada del prefijo 10.0.0.0/16 (ID1), que enruta automáticamente el tráfico dirigido a 10.0.0.1 y 10.0.255.254 dentro de la red virtual a través del tipo de próximo salto de la red virtual. Esta ruta existe para cumplir el [requisito](#requirements) 3, para forzar que todo el tráfico de salida pase por una aplicación virtual.
3. Azure agregó esta ruta cuando una ruta definida por el usuario para el prefijo de red 10.0.0.0/24 se asoció a la subred *Subnet1*. El tráfico destinado a las direcciones entre 10.0.0.1 y 10.0.0.0.254 permanece dentro de la subred, en lugar de enrutarse a la aplicación virtual especificada en la regla anterior (ID2), ya que su prefijo es más largo que la ruta ID2. La tabla de rutas no estaba asociada a *Subnet2*, por lo que no aparece en la tabla de rutas de *Subnet2*. Esta ruta reemplaza eficazmente la ruta ID2 para el tráfico de *Subnet1*. Esta ruta existe para cumplir el [requisito](#requirements) 3.
4. Azure agregó automáticamente de los ID 4 y 5 para todas las subredes de *Virtual-network-1*, cuando la red virtual se emparejó con *Virtual-network-2*. *Virtual-network-2* tiene dos intervalos de direcciones en su espacio de direcciones: 10.1.0.0/16 y 10.2.0.0/16, por lo que Azure agregó una ruta a cada intervalo. Si no se crearon las rutas definidas por el usuario en los ID 6 y 7 de la ruta, el tráfico enviado a cualquier dirección entre 10.1.0.1-10.1.255.254 y 10.2.0.1-10.2.255.254 se enrutaría a la red virtual emparejada, ya que el prefijo es mayor que 0.0.0.0/0 y no se encuentra dentro de los prefijos de direcciones de las otras rutas. Azure ha cambiado automáticamente el estado de *Activo* a *No válido*, cuando se han agregado las rutas de los ID 6 y 7, ya que tienen el mismo prefijo que las rutas de los ID 4 y 5, y las rutas definidas por el usuario reemplazan a las rutas predeterminadas. El estado de las rutas de los ID 4 y 5 sigue siendo *Activo* en *Subnet2*, ya que la tabla de rutas en la que se encuentran la rutas definidas por el usuario en los ID 4 y 5 no está asociada a *Subred2*. Se ha creado un emparejamiento de red virtual para cumplir el [requisito](#requirements) 1.
5. La misma explicación que para ID4.
6. Azure agregó esta ruta y la ruta de ID7, cuando las rutas definidas por el usuario para los prefijos de dirección 10.1.0.0/16 y 10.2.0.0/16 se asociaron a la subred *Subnet1*. Azure elimina el tráfico destinado a direcciones entre 10.1.0.1-10.1.255.254 y 10.2.0.1-10.2.255.254, en lugar de enrutarlo a la red virtual emparejada, porque las rutas definidas por el usuario reemplazan las rutas predeterminadas. Las rutas no están asociadas a *Subnet2*, por lo que no aparecen en la tabla de rutas de *Subnet2*. Las rutas reemplazan las rutas ID4 y ID5 en el caso del tráfico que sale del *Subnet1*. Las rutas ID6 y ID7 existen para satisfacer el [requisito](#requirements) 3 de eliminación del tráfico destinado a la otra red virtual.
7. La misma explicación que para ID6.
8. Azure agregó automáticamente esta ruta para todas las subredes de *Virtual-network-1* cuando se creó una puerta de enlace de red virtual del tipo VPN en la red virtual. Azure agregó la dirección IP pública de la puerta de enlace de red virtual a la raba de rutas. El tráfico enviado a cualquier dirección entre 10.10.0.1 y 10.10.255.254 se enruta a la puerta de enlace de red virtual. El prefijo es más largo que 0.0.0.0/0 y no está dentro de los prefijos de dirección de las restantes rutas. Se ha creado una puerta de enlace de red virtual para cumplir el [requisito](#requirements) 2.
9. Azure agregó esta ruta cuando una ruta definida por el usuario para el prefijo de red 10.10.0.0/16 se agregó a la tabla de rutas asociada a *Subnet1*. Esta ruta reemplaza ID8. La ruta envía todo el tráfico destinado a la red local a una NVA para su inspección, en lugar de enrutarlo directamente al entorno local. Esta ruta se ha creado para cumplir el [requisito](#requirements) 3.
10. Azure ha agregado automáticamente esta ruta a la subred cuando se ha habilitado un punto de conexión de servicio para un servicio de Azure para la subred. Azure enruta el tráfico de la subred a una dirección IP pública del servicio, a través de la red de la infraestructura de Azure. El prefijo es más largo que 0.0.0.0/0 y no está dentro de los prefijos de dirección de las restantes rutas. Se ha creado un punto de conexión de servicio para cumplir el [requisito](#requirements) 3, con el fin de habilitar que el tráfico destinado a Azure Storage que fluya directamente a dicho servicio.
11. Azure ha agregado automáticamente esta ruta a la tabla de rutas de todas las subredes de *Virtual-network-1* y *Virtual-network-2*. El prefijo de dirección 0.0.0.0/0 es el más corto. Todo el tráfico enviado a direcciones de un prefijo de dirección más largo se enrutan en función de otras rutas. De forma predeterminada, Azure enruta todo el tráfico destinado a aquellas direcciones que no sean las especificadas en una de las otras rutas a Internet. Azure ha cambiado automáticamente el estado de *Activo* a *No válido* para la subred *Subnet1* cuando una ruta definida por el usuario para el prefijo de dirección 0.0.0.0/0 (ID12) se ha asociado a la subred. El estado de esta ruta sigue siendo *Activo* para las restantes subredes de ambas redes virtuales, ya que la ruta no está asociada a otras subredes de otras redes virtuales.
12. Azure agregó esta ruta cuando una ruta definida por el usuario para el prefijo de red 0.0.0.0/0 se asoció a la subred *Subnet1*. La ruta definida por el usuario especifica 10.0.100.4 como dirección IP de la aplicación virtual. Esta ruta no está asociada a *Subnet2*, por lo que no aparece en la tabla de rutas de *Subnet2*. Todo el tráfico de cualquier dirección no incluida en los prefijos de dirección de cualquiera de las otras rutas se envía a la aplicación virtual. La adición de esta ruta ha cambiado el estado de la ruta predeterminada para el prefijo de dirección 0.0.0.0/0 (ID11) de *Activo* a *No válido* para *Subnet1*, porque una ruta definida por el usuario reemplaza a una ruta predeterminada. Esta ruta existe para cumplir el [requisito](#requirements) 3.

#### <a name="subnet2"></a>Subnet2

La tabla de rutas de *Subnet2* en la imagen contiene las rutas siguientes:

|Origen  |Estado  |Prefijos de dirección    |Tipo de próximo salto             |Dirección IP de siguiente salto|
|------- |-------|------              |-------                   |--------           
|Valor predeterminado |Active |10.0.0.0/16         |Red virtual           |                   |
|Valor predeterminado |Active |10.1.0.0/16         |Emparejamiento de VNET              |                   |
|Valor predeterminado |Active |10.2.0.0/16         |Emparejamiento de VNET              |                   |
|Valor predeterminado |Active |10.10.0.0/16        |Puerta de enlace de red virtual   |[X.X.X.X]          |
|Valor predeterminado |Active |0.0.0.0/0           |Internet                  |                   |
|Valor predeterminado |Active |10.0.0.0/8          |None                      |                   |
|Valor predeterminado |Active |100.64.0.0/10       |None                      |                   |
|Valor predeterminado |Active |172.16.0.0/12       |None                      |                   |
|Valor predeterminado |Active |192.168.0.0/16      |None                      |                   |

La tabla de rutas de *Subnet2* contiene todas las rutas predeterminadas creadas por Azure y el emparejamiento de VNet opcional y las rutas opcionales de la puerta de enlace de red virtual. Azure ha agregado las rutas opcionales a todas las subredes de la red virtual cuando tanto la puerta de enlace como el emparejamiento se han agregado a la red virtual. Azure ha quitado las rutas de los prefijos de dirección 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 y 100.64.0.0/10 de la tabla de rutas de *Subnet1* cuando la ruta definida por el usuario del prefijo de dirección 0.0.0.0/0 se ha agregado a *Subnet1*.  

## <a name="next-steps"></a>pasos siguientes

- [Creación de una ruta definida por el usuario - Azure Portal](create-user-defined-route-portal.md)
- [Configuración de BGP para Azure VPN Gateway con PowerShell](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Uso de BGP con ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#route-aggregation-and-prefix-limits)
- [Solución de problemas de rutas mediante Azure Portal](virtual-network-routes-troubleshoot-portal.md). Las tablas de rutas definidas por el usuario solo muestran las rutas definidas por el usuario, no las rutas predeterminada y de BGP de una subred. La visualización de todas las rutas muestra las rutas predeterminada, de BGP y definidas por el usuario de la subred en que se encuentra una interfaz de red.
- [Determine el tipo de próximo salto](../network-watcher/network-watcher-check-next-hop-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) entre una máquina virtual y una dirección IP de destino. La característica de próximo salto de Azure Network Watcher permite determinar si el tráfico sale de una subred y se enruta al lugar en que cree que debería estar.
