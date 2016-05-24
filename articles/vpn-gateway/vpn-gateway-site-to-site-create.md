<properties
   pageTitle="Creación de una red virtual con una conexión VPN de sitio a sitio mediante el Portal de Azure clásico | Microsoft Azure"
   description="Cree una red virtual con una conexión de puerta de enlace de VPN S2S para configuraciones entre entornos e híbridas con el modelo de implementación clásico."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/13/2016"
   ms.author="cherylmc"/>

# Creación de una red virtual con una conexión VPN de sitio a sitio mediante el Portal de Azure clásico

> [AZURE.SELECTOR]
- [Portal de Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Portal de Azure clásico](vpn-gateway-site-to-site-create.md)
- [PowerShell: administrador de recursos](vpn-gateway-create-site-to-site-rm-powershell.md)


Este artículo le guiará a través de la creación de una red virtual y una conexión VPN de sitio a sitio con la red local. Se pueden utilizar conexiones de sitio a sitio para las configuraciones híbridas y entre locales. Este artículo se aplica al modelo de implementación clásico y utiliza el Portal de Azure clásico.


**Información sobre los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Diagrama de conexión
 
![Diagrama de sitio a sitio](./media/vpn-gateway-site-to-site-create/site2site.png "De sitio a sitio")

**Modelos de implementación y herramientas para las conexiones de sitio a sitio**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

Si desea conectar las redes virtuales entre sí pero no está creando una conexión a una ubicación local, consulte [Configuración de una conexión de red virtual a red virtual para el modelo de implementación clásico](virtual-networks-configure-vnet-to-vnet-connection.md). Si busca un tipo diferente de configuración de la conexión, consulte el artículo [Topologías de conexión de la puerta de enlace de VPN de Azure](vpn-gateway-topology.md).

 
## Antes de empezar

Antes de comenzar con la configuración, comprueba que dispones de los elementos siguientes:

- Un dispositivo VPN compatible y alguien que pueda configurarlo. Consulte [Acerca de los dispositivos VPN para conexiones de red virtual de sitio a sitio](vpn-gateway-about-vpn-devices.md). Si no está familiarizado con la configuración de su dispositivo VPN o con los intervalos de direcciones IP en la configuración de la red local, tendrá que trabajar con alguien que pueda proporcionarle estos detalles.

-  Una dirección IP pública externa para el dispositivo VPN. Esta dirección IP no puede estar detrás de un NAT.

- Una suscripción de Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o registrarse para obtener una [cuenta gratis](https://azure.microsoft.com/pricing/free-trial/).


## Creación de la red virtual

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/).

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
- 	**Espacio de direcciones**: incluidas la dirección IP de inicio y el CIDR (recuento de direcciones). Aquí se especifican los intervalos de direcciones a través de las que desea que se realicen los envíos de la puerta de enlace de red virtual a su ubicación local. Si una dirección IP de destino se encuentra dentro de los intervalos que especifique aquí, se enrutará a través de la puerta de enlace de red virtual.
- 	**Agregar espacio de direcciones**: si tiene varios intervalos de direcciones que desea enviar a través de la puerta de enlace de red virtual, especifique aquí cada intervalo de direcciones adicional. Puede agregar o quitar rangos más adelante en la página **Red Local**.

## Página de espacios de direcciones de la red virtual

Especifique el intervalo de direcciones que desea usar para la red virtual. Estas son las direcciones IP dinámicas (DIPS) que se asignarán a las máquinas virtuales y a las demás instancias de rol implementadas en esta red virtual.

Es especialmente importante seleccionar un intervalo que no se superponga a ninguno de los intervalos usados para la red local. Necesitará coordinarse con el administrador de la red. Es posible que el administrador necesite definir un intervalo de direcciones IP desde el espacio de direcciones de la red local para que los use en la red virtual.

Escriba la información siguiente y, a continuación, haga clic en la marca de verificación situada en la esquina inferior derecha para configurar la red.

- **Espacio de direcciones**: incluidas la dirección IP de inicio y el recuento de direcciones. Compruebe que los espacios de direcciones especificados no se superponen con los espacios de direcciones que existen en la red local.
- **Agregar subred**: incluidas la dirección IP de inicio y el recuento de direcciones. No se necesitan subredes adicionales, pero puede que desee crear una subred independiente para las máquinas virtuales que tendrán DIP estáticas. O bien, puede que desee que las máquinas virtuales se encuentren en una subred independiente de las demás instancias de rol.
- **Agregar subred de puerta de enlace**: haga clic para agregar la subred de puerta de enlace. La subred de puerta de enlace solo se usa para la puerta de enlace de red virtual y es obligatoria para esta configuración.

Haga clic en la marca de verificación de la parte inferior derecha de la página y se empezará a crear la red virtual. Cuando termine, verá el mensaje **Creado** indicado en el **Estado** de la página **Redes** del Portal de Azure clásico. Una vez creada la red virtual, puede configurar la puerta de enlace de red virtual.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## Configuración de la puerta de enlace de la red virtual

A continuación, configurará la puerta de enlace de la red virtual con el fin de crear una conexión segura de sitio a sitio. Consulte [Configuración de una puerta de enlace VPN para el modelo de implementación clásico](vpn-gateway-configure-vpn-gateway-mp.md).

## Pasos siguientes

Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte la [documentación sobre máquinas virtuales](https://azure.microsoft.com/documentation/services/virtual-machines/) para más información.

<!---HONumber=AcomDC_0518_2016-->