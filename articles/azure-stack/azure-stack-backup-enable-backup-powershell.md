---
title: "Habilitación de la copia de seguridad para Azure Stack con PowerShell | Microsoft Docs"
description: Habilite el servicio Infrastructure Backup con Windows PowerShell para que Azure Stack se pueda restaurar si se produce un error.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 7DFEFEBE-D6B7-4BE0-ADC1-1C01FB7E81A6
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: cbec6242fb4e185c9801a93fc2c4b35721269c2f
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Habilitación de la copia de seguridad de Azure Stack con PowerShell

*Se aplica a: sistemas integrados de Azure Stack y kit de desarrollo de Azure Stack*

Habilite el servicio Infrastructure Backup con Windows PowerShell para que Azure Stack se pueda restaurar si se produce un error. Puede tener acceso a los cmdlets de PowerShell para habilitar la copia de seguridad, iniciar la copia de seguridad y obtener información de la copia de seguridad a través del punto de conexión de administración del operador.

## <a name="download-azure-stack-tools"></a>Descarga de herramientas de Azure Stack

Instalación y configuración de PowerShell para Azure Stack y las herramientas de Azure Stack. Consulte [Get up and running with PowerShell in Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart) (Ponerse en marcha con PowerShell en Azure Stack).

##  <a name="load-the-connect-and-infrastructure-modules"></a>Carga de los módulos de conexión e infraestructura

Abra una sesión de Windows PowerShell con privilegios elevados y ejecute los siguientes comandos:

   ```powershell
    cd C:\tools\AzureStack-Tools-master\Connect
    Import-Module .\AzureStack.Connect.psm1
    
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
    
   ```

##  <a name="setup-rm-environment-and-log-into-the-operator-management-endpoint"></a>Configuración del entorno de RM e inicio de sesión en el punto de conexión de administración del operador

En la misma sesión de PowerShell, modifique el siguiente script de PowerShell agregando las variables del entorno. Ejecute el script actualizado para configurar el entorno de RM e inicie sesión en el punto de conexión de administración del operador.

| Variable    | DESCRIPCIÓN |
|---          |---          |
| $TenantName | Nombre del inquilino de Azure Active Directory. |
| Operator account name        | El nombre de cuenta del operador de Azure Stack. |
| Azure Resource Manager Endpoint | Dirección URL a Azure Resource Manager. |

   ```powershell
   # Specify Azure Active Directory tenant name
    $TenantName = "contoso.onmicrosoft.com"
    
    # Set the module repository and the execution policy
    Set-PSRepository `
      -Name "PSGallery" `
      -InstallationPolicy Trusted
    
    Set-ExecutionPolicy RemoteSigned `
      -force
    
    # Configure the Azure Stack operator’s PowerShell environment.
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint "https://adminmanagement.seattle.contoso.com"
    
    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience "https://graph.windows.net/"
    
    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName $TenantName `
      -EnvironmentName AzureStackAdmin
    
    # Sign-in to the operator's console.
    Login-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID 
    
   ```
## <a name="generate-a-new-encryption-key"></a>Generación de una nueva clave de cifrado

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
| $username       | Escriba el **nombre de usuario** con el dominio y el nombre de usuario para la ubicación de la unidad compartida. Por ejemplo, `Contoso\administrator`. |
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
   Get-AzsBackupLocation | Select-Object -Property Path, UserName, Password | ConvertTo-Json 
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

## <a name="next-steps"></a>pasos siguientes

 - Aprenda a ejecutar una copia de seguridad: consulte [Copia de seguridad de Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
- Aprenda a comprobar que la copia de seguridad se ejecutó: consulte [Confirm backup completed in administration portal](azure-stack-backup-back-up-azure-stack.md ) (Confirmación de copia de seguridad completada en el portal de administración).
