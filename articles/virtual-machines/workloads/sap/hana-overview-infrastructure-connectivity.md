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
ms.date: 10/31/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7a44fdbfb973d75c21aa87e9b9d0eea8fb2b3392
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Infraestructura y conectividad con SAP HANA en Azure (instancias grandes) 

Algunas definiciones iniciales antes de leer esta guía. En [Introducción y arquitectura de SAP HANA en Azure (instancias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) se presentan dos clases distintas de unidades de HANA (Instancias grandes) con:

- S72, S72m, S144, S144m, S192 y S192m, a las que se hace referencia como "clase de tipo I" de SKU.
- S384, S384m, S384xm, S576, S768 y S960, a los que se hace referencia como "clase de tipo II" de SKU.

Los especificadores de clase se van a usar en toda la documentación sobre HANA (Instancias grandes) para hacer referencia a diferentes capacidades y requisitos en función de las SKU de HANA (Instancias grandes).

Otras definiciones que se usan con frecuencia son:
- **Sello de instancias grandes:** una pila de infraestructura de hardware que está certificada para SAP HANA TDI y se dedica a ejecutar instancias de SAP HANA dentro de Azure.
- **SAP HANA en Azure (Instancias grandes):** nombre oficial de la oferta de Azure para ejecutar instancias de HANA en hardware con certificación SAP HANA TDI que se implementa en sellos de instancias grandes en diferentes regiones de Azure. El término relacionado **HANA (Instancias grandes)** es la versión abreviada de SAP HANA en Azure (Instancias grandes) y se usa con frecuencia en esta guía de implementación técnica.
 

Una vez finalizada la compra de SAP HANA en Azure (Instancias grandes) con el equipo de cuentas de empresa de Microsoft, Microsoft necesita la siguiente información para implementar unidades de HANA (Instancias grandes):

- Nombre del cliente
- Información del contacto de la empresa (incluida la dirección de correo electrónico y el número de teléfono)
- Información del contacto técnico (incluida la dirección de correo electrónico y el número de teléfono)
- Información del contacto técnico de red (incluida la dirección de correo electrónico y el número de teléfono)
- Región de implementación de Azure (oeste de EE. UU., este de EE. UU., este de Australia, sudeste de Australia, Europa Occidental y Europa del Norte a partir de julio de 2017)
- Confirmación de la SKU de SAP HANA en Azure (Instancias grandes) (configuración)
- Como se ha detallado en el documento Información general y arquitectura de HANA (Instancias grandes), para cada región de Azure en la que se implemente:
    - Un intervalo de direcciones IP /29 para conexiones ER-P2P para conectar redes virtuales de Azure con HANA (Instancias grandes)
    - Un bloque CIDR /24 usado para el grupo de direcciones IP de servidor de HANA (Instancias grandes)
- Los valores del intervalo de direcciones IP usados en el atributo Espacio de direcciones de red virtual de cada red virtual de Azure que se conecta a HANA (Instancias grandes)
- Datos para cada sistema Instancias grandes de HANA:
  - Nombre de host deseado, preferiblemente el nombre de dominio completo.
  - Dirección IP deseada para la unidad de HANA (Instancias grandes) fuera del intervalo de direcciones del grupo de direcciones IP de servidor. Tenga en cuenta que las primeras 30 direcciones IP del intervalo de direcciones del grupo de direcciones IP de servidor están reservadas para uso interno en HANA (Instancias grandes)
  - Nombre del SID de SAP HANA para la instancia de SAP HANA (necesario para crear los volúmenes de disco necesarios relacionados con SAP HANA). El SID de HANA se necesita para crear los permisos para <sidadm> en los volúmenes NFS, que se están conectando a la unidad de HANA (Instancias grandes). También se usa como uno de los componentes del nombre de los volúmenes de disco que se montan. Si quiere ejecutar más de una instancia de HANA en la unidad, debe enumerar varios SID de HANA. Cada uno obtiene un conjunto independiente de volúmenes asignados.
  - El identificador de grupo que tiene el usuario hana-sidadm en el sistema operativo Linux es necesario para crear los volúmenes de disco necesarios relacionados con SAP HANA. La instalación de SAP HANA normalmente crea el grupo sapsys con un identificador de grupo de 1001. El usuario hana-sidadm forma parte de ese grupo
  - El identificador de usuario que tiene el usuario hana-sidadm en el sistema operativo Linux es necesario para crear los volúmenes de disco necesarios relacionados con SAP HANA. Si está ejecutando varias instancias de HANA en la unidad, debe enumerar todos los usuarios <sid>adm 
- Identificador de la suscripción de Azure a la que SAP HANA en Azure (Instancias grandes) vaya a estar directamente conectado. Este identificador de suscripción hace referencia a la suscripción de Azure, que se va a cargar con las unidades de HANA (Instancias grandes).

Después de proporcionar la información, Microsoft aprovisiona SAP HANA en Azure (Instancias grandes) y devuelve la información necesaria para vincular las redes virtuales de Azure a HANA Instancias grandes y para acceder a las unidades de HANA Instancias grandes.

## <a name="connecting-azure-vms-to-hana-large-instances"></a>Conexión de las máquinas virtuales de Azure a HANA Instancias grandes

Como se menciona en [Introducción y arquitectura de SAP HANA en Azure (Instancias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), la implementación mínima de HANA Instancias grandes con la capa de aplicación de SAP en Azure es similar a:

![Red virtual de Azure conectada a SAP HANA en Azure (Instancias grandes) y localmente](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Al examinar más de cerca el lado de la red virtual de Azure, somos conscientes de la necesidad de:

- La definición de una red virtual de Azure que se va a usar para implementar las máquinas virtuales de la capa de aplicación de SAP.
- Esto implica la definición de una subred predeterminada en la red virtual de Azure que es la utilizada realmente para implementar las máquinas virtuales.
- La red virtual de Azure que se crea debe tener al menos una subred de máquina virtual y una subred de puerta de enlace de ExpressRoute. A estas subredes se les deben asignar los intervalos de direcciones IP, como se especifica y trata en las siguientes secciones.

Por tanto, observemos más de cerca la creación de la red virtual de Azure para HANA Instancias grandes

### <a name="creating-the-azure-vnet-for-hana-large-instances"></a>Creación de la red virtual de Azure para HANA Instancias grandes

>[!Note]
>La red virtual de Azure para HANA Instancias grandes debe crearse mediante el modelo de implementación de Azure Resource Manager. La solución HANA (Instancias grandes) no admite el antiguo modelo de implementación de Azure, normalmente conocido como modelo de implementación clásica.

La red virtual puede crearse mediante Azure Portal, PowerShell, una plantilla de Azure o la CLI de Azure (vea [Creación de una red virtual (clásica) usando Azure Portal](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). En el siguiente ejemplo se observa una red virtual creada mediante Azure Portal.

En él se explican las definiciones de una red virtual de Azure en Azure Portal y cómo se relacionan con lo que se indica sobre los distintos intervalos de direcciones IP. Por **espacio de direcciones** se entiende el espacio de direcciones que la red virtual de Azure puede usar. Este espacio de direcciones también es el intervalo de direcciones que usa la red virtual para la propagación de la ruta BGP. Este **espacio de direcciones** puede verse a continuación:

![Espacio de direcciones de la red virtual de Azure mostrado en Azure Portal](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

En el caso anterior, con 10.16.0.0/16, se le asigna a la red virtual de Azure un amplio intervalo de direcciones IP para su uso. Esto significa que todos los intervalos de direcciones IP de subredes subsiguientes dentro de esta red virtual pueden tener sus intervalos dentro de ese “espacio de direcciones”. Normalmente no se recomienda un intervalo de direcciones tan grande para una única red virtual de Azure. Vayamos un poco más allá y observemos las subredes definidas en la red virtual de Azure:

![Subredes de la red virtual de Azure y sus intervalos de direcciones IP](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

Como se puede ver, hay una primera subred de máquina virtual (denominada "default") y una subred llamada "GatewaySubnet".
En la sección siguiente se hace referencia al intervalo de direcciones IP de la subred denominada "default" en los gráficos como **Intervalo de direcciones IP de la subred de la máquina virtual de Azure**. En las secciones siguientes, nos referimos al intervalo de direcciones IP de la subred de la puerta de enlace como **Intervalo de direcciones IP de la subred de la puerta de enlace de la red virtual**. 

En el caso que muestran los dos gráficos anteriores, se observa que el **espacio de direcciones de la red virtual** abarca ambos, el **intervalo de direcciones IP de la subred de la máquina virtual de Azure** y el **intervalo de direcciones IP de la subred de la puerta de enlace de la red virtual**. 

En otros casos en los que necesite conservar o ser concreto con los intervalos de direcciones IP, puede restringir el **espacio de direcciones de red virtual** de una red virtual a los intervalos específicos usados por cada subred. En este caso, puede definir el **espacio de direcciones de la red virtual** de una red virtual como varios intervalos específicos, como se muestra a continuación:

![Espacio de direcciones de red virtual de Azure con dos espacios](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

En este caso, el **espacio de direcciones de red virtual** tiene dos espacios definidos. Estos dos espacios son idénticos a los intervalos de direcciones IP definidos para el **intervalo de direcciones IP de la subred de la máquina virtual de Azure** y el **intervalo de direcciones IP de la subred de la puerta de enlace de la red virtual**.

Puede usar la nomenclatura que desee para estas subredes de inquilino (subredes de máquina virtual). Pero **siempre debe haber una, y solo una, subred de puerta de enlace para cada red virtual** que se conecte al circuito ExpressRoute de SAP HANA en Azure (Instancias grandes). Y **esta subred de puerta de enlace siempre debe denominarse "GatewaySubnet"** para garantizar la correcta colocación de la puerta de enlace de ExpressRoute.

> [!WARNING] 
> Es fundamental que la subred de la puerta de enlace siempre se denomine "GatewaySubnet".

Se pueden utilizar varias subredes de máquina virtual, incluso con intervalos de direcciones no contiguas. Pero como se mencionó anteriormente, estos intervalos de direcciones deben ser abarcados por el **espacio de direcciones de la red virtual** de la red virtual, bien en forma agregada o como una lista de los intervalos exactos de las subredes de máquina virtual y de la subred de la puerta de enlace.

Resumen de los hechos importantes sobre una red virtual de Azure que se conecta HANA Instancias grandes:

- Tiene que enviar a Microsoft el **espacio de direcciones de la red virtual** al realizar una implementación inicial de HANA (Instancias grandes). 
- El **espacio de direcciones de la red virtual** puede ser un intervalo mayor que abarque los intervalos de direcciones IP de las subredes de la máquina virtual de Azure y el intervalo de direcciones IP de la subred de la puerta de enlace de la red virtual.
- O puede enviar como **espacio de direcciones de la red virtual** varios intervalos que cubran los diferentes intervalos de direcciones IP de subredes de la máquina virtual y el intervalo de direcciones IP de la subred de la puerta de enlace de la red virtual.
- El **espacio de direcciones de la red virtual** definido se utiliza en la propagación del enrutamiento BGP.
- El nombre de la subred de la puerta de enlace debe ser: **"GatewaySubnet"**.
- El **espacio de direcciones de la red virtual** se usa como un filtro en el lado de HANA Instancias grandes para permitir o impedir el tráfico a las unidades de HANA Instancias grandes desde Azure. Si la información de enrutamiento de BGP de la red virtual de Azure y los intervalos de direcciones IP configurados para el filtrado en el lado de HANA (Instancias grandes) no coinciden, se pueden producir problemas de conectividad.
- Hay algunos detalles acerca de la subred de la puerta de enlace que se describen más adelante en la sección 'Conexión de una red virtual a ExpressRoute de HANA Instancias grandes'



### <a name="different-ip-address-ranges-to-be-defined"></a>Distintos intervalos de direcciones IP para definir 

En secciones anteriores ya se han presentado algunos de los intervalos de direcciones IP necesarios para implementar HANA (Instancias grandes). Pero hay algunos otros intervalos de direcciones IP que son importantes. Analicemos algunos detalles adicionales. Se necesita definir las siguientes direcciones IP (algunas de las cuales no es necesario enviar a Microsoft) antes de enviar una solicitud de implementación inicial:

- **Espacio de direcciones de la red virtual:** como se ha explicado antes, es el intervalo de direcciones IP que ha asignado (o planea asignar) al parámetro de espacio de direcciones de las redes virtuales de Azure que se conectan al entorno de SAP HANA (Instancias grandes). Se recomienda que el parámetro de espacio de direcciones sea un valor de varias líneas que comprenda los intervalos de las subredes de máquina virtual de Azure y el intervalo de la subred de la puerta de enlace de Azure, como muestran los gráficos anteriores. Este intervalo no se debe solapar con los intervalos de direcciones locales, ni con los del grupo de direcciones IP de servidor, ni con los de ER-P2P. ¿Cómo obtener estos intervalos de direcciones IP? El equipo de redes corporativas o el proveedor de servicios deben proporcionar uno o varios intervalos de direcciones IP que no se usen en la red. Ejemplo: si la subred de la máquina virtual de Azure (ver arriba) es 10.0.1.0/24 y la subred de la puerta de enlace de Azure (ver a continuación) es 10.0.2.0/28, se recomienda que el espacio de direcciones de la red virtual de Azure tenga dos líneas: 10.0.1.0/24 y 10.0.2.0/28. Aunque se pueden agregar valores al espacio de direcciones, se recomienda asociarlos a los intervalos de la subred para evitar una futura reutilización accidental de intervalos de direcciones IP sin usar en espacios de direcciones mayores en alguna parte de la red. **El espacio de direcciones de la red virtual es un intervalo de direcciones IP que se debe enviar a Microsoft al solicitar una implementación inicial**

- **Intervalo de direcciones IP de la subred de la máquina virtual de Azure:** este intervalo de direcciones IP, como se ha explicado anteriormente, es el que ha asignado (o planea asignar) al parámetro de la subred de la red virtual de Azure que va a conectar al entorno de SAP HANA (Instancias grandes). Este intervalo de direcciones IP se usa para asignar direcciones IP a las máquinas virtuales de Azure. Las direcciones IP fuera de este intervalo pueden conectarse a los servidores de SAP HANA (Instancias grandes). Si es necesario, se pueden utilizar varias subredes de máquina virtual de Azure. Microsoft recomienda un bloque CIDR /24 para cada subred de máquina virtual de Azure. Este intervalo de direcciones debe formar parte de los valores utilizados en el espacio de direcciones de red virtual de Azure. ¿Cómo obtener este intervalo de direcciones IP? El equipo de redes corporativas o el proveedor de servicios deben proporcionar un intervalo de direcciones IP no utilizado actualmente en la red.

- **Intervalo de direcciones IP de la subred de la puerta de enlace de la red virtual:** dependiendo de las características que se van a utilizar, el tamaño recomendado sería:
   - Puerta de enlace de ExpressRoute de ultrarrendimiento: bloque de direcciones /26: se necesita para la clase de tipo II de SKU
   - Coexistencia con VPN y ExpressRoute mediante una puerta de enlace de ExpressRoute de alto rendimiento (o inferior): bloque de direcciones /27
   - Resto de situaciones: bloque de direcciones /28. Este intervalo de direcciones debe formar parte de los valores utilizados en los valores de "Espacio de direcciones de la red virtual". Este intervalo de direcciones debe formar parte de los valores utilizados en los valores del espacio de direcciones de la red virtual de Azure que debe enviar a Microsoft. ¿Cómo obtener este intervalo de direcciones IP? El equipo de redes corporativas o el proveedor de servicios deben proporcionar un intervalo de direcciones IP no utilizado actualmente en la red. 

- **Intervalo de direcciones para conectividad ER-P2P:** es el intervalo de direcciones IP para la conexión ExpressRoute (ER) P2P de SAP HANA (Instancias grandes). Este intervalo de direcciones IP debe ser un intervalo de direcciones IP CIDR /29. Este intervalo no se debe superponer con los intervalos de direcciones locales ni con otros intervalos de direcciones IP de Azure. Este intervalo de direcciones IP se usa para configurar la conectividad ER entre la puerta de enlace ExpressRoute de la red virtual de Azure y los servidores de SAP HANA (Instancias grandes). ¿Cómo obtener este intervalo de direcciones IP? El equipo de redes corporativas o el proveedor de servicios deben proporcionar un intervalo de direcciones IP no utilizado actualmente en la red. **Se debe enviar a Microsoft este intervalo de direcciones IP al solicitar una implementación inicial**
  
- **Intervalo de direcciones del grupo de direcciones IP de servidor:** este intervalo de direcciones IP se usa para asignar direcciones IP individuales a los servidores de HANA Instancias grandes. El tamaño de subred recomendado es un bloque CIDR /24, aunque si es necesario, puede ser menor hasta un mínimo de 64 direcciones IP. De este intervalo, las primeras 30 direcciones IP están reservadas para uso de Microsoft. Asegúrese de tenerlo en cuenta al elegir el tamaño del intervalo. Este intervalo no se debe superponer con los intervalos de direcciones locales ni con otras direcciones IP de Azure. ¿Cómo obtener este intervalo de direcciones IP? El equipo de redes corporativas o el proveedor de servicios deben proporcionar un intervalo de direcciones IP no utilizado actualmente en la red. Un bloque CIDR /24 único (recomendado) que se usará para asignar las direcciones IP específicas necesarias para SAP HANA en Azure (Instancias grandes). **Se debe enviar a Microsoft este intervalo de direcciones IP al solicitar una implementación inicial**
 
Aunque tiene que definir y planear los intervalos de direcciones IP anteriores, no es necesario transmitirlos todos a Microsoft. Para resumir lo anterior, los intervalos de direcciones IP que es necesario notificar a Microsoft son:

- Espacios de direcciones de la red virtual de Azure
- Intervalo de direcciones para conectividad ER-P2P
- Intervalo de direcciones del grupo de direcciones IP de servidor

Al agregar redes virtuales adicionales que necesitan conectarse a HANA Instancias grandes, es necesario enviar a Microsoft el nuevo espacio de direcciones de la red virtual de Azure que está agregando. 

A continuación se muestra un ejemplo de los diferentes intervalos y algunos intervalos de ejemplo que se deben configurar y, en algunos casos, proporcionar a Microsoft. Como se puede observar, en el primer ejemplo no se agrega el valor del espacio de direcciones de la red virtual de Azure; en su lugar, se define a partir de los intervalos del primer intervalo de direcciones IP de la subred de la máquina virtual de Azure y del intervalo de direcciones IP de la subred de la puerta de enlace de la red virtual. El uso de varias subredes de máquina virtual en la red virtual de Azure funcionaría mediante la configuración y el envío de los intervalos de direcciones IP adicionales de las subredes de máquina virtual adicionales como parte del espacio de direcciones de la red virtual de Azure.

![Intervalos de direcciones IP necesarios en la implementación mínima de SAP HANA en Azure (Instancias grandes)](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

También tiene la posibilidad de agregar los datos que envíe a Microsoft. En ese caso, el espacio de direcciones de la red virtual de Azure solo incluiría un espacio. Con los intervalos de direcciones IP usados en el ejemplo anterior. Este espacio de direcciones de red virtual agregado podría tener un aspecto como este:

![Segunda posibilidad de intervalos de direcciones IP necesarios en la implementación mínima de SAP HANA en Azure (Instancias grandes)](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

Como puede ver más arriba, en lugar de dos intervalos más pequeños que definían el espacio de direcciones de la red virtual de Azure, tenemos un intervalo mayor que abarca 4096 direcciones IP. Una definición de gran tamaño del espacio de direcciones deja algunos intervalos bastante grandes sin usar. Puesto que los valores del espacio de direcciones de la red virtual se usan para la propagación de la ruta BGP, la utilización de los intervalos no usados en instancias locales o en otra parte de la red puede causar problemas de enrutamiento. Por este motivo, se recomienda mantener el espacio de direcciones estrechamente alineado con el espacio de direcciones de la subred realmente utilizado. En caso necesario, siempre puede agregar nuevos valores al espacio de direcciones más adelante sin incurrir en tiempo de inactividad en la red virtual.
 
> [!IMPORTANT] 
> Los intervalos de direcciones IP de ER-P2P, del grupo de direcciones IP de servidor y del espacio de direcciones de la red virtual de Azure **NO** deben superponerse entre sí ni con ningún otro intervalo usado en la red; cada uno de ellos debe ser diferente y, como muestran los dos gráficos anteriores, no pueden ser una subred de cualquier otro intervalo. Si se producen superposiciones entre los intervalos, la red virtual de Azure podría no conectarse al circuito de ExpressRoute.

### <a name="next-steps-after-address-ranges-have-been-defined"></a>Pasos siguientes después de definir los intervalos de direcciones
Después de definir los intervalos de direcciones IP, son necesarias las siguientes actividades:

1. Enviar los intervalos de direcciones IP del espacio de nombres de la red virtual de Azure, de la conectividad ER-P2P y del grupo de direcciones IP de servidor, junto con los otros datos indicados al principio del documento. En este momento también podría empezar a crear la red virtual y las subredes de la máquina virtual. 
2. Microsoft crea un circuito de ExpressRoute entre la suscripción de Azure y el sello de HANA Instancias grandes.
3. Microsoft crea una red de inquilino en el sello de HANA Instancias grandes.
4. Microsoft configura las redes en la infraestructura de SAP HANA en Azure (Instancias grandes) de forma que acepten las direcciones IP del espacio de direcciones de la red virtual de Azure que se comunica con HANA (Instancias grandes).
5. Según la SKU concreta de SAP HANA en Azure (Instancias grandes) que se haya adquirido, Microsoft asigna una unidad de proceso en una red de inquilino, asigna y monta almacenamiento e instala el sistema operativo (SUSE o Red Hat Linux). Las direcciones IP para estas unidades se toman del intervalo de direcciones del grupo de direcciones IP de servidor que envió a Microsoft.

Al final del proceso de implementación, Microsoft le proporciona los siguientes datos:
- Información necesaria para conectar las redes virtuales de Azure al circuito de ExpressRoute que conecta las redes virtuales de Azure a HANA Instancias grandes:
     - Claves de autorización
     - PeerID de ExpressRoute
- Datos de acceso a HANA Instancias grandes después de establecer el circuito de ExpressRoute y la red virtual de Azure.

También puede buscar la secuencia de conexión de HANA (Instancias grandes) en el documento [End to End Setup for SAP HANA Large Instances (Configuración de extremo a extremo para SAP HANA (Instancias grandes))](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/). Muchos de los pasos siguientes se muestran en una implementación de ejemplo en ese documento. 


## <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>Conexión de una red virtual a ExpressRoute de HANA Instancias grandes

Una vez definidos todos los intervalos de direcciones IP y con los datos devueltos por Microsoft, puede comenzar a conectar la red virtual creada anteriormente a HANA Instancias grandes. Una vez creada la red virtual de Azure, es necesario crear una puerta de enlace de ExpressRoute en la red virtual para vincular la red al circuito de ExpressRoute que conecta con el inquilino del cliente en la marca Instancias grandes.

> [!NOTE] 
> Este paso puede tardar hasta 30 minutos en completarse, ya que la nueva puerta de enlace se crea en la suscripción de Azure designada y, después, se conecta a la red virtual de Azure especificada.

Si ya existe una puerta de enlace, compruebe si se trata de una puerta de enlace de ExpressRoute o no. Si no lo es, debe eliminarla y volver a crearla como una puerta de enlace de ExpressRoute. Si ya hay una puerta de enlace de ExpressRoute establecida, la red virtual de Azure ya está conectada al circuito de ExpressRoute para la conectividad local; por tanto, continúe con la sección Vinculación de redes virtuales, que aparece a continuación.

- Use el (nuevo) [Azure Portal](https://portal.azure.com/) o PowerShell para crear una puerta de enlace de VPN de ExpressRoute conectada a la red virtual.
  - Si usa Azure Portal, agregue una nueva **puerta de enlace de red virtual** y seleccione **ExpressRoute** como tipo de puerta de enlace.
  - Si prefiere PowerShell, descargue y use la versión más reciente [SDK de Azure PowerShell](https://azure.microsoft.com/downloads/) para garantizar una experiencia óptima. Los siguientes comandos crean una puerta de enlace de ExpressRoute. Los textos precedidos por un signo _$_ son variables definidas por el usuario que es necesario actualizar con la información específica.

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

> [!IMPORTANT]
> Para HANA (Instancias grandes) de los tipos de SKU S384, S384m, S384xm, S576, S768 y S960 (SKU de clase de tipo II), el uso de la SKU de puerta de enlace UltraPerformance es obligatorio.

### <a name="linking-vnets"></a>Vinculación de redes virtuales

Ahora que la red virtual de Azure tiene una puerta de enlace de ExpressRoute, utilice la información de autorización proporcionada por Microsoft para conectar la puerta de enlace de ExpressRoute al circuito de ExpressRoute de SAP HANA en Azure (Instancias grandes) creado para esta conectividad. Este paso puede realizarse mediante Azure Portal o PowerShell. Aunque se recomienda Azure Portal, las instrucciones de PowerShell son las siguientes. 

- Ejecute los siguientes comandos para cada puerta de enlace de la red virtual con un AuthGUID diferente para cada conexión. Las dos primeras entradas mostradas en el siguiente script proceden de la información proporcionada por Microsoft. Además, el AuthGUID es específico de cada red virtual y su puerta de enlace. Significa que, si quiere agregar otra red virtual de Azure, tiene que obtener otro AuthID para el circuito de ExpressRoute que conecta HANA (Instancias grandes) a Azure. 

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

Si quiere conectar la puerta de enlace a varios circuitos de ExpressRoute asociados a la suscripción, puede que tenga que ejecutar este paso más de una vez. Por ejemplo, probablemente vaya a conectar la misma puerta de enlace de la red virtual al circuito de ExpressRoute que conecta la red virtual a la red local.

## <a name="adding-more-ip-addresses-or-subnets"></a>incorporación de más direcciones IP o subredes

Use Azure Portal, PowerShell o la CLI al agregar más direcciones IP o subredes.

En este caso, se recomienda agregar el nuevo intervalo de direcciones IP como un nuevo intervalo al espacio de direcciones de la red virtual en lugar de generar un nuevo intervalo agregado. En cualquier caso, debe enviar este cambio a Microsoft para permitir la conectividad desde el nuevo intervalo de direcciones IP a las unidades de HANA Instancias grandes del cliente. Puede abrir una solicitud de soporte técnico de Azure para obtener el nuevo espacio de direcciones de red virtual agregado. Después de recibir la confirmación, realice los pasos siguientes.

Para crear una subred adicional desde Azure Portal, consulte el artículo [Creación de una red virtual mediante Azure Portal](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) y para crearla desde PowerShell, consulte [Creación de una red virtual mediante PowerShell](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="adding-vnets"></a>Incorporación de redes virtuales

Después de conectarse inicialmente a una o varias redes virtuales de Azure, puede agregar otras que tengan acceso a SAP HANA en Azure (Instancias grandes). En primer lugar, envíe una solicitud de soporte técnico de Azure que incluya tanto la información específica para identificar la implementación de Azure en particular como los intervalos de direcciones IP del espacio de direcciones de la red virtual de Azure. A continuación, SAP HANA en Azure Service Management, proporciona la información necesaria para conectar las redes virtuales y ExpressRoute adicionales. Para cada red virtual necesita una clave de autorización única a fin de conectar el circuito de ExpressRoute a HANA (Instancias grandes).

Pasos para agregar una nueva red virtual de Azure:

1. Realice el primer paso del proceso de incorporación; vea la sección _Creación de una red virtual de Azure_.
2. Realice el segundo paso del proceso de incorporación; vea la sección _Creación de una subred de puerta de enlace_.
3. Para conectar las redes virtuales adicionales al circuito de ExpressRoute de HANA (Instancias grandes), abra una solicitud de soporte técnico de Azure con información sobre la nueva red virtual y solicite una nueva clave de autorización.
4. Cuando reciba la notificación de que la autorización está completa, use la información de autorización proporcionada por Microsoft para realizar el tercer paso del proceso de incorporación; vea la sección _Vinculación de redes virtuales_.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Aumento del ancho de banda del circuito de ExpressRoute

Consulte con SAP HANA en Azure Service Management. Si se le recomienda aumentar el ancho de banda del circuito de ExpressRoute de SAP HANA en Azure (Instancias grandes), cree una solicitud de soporte técnico de Azure. Puede solicitar un aumento del ancho de banda de un solo circuito de hasta 10 Gbps. Recibirá una notificación cuando la operación se complete; no es necesaria ninguna acción adicional para habilitar esta velocidad superior en Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Incorporación de un circuito de ExpressRoute adicional

Consulte con SAP HANA en Azure Service Management; si se le recomienda agregar otro circuito de ExpressRoute, abra una solicitud de soporte técnico de Azure para crear un circuito de ExpressRoute y obtener información de autorización para conectarse a él. Para que la administración de servicios de SAP HANA en Azure proporcione la autorización, es necesario definir el espacio de direcciones que se va a usar en las redes virtuales antes de realizar la solicitud.

Una vez creado el nuevo circuito y terminada la configuración de administración de servicios de SAP HANA en Azure, recibirá una notificación con la información que necesita para continuar. Siga los pasos antes indicados para crear y conectar la nueva red virtual para este circuito adicional. No puede conectar redes virtuales de Azure a este circuito adicional si ya están conectadas a otro circuito de ExpressRoute de SAP HANA en Azure (Instancias grandes) en la misma región de Azure.

## <a name="deleting-a-subnet"></a>Eliminación de una subred

Para quitar una subred de red virtual, se puede usar Azure Portal, PowerShell o la CLI. Si el intervalo de direcciones IP de la red virtual de Azure o el espacio de direcciones de la red virtual de Azure fuesen intervalos agregados, no son necesarias acciones con Microsoft. Eso salvo que la red virtual todavía esté propagando el espacio de direcciones de la ruta BGP que incluye la subred eliminada. Si ha definido el intervalo de direcciones IP de la red virtual de Azure o el espacio de direcciones de la red virtual de Azure como varios intervalos de direcciones IP, de los cuales uno está asignado a la subred eliminada, debe eliminarlo del espacio de direcciones de la red virtual y, posteriormente, informar a SAP HANA en Azure Service Management para que lo elimine de los intervalos con los que SAP HANA en Azure (Instancias grandes) tiene permitido comunicarse.

Aunque aún no existe ninguna guía específica en Azure.com sobre cómo quitar subredes, el proceso para hacerlo es el inverso del proceso para agregarlas, que se ha descrito antes. Vea el artículo [Creación de una red virtual (clásica) usando Azure Portal](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para más información sobre la creación de subredes.

## <a name="deleting-a-vnet"></a>Eliminación de una red virtual

Use Azure Portal, PowerShell o la CLI para eliminar una red virtual. SAP HANA en Azure Service Management quita las autorizaciones existentes en el circuito ExpressRoute de SAP HANA en Azure (Instancias grandes) y elimina el intervalo de direcciones IP de la red virtual de Azure o el espacio de direcciones de la red virtual de Azure para la comunicación con HANA Instancias grandes.

Una vez eliminada la red virtual, abra una solicitud de soporte técnico de Azure para proporcionar los intervalos de espacios de direcciones IP que se van a eliminar.

Aunque aún no existe ninguna guía específica en Azure.com sobre cómo quitar redes virtuales, el proceso para hacerlo es el inverso del proceso para agregarlas, que se ha descrito antes. Consulte los artículos [Creación de una red virtual mediante Azure Portal](../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) y [Creación de una red virtual mediante PowerShell](../../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para más información sobre la creación de redes virtuales.

Para asegurarse de que se quita todo, elimine los elementos siguientes:

- La conexión de ExpressRoute, la puerta de enlace de la red virtual, la dirección IP pública de la puerta de enlace de la red virtual y la red virtual

## <a name="deleting-an-expressroute-circuit"></a>Eliminación de un circuito de ExpressRoute

Para quitar un circuito de ExpressRoute adicional de SAP HANA en Azure (Instancias grandes), abra una solicitud de soporte técnico de Azure en la administración de servicios de SAP HANA en Azure y solicite que se elimine. En la suscripción de Azure, puede eliminar o mantener la red virtual, según sea necesario. Sin embargo, debe eliminar la conexión entre el circuito de ExpressRoute de HANA Instancias grandes y la puerta de enlace de la red virtual enlazada.

Si también desea quitar una red virtual, siga las instrucciones de la sección anterior, Eliminación de una red virtual.


