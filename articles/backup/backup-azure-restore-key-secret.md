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
ms.date: 10/18/2016
ms.author: pajosh
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 82b7541ab1434179353247ffc50546812346bda9
ms.openlocfilehash: ddb9e7909eb4ab97204059d21690795ceb6ff9e8
ms.lasthandoff: 03/02/2017


---
# <a name="restore-an-encrypted-virtual-machine-from-an-azure-backup-recovery-point"></a>Restauración de una máquina virtual de Azure desde un punto de recuperación de Azure Backup
En este artículo se explica cómo usar Azure Backup para restaurar máquinas virtuales de Azure cifradas, si su secreto y su clave no existen en el almacén de claves. Estos pasos también se pueden usar si desea mantener una copia independiente de la clave (clave de cifrado de Key Vault) y el secreto (clave de cifrado de BitLocker) para la máquina virtual restaurada.

## <a name="pre-requisites"></a>Requisitos previos
1. **Realizar una copia de seguridad de las máquinas virtuales cifradas**: se realizó una copia de seguridad de las máquinas virtuales cifradas de Azure mediante Azure Backup. Consulte el artículo [Implementación y administración de copias de seguridad para las máquinas virtuales implementadas según el modelo de Resource Manager mediante PowerShell](backup-azure-vms-automation.md) para más información acerca de cómo hacer una copia de seguridad cifrada de las máquinas virtuales de Azure.
2. **Configurar Azure Key Vault**: asegúrese de que ya exista el almacén de claves donde deben restaurarse las claves y los secretos. Consulte el artículo [Introducción a Azure Key Vault](../key-vault/key-vault-get-started.md) para más información sobre la administración de almacenes de claves.

## <a name="setup-recovery-services-vault"></a>Configuración del almacén de Recovery Services
Siga estos pasos para iniciar sesión en PowerShell y establecer el contexto del almacén de Recovery Services.

### <a name="log-in-to-azure-powershell"></a>Iniciar sesión en Azure PowerShell
Inicie sesión en la cuenta de Azure mediante el siguiente cmdlet.

```
PS C:\> Login-AzureRmAccount
```

### <a name="set-recovery-services-vault-context"></a>Establezca el contexto del almacén de Recovery Services.
Una vez iniciada la sesión, use el cmdlet siguiente para obtener una lista de las suscripciones disponibles.

```
PS C:\> Get-AzureRmSubscription
```

Seleccione la suscripción en la que los recursos están disponibles.

```
PS C:\> Set-AzureRmContext -SubscriptionId "<subscription-id>"
```

Establezca el contexto del almacén usando el almacén de Recovery Services donde se habilitó la copia de seguridad de las máquinas virtuales cifradas.

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName "<rg-name>" -Name "<rs-vault-name>" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="get-recovery-point"></a>Obtención del punto de recuperación
Seleccione el contenedor del almacén que representa la máquina virtual de Azure cifrada.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "<vm-name>"
```

Con este contenedor, obtenga el elemento de copia de seguridad de la máquina virtual correspondiente.

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

Obtenga una matriz de puntos de recuperación para el elemento de copia de seguridad seleccionado en la variable rp.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-encrypted-virtual-machine"></a>Restauración de la máquina virtual cifrada
Siga estos pasos para restaurar la máquina virtual cifrada, su clave y el secreto.

### <a name="restore-key"></a>Restauración de la clave
La matriz $rp anterior se ordena en orden inverso de tiempo, con el punto de recuperación más reciente en el índice 0. Por ejemplo: $rp[0] seleccionará el último punto de recuperación.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

> [!NOTE]
> Después de que este cmdlet se ejecute correctamente, se genera un archivo blob en la carpeta especificada del equipo donde se ejecuta. Este archivo de blob representa la clave de cifrado de Key Vault en su forma cifrada.
>
>

Restaure la clave al almacén de claves con el siguiente cmdlet.

```
PS C:\> Restore-AzureKeyVaultKey -VaultName "contosokeyvault" -InputFile "C:\Users\downloads\key.blob"
```

### <a name="restore-secret"></a>Restauración del secreto
Restaure los datos del secreto del punto de recuperación obtenido anteriormente.

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

> [!NOTE]
> El texto que aparece antes de vault.azure.net representa el nombre original del almacén de claves. El texto incluido después de secrets/ representa el nombre del secreto.
>
>

Si desea usar el mismo nombre de secreto, consulte la salida del cmdlet ejecutado anteriormente para obtener el nombre del secreto y su valor. En otros casos, el valor de $secretname debe actualizarse para usar el nuevo nombre de secreto.

```
PS C:\> $secretname = "B3284AAA-DAAA-4AAA-B393-60CAA848AAAA"
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

Si las máquinas virtuales también deben restablecerse, establezca las etiquetas del secreto. Para la etiqueta DiskEncryptionKeyFileName, el valor debe contener el nombre del secreto que se va a usar.

```
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

> [!NOTE]
> El valor de DiskEncryptionKeyFileName es igual que el nombre de secreto obtenido anteriormente. El valor de DiskEncryptionKeyEncryptionKeyURL puede obtenerse del almacén de claves después de restaurar las claves y usar el cmdlet [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx).    
>
>

Restablecimiento del secreto en el almacén de claves

```
PS C:\> Set-AzureKeyVaultSecret -VaultName "contosokeyvault" -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```

### <a name="restore-virtual-machine"></a>Restauración de la máquina virtual
Los cmdlets de PowerShell anteriores le ayudarán a restaurar la clave y el secreto al almacén de claves, si ha realizado la copia de seguridad de la máquina virtual cifrada mediante Azure Backup. Después de restaurarlos, consulte el artículo [Implementación y administración de copias de seguridad para las máquinas virtuales implementadas según el modelo de Resource Manager mediante PowerShell](backup-azure-vms-automation.md) para restaurar las máquinas virtuales cifradas.

