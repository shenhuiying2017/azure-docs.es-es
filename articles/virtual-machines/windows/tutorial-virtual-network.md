---
title: "Máquinas virtuales Windows y redes virtuales de Azure | Microsoft Docs"
description: "Tutorial: Administración de máquinas virtuales Windows y redes virtuales de Azure con Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: davidmu
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 7fd0ace35cfe0286c874e4a75b733053aa945d39
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017

---

<a id="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell" class="xliff"></a>

# Administración de máquinas virtuales Windows y redes virtuales de Azure con Azure PowerShell

Las máquinas virtuales de Azure utilizan las redes de Azure para la comunicación de red interna y externa. En este tutorial, creará varias máquinas virtuales (VM) en una red virtual y configurará la conectividad de red entre ellas. Aprenderá a:

> [!div class="checklist"]
> * Crear una red virtual
> * Crear subredes de redes virtuales
> * Controlar el tráfico de red con grupos de seguridad de red
> * Ver reglas de tráfico en acción

Para realizar este tutorial es necesaria la versión 3.6 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps).

<a id="create-vnet" class="xliff"></a>

## Creación de una red virtual

Una red virtual es una representación de su propia red en la nube. Una red virtual es un aislamiento lógico de la nube de Azure dedicada a su suscripción. Dentro de una red virtual, hay subredes, reglas para la conectividad a esas subredes y conexiones desde las máquinas virtuales a las subredes.

Para poder crear otros recursos de Azure, tiene que crear un grupo de recursos con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En el ejemplo siguiente, se crea un grupo de recursos denominado *myRGNetwork* en la ubicación *EastUS*:

```powershell
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

Una subred es un recurso secundario de una red virtual que le ayudará a definir segmentos de espacios de direcciones dentro de un bloque CIDR mediante prefijos de direcciones IP. Las NIC pueden agregarse a subredes y conectarse a máquinas virtuales, lo cual le proporciona conectividad a varias cargas de trabajo.

Cree una subred con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig):

```powershell
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Crear una red virtual denominada *myVNet* con *myFrontendSubnet* con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet
```

<a id="create-front-end-vm" class="xliff"></a>

## Creación de una máquina virtual de "front-end"

Una máquina virtual debe tener un interfaz de red virtual (NIC) para comunicarse en una red virtual. A *myFrontendVM* se obtiene acceso desde Internet, por lo que también se necesita una dirección IP pública. 

Cree una dirección IP pública con [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

Cree una NIC con [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface):


```powershell
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Establezca el nombre de usuario y la contraseña que se necesitan para la cuenta de administrador en la máquina virtual con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Cree las máquinas virtuales con [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem), [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage), [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk), [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) y [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). 

```powershell
$frontendVM = New-AzureRmVMConfig `
    -VMName myFrontendVM `
    -VMSize Standard_D1
$frontendVM = Set-AzureRmVMOperatingSystem `
    -VM $frontendVM `
    -Windows `
    -ComputerName myFrontendVM `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$frontendVM = Set-AzureRmVMSourceImage `
    -VM $frontendVM `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
$frontendVM = Set-AzureRmVMOSDisk `
    -VM $frontendVM `
    -Name myFrontendOSDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
$frontendVM = Add-AzureRmVMNetworkInterface `
    -VM $frontendVM `
    -Id $frontendNic.Id
New-AzureRmVM `
    -ResourceGroupName myRGNetwork `
    -Location EastUS `
    -VM $frontendVM
```

<a id="install-web-server" class="xliff"></a>

## Instalación del servidor web

Puede instalar IIS en *myFrontendVM* usando una sesión del Escritorio remoto. Debe obtener la dirección IP pública de la máquina virtual para acceder a ella.

Puede conseguir la dirección IP pública de *myFrontendVM* con [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). En el ejemplo siguiente se obtiene la dirección IP de *myPublicIPAddress* que se ha creado anteriormente:

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName myRGNetwork `
    -Name myPublicIPAddress | select IpAddress
```

Tome nota de esta dirección IP para poder usarla en pasos futuros.

Utilice el comando siguiente para crear una sesión del Escritorio remoto con *myFrontendVM*. Reemplace *<publicIPAddress>* por la dirección que haya registrado previamente. Cuando se le solicite, escriba las credenciales usadas al crear la máquina virtual.

```
mstsc /v:<publicIpAddress>
``` 

Ahora que ha iniciado sesión en *myFrontendVM*, puede usar una sola línea de PowerShell para instalar IIS y habilitar la regla de firewall local para permitir el tráfico web. Abra una ventana de PowerShell y ejecute el siguiente comando:

Use [Install-WindowsFeature](https://technet.microsoft.com/itpro/powershell/windows/servermanager/install-windowsfeature) para ejecutar la extensión de script personalizado que instala el servidor web de IIS:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Ahora puede usar la dirección IP pública para ir a la máquina virtual con el fin de ver el sitio de IIS.

![Sitio predeterminado de IIS](./media/tutorial-virtual-network/iis.png)

<a id="manage-internal-traffic" class="xliff"></a>

## Administración del tráfico interno

Un grupo de seguridad de red (NSG) contiene una lista de reglas de seguridad que permiten o deniegan el tráfico de red a recursos conectados a una red virtual. Los NSG se pueden asociar a las subredes o a NIC individuales conectados a máquinas virtuales. La apertura o el cierre del acceso a las máquinas virtuales a través de puertos se realiza mediante las reglas de NSG. Al crear *myFrontendVM*, el puerto de entrada 3389 se abrió automáticamente para la conectividad RDP.

La comunicación interna de las máquinas virtuales puede configurarse con un NSG. En esta sección, aprenderá a crear una subred adicional en la red y asignar un NSG a ella para permitir una conexión de *myFrontendVM* a *myBackendVM* en el puerto 1433. Después, la subred se asigna a la máquina virtual cuando se crea.

Puede limitar el tráfico interno a *myBackendVM* solo desde *myFrontendVM* creando un NSG para la subred de "back-end". En el ejemplo siguiente se crea una regla NSG denominada *myBackendNSGRule* con [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig):

```powershell
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Agregue un nuevo grupo de seguridad de red denominado *myBackendNSG* con [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup):

```powershell
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```
<a id="add-back-end-subnet" class="xliff"></a>

## Adición de una subred de "back-end"

Adición de *myBackEndSubnet* a *myVNet* con [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig):

```powershell
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -VirtualNetwork $vnet `
  -AddressPrefix 10.0.1.0/24 `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
```

<a id="create-back-end-vm" class="xliff"></a>

## Creación de la máquina virtual de "back-end"

La manera más fácil de crear la máquina virtual de "back-end "es utilizando una imagen de SQL Server. Con este tutorial solo se crea la máquina virtual con el servidor de base de datos, pero no se proporciona información del acceso a la base de datos.

Creación de *myBackendNic*:

```powershell
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNic `
  -SubnetId $vnet.Subnets[1].Id
```

Establezca el nombre de usuario y la contraseña que se necesitan para la cuenta de administrador en la máquina virtual con Get-Credential:

```powershell
$cred = Get-Credential
```

Creación de *myBackendVM*:

```powershell
$backendVM = New-AzureRmVMConfig `
  -VMName myBackendVM `
  -VMSize Standard_D1
$backendVM = Set-AzureRmVMOperatingSystem `
  -VM $backendVM `
  -Windows `
  -ComputerName myBackendVM `
  -Credential $cred `
  -ProvisionVMAgent `
  -EnableAutoUpdate
$backendVM = Set-AzureRmVMSourceImage `
  -VM $backendVM `
  -PublisherName MicrosoftSQLServer `
  -Offer SQL2016-WS2016 `
  -Skus Enterprise `
  -Version latest
$backendVM = Set-AzureRmVMOSDisk `
  -VM $backendVM `
  -Name myBackendOSDisk `
  -DiskSizeInGB 128 `
  -CreateOption FromImage `
  -Caching ReadWrite
$backendVM = Add-AzureRmVMNetworkInterface `
  -VM $backendVM `
  -Id $backendNic.Id
New-AzureRmVM `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -VM $backendVM
```

La imagen que se utiliza tiene instalado SQL Server, pero no se usa en este tutorial. Se incluye para mostrar cómo se pueden configurar una máquina virtual para controlar el tráfico de web y otra para controlar la administración de bases de datos.

<a id="next-steps" class="xliff"></a>

## Pasos siguientes

En este tutorial, ha creado y protegido redes de Azure cuando están relacionadas con máquinas virtuales. 

> [!div class="checklist"]
> * Crear una red virtual
> * Crear subredes de redes virtuales
> * Controlar el tráfico de red con grupos de seguridad de red
> * Ver reglas de tráfico en acción

Prosiga con el siguiente tutorial para aprender a supervisar la protección de datos en máquinas virtuales mediante Azure Backup. .

> [!div class="nextstepaction"]
> [Copia de seguridad de máquinas virtuales Windows en Azure](./tutorial-backup-vms.md)

