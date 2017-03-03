---
title: "Creación de conjuntos de escalado de máquinas virtuales con los cmdlets de PowerShell | Microsoft Docs"
description: "Empezar a crear y administrar los primeros conjuntos de escalado de máquina virtual de Azure con cmdlets de Azure PowerShell"
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 430d9d64-1f35-48f0-a4fd-9b69910ffa59
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: danielsollondon
translationtype: Human Translation
ms.sourcegitcommit: b2e89f0d5e6b14ce8d5847f8adc3d57a6122172e
ms.openlocfilehash: a3a36028a75d6cb7eb36277f3e2b5ab833c96a96
ms.lasthandoff: 02/21/2017


---
# <a name="creating-virtual-machine-scale-sets-using-powershell-cmdlets"></a>Creación de conjuntos de escalado de máquina virtual con cmdlets de PowerShell
Este artículo le guía a través de un ejemplo de cómo crear un conjunto de escalado de máquinas virtuales (VMSS). Crea un conjunto de escalado de tres nodos, con redes y almacenamiento asociados.

## <a name="first-steps"></a>Primeros pasos
Asegúrese de que tiene instalado el módulo de Azure PowerShell más reciente con los commandlets de PowerShell necesarios para mantener y crear conjuntos de escalado.
Vaya [aquí](http://aka.ms/webpi-azps) para obtener las herramientas de línea de comandos más recientes para los módulos de Azure.

Para buscar commandlets relacionados con los VMSS, use la cadena de búsqueda \*VMSS\*. Por ejemplo, _gcm *vmss*_.

## <a name="creating-a-vmss"></a>Creación de un VMSS
#### <a name="create-resource-group"></a>Crear grupo de recursos
```
$loc = 'westus';
$rgname = 'mynewrgwu';
  New-AzureRmResourceGroup -Name $rgname -Location $loc -Force;
```

### <a name="create-networking-vnet--subnet"></a>Crear redes (VNET o subred)
#### <a name="subnet-specification"></a>Especificación de subred
```
$subnetName = 'websubnet'
  $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix "10.0.0.0/24";
```

#### <a name="vnet-specification"></a>Especificación de VNET
```
$vnet = New-AzureRmVirtualNetwork -Force -Name ('vnet' + $rgname) -ResourceGroupName $rgname -Location $loc -AddressPrefix "10.0.0.0/16" -Subnet $subnet;
$vnet = Get-AzureRmVirtualNetwork -Name ('vnet' + $rgname) -ResourceGroupName $rgname;

# In this case assume the new subnet is the only one
$subnetId = $vnet.Subnets[0].Id;
```

#### <a name="create-public-ip-resource-to-allow-external-access"></a>Crear un recurso de IP pública para permitir el acceso externo
Se enlazará con el equilibrador de carga.

```
$pubip = New-AzureRmPublicIpAddress -Force -Name ('pubip' + $rgname) -ResourceGroupName $rgname -Location $loc -AllocationMethod Dynamic -DomainNameLabel ('pubip' + $rgname);
$pubip = Get-AzureRmPublicIpAddress -Name ('pubip' + $rgname) -ResourceGroupName $rgname;
```

#### <a name="create-load-balancer"></a>Cree un equilibrador de carga
```
$frontendName = 'fe' + $rgname
$backendAddressPoolName = 'bepool' + $rgname
$probeName = 'vmssprobe' + $rgname
$inboundNatPoolName = 'innatpool' + $rgname
$lbruleName = 'lbrule' + $rgname
$lbName = 'vmsslb' + $rgname

# Bind Public IP to Load Balancer
$frontend = New-AzureRmLoadBalancerFrontendIpConfig -Name $frontendName -PublicIpAddress $pubip
```

#### <a name="configure-load-balancer"></a>Configurar el equilibrador de carga
Cree una configuración de grupo de direcciones de back-end compartida entre las NIC de las máquinas virtuales del conjunto de escalado.

```
$backendAddressPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name $backendAddressPoolName
```

Establezca el puerto de sondeo de carga equilibrada y cambie la configuración para adecuarla a su aplicación.

```
$probe = New-AzureRmLoadBalancerProbeConfig -Name $probeName -RequestPath healthcheck.aspx -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
```

Cree un grupo NAT de entrada para una conectividad enrutada directa (sin carga equilibrada) con las máquinas virtuales del conjunto de escalado a través del equilibrador de carga. Esto se hace para demostrar el uso de RDP y es posible que no sea necesario en su aplicación.

```
$frontendpoolrangestart = 3360
$frontendpoolrangeend = 3370
$backendvmport = 3389
$inboundNatPool = New-AzureRmLoadBalancerInboundNatPoolConfig -Name $inboundNatPoolName -FrontendIPConfigurationId `
$frontend.Id -Protocol Tcp -FrontendPortRangeStart $frontendpoolrangestart -FrontendPortRangeEnd $frontendpoolrangeend -BackendPort $backendvmport;
```

Cree la regla de carga equilibrada, en este ejemplo se muestran las solicitudes de equilibrio de carga del puerto 80, con la configuración de los pasos anteriores.

```
$protocol = 'Tcp'
$feLBPort = 80
$beLBPort = 80

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name $lbruleName `
-FrontendIPConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -Protocol $protocol -FrontendPort $feLBPort -BackendPort $beLBPort `
-IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -Verbose;
```

Cree el equilibrador de carga con la configuración.

```
$actualLb = New-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname -Location $loc `
-FrontendIpConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -LoadBalancingRule $lbrule -InboundNatPool $inboundNatPool -Verbose;
```

Compruebe la configuración de Load Balancer y la configuración del puerto de carga equilibrada. Tenga en cuenta que no verá las reglas NAT entrantes hasta que se cree la máquina virtual en el conjunto de escalado.

```
$expectedLb = Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname
```

##### <a name="configure-and-create-the-scale-set"></a>Configuración y creación del conjunto de escalado
Tenga en cuenta que este ejemplo de infraestructura muestra cómo configurar, distribuir y escalar el tráfico web a través del conjunto de escalado, pero que las imágenes de las máquinas virtuales especificadas aquí no tienen instalado ningún servicio web.

```
# specify scale set Name
$vmssName = 'vmss' + $rgname;

## specify VMSS specific details
$adminUsername = 'azadmin';
$adminPassword = "Password1234!";

$PublisherName = 'MicrosoftWindowsServer'
$Offer         = 'WindowsServer'
$Sku          = '2012-R2-Datacenter'
$Version       = 'latest'
$vmNamePrefix = 'winvmss'

###add an extension
$extname = 'BGInfo';
$publisher = 'Microsoft.Compute';
$exttype = 'BGInfo';
$extver = '2.1';
```

Enlazar la NIC al equilibrador de carga y la subred

```
$ipCfg = New-AzureRmVmssIPConfig -Name 'nic' `
-LoadBalancerInboundNatPoolsId $actualLb.InboundNatPools[0].Id `
-LoadBalancerBackendAddressPoolsId $actualLb.BackendAddressPools[0].Id `
-SubnetId $subnetId;
```

Crear la configuración del conjunto de escalado

```
# Specify number of nodes
$numberofnodes = 3

$vmss = New-AzureRmVmssConfig -Location $loc -SkuCapacity $numberofnodes -SkuName 'Standard_A2' -UpgradePolicyMode 'automatic' `
    | Add-AzureRmVmssNetworkInterfaceConfiguration -Name $subnetName -Primary $true -IPConfiguration $ipCfg `
    | Set-AzureRmVmssOSProfile -ComputerNamePrefix $vmNamePrefix -AdminUsername $adminUsername -AdminPassword $adminPassword `
    | Set-AzureRmVmssStorageProfile -OsDiskCreateOption 'FromImage' -OsDiskCaching 'None' `
    -ImageReferenceOffer $Offer -ImageReferenceSku $Sku -ImageReferenceVersion $Version `
    -ImageReferencePublisher $PublisherName `
    | Add-AzureRmVmssExtension -Name $extname -Publisher $publisher -Type $exttype -TypeHandlerVersion $extver -AutoUpgradeMinorVersion $true
```

Compilar la configuración del conjunto de escalado

```
New-AzureRmVmss -ResourceGroupName $rgname -Name $vmssName -VirtualMachineScaleSet $vmss -Verbose;
```

Ya se ha creado el conjunto de escalado. Puede probar la conexión a la máquina virtual individual mediante RDP en este ejemplo:

```
VM0 : pubipmynewrgwu.westus.cloudapp.azure.com:3360
VM1 : pubipmynewrgwu.westus.cloudapp.azure.com:3361
VM2 : pubipmynewrgwu.westus.cloudapp.azure.com:3362
```

