<properties 
   pageTitle="Creación de grupos de seguridad de red en el modo ARM mediante PowerShell | Microsoft Azure"
   description="Aprenda a crear e implementar grupos de seguridad de red en ARM mediante PowerShell"
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
   ms.date="09/14/2015"
   ms.author="telmos" />

# Creación de grupos de seguridad de red en PowerShell

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puede [crear grupos de seguridad de red en el modelo de implementación clásica](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

En los siguientes comandos de PowerShell de ejemplo se presupone que ya se ha creado un entorno simple según el escenario anterior. Si desea ejecutar los comandos tal y como aparecen en este documento, cree primero el entorno de prueba mediante la implementación de [esta plantilla](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), haga clic en **Implementar en Azure**, reemplace los valores de parámetro predeterminados si es necesario y siga las instrucciones del portal.

## Creación del grupo de seguridad de red para la subred front-end
Para crear un grupo de seguridad de red denominado *NSG-FrontEnd* según el escenario anterior, siga estos pasos.

1. Si es la primera vez que usa Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](powershell-install-configure.md) y siga las instrucciones hasta el final para iniciar sesión en Azure y seleccionar su suscripción.
2. En un símbolo del sistema de Azure PowerShell, ejecute el cmdlet **Switch-AzureMode** para cambiar al modo de Administrador de recursos, como se muestra a continuación.

		Switch-AzureMode AzureResourceManager
	
	Resultado esperado:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]El cmdlet Switch-AzureMode pronto estará en desuso. Cuando esto suceda, se cambiará el nombre de todos los cmdlets del Administrador de recursos.

3. Cree una regla de seguridad que permita el acceso desde Internet al puerto 3389.

		$rule1 = New-AzureNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
		    -SourceAddressPrefix Internet -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 3389

4. Cree una regla de seguridad que permita el acceso desde Internet al puerto 80.

		$rule2 = New-AzureNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
		    -SourceAddressPrefix Internet -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 80

5. Agregue las reglas creadas anteriormente a un nuevo grupo de seguridad de red denominado **NSG-FrontEnd**.

		New-AzureNetworkSecurityGroup -ResourceGroupName TestRG -Location westus -Name "NSG-FrontEnd" `
			-SecurityRules $rule1,$rule2

	Resultado esperado (no muestra las reglas predeterminadas):

		Name                 : NSG-FrontEnd
		ResourceGroupName    : TestRG
		Location             : westus
		Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroup
		                       s/NSG-FrontEnd
		Etag                 : W/"6e0d0b50-5b92-4a21-9517-aa7ee8d3b398"
		ProvisioningState    : Succeeded
		Tags                 : 
		SecurityRules        : [	
		                         {
		                           "Name": "rdp-rule",
		                           "Etag": "W/"6e0d0b50-5b92-4a21-9517-aa7ee8d3b398"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSe
		                       curityGroups/NSG-FrontEnd/securityRules/rdp-rule",
		                           "Description": "Allow RDP",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "3389",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 100,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         },
		                         {
		                           "Name": "web-rule",
		                           "Etag": "W/"6e0d0b50-5b92-4a21-9517-aa7ee8d3b398"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSe
		                       curityGroups/NSG-FrontEnd/securityRules/web-rule",
		                           "Description": "Allow HTTP",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "80",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 101,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]
		DefaultSecurityRules : [ ... ]
		NetworkInterfaces    : []
		Subnets              : []		

6. Asocie el grupo de seguridad de red creado anteriormente a la subred *Front-end*.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
		Set-AzureVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
			-AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $nsg

	Resultado esperado que muestra solo la configuración de subred *Front-end*, observe el valor de la propiedad **NetworkSecurityGroup**:

		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"42bc8afe-f843-4a09-9c5f-d294c6338a4f"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
		                          },
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
		                          }
		                        ],
		                        "NetworkSecurityGroup": {
		                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        },
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }

## Creación del grupo de seguridad de red para la subred back-end
Para crear un grupo de seguridad de red denominado *NSG-BackEnd* según el escenario anterior, siga estos pasos.

1. Crear una regla de seguridad que permita el acceso desde la subred front-end al puerto 1433 (puerto predeterminado utilizado por SQL Server).

		$rule1 = New-AzureNetworkSecurityRuleConfig -Name frontend-rule -Description "Allow FE subnet" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
		    -SourceAddressPrefix 192.168.1.0/24 -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 1433

4. Crear una regla de seguridad que bloquee el acceso a Internet.

		$rule2 = New-AzureNetworkSecurityRuleConfig -Name web-rule -Description "Block Internet" `
		    -Access Deny -Protocol * -Direction Outbound -Priority 200 `
		    -SourceAddressPrefix * -SourcePortRange * `
		    -DestinationAddressPrefix Internet -DestinationPortRange *

5. Agregue las reglas creadas anteriormente a un nuevo grupo de seguridad de red denominado **NSG-BackEnd**.

		New-AzureNetworkSecurityGroup -ResourceGroupName TestRG -Location westus `-Name "NSG-BackEnd" `
			-SecurityRules $rule1,$rule2

	Resultado esperado (no muestra las reglas predeterminadas):

		Name                 : NSG-BackEnd
		ResourceGroupName    : TestRG
		Location             : westus
		Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroup
		                       s/NSG-BackEnd
		Etag                 : W/"3ca4eb9f-03a7-4e72-be25-6c066c0d8928"
		ProvisioningState    : Succeeded
		Tags                 : 
		SecurityRules        : [
		                         {
		                           "Name": "frontend-rule",
		                           "Etag": "W/"3ca4eb9f-03a7-4e72-be25-6c066c0d8928"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/frontend-rule",
		                           "Description": "Allow FE subnet",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "1433",
		                           "SourceAddressPrefix": "192.168.1.0/24",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 100,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         },
		                         {
		                           "Name": "web-rule",
		                           "Etag": "W/"3ca4eb9f-03a7-4e72-be25-6c066c0d8928"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/web-rule",
		                           "Description": "Block Internet",
		                           "Protocol": "*",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "*",
		                           "SourceAddressPrefix": "*",
		                           "DestinationAddressPrefix": "Internet",
		                           "Access": "Deny",
		                           "Priority": 200,
		                           "Direction": "Outbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]
		DefaultSecurityRules : [...]
		NetworkInterfaces    : []
		Subnets              : []		

6. Asocie el grupo de seguridad de red creado anteriormente a la subred *Back-end*.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
		Set-AzureVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
			-AddressPrefix 192.168.2.0/24 -NetworkSecurityGroup $nsg

	Resultado esperado que muestra solo la configuración de la subred *Back-end*, observe el valor de la propiedad **NetworkSecurityGroup**:

		Subnets           : [
                      {
                        "Name": "BackEnd",
                        "Etag": "W/"42bc8afe-f843-4a09-9c5f-d294c6338a4f"",
                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                        "AddressPrefix": "192.168.2.0/24",
                        "IpConfigurations": [
                          {
                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL1/ipConfigurations/ipconfig1"
                          },
                          {
                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL2/ipConfigurations/ipconfig1"
                          }
                        ],
                        "NetworkSecurityGroup": {
                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                        },
                        "RouteTable": null,
                        "ProvisioningState": "Succeeded"
                      }

<!---HONumber=Sept15_HO4-->