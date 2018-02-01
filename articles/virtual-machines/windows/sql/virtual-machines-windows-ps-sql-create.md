---
title: "Cómo crear VM con SQL Server con Azure PowerShell | Microsoft Docs"
description: "Ofrece pasos y comandos de PowerShell para crear una VM de Azure con imágenes de la galería de máquinas virtuales de SQL Server."
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
ms.date: 11/29/2017
ms.author: jroth
ms.openlocfilehash: e6d1f36d998ac8726e3a74b31772a5dd5a24bd58
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2018
---
# <a name="how-to-create-sql-server-virtual-machines-with-azure-powershell"></a>Cómo crear máquinas virtuales de SQL Server con Azure PowerShell

En esta guía se explican las opciones para crear VM de Windows SQL Server con Azure PowerShell. Para obtener un ejemplo de Azure PowerShell simplificado con varios valores predeterminados, consulte el [inicio rápido de Azure PowerShell de VM de SQL](quickstart-sql-vm-create-powershell.md).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Para realizar los pasos de esta guía, se requiere la versión 3.6 del módulo Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

## <a name="configure-your-subscription"></a>Configuración de su suscripción

1. Abra PowerShell y establezca el acceso a su cuenta de Azure mediante la ejecución del comando **Add-AzureRmAccount**.

   ```PowerShell
   Add-AzureRmAccount
   ```

1. Debería ver una pantalla de inicio de sesión para escribir sus credenciales. Use el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el portal de Azure.

## <a name="define-image-variables"></a>Definición de variables de imagen
Para simplificar las creaciones de scripts y la reutilización, empiece por definir una serie de variables. Cambie los valores de los parámetros como considere oportuno, pero tenga en cuenta las restricciones relacionadas con longitudes de los nombres y los caracteres especiales al hacerlo.

### <a name="location-and-resource-group"></a>Ubicación y grupo de recursos
Utilice dos variables para definir la región de los datos y el grupo de recursos en el que crear los restantes recursos de la máquina virtual.

Modifíquelas como desee y luego ejecute los siguientes cmdlets para inicializar dichas variables.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Propiedades de almacenamiento
Utilice las siguientes variables para definir la cuenta de almacenamiento y el tipo de almacenamiento que va a usar la máquina virtual.

Modifíquelas como desee y luego ejecute el siguiente cmdlet para inicializarlas. Tenga en cuenta que en este ejemplo se usa [Premium Storage](../premium-storage.md), que es el recomendado para cargas de trabajo de producción.

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Propiedades de red
Utilice las siguientes variables para definir la interfaz de red, el método de asignación de TCP/IP, el nombre de red virtual, el nombre de subred virtual, el intervalo de direcciones IP de la red virtual, el intervalo de direcciones IP de la subred y la etiqueta de nombre de dominio público que va a usar la red en la máquina virtual.

Modifíquelas como desee y luego ejecute el siguiente cmdlet para inicializarlas.

```PowerShell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
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

### <a name="choose-a-sql-server-image"></a>Elegir una imagen de SQL Server
Utilice las siguientes variables para definir la imagen de SQL Server que se va a usar para la máquina virtual.

1. Primero, incluya una lista de todas las ofertas de imágenes de SQL Server con el comando **Get-AzureRmVMImageOffer**:

   ```PowerShell
   Get-AzureRmVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Para este tutorial, use las siguientes variables para especificar SQL Server 2017 en Windows Server 2016.

   ```PowerShell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Después, incluya una lista de las ediciones disponibles para su oferta.

   ```PowerShell
   Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Para este tutorial, utilice SQL Server 2017 Developer Edition (**SQLDEV**). La edición Developer ofrece licencias gratuitamente para desarrollo y pruebas, y solo se paga por el costo de ejecución de la máquina virtual.

   ```PowerShell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Con el modelo de implementación de Resource Manager, el primer objeto que se crea es el grupo de recursos. Use el cmdlet [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) para crear un grupo de recursos de Azure y sus propios recursos, con el nombre y la ubicación del grupo de recursos definidos por las variables que inicializó anteriormente.

Ejecute el siguiente cmdlet para crear un nuevo grupo de recursos.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
La máquina virtual requiere recursos de almacenamiento tanto para el disco del sistema operativo como para los archivos de registro y de datos de SQL Server. Por motivos de simplicidad, crearemos un único disco para ambos. Posteriormente se pueden adjuntar discos adicionales mediante el cmdlet [Add-Azure Disk](/powershell/module/azure/add-azuredisk) con el fin de colocar los archivos de registro y de datos de SQL Server en discos dedicados. Use el cmdlet [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) para crear una cuenta de almacenamiento estándar en el nuevo grupo de recursos y con el nombre de la cuenta de almacenamiento, el nombre de SKU de almacenamiento y la ubicación definidos mediante las variables que inicializó anteriormente.

Ejecute el siguiente cmdlet para crear una nueva cuenta de almacenamiento.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> La creación de la cuenta de almacenamiento puede tardar unos minutos.

## <a name="create-network-resources"></a>Crear recursos de red
La máquina virtual requiere un número de recursos de red para la conectividad de red.

* Cada máquina virtual requiere una red virtual.
* Una red virtual debe tener al menos una subred definida.
* Una interfaz de red debe definirse con una dirección IP privada o pública.

### <a name="create-a-virtual-network-subnet-configuration"></a>Creación de una configuración de subred de una red virtual
Comience con la creación de una configuración de subred para la red virtual. Para este tutorial, creamos una subred predeterminada mediante el cmdlet [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). La configuración de subred de la red virtual se crea con el prefijo de nombre y dirección de subred definido mediante las variables que inicializó anteriormente.

> [!NOTE]
> Con este puede definir propiedades adicionales de la configuración de subred de la red virtual, pero eso está fuera del ámbito de este tutorial.

Ejecute el siguiente cmdlet para crear una configuración de subred virtual.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Crear una red virtual
A continuación, cree una red virtual con el cmdlet [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Cree dicha red virtual en el nuevo grupo de recursos con el prefijo de nombre, ubicación y dirección definido con las variables que inicializó anteriormente y con la configuración de subred que definió en el paso anterior.

Ejecute el siguiente cmdlet para crear una red virtual.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Crear la dirección IP pública
Ahora que la red virtual está definida, es preciso configurar una dirección IP para poder conectarse a la máquina virtual. Para este tutorial, creamos una dirección IP pública con direccionamiento IP dinámico, con el fin de admitir la conectividad a Internet. Use el cmdlet [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) para crear la dirección IP pública en el grupo de recursos creado anteriormente y con el nombre, la ubicación, el método de asignación y la etiqueta de nombre de dominio de DNS definidos con las variables que inicializó anteriormente.

> [!NOTE]
> Con este cmdlet se pueden definir propiedades adicionales de la dirección IP pública, pero eso es algo que está fuera del ámbito de este tutorial inicial. También se puede crear una dirección privada o una dirección con una dirección estática, pero eso está también fuera del ámbito de este tutorial.

Ejecute el siguiente cmdlet para crear una dirección IP pública.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Creación del grupo de seguridad de red
Para proteger el tráfico de la VM y SQL Server, cree un grupo de seguridad de red.

1. Primero, cree una regla de grupo de seguridad de red para RDP para permitir conexiones de escritorio remoto.

   ```PowerShell
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Configure una regla del grupo de seguridad de red que permita el tráfico en el puerto TCP 1433. De este modo, podrá realizar conexiones a SQL Server a través de Internet.

   ```PowerShell
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. A continuación, cree el grupo de seguridad de red.

   ```PowerShell
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Creación de la interfaz de red
Ya estamos listos para crear la interfaz de red que utilizará la máquina virtual. Llamamos al cmdlet [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) para crear la interfaz de red en el grupo de recursos creado anteriormente y con el nombre, la ubicación, la subred y la dirección IP pública definidos anteriormente.

Ejecute el siguiente cmdlet para crear la interfaz de red.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Configuración de un objeto de VM
Ahora que contamos con los recursos de almacenamiento y red definidos, estamos preparados para definir los recursos de proceso para la máquina virtual. En este tutorial, se especifica el tamaño de la máquina virtual y diversas propiedades de sistema operativo, así como la interfaz de red que creamos anteriormente, se define el almacenamiento de blobs y, después, se especifica el disco del sistema operativo.

### <a name="create-the-vm-object"></a>Creación del objeto de VM
Comience por especificar el tamaño de la máquina virtual. Para este tutorial, vamos a especificar un DS13. Llamamos al cmdlet [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) para crear un objeto de máquina virtual configurable con el nombre y el tamaño definidos mediante las variables que inicializó anteriormente.

Ejecute el siguiente cmdlet para crear un objeto de máquina virtual.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Creación de un objeto de credencial que contenga el nombre y la contraseña de las credenciales de administrador local
Para poder establecer las propiedades del sistema operativo de la máquina virtual, es preciso proporcionar las credenciales de la cuenta de administrador local en forma de cadena segura. Para ello, use el cmdlet [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx).

Ejecute el siguiente cmdlet y, en la ventana de solicitud de credenciales de PowerShell, escriba el nombre y la contraseña que se usarán para la cuenta de administrador local en la máquina virtual.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Establecimiento de las propiedades del sistema operativo de la máquina virtual
Ahora estamos listos para establecer las propiedades del sistema operativo de la máquina virtual con el cmdlet [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) para establecer el tipo de sistema operativo como Windows, requerir que se instale el [agente de la máquina virtual](../agent-user-guide.md), especificar que el cmdlet habilite la actualización automática y establecer el nombre de la máquina virtual, el nombre del equipo y la credencial mediante las variables que inicializó anteriormente.

Ejecute el siguiente cmdlet para establecer las propiedades de sistema operativo de la máquina virtual.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Adición de la interfaz de red a la máquina virtual
A continuación, agregamos a la máquina virtual la interfaz de red que hemos creado anteriormente. Llamamos al cmdlet [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) para agregar la interfaz de red mediante la variable de la interfaz de red que definió anteriormente.

Ejecute el siguiente cmdlet para establecer la interfaz de red de la máquina virtual.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Establecimiento la ubicación de Almacenamiento de blobs en el disco que va a usar la máquina virtual
A continuación, establezca la ubicación de almacenamiento de blobs en el disco que va a usar la máquina virtual mediante las variables que definió anteriormente.

Ejecute el siguiente cmdlet para establecer la ubicación de Almacenamiento de blobs.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Establecimiento de las propiedades del disco del sistema operativo de la máquina virtual
A continuación, establezca las propiedades del disco del sistema operativo de la máquina virtual. Usamos el cmdlet [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) para especificar que el sistema operativo de la máquina virtual procederá de una imagen, para establecer el almacenamiento en caché en solo lectura (porque SQL Server está instalado en el mismo disco) y para definir el nombre de la máquina virtual y el disco de sistema operativo que se definen mediante las variables que definimos anteriormente.

Ejecute el siguiente cmdlet para establecer las propiedades del disco del sistema operativo de la máquina virtual.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Especificación de la imagen de la plataforma de la máquina virtual.
El último paso de la configuración es especificar la imagen de la plataforma de la máquina virtual. En este tutorial se usa la última imagen de SQL Server 2016 CTP. Usamos el cmdlet [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) para usar esta imagen, tal como definen las variables que definió anteriormente.

Ejecute el siguiente cmdlet para especificar la imagen de la plataforma de la máquina virtual.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Creación de la máquina virtual con SQL
Ahora que ha terminado los pasos de la configuración, está listo para crear la máquina virtual. Usamos el cmdlet [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) para crear la máquina virtual mediante las variables que hemos definido.

Ejecute el siguiente cmdlet para crear una máquina virtual.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

La máquina virtual se ha creado.

> [!NOTE]
> Puede ignorar el error acerca de los diagnósticos del arranque. Se crea una cuenta de almacenamiento estándar para el diagnóstico del arranque porque la cuenta de almacenamiento especificada para el disco de la máquina virtual es una cuenta de almacenamiento premium.

## <a name="install-the-sql-iaas-agent"></a>Instalación del Agente de IaaS de SQL
Las máquinas virtuales de SQL Server son compatibles con características de administración automatizada con la [extensión del Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Para instalar al agente en la nueva VM, ejecute el comando siguiente después de crearlo.

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remove-a-test-vm"></a>Eliminación de una VM de prueba

Si no necesita que la VM se ejecute continuamente, puede detenerla cuando no se esté usando y así evitar cargos innecesarios. El siguiente comando detiene la VM, pero la deja disponible para usarla en el futuro.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

También puede eliminar de forma definitiva todos los recursos asociados a la máquina virtual con el comando **Remove-AzureRmResourceGroup**. Como esta acción también elimina la máquina virtual definitivamente, use este comando con cuidado.

## <a name="example-script"></a>Script de ejemplo
El siguiente script contiene el script de PowerShell completo de este tutorial. Se da por hecho que ya tiene configurada la suscripción de Azure para usarla con los comandos **Add-AzureRmAccount** y **Select-AzureRmSubscription**.

```PowerShell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension
Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
```

## <a name="next-steps"></a>pasos siguientes
Después de crear la máquina virtual, puede:

- Conectarse a la máquina virtual mediante el escritorio remoto (RDP).
- Configurar las opciones de SQL Server en el portal de la VM, incluidas las opciones siguientes:
   - [Configuración de almacenamiento](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Tareas de administración automatizadas](virtual-machines-windows-sql-server-agent-extension.md)
- [Configuración de la conectividad](virtual-machines-windows-sql-connect.md)
- Conecte clientes y aplicaciones a la nueva instancia de SQL Server.

