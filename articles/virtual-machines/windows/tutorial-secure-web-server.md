---
title: IIS Seguro con certificados SSL en Azure | Microsoft Docs
description: "Aprenda a proteger el servidor web IIS con certificados SSL en una máquina virtual Windows en Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6567853e9ef3cad63595dc0afe7a793bdc5d972c
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="secure-iis-web-server-with-ssl-certificates-on-a-windows-virtual-machine-in-azure"></a>Protección de un servidor web IIS con certificados SSL en una máquina virtual de Windows en Azure
Para proteger los servidores web, se puede utilizar un certificado Capa de sockets seguros (SSL) para cifrar el tráfico web. Estos certificados SSL pueden almacenarse en Azure Key Vault y permiten implementaciones seguras de certificados en máquinas virtuales Windows en Azure. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una instancia de Azure Key Vault
> * Generar o cargar un certificado en Key Vault
> * Creación de una máquina virtual e instalación del servidor web IIS
> * Inserción del certificado en la máquina virtual y configuración de IIS con un enlace SSL

Para realizar este tutorial es necesaria la versión 3.6 del módulo de Azure PowerShell, o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="overview"></a>Información general
Azure Key Vault protege claves y secretos criptográficos, como certificados y contraseñas. Key Vault ayuda a agilizar el proceso de administración de certificados y le permite mantener el control de las claves que acceden a ellos. Puede crear un certificado autofirmado en Key Vault o cargar un certificado de confianza existente que ya posea.

En lugar de usar una imagen de máquina virtual personalizada que incluya los certificados preparados, inserta los certificados en una máquina virtual en ejecución. Este proceso garantiza que los certificados más actualizados se instalan en un servidor web durante la implementación. Si renueva o reemplaza un certificado, no tiene también que crear una nueva imagen de máquina virtual personalizada. Los certificados más recientes se insertan automáticamente a medida que se crean máquinas virtuales adicionales. Durante el proceso completo, el certificado nunca deja la plataforma de Azure ni se expone en un script, historial de la línea de comandos o una plantilla.


## <a name="create-an-azure-key-vault"></a>Crear una instancia de Azure Key Vault
Para poder crear una instancia de Key Vault y certificados, cree un grupo de recursos con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroupSecureWeb* en la ubicación *Este de EE. UU.*:

```powershell
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Después, cree una instancia de Key Vault con [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Cada instancia de Key Vault requiere un nombre único, que debe estar todo en minúsculas. Reemplace `<mykeyvault>` en el siguiente ejemplo por su propio nombre único de Key Vault:

```powershell
$keyvaultName="<mykeyvault>"
New-AzureRmKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Generación de un certificado y almacenamiento en Key Vault
Para usarlo en producción, debe importar un certificado válido firmado por un proveedor de confianza con [Import-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate). En este tutorial, el ejemplo siguiente muestra cómo puede generar un certificado autofirmado con [Add-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificate) que usa la directiva de certificado predeterminada de [New-AzureKeyVaultCertificatePolicy](/powershell/module/azurerm.keyvault/new-azurekeyvaultcertificatepolicy). 

```powershell
$policy = New-AzureKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzureKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>de una máquina virtual
Establezca un nombre de usuario de administrador y una contraseña para la máquina virtual con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Ahora puede crear la máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). En el ejemplo siguiente se crean los componentes de red virtual necesarios, la configuración del sistema operativo y, después, se crea una máquina virtual denominada *myVM*:

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name "myVnet" `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRuleRDP"  `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

# Create an inbound network security group rule for port 443
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRuleWWW"  `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority 1001 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443 `
    -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $nsgRuleRDP,$nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name "myNic" `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2" | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName "myVM" -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2016-Datacenter" -Version "latest" | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

# Use the Custom Script Extension to install IIS
Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

La máquina virtual tarda unos minutos en crearse. El último paso usa la extensión Script personalizado de Azure para instalar el servidor web IIS con [Set-AzureRmVmExtension](/powershell/module/azurerm.compute/set-azurermvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Incorporación de un certificado a la máquina virtual desde Key Vault
Para agregar el certificado desde Key Vault a una máquina virtual, obtenga el identificador de su certificado con [Get-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret). Agregue el certificado a la máquina virtual con [Add-AzureRmVMSecret](/powershell/module/azurerm.compute/add-azurermvmsecret):

```powershell
$certURL=(Get-AzureKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzureRMVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzureRmKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Configuración de IIS para utilizar el certificado
Use la extensión Script personalizado de nuevo con [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) para actualizar la configuración de IIS. Esta actualización aplica el certificado insertado desde Key Vault a IIS y configura el enlace web:

```powershell
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Prueba de la aplicación web segura
Obtenga la dirección IP pública de la máquina virtual con [Get-AzureRmPublicIPAddress](/powershell/resourcemanager/azurerm.network/get-azurermpublicipaddress). En el ejemplo siguiente se obtiene la dirección IP de `myPublicIP` que se ha creado anteriormente:

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIP" | select "IpAddress"
```

Ahora puede abrir un explorador web y escribir `https://<myPublicIP>` en la barra de direcciones. Para aceptar la advertencia de seguridad si usó un certificado autofirmado, seleccione **Detalles** y, a continuación, **Acceder a la página web**:

![Aceptar la advertencia de seguridad del explorador web](./media/tutorial-secure-web-server/browser-warning.png)

El sitio web IIS protegido se muestra ahora como en el ejemplo siguiente:

![Visualización del sitio IIS seguro en funcionamiento](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, protegió un servidor web IIS con un certificado SSL almacenado en Azure Key Vault. Ha aprendido a:

> [!div class="checklist"]
> * Crear una instancia de Azure Key Vault
> * Generar o cargar un certificado en Key Vault
> * Creación de una máquina virtual e instalación del servidor web IIS
> * Inserción del certificado en la máquina virtual y configuración de IIS con un enlace SSL

Siga este vínculo para ver ejemplos de scripts de máquina virtual creados previamente.

> [!div class="nextstepaction"]
> [Ejemplos de scripts de máquina virtual Windows](./powershell-samples.md)