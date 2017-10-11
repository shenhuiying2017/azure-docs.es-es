---
title: "Implementar máquinas virtuales con varias NIC (clásica) mediante PowerShell | Microsoft Docs"
description: "Aprenda a crear y configurar máquinas virtuales con varias tarjetas NIC mediante PowerShell."
services: virtual-network, virtual-machines
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: a1a3952c-2dcc-4977-bd7a-52d623c1fb07
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.openlocfilehash: 68ccc1cac22e593b099729fe68c6bee63df44d9b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-vm-classic-with-multiple-nics"></a>Creación de una máquina virtual (clásica) con varias NIC
Puede crear máquinas virtuales (VM) en Azure y asociar varias interfaces de red (NIC) a cada una de las máquinas virtuales. Tener varias NIC es un requisito para muchos dispositivos virtuales de red, por ejemplo, las soluciones de optimización de WAN y de entrega de aplicaciones. Varias NIC también aportan aislamiento del tráfico entre ellas.

![Varias NIC para máquina virtual](./media/virtual-networks-multiple-nics/IC757773.png)

La figura anterior muestra una máquina virtual con tres NIC, cada una de ellas conectada a una subred diferente.

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../resource-manager-deployment-model.md). Este artículo trata del modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen Resource Manager.

* La VIP accesible desde Internet (implementaciones clásicas) solo se admite en la NIC marcada como "predeterminada". Solo hay una VIP a la IP de la NIC predeterminada.
* En estos momentos, no se admiten direcciones IP públicas (implementaciones clásicas) de nivel de instancia (LPIP) para máquinas virtuales con varias NIC.
* El orden de las NIC desde la máquina virtual será aleatorio y también podría cambiar en todas las actualizaciones de infraestructura de Azure. Sin embargo, las direcciones IP y las direcciones MAC Ethernet correspondientes seguirán siendo las mismas. Por ejemplo, suponga que **Eth1** tiene la dirección IP 10.1.0.100 y la dirección MAC 00-0D-3A-B0-39-0D; después de una actualización de la infraestructura de Azure y de reiniciar el equipo, se podría cambiar a **Eth2**, pero el emparejamiento de la IP y la MAC seguirá siendo el mismo. Cuando es un cliente quien ejecuta un reinicio, el orden de NIC seguirá siendo el mismo.
* La dirección de cada NIC en cada máquina virtual debe estar ubicada en una subred; las direcciones que se encuentran en la misma subred se pueden asignar a cada una de las varias NIC de una sola máquina virtual.
* El tamaño de la máquina virtual determina el número de las NIC que se puede crear para una máquina virtual. Consulte los artículos sobre el tamaño de las máquinas virtuales [Windows Server](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) y [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para determinar cuántas NIC admite cada tamaño. 

## <a name="network-security-groups-nsgs"></a>Grupos de seguridad de red (NSG)
En una implementación del Administrador de recursos, cualquier NIC de una máquina virtual puede asociarse a un grupo de seguridad de red (NSG), incluyendo cualquier NIC de una máquina virtual que tenga varias NIC habilitadas. Si a una NIC se le asigna una dirección dentro de una subred que está asociada a un NSG, las reglas de NSG de la subred también se aplican a esa NIC. Además de asociar subredes a NSG, también puede asociar una NIC a un NSG.

Si una subred está asociada a un NSG y una NIC dentro de esa subred está asociada individualmente a un NSG, las reglas asociadas de NSG se aplican en **orden de flujo** según la dirección del tráfico que se pasa dentro o fuera de la NIC:

* **tráfico entrante** cuyo destino es la NIC en cuestión fluye primero a través de la subred y desencadena las reglas de NSG de la subred antes de pasar a la NIC y desencadenar las reglas de NSG de la NIC.
* **tráfico de salida** cuyo origen es la NIC en cuestión fluye primero fuera de la NIC, desencadenando reglas de NSG de la NIC, antes de pasar a la subred y desencadenar reglas de NSG de la subred.

Obtener más información sobre los [Grupos de seguridad de red](virtual-networks-nsg.md) y cómo se aplican en función de las asociaciones de subredes, las máquinas virtuales y las NIC.

## <a name="how-to-configure-a-multi-nic-vm-in-a-classic-deployment"></a>Cómo configurar una máquina virtual con varias NIC en una implementación clásica
Las instrucciones siguientes le ayudarán a crear una máquina virtual de varias NIC con 3 NIC: una NIC predeterminada y dos NIC adicionales. Los pasos de configuración crearán una máquina virtual que se configurará según el fragmento de archivo de configuración de servicio siguiente:

    <VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
    <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
    … Skip over the remainder section …
    </VirtualNetworkSite>


Necesitará cumplir los siguientes requisitos previos antes de poder ejecutar los comandos de PowerShell del ejemplo.

* Una suscripción de Azure.
* Una red virtual configurada. Para obtener más información sobre redes virtuales, consulte [Información general sobre redes virtuales](virtual-networks-overview.md) .
* La versión más reciente de Azure PowerShell descargada e instalada. Consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

Para crear una máquina virtual con varias NIC, complete los pasos siguientes escribiendo cada comando en una única sesión de PowerShell:

1. Seleccione una imagen de máquina virutal en la galería de imágenes de máquinas virtuales de Azure. Tenga en cuenta que las imágenes cambian con frecuencia y están disponibles por región. La imagen especificada en el ejemplo siguiente puede cambiar o puede no estar presente en su región, así que asegúrese de especificar la imagen que necesita.

    ```powershell
    $image = Get-AzureVMImage `
    -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"
    ```

2. Creación de una configuración de máquina virtual

    ```powershell
    $vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
    -Image $image.ImageName –AvailabilitySetName "MyAVSet"
    ```

3. Cree el inicio de sesión de administrador predeterminado.

    ```powershell
    Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
    -Password "<YourAdminPassword>"
    ```

4. Agregue NIC adicionales a la configuración de la máquina virtual.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
    -SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm
    Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
    -SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm
    ```

5. Especifique la subred y la dirección IP de la NIC predeterminada.

    ```powershell
    Set-AzureSubnet -SubnetNames "Frontend" -VM $vm
    Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm
    ```

6. Cree la máquina virtual en la red virtual.

    ```powershell
    New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm
    ```

    > [!NOTE]
    > La red virtual que especifique aquí debe existir previamente (tal como se indicó en los requisitos previos). En el ejemplo siguiente se especifica una red virtual denominada **MultiNIC VNet**.
    >

## <a name="limitations"></a>Limitaciones
Se aplican las siguientes limitaciones al usar varias NIC:

* Las máquinas virtuales con varias NIC deben crearse en redes virtuales de Azure. Las máquinas virtuales que no son de redes virtuales no se pueden configurar con varias NIC.
* Todas las máquinas virtuales de un conjunto de disponibilidad deben usar varias NIC o una NIC única. En un conjunto de disponibilidad no puede haber una combinación de máquinas virtuales de varias NIC y de NIC única. Para las máquinas virtuales de un servicio en la nube se aplican las mismas reglas. No es necesario que las máquinas virtuales de varias NIC tengan el mismo número de tarjetas NIC, siempre y cuando cada una tenga al menos dos.
* Una máquina virtual con una NIC única no se puede configurar con varias NIC (y viceversa) una vez implementada si no se elimina y se vuelve a crear.

## <a name="secondary-nics-access-to-other-subnets"></a>Acceso de las NIC secundarias a otras subredes
De forma predeterminada, las NIC secundarias no se configurarán con una puerta de enlace predeterminada, por lo que el flujo de tráfico en ellas se limitará para que esté dentro de la misma subred. Si los usuarios desean habilitar las NIC secundarias para comunicarse fuera de su propia subred, tendrán que agregar una entrada en la tabla de enrutamiento para configurar la puerta de enlace, tal y como se describe a continuación.

> [!NOTE]
> Las máquinas virtuales creadas antes de julio de 2015 pueden tener una puerta de enlace predeterminada configurada para todas las NIC. La puerta de enlace predeterminada para las NIC secundarias no se quitará hasta que se hayan reiniciado estas máquinas virtuales. En los sistemas operativos que usan el modelo de enrutamiento de host no seguro (como Linux), la conectividad a Internet se puede interrumpir si el tráfico de entrada y salida usa NIC diferentes.
> 

### <a name="configure-windows-vms"></a>Configurar las máquinas virtuales de Windows
Suponga que tiene una máquina virtual de Windows con dos NIC, tal y como se muestra a continuación:

* Dirección IP de la NIC principal: 192.168.1.4
* Dirección IP de la NIC secundaria: 192.168.2.5

La tabla de enrutamiento de IPv4 para esta máquina virtual tendría este aspecto:

    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
          192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
          192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
        192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
          192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
          192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
        192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
            224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
            224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
      255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
      255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
    ===========================================================================

Tenga en cuenta que la ruta predeterminada (0.0.0.0) sólo está disponible para la NIC principal. No podrá obtener acceso a recursos externos a la subred de la NIC secundaria, tal y como se muestra a continuación:

    C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

    Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.

Para agregar una ruta predeterminada en la NIC secundaria, siga estos pasos:

1. Desde un símbolo del sistema, ejecute el comando siguiente para identificar el número de índice de la NIC secundaria:
   
        C:\Users\Administrator>route print
        ===========================================================================
        Interface List
         29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
         27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
          1...........................Software Loopback Interface 1
         14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
         20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
        ===========================================================================
2. Busque la segunda entrada en la tabla con un índice de 27 (en este ejemplo).
3. Desde el símbolo del sistema, ejecute el comando **route add** tal y como se muestra a continuación. En este ejemplo, está especificando 192.168.2.1 como puerta de enlace predeterminada para la NIC secundaria:
   
        route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27
4. Para probar la conectividad, vuelva al símbolo del sistema e intente hacer ping en una subred distinta de la NIC secundaria, tal y como se muestra en el ejemplo siguiente:
   
        C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
   
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
5. Asimismo, puede consultar la tabla de rutas para comprobar la ruta recién agregada, tal y como se muestra a continuación:
   
        C:\Users\Administrator>route print
   
        ...
   
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                  0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
                  0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### <a name="configure-linux-vms"></a>Configurar máquinas virtuales de Linux
En cuanto a las máquinas virtuales de Linux, puesto que el comportamiento predeterminado está usando el enrutamiento del host no seguro, le recomendamos restrinja el flujo de tráfico de las NIC secundarias para que permanezca dentro de la misma subred. Sin embargo, si ciertos escenarios exigen que tenga conectividad fuera de la subred, los usuarios deben habilitar el enrutamiento basado en las directivas para asegurarse de que el tráfico de entrada y salida utiliza la misma NIC.

## <a name="next-steps"></a>Pasos siguientes
* Implemente [máquinas virtuales MultiNIC en un escenario de aplicación de 2 niveles en una implementación del Administrador de recursos](virtual-network-deploy-multinic-arm-template.md).
* Implemente [máquinas virtuales MultiNIC en un escenario de aplicación de 2 niveles en una implementación clásica](virtual-network-deploy-multinic-classic-ps.md).

