---
title: "Creación de una máquina virtual de SQL Server en Azure PowerShell (Resource Manager)| Microsoft Docs"
description: "Ofrece pasos y scripts de PowerShell para crear una máquina virtual de Azure con imágenes de la galería de máquinas virtuales de SQL Server."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/29/2017
ms.author: jroth
ms.openlocfilehash: 4b8cc80f2d1ed6f09ec917118dc9495d20394b94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>Aprovisionamiento de una máquina virtual de SQL mediante Azure PowerShell (Resource Manager)
> [!div class="op_single_selector"]
> * [Portal](virtual-machines-windows-portal-sql-server-provision.md)
> * [PowerShell](virtual-machines-windows-ps-sql-create.md)
>
>

## <a name="overview"></a>Información general
Este tutorial muestra cómo crear una máquina virtual de Azure mediante el modelo de implementación de **Azure Resource Manager** mediante los cmdlets de Azure PowerShell. En este tutorial, se creará una máquina virtual con una sola unidad de disco desde una imagen de la Galería de SQL. Se crearán nuevos proveedores para los recurso de almacenamiento, red y proceso que usará la máquina virtual. Si ya dispone de proveedores existentes para cualquiera de estos recursos, puede usarlos en su lugar.

Si necesita la versión clásica de este tema, consulte [Aprovisionamiento de una máquina virtual de SQL mediante Azure PowerShell (clásico)](../classic/ps-sql-create.md).

## <a name="prerequisites"></a>Requisitos previos
Para este tutorial, necesitará:

* una cuenta de Azure y una suscripción antes de empezar. Si no tiene una, suscríbase para [una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).
* [Azure PowerShell](/powershell/azure/overview), versión 1.4.0 (mínima) o posterior (en este tutorial se usa la versión 1.5.0).
  * Para recuperar la versión, escriba **Get-Module Azure -ListAvailable**.

## <a name="configure-your-subscription"></a>Configuración de su suscripción
Abra Windows PowerShell y establecer el acceso a su cuenta de Azure, para lo que debe ejecutar el siguiente cmdlet. Aparecerá una pantalla de inicio de sesión para especificar sus credenciales. Use el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el portal de Azure.

```PowerShell
Add-AzureRmAccount
```

Después de iniciar sesión correctamente, verá información en la pantalla que incluye el identificador y el nombre de la suscripción predeterminada. Esta es la suscripción en la que los recursos de este tutorial se crearán a menos que cambie a una suscripción diferente. Si tiene varias suscripciones, ejecute el siguiente cmdlet para devolver una lista de todas ellas:

```PowerShell
Get-AzureRmSubscription
```
Para cambiar a otra suscripción, ejecute el siguiente comando con el SubscriptionName de destino.

```PowerShell
Select-AzureRmSubscription -SubscriptionName YourTargetSubscriptionName
```

## <a name="define-image-variables"></a>Definición de variables de imagen
Para simplificar el uso y la comprensión de todo el script de este tutorial, comenzaremos por definir una serie de variables. Cambie los valores de los parámetros como considere oportuno, pero tenga en cuenta las restricciones relacionadas con longitudes de los nombres y los caracteres especiales al hacerlo.

### <a name="location-and-resource-group"></a>Ubicación y grupo de recursos
Utilice dos variables para definir la región de los datos y el grupo de recursos en el que va a crear los restantes recursos de la máquina virtual.

Modifíquelas como desee y luego ejecute los siguientes cmdlets para inicializar dichas variables.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm1"
```

### <a name="storage-properties"></a>Propiedades de almacenamiento
Utilice las siguientes variables para definir la cuenta de almacenamiento y el tipo de almacenamiento que va a usar la máquina virtual.

Modifíquelas como desee y luego ejecute el siguiente cmdlet para inicializarlas. Tenga en cuenta que en este ejemplo se usa [Almacenamiento premium](../../../storage/common/storage-premium-storage.md), que es el recomendado para cargas de trabajo de producción. Para más información sobre esta guía y para otras recomendaciones, consulte [Prácticas recomendadas para mejorar el rendimiento para SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-performance.md).

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Propiedades de red
Utilice las siguientes variables para definir la interfaz de red, el método de asignación de TCP/IP, el nombre de red virtual, el nombre de subred virtual, el intervalo de direcciones IP de la red virtual, el intervalo de direcciones IP de la subred y la etiqueta de nombre de dominio público que va a usar la red en la máquina virtual.

Modifíquelas como desee y luego ejecute el siguiente cmdlet para inicializarlas.

```PowerShell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = "sqlvm1"
```

### <a name="virtual-machine-properties"></a>Propiedades de máquina virtual
Utilice las siguientes variables para definir el nombre de la máquina virtual, el nombre del equipo, el tamaño de la máquina virtual y el nombre del disco del sistema operativo de la máquina virtual.

Modifíquelas como desee y luego ejecute el siguiente cmdlet para inicializarlas.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="image-properties"></a>Propiedades de la imagen
Utilice las siguientes variables para definir la imagen que se va a usar para la máquina virtual. En este ejemplo, se utiliza la imagen de la edición SQL Server 2016 Developer. La edición Developer ofrece licencias gratuitamente para desarrollo y pruebas, y solo se paga por el costo de ejecución de la máquina virtual.

Modifíquelas como desee y luego ejecute el siguiente cmdlet para inicializarlas.

```PowerShell
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2016-WS2016"
$Sku = "SQLDEV"
$Version = "latest"
```

Tenga en cuenta que con el comando Get-AzureRmVMImageOffer puede obtener una lista completa de las ofertas de imágenes de SQL Server:

```PowerShell
Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'
```

Por su parte, con el comando Get-AzureRmVMImageSku se pueden ver las SKU disponibles para una oferta. El comando siguiente muestra todas las SKU disponibles para la oferta **SQL2016SP1-WS2016**.

```PowerShell
Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer 'SQL2016SP1-WS2016' | Select Skus
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Con el modelo de implementación de Resource Manager, el primer objeto que se crea es el grupo de recursos. Usaremos el cmdlet [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) para crear un grupo de recursos de Azure y sus propios recursos, con el nombre y la ubicación del grupo de recursos definidos por las variables que inicializó anteriormente.

Ejecute el siguiente cmdlet para crear un nuevo grupo de recursos.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
La máquina virtual requiere recursos de almacenamiento tanto para el disco del sistema operativo como para los archivos de registro y de datos de SQL Server. Por motivos de simplicidad, crearemos un único disco para ambos. Posteriormente se pueden adjuntar discos adicionales mediante el cmdlet [Add-Azure Disk](/powershell/module/azure/add-azuredisk) con el fin de colocar los archivos de registro y de datos de SQL Server en discos dedicados. Usaremos el cmdlet [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) para crear una cuenta de almacenamiento estándar en el nuevo grupo de recursos y con el nombre de la cuenta de almacenamiento, el nombre de almacenamiento y la ubicación definidos mediante las variables que inicializó anteriormente.

Ejecute el siguiente cmdlet para crear una nueva cuenta de almacenamiento.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location
```

## <a name="create-network-resources"></a>Crear recursos de red
La máquina virtual requiere un número de recursos de red para la conectividad de red.

* Cada máquina virtual requiere una red virtual.
* Una red virtual debe tener al menos una subred definida.
* Una interfaz de red debe definirse con una dirección IP privada o pública.

### <a name="create-a-virtual-network-subnet-configuration"></a>Creación de una configuración de subred de una red virtual
Comenzaremos por crear una configuración de subred para la red virtual. Para este tutorial, crearemos una subred predeterminada mediante el cmdlet [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) . La configuración de subred de la red virtual se crea con el prefijo de nombre y dirección de subred definido mediante las variables que inicializó anteriormente.

> [!NOTE]
> Con este puede definir propiedades adicionales de la configuración de subred de la red virtual, pero eso está fuera del ámbito de este tutorial.

Ejecute el siguiente cmdlet para crear una configuración de subred virtual.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Crear una red virtual
A continuación, crearemos una red virtual, para lo que usaremos el cmdlet [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) . Dicha red virtual se creará en el nuevo grupo de recursos con el prefijo de nombre, ubicación y dirección definido con las variables que inicializó anteriormente y con la configuración de subred que definió en el paso anterior.

Ejecute el siguiente cmdlet para crear una red virtual.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Crear la dirección IP pública
Ahora que la red virtual está definida, es preciso configurar una dirección IP para poder conectarse a la máquina virtual. Para este tutorial, crearemos una dirección IP pública con direccionamiento IP dinámico, con el fin de admitir la conectividad a Internet. Usaremos el cmdlet [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) para crear la dirección IP pública en el grupo de recursos creado anteriormente y con el nombre, la ubicación, el método de asignación y la etiqueta de nombre de dominio de DNS definidos con las variables que inicializó anteriormente.

> [!NOTE]
> Con este cmdlet se pueden definir propiedades adicionales de la dirección IP pública, pero eso es algo que está fuera del ámbito de este tutorial inicial. También se puede crear una dirección privada o una dirección con una dirección estática, pero eso está también fuera del ámbito de este tutorial.

Ejecute el siguiente cmdlet para crear una dirección IP pública.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-interface"></a>Creación de la interfaz de red
Ya estamos listos para crear la interfaz de red que utilizará la máquina virtual. Usaremos el cmdlet [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) para crear la interfaz de red en el grupo de recursos creado anteriormente y con el nombre, la ubicación, la subred y la dirección IP pública definidos anteriormente.

Ejecute el siguiente cmdlet para crear la interfaz de red.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id
```

## <a name="configure-a-vm-object"></a>Configuración de un objeto de VM
Ahora que contamos con los recursos de almacenamiento y red definidos, estamos preparados para definir los recursos de proceso para la máquina virtual. En este tutorial, especificaremos el tamaño de la máquina virtual y diversas propiedades de sistema operativo, así como la interfaz de red que creamos anteriormente, definiremos el almacenamiento de blobs y, después, especificaremos el disco del sistema operativo.

### <a name="create-the-vm-object"></a>Creación del objeto de VM
Comenzamos por especificar el tamaño de la máquina virtual. Para este tutorial, vamos a especificar un DS13. Usaremos el cmdlet [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) para crear un objeto de máquina virtual configurable con el nombre y el tamaño definidos mediante las variables que inicializó anteriormente.

Ejecute el siguiente cmdlet para crear un objeto de máquina virtual.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Creación de un objeto de credencial que contenga el nombre y la contraseña de las credenciales de administrador local
Para poder establecer las propiedades del sistema operativo de la máquina virtual, es preciso proporcionar las credenciales de la cuenta de administrador local en forma de cadena segura. Para ello, utilizaremos el cmdlet [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) .

Ejecute el siguiente cmdlet y, en la ventana de solicitud de credenciales de Windows PowerShell, escriba el nombre y la contraseña que se usarán para la cuenta de administrador local en la máquina virtual de Windows.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Establecimiento de las propiedades del sistema operativo de la máquina virtual
Ya estamos listos para establecer las propiedades del sistema operativo de la máquina virtual. Usaremos el cmdlet [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) para establecer el tipo de sistema operativo como Windows, requerir que se instale el [agente de la máquina virtual](../classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json), especificar que el cmdlet habilite la actualización automática y establecer el nombre de la máquina virtual, el nombre del equipo y la credencial mediante las variables que inicializó anteriormente.

Ejecute el siguiente cmdlet para establecer las propiedades de sistema operativo de la máquina virtual.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Adición de la interfaz de red a la máquina virtual
A continuación, agregaremos a la máquina virtual la interfaz de red que hemos creado anteriormente. Usaremos el cmdlet [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) para agregar la interfaz de red mediante la variable de la interfaz de red que definió anteriormente.

Ejecute el siguiente cmdlet para establecer la interfaz de red de la máquina virtual.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Establecimiento la ubicación de Almacenamiento de blobs en el disco que va a usar la máquina virtual
A continuación, estableceremos la ubicación de Almacenamiento de blobs en el disco que va a usar la máquina virtual mediante las variables que definió anteriormente.

Ejecute el siguiente cmdlet para establecer la ubicación de Almacenamiento de blobs.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Establecimiento de las propiedades del disco del sistema operativo de la máquina virtual
A continuación, estableceremos las propiedades del disco del sistema operativo de la máquina virtual Usaremos el cmdlet [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) para especificar que el sistema operativo de la máquina virtual procederá de una imagen, para establecer el almacenamiento en caché en solo lectura (porque SQL Server está instalado en el mismo disco) y para definir el nombre de la máquina virtual y el disco de sistema operativo que se definen mediante las variables que definimos anteriormente.

Ejecute el siguiente cmdlet para establecer las propiedades del disco del sistema operativo de la máquina virtual.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Especificación de la imagen de la plataforma de la máquina virtual.
El último paso de la configuración es especificar la imagen de la plataforma de la máquina virtual. En este tutorial se usa la última imagen de SQL Server 2016 CTP. Usaremos el cmdlet [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) para usar esta imagen, tal como definen las variables que se definió anteriormente.

Ejecute el siguiente cmdlet para especificar la imagen de la plataforma de la máquina virtual.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Creación de la máquina virtual con SQL
Ahora que ha terminado los pasos de la configuración, está listo para crear la máquina virtual. Usaremos el cmdlet [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) para crear la máquina virtual mediante las variables que hemos definido.

Ejecute el siguiente cmdlet para crear una máquina virtual.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

La máquina virtual se ha creado. Observe que se crea una cuenta de almacenamiento estándar para el diagnóstico del arranque, porque la cuenta de almacenamiento especificada para el disco de la máquina virtual es una cuenta de Almacenamiento premium.

La máquina ya aparece en el Portal de Azure y ahí puede ver [su dirección IP pública y su nombre de dominio completo](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="example-script"></a>Script de ejemplo
El siguiente script contiene el script de PowerShell completo de este tutorial. Se da por hecho que ya tiene configurada la suscripción de Azure para usarla con los comandos **Add-AzureRmAccount** y **Select-AzureRmSubscription**.

```PowerShell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm1"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = "sqlvm1"

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2016-WS2016"
$Sku = "Enterprise"
$Version = "latest"

# Resource Group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

## <a name="next-steps"></a>Pasos siguientes
Después de crear la máquina virtual, está listo para conectarse a ella mediante RDP y para configurar la conectividad. Para más información, consulte [Conexión a una máquina virtual de SQL Server en Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).
