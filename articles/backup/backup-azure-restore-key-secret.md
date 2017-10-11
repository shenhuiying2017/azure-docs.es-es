---
title: "Restauración de la clave y el secreto de Key Vault para máquinas virtuales cifradas mediante Azure Backup | Microsoft Docs"
description: Aprenda a restaurar la clave y el secreto de Key Vault en Azure Backup con PowerShell
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 45214083-d5fc-4eb3-a367-0239dc59e0f6
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2db3449187d655248b13198b268841052570626
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Restauración de la clave y el secreto de Key Vault para máquinas virtuales cifradas mediante Azure Backup
En este artículo se explica cómo usar Azure Backup para restaurar máquinas virtuales de Azure cifradas, si su secreto y su clave no existen en el almacén de claves. Estos pasos también se pueden usar si desea mantener una copia independiente de la clave (clave de cifrado de Key Vault) y el secreto (clave de cifrado de BitLocker) para la máquina virtual restaurada.

## <a name="prerequisites"></a>Requisitos previos
* **Realizar una copia de seguridad de las máquinas virtuales cifradas**: se realizó una copia de seguridad de las máquinas virtuales cifradas de Azure mediante Azure Backup. Consulte el artículo [Implementación y administración de copias de seguridad para las máquinas virtuales implementadas según el modelo de Resource Manager mediante PowerShell](backup-azure-vms-automation.md) para más información acerca de cómo hacer una copia de seguridad cifrada de las máquinas virtuales de Azure.
* **Configurar Azure Key Vault**: asegúrese de que ya exista el almacén de claves donde deben restaurarse las claves y los secretos. Consulte el artículo [Introducción a Azure Key Vault](../key-vault/key-vault-get-started.md) para más información sobre la administración de almacenes de claves.
* **Restaurar disco**: asegúrese de que ha desencadenado el trabajo de restauración para restaurar los discos de la máquina virtual cifrada con los [pasos de PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm). Esto se debe a que este trabajo genera un archivo JSON en la cuenta de almacenamiento que contiene las claves y secretos para la máquina virtual cifrada que se va a restaurar.

## <a name="get-key-and-secret-from-azure-backup"></a>Obtención de la clave y el secreto en Azure Backup

> [!NOTE]
> Una vez que se haya restaurado el disco de la máquina virtual cifrada, asegúrese de que:
> 1. $details se rellena con los detalles del trabajo de restauración de disco, como se mencionó en los [pasos de PowerShell en la sección para restaurar los discos](backup-azure-vms-automation.md#restore-an-azure-vm)
> 2. La máquina virtual debe crearse desde los discos restaurados solo **después de que la clave y el secreto se restauren en el almacén de claves**.
>
>

Realice una consulta destinada a las propiedades de los discos restaurados para obtener los detalles del trabajo.

```
PS C:\> $properties = $details.properties
PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
PS C:\> $containerName = $properties["Config Blob Container Name"]
PS C:\> $encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Establezca el contexto de almacenamiento de Azure y restaure el archivo de configuración JSON que contenga los detalles del secreto y la clave para la máquina virtual cifrada.

```
PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
PS C:\> $destination_path = 'C:\vmencryption_config.json'
PS C:\> Get-AzureStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
PS C:\> $encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Restauración de la clave
Una vez que el archivo JSON se genere en la ruta de acceso de destino que se mencionó anteriormente, genere el archivo de blob de clave a partir de JSON y úselo para restaurar el cmdlet de la clave y colocar la clave (KEK) nuevo en el almacén de claves.

```
PS C:\> $keyDestination = 'C:\keyDetails.blob'
PS C:\> [io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Restauración del secreto
Use el archivo JSON generado anteriormente para obtener el valor y nombre del secreto, y páselos para establecer el cmdlet del secreto para poner de nuevo el secreto (BEK) en el almacén de claves. **Use estos cmdlets si la máquina virtual está cifrada mediante BEK y KEK.**

```
PS C:\> $secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Si la máquina virtual está **cifrada solo con BEK**, genere un archivo blob secreto a partir del JSON y rellénelo para restaurar el cmdlet secreto con el fin de volver a poner el secreto (BEK) en el almacén de claves.

```
PS C:\> $secretDestination = 'C:\secret.blob'
PS C:\> [io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
PS C:\> Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
```

> [!NOTE]
> 1. El valor de $secretname puede obtenerse haciendo referencia a la salida de $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl y usando el texto después de secrets/. Por ejemplo, la dirección URL de secreto de salida es https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 y el nombre del secreto B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> 2. El valor de la etiqueta DiskEncryptionKeyFileName es igual que el nombre de secreto.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Creación de la máquina virtual desde el disco restaurado
Si ha realizado la copia de seguridad de la máquina virtual cifrada mediante Azure VM Backup, los cmdlets de PowerShell mencionados arriba le ayudan a restaurar la clave y el secreto en el almacén de claves. Después de restaurarlos, vea el artículo [Administración de copias de seguridad y restauración de las máquinas virtuales de Azure mediante PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) para crear máquinas virtuales cifradas a partir del disco, la clave y el secreto restaurados.

## <a name="legacy-approach"></a>Enfoque heredado
El enfoque mencionado arriba podría funcionar para todos los puntos de recuperación. Pero el enfoque anterior de obtener la información de la clave y el secreto desde el punto de recuperación sería válido para los puntos de recuperación anteriores al 11 de julio de 2017 para máquinas virtuales cifradas con BEK y KEK. Una vez completo el trabajo de restauración de disco para la máquina virtual cifrada con los [pasos en PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm), asegúrese de que $rp se rellena con un valor válido.

### <a name="restore-key"></a>Restauración de la clave
Use los siguientes cmdlets para obtener la información de claves (KEK) del punto de recuperación y usarla para restaurar el cmdlet de claves y volver a colocarla en el almacén de claves.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Restauración del secreto
Use los siguientes cmdlets para obtener la información de secretos (BEK) del punto de recuperación y usarla para establecer el cmdlet de secretos y volver a colocarla en el almacén de claves.

```
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> 1. El valor de $secretname puede obtenerse haciendo referencia a la salida de $rp1.KeyAndSecretDetails.SecretUrl y usando el texto después de secrets/. Por ejemplo, la dirección URL de secreto de salida es https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 y el nombre del secreto B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> 2. El valor de la etiqueta DiskEncryptionKeyFileName es igual que el nombre de secreto.
> 3. El valor de DiskEncryptionKeyEncryptionKeyURL puede obtenerse del almacén de claves después de restaurar las claves y usar el cmdlet [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx).
>
>

## <a name="next-steps"></a>Pasos siguientes
Después de restaurar la clave y el secreto de nuevo en el almacén de claves, consulte el artículo [Administración de copias de seguridad y restauración de las máquinas virtuales de Azure mediante PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) para restaurar las máquinas virtuales cifradas a partir del disco, la clave y el secreto restaurados.
