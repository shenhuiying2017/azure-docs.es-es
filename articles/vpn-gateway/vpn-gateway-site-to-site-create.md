<properties
   pageTitle="Creación de una red virtual con una conexión VPN de sitio a sitio mediante el portal de administración de Azure | Microsoft Azure"
   description="Cree una red virtual con una conexión VPN de sitio a sitio para configuraciones entre entornos e híbridas."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carolz"
   editor=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/11/2015"
   ms.author="cherylmc"/>

# Crear una red virtual con una conexión VPN de sitio a sitio mediante el portal de Azure

> [AZURE.SELECTOR]
- [Azure portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Este artículo le guiará a través de la creación de una red virtual clásica y una conexión VPN de sitio a sitio con la red local.

Actualmente, Azure tiene dos modelos de implementación: el modelo de implementación clásica y el modelo de implementación Administrador de recursos de Azure. Los pasos de configuración difieren en función del modelo que se usó para implementar la red virtual.

Estas instrucciones se aplican al modelo de implementación clásica. Si desea crear una conexión de puerta de enlace de VPN de sitio a sitio mediante el modelo del Administrador de recursos de Azure, consulte [Crear una conexión VPN de sitio a sitio mediante el Administrador de recursos de Azure y PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).


## Antes de empezar

- Compruebe que el dispositivo VPN que desea usar cumple los requisitos necesarios para crear una conexión de red virtual entre entornos. Consulte [Información acerca de los dispositivos VPN para conexiones de red virtual](vpn-gateway-about-vpn-devices.md) para obtener más información.

- Obtenga una dirección IP IPv4 con orientación externa para el dispositivo VPN. Esta dirección IP es necesaria para una configuración de sitio a sitio y se usa para el dispositivo VPN, que no se encuentra detrás de un NAT.

>[AZURE.IMPORTANT]Si no está familiarizado con la configuración de su dispositivo VPN o con los intervalos de direcciones IP, ubicados en la configuración de la red local, necesitará trabajar con alguien que pueda proporcionar estos detalles.

## Creación de la red virtual

1. Inicie sesión en el **Portal de Azure**.

2. En la esquina inferior izquierda de la pantalla, haga clic en **Nuevo**. En el panel de navegación, haga clic en **Servicios de red** y, a continuación, haga clic en **Red virtual**. Haga clic en **Creación personalizada** para iniciar el Asistente para configuración.

3. Rellene la información en las páginas siguientes para crear la red virtual.

## Página de detalles de la red virtual

Escriba la siguiente información.

- **Nombre**: Nombre de la red virtual. Por ejemplo, *EastUSVNet*. Usará este nombre de red virtual al implementar las máquinas virtuales y las instancias PaaS, por lo que probablemente no querrá que este nombre sea muy complicado.
- **Ubicación**: La ubicación está directamente relacionada con la ubicación física (región) en la que desea que residan los recursos (máquinas virtuales). Por ejemplo, si desea que las máquinas virtuales que implementa en la red virtual se encuentren físicamente en el *Este de EE. UU.*, seleccione esa ubicación. No se puede cambiar la región asociada a la red virtual después de crearla.

## Página de servidores DNS y conectividad VPN
Especifique la siguiente información y, a continuación, haga clic en la flecha siguiente en el ángulo inferior derecho.

- **Servidores DNS**: escriba el nombre del servidor DNS y la dirección IP o seleccione un servidor DNS previamente registrado del menú contextual. Este valor no crea un servidor DNS; permite especificar los servidores DNS que desea usar para la resolución de nombres para esta red virtual.
- **Configurar VPN de sitio a sitio**: seleccione la casilla **Configurar una VPN de sitio a sitio**.
- **Red local**: una red local representa la ubicación física local. Puede seleccionar una red local que haya creado previamente o puede crear una nueva. Sin embargo, si decide utilizar una red local creada anteriormente, querrá ir a la página de configuración de **Redes locales** y asegurare de que la dirección IP del dispositivo VPN (dirección IPv4 pública) para el dispositivo VPN que se utiliza para esta conexión es precisa.

## Página de conectividad sitio a sitio
Si va a crear una nueva red local, verá la página **Conectividad de sitio a sitio**. Si desea usar una red local que creó anteriormente, esta página no aparecerá en el asistente y puede pasar a la siguiente sección.

Especifique la siguiente información y, a continuación, haga clic en la flecha siguiente.

- 	**Nombre**: el nombre que quiere usar para el sitio de red local.
- 	**Dirección IP del dispositivo VPN**: se trata de la dirección IPv4 pública del dispositivo VPN local que se va a utilizar para conectarse a Azure. El dispositivo VPN no se encuentra detrás de un NAT.
- 	**Espacio de direcciones**: incluidas la dirección IP de inicio y el CIDR (recuento de direcciones). Aquí se especifican los intervalos de direcciones que desea envían a través de la puerta de enlace de red virtual a su ubicación local. Si una dirección IP de destino se encuentra dentro de los intervalos que especifique aquí, se enrutará a través de la puerta de enlace de red virtual.
- 	**Agregar espacio de direcciones**: si tiene varios intervalos de direcciones que desea enviar a través de la puerta de enlace de red virtual, especifique aquí cada intervalo de direcciones adicional. Puede agregar o quitar rangos más adelante en la página **Red Local**.

## Página de espacios de direcciones de la red virtual
Especifique el intervalo de direcciones que desea usar para la red virtual. Estas son las direcciones IP dinámicas (DIPS) que se asignarán a las máquinas virtuales y a las demás instancias de rol implementadas en esta red virtual.

Es especialmente importante seleccionar un intervalo que no se superponga a ninguno de los intervalos usados para la red local. Necesitará coordinarse con el administrador de la red. Es posible que el administrador necesite definir un intervalo de direcciones IP desde el espacio de direcciones de la red local para que los use en la red virtual.

Escriba la información siguiente y, a continuación, haga clic en la marca de verificación situada en la esquina inferior derecha para configurar la red.

- **Espacio de direcciones**: incluidas la dirección IP de inicio y el recuento de direcciones. Compruebe que los espacios de direcciones especificados no se superponen con los espacios de direcciones que existen en la red local.
- **Agregar subred**: incluidas la dirección IP de inicio y el recuento de direcciones. No se necesitan subredes adicionales, pero puede que desee crear una subred independiente para las máquinas virtuales que tendrán DIP estáticas. O bien, puede que desee que las máquinas virtuales se encuentren en una subred independiente de las demás instancias de rol.
- **Agregar subred de puerta de enlace**: haga clic para agregar la subred de puerta de enlace. La subred de puerta de enlace solo se usa para la puerta de enlace de red virtual y es obligatoria para esta configuración.

Haga clic en la marca de verificación de la parte inferior derecha de la página y se empezará a crear la red virtual. Cuando finalice, verá **creado** listado bajo **estado** en la página **Redes** del portal de Azure. Una vez creada la red virtual, puede configurar la puerta de enlace de red virtual.

## Configuración de la puerta de enlace de la red virtual

A continuación, configurará la puerta de enlace de la red virtual con el fin de crear una conexión segura de sitio a sitio. Consulte [Configurar una puerta de enlace de red virtual en el portal de Azure](vpn-gateway-configure-vpn-gateway-mp.md).

## Pasos siguientes

Puede obtener más información acerca de la conectividad de red virtual entre locales en este artículo: [Información sobre la conectividad segura entre locales de redes virtuales](vpn-gateway-cross-premises-options.md).

Si desea configurar una conexión VPN de punto a sitio, consulte [Configurar una conexión VPN de punto a sitio](vpn-gateway-point-to-site-create.md)

Puede agregar máquinas virtuales a la red virtual. Consulte [Creación de una máquina virtual personalizada](../virtual-machines/virtual-machines-create-custom.md).

Si desea configurar una conexión VNet mediante RRAS, consulte [Conexión VPN sitio a sitio en la Red virtual de Azure mediante el Servicio de enrutamiento y acceso remoto (RRAS) de Windows Server 2012](https://msdn.microsoft.com/library/dn636917.aspx).

Si desea configurar una conexión entre una red virtual clásica y una red virtual creada con el modo de Administrador de recursos de Azure, consulte [Conectar redes virtuales clásicas con redes virtuales del Administrador de recursos de Azure](../virtual-network/virtual-networks-arm-asm-s2s-howto.md).

<!---HONumber=August15_HO7-->