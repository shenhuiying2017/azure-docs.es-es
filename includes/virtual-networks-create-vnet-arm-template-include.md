## Cómo crear una red virtual mediante PowerShell

Para crear una red virtual mediante PowerShell, siga estos pasos.

1. Si es la primera vez que usa Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccionar su suscripción.
2. En un símbolo del sistema de Azure PowerShell, ejecute el cmdlet **Switch-AzureMode** para cambiar al modo de Administrador de recursos, como se muestra a continuación.

	Switch-AzureMode AzureResourceManager

	ADVERTENCIA: El cmdlet Switch-AzureMode está en desuso y se quitará en futuras versiones.

	>[AZURE.WARNING]El cmdlet Switch-AzureMode pronto estará en desuso. Cuando esto suceda, se cambiará el nombre de todos los cmdlets del Administrador de recursos.
	
3. Si es necesario, ejecute el cmdlet **New-AzureResourceGroup** para crear un nuevo grupo de recursos, tal y como se muestra a continuación. En nuestro escenario, cree un grupo de recursos llamado *RG1*.

	New-AzureResourceGroup -Name RG1 -Location centralus

	ResourceGroupName : RG1 Location : centralus ProvisioningState : Succeeded Tags : Permissions : Actions NotActions ======= ========== *
	
	ResourceId : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1

4. Ejecute el cmdlet **New-AzureVirtualNetwork** para crear una red virtual nueva, tal y como se muestra a continuación.

	New-AzureVirtualNetwork -ResourceGroupName RG1 -Name TestVNet ` -AddressPrefix 192.168.0.0/16 -Location centralus
	
	Name : TestVNet ResourceGroupName : RG1 Location : centralus Id : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/TestVNet Etag : W/"5b89894f-db7f-4634-9870-f9b97e209510" ProvisioningState : Succeeded Tags : AddressSpace : { "AddressPrefixes": [ "192.168.0.0/16" ] } DhcpOptions : { "DnsServers": null } NetworkInterfaces : null Subnets :

5. Ejecute el cmdlet **Get-AzureVirtualNetwork** para almacenar el objeto de red virtual en una variable, como se muestra a continuación.

	$vnet = Get-AzureVirtualNetwork -ResourceGroupName RG1 -Name TestVNet

	>[AZURE.TIP]Puede combinar los pasos 4 y 5 ejecutando **$vnet = New-AzureVirtualNetwork -ResourceGroupName RG1 -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus**.

6. Ejecute el cmdlet **Add-AzureVirtualNetworkSubnetConfig** para agregar una subred a la nueva red virtual, como se muestra a continuación.

	Add-AzureVirtualNetworkSubnetConfig -Name FrontEnd ` -VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
	
	Name : TestVNet ResourceGroupName : RG1 Location : centralus Id : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/TestVNet Etag : W/"5b89894f-db7f-4634-9870-f9b97e209510" ProvisioningState : Succeeded Tags : AddressSpace : { "AddressPrefixes": [ "192.168.0.0/16" ] } DhcpOptions : { "DnsServers": null } NetworkInterfaces : null Subnets : [ { "Name": "FrontEnd", "Etag": null, "Id": null, "AddressPrefix": "192.168.1.0/24", "IpConfigurations": null, "NetworkSecurityGroup": null, "RouteTable": null, "ProvisioningState": null } ]

7. Repita el paso 6 anterior para cada subred que desee crear. El siguiente comando crea la subred *BackEnd* para nuestro escenario.

	Add-AzureVirtualNetworkSubnetConfig -Name BackEnd ` -VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24

8. Aunque cree subredes, actualmente solo existen en la variable local que se usa para recuperar la red virtual creada en el paso 4 anterior. Para guardar los cambios en Azure, ejecute el cmdlet **Set-AzureVirtualNetwork**, como se muestra a continuación.

	Set-AzureVirtualNetwork -VirtualNetwork $vnet
	
	Name : TestVNet ResourceGroupName : RG1 Location : centralus Id : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworks/TestVNet Etag : W/"2d3496d8-2b85-4238-bde2-377fe660aa4a" ProvisioningState : Succeeded Tags : AddressSpace : { "AddressPrefixes": [ "192.168.0.0/16" ] } DhcpOptions : { "DnsServers": } NetworkInterfaces : null Subnets : [ { "Name": "FrontEnd", "Etag": "W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"", "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Micro soft.Network/virtualNetworks/TestVNet/subnets/FrontEnd", "AddressPrefix": "192.168.1.0/24", "IpConfigurations": , "NetworkSecurityGroup": null, "RouteTable": null, "ProvisioningState": "Succeeded" }, { "Name": "BackEnd", "Etag": "W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"", "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Micro soft.Network/virtualNetworks/TestVNet/subnets/BackEnd", "AddressPrefix": "192.168.2.0/24", "IpConfigurations": , "NetworkSecurityGroup": null, "RouteTable": null, "ProvisioningState": "Succeeded" } ]

<!---HONumber=August15_HO8-->