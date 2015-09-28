<properties
	pageTitle="Creación de una máquina virtual de Windows con el Administrador de recursos de Azure y PowerShell"
	description="Aprenda a usar Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows y Administrador de recursos en Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2015"
	ms.author="cynthn"/>

# Creación de una máquina virtual de Windows con el Administrador de recursos de Azure y PowerShell

En estos pasos se muestra cómo construir un conjunto de comandos en el modo de Administrador de recursos que creen y preconfiguren una máquina virtual de Azure basada en Windows. Puede utilizar este proceso de bloques de creación para crear de forma rápida un conjunto de comandos para una nueva máquina virtual basada en Windows y expandir una implementación existente. También puede utilizarlo para crear varios conjuntos de comandos que crean rápidamente un entorno personalizado de pruebas/desarrollo o de profesional de TI.

En estos pasos se sigue un enfoque consistente en atar cabos para crear conjuntos de comandos de Azure PowerShell. Este enfoque puede ser útil si está familiarizado con PowerShell o desea conocer los valores que debe especificar para una configuración correcta. Los usuarios avanzados de PowerShell pueden tomar los comandos y sustituir las variables con sus propios valores (las líneas que comienzan por "$").

[AZURE.INCLUDE [resource-manager-pointer-to-service-management](../../includes/resource-manager-pointer-to-service-management.md)]

- [Uso de Azure PowerShell para crear y preconfigurar máquinas virtuales basadas en Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

## Paso 1: Instalación de Azure PowerShell

También debe disponer de Azure PowerShell, versión 0.9.0 o posterior. Si no ha instalado y configurado Azure PowerShell, haga clic en [aquí](../powershell-install-configure.md) para obtener instrucciones.

Puede comprobar la versión de Azure PowerShell que ha instalado con este comando en el símbolo del sistema de Azure PowerShell.

	Get-Module azure | format-table version

Aquí tiene un ejemplo.

	Version
	-------
	0.9.0

Si no tiene la versión 0.9.0 o posterior, debe quitar Azure PowerShell mediante la opción Programas y características del Panel de control y, a continuación, instalar la versión más reciente. Para obtener más información, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

## Paso 2: Establecimiento de la suscripción

En primer lugar, ejecute un símbolo del sistema de Azure PowerShell.

A continuación, establezca su suscripción de Azure mediante la ejecución de estos comandos en el símbolo del sistema de Azure PowerShell. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >, por los nombres correctos.

	$subscr="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Puede obtener el nombre de suscripción correcta en la visualización de este comando.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

A continuación, cambie Azure PowerShell al modo Administrador de recursos.

	Switch-AzureMode AzureResourceManager

## Paso 3: Creación de recursos necesarios

Las máquinas virtuales basadas en Administrador de recursos requieren un grupo de recursos. Si es necesario, cree un nuevo grupo de recursos para la nueva máquina virtual con estos comandos. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >, por los nombres correctos.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

Para determinar el nombre único del grupo de recursos, utilice este comando para enumerar los grupos de recursos existente.

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Para obtener una lista de las ubicaciones de Azure donde puede crear máquinas virtuales basadas en Administrador de recursos, utilice estos comandos.

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

Las máquinas virtuales basadas en Administrador de recursos requieren una cuenta de almacenamiento basada en Administrador de recursos. Si es necesario, cree una nueva cuenta de almacenamiento para la nueva máquina virtual con estos comandos.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	$saName="<storage account name>"
	$saType="<storage account type, specify one: Standard_LRS, Standard_GRS, Standard_RAGRS, or Premium_LRS>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

Debe elegir un nombre único global para la cuenta de almacenamiento que contenga solo letras minúsculas y números. Puede usar este comando para enumerar las cuentas de almacenamiento existentes.

	Get-AzureStorageAccount | Sort Name | Select Name

Para probar si la cuenta de almacenamiento elegida tiene un nombre único global, necesitará ejecutar el comando **Test-AzureName** en el modo de administración de servicios de Azure de PowerShell. Use estos comandos.

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Si el comando Test-AzureName muestra "False", el nombre propuesto es único. Cuando haya determinado un nombre único, vuelva a cambiar Azure PowerShell al modo Administrador de recursos con este comando.

	Switch-AzureMode AzureResourceManager

Las máquinas virtuales basadas en Administrador de recursos puede utilizar una etiqueta de nombre de dominio público, que puede contener solo letras, números y guiones. El primer y el último carácter del campo deben ser una letra o un número.

Para probar si una etiqueta de nombre de dominio elegido tiene un nombre único global, use estos comandos.

	$domName="<domain name label to test>"
	$loc="<short name of an Azure location, for example, for West US, the short name is westus>"
	Test-AzureDnsAvailability -DomainQualifiedName $domName -Location $loc

Si DNSNameAvailability es "True", el nombre propuesto es un nombre único global.

>[AZURE.NOTE]El cmdlet Test-AzureDnsAvailability se llamaba Get-AzureCheckDnsAvailability en versiones anteriores a la versión 0.9.5 de Azure PowerShell. Si usa una versión 0.9.4 o anterior, reemplace Test-AzureDnsAvailability por Get-AzureCheckDnsAvailability en el comando anterior.

Las máquinas virtuales basadas en Administrador de recursos pueden colocarse en un conjunto de disponibilidad basada en Administrador de recursos. Si es necesario, cree un nuevo conjunto de disponibilidad para la nueva máquina virtual con estos comandos.

	$avName="<availability set name>"
	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

Use este comando para enumerar los conjuntos de disponibilidad existentes.

	Get-AzureAvailabilitySet –ResourceGroupName $rgName | Sort Name | Select Name

Las máquinas virtuales basadas en el Administrador de recursos pueden configurarse con reglas de NAT de entrada para permitir el tráfico entrante de Internet y colocarse en un conjunto de carga equilibrada. En ambos casos, debe especificar una instancia del equilibrador de carga y otras opciones. Para obtener más información, consulte [Creación de un equilibrador de carga mediante el Administrador de recursos de Azure](../load-balancer/load-balancer-arm-powershell.md)

Las máquinas virtuales basadas en Administrador de recursos requieren una red virtual basada en Administrador de recursos. Si es necesario, cree una nueva red virtual basada en Administrador de recursos con al menos una subred para la nueva máquina virtual. Este es un ejemplo de una nueva red virtual con dos subredes denominadas frontendSubnet y backendSubnet.

	$rgName="LOBServers"
	$locName="West US"
	$frontendSubnet=New-AzureVirtualNetworkSubnetConfig -Name frontendSubnet -AddressPrefix 10.0.1.0/24
	$backendSubnet=New-AzureVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix 10.0.2.0/24
	New-AzurevirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $frontendSubnet,$backendSubnet

Use estos comandos para enumerar las redes virtuales existentes.

	$rgName="<resource group name>"
	Get-AzureVirtualNetwork -ResourceGroupName $rgName | Sort Name | Select Name

## Paso 4: Creación del conjunto de comandos

Abra una nueva instancia del editor de texto que prefiera o el entorno de scripting integrado de PowerShell (ISE) y copie las líneas siguientes para iniciar el conjunto de comandos. Especifique el nombre del grupo de recursos, la ubicación de Azure y la cuenta de almacenamiento para esta nueva máquina virtual. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >, por los nombres correctos.

	Switch-AzureMode AzureResourceManager
	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"

Debe especificar el nombre de una red virtual basada en Administrador de recursos y el número de índice de una subred en la red virtual. Use estos comandos para enumerar las subredes de una red virtual.

	$rgName="<resource group name>"
	$vnetName="<virtual network name>"
	Get-AzureVirtualNetwork -Name $vnetName -ResourceGroupName $rgName | Select Subnets

El índice de subred es el número de la subred en la visualización de este comando, se numeran consecutivamente de izquierda a derecha y empiezan en 0.

En este ejemplo:

	PS C:\> Get-AzureVirtualNetwork -Name TestNet -ResourceGroupName LOBServers | Select Subnets

	Subnets
	-------
	{frontendSubnet, backendSubnet}

El índice de subred para frontendSubnet es 0 y el índice de subred para backendSubnet es 1.

Copie estas líneas en el conjunto de comandos y especifique un nombre de red virtual existente y el índice de subred de la máquina virtual.

	$vnetName="<name of an existing virtual network>"
	$subnetIndex=<index of the subnet on which to create the NIC for the virtual machine>
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName

A continuación, cree una tarjeta de interfaz de red (NIC). Copie una de las siguientes opciones en el conjunto de comandos y rellene la información necesaria.

### Opción 1: especificación de un nombre NIC y asignación de una dirección IP pública

Copie las siguientes líneas en el conjunto de comandos y especifique el nombre de la NIC.

	$nicName="<name of the NIC of the VM>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### Opción 2: especificación de un nombre de NIC y una etiqueta de nombre de dominio DNS

Copie las siguientes líneas en el conjunto de comandos y especifique el nombre de NIC y la etiqueta de nombre de dominio único global. Al crear máquinas virtuales en el modo de administración de servicios de Azure PowerShell, Azure completa estos pasos automáticamente.

	$nicName="<name of the NIC of the VM>"
	$domName="<domain name label>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### Opción 3: especificación de un nombre de NIC y asignación de una dirección IP privada estática

Copie las siguientes líneas en el conjunto de comandos y especifique el nombre de la NIC.

	$nicName="<name of the NIC of the VM>"
	$staticIP="<available static IP address on the subnet>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id -PrivateIpAddress $staticIP

### Opción 4: especificación de un nombre de NIC y una instancia del equilibrador de carga para una regla de NAT de entrada.

Para crear una tarjeta de NIC y agregarla a una instancia del equilibrador de carga para una regla de NAT de entrada, necesitará:

- El nombre de instancia del equilibrador de carga creada anteriormente que tenga una regla de NAT de entrada para el tráfico que se reenvía a la máquina virtual.
- El número de índice del grupo de direcciones de back-end de la instancia de equilibrador de carga que se va a asignar a la NIC.
- El número de índice de la regla de NAT de entrada para asignar a la NIC.

Para obtener información sobre cómo crear una instancia del equilibrador de carga con reglas de NAT de entrada, consulte [Creación de un equilibrador de carga mediante el Administrador de recursos de Azure](../load-balancer/load-balancer-arm-powershell.md).

Copie estas líneas en el conjunto de comandos y especifique los nombres y números de índice necesarios.

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$natRuleIndex=<index of the inbound NAT rule, starting at 0>
	$lb=Get-AzureLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex] -LoadBalancerInboundNatRule $lb.InboundNatRules[$natRuleIndex]

La cadena de $nicName debe ser única para el grupo de recursos. Un procedimiento recomendado es incorporar el nombre de la máquina virtual a la cadena, como "LOB07-NIC".

### Opción 5: especificación de un nombre de NIC y una instancia del equilibrador de carga para un conjunto de carga equilibrada.

Para crear una NIC y agregarla a una instancia del equilibrador de carga para un conjunto de carga equilibrada, necesitará:

- El nombre de una instancia del equilibrador de carga creada anteriormente que tenga una regla para el tráfico de carga equilibrada.
- El número de índice del grupo de direcciones de back-end de la instancia de equilibrador de carga que se va a asignar a la NIC.

Para obtener información sobre cómo crear una instancia del equilibrador de carga con reglas para el tráfico de carga equilibrada, consulte [Creación de un equilibrador de carga mediante el Administrador de recursos de Azure](../load-balancer/load-balancer-arm-powershell.md).

Copie estas líneas en el conjunto de comandos y especifique los nombres y números de índice necesarios.

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$lb=Get-AzureLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex]

A continuación, cree un objeto de la máquina virtual local y, opcionalmente, agréguelo a un conjunto de disponibilidad. Copie una de las dos opciones siguientes en el conjunto de comandos y rellene el nombre, el tamaño y el nombre del conjunto de disponibilidad.

Opción 1: Especificación de un nombre y tamaño de máquina virtual.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize

Para determinar los valores posibles de la cadena de tamaño de máquina virtual para la opción 1, use estos comandos.

	$locName="<Azure location of your resource group>"
	Get-AzureVMSize -Location $locName | Select Name

Opción 2: Especificación de un nombre y un tamaño de la máquina virtual y su incorporación a un conjunto de disponibilidad.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$avName="<availability set name>"
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

Para determinar los valores posibles de la cadena de tamaño de máquina virtual para la opción 2, use estos comandos.

	$rgName="<resource group name>"
	$avName="<availability set name>"
	Get-AzureVMSize -ResourceGroupName $rgName -AvailabilitySetName $avName | Select Name

> [AZURE.NOTE]Actualmente con Administrador de recursos solo puede agregar una máquina virtual a un conjunto de disponibilidad durante su creación.

Para agregar un disco de datos adicionales a la máquina virtual, copie estas líneas al conjunto de comando y especifique la configuración de disco.

	$diskSize=<size of the disk in GB>
	$diskLabel="<the label on the disk>"
	$diskName="<name identifier for the disk in Azure storage, such as 21050529-DISK02>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty

A continuación, deberá determinar el publicador, la oferta y SKU de la imagen para la máquina virtual. Esta es una tabla de imágenes basadas en Windows y utilizadas habitualmente.

|Nombre de publicador | Nombre de la oferta | Nombre de SKU
|:---------------------------------|:-------------------------------------------|:---------------------------------|
|Microsoft Windows Server | Windows Server | 2008-R2-SP1 |
|Microsoft Windows Server | Windows Server | Centro de datos de 2012 |
|Microsoft Windows Server | Windows Server | Centro de datos de 2012-R2 |
|MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
|MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
|MicrosoftSQLServer | WS2012R2 SQL2014 | Enterprise-Optimized-for-DW |
|MicrosoftSQLServer | WS2012R2 SQL2014 | Enterprise-Optimized-for-OLTP |
|MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
|MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |

Si la imagen de máquina virtual que necesita no aparece, siga las instrucciones que se muestran [aquí](resource-groups-vm-searching.md#powershell) para determinar el publicador, la oferta y los nombres de SKU.

Copie estos comandos en el conjunto de comandos y rellene el publicador, la oferta y nombres de SKU.

	$pubName="<Image publisher name>"
	$offerName="<Image offer name>"
	$skuName="<Image SKU name>"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id

Por último, copie estos comandos en el conjunto de comandos y rellene el identificador de nombre del disco de sistema operativo para la máquina virtual.

	$diskName="<name identifier for the disk in Azure storage, such as OSDisk>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Paso 5: Ejecución del conjunto de comandos

Revise el conjunto de comandos de Azure PowerShell creado en el editor de texto o el ISE de PowerShell, que consta de varios bloques de comandos del paso 4. Asegúrese de que ha especificado todas las variables necesarias y de que tengan los valores correctos. También asegúrese de que ha quitado todos los caracteres < and >.

Si tiene sus comandos en un editor de texto, copie el conjunto de comandos en el Portapapeles y, a continuación, haga clic con el botón derecho en el símbolo del sistema del Azure PowerShell abierto. Así, se emitirá el conjunto de comandos como una serie de comandos de PowerShell y se creará la máquina virtual de Azure. Como alternativa, ejecute el conjunto de comandos desde el ISE de Azure PowerShell.

Si va a volver a crear esta máquina virtual o una similar, puede guardar este conjunto de comandos como un archivo de script de PowerShell (*.ps1).

## Ejemplo

Se necesita un conjunto de comandos de PowerShell para crear una máquina virtual adicional para una carga de trabajo de línea de negocio basada en web que:

- Se encuentre en el grupo de recursos LOBServers existente
- Utilice la imagen de Windows Server 2012 R2 Datacenter
- Tenga el nombre LOB07 y se encuentre en el conjunto de disponibilidad WEB\_AS existente
- Tenga una NIC con una dirección IP pública en la subred FrontEnd (índice de subred 0) de la red virtual AZDatacenter existente
- Tenga un disco de datos adicional de 200 GB

Este es el comando de Azure PowerShell correspondiente para crear esta máquina virtual, basado en el proceso descrito en el paso 4.

	# Switch to the Resource Manager mode
	Switch-AzureMode AzureResourceManager

	# Set values for existing resource group and storage account names
	$rgName="LOBServers"
	$locName="West US"
	$saName="contosolobserverssa"

	# Set the existing virtual network and subnet index
	$vnetName="AZDatacenter"
	$subnetIndex=0
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	# Create the NIC
	$nicName="LOB07-NIC"
	$domName="contoso-vm-lob07"
	$pip=New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

	# Specify the name, size, and existing availability set
	$vmName="LOB07"
	$vmSize="Standard_A3"
	$avName="WEB_AS"
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

	# Add a 200 GB additional data disk
	$diskSize=200
	$diskLabel="APPStorage"
	$diskName="21050529-DISK02"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI -CreateOption empty

	# Specify the image and local administrator account, and then add the NIC
	$pubName="MicrosoftWindowsServer"
	$offerName="WindowsServer"
	$skuName="2012-R2-Datacenter"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id

	# Specify the OS disk name and create the VM
	$diskName="OSDisk"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Recursos adicionales

[Proceso, red y proveedores de almacenamiento de Azure en el Administrador de recursos de Azure](virtual-machines-azurerm-versus-azuresm.md)

[Información general del Administrador de recursos de Azure](../resource-group-overview.md)

[Implementación y administración de Máquinas virtuales de Azure mediante el Administrador de recursos de plantillas y PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Creación de una máquina virtual Windows con una plantilla del Administrador de recursos y PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple)

[Instalación y configuración de Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=Sept15_HO3-->