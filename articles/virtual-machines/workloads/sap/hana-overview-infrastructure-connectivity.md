---
title: Infraestructura y conectividad con SAP HANA en Azure (Instancias grandes) | Microsoft Docs
description: "Configuración de la infraestructura de conectividad necesaria para usar SAP HANA en Azure (Instancias grandes)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 1d216d7d22fa671d7d35921d3ef7189c335a2577
ms.contentlocale: es-es
ms.lasthandoff: 04/28/2017


---

# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Infraestructura y conectividad con SAP HANA en Azure (instancias grandes) 

Una vez finalizada la compra de SAP HANA en Azure (Instancias grandes) con el equipo de cuentas de empresa de Microsoft, Microsoft necesita la siguiente información para implementar unidades de HANA (Instancias grandes):

- Nombre del cliente
- Información del contacto de la empresa (incluida la dirección de correo electrónico y el número de teléfono)
- Información del contacto técnico (incluida la dirección de correo electrónico y el número de teléfono)
- Información del contacto técnico de red (incluida la dirección de correo electrónico y el número de teléfono)
- Región de implementación de Azure (oeste de EE. UU. o este de EE. UU. a partir de abril de 2017)
- Confirmación de la SKU de SAP HANA en Azure (Instancias grandes) (configuración)
- Como se ha detallado en el documento Información general y arquitectura de HANA (Instancias grandes), para cada región de Azure en la que se implemente:
    - Un intervalo de direcciones IP /29 para conexiones ER-P2P para conectar redes virtuales de Azure con HANA (Instancias grandes)
    - Un bloque CIDR /24 usado para el grupo de direcciones IP de servidor de HANA (Instancias grandes)
- Los valores del intervalo de direcciones IP utilizados en el atributo espacio de direcciones de red virtual de cada red virtual de Azure que se conectará a HANA (Instancias grandes)
- Datos para cada sistema Instancias grandes de HANA:
  - Nombre de host deseado, preferiblemente el nombre de dominio completo.
  - Dirección IP deseada para la unidad de HANA (Instancias grandes) fuera del intervalo de direcciones del grupo de direcciones IP de servidor. Tenga en cuenta que las primeras 30 direcciones IP del intervalo de direcciones del grupo de direcciones IP de servidor están reservadas para uso interno en HANA (Instancias grandes)
  - Nombre del SID de SAP HANA para la instancia de SAP HANA (necesario para crear los volúmenes de disco necesarios relacionados con SAP HANA)
  - El identificador de grupo que tiene el usuario hana-sidadm en el sistema operativo Linux es necesario para crear los volúmenes de disco necesarios relacionados con SAP HANA. La instalación de SAP HANA normalmente crea el grupo sapsys con un identificador de grupo de 1001. El usuario hana-sidadm forma parte de ese grupo
  - El identificador de usuario que tiene el usuario hana-sidadm en el sistema operativo Linux es necesario para crear los volúmenes de disco necesarios relacionados con SAP HANA.
- Identificador de la suscripción de Azure a la que SAP HANA en Azure (Instancias grandes) estará directamente conectado

Después de proporcionar la información, Microsoft aprovisiona SAP HANA en Azure (Instancias grandes) y devuelve la información necesaria para vincular las redes virtuales de Azure a HANA Instancias grandes y para acceder a las unidades de HANA Instancias grandes.

## <a name="connecting-azure-vms-to-hana-large-instances"></a>Conexión de las máquinas virtuales de Azure a HANA Instancias grandes

Como se menciona en [Introducción y arquitectura de SAP HANA en Azure (Instancias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), la implementación mínima de HANA Instancias grandes con la capa de aplicación de SAP en Azure es similar a:

![Red virtual de Azure conectada a SAP HANA en Azure (Instancias grandes) y localmente](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Al examinar más de cerca el lado de la red virtual de Azure, somos conscientes de la necesidad de:

- La definición de una red virtual de Azure que se utilizará para implementar las máquinas virtuales de la capa de aplicación de SAP.
- Esto implica la definición de una subred predeterminada en la red virtual de Azure que es la utilizada realmente para implementar las máquinas virtuales.
- La red virtual de Azure que se crea debe tener al menos una subred de máquina virtual y una subred de puerta de enlace de ExpressRoute. Los intervalos de direcciones IP deben asignarse a dichas subredes, tal y como se especifica y trata más adelante.

Por tanto, observemos más de cerca la creación de la red virtual de Azure para HANA Instancias grandes

### <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Creación de la red virtual de Azure para HANA Instancias grandes

>[!Note]
>La red virtual de Azure para HANA Instancias grandes debe crearse mediante el modelo de implementación de Azure Resource Manager. La solución HANA Instancias grandes no admite el antiguo modelo de implementación de Azure, normalmente conocido como ASM.

La red virtual puede crearse mediante Azure Portal, PowerShell, una plantilla de Azure o la CLI de Azure (consulte [Creación de una red virtual mediante Azure Portal](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). En el siguiente ejemplo observamos una red virtual creada mediante Azure Portal.

Veamos las definiciones de una red virtual de Azure mediante Azure Portal, echemos un vistazo a algunas de ellas y veamos cómo se relacionan con lo que se indica a continuación. Por **espacio de direcciones** se entiende el espacio de direcciones que la red virtual de Azure puede utilizar. También es el intervalo de direcciones que usará la red virtual para la propagación de la ruta BGP. Este **espacio de direcciones** puede verse a continuación:

![Espacio de direcciones de la red virtual de Azure mostrado en Azure Portal](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

En el caso anterior, con 10.16.0.0/16, se le asigna a la red virtual de Azure un amplio intervalo de direcciones IP para su uso. Esto significa que todos los intervalos de direcciones IP de subredes subsiguientes dentro de esta red virtual pueden tener sus intervalos dentro de ese “espacio de direcciones”. Normalmente no se recomienda un intervalo de direcciones tan grande para una única red virtual de Azure. Vayamos un poco más allá y observemos las subredes definidas en la red virtual de Azure:

![Subredes de la red virtual de Azure y sus intervalos de direcciones IP](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Como se puede ver al observar la red virtual, tenemos una primera subred de la máquina virtual (denominada 'default') y una subred llamada 'GatewaySubnet'.
En la sección siguiente nos referimos al intervalo de direcciones IP de la subred denominada 'default' en los gráficos como **Intervalo de direcciones IP de la subred de la máquina virtual de Azure**. En las secciones siguientes, nos referimos al intervalo de direcciones IP de la subred de la puerta de enlace como **Intervalo de direcciones IP de la subred de la puerta de enlace de la red virtual**. 

En el caso que muestran los dos gráficos anteriores, se observa que el **espacio de direcciones de la red virtual** abarca ambos, el **intervalo de direcciones IP de la subred de la máquina virtual de Azure** y el **intervalo de direcciones IP de la subred de la puerta de enlace de la red virtual**. 

En otros casos en los que necesite conservar o ser muy específico con los intervalos de direcciones IP, puede restringir el **espacio de direcciones de la red virtual** de una red virtual a los intervalos específicos utilizados por cada subred. En este caso, puede definir el **espacio de direcciones de la red virtual** de una red virtual como varios intervalos específicos, como se muestra a continuación:

![Espacio de direcciones de red virtual de Azure con dos espacios](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

En este caso, el **espacio de direcciones de red virtual** tiene dos espacios definidos. Son idénticos a los intervalos de direcciones IP definidos para el **intervalo de direcciones IP de la subred de la máquina virtual de Azure** y el **intervalo de direcciones IP de la subred de la puerta de enlace de la red virtual**.

Puede usar la nomenclatura que desee para estas subredes de inquilino (subredes de máquina virtual). Sin embargo, **siempre debe haber una (solo una) subred de puerta de enlace para cada red virtual** que se conecta al circuito de ExpressRoute de SAP HANA en Azure (Instancias grandes), y **dicha subred de la puerta de enlace siempre debe llamarse "GatewaySubnet"** para garantizar la correcta ubicación de la puerta de enlace de ExpressRoute.

> [!WARNING] 
> Es fundamental que la subred de la puerta de enlace siempre se denomine "GatewaySubnet".

Se pueden utilizar varias subredes de máquina virtual, incluso con intervalos de direcciones no contiguas. Pero como se mencionó anteriormente, estos intervalos de direcciones deben ser abarcados por el **espacio de direcciones de la red virtual** de la red virtual, bien en forma agregada o como una lista de los intervalos exactos de las subredes de máquina virtual y de la subred de la puerta de enlace.

Resumen de los hechos importantes sobre una red virtual de Azure que se conecta HANA Instancias grandes:

- Tendrá que enviar a Microsoft el **espacio de direcciones de la red virtual** al realizar la implementación inicial de HANA Instancias grandes. 
- El **espacio de direcciones de la red virtual** puede ser un intervalo mayor que abarque los intervalos de direcciones IP de las subredes de la máquina virtual de Azure y el intervalo de direcciones IP de la subred de la puerta de enlace de la red virtual.
- O puede enviar como **espacio de direcciones de la red virtual** varios intervalos que cubran los diferentes intervalos de direcciones IP de subredes de la máquina virtual y el intervalo de direcciones IP de la subred de la puerta de enlace de la red virtual.
- El **espacio de direcciones de la red virtual** definido se utiliza en la propagación del enrutamiento BGP.
- El nombre de la subred de la puerta de enlace debe ser: **"GatewaySubnet"**.
- El **espacio de direcciones de la red virtual** se usa como un filtro en el lado de HANA Instancias grandes para permitir o impedir el tráfico a las unidades de HANA Instancias grandes desde Azure. Si la información de enrutamiento de BGP de la red virtual de Azure y los intervalos de direcciones IP configurados para el filtrado en el lado de HANA Instancias grandes no coinciden, se producirán problemas de conectividad.
- Hay algunos detalles acerca de la subred de la puerta de enlace que se describen más adelante en la sección 'Conexión de una red virtual a ExpressRoute de HANA Instancias grandes'



### <a name="different-ip-address-ranges-to-be-defined"></a>Distintos intervalos de direcciones IP para definir 

Ya se han presentado anteriormente algunos de los intervalos de direcciones IP necesarios para implementar HANA Instancias grandes. Pero hay algunos otros intervalos de direcciones IP que son importantes. Analicemos algunos detalles adicionales. Se necesita definir las siguientes direcciones IP (algunas de las cuales no es necesario enviar a Microsoft) antes de enviar una solicitud de implementación inicial:

- **Espacio de direcciones de la red virtual:** como se mencionó antes, es el intervalo de direcciones IP que ha asignado (o planea asignar) al parámetro de espacio de direcciones de las redes virtuales de Azure que se conectan al entorno de SAP HANA Instancias grandes. Se recomienda que el parámetro de espacio de direcciones sea un valor de varias líneas que comprenda los intervalos de las subredes de máquina virtual de Azure y el intervalo de la subred de la puerta de enlace de Azure, como muestran los gráficos anteriores. Este intervalo no se debe solapar con los intervalos de direcciones locales, ni con los del grupo de direcciones IP de servidor, ni con los de ER-P2P. Cómo conseguirlo El equipo de redes corporativas o el proveedor de servicios deben proporcionar un intervalo de direcciones IP no utilizado en la red. Ejemplo: si la subred de la máquina virtual de Azure (ver arriba) es 10.0.1.0/24 y la subred de la puerta de enlace de Azure (ver abajo) es 10.0.2.0/28, se recomienda que el espacio de direcciones de la red virtual de Azure tenga dos líneas: 10.0.1.0/24 y 10.0.2.0/28. Aunque se pueden agregar valores al espacio de direcciones, se recomienda asociarlos a los intervalos de la subred para evitar una futura reutilización accidental en alguna parte de la red. **Se debe enviar a Microsoft este intervalo de direcciones IP al solicitar una implementación inicial**

- **Intervalo de direcciones IP de la subred de la máquina virtual de Azure:** este intervalo de direcciones IP, como se explicó anteriormente, es el que ha asignado (o planea asignar) al parámetro de la subred de la red virtual de Azure que va a conectar al entorno de SAP HANA Instancias grandes. Este intervalo de direcciones IP se usa para asignar direcciones IP a las máquinas virtuales de Azure. Este intervalo podrá conectarse a los servidores de SAP HANA Instancias grandes. Si es necesario, se pueden utilizar varias subredes de máquina virtual de Azure. Microsoft recomienda un bloque CIDR /24 para cada subred de máquina virtual de Azure. Este intervalo de direcciones debe formar parte de los valores utilizados en el espacio de direcciones de red virtual de Azure. Cómo conseguirlo El equipo de redes corporativas o el proveedor de servicios deben proporcionar un intervalo de direcciones IP no utilizado actualmente en la red.

- **Intervalo de direcciones IP de la subred de la puerta de enlace de la red virtual:** dependiendo de las características que se van a utilizar, el tamaño recomendado sería:
   - Puerta de enlace de ExpressRoute de ultrarrendimiento: bloque de direcciones /26
   - Coexistencia con VPN y ExpressRoute mediante una puerta de enlace de ExpressRoute de alto rendimiento (o inferior): bloque de direcciones /27
   - Resto de situaciones: bloque de direcciones /28. Este intervalo de direcciones debe formar parte de los valores utilizados en los valores de "Espacio de direcciones de la red virtual". Este intervalo de direcciones debe formar parte de los valores utilizados en los valores del espacio de direcciones de la red virtual de Azure que debe enviar a Microsoft. Cómo conseguirlo El equipo de redes corporativas o el proveedor de servicios deben proporcionar un intervalo de direcciones IP no utilizado actualmente en la red. 

- **Intervalo de direcciones para conectividad ER-P2P:** es el intervalo de direcciones IP para la conexión ExpressRoute (ER) P2P de SAP HANA Instancias grandes. Este intervalo de direcciones IP debe ser un intervalo de direcciones IP CIDR /29. Este intervalo no se debe superponer con los intervalos de direcciones locales ni con otros intervalos de direcciones IP de Azure. Se utiliza para configurar la conectividad ER entre la puerta de enlace ExpressRoute de la red virtual de Azure y los servidores de SAP HANA Instancias grandes. Cómo conseguirlo El equipo de redes corporativas o el proveedor de servicios deben proporcionar un intervalo de direcciones IP no utilizado actualmente en la red. **Se debe enviar a Microsoft este intervalo de direcciones IP al solicitar una implementación inicial**
  
- **Intervalo de direcciones del grupo de direcciones IP de servidor:** este intervalo de direcciones IP se usa para asignar direcciones IP individuales a los servidores de HANA Instancias grandes. El tamaño de subred recomendado es un bloque CIDR /24, aunque si es necesario, puede ser menor hasta un mínimo de 64 direcciones IP. De este intervalo, las primeras 30 direcciones IP están reservadas para uso de Microsoft. Asegúrese de que esto se tiene en cuenta al elegir el tamaño del intervalo. Este intervalo no se debe superponer con los intervalos de direcciones locales ni con otras direcciones IP de Azure. Cómo conseguirlo El equipo de redes corporativas o el proveedor de servicios deben proporcionar un intervalo de direcciones IP no utilizado actualmente en la red. Un bloque CIDR /24 único (recomendado) que se usará para asignar las direcciones IP específicas necesarias para SAP HANA en Azure (Instancias grandes). **Se debe enviar a Microsoft este intervalo de direcciones IP al solicitar una implementación inicial**
 
Aunque tiene que definir y planear los intervalos de direcciones IP anteriores, no es necesario transmitirlos todos a Microsoft. Para resumir lo anterior, los intervalos de direcciones IP que es necesario notificar a Microsoft son:

- Espacios de direcciones de la red virtual de Azure
- Intervalo de direcciones para conectividad ER-P2P
- Intervalo de direcciones del grupo de direcciones IP de servidor

Al agregar redes virtuales adicionales que necesitan conectarse a HANA Instancias grandes, es necesario enviar a Microsoft el nuevo espacio de direcciones de la red virtual de Azure que está agregando. 

A continuación se muestra un ejemplo de los diferentes intervalos y algunos intervalos de ejemplo que se deben configurar y, en algunos casos, proporcionar a Microsoft. Como se puede observar, en el primer ejemplo no se agregan los valores del espacio de direcciones de la red virtual de Azure; en su lugar, se definen a partir de los intervalos del primer intervalo de direcciones IP de la subred de la máquina virtual de Azure y del intervalo de direcciones IP de la subred de la puerta de enlace de la red virtual. El uso de varias subredes de máquina virtual en la red virtual de Azure funcionaría mediante la configuración y el envío de los intervalos de direcciones IP adicionales de las subredes de máquina virtual adicionales como parte del espacio de direcciones de la red virtual de Azure.

![Intervalos de direcciones IP necesarios en la implementación mínima de SAP HANA en Azure (Instancias grandes)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

También tiene la posibilidad de agregar los datos que envíe a Microsoft. En ese caso, el espacio de direcciones de la red virtual de Azure solo incluiría un espacio. Con los intervalos de direcciones IP utilizados en el ejemplo anterior. Podría ser similar a:

![Segunda posibilidad de intervalos de direcciones IP necesarios en la implementación mínima de SAP HANA en Azure (Instancias grandes)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Como puede ver más arriba, en lugar de dos intervalos más pequeños que definían el espacio de direcciones de la red virtual de Azure, tenemos un intervalo mayor que abarca 4096 direcciones IP. Una definición de gran tamaño del espacio de direcciones deja algunos intervalos bastante grandes sin usar. Puesto que los valores del espacio de direcciones de la red virtual se usan para la propagación de la ruta BGP, la utilización de los intervalos no usados en instancias locales o en otra parte de la red puede causar problemas de enrutamiento. Por este motivo, se recomienda mantener el espacio de direcciones estrechamente alineado con el espacio de direcciones de la subred realmente utilizado. Siempre puede agregar nuevos valores del espacio de direcciones más adelante, si es necesario, sin incurrir en tiempo de inactividad de la red virtual.
 
> [!IMPORTANT] 
> Los intervalos de direcciones IP de ER-P2P, del grupo de direcciones IP de servidor y del espacio de direcciones de la red virtual de Azure **NO** deben superponerse entre sí ni con ningún otro intervalo utilizado en la red; cada uno de ellos debe ser diferente y, como muestran los dos gráficos anteriores, no deben ser una subred de cualquier otro intervalo. Si se producen superposiciones entre los intervalos, la red virtual de Azure podría no conectarse al circuito de ExpressRoute.

### <a name="next-steps-after-address-ranges-have-been-defined"></a>Pasos siguientes después de definir los intervalos de direcciones
Después de definir los intervalos de direcciones IP, son necesarias las siguientes actividades:

1. Enviar los intervalos de direcciones IP del espacio de nombres de la red virtual de Azure, de la conectividad ER-P2P y del grupo de direcciones IP de servidor, junto con los otros datos indicados al principio del documento. En este momento también podría empezar a crear la red virtual y las subredes de la máquina virtual. 
2. Microsoft crea un circuito de ExpressRoute entre la suscripción de Azure y el sello de HANA Instancias grandes.
3. Microsoft crea una red de inquilino en el sello de HANA Instancias grandes.
4. Microsoft configurará las redes en la infraestructura de SAP HANA en Azure (Instancias grandes) de forma que acepten las direcciones IP del espacio de direcciones de la red virtual de Azure que se comunicará con HANA Instancias grandes.
5. Según la SKU concreta de SAP HANA en Azure (Instancias grandes) que se haya adquirido, Microsoft asignará una unidad de proceso en una red de inquilino, asignará y montará almacenamiento, e instalará el sistema operativo (SUSE o RedHat Linux). Las direcciones IP para estas unidades se toman del intervalo de direcciones del grupo de direcciones IP de servidor que envió a Microsoft.

Al final del proceso de implementación, Microsoft le proporciona los siguientes datos:
- Información necesaria para conectar las redes virtuales de Azure al circuito de ExpressRoute que conecta las redes virtuales de Azure a HANA Instancias grandes:
     - Claves de autorización
     - PeerID de ExpressRoute
- Datos de acceso a HANA Instancias grandes después de establecer el circuito de ExpressRoute y la red virtual de Azure.


## <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Conexión de una red virtual a ExpressRoute de HANA Instancias grandes

Una vez definidos todos los intervalos de direcciones IP y con los datos devueltos por Microsoft, puede comenzar a conectar la red virtual creada anteriormente a HANA Instancias grandes. Una vez creada la red virtual de Azure, es necesario crear una puerta de enlace de ExpressRoute en la red virtual para vincular la red al circuito de ExpressRoute que conecta con el inquilino del cliente en la marca Instancias grandes.

> [!NOTE] 
> Este paso puede tardar hasta 30 minutos en completarse, ya que la nueva puerta de enlace se crea en la suscripción de Azure designada y, después, se conecta a la red virtual de Azure especificada.

Si ya existe una puerta de enlace, compruebe si se trata de una puerta de enlace de ExpressRoute o no. Si no lo es, debe eliminarla y volver a crearla como una puerta de enlace de ExpressRoute. Si ya hay una puerta de enlace de ExpressRoute establecida, la red virtual de Azure ya está conectada al circuito de ExpressRoute para la conectividad local; por tanto, continúe con la sección Vinculación de redes virtuales, que aparece a continuación.

- Utilice [Azure Portal](https://portal.azure.com/) (nuevo) o PowerShell para crear una puerta de enlace de red virtual de ExpressRoute conectada a la red virtual.
  - Si usa Azure Portal, agregue una nueva **puerta de enlace de red virtual** y seleccione **ExpressRoute** como tipo de puerta de enlace.
  - Si prefiere PowerShell, descargue y use la versión más reciente [SDK de Azure PowerShell](https://azure.microsoft.com/downloads/) para garantizar una experiencia óptima. Los siguientes comandos crean una puerta de enlace de ExpressRoute. Los textos precedidos por un signo _$_ son variables definidas por el usuario que es necesario actualizar con su información específica.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

En este ejemplo, se usó la SKU de puerta de enlace HighPerformance. Puede elegir HighPerformance o UltraPerformance, las únicas SKU de puerta de enlace que son compatibles con SAP HANA en Azure (Instancias grandes).

### <a name="linking-vnets"></a>Vinculación de redes virtuales

Ahora que la red virtual de Azure tiene una puerta de enlace de ExpressRoute, utilice la información de autorización proporcionada por Microsoft para conectar la puerta de enlace de ExpressRoute al circuito de ExpressRoute de SAP HANA en Azure (Instancias grandes) creado para esta conectividad. Esto puede realizarse mediante Azure Portal o PowerShell. Aunque se recomienda Azure Portal, las instrucciones para PowerShell son las siguientes. 

- Ejecute lo siguiente para cada puerta de enlace de la red virtual usando un AuthGUID diferente para cada conexión. Las dos primeras entradas mostradas a continuación proceden de la información proporcionada por Microsoft. Además, el AuthGUID es específico de cada red virtual y su puerta de enlace. Significa que necesita obtener otro AuthID para el circuito de ExpressRoute que conecta HANA Instancias grandes a Azure si desea agregar otra red virtual de Azure. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Debe realizar este paso más de una vez si desea conectar la puerta de enlace a varios circuitos de ExpressRoute asociados a su suscripción. Por ejemplo, probablemente necesita conectar la misma puerta de enlace de la red virtual al circuito de ExpressRoute que conecta la red virtual a la red local.

## <a name="adding-more-ip-addresses-or-subnets"></a>incorporación de más direcciones IP o subredes

Use Azure Portal, PowerShell o la CLI al agregar más IP direcciones o subredes.

En este caso, se recomienda agregar el nuevo intervalo de direcciones IP como un nuevo intervalo del espacio de direcciones de la red virtual en lugar de generar un nuevo intervalo agregado. En cualquier caso, debe enviar este cambio a Microsoft para permitir la conectividad desde el nuevo intervalo de direcciones IP a las unidades de HANA Instancias grandes del cliente. Puede abrir una solicitud de soporte técnico de Azure para que sea agregado. Después de recibir la confirmación, realice los pasos siguientes.

Para crear una subred adicional desde Azure Portal, consulte el artículo [Creación de una red virtual mediante Azure Portal](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) y para crearla desde PowerShell, consulte [Creación de una red virtual mediante PowerShell](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="adding-vnets"></a>Incorporación de redes virtuales

Después de conectarse inicialmente a una o varias redes virtuales de Azure, puede agregar otras que tengan acceso a SAP HANA en Azure (Instancias grandes). En primer lugar, envíe una solicitud de soporte técnico de Azure que incluya tanto la información específica para identificar la implementación de Azure en particular como los intervalos de direcciones IP del espacio de direcciones de la red virtual de Azure. A continuación, SAP HANA en Azure Service Management, proporciona la información necesaria para conectar las redes virtuales y ExpressRoute adicionales. Para cada red virtual, necesitará una clave de autorización única para conectar el circuito de ExpressRoute a HANA Instancias grandes.

Pasos para agregar una nueva red virtual de Azure:

1. Complete el primer paso del proceso de incorporación; consulte la sección anterior, _Creación de una red virtual de Azure_.
2. Complete el segundo paso del proceso de incorporación; consulte la sección anterior, _Creación de una subred de puerta de enlace_.
3. Abra una solicitud de soporte técnico de Azure con información sobre la nueva red virtual y solicite una nueva clave de autorización para conectar sus redes virtuales adicionales al circuito de ExpressRoute de Instancias grandes de HANA.
4. Una vez que se le notifique que la autorización está completa, utilice la información de autorización proporcionada por Microsoft para completar el tercer paso del proceso de incorporación; consulte la sección anterior, _Vinculación de redes virtuales_.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Aumento del ancho de banda del circuito de ExpressRoute

Consulte con SAP HANA en Azure Service Management. Si se le recomienda aumentar el ancho de banda del circuito de ExpressRoute de SAP HANA en Azure (Instancias grandes), cree una solicitud de soporte técnico de Azure. Puede solicitar un aumento del ancho de banda de un solo circuito de hasta 10 Gbps. Recibirá una notificación cuando la operación se complete; no es necesaria ninguna acción adicional para habilitar esta velocidad superior en Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Incorporación de un circuito de ExpressRoute adicional

Consulte con SAP HANA en Azure Service Management; si se le recomienda agregar otro circuito de ExpressRoute, abra una solicitud de soporte técnico de Azure para crear un circuito de ExpressRoute y obtener información de autorización para conectarse a él. Para que SAP HANA en Azure Service Management proporcione la autorización, es necesario definir el espacio de direcciones que se utilizará en las redes virtuales antes de realizar la solicitud.

Una vez creado el circuito y terminada la configuración en SAP HANA en Azure Service Management, recibirá una notificación con la información que necesita para continuar. Siga los pasos antes indicados para crear y conectar la nueva red virtual para este circuito adicional. No podrá conectar redes virtuales de Azure a este circuito adicional si ya están conectadas a otro circuito de ExpressRoute de SAP HANA en Azure (Instancias grandes).

## <a name="deleting-a-subnet"></a>Eliminación de una subred

Para quitar una subred de red virtual, se puede usar Azure Portal, PowerShell o CLI. Si el intervalo de direcciones IP de la red virtual de Azure o el espacio de direcciones de la red virtual de Azure fuesen intervalos agregados, no son necesarias acciones con Microsoft. Salvo que debe tener en cuenta que la red virtual todavía está propagando el espacio de direcciones de la ruta BGP que incluye la subred eliminada. Si ha definido el intervalo de direcciones IP de la red virtual de Azure o el espacio de direcciones de la red virtual de Azure como varios intervalos de direcciones IP, de los cuales uno está asignado a la subred eliminada, debe eliminarlo del espacio de direcciones de la red virtual y, posteriormente, informar a SAP HANA en Azure Service Management para que lo elimine de los intervalos con los que SAP HANA en Azure (Instancias grandes) tiene permitido comunicarse.

Aunque aún no existe ninguna guía específica en Azure.com sobre cómo quitar subredes, el proceso para hacerlo es el inverso del proceso para agregarlas, que se ha descrito antes. Consulte el artículo [Creación de una red virtual mediante Azure Portal](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para más información sobre la creación de subredes.

## <a name="deleting-a-vnet"></a>Eliminación de una red virtual

Utilice Azure Portal, PowerShell o CLI para eliminar una red virtual. SAP HANA en Azure Service Management quita las autorizaciones existentes en el circuito ExpressRoute de SAP HANA en Azure (Instancias grandes) y elimina el intervalo de direcciones IP de la red virtual de Azure o el espacio de direcciones de la red virtual de Azure para la comunicación con HANA Instancias grandes.

Una vez eliminada la red virtual, abra una solicitud de soporte técnico de Azure para proporcionar los intervalos de espacios de direcciones IP que se van a eliminar.

Aunque aún no existe ninguna guía específica en Azure.com sobre cómo quitar redes virtuales, el proceso para hacerlo es el inverso del proceso para agregarlas, que se ha descrito antes. Consulte los artículos [Creación de una red virtual mediante Azure Portal](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) y [Creación de una red virtual mediante PowerShell](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para más información sobre la creación de redes virtuales.

Para asegurarse de que se quita todo, elimine los elementos siguientes:

- La conexión de ExpressRoute, la puerta de enlace de la red virtual, la dirección IP pública de la puerta de enlace de la red virtual y la red virtual

## <a name="deleting-an-expressroute-circuit"></a>Eliminación de un circuito de ExpressRoute

Para quitar un circuito de ExpressRoute adicional de SAP HANA en Azure (Instancias grandes), abra una solicitud de soporte técnico de Azure en SAP HANA en Azure Service Management y solicite que se elimine. En la suscripción de Azure, puede eliminar o mantener la red virtual, según sea necesario. Sin embargo, debe eliminar la conexión entre el circuito de ExpressRoute de HANA Instancias grandes y la puerta de enlace de la red virtual enlazada.

Si también desea quitar una red virtual, siga las instrucciones de la sección anterior, Eliminación de una red virtual.



