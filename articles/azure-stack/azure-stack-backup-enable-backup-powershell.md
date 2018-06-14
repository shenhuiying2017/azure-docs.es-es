---
title: Habilitación de la copia de seguridad para Azure Stack con PowerShell | Microsoft Docs
description: Habilite el servicio Infrastructure Backup con Windows PowerShell para que Azure Stack se pueda restaurar si se produce un error.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 5fab656734d0984cf44a9fe1f29fd73530bd9aa8
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259862"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Habilitación de la copia de seguridad de Azure Stack con PowerShell

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Habilite el servicio Copia de seguridad de infraestructura con Windows PowerShell para realizar copias de seguridad periódicas de:
 - Servicio de identidad interno y certificados raíz.
 - Planes de usuario, ofertas, suscripciones
 - Secretos de Key Vault
 - Directivas y roles de RBAC de usuario

Puede tener acceso a los cmdlets de PowerShell para habilitar la copia de seguridad, iniciar la copia de seguridad y obtener información de la copia de seguridad a través del punto de conexión de administración del operador.

## <a name="prepare-powershell-environment"></a>Preparación del entorno de PowerShell

Para obtener instrucciones acerca de cómo configurar el entorno de PowerShell, consulte [Instalación de PowerShell para Azure Stack](azure-stack-powershell-install.md).

## <a name="generate-a-new-encryption-key"></a>Generación de una nueva clave de cifrado

Instalación y configuración de PowerShell para Azure Stack y las herramientas de Azure Stack.
 - Consulte [Get up and running with PowerShell in Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart) (Ponerse en marcha con PowerShell en Azure Stack).
 - Consulte [Descarga de herramientas de Azure Stack desde GitHub](azure-stack-powershell-download.md)

Abra una sesión de Windows PowerShell con privilegios elevados y ejecute los siguientes comandos:
   
   ```powershell
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
   ```
   
En la misma sesión de PowerShell, ejecute los comandos siguientes:

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

> [!Warning]  
> Debe usar las herramientas de AzureStack para generar la clave.

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Provisión del recurso compartido de copia de seguridad, las credenciales y la clave de cifrado para habilitar la copia de seguridad

En la misma sesión de PowerShell, modifique el siguiente script de PowerShell agregando las variables del entorno. Ejecute el script actualizado para proporcionar la clave de cifrado, las credenciales y la clave de cifrado al servicio Infrastructure Backup.

| Variable        | DESCRIPCIÓN   |
|---              |---                                        |
| $username       | Escriba el **nombre de usuario** con el dominio y el nombre de usuario para la ubicación de la unidad compartida con acceso suficiente para leer y escribir archivos. Por ejemplo, `Contoso\backupshareuser`. |
| $password       | Escriba la **Contraseña** del usuario. |
| $sharepath      | Escriba la ruta de acceso a la **ubicación de almacenamiento de la copia de seguridad**. Debe utilizar una cadena de convención de nomenclatura universal (UNC) para la ruta de acceso de un recurso compartido de archivos hospedado en un dispositivo independiente. Una cadena UNC especifica la ubicación de recursos como archivos compartidos o dispositivos. Para garantizar la disponibilidad de los datos de copia de seguridad, el dispositivo debe estar en una ubicación independiente. |

   ```powershell
    $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
    
    Set-AzSBackupShare -Location $location.Name -Path $sharepath -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey
   ```
   
##  <a name="confirm-backup-settings"></a>Confirmación de la configuración de copia de seguridad

En la misma sesión de PowerShell, ejecute los comandos siguientes:

   ```powershell
   Get-AzsBackupLocation | Select-Object -ExpandProperty externalStoreDefault | Select-Object -Property Path, UserName, Password | ConvertTo-Json
   ```

El resultado debe tener un aspecto similar a la siguiente salida JSON:

   ```json
      {
    "ExternalStoreDefault":  {
        "Path":  "\\\\serverIP\\AzSBackupStore\\contoso.com\\seattle",
        "UserName":  "domain\backupoadmin",
        "Password":  null
       }
   } 
   ```

## <a name="next-steps"></a>Pasos siguientes

 - Aprenda a ejecutar una copia de seguridad: consulte [Copia de seguridad de Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
 - Aprenda a comprobar que la copia de seguridad se ejecutó: consulte [Confirm backup completed in administration portal](azure-stack-backup-back-up-azure-stack.md ) (Confirmación de copia de seguridad completada en el portal de administración).
