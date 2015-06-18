<properties 
   pageTitle="Creación de una máquina virtual con varias NIC"
   description="Cómo crear máquinas virtuales con varias NIC"
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/30/2015"
   ms.author="telmos" />

# Creación de una máquina virtual con varias NIC

La característica de varias NIC le permite crear y administrar varias tarjetas de interfaz de red virtual (NIC) en las máquinas virtuales (VM) de Azure. Tener varias NIC es un requisito para muchos dispositivos virtuales de red, por ejemplo, entrega de aplicaciones y soluciones de optimización de la WAN. Tener varias NIC también proporciona más funcionalidad de administración de tráfico de red, incluido el aislamiento del tráfico entre una NIC de front-end y varias NIC de back-end o la separación entre el tráfico del plano de datos y el tráfico del plano de administración.

![Varias NIC para máquina virtual](./media/virtual-networks-multiple-nics/IC757773.png)

La figura anterior muestra una máquina virtual con tres NIC, cada una de ellas conectada a una subred diferente.

## Límites y restricciones

Actualmente, tener varias NIC tiene los siguientes límites y restricciones:

- Las máquinas virtuales con varias NIC deben crearse en redes virtuales de Azure. Las máquinas virtuales que no sean de redes virtuales no están admitidas. 
- Dentro de un servicio en la nube solo se permite únicamente la configuración siguiente: 
	- Todas las máquinas virtuales de ese servicio en la nube deben tener varias NIC habilitadas o 
	- Todas las máquinas virtuales de ese servicio en la nube deben tener cada una sola NIC 

>[AZURE.IMPORTANT]Si intenta agregar una máquina virtual con varias NIC a una implementación (servicio en la nube) que ya contiene una máquina virtual de NIC única (o viceversa), recibirá el siguiente error: No se admiten máquinas virtuales con interfaces de red secundaria y máquinas virtuales sin interfaces de red secundaria en la misma implementación, además, no se puede actualizar una máquina virtual sin interfaces de red secundaria para que tenga interfaces de red secundaria y viceversa.
 
- VIP accesible desde Internet solo se admite en la NIC "predeterminada". Solo hay una VIP a la IP de la NIC predeterminada. 
- Actualmente, no se admiten direcciones IP públicas del nivel de instancia para máquinas virtuales con varias NIC. 
- El orden de las NIC desde la máquina virtual será aleatorio y también podría cambiar en todas las actualizaciones de infraestructura de Azure. Sin embargo, las direcciones IP y las direcciones MAC Ethernet correspondientes seguirán siendo las mismas. Por ejemplo, suponga que **Eth1** tiene la dirección IP 10.1.0.100 y la dirección MAC 00-0D-3A-B0-39-0D; después de una actualización de la infraestructura de Azure y de reiniciar el equipo, se podría cambiar a Eth2, pero el emparejamiento de la IP y la MAC seguirá siendo el mismo. Cuando es un cliente quien ejecuta un reinicio, el orden de NIC seguirá siendo el mismo. 
- La dirección de cada NIC en cada máquina virtual debe estar ubicada en una subred; las direcciones que se encuentran en la misma subred se pueden asignar a cada una de las varias NIC de una sola máquina virtual. 
- El tamaño de la máquina virtual determina el número de las NIC que se puede crear para una máquina virtual. La tabla siguiente enumera los números de NIC correspondientes al tamaño de las máquinas virtuales: 

|Tamaño de memoria virtual (SKU estándar)|NIC (número máximo permitido por máquina virtual)|
|---|---|
|Todos los tamaños básicos|1|
|A0\extra pequeño|1|
|A1\pequeño|1|
|A2\mediano|1|
|A3\grande|2|
|A4\extra grande|4|
|A5|1|
|A6|2|
|A7|4|
|A8|2|
|A9|4|
|A10|2|
|A11|4|
|D1|1|
|D2|2|
|D3|4|
|D4|8|
|D11|2|
|D12|4|
|D13|8|
|D14|16|
|DS1|1|
|DS2|2|
|DS3|4|
|DS4|8|
|DS11|2|
|DS12|4|
|DS13|8|
|DS14|16|
|G1|1|
|G2|2|
|G3|4|
|G4|8|
|G5|16|
|Todos los tamaños|1|

## Grupos de seguridad de red
Cualquier NIC de una máquina virtual puede asociarse a un grupo de seguridad de red (NSG), incluida cualquier NIC de una máquina virtual que tenga varias NIC habilitadas. Si a una NIC se le asigna una dirección dentro de una subred que está asociada a un NSG, las reglas de NSG de la subred también se aplican a esa NIC. Además de asociar subredes a NSG, también puede asociar una NIC a un NSG.

Si una subred está asociada a un NSG y una NIC dentro de esa subred está asociada individualmente a un NSG, las reglas asociadas de NSG se aplican en "** orden de flujo**" según la dirección del tráfico que se pasa dentro o fuera de la NIC:

- El **tráfico entrante** cuyo destino es la NIC en cuestión fluye primero a través de la subred, desencadenando reglas de NSG de la subred, antes de pasar a la NIC y desencadenar reglas de NSG de la NIC. - El **tráfico de salida** cuyo origen es la NIC en cuestión fluye primero fuera de la NIC, desencadenando reglas de NSG de la NIC, antes de pasar a la subred y desencadenar reglas de NSG de la subred. 

La ilustración anterior representa cómo se realiza la aplicación de reglas de NSG basándose en el flujo de tráfico (de la máquina virtual a la subred o de la subred a la máquina virtual).

## Configuración de una máquina virtual con varias NIC

Las instrucciones siguientes le ayudarán a crear una máquina virtual de varias NIC con 3 NIC: una NIC predeterminada y dos NIC adicionales. Los pasos de configuración crearán una máquina virtual que se configurará según el fragmento de archivo de configuración de servicio siguiente:

	<VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
	<AddressSpace>
	  <AddressPrefix>10.1.0.0/16</AddressPrefix>
	    </AddressSpace>
	    <Subnets>
	      <Subnet name="Frontend">
	        <AddressPrefix>10.1.0.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Midtier">
	        <AddressPrefix>10.1.1.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Backend">
	        <AddressPrefix>10.1.2.0/23</AddressPrefix>
	      </Subnet>
	      <Subnet name="GatewaySubnet">
	        <AddressPrefix>10.1.200.0/28</AddressPrefix>
	      </Subnet>
	    </Subnets>
	… Skip over the remainder section …
	</VirtualNetworkSite>


Necesitará cumplir los siguientes requisitos previos antes de poder ejecutar los comandos de PowerShell del ejemplo.

- Una suscripción de Azure.
- Una red virtual configurada. Para obtener más información sobre redes virtuales, consulte [Información general sobre redes virtuales](https://msdn.microsoft.com/library/azure/jj156007.aspx).
- La versión más reciente de Azure PowerShell descargada e instalada. Consulte [Instalación y configuración de Azure PowerShell](../install-configure-powershell).

1. Seleccione una imagen de máquina virutal en la galería de imágenes de máquinas virtuales de Azure. Tenga en cuenta que las imágenes cambian con frecuencia y están disponibles por región. La imagen especificada en el ejemplo siguiente puede cambiar o puede no estar presente en su región, así que asegúrese de especificar la imagen que necesita. 

	    $image = Get-AzureVMImage `
	    	-ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. Creación de una configuración de máquina virtual

		$vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
			-Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. Cree el inicio de sesión de administrador predeterminado.

		Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
			-Password "<YourAdminPassword>"

1. Agregue NIC adicionales a la configuración de la máquina virtual.

		Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
			-SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm 
		Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
			-SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. Especifique la subred y la dirección IP de la NIC predeterminada.

		Set-AzureSubnet -SubnetNames "Frontend" -VM $vm Set-AzureStaticVNetIP  `
			-IPAddress "10.1.0.100" -VM $vm

1. Cree la máquina virtual en la red virtual.

		New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE]La red virtual que especifique aquí debe existir previamente (tal como se indicó en los requisitos previos). En el ejemplo siguiente se especifica una red virtual denominada "MultiNIC VNet".

## Otras referencias

[Información general sobre redes virtuales](https://msdn.microsoft.com/library/azure/jj156007.aspx)

[Tareas de configuración de red virtual](https://msdn.microsoft.com/library/azure/jj156206.aspx)

[Publicación de blog: Varias NIC de máquina virtual y dispositivos de red virtual de Azure](../multiple-vm-nics-and-network-virtual-appliances-in-azure)

<!---HONumber=58--> 