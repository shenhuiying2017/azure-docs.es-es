## Cómo crear una red virtual mediante PowerShell

Para crear una red virtual mediante PowerShell, siga estos pasos.

1. Si es la primera vez que usa Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccionar su suscripción.
	
3. Si es necesario, ejecute el cmdlet **New-AzureRMResourceGroup** para crear un nuevo grupo de recursos, como se muestra a continuación. En este escenario, cree un grupo de recursos denominado *TestRG*. Para obtener más información sobre los grupos de recursos, visite [Información general del Administrador de recursos de Azure](resource-group-overview.md/#resource-groups).

		New-AzureRMResourceGroup -Name TestRG -Location centralus

	Resultado esperado:
	
		ResourceGroupName : TestRG
		Location          : centralus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		                    Actions  NotActions
		                    =======  ==========
		                    *
		
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG	

4. Ejecute el cmdlet **New-AzureRMVirtualNetwork** para crear una red virtual nueva, tal y como se muestra a continuación.

		New-AzureRMVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
			-AddressPrefix 192.168.0.0/16 -Location centralus	
		
	Resultado esperado:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : []

5. Ejecute el cmdlet **Get-AzureRMVirtualNetwork** para almacenar el objeto de red virtual en una variable, tal y como se muestra a continuación.

		$vnet = Get-AzureRMVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
	
	>[AZURE.TIP]Puede combinar los pasos 4 y 5 ejecutando **$vnet = New-AzureRMVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus**.

6. Ejecute el cmdlet **Add-AzureRMVirtualNetworkSubnetConfig** para agregar una subred a la nueva red virtual, como se muestra a continuación.

		Add-AzureRMVirtualNetworkSubnetConfig -Name FrontEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
		
	Resultado esperado:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": null,
		                        "Id": null,
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": null,
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": null
		                      }
		                    ]

7. Repita el paso 6 anterior para cada subred que desee crear. El siguiente comando crea la subred *BackEnd* para nuestro escenario.

		Add-AzureRMVirtualNetworkSubnetConfig -Name BackEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24

8. Aunque cree subredes, actualmente solo existen en la variable local que se usa para recuperar la red virtual creada en el paso 4 anterior. Para guardar los cambios en Azure, ejecute el cmdlet **Set-AzureRMVirtualNetwork**, como se muestra a continuación.

		Set-AzureRMVirtualNetwork -VirtualNetwork $vnet	
		
	Resultado esperado:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": []
		                    }
		NetworkInterfaces : null
		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      },
		                      {
		                        "Name": "BackEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
		                        "AddressPrefix": "192.168.2.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]

<!---HONumber=Oct15_HO3-->