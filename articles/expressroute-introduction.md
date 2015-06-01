<properties 
   pageTitle="Introducción a ExpressRoute"
   description="Esta página proporciona una visión general del servicio ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="04/29/2015"
   ms.author="cherylmc"/>

# Información técnica de ExpressRoute

Microsoft Azure ExpressRoute le permite crear conexiones privadas entre los centros de datos de Microsoft y la infraestructura local o en un entorno de coubicación. Con ExpressRoute, se pueden establecer conexiones con servicios en la nube de Microsoft, como Microsoft Azure y Office 365 en una instalación de coubicación de asociado de ExpressRoute o se pueden conectar directamente desde la red WAN existente (como una VPN MPLS de un proveedor de servicios de red).
 
Las conexiones ExpressRoute ofrecen más confiabilidad, velocidad y seguridad, y una menor latencia que las conexiones a Internet típicas. En algunos casos, el uso de conexiones ExpressRoute para transferir datos entre la red local y Azure también puede aportar beneficios económicos importantes. Si ya ha creado una conexión entre instalaciones desde la red local, puede migrar a una conexión ExpressRoute manteniendo intacta la red virtual.

Consulte [P+F de ExpressRoute](expressroute-faqs.md) para obtener más detalles.

## ¿Cómo funciona una conexión ExpressRoute?

Para conectar la WAN a los servicios en la nube de Microsoft, debe solicitar un circuito dedicado y habilitarlo a través de un proveedor de conectividad. Hay dos tipos de proveedor de conectividad entre los que elegir: capa directa 3 a través de un proveedor de Exchange (EXP) o capa 3 a través de un proveedor de servicios de red (NSP). Puede elegir habilitar uno o ambos tipos de conectividad entre WAN y la nube de Microsoft.

## Proveedores de Exchange y proveedores de servicios de red
Los proveedores de ExpressRoute se clasifican como proveedores de servicios de red (NSP) y proveedores de Exchange (EXP).

![](./media/expressroute-introduction/expressroute-nsp-exp.png)


| |**Proveedor de Exchange**|**Proveedor de servicios de red**|
|---|---|---|
|**Modelo de conectividad normal**| Vínculos Ethernet punto a punto o conectividad en un intercambio en la nube | Conectividad cualquiera a través de una VPN telco |
|**Anchos de banda compatibles**|200 Mbps, 500 Mbps, 1 Gbps y 10 Gbps|10 Mbps, 50 Mbps, 100 Mbps, 500 Mbps y 1 Gbps|
|**Proveedores de conectividad**|[Página de proveedores de Exchange](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_EXP)|[Página de proveedores de servicios de red](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_NSP)|
|**Enrutamiento**|Sesiones BGP directamente con los enrutadores de borde de cliente| Sesiones BGP con telco|
|**Precios**|[Precios de EXP](http://azure.microsoft.com/pricing/details/expressroute/)|[Precios de NSP](http://azure.microsoft.com/pricing/details/expressroute/)|

### Proveedores de Exchange (EXP)
Nos asociamos con proveedores de servicios de Exchange en la nube como grupo Equinix y TeleCity, y también con proveedores de servicios de conectividad de punto a punto como Cole y Level 3, para ofrecer conectividad entre Azure y las instalaciones del cliente. Ofrecemos anchos de banda de circuito de 200 Mbps a 10 Gbps (200 Mbps, 500 Mbps, 1 Gbps y 10 Gbps).

Si desea una conexión de capa directa 3 a través de un proveedor de Exchange, puede realizar una de tres formas:

- Puede coexistir con los intercambios en la nube como Cloud Exchange de Equinix o Cloud IX de TeleCity en las ubicaciones en las que ofrecemos servicios. En casos como este le solicitarán conectividad redundante para el intercambio en la nube. 
- Puede trabajar con proveedores de Ethernet como Level 3 para disponer de una configuración de circuitos de Ethernet entre los centros de datos y Microsoft. 
- Puede trabajar con su proveedor de conectividad local para adquirir conectividad redundante en la instalación de proveedor de Exchange más cercana y conectarse al intercambio de la nube.

Es necesario tener conectividad redundante para cumplir con los requisitos para nuestro SLA. No se admite la conectividad directa con el borde de Microsoft. Siempre se habilitarán circuitos dedicados a través de un proveedor de Ethernet o el intercambio en la nube local. Mientras se configura la conectividad de la capa 2 entre Microsoft y la red, no se admitirá la ampliación del dominio de la capa 2. Debe configurar las sesiones de enrutamiento redundantes entre los enrutadores de borde y los enrutadores de borde de Microsoft para que tengan la conectividad de capa 3.

Para obtener más información sobre la configuración y para ver ejemplos del mundo real, puede seguir esta guía paso a paso: [Configuración de una conexión ExpressRoute a través de un EXP](expressroute-configuring-exps.md).


### Proveedores de servicios de red (NSP)

Nos asociamos con compañías telefónicas como AT&T y British Telecom para ofrecer conectividad entre Azure y la WAN. Ofrecemos anchos de banda de circuito de 10 Mbps a 1 Gbps (10 Mbps, 50 Mbps, 100 Gbps, 500 Gbps y 1 Gbps).

Si utiliza los servicios VPN desde cualquiera de los proveedores de servicios de red con los que colaboramos, pueden ampliar las redes en Azure sin tener que implementar hardware nuevo o realizar cambios de configuración importantes en sus redes existentes.

Para obtener más información sobre la configuración y para ver ejemplos del mundo real, puede seguir esta guía paso a paso: [Configuración de circuitos ExpressRoute a través de NSP](expressroute-configuring-nsps.md).

## Emparejamientos de ExpressRoute
La ilustración siguiente proporciona una representación lógica de conectividad entre la WAN y Microsoft. Para conectar la WAN a Microsoft a través de un proveedor de conectividad (NSP/EXP), debe solicitar un "circuito dedicado". Un "circuito dedicado" representa una conexión lógica entre la WAN y Microsoft a través del proveedor de conectividad. Puede solicitar varios circuitos dedicados, cada uno de ellos puede estar en la misma región o en distintas, y se puede conectar a la WAN a través de otros proveedores de servicios.

![](./media/expressroute-introduction/expressroute-basic.png)

Un circuito dedicado tendrá varios dominios de enrutamiento asociados: público, privado y Microsoft. Cada uno de los dominios de enrutamiento tiene una configuración idéntica en un par de enrutadores (configuración en activo-activo o de uso compartido de carga) de alta disponibilidad.

![](./media/expressroute-introduction/expressroute-peerings.png)


### Emparejamiento privado
Servicios de proceso de Azure, concretamente las máquinas virtuales (IaaS) y los servicios en la nube (PaaS) implementados dentro de una red virtual, pueden estar conectados a través del dominio de emparejamiento privado. El dominio de emparejamiento privado se considera una extensión confiable de la red principal en Microsoft Azure. Puede configurar una conectividad bidireccional entre la red principal y las redes virtuales de Azure (redes virtuales). Esto le permitirá conectarse a máquinas virtuales y servicios en la nube directamente en sus direcciones IP privadas.

Puede conectar más de una red virtual al dominio de emparejamiento privado. Revise la [página de P+G](expressroute-faqs.md) para obtener información sobre los límites y las limitaciones.
  

### Emparejamiento público
Se ofrecen los servicios como Almacenamiento de Azure, Bases de datos SQL y Sitios web en direcciones IP públicas. Puede conectarse de forma privada a servicios hospedados en direcciones IP públicas (incluida la VIP de servicios en la nube) a través del dominio de enrutamiento de emparejamiento público. Puede conectarse al dominio de emparejamiento público a la Extranet y conectarse a todos los servicios de Azure en sus direcciones IP públicas desde la WAN sin tener que conectarse a través de Internet. La conectividad siempre se inicia desde la WAN a los servicios de Microsoft Azure. Servicios de Microsoft Azure no podrá iniciar conexiones en la red a través de este dominio de enrutamiento. Una vez que se habilita el emparejamiento público, podrá conectarse a todos los servicios de Azure (no es posible elegir selectivamente servicios para los que se anuncian las rutas). Puede revisar la lista de prefijos que se anuncian a través de este emparejamiento en la página [Intervalos de direcciones IP de centro de datos de Microsoft Azure](http://www.microsoft.com/es-es/download/details.aspx?id=41653). Puede definir filtros de ruta personalizados dentro de la red para usar solo las rutas que necesita.

Revise la [página de P+F](expressroute-faqs.md) para obtener más información sobre servicios compatibles a través del dominio de enrutamiento de emparejamiento público.
 
### Emparejamiento de Microsoft
La conectividad con los demás servicios en línea de Microsoft (por ejemplo, servicios de Office 365) se llevará a cabo a través del emparejamiento de Microsoft. Habilitamos una conectividad bidireccional entre los servicios en la nube WAN y Microsoft a través del dominio de enrutamiento de emparejamiento de Microsoft. Debe conectarse a los servicios en la nube de Microsoft a través de direcciones IP públicas que pertenecen a usted o su proveedor de conectividad, y debe cumplir todas las reglas que se definen. Revise la página [Requisitos previos de ExpressRoute](expressroute-prerequisites.md) para obtener más información.

Revise la página [P+F](expressroute-faqs.md) para obtener más información sobre servicios compatibles, los costes y detalles de configuración. Revise la página [Ubicaciones de ExpressRoute](expressroute-locations.md) para obtener información sobre la lista de proveedores de conectividad que ofrecen soporte técnico de emparejamiento de Microsoft.


La tabla siguiente comparan los tres dominios de enrutamiento.

||**Emparejamiento privado**|**Emparejamiento público**|**Emparejamiento de Microsoft**| 
|---|---|---|---| 
|**N.º máx. de prefijos compatibles por emparejamiento**|4000 de forma predeterminada, 10.000 con ExpressRoute Premium|4000 de forma predeterminada, 10.000 con ExpressRoute Premium|200| 
|**Rangos de direcciones IP compatibles**|Cualquier dirección IPv4 válida dentro de la WAN|Direcciones IPv4 públicas suyas o del proveedor de conectividad|Direcciones IPv4 públicas de usted o del proveedor de conectividad| 
|**Requisitos de número de AS**|Números de AS privados y públicos . El cliente debe poseer un número de AS público. | Números de AS privados y públicos. El cliente debe poseer un número de AS público. | Solo números de AS públicos. El número de AS debe validarse con los registros de enrutamiento para validar la propiedad.| 
|**Dirección IP de la interfaz de enrutamiento**|RFC1918 y direcciones IP públicas|Direcciones IP públicas registradas para los clientes / NSP en registros de enrutamiento.| Direcciones IP públicas registradas para los clientes / NSP en registros de enrutamiento.| 
|**Compatibilidad con hash MD5**| Sí|Sí|Sí|

Puede elegir habilitar uno o varios de los dominios de enrutamiento como parte de un circuito dedicado. Puede elegir que todos los dominios de enrutamiento se coloquen en la misma VPN (para el caso NSP) si desean insertarlos en un único dominio de enrutamiento. También puede colocarlos en diferentes dominios de enrutamiento parecidos al diagrama anterior. La configuración recomendada es el emparejamiento privado conectado directamente a la red principal y los vínculos de emparejamiento de Microsoft y público conectados a la Extranet.
 
Si se decide tener las tres sesiones de emparejamiento, debe tener tres pares de sesiones de BGP (un par para cada tipo de emparejamiento). Los pares de sesión de BGP proporcionan un vínculo de alta disponibilidad. Si se conecta a través de EXP, será responsable de configurar y administrar el enrutamiento (a menos que EXP ofrezca administrar el enrutamiento por usted). Si elige conectarse a través de NSP, puede confiar en NSP para administrar el enrutamiento por usted. Puede obtener más información revisando los flujos de trabajo para la configuración de ExpressRoute

- [Configuración de una conexión ExpressRoute a través de un proveedor de servicios de red](expressroute-configuring-nsps.md)
- [Configuración de una conexión ExpressRoute a través de un proveedor de Exchange](expressroute-configuring-exps.md)

## Pasos siguientes

- [Ubicaciones y proveedores de servicios de ExpressRoute](expressroute-locations.md) 
- [Configuración de una conexión ExpressRoute a través de un proveedor de servicios de red](expressroute-configuring-nsps.md)
- [Configuración de una conexión ExpressRoute a través de un proveedor de Exchange](expressroute-configuring-exps.md)

<!--HONumber=54-->