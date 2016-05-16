<properties 
   pageTitle="Planeamiento y diseño de puerta de enlace de VPN| Microsoft Azure"
   description="Obtenga información acerca del planeamiento y diseño de puerta de enlace de VPN para conexiones locales, híbridas y de red virtual a red virtual"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/20/2016"
   ms.author="cherylmc"/>

# Planeamiento y diseño de puerta de enlace de VPN

El planeamiento y el diseño de las configuraciones entre locales y de red virtual a red virtual pueden ser muy simples o bastante complejos, según las necesidades de la red. Este artículo le guiará a través de las consideraciones de diseño y planificación básicas.

## Planificación


### <a name="compare"></a>1. Comparar las opciones de conectividad entre locales

Si decidió que desea conectar los sitios locales de manera a una red virtual de manera segura, existen tres maneras distintas para hacerlo: de sitio a sitio, de punto a sitio y ExpressRoute. Compare las distintas conexiones locales que se encuentran disponibles. La opción que elija dependerá de varios factores, como, por ejemplo:


- ¿Qué tipo de rendimiento requiere la solución?
- ¿Desea comunicarse a través de la red pública de Internet mediante una VPN segura o a través de una conexión privada?
- ¿Dispone de una dirección IP pública?
- ¿Planea usar un dispositivo VPN? Si es así, ¿es compatible?
- ¿Va a conectar solo algunos equipos o desea establecer una conexión persistente para su sitio?
- ¿Qué tipo de puerta de enlace de VPN se necesita para la solución que desea crear?
- ¿Qué SKU de puerta de enlace se debe usar?


La tabla siguiente puede ayudarle a decidir la mejor opción de conectividad para su solución.


[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]



### <a name="gwrequire"></a>2. Considerar los requisitos de puerta de enlace por tipo de VPN y SKU


Cuando se crea una puerta de enlace de VPN, debe especificar la SKU de puerta de enlace que desea usar. Hay tres SKU de puerta de enlace de VPN:

- Básica
- Standard
- Alto rendimiento

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]



### 3\. Tipos de puerta de enlace y estimaciones de rendimiento agregado

La tabla siguiente muestra los tipos de puerta de enlace y el rendimiento agregado estimado. El rendimiento agregado estimado puede ser un factor decisivo en el diseño. Los precios difieren entre las SKU de puerta de enlace. Para obtener información acerca de los precios, consulte [Precios de puertas de enlace de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Esta tabla se aplica a los modelos de implementación del Administrador de recursos y clásico.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]



### <a name="wf"></a>4. Flujo de trabajo

La lista siguiente describe el flujo de trabajo común para la conectividad en la nube:

1.	Diseñe y planee la topología de conectividad y enumere los espacios de direcciones para todas las redes que desea conectar.
2.	Cree una red virtual de Azure. 
3.	Cree una puerta de enlace de VPN para la red virtual.
4.	Cree y configure conexiones a redes locales o a otras redes virtuales (según sea necesario).
5.	Cree y configure una conexión de punto a sitio para la puerta de enlace de VPN de Azure (según sea necesario).
 

## Diseño

### 1\. Seleccione una topología de conexión

Para comenzar, consulte el artículo [Topologías de conexión](vpn-gateway-topology.md). El artículo contiene diagramas básicos, los modelos de implementación de cada topología (Resource Manager o clásico) y las herramientas de implementación que puede usar para implementar la configuración.

### 2\. Comprenda los aspectos básicos del diseño

Las secciones siguientes analizan los aspectos básicos de la puerta de enlace de VPN. Además, deseará considerar las [limitaciones de los servicios de red](../articles/azure-subscription-service-limits.md#networking-limits).


#### <a name="subnets"></a>Acerca de las subredes

Cuando planea y diseña la conexión que funcionará mejor en su entorno, es muy importante que considere los intervalos de direcciones IP y las subredes que tendrá disponibles para usar.

Deberá crear una subred de puerta de enlace para la red virtual para configurar una puerta de enlace de VPN. Para que funcionen correctamente, todas las subredes de puerta de enlace se deben llamar GatewaySubnet. Asegúrese de no asignar un nombre distinto a la subred de puerta de enlace y no implemente máquinas virtuales ni ningún otro elemento en la subred de puerta de enlace. Para obtener más información acerca de las subredes de puerta de enlace, consulte la sección [Subred de puerta de enlace](vpn-gateway-about-vpngateways.md#gwsub) del artículo Acerca de las puertas de enlace de VPN.

Cuando crea conexiones, en muchos casos deberá estar atento a los intervalos de direcciones de subredes que se superponen entre las conexiones. Se habla de subred superpuesta cuando una red virtual o una ubicación local contiene el mismo espacio de direcciones que la otra ubicación. Esto significa que será necesario que los ingenieros de redes de sus redes locales definan un intervalo que puede usar para las subredes o el espacio de direcciones IP de Azure. Necesitará espacio de direcciones que no esté siendo usado en la red local.

También es importante evitar las subredes superpuestas cuando se trabaja con conexiones de red virtual a red virtual. La creación de una conexión de red virtual a red virtual presentará errores si las subredes se superponen y si existe una misma dirección IP en la red virtual de envío y en la red virtual de destino. En este caso, Azure no puede enrutar los datos a la otra red virtual porque la dirección de destino forma parte de la red virtual de envío.



#### <a name="local"></a>Acerca de las puertas de enlace de red local

La puerta de enlace de red local suele hacer referencia a la ubicación local. En el modelo de implementación clásica, la puerta de enlace de red local se conoce como un sitio local. Debe asignar un nombre a la puerta de enlace de red local, así como la dirección IP pública del dispositivo VPN local, y especificar los prefijos de dirección que se encuentran en la ubicación local. Azure examinará los prefijos de dirección de destino para el tráfico de red, consultará la configuración que especificó para la puerta de enlace de red local, y enrutará los paquetes según corresponda. Puede modificar estos prefijos de dirección según sea necesario. Para obtener más información acerca de las puertas de enlace de red local, consulte la sección [Puertas de enlace de red local](vpn-gateway-about-vpngateways.md#lng) del artículo Acerca de las puertas de enlace de VPN.


#### <a name="gwtype"></a>Acerca de los tipos de puerta de enlace

Seleccionar el tipo de puerta de enlace correcto para su topología es fundamental. La puerta de enlace no funcionará correctamente si selecciona un tipo incorrecto. El tipo de puerta de enlace especifica cómo se conecta la puerta de enlace y es un valor de configuración necesario para el modelo de implementación de Resource Manager.

Los tipos de puerta de enlace son los siguientes:

- VPN
- ExpressRoute

#### Acerca de los tipos de conexión

Cada configuración requiere un tipo de conexión específico. Los tipos de conexión son los siguientes:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient


#### <a name="vpntype"></a>Acerca de los tipos de VPN

Cada configuración requiere un tipo específico de VPN para funcionar. Si combina dos configuraciones, como la creación de una conexión de sitio a sitio y una conexión de punto a sitio a la misma red virtual, debe usar un tipo de VPN que cumpla ambos requisitos de conexión. En el caso de las conexiones de punto a sitio y de sitio a sitio coexistentes, debe usar un tipo de VPN basado en la ruta cuando se trabaja con el modelo de implementación de Resource Manager, o una puerta de enlace dinámica si trabaja con el modo de implementación clásica.

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Las tablas siguientes muestran el tipo de VPM según cómo se asigna a cada configuración de conexión. Asegúrese de que el tipo de VPN correspondiente a su puerta de enlace coincide con la configuración que desea crear.


[AZURE.INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="devices"></a>3. Seleccionar un dispositivo VPN para conexiones de sitio a sitio

Para configurar una conexión de sitio a sitio, independientemente del modelo de implementación, necesitará los siguientes elementos:

- Un dispositivo VPN compatible con puertas de enlace de VPN de Azure.
- Una dirección IP IPv4 orientada al público que no se encuentre detrás de NAT.

Necesitará contar con experiencia en la configuración del dispositivo VPN para crear una configuración de sitio a sitio. Para obtener más información acerca de los dispositivos VPN, consulte [Acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md). El artículo sobre dispositivos VPN contiene información sobre los dispositivos validados, requisitos de los dispositivos no validados y vínculos a los documentos sobre configuración de dispositivo correspondiente a cada dispositivo, si se encuentran disponibles.

### <a name="forcedtunnel"></a>4. Considerar el enrutamiento de túnel forzado

La tunelización forzada se puede configurar en la mayoría de las configuraciones. La tunelización forzada permite redirigir o forzar todo el tráfico vinculado a Internet de vuelta a su ubicación local a través de un túnel VPN de sitio a sitio para inspección y auditoría. Se trata de un requisito de seguridad crítico en la mayoría de las directivas de las empresas de TI.

Sin la tunelización forzada, el tráfico vinculado a Internet desde las máquinas virtuales en Azure siempre atravesará desde la infraestructura de red de Azure directamente a Internet, sin la opción que permite inspeccionar o auditar el tráfico. Un acceso no autorizado a Internet puede provocar la divulgación de información u otros tipos de infracciones de seguridad. Para obtener más información sobre cómo configurar la tunelización forzada, consulte [Configuración de la tunelización forzada para el modelo de implementación clásica](vpn-gateway-about-forced-tunneling.md) y [Configuración de la tunelización forzada para el modelo de implementación de Resource Manager](vpn-gateway-about-forced-tunneling.md).

**Diagrama de tunelización forzada**

![Conexión de tunelización forzada](./media/vpn-gateway-plan-design/forced-tunnel.png "tunelización forzada")


Esta tabla muestra el modelo de implementación para el que está disponible la tunelización forzada, las herramientas de implementación que puede usar para configurar la tunelización forzada y vínculos directos a un artículo, si está disponible. Actualizamos las tablas con frecuencia a medida que nuevos artículos están disponibles para su uso.

[AZURE.INCLUDE [vpn-gateway-table-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)]



## Pasos siguientes

Consulte las [Preguntas más frecuentes sobre la puerta de enlace de VPN](vpn-gateway-vpn-faq.md) y [Acerca de las puertas de enlace de VPN](vpn-gateway-about-vpngateways.md) para obtener más información que pueda ayudarle con el diseño. Para obtener más información sobre las topologías de conexión, consulte [Topologías de conexión](vpn-gateway-topology.md).

<!---HONumber=AcomDC_0504_2016-->