<properties
	pageTitle="Creación de conjuntos de escalado de máquina virtual con cmdlets de PowerShell | Microsoft Azure"
	description="Empezar a crear y administrar los primeros conjuntos de escalado de máquina virtual de Azure con cmdlets de Azure PowerShell"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="danielsollondon"
	manager="guybo"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="danielsollondon"/>

# Creación de conjuntos de escalado de máquina virtual con cmdlets de PowerShell

En el siguiente ejemplo de creación de un conjunto de escalado de máquina virtual (VMSS) se crea un VMSS de tres nodos con todas las redes y almacenamiento asociados.

## Primeros pasos
Asegúrese de que tiene instalado el módulo Azure PowerShell más reciente con los commandlets de PowerShell necesarios para mantener y crear VMSS. Vaya [aquí](http://aka.ms/webpi-azps) para obtener las herramientas de línea de comandos más recientes para los módulos de Azure.

Para buscar commandlets relacionados con los VMSS, use la cadena de búsqueda *VMSS*.

## Creación de un VMSS

##### Crear grupo de recursos

```
$loc = 'westus';
$rgname = 'mynewrgwu';
  New-AzureRmResourceGroup -Name $rgname -Location $loc -Force;
```

##### Crear una cuenta de almacenamiento

Establezca el tipo o nombre de la cuenta de almacenamiento.

```
$stoname = 'sto' + $rgname;
$stotype = 'Standard_LRS';
 New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $stoname -Location $loc -SkuName $stotype -Kind "Storage";

$stoaccount = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $stoname;
```

#### Crear redes (VNET o subred)

##### Especificación de subred

```
$subnetName = 'websubnet'
  $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix "10.0.0.0/24";
```

##### Especificación de VNET

```
$vnet = New-AzureRmVirtualNetwork -Force -Name ('vnet' + $rgname) -ResourceGroupName $rgname -Location $loc -AddressPrefix "10.0.0.0/16" -DnsServer "10.1.1.1" -Subnet $subnet;
$vnet = Get-AzureRmVirtualNetwork -Name ('vnet' + $rgname) -ResourceGroupName $rgname;

#In this case below we assume the new subnet is the only one, note difference if you have one already or have adjusted this code to more than one subnet.
$subnetId = $vnet.Subnets[0].Id;
```

##### Crear un recurso de IP pública para permitir el acceso externo

Se enlazará con el equilibrador de carga.

```
$pubip = New-AzureRmPublicIpAddress -Force -Name ('pubip' + $rgname) -ResourceGroupName $rgname -Location $loc -AllocationMethod Dynamic -DomainNameLabel ('pubip' + $rgname);
$pubip = Get-AzureRmPublicIpAddress -Name ('pubip' + $rgname) -ResourceGroupName $rgname;
```

##### Crear y configurar un equilibrador de carga

```
$frontendName = 'fe' + $rgname
$backendAddressPoolName = 'bepool' + $rgname
$probeName = 'vmssprobe' + $rgname
$inboundNatPoolName = 'innatpool' + $rgname
$lbruleName = 'lbrule' + $rgname
$lbName = 'vmsslb' + $rgname

#Bind Public IP to Load Balancer
$frontend = New-AzureRmLoadBalancerFrontendIpConfig -Name $frontendName -PublicIpAddress $pubip
```

##### Configurar el equilibrador de carga
Cree una configuración de grupo de direcciones de back-end compartida entre las NIC de las máquinas virtuales del VMSS.

```
$backendAddressPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name $backendAddressPoolName
```

Establezca el puerto de sondeo de carga equilibrada y cambie la configuración para adecuarla a su aplicación.

```
$probe = New-AzureRmLoadBalancerProbeConfig -Name $probeName -RequestPath healthcheck.aspx -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
```

Cree reglas NAT para una conectividad enrutada directa (sin carga equilibrada) con las máquinas virtuales del VMSS a través del equilibrador de carga. Tenga en cuenta que esto se comprueba mediante RDP y que este solo es un ejemplo, así que para la conectividad RDP con estos servidores deberían usarse métodos de VNET interna.

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

Compruebe la configuración del equilibrador de carga y la configuración del puerto de carga equilibrada. Tenga en cuenta que no verá las reglas NAT entrantes hasta que se cree la máquina virtual en el VMSS.

```
$expectedLb = Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname
```

##### Configurar y crear VMSS

Tenga en cuenta que este ejemplo de infraestructura muestra cómo configurar, distribuir y escalar el tráfico web a través del VMSS, pero que las imágenes de las máquinas virtuales especificadas aquí no tienen instalado ningún servicio web.

```
#specify VMSS Name
$vmssName = 'vmss' + $rgname;

##specify VMSS specific details
$adminUsername = 'azadmin';
$adminPassword = "Password1234!";

$PublisherName = 'MicrosoftWindowsServer'
$Offer         = 'WindowsServer'
$Sku          = '2012-R2-Datacenter'
$Version       = 'latest'
$vmNamePrefix = 'winvmss'

$vhdContainer = "https://" + $stoname + ".blob.core.windows.net/" + $vmssName;

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

$ipCfg.LoadBalancerBackendAddressPools.Add($actualLb.BackendAddressPools[0].Id);
$ipCfg.LoadBalancerInboundNatPools.Add($actualLb.InboundNatPools[0].Id);
```

Crear la configuración del VMSS

```
#Specify number of nodes
$numberofnodes = 3

$vmss = New-AzureRmVmssConfig -Location $loc -SkuCapacity $numberofnodes -SkuName 'Standard_A2' -UpgradePolicyMode 'automatic' `
    | Add-AzureRmVmssNetworkInterfaceConfiguration -Name $subnetName -Primary $true -IPConfiguration $ipCfg `
    | Set-AzureRmVmssOSProfile -ComputerNamePrefix $vmNamePrefix -AdminUsername $adminUsername -AdminPassword $adminPassword `
    | Set-AzureRmVmssStorageProfile -Name 'test' -OsDiskCreateOption 'FromImage' -OsDiskCaching 'None' `
    -ImageReferenceOffer $Offer -ImageReferenceSku $Sku -ImageReferenceVersion $Version `
    -ImageReferencePublisher $PublisherName -VhdContainer $vhdContainer `
    | Add-AzureRmVmssExtension -Name $extname -Publisher $publisher -Type $exttype -TypeHandlerVersion $extver -AutoUpgradeMinorVersion $true
```

Compilar la configuración del VMSS

```
New-AzureRmVmss -ResourceGroupName $rgname -Name $vmssName -VirtualMachineScaleSet $vmss -Verbose;
```

Ya se ha creado el VMSS. Puede probar la conexión a la máquina virtual individual mediante RDP en este ejemplo:

```
VM0 : pubipmynewrgwu.westus.cloudapp.azure.com:3360
VM1 : pubipmynewrgwu.westus.cloudapp.azure.com:3361
VM2 : pubipmynewrgwu.westus.cloudapp.azure.com:3362
```

<!---HONumber=AcomDC_0622_2016-->