---
title: "Cifrado de discos en una máquina virtual de Windows en Azure | Microsoft Docs"
description: "Cómo cifrar discos virtuales en una máquina virtual de Windows para mejorar la seguridad Azure PowerShell"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/10/2017
ms.author: iainfou
ms.openlocfilehash: 98b42b252a601af090579e3939f3c7ab91c3803b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-encrypt-virtual-disks-on-a-windows-vm"></a>Cómo cifrar discos virtuales en una máquina virtual de Windows
Para mejorar la seguridad y el cumplimiento de las máquinas virtuales, se pueden cifrar los discos virtuales en Azure. Los discos se cifran mediante claves criptográficas que están protegidas en Azure Key Vault. Estas claves criptográficas se pueden controlar y se puede auditar su uso. En este artículo se detalla cómo cifrar los discos virtuales en una máquina virtual de Windows con Azure PowerShell. También se puede [cifrar una máquina virtual de Linux mediante la CLI 2.0 de Azure](../linux/encrypt-disks.md).

## <a name="overview-of-disk-encryption"></a>Introducción al cifrado de discos
Los discos virtuales en máquinas virtuales de Windows se cifran en reposo mediante BitLocker. El cifrado de los discos virtuales en Azure no conlleva ningún cargo. Las claves criptográficas se almacenan en Azure Key Vault con protección de software, o puede importar o generar las claves en módulos de seguridad de hardware (HSM) certificados conforme a las normas FIPS 140-2 de nivel 2. Las claves criptográficas se usan para cifrar y descifrar los discos virtuales conectados a la máquina virtual. Estas claves criptográficas se pueden controlar y se puede auditar su uso. Como las máquinas virtuales se encienden y se apagan, una entidad de servicio de Azure Active Directory proporciona un mecanismos seguro para la emisión de estas claves criptográficas.

El proceso para cifrar una máquina virtual es el siguiente:

1. Cree una clave criptográfica en Azure Key Vault.
2. Configure la clave criptográfica para poder utilizarla para el cifrado de discos.
3. Para leer la clave criptográfica de Azure Key Vault, cree una entidad de servicio de Azure Active Directory con los permisos adecuados.
4. Emita el comando para cifrar los discos virtuales y especifique la entidad de servicio de Azure Active Directory y la clave criptográfica adecuada que se deberá usar.
5. La entidad de servicio de Azure Active Directory solicita la clave criptográfica necesaria a Azure Key Vault.
6. Los discos virtuales se cifran con la clave criptográfica proporcionada.

## <a name="encryption-process"></a>Proceso de cifrado
El cifrado de discos utiliza los siguientes componentes adicionales:

* **Azure Key Vault**: se usa para proteger las claves criptográficas y los secretos usados para el proceso de cifrado y descifrado de discos. 
  * Si ya existe un almacén de Azure Key Vault, puede utilizarlo. No es necesario dedicar un almacén de Key Vault para el cifrado de discos.
  * Para separar los límites administrativos y la visibilidad de las claves, puede crear un almacén de Key Vault dedicado.
* **Azure Active Directory**: controla el intercambio seguro de las claves cifradas necesarias y la autenticación para las acciones solicitadas. 
  * Normalmente, puede usar un almacén existente de Azure Active Directory para hospedar la aplicación.
  * La entidad de servicio proporciona un mecanismo seguro para solicitar y recibir las claves criptográficas correspondientes. No está desarrollando una aplicación real que se integrará con Azure Active Directory.

## <a name="requirements-and-limitations"></a>Requisitos y limitaciones
Requisitos y escenarios admitidos para el cifrado de discos:

* Habilitación del cifrado en nuevas máquinas virtuales de Windows desde imágenes de Azure Marketplace o una imagen de disco duro virtual personalizada.
* Habilitación del cifrado en máquinas virtuales de Windows existentes en Azure.
* Habilitación del cifrado en máquinas virtuales de Windows configuradas mediante Espacios de almacenamiento.
* Deshabilitación del cifrado en las unidades de datos y del sistema operativo en máquinas virtuales de Windows.
* Todos los recursos (por ejemplo: almacén de Key Vault, cuenta de almacenamiento, máquina virtual, etc.) deben pertenecer a la misma región y suscripción de Azure.
* Nivel estándar de máquinas virtuales, como las máquinas virtuales de la serie A, D, DS, G y GS.

El cifrado del disco no se admite actualmente en los siguientes escenarios:

* Máquina s virtuales de nivel básico
* Máquinas virtuales creadas con el modelo de implementación clásica.
* Actualización de las claves criptográficas en una máquina virtual ya cifrada.
* Integración con el Servicio de administración de claves local.

## <a name="create-azure-key-vault-and-keys"></a>Creación de Azure Key Vault y claves
Antes de empezar, asegúrese de tener instalada la versión más reciente del módulo de Azure PowerShell. Para más información, vea [Instalación y configuración de Azure PowerShell](/powershell/azure/overview). En todos los ejemplos de comandos, reemplace todos los parámetros de ejemplo por sus propios nombres, ubicaciones y valores de clave. Los siguientes ejemplos usan una convención de *myResourceGroup*, *myKeyVault*, *myVM*, etc.

El primer paso es crear un almacén de Azure Key Vault para almacenar las claves criptográficas. Azure Key Vault puede almacenar claves, secretos o contraseñas que permiten su implementación segura en las aplicaciones y los servicios. Para el cifrado de discos virtuales, se crea un Key Vault para almacenar una clave criptográfica que se usa para cifrar o descifrar los discos virtuales. 

Habilite el proveedor Azure Key Vault dentro de la suscripción de Azure con [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) y, a continuación, cree un grupo de recursos con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación del *este de EE. UU.*:

```powershell
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

El almacén de Azure Key Vault que contiene las claves criptográficas y los recursos de proceso asociados, como el almacenamiento y la propia máquina virtual, debe residir en la misma región. Cree una instancia de Azure Key Vault con [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) y habilite Key Vault para usarlo con el cifrado de discos. Especifique un nombre de Key Vault único para *keyVaultName* de la siguiente manera:

```powershell
$keyVaultName = "myUniqueKeyVaultName"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Puede almacenar las claves criptográficas mediante software o protección del modelo de seguridad de hardware (HSM). Para usar HSM se necesita un almacén premium de Key Vault. La creación de un almacén premium de Key Vault conlleva un coste, frente al almacén estándar de Key Vault, que almacena las claves protegidas por software. Para crear un Key Vault premium, en el paso anterior agregue los parámetros *-Sku "Premium"*. En el ejemplo siguiente se usa claves protegidas por software ya que hemos creado un almacén de Key Vault estándar. 

En ambos modelos de protección, la plataforma Windows Azure debe tener acceso para solicitar las claves criptográficas cuando la máquina virtual arranca para descifrar los discos virtuales. Cree una clave criptográfica en la instancia de Key Vault con [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey). En el ejemplo siguiente se crea una clave llamada *myKey*:

```powershell
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```


## <a name="create-the-azure-active-directory-service-principal"></a>Creación de la entidad de servicio de Azure Active Directory
Cuando los discos virtuales se cifran o descifran, se especifica una cuenta para controlar la autenticación y el intercambio de claves criptográficas desde Key Vault. Esta cuenta, una entidad de servicio de Azure Active Directory, permite que la plataforma de Azure solicite las claves criptográficas correspondientes en nombre de la máquina virtual. Su suscripción dispone de una instancia de Azure Active Directory predeterminada, aunque muchas organizaciones tienen directorios de Azure Active Directory dedicados.

Cree una entidad de seguridad de servicio en Azure Active Directory con [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal). Para especificar una contraseña segura, siga las [Restricciones y directivas de contraseñas en Azure Active Directory](../../active-directory/active-directory-passwords-policy.md):

```powershell
$appName = "My App"
$securePassword = "P@ssword!"
$app = New-AzureRmADApplication -DisplayName $appName `
    -HomePage "https://myapp.contoso.com" `
    -IdentifierUris "https://contoso.com/myapp" `
    -Password $securePassword
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
```

Para cifrar o descifrar los discos virtuales correctamente, debe establecer los permisos de la clave criptográfica que se almacena en Key Vault para que permitan que la entidad de servicio de Azure Active Directory lea las claves. Establezca los permisos en Key Vault con [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy):

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyvaultName `
    -ServicePrincipalName $app.ApplicationId `
    -PermissionsToKeys "WrapKey" `
    -PermissionsToSecrets "Set"
```


## <a name="create-virtual-machine"></a>Create virtual machine
Para probar el proceso de cifrado, se va a crear una máquina virtual. En el ejemplo siguiente se crea una máquina virtual llamada *myVM* mediante una imagen de *Windows Server 2016 Datacenter*:

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName `
    -Location $location `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "mypublicdns$(Get-Random)"

$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName `
    -Location $location `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface -Name myNic `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id `
    -NetworkSecurityGroupId $nsg.Id

$cred = Get-Credential

$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vmConfig
```


## <a name="encrypt-virtual-machine"></a>Cifrado de máquina virtual
Para cifrar los discos virtuales, reúna todos los componentes anteriores:

1. Especifique la entidad de servicio de Azure Active Directory y la contraseña.
2. Especifique el almacén de Key Vault donde se almacenarán los metadatos de los discos cifrados.
3. Especifique las claves criptográficas que se utilizarán para el cifrado y descifrado.
4. Especifique si desea cifrar el disco del sistema operativo, los discos de datos o todos.

Cifre la VM con [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) mediante la clave de Azure Key Vault y las credenciales de entidad de seguridad de servicio de Azure Active Directory. En el ejemplo siguiente se recupera toda la información de clave y después se cifra la máquina virtual denominada *myVM*:

```powershell
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName $vmName `
    -AadClientID $app.ApplicationId `
    -AadClientSecret $securePassword `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Acepte el mensaje para continuar con el cifrado de la máquina virtual. La VM se reinicia durante el proceso. Una vez que finalice el proceso de cifrado y se reinicie la máquina virtual, revise el estado del cifrado con [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus):

```powershell
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName $vmName
```

La salida es similar a la del ejemplo siguiente:

```powershell
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre cómo administrar Azure Key Vault, vea [Configuración de Key Vault para máquinas virtuales en Azure Resource Manager](key-vault-setup.md).
* Para obtener más información acerca del cifrado de discos, por ejemplo, cómo preparar una máquina virtual personalizada cifrada para cargar en Azure, consulte [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
