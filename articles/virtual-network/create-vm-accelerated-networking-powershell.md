---
title: "Creación de una máquina virtual de Azure con Accelerated Networking | Microsoft Docs"
description: "Aprenda a crear una máquina virtual Linux con Accelerated Networking."
services: virtual-network
documentationcenter: 
author: jdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/04/2018
ms.author: jimdial
ms.openlocfilehash: f4908963e0650be9b12b745f6868a1ba6ad933e4
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="create-a-windows-virtual-machine-with-accelerated-networking"></a>Creación de una máquina virtual Windows con Accelerated Networking

> [!IMPORTANT] 
> Las máquinas virtuales deben crearse con Accelerated Networking habilitado. Esta característica no se puede habilitar en las máquinas virtuales existentes. Puede seguir los pasos siguientes para habilitar Accelerated Networking:
>   1. Eliminación de la máquina virtual
>   2. Volver a crear una máquina virtual con Accelerated Networking habilitado
>

En este tutorial, aprenderá a crear una máquina virtual Windows con Accelerated Networking. Accelerated Networking habilita la virtualización de E/S de raíz única (SR-IOV) en una máquina virtual (VM), lo que mejora significativamente su rendimiento en la red. Este método de alto rendimiento omite el host de la ruta de acceso de datos, lo que reduce la latencia, la inestabilidad y la utilización de la CPU para usarse con las cargas de trabajo de red más exigentes en los tipos de máquina virtual admitidos. En la siguiente imagen, se muestra la comunicación entre dos máquinas virtuales (VM) con y sin Accelerated Networking:

![De comparación](./media/create-vm-accelerated-networking/accelerated-networking.png)

Sin Accelerated Networking, todo el tráfico de red de entrada y salida de la máquina virtual tiene que atravesar el host y el conmutador virtual. El conmutador virtual se encarga de toda la aplicación de directivas, como grupos de seguridad de red, listas de control de acceso, aislamiento y otros servicios virtualizados de red, al tráfico de red. Para más información sobre los conmutadores virtuales, lea el artículo sobre [virtualización de red y conmutador virtual de Hyper-V](https://technet.microsoft.com/library/jj945275.aspx).

Con Accelerated Networking, el tráfico de red llega a la interfaz de red (NIC) de la máquina virtual y se reenvía después a la máquina virtual. Todas las directivas de red que el conmutador virtual aplica se descargan y aplican en el hardware. La aplicación de directivas en hardware permite que la NIC reenvíe el tráfico de red directamente a la máquina virtual, pasando por alto el host y el conmutador virtual, al mismo tiempo que se mantienen todas las directivas aplicadas en el host.

Las ventajas de Accelerated Networking solo se aplican a la máquina virtual donde esté habilitado. Para obtener resultados óptimos, lo ideal es habilitar esta característica en al menos dos máquinas virtuales conectadas a la misma instancia de Azure Virtual Network (VNet). Al comunicarse entre redes virtuales o conectarse de forma local, esta característica tiene un efecto mínimo sobre la latencia total.

## <a name="benefits"></a>Ventajas
* **Menor latencia/Más paquetes por segundo (pps):** al quitarse el conmutador virtual de la ruta de acceso de datos, se elimina el tiempo que los paquetes pasan en el host para el procesamiento de las directivas y se aumenta el número de paquetes que se pueden procesar dentro de la máquina virtual.
* **Inestabilidad reducida:** el procesamiento del conmutador virtual depende de la cantidad de directivas que deben aplicarse y la carga de trabajo de la CPU que se encarga del procesamiento. Al descargarse la aplicación de directivas en el hardware, se elimina esa variabilidad, ya que los paquetes se entregan directamente a la máquina virtual y se elimina el host de la comunicación de la máquina virtual, así como todas las interrupciones de software y los cambios de contexto.
* **Disminución de la utilización de la CPU:** el pasar por alto el conmutador virtual en el host conlleva una disminución de la utilización de la CPU para procesar el tráfico de red.

## <a name="supported-operating-systems"></a>Sistemas operativos compatibles
Microsoft Windows Server 2012 R2 Datacenter y Windows Server 2016.

## <a name="supported-vm-instances"></a>Instancias de máquina virtual admitidas
Accelerated Networking se admite con tamaños de instancia optimizados para procesos de cuatro o más vCPU y con los fines más generales. En instancias como DSv3/D o E/ESv3 que admiten hyperthreading, Accelerated Networking se admite en instancias de máquinas virtuales con ocho o más vCPU. Las series admitidas son: D/DSv2, D/DSv3, ESv3/E, F/Fs/Fsv2 y Ms/Mms.

Para más información sobre las instancias de máquinas virtuales, consulte [Tamaños de las máquinas virtuales con Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="regions"></a>Regiones
Está disponible en todas las regiones públicas de Azure y la nube de Azure Government. 

## <a name="limitations"></a>Limitaciones
Cuando se utiliza esta funcionalidad, existen las siguientes limitaciones:

* **Creación de interfaz de red:** Accelerated Networking solo se puede habilitar para una nueva interfaz de red. No se puede habilitar para una NIC existente.
* **Creación de máquina virtual:** una NIC con Accelerated Networking habilitado solo se puede asociar a una máquina virtual cuando esta se crea. La NIC no puede asociarse a una máquina virtual existente. Si se agrega la máquina virtual a un conjunto de disponibilidad, todas las máquinas virtuales del conjunto de disponibilidad también deben tener habilitada la red acelerada.
* **Implementación solo mediante Azure Resource Manager:** las máquinas virtuales (clásicas) no se pueden implementar con Accelerated Networking.

## <a name="create-a-virtual-network"></a>Crear una red virtual

Instale [Azure PowerShell](/powershell/azure/install-azurerm-ps) versión 5.1.1 o posterior. Para encontrar la versión instalada actualmente, ejecute `Get-Module -ListAvailable AzureRM`. Si necesita instalar o actualizar una versión, instale la más reciente del módulo AzureRM desde la [Galería de PowerShell](https://www.powershellgallery.com/packages/AzureRM). En una sesión de PowerShell, inicie sesión en una cuenta de Azure con [Add-AzureRmAccount](/powershell/module/AzureRM.Profile/Add-AzureRmAccount).

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetro de ejemplo incluían *myResourceGroup*, *myNic* y *myVM*.

Cree un grupo de recursos con [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *centralus*:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "centralus"
```

Primero, cree una configuración de subred con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetworkSubnetConfig). En el ejemplo siguiente se crea una subred denominada *mySubnet*:

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name "mySubnet" `
    -AddressPrefix "192.168.1.0/24"
```

Cree una red virtual con [New-AzureRmVirtualNetwork](/powershell/module/AzureRM.Network/New-AzureRmVirtualNetwork) y la subred *mySubnet*.

```powershell
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "centralus" `
    -Name "myVnet" `
    -AddressPrefix "192.168.0.0/16" `
    -Subnet $Subnet
```

## <a name="create-a-network-security-group"></a>Crear un grupo de seguridad de red

En primer lugar, cree una regla de grupo de seguridad de red con [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityRuleConfig).

```powershell
$rdp = New-AzureRmNetworkSecurityRuleConfig `
    -Name 'Allow-RDP-All' `
    -Description 'Allow RDP' `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 100 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389
```

A continuación, cree un grupo de seguridad de red con [New-AzureRmNetworkSecurityGroup](/powershell/module/AzureRM.Network/New-AzureRmNetworkSecurityGroup) y asígnele la regla de seguridad *Allow-RDP-All*. Además de la regla *Allow-RDP-All*, el grupo de seguridad de red contiene varias reglas predeterminadas. Una regla predeterminada deshabilita todo el acceso entrante desde Internet, lo que constituye la razón por la que la regla *Allow-RDP-All* se asigna al grupo de seguridad de red para que pueda conectarla remotamente a la máquina virtual, una vez que se crea.

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroup `
    -Location centralus `
    -Name "myNsg" `
    -SecurityRules $rdp
```

Asocie el grupo de seguridad de red a la subred *mySubnet* con [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/AzureRM.Network/Set-AzureRmVirtualNetworkSubnetConfig). La regla en el grupo de seguridad de red es eficaz para todos los recursos implementados en la subred.

```powershell
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name 'mySubnet' `
    -AddressPrefix "192.168.1.0/24" `
    -NetworkSecurityGroup $nsg
```

## <a name="create-a-network-interface-with-accelerated-networking"></a>Creación de una interfaz de red con Accelerated Networking
Cree una dirección IP pública con [New-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/New-AzureRmPublicIpAddress). No se requiere una dirección IP pública si no tiene pensado acceder a la máquina virtual desde Internet, pero, para completar los pasos de este artículo, sí es necesaria.

```powershell
$publicIp = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroup `
    -Name 'myPublicIp' `
    -location centralus `
    -AllocationMethod Dynamic
```

Cree una interfaz de red con [AzureRmNetworkInterface New](/powershell/module/AzureRM.Network/New-AzureRmNetworkInterface) con Accelerated Networking habilitado y asígnele la dirección IP pública. En el ejemplo siguiente se crea una interfaz de red denominada *myNic* en *mySubnet* de la red virtual *myVnet* y asigna la dirección IP pública *myPublicIp* :

```powershell
$nic = New-AzureRmNetworkInterface `
    -ResourceGroupName "myResourceGroup" `
    -Name "myNic" `
    -Location "centralus" `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIp.Id `
    -EnableAcceleratedNetworking
```

## <a name="create-the-virtual-machine"></a>Creación de la máquina virtual

Establezca las credenciales de la máquina virtual en la variable `$cred` con [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```powershell
$cred = Get-Credential
```

En primer lugar, defina la máquina virtual con [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). En el ejemplo siguiente se define una máquina virtual denominada *myVM* con un tamaño que admite Accelerated Neworking (*Standard_DS4_v2*):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
```

Para obtener una lista de todos los tamaños de máquinas virtuales y las características, consulte [tamaños de máquina virtual de Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Cree el resto de la configuración de máquinas virtuales con [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) y [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage). En el ejemplo siguiente se crea una máquina virtual con Windows Server 2016:

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
    -Windows `
    -ComputerName "myVM" `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
    -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" `
    -Skus "2016-Datacenter" `
    -Version "latest"
```

Conecte la interfaz de red que creó anteriormente con [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```

Por último, cree la máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
```

## <a name="confirm-the-driver-is-installed-in-the-operating-system"></a>Confirmación de que el controlador está instalado en el sistema operativo

Una vez creada la máquina virtual en Azure, conéctese a ella y confirme que el controlador está instalado en Windows. 

1. Desde un explorador de Internet, abra[Azure Portal](https://portal.azure.com) e inicie sesión con su cuenta de Azure.
2. En el cuadro que contiene el texto *Buscar recursos*, en la parte superior de Azure Portal, escriba *myVm*. Haga clic en **MyVm** cuando aparezca en los resultados de búsqueda. Si **Creando** está visible en el botón **Conectar**, Azure aún no ha terminado de crear la máquina virtual. Haga clic en **Conectar** en la esquina superior izquierda de la información general, pero solo después de que ya no se vea **Creando** en el botón **Conectar**.
3. Escriba el nombre de usuario y la contraseña que escribió en [Creación de la máquina virtual](#create-the-virtual-machine). Si nunca se ha conectado a una máquina virtual Windows en Azure, consulte [Conexión a la máquina virtual](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).
4. Haga clic con el botón derecho en el botón Inicio de Windows y haga clic en **Administrador de dispositivos**. Expanda el nodo **Adaptadores de red**. Compruebe que el **Adaptador Ethernet de función virtual ConnectX-3 de Mellanox** aparezca como se muestra en la siguiente imagen:
   
    ![Administrador de dispositivos](./media/create-vm-accelerated-networking/device-manager.png)

Las redes aceleradas ya están habilitadas para su máquina virtual.
