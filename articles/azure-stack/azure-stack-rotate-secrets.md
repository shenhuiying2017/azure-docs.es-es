---
title: Cambio de secretos en Azure Stack | Microsoft Docs
description: Aprenda a cambiar los secretos en Azure Stack.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 49071044-6767-4041-9EDD-6132295FA551
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: mabrigg
ms.openlocfilehash: e2e9d93af3889714ade1d0364a6f747c184e6d75
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>Cambio de secretos en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack*

Actualice regularmente las contraseñas de los componentes de Azure Stack.

## <a name="updating-the-passwords-for-the-baseboard-management-controller-bmc"></a>Actualización de las contraseñas para el controlador de administración de placa base (BMC)

Los controladores de administración de placa base (BMC) supervisan el estado físico de sus servidores. Las especificaciones e instrucciones para actualizar la contraseña de BMC varían según el proveedor de hardware del fabricante de equipos originales (OEM).

1. Actualice el BMC en los servidores físicos de Azure Stack siguiendo las instrucciones del OEM. La contraseña para cada BMC de su entorno debe ser la misma.
2. Abra un punto de conexión con privilegios en sesiones de Azure Stack. Para obtener instrucciones, consulte [Uso del punto de conexión con privilegios en Azure Stack](azure-stack-privileged-endpoint.md).
3. Después que el símbolo del sistema de PowerShell cambia a **[dirección IP o nombre de máquina virtual de ERCS]: PS>** o a **[azs-ercs01]: PS>**, según el entorno, ejecute `Set-BmcPassword` mediante la ejecución de `invoke-command`. Pase la variable de sesión del punto de conexión con privilegios como parámetro. Por ejemplo: 

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```
    
    También puede usar la versión de PowerShell estática con las contraseñas como líneas de código:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```

## <a name="next-steps"></a>pasos siguientes

Para más información sobre la seguridad y Azure Stack, consulte [Posición de seguridad de la infraestructura de Azure Stack](azure-stack-security-foundations.md).
