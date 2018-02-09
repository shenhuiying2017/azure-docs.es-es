---
title: Copia de seguridad de Azure Stack | Microsoft Docs
description: "Realice una copia de seguridad a petición en Azure Stack con la copia de seguridad vigente."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 955b286967ca2bc8450e8988ec16c6a5c352aa8a
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="back-up-azure-stack"></a>Copia de seguridad de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y kit de desarrollo de Azure Stack*

Realice una copia de seguridad a petición en Azure Stack con la copia de seguridad vigente. Si necesita habilitar el servicio Infrastructure Backup Service, consulte [Habilitación de la copia de seguridad de Azure Stack desde el portal de administración](azure-stack-backup-enable-backup-console.md).

> [!Note]  
>  Las herramientas de Azure Stack contienen el cmdlet **Start-AzSBackup**. Para obtener instrucciones sobre cómo instalar las herramientas, consulte [Póngase a trabajar con PowerShell en Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart).

## <a name="start-azure-stack-backup"></a>Inicio de la copia de seguridad de Azure Stack

Abra una sesión de Windows PowerShell con privilegios elevados en el entorno de administración de operador y ejecute los siguientes comandos:

```powershell
    cd C:\tools\AzureStack-Tools-master\Connect
    Import-Module .\AzureStack.Connect.psm1

    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
    
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Confirmación de la copia de seguridad realizada en el portal de administración

1. Abra el portal de administración de Azure Stack en [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).
2. Seleccione **Más servicios** > **Infrastructure backup** (Copia de seguridad de infraestructura). Elija **Configuración** en la hoja **Copia de seguridad de infraestructura**.
3. Busque el **Nombre** y la **Fecha de finalización** de la copia de seguridad en la lista **Copias de seguridad disponibles**.
4. Compruebe que el **Estado** es **Correcto**.

También puede confirmar la copia de seguridad completada desde el portal de administración. Vaya a `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`.

## <a name="next-steps"></a>pasos siguientes

- Obtenga más información sobre el flujo de trabajo para recuperarse de un evento de pérdida de datos. Consulte [Recover from catastrophic data loss](azure-stack-backup-recover-data.md) (Recuperación después de una pérdida de datos grave).
