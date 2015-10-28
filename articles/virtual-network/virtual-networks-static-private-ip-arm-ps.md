<properties 
   pageTitle="Establecimiento de una IP privada estática en el modo ARM con PowerShell | Microsoft Azure"
   description="Descripción de las IP estáticas (DIP) y su administración en el modo ARM con PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/08/2015"
   ms.author="telmos" />

# Configuración de una dirección IP privada estática en PowerShell

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puede [administrar la dirección IP privada estática en el modelo de implementación clásica](virtual-networks-static-private-ip-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

En los siguientes comandos de PowerShell de ejemplo se presupone que ya se ha creado un entorno simple según el escenario anterior. Si desea ejecutar los comandos que aparecen en este documento, cree primero el entorno de prueba descrito en [creación de una red virtual](virtual-networks-create-vnet-arm-ps.md).

## Especificación de una dirección IP privada estática al crear una VM
Para crear una VM denominada *DNS01* en la subred de *FrontEnd* de una Red virtual denominada *TestVNet* con una IP privada estática de *192.168.1.101*, sigue estos pasos:

1. Configure las variables para la cuenta de almacenamiento, la ubicación, el grupo de recursos y las credenciales que se van a utilizar. Tendrá que especificar un nombre de usuario y una contraseña para la VM. El grupo de recursos y la cuenta de almacenamiento deben existir.

		$stName = "vnetstorage"
		$locName = "Central US"
		$rgName = "TestRG"
	    $cred = Get-Credential -Message "Type the name and password of the local administrator account."

3. Recupere la red virtual y la subred que desee crear en la VM.

	    $vnet = Get-AzureRMVirtualNetwork -ResourceGroupName TestRG -Name TestVNet	
	    $subnet = $vnet.Subnets[0].Id

4. Si fuese necesario, cree una dirección IP pública para obtener acceso a la VM desde Internet.

		$pip = New-AzureRMPublicIpAddress -Name TestPIP -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

5. Cree una NIC con la dirección IP privada estática que desee asignar a la VM. Asegúrese de que la IP pertenece al intervalo de subred que está añadiendo a la VM. Este es el paso principal para este artículo, en el que configura la IP privada para que sea estática.

		$nic = New-AzureRMNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.1.101

6. Cree la VM con la NIC creada anteriormente.

		$vm = New-AzureRMVMConfig -VMName DNS01 -VMSize "Standard_A1"
		$vm = Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DNS01  -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		$vm = Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
		$vm = Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
		$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
		$vm = Set-AzureRMVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
		New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm 

	Resultado esperado:

		EndTime             : 9/8/2015 2:32:09 PM -07:00
		Error               : 
		Output              : 
		StartTime           : 9/8/2015 2:27:42 PM -07:00
		Status              : Succeeded
		TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		StatusCode          : OK 


## Recuperación de la información de la dirección IP privada estática para una VM
Para ver la información de la dirección IP privada estática para la máquina virtual que se creó con el script anterior, ejecute el siguiente comando de PowerShell y observe los valores para *PrivateIpAddress* y *PrivateIpAllocationMethod*:

	Get-AzureRMNetworkInterface -Name TestNIC -ResourceGroupName TestRG

Resultado esperado:

	Name                 : TestNIC
	ResourceGroupName    : TestRG
	Location             : centralus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/Te
	                       stNIC
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState    : Succeeded
	Tags                 : 
	VirtualMachine       : {
	                         "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMach
	                       ines/DNS01"
	                       }
	IpConfigurations     : [
	                         {
	                           "Name": "ipconfig1",
	                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkIn
	                       terfaces/TestNIC/ipConfigurations/ipconfig1",
	                           "PrivateIpAddress": "192.168.1.101",
	                           "PrivateIpAllocationMethod": "Static",
	                           "Subnet": {
	                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtual
	                       Networks/TestVNet/subnets/FrontEnd"
	                           },
	                           "PublicIpAddress": {
	                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicI
	                       PAddresses/TestPIP"
	                           },
	                           "LoadBalancerBackendAddressPools": [],
	                           "LoadBalancerInboundNatRules": [],
	                           "ProvisioningState": "Succeeded"
	                         }
	                       ]
	DnsSettings          : {
	                         "DnsServers": [],
	                         "AppliedDnsServers": [],
	                         "InternalDnsNameLabel": null,
	                         "InternalFqdn": null
	                       }
	EnableIPForwarding   : False
	NetworkSecurityGroup : null
	Primary              : True

## Eliminación de una dirección IP privada estática de una VM
Para quitar la dirección IP privada estática agregada a la máquina virtual en el script anterior, ejecute los siguientes comandos de PowerShell:
	
	$nic=Get-AzureRMNetworkInterface -Name TestNIC -ResourceGroupName TestRG
	$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Dynamic"
	Set-AzureRMNetworkInterface -NetworkInterface $nic

Resultado esperado:

	Name                 : TestNIC
	ResourceGroupName    : TestRG
	Location             : centralus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/Te
	                       stNIC
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState    : Succeeded
	Tags                 : 
	VirtualMachine       : {
	                         "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMach
	                       ines/WindowsVM"
	                       }
	IpConfigurations     : [
	                         {
	                           "Name": "ipconfig1",
	                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkIn
	                       terfaces/TestNIC/ipConfigurations/ipconfig1",
	                           "PrivateIpAddress": "192.168.1.101",
	                           "PrivateIpAllocationMethod": "Dynamic",
	                           "Subnet": {
	                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtual
	                       Networks/TestVNet/subnets/FrontEnd"
	                           },
	                           "PublicIpAddress": {
	                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicI
	                       PAddresses/TestPIP"
	                           },
	                           "LoadBalancerBackendAddressPools": [],
	                           "LoadBalancerInboundNatRules": [],
	                           "ProvisioningState": "Succeeded"
	                         }
	                       ]
	DnsSettings          : {
	                         "DnsServers": [],
	                         "AppliedDnsServers": [],
	                         "InternalDnsNameLabel": null,
	                         "InternalFqdn": null
	                       }
	EnableIPForwarding   : False
	NetworkSecurityGroup : null
	Primary              : True

## Adición de una dirección IP privada estática a una VM existente
Para agregar una dirección IP privada estática a la VM creada con el script anterior, ejecute el siguiente comando:

	$nic=Get-AzureRMNetworkInterface -Name TestNIC -ResourceGroupName TestRG
	$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Static"
	$nic.IpConfigurations[0].PrivateIpAddress = "192.168.1.101"
	Set-AzureRMNetworkInterface -NetworkInterface $nic

## Pasos siguientes

- Encuentre más información sobre las direcciones [IP públicas reservadas](../virtual-networks-reserved-public-ip).
- Obtenga más información acerca de las [direcciones IP públicas de nivel de instancia (ILPIP)](../virtual-networks-instance-level-public-ip).
- Consulte las [API de REST de IP reservadas](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=Oct15_HO3-->