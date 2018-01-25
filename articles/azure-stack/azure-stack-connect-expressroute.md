---
title: "Conexión de Azure Stack a Azure mediante ExpressRoute"
description: "Cómo conectar redes virtuales en Azure Stack a redes virtuales en Azure mediante ExpressRoute."
services: azure-stack
documentationcenter: 
author: victorar
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: 248e9cb521975e9c982684668a68214ce5a1c827
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="connect-azure-stack-to-azure-using-expressroute"></a>Conexión de Azure Stack a Azure mediante ExpressRoute

*Se aplica a: sistemas integrados de Azure Stack y kit de desarrollo de Azure Stack*

Hay dos métodos admitidos para conectar redes virtuales de Azure Stack a redes virtuales de Azure:
   * **De sitio a sitio**

     Una conexión VPN sobre IPsec (IKE v1 e IKE v2). Este tipo de conexión requiere un dispositivo VPN o RRAS. Para obtener más información, consulte [Conectar Azure Stack a Azure mediante VPN](azure-stack-connect-vpn.md).
   * **ExpressRoute**

     Una conexión directa a Azure desde la implementación de Azure Stack. ExpressRoute **no** es una conexión VPN por la red pública de Internet. Para obtener más información sobre Azure ExpressRoute, consulte [Información general sobre ExpressRoute](../expressroute/expressroute-introduction.md).

En este artículo se muestra un ejemplo del uso de ExpressRoute para conectar Azure Stack a Azure.
## <a name="requirements"></a>Requisitos
Los siguientes son requisitos específicos para conectar Azure Stack y Azure mediante ExpressRoute:
* Una suscripción a Azure para crear un circuito ExpressRoute y VNet en Azure.
* Un circuito ExpressRoute aprovisionado a través de un [proveedor de conectividad](../expressroute/expressroute-locations.md).
* Un enrutador que tenga el circuito ExpressRoute conectado a sus puertos WAN.
* El lado de LAN del enrutador está vinculado a la puerta de enlace multiinquilino de Azure Stack.
* El enrutador debe admitir las conexiones VPN de sitio a sitio entre su interfaz LAN y la puerta de enlace multiinquilino de Azure Stack.
* Si se agrega más de un inquilino a la implementación de Azure Stack, el enrutador debe ser capaz de crear múltiples VRF (enrutamiento virtual y reenvío).

En el siguiente diagrama se muestra una vista conceptual de red después de completar la configuración:

![Diagrama conceptual](media/azure-stack-connect-expressroute/Conceptual.png)

**Diagrama 1**

En el siguiente diagrama de arquitectura se muestra cómo varios inquilinos se conectan desde la infraestructura de Azure Stack a través del enrutador de ExpressRoute a Azure en Microsoft Edge:

![Diagrama de la arquitectura](media/azure-stack-connect-expressroute/Architecture.png)

**Diagrama 2**

En el ejemplo mostrado en este artículo se usa la misma arquitectura para conectarse a Azure a través de emparejamiento privado de ExpressRoute. Esto se realiza mediante una conexión VPN de sitio a sitio desde la puerta de enlace de red virtual en Azure Stack a un enrutador de ExpressRoute. Los pasos siguientes en este artículo muestran cómo crear una conexión de extremo a extremo entre dos VNet de dos inquilinos distintos en Azure Stack a sus VNet correspondientes en Azure. Puede elegir agregar tantas VNet de inquilino y replicar los pasos para cada inquilino, o utilizar este ejemplo para implementar una única VNet de inquilino.

## <a name="configure-azure-stack"></a>Configurar Azure Stack
Ahora creará los recursos que necesita para configurar el entorno de Azure Stack como inquilino. Los siguientes pasos muestran lo que tiene que hacer. Estas instrucciones muestran cómo crear recursos mediante el portal de Azure Stack, pero también puede usar PowerShell.

![Pasos para recursos de red](media/azure-stack-connect-expressroute/image2.png)
### <a name="before-you-begin"></a>Antes de empezar
Antes de empezar la configuración, necesita:
* Una implementación de Azure Stack.

   Para obtener información sobre cómo implementar Azure Stack Development Kit, consulte [Azure Stack Development Kit deployment quickstart](azure-stack-deploy-overview.md) (Inicio rápido de implementación de Azure Stack Development Kit).
* Una oferta de Azure Stack a la que el usuario pueda suscribirse.

  Para obtener instrucciones, consulte [Máquinas virtuales disponibles para los usuarios de Azure Stack](azure-stack-tutorial-tenant-vm.md).

### <a name="create-network-resources-in-azure-stack"></a>Creación de recursos de red en Azure Stack

Utilice los procedimientos siguientes para crear los recursos de red necesarios en Azure Stack para cada inquilino:

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Creación de la red virtual y la subred de máquina virtual
1. Inicie sesión en el portal de usuario con una cuenta de usuario (inquilino).

2. En el portal, haga clic en **Nuevo**.

   ![](media/azure-stack-connect-expressroute/MAS-new.png)

3. Seleccione **Redes** en el menú de Marketplace.

4. Haga clic en **Red virtual** en el menú.

5. Escriba los valores en los campos correspondientes usando la siguiente tabla:

   |Campo  |Valor  |
   |---------|---------|
   |NOMBRE     |Tenant1VNet1         |
   |Espacio de direcciones     |10.1.0.0/16|
   |Nombre de subred     |Tenant1-Sub1|
   |Intervalo de direcciones de subred     |10.1.1.0/24|

6. Debería ver la suscripción que creó anteriormente rellena en el campo **Suscripción**.

    a. Para Grupo de recursos, puede crear un grupo de recursos o, si ya tiene uno, seleccione **Usar existente**.

    b. Compruebe la ubicación predeterminada.

    c. Haga clic en **Anclar al panel**.

    d. Haga clic en **Create**(Crear).



#### <a name="create-the-gateway-subnet"></a>Creación de la subred de la puerta de enlace
1. Abra el recurso Red virtual que acaba de crear (Tenant1VNet1) desde el panel.
2. En la sección Configuración, seleccione **Subredes**.
3. Haga clic en **Subred de puerta de enlace** para agregar una subred de puerta de enlace a la red virtual.
   
    ![](media/azure-stack-connect-expressroute/gatewaysubnet.png)
4. El nombre de la subred se establece como **GatewaySubnet** de forma predeterminada.
   Las subredes de puerta de enlace son especiales y tienen que tener este nombre específico para funcionar correctamente.
5. En el campo **Intervalo de direcciones**, compruebe que la dirección sea **10.1.0.0/24**.
6. Haga clic en **Aceptar** para crear la subred de puerta de enlace.

#### <a name="create-the-virtual-network-gateway"></a>Creación de la puerta de enlace de red virtual
1. En el portal de usuario de Azure Stack, haga clic en **Nuevo**.
   
2. Seleccione **Redes** en el menú de Marketplace.
3. Seleccione **Puerta de enlace de red virtual** en la lista de recursos de red.
4. En el campo **Nombre** escriba **GW1**.
5. Haga clic en el elemento **Red virtual** para seleccionar una red virtual.
   Seleccione **Tenant1VNet1** en la lista.
6. Haga clic el elemento de menú **Dirección IP pública**. Cuando se abra la sección **Elegir dirección IP pública**, haga clic en **Crear nuevo**.
7. En el campo **Nombre**, escriba **GW1 PiP** y haga clic en **Aceptar**.
8. El **Tipo de VPN** debe tener seleccionada la opción **Route-based** (Basada en enrutamiento) de forma predeterminada.
    Mantenga este ajuste.
9. Compruebe que la **Suscripción** y la **Ubicación** son correctas. Si lo desea, puede anclar el recurso al panel. Haga clic en **Create**(Crear).

#### <a name="create-the-local-network-gateway"></a>Creación de la puerta de enlace de red local

El propósito del recurso de la puerta de enlace de red Local es indicar la puerta de enlace remota en el otro extremo de la conexión VPN. En este ejemplo, el lado remoto es la subinterfaz LAN del enrutador de ExpressRoute. Para Tenant 1 en este ejemplo, la dirección remota es 10.60.3.255, tal como se muestra en el diagrama 2.

1. Inicie sesión en la máquina física de Azure Stack.
2. Inicie sesión en el portal de usuario con su cuenta de usuario y haga clic en **Nuevo**.
3. Seleccione **Redes** en el menú de Marketplace.
4. Seleccione **local network gateway** (puerta de enlace de red local) en la lista de recursos de red.
5. En el campo **Nombre**, escriba **ER-Router-GW**.
6. Para el campo **Dirección IP**, consulte el diagrama 2. La dirección IP de la subinterfaz LAN del enrutador de ExpressRoute para Tenant 1 es 10.60.3.255. Para su propio entorno, escriba la dirección IP de la interfaz correspondiente a su enrutador.
7. En el campo **Espacio de direcciones**, escriba el espacio de direcciones de las VNet que van a conectarse con Azure. Para este ejemplo, consulte el diagrama 2. Para Tenant 1, tenga en cuenta que las subredes necesarias son **192.168.2.0/24** (esta es la VNet de hub en Azure) y **10.100.0.0/16** (esta es la VNet de spoke en Azure). Escriba las subredes correspondientes para su propio entorno.
   > [!IMPORTANT]
   > En este ejemplo se da por supuesto que usa rutas estáticas para la conexión VPN de sitio a sitio entre la puerta de enlace de Azure Stack y el enrutador de ExpressRoute.

8. Compruebe que **Suscripción**, **Grupo de recursos** y **Ubicación** sean correctos y haga clic en **Crear**.

#### <a name="create-the-connection"></a>Creación de la conexión
1. En el portal de usuario de Azure Stack, haga clic en **Nuevo**.
2. Seleccione **Redes** en el menú de Marketplace.
3. Seleccione **Conexión** en la lista de recursos.
4. En la sección **Conceptos básicos**, elija **De sitio a sitio (IPsec)** como el **Tipo de conexión**.
5. Seleccione **Suscripción**, **Grupo de recursos** y **Ubicación** y haga clic en **Aceptar**.
6. En la sección **Configuración**, haga clic en **Puerta de enlace de red virtual** y haga clic en **GW1**.
7. Haga clic en **Puerta de enlace de red local**y haga clic en **ER Router GW**.
8. En el campo **Nombre de la conexión**, escriba **ConnectToAzure**.
9. En el campo **Clave compartida (PSK)**, escriba **abc123** y haga clic en **Aceptar**.
10. En la sección **Resumen**, haga clic en **Aceptar**.

    Una vez que se haya creado la conexión, puede ver la dirección IP pública que utiliza la puerta de enlace de red virtual. Para buscar la dirección en el portal de Azure Stack, vaya a la puerta de enlace de red virtual. En **Información general**, busque la **Dirección IP pública**. Anote esta dirección; se usará como *Dirección IP interna* en la sección siguiente (si corresponde su implementación).

    ![](media/azure-stack-connect-expressroute/GWPublicIP.png)

#### <a name="create-a-virtual-machine"></a>de una máquina virtual
Para validar los datos que se desplazan a través de la conexión VPN, es necesario que las máquinas virtuales envíen y reciban datos en cada VNet de Azure Stack. Ahora, cree una máquina virtual y colóquela en la subred de máquina virtual en la red virtual.

1. En el portal de usuario de Azure Stack, haga clic en **Nuevo**.
2. Seleccione **Máquinas virtuales** en el menú de Marketplace.
3. En la lista de imágenes de máquinas virtuales, seleccione la imagen **Windows Server 2016 Datacenter Eval** y haga clic en **Crear**.
4. En la sección **Conceptos básicos**, en el campo **Nombre** escriba **VM01**.
5. Escriba un nombre de usuario y una contraseña válidos. Usará esta cuenta para iniciar sesión en la máquina virtual una vez creada.
6. Proporcione **Suscripción**, **Grupo de recursos** y **Ubicación** y haga clic en **Aceptar**.
7. En la sección **Tamaño**, haga clic en un tamaño de máquina virtual para esta instancia y luego haga clic en **Seleccionar**.
8. En la sección **Configuración**, acepte los valores predeterminados. Pero asegúrese de que la red virtual seleccionada sea **Tenant1VNet1** y la subred esté establecida en **10.1.1.0/24**. Haga clic en **OK**.
9. Revise la configuración en la sección **Resumen** y haga clic en **Aceptar**.

Para cada VNet de inquilino a la que desee conectarse, repita los pasos anteriores de la sección **Creación de la red virtual y la subred de máquina virtual** a la **Creación de una máquina virtual**.

### <a name="configure-the-nat-virtual-machine-for-gateway-traversal"></a>Configuración de la máquina virtual de NAT para cada recorrido de puerta de enlace
> [!IMPORTANT]
> Esta sección está dirigida únicamente a implementaciones de Azure Stack Development Kit. La NAT no es necesaria para las implementaciones de varios nodos.

El Azure Stack Development Kit está autocontenido y aislado de la red en el que se implementa el host físico. Por lo tanto, la red de VIP “externa” a la que están conectadas las puertas de enlace no es externa, sino que, está oculta detrás de un enrutador que realiza la traducción de direcciones de red (NAT).
 
El enrutador es una máquina virtual Windows Server (**AzS-BGPNAT01**) que ejecuta el rol de Servicios de enrutamiento y acceso remoto (RRAS) en la infraestructura de Azure Stack Development Kit. Tiene que configurar NAT en la máquina virtual AzS-BGPNAT01 para permitir la conexión VPN de sitio a sitio en ambos extremos.

#### <a name="configure-the-nat"></a>Configuración de la NAT

1. Inicie sesión en el equipo físico de Azure Stack con su cuenta de administrador.
2. Copie y edite el siguiente script de PowerShell y ejecútelo en un Windows PowerShell ISE con privilegios elevados. Reemplace la contraseña de administrador. La dirección devuelta es la *Dirección de BGPNAT externo*.

   ```
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "azs-bgpnat01" `
    -Password $Password
   ```
4. Para configurar la NAT, copie y edite el siguiente script de PowerShell y ejecútelo en un Windows PowerShell ISE con privilegios elevados. Edite el script para reemplazar la *Dirección de BGPNAT externo* y la *Dirección IP interna* (que anotó anteriormente en la sección **Creación de la conexión**).

   En los diagramas de ejemplo, la *Dirección de BGPNAT externo* es 10.10.0.62 y la *Dirección IP interna* es 192.168.102.1.

   ```
   $ExtBgpNat = '<External BGPNAT address>'
   $IntBgpNat = '<Internal IP address>'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName azs-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

## <a name="configure-azure"></a>Configuración de Azure
Ahora que ha completado la configuración de Azure Stack, puede implementar algunos recursos de Azure. El siguiente diagrama muestra una red virtual de inquilino de ejemplo en Azure. Puede usar cualquier esquema de nombres y de direccionamiento para VNet en Azure. Sin embargo, el intervalo de direcciones de las VNet en Azure y Azure Stack debe ser único y no superponerse.

![VNet de Azure](media/azure-stack-connect-expressroute/AzureArchitecture.png)

**Diagrama 3**

Los recursos que se implementan en Azure son similares a los recursos que se implementar en Azure Stack. De forma similar, se implementan:
* Redes virtuales y subredes
* Una subred de puerta de enlace
* Una puerta de enlace de red virtual
* Una conexión
* Un circuito ExpressRoute

La infraestructura de red de Azure de ejemplo está configurada de la manera siguiente:
* Se utiliza un modelo estándar de VNet de hub (192.168.2.0/24) y spoke (10.100.0.0./16).
* Las cargas de trabajo se implementan en la VNet de spoke y el circuito ExpressRoute se conecta a la VNet de hub.
* Las dos VNet están vinculadas mediante la característica de emparejamiento de VNET.

### <a name="configure-vnets"></a>Configuración de VNet
1. Inicie sesión en Azure Portal con las credenciales de Azure.
2. Cree la VNet de hub con el espacio de direcciones 192.168.2.0/24. Cree una subred con el intervalo de direcciones 192.168.2.0/25 y agregue una subred de puerta de enlace con el intervalo de direcciones 192.168.2.128/27.
3. Cree la VNet de spoke con el intervalo de direcciones 10.100.0.0/16.


Para obtener más información sobre cómo crear redes virtuales en Azure, consulte [Creación de una red virtual con varias subredes](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

### <a name="configure-an-expressroute-circuit"></a>Configuración de un circuito ExpressRoute

1. Revise los requisitos previos de ExpressRoute en [Requisitos previos y lista de comprobación de ExpressRoute](../expressroute/expressroute-prerequisites.md).
2. Siga los pasos de [Creación y modificación de un circuito ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) para crear un circuito ExpressRoute con su suscripción a Azure.
3. Comparta la clave de servicio del paso anterior con su anfitrión o proveedor para aprovisionar el circuito ExpressRoute en su extremo.
4. Siga los pasos de [Creación y modificación del emparejamiento de un circuito ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md) para configurar el emparejamiento privado en el circuito ExpressRoute.

### <a name="create-the-virtual-network-gateway"></a>Creación de la puerta de enlace de red virtual

* Siga los pasos de [Configuración de una puerta de enlace de red virtual para ExpressRoute con PowerShell](../expressroute/expressroute-howto-add-gateway-resource-manager.md) para crear una puerta de enlace de red virtual para ExpressRoute en la VNet de hub.

### <a name="create-the-connection"></a>Creación de la conexión

* Para vincular el circuito ExpressRoute con la VNet de hub, siga los pasos de [Conexión de una red virtual a un circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).

### <a name="peer-the-vnets"></a>Empareje las VNet

* Empareje las VNet de hub y spoke según los pasos de [Creación de un emparejamiento de redes virtuales con Azure Portal](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md). Al configurar el emparejamiento de VNet, asegúrese de seleccionar las opciones siguientes:
   * De hub a spoke: **Permitir tránsito de puerta de enlace**
   * De spoke a hub: **Usar puerta de enlace remota**

### <a name="create-a-virtual-machine"></a>de una máquina virtual

* Implemente las máquinas virtuales de carga de trabajo en la VNet de spoke.

Repita estos pasos para cualquier VNet de inquilino adicional que desee conectar en Azure a través de sus circuitos ExpressRoute correspondientes.

## <a name="configure-the-router"></a>Configuración del enrutador

Puede usar el siguiente diagrama de infraestructura de un extremo a otro para guiar la configuración del enrutador ExpressRoute. Este diagrama muestra dos inquilinos (Tenant 1 y Tenant 2) con sus circuitos ExpressRoute correspondientes. Cada uno está vinculado a su propio VRF (enrutamiento virtual y reenvío) en el lado LAN y WAN del enrutador ExpressRoute para garantizar el aislamiento de extremo a extremo entre los dos inquilinos. Anote las direcciones IP usadas en las interfaces del enrutador a medida que siga la configuración del ejemplo.

![Diagrama de un extremo a otro](media/azure-stack-connect-expressroute/EndToEnd.png)

**Diagrama 4**

Puede usar cualquier enrutador que admita VPN de IKEv2 y BGP para finalizar la conexión VPN de sitio a sitio desde Azure Stack. El mismo enrutador se utiliza para conectarse a Azure con un circuito ExpressRoute. 

Este es un ejemplo de configuración de Cisco ASR 1000 que admite la infraestructura de red que se muestra en el Diagrama 4:

```
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2 
description IKEv2 proposal for Tenant 1 
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2 
description IKEv2 proposal for Tenant 2 
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2 
description IKEv2 Policy for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2 
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1 
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256 
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256 
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2 
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2 
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP Express Route Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2 
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF 
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF 
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>Comprobación de la conexión

Pruebe la conexión después de establecer la conexión de sitio a sitio y el circuito ExpressRoute. Esta tarea es sencilla.  Inicie sesión en una de las máquinas virtuales creadas en la VNet de Azure y haga ping a la máquina virtual que creó en el entorno de Azure Stack o viceversa. 

Para asegurarse de enviar el tráfico a través de conexiones de sitio a sitio y ExpressRoute, debe hacer ping a la dirección IP dedicada (DIP) de la máquina virtual en ambos extremos y no a la dirección VIP de la máquina virtual. Para ello, tiene que encontrar y anotar la dirección en el otro extremo de la conexión.

### <a name="allow-icmp-in-through-the-firewall"></a>Permitir que ICMP pase por el firewall
De manera predeterminada, Windows Server 2016 no permite que los paquetes ICMP pasen por el firewall. Por lo tanto, para cada máquina virtual que use en la prueba, ejecute el siguiente cmdlet en una ventana de PowerShell con privilegios elevados:


   ```
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="ping-the-azure-stack-virtual-machine"></a>Hacer ping a la máquina virtual de Azure Stack

1. Inicie sesión en el portal de usuarios de Azure Stack con una cuenta de inquilino.
2. En la barra de navegación de la izquierda, haga clic en **Máquinas virtuales**.
3. Busque la máquina virtual que creó anteriormente y haga clic en ella.
4. En la sección de la máquina virtual, haga clic en **Conectar**.
5. Abra una ventana de PowerShell con privilegios elevados y escriba **ipconfig/all**.
6. Encuentre la dirección IPv4 en la salida y anótela. Haga ping a esta dirección desde la máquina virtual en la VNet de Azure. En el entorno de ejemplo, la dirección proviene de la subred 10.1.1.x/24. En su entorno, la dirección puede variar. Sin embargo, debe estar dentro de la subred que creó para la subred de VNet de inquilino.


### <a name="view-data-transfer-statistics"></a>Visualización de estadísticas de transferencia de datos

Si desea saber la cantidad de tráfico que está pasando por la conexión, puede encontrar esta información en la sección Conexión en el portal de usuario de Azure Stack. Esta información también es otra buena forma de comprobar que el ping que acaba de enviar haya pasado por las conexiones VPN y ExpressRoute.

1. Inicie sesión en el portal de usuarios de Microsoft Azure Stack con la cuenta de inquilino.
2. Vaya al grupo de recursos donde se creó la puerta de enlace de VPN y seleccione el tipo de objeto **Conexiones**.
3. Haga clic en la conexión **ConnectToAzure** en la lista.
4. En la sección **Conexión**, puede ver las estadísticas de **Datos de entrada** y **Datos de salida**. Debería ver algunos valores distintos de cero.

   ![Datos de entrada y salida](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>pasos siguientes
[Implementar aplicaciones en Azure y Azure Stack](azure-stack-solution-pipeline.md)
