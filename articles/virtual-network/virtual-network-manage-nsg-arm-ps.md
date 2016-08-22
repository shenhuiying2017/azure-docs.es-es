<properties 
   pageTitle="Administración de los NSG con PowerShell en Resource Manager | Microsoft Azure"
   description="Obtenga información sobre cómo administrar NSG existentes con PowerShell en Resource Manager"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jdial" />

# Administración de los NSG con PowerShell

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] modelo de implementación clásica.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Recuperar información

Puede consultar los NSG existentes, recuperar las reglas de un NSG existente y averiguar cuáles son los recursos a los que está asociado un NSG.

### Consultar los NSG existentes
Para consultar todos los NSG existentes en una suscripción, ejecute el cmdlet `Get-AzureRmNetworkSecurityGroup` como se muestra a continuación.

	Get-AzureRmNetworkSecurityGroup

Resultado esperado:

	Name                 : NSG-BackEnd
	ResourceGroupName    : RG-NSG
	Location             : westus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-BackEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 	                       
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	
	Name                 : NSG-FrontEnd
	ResourceGroupName    : RG-NSG
	Location             : eastus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	                     	
	Name                 : WEB1
	ResourceGroupName    : RG101
	Location             : eastus2
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG101/providers/M
	                       icrosoft.Network/networkSecurityGroups/WEB1
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]


Para consultar la lista de NSG de un grupo de recursos específico, ejecute el cmdlet `Get-AzureRmNetworkSecurityGroup` como se muestra a continuación.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG

Resultado esperado:

	Name                 : NSG-BackEnd
	ResourceGroupName    : RG-NSG
	Location             : westus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-BackEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 	                       
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	
	Name                 : NSG-FrontEnd
	ResourceGroupName    : RG-NSG
	Location             : eastus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
		 
### Mostrar todas las reglas de un NSG

Para consultar las reglas de un NSG llamado **NSG-FrontEnd**, ejecute el cmdlet `Get-AzureRmNetworkSecurityGroup` como se muestra a continuación.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules

Resultado esperado:
	
	Name                     : rdp-rule
	Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/						   Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
	Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState        : Succeeded
	Description              : Allow RDP
	Protocol                 : Tcp
	SourcePortRange          : *
	DestinationPortRange     : 3389
	SourceAddressPrefix      : Internet
	DestinationAddressPrefix : *
	Access                   : Allow
	Priority                 : 100
	Direction                : Inbound
	
	Name                     : web-rule
	Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/						   Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
	Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState        : Succeeded
	Description              : Allow HTTP
	Protocol                 : Tcp
	SourcePortRange          : *
	DestinationPortRange     : 80
	SourceAddressPrefix      : Internet
	DestinationAddressPrefix : *
	Access                   : Allow
	Priority                 : 101
	Direction                : Inbound

>[AZURE.NOTE] También puede usar `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` para enumerar las reglas predeterminadas desde el NSG **NSG-FrontEnd**.

### Consultar las asociaciones de NSG

Para consultar cuáles son los recursos con que está asociado el NSG **NSG-FrontEnd**, ejecute el cmdlet `Get-AzureRmNetworkSecurityGroup` como se muestra a continuación.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd

Busque las propiedades **NetworkInterfaces** y **Subnets** como se muestra a continuación:

	NetworkInterfaces    : []
	Subnets              : [
	                         {
	                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
	                           "IpConfigurations": []
	                         }
	                       ]

En el ejemplo anterior, el NSG no está asociado a ninguna interfaz de red (NIC) y está asociado a una subred llamada **FrontEnd**.

## Administrar las reglas

Puede agregar reglas a un NSG existente, editar reglas existentes y quitar reglas.

### Agregar una regla

Para agregar una regla que permite el tráfico **de entrada** al puerto **443** desde cualquier máquina al NSG **NSG-FrontEnd**, siga estos pasos.

1. Ejecute el cmdlet `Get-AzureRmNetworkSecurityGroup` para recuperar el NSG existente y almacenarlo en una variable, tal como se muestra a continuación.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Ejecute el cmdlet `Add-AzureRmNetworkSecurityRuleConfig`, tal como aparece a continuación.

		Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule `
		    -Description "Allow HTTPS" `
		    -Access Allow `
		    -Protocol Tcp `
		    -Direction Inbound `
		    -Priority 102 `
		    -SourceAddressPrefix * `
		    -SourcePortRange * `
		    -DestinationAddressPrefix * `
		    -DestinationPortRange 443

3. Para guardar los cambios hechos en el NSG, ejecute el cmdlet `Set-AzureRmNetworkSecurityGroup`, tal como aparece a continuación.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	La salida esperada que solo muestra las reglas de seguridad:

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         },
		                         {
		                           "Name": "https-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
		                           "Description": "Allow HTTPS",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "443",
		                           "SourceAddressPrefix": "*",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 102,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

### Cambiar una regla

Para cambiar la regla que se creó anteriormente y permitir el tráfico de entrada solo desde **Internet**, siga estos pasos.

1. Ejecute el cmdlet `Get-AzureRmNetworkSecurityGroup` para recuperar el NSG existente y almacenarlo en una variable, tal como se muestra a continuación.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Ejecute el cmdlet `Set-AzureRmNetworkSecurityRuleConfig`, tal como aparece a continuación.

		Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule `
		    -Description "Allow HTTPS" `
		    -Access Allow `
		    -Protocol Tcp `
		    -Direction Inbound `
		    -Priority 102 `
		    -SourceAddressPrefix * `
		    -SourcePortRange Internet `
		    -DestinationAddressPrefix * `
		    -DestinationPortRange 443

3. Para guardar los cambios hechos en el NSG, ejecute el cmdlet `Set-AzureRmNetworkSecurityGroup`, tal como aparece a continuación.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	La salida esperada que solo muestra las reglas de seguridad:

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         },
		                         {
		                           "Name": "https-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
		                           "Description": "Allow HTTPS",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "443",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 102,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

### Eliminar una regla

1. Ejecute el cmdlet `Get-AzureRmNetworkSecurityGroup` para recuperar el NSG existente y almacenarlo en una variable, tal como se muestra a continuación.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Ejecute el cmdlet `Remove-AzureRmNetworkSecurityRuleConfig`, tal como aparece a continuación.

		Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule

3. Para guardar los cambios hechos en el NSG, ejecute el cmdlet `Set-AzureRmNetworkSecurityGroup`, tal como aparece a continuación.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	La salida esperada que solo muestra las reglas de seguridad. Observe que ya no aparece **https-rule**:

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         }
		                       ]

## Administrar las asociaciones

Puede asociar un NSG a subredes y NIC. También puede desasociar un NSG de cualquier recurso al que está asociado.

### Asociar un NSG a una NIC

Para asociar el NSG **NSG-FrontEnd** a la NIC **TestNICWeb1**, siga estos pasos.

1. Ejecute el cmdlet `Get-AzureRmNetworkSecurityGroup` para recuperar el NSG existente y almacenarlo en una variable, tal como se muestra a continuación.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Ejecute el cmdlet `Get-AzureRmNetworkInterface` para recuperar la NIC existente y almacenarla en una variable, tal como se muestra a continuación.

		$nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
		    -Name TestNICWeb1

3. Defina la propiedad **NetworkSecurityGroup** de la variable **NIC** en el valor de la variable **NSG**, tal como se muestra a continuación.

		$nic.NetworkSecurityGroup = $nsg

4. Para guardar los cambios hechos en la NIC, ejecute el cmdlet `Set-AzureRmNetworkInterface`, tal como se muestra a continuación.

		Set-AzureRmNetworkInterface -NetworkInterface $nic

	La salida esperada que solo muestra la propiedad **NetworkSecurityGroup**:

		NetworkSecurityGroup : {
		                         "SecurityRules": [],
		                         "DefaultSecurityRules": [],
		                         "NetworkInterfaces": [],
		                         "Subnets": [],
		                         "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                       }

### Desasociar un NSG de una NIC

Para desasociar el NSG **NSG-FrontEnd** de la NIC **TestNICWeb1**, siga estos pasos.

1. Ejecute el cmdlet `Get-AzureRmNetworkSecurityGroup` para recuperar el NSG existente y almacenarlo en una variable, tal como se muestra a continuación.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Ejecute el cmdlet `Get-AzureRmNetworkInterface` para recuperar la NIC existente y almacenarla en una variable, tal como se muestra a continuación.

		$nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
		    -Name TestNICWeb1

3. Defina la propiedad **NetworkSecurityGroup** de la variable **NIC** en **$null**, tal como se muestra a continuación.

		$nic.NetworkSecurityGroup = $null

4. Para guardar los cambios hechos en la NIC, ejecute el cmdlet `Set-AzureRmNetworkInterface`, tal como se muestra a continuación.

		Set-AzureRmNetworkInterface -NetworkInterface $nic

	La salida esperada que solo muestra la propiedad **NetworkSecurityGroup**:

		NetworkSecurityGroup : null

### Desasociar un NSG de una subred

Para desasociar el NSG **NSG-FrontEnd** de la subred **FrontEnd**, siga estos pasos.

1. Ejecute el cmdlet `Get-AzureRmVirtualNetwork` para recuperar la red virtual existente y almacenarla en una variable, tal como se muestra a continuación.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
		    -Name TestVNet

2. Ejecute el cmdlet `Get-AzureRmVirtualNetworkSubnetConfig` para recuperar la subred **FrontEnd** y almacenarla en una variable, tal como se muestra a continuación.

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
		    -Name FrontEnd 

3. Defina la propiedad **NetworkSecurityGroup** de la variable **subnet** en **$null**, tal como se muestra a continuación.

		$subnet.NetworkSecurityGroup = $null

4. Para guardar los cambios hechos en la subred, ejecute el cmdlet `Set-AzureRmVirtualNetwork`, tal como se muestra a continuación.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	La salida esperada que solo muestra las propiedades de la subred **FrontEnd**. Observe que no hay una propiedad para **NetworkSecurityGroup**:

			...
			Subnets           : [
			                      {
			                        "Name": "FrontEnd",
			                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
			                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
			                        "AddressPrefix": "192.168.1.0/24",
			                        "IpConfigurations": [
			                          {
			                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
			                          },
			                          {
			                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
			                          }
			                        ],
			                        "ProvisioningState": "Succeeded"
			                      },
									...
			                    ]

### Asociación de un grupo de seguridad de red a una máquina virtual

Para asociar nuevamente el NSG **NSG-FrontEnd** a la subred **FrontEnd**, siga estos pasos.

1. Ejecute el cmdlet `Get-AzureRmVirtualNetwork` para recuperar la red virtual existente y almacenarla en una variable, tal como se muestra a continuación.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
		    -Name TestVNet

2. Ejecute el cmdlet `Get-AzureRmVirtualNetworkSubnetConfig` para recuperar la subred **FrontEnd** y almacenarla en una variable, tal como se muestra a continuación.

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
		    -Name FrontEnd 

1. Ejecute el cmdlet `Get-AzureRmNetworkSecurityGroup` para recuperar el NSG existente y almacenarlo en una variable, tal como se muestra a continuación.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

3. Defina la propiedad **NetworkSecurityGroup** de la variable **subnet** en **$null**, tal como se muestra a continuación.

		$subnet.NetworkSecurityGroup = $nsg

4. Para guardar los cambios hechos en la subred, ejecute el cmdlet `Set-AzureRmVirtualNetwork`, tal como se muestra a continuación.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	La salida esperada que solo muestra la propiedad **NetworkSecurityGroup** de la subred **FrontEnd**:

		...
		"NetworkSecurityGroup": {
		                          "SecurityRules": [],
		                          "DefaultSecurityRules": [],
		                          "NetworkInterfaces": [],
		                          "Subnets": [],
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        }
		...

## Eliminación de un grupo de seguridad de red

Solo se puede eliminar un NSG que no está asociado a ningún recurso. Para eliminar un NSG, siga estos pasos.

1. Para comprobar los recursos asociados a un NSG, ejecute `azure network nsg show` tal como aparece en [Consultar las asociaciones de NSG](#View-NSGs-associations).
2. Si el NSG está asociado a alguna NIC, ejecute `azure network nic set` tal como aparece en [Desasociar un NSG de una NIC](#Dissociate-an-NSG-from-a-NIC) para cada NIC.
3. Si el NSG está asociado a alguna subred, ejecute `azure network vnet subnet set` tal como aparece en [Desasociar un NSG de una subred](#Dissociate-an-NSG-from-a-subnet) para cada subred.
4. Para eliminar el NSG, ejecute el cmdlet `Remove-AzureRmNetworkSecurityGroup`, tal como se muestra a continuación.

		Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force

	>[AZURE.NOTE] El parámetro **-Force** le asegura que no necesita confirmar la eliminación.

## Pasos siguientes

- [Habilite el registro](virtual-network-nsg-manage-log.md) para los NSG.

<!---HONumber=AcomDC_0810_2016-->