## Cómo crear una red virtual mediante PowerShell
Para crear una red virtual mediante PowerShell, siga estos pasos.

1. Si es la primera vez que usa Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](../articles/powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccionar su suscripción.
	
2. Si es necesario, cree un nuevo grupo de recursos, como se muestra a continuación. En este escenario, cree un grupo de recursos denominado *TestRG*. Para obtener más información sobre los grupos de recursos, visite [Información general del Administrador de recursos de Azure](../articles/resource-group-overview.md).

		New-AzureRmResourceGroup -Name TestRG -Location centralus

	Resultado esperado:
	
		ResourceGroupName : TestRG
		Location          : centralus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG	

3. Cree una red virtual nueva denominada *TestVNet*, tal como se muestra a continuación.

		New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
			-AddressPrefix 192.168.0.0/16 -Location centralus	
		
	Resultado esperado:

		Name              	: TestVNet
		ResourceGroupName 	: TestRG
		Location          	: centralus
		Id                	: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag           		: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState      	: Succeeded
		Tags                   	: 
		AddressSpace           	: {
		                           "AddressPrefixes": [
		                             "192.168.0.0/16"
		                           ]
                         		 }
		DhcpOptions            	: {}
		Subnets                	: []
		VirtualNetworkPeerings 	: []

4. Almacene el objeto de red virtual en una variable, como se muestra a continuación.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
	
	>[AZURE.TIP] Puede combinar los pasos 3 y 4 mediante la ejecución de **$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus**.

5. Agregue una subred a la nueva variable de red virtual, como se muestra a continuación.

		Add-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
		
	Resultado esperado:

		Name              	: TestVNet
		ResourceGroupName 	: TestRG
		Location          	: centralus
		Id                	: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              	: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState 	: Succeeded
		Tags              	:
		AddressSpace      	: {
			                      "AddressPrefixes": [
			                        "192.168.0.0/16"
			                      ]
			                    }
		DhcpOptions       	: {}
		Subnets         	: [
			                      {
			                        "Name": "FrontEnd",
			                        "AddressPrefix": "192.168.1.0/24"
			                      }
			                    ]
		VirtualNetworkPeerings 	: []

6. Repita el paso 5 anterior para cada subred que quiera crear. El siguiente comando crea la subred *BackEnd* para nuestro escenario.

		Add-AzureRmVirtualNetworkSubnetConfig -Name BackEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24

7. Aunque cree subredes, actualmente solo existen en la variable local que se usa para recuperar la red virtual creada en el paso 4 anterior. Para guardar los cambios en Azure, ejecute el cmdlet **Set-AzureRMVirtualNetwork**, como se muestra a continuación.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet	
		
	Resultado esperado:

		Name              	: TestVNet
		ResourceGroupName 	: TestRG
		Location          	: centralus
		Id                	: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              	: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState 	: Succeeded
		Tags              	:
		AddressSpace      	: {
			                      "AddressPrefixes": [
			                        "192.168.0.0/16"
			                      ]
			                    }
		DhcpOptions       	: {
			                      "DnsServers": []
			                    }
		Subnets           	: [
			                      {
			                        "Name": "FrontEnd",
			                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
			                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
			                        "AddressPrefix": "192.168.1.0/24",
			                        "IpConfigurations": [],
			                        "ProvisioningState": "Succeeded"
			                      },
			                      {
			                        "Name": "BackEnd",
			                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
			                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
			                        "AddressPrefix": "192.168.2.0/24",
			                        "IpConfigurations": [],
			                        "ProvisioningState": "Succeeded"
			                      }
			                    ]
		VirtualNetworkPeerings : []

<!---HONumber=AcomDC_0831_2016-->