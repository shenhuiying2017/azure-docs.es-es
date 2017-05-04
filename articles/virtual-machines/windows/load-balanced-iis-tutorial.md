---
title: "Tutorial: Compilación de una aplicación de alta disponibilidad en máquinas virtuales de Azure | Microsoft Docs"
description: "Aprenda cómo crear una aplicación con alta disponibilidad y segura en tres máquinas virtuales Windows con un equilibrador de carga en Azure."
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
ms.date: 03/30/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 61fd0dc45cfa2d320713819be3db811be58ab77e
ms.lasthandoff: 04/27/2017

---

# <a name="build-a-load-balanced-highly-available-application-on-windows-virtual-machines-in-azure"></a>Compilación de una aplicación de alta disponibilidad y carga equilibrada en máquinas virtuales Windows en Azure

En este tutorial, creará una aplicación de alta disponibilidad que es resistente a los eventos de mantenimiento. La aplicación utiliza un equilibrador de carga, un conjunto de disponibilidad y tres máquinas virtuales Windows. Este tutorial instala IIS, aunque se puede utilizar para implementar otra plataforma de aplicación usando los mismos componentes de alta disponibilidad y las mismas directrices. 

## <a name="step-1---azure-prerequisites"></a>Paso 1: Requisitos previos de Azure

Para completar este tutorial, asegúrese de haber instalado el módulo más reciente de [Azure PowerShell](/powershell/azure/overview).

Primero, inicie sesión en la suscripción de Azure con el comando Login-AzureRmAccount y siga las instrucciones que aparecen en pantalla.

```powershell
Login-AzureRmAccount
```

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. Para poder crear otros recursos de Azure, tiene que crear un grupo de recursos con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En el ejemplo siguiente, se crea un grupo de recursos denominado `myResourceGroup` en la región `westeurope`. 

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location westeurope
```

## <a name="step-2---create-availability-set"></a>Paso 2: Creación de un conjunto de disponibilidad

Las máquinas virtuales se pueden crear en dominios de error lógico y actualización. Cada dominio lógico representa una parte del hardware del centro de datos de Azure subyacente. Cuando crea dos o más máquinas virtuales, los recursos de proceso y almacenamiento se distribuyen en estos dominios. Esta distribución mantiene la disponibilidad de la aplicación si un componente de hardware necesita mantenimiento. Los conjuntos de disponibilidad le permiten definir estos dominios de error lógico y actualización.

Cree un conjunto de disponibilidad con [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). En el ejemplo siguiente se crea un conjunto de disponibilidad denominado `myAvailabilitySet`:

```powershell
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName myResourceGroup `
  -Name myAvailabilitySet `
  -Location westeurope `
  -Managed `
  -PlatformFaultDomainCount 3 `
  -PlatformUpdateDomainCount 2
```

## <a name="step-3---create-load-balancer"></a>Paso 3: Creación de un equilibrador de carga

Un equilibrador de carga de Azure distribuye el tráfico a través de un conjunto de máquinas virtuales definidas usando reglas de equilibrador de carga. Un sondeo de estado supervisa un puerto determinado en cada máquina virtual y solo distribuye tráfico a una máquina virtual operativa.

### <a name="create-public-ip-address"></a>Creación de una dirección IP pública

Para obtener acceso a la aplicación en Internet, asigne una dirección IP pública para el equilibrador de carga. Cree una dirección IP pública con [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). El ejemplo siguiente crea una dirección IP pública denominada `myPublicIP`:

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -AllocationMethod Static `
  -Name myPublicIP
```

### <a name="create-load-balancer"></a>Creación de un equilibrador de carga

Cree una dirección IP de front-end con [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig). En el ejemplo siguiente se crea una dirección IP de front-end denominada `myFrontEndPool`: 

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name myFrontEndPool -PublicIpAddress $pip
```

Cree un grupo de direcciones de back-end con [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). En el siguiente ejemplo se crea un grupo de direcciones de back-end denominado `myBackEndPool`:

```powershell
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool
```

Ahora, cree el equilibrador de carga con [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer). En el ejemplo siguiente se crea un equilibrador de carga denominado `myLoadBalancer` utilizando la dirección `myPublicIP`:

```powershell
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroup `
  -Name myLoadBalancer `
  -Location westeurope `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-health-probe"></a>Creación de un sondeo de estado

Para permitir que el equilibrador de carga supervise el estado de la aplicación, utilice un sondeo de estado. El sondeo de estado agrega o quita de forma dinámica las máquinas virtuales de la rotación del equilibrador de carga en base a su respuesta a las comprobaciones de estado. De forma predeterminada, una máquina virtual se quita de la distribución del equilibrador de carga después de dos errores consecutivos en un intervalo de 15 segundos.

Cree un sondeo de estado con [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig). En el ejemplo siguiente se crea un sondeo de estado denominado `myHealthProbe` que supervisa cada máquina virtual:

```powershell
Add-AzureRmLoadBalancerProbeConfig -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

### <a name="create-load-balancer-rule"></a>Creación de reglas del equilibrador de carga

Las reglas de equilibrador de carga se utilizan para definir cómo se distribuye el tráfico a las máquinas virtuales.

Cree una regla del equilibrador de carga con [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig). En el ejemplo siguiente se crea una regla del equilibrador de carga denominada `myLoadBalancerRule` y se equilibra el tráfico en el puerto `80`:

```powershell
Add-AzureRmLoadBalancerRuleConfig -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

Actualice el equilibrador de carga con [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="step-4---configure-networking"></a>Paso 4: Configuración de las redes

Cada máquina virtual tiene una o más tarjetas de interfaz de red virtual que se conectan a una red virtual. Esta red virtual está protegida para filtrar el tráfico basándose en reglas de acceso definidas.

### <a name="create-virtual-network"></a>Creación de una red virtual

Primero, cree una subred con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). En el ejemplo siguiente se crea una subred denominada `mySubnet`:

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
```

Para proporcionar conectividad de red a las máquinas virtuales, cree una red virtual con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). En el ejemplo siguiente se crea una red virtual denominada `myVnet` con `mySubnet`:

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

### <a name="configure-network-security"></a>Configuración de la seguridad de red

Un [grupo de seguridad de red](../../virtual-network/virtual-networks-nsg.md) de Azure controla el tráfico entrante y saliente para una o varias máquinas virtuales. Las reglas del grupo de seguridad de red permiten o deniegan el tráfico de red en un puerto específico o en un intervalo de puertos. Estas reglas también pueden incluir un prefijo de dirección de origen para que solo el tráfico que se origine en un origen predefinido pueda comunicarse con una máquina virtual.

Para permitir que el tráfico de red llegue a la aplicación, cree una regla del grupo de seguridad de red con [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). En el ejemplo siguiente, se crea un grupo de seguridad de red denominado `myNetworkSecurityGroupRule`:

```powershell
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

Cree un grupo de seguridad de red con [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). En el ejemplo siguiente se crea un NSG denominado `myNetworkSecurityGroup`:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule
```

Agregue el grupo de seguridad de red a la subred con [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig):

```powershell
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
  -Name mySubnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24
```

Actualice la red virtual con [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork):

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-network-interface-cards"></a>Creación de las tarjetas de interfaz de red virtual

Los equilibradores de carga funcionan con el recurso de NIC virtual en lugar de con la máquina virtual real. La NIC virtual se conecta al equilibrador de carga y, a continuación, se adjunta a una máquina virtual.

Cree una NIC virtual con [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). En el ejemplo siguiente se crean tres NIC. (Una NIC virtual para cada máquina virtual que cree para la aplicación en los pasos siguientes):


```powershell
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface -ResourceGroupName myResourceGroup `
     -Name myNic$i `
     -Location westeurope `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}

```

## <a name="step-5---create-virtual-machines"></a>Paso 5: Creación de máquinas virtuales

Con todos los componentes subyacentes en su lugar, ahora puede crear máquinas virtuales de alta disponibilidad para ejecutar la aplicación. 

Establezca el nombre de usuario y la contraseña que se necesitan para la cuenta de administrador en la máquina virtual con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Cree las máquinas virtuales con [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvmconfig), [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmoperatingsystem), [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmsourceimage), [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmosdisk), [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmnetworkinterface) y [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). En el ejemplo siguiente se crean tres máquinas virtuales:

```powershell
for ($i=1; $i -le 3; $i++)
{
   $vm = New-AzureRmVMConfig -VMName myVM$i -VMSize Standard_D1 -AvailabilitySetId $availabilitySet.Id
   $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName myVM$i -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
   $vm = Set-AzureRmVMOSDisk -VM $vm -Name myOsDisk$i -StorageAccountType StandardLRS -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
   $nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroup -Name myNic$i
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vm
}

```

Se tardan unos minutos en crear y configurar las tres máquinas virtuales. El sondeo de estado del equilibrador de carga detecta automáticamente cuándo la aplicación se está ejecutando en cada máquina virtual. Una vez que la aplicación se esté ejecutando, la regla del equilibrador de carga empieza a distribuir el tráfico.

### <a name="install-the-app"></a>Instalación de la aplicación 

Las extensiones de máquina virtual de Azure se usan para automatizar las tareas de configuración de máquina virtual, como la instalación de aplicaciones y la configuración del sistema operativo. La [extensión de script personalizado para Windows](./../virtual-machines-windows-extensions-customscript.md) se usa para ejecutar cualquier script de PowerShell en la máquina virtual. El script puede almacenarse en Azure Storage, cualquier punto de conexión HTTP accesible o incrustarse en la configuración de la extensión del script personalizado. Al usar el script personalizado, el agente de la máquina virtual de Azure administra la ejecución del script.

Use [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) para instalar la extensión del script personalizado. Se ejecuta la extensión `powershell Add-WindowsFeature Web-Server` para instalar el servidor web IIS:

```powershell
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
     -ExtensionName IIS `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
     -Location westeurope
}
```

### <a name="test-your-app"></a>Prueba de la aplicación

Obtenga la dirección IP pública del equilibrador de carga con [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). En el ejemplo siguiente se obtiene la dirección IP de `myPublicIP` que se ha creado anteriormente:

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroup -Name myPublicIP | select IpAddress
```

Escriba la dirección IP pública en un explorador web. Con la regla del NSG activada, se muestra el sitio web predeterminado de IIS. 

![Sitio predeterminado de IIS](media/load-balanced-iis-tutorial/iis.png)

## <a name="step-6--management-tasks"></a>Paso 6: Tareas de administración

Puede que tenga que realizar labores de mantenimiento de las máquinas virtuales que ejecutan la aplicación, como la instalación de actualizaciones del sistema operativo. Para gestionar un aumento de tráfico a la aplicación, tiene que agregar más máquinas virtuales. Esta sección le muestra cómo quitar o agregar una máquina virtual desde el equilibrador de carga. 

### <a name="remove-a-vm-from-the-load-balancer"></a>Eliminación de una máquina virtual del equilibrador de carga

Para quitar una máquina virtual del grupo de direcciones de back-end restablezca la propiedad LoadBalancerBackendAddressPools de la tarjeta de interfaz de red.

Obtenga una tarjeta de interfaz de red con [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface):

```powershell
$nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroup -Name myNic2
``` 

Establezca la propiedad LoadBalancerBackendAddressPools de la tarjeta de interfaz de red en $null:

```powershell
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
```

Actualización de la tarjeta de interfaz de red:

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

### <a name="add-a-vm-to-the-load-balancer"></a>Incorporación de una máquina virtual al equilibrador de carga

Después de realizar el mantenimiento de una máquina virtual, o si necesita expandir la capacidad, agregue la NIC de una máquina virtual al grupo de direcciones de back-end.

Obtención del equilibrador de carga:

```powershell
$lb = Get-AzureRMLoadBalancer -ResourceGroupName myResourceGroup -Name myLoadBalancer 
```

Agregue el grupo de direcciones de back-end del equilibrador de carga a la tarjeta de interfaz de red:

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
```

Actualización de la tarjeta de interfaz de red:

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Pasos siguientes

Ejemplos – [Scripts de ejemplo de Azure Virtual Machine PowerShell](./../virtual-machines-windows-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

