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
ms.openlocfilehash: df1f4c6fadd08b17a1a1eb8bbe41ab71ae4729fc
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="back-up-azure-stack"></a>Copia de seguridad de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y kit de desarrollo de Azure Stack*

Realice una copia de seguridad a petición en Azure Stack con la copia de seguridad vigente. Si necesita habilitar el servicio Infrastructure Backup Service, consulte [Habilitación de la copia de seguridad de Azure Stack desde el portal de administración](azure-stack-backup-enable-backup-console.md).

## <a name="start-azure-stack-backup"></a>Inicio de la copia de seguridad de Azure Stack

Abra una sesión de Windows PowerShell con privilegios elevados y ejecute los siguientes comandos:

   ```powershell
   Start-AzSBackup -Location $location
   ```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Confirmación de la copia de seguridad realizada en el portal de administración

1. Abra el portal de administración de Azure Stack en [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).
2. Seleccione **Más servicios** > **Infrastructure backup** (Copia de seguridad de infraestructura). Elija **Configuración** en la hoja **Copia de seguridad de infraestructura**.
3. Busque el **Nombre** y la **Fecha de finalización** de la copia de seguridad en la lista **Copias de seguridad disponibles**.
4. Compruebe que el **Estado** es **Correcto**.

También puede confirmar la copia de seguridad completada desde el portal de administración. Vaya a `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`.

## <a name="next-steps"></a>pasos siguientes

- Obtenga más información sobre el flujo de trabajo para recuperarse de un evento de pérdida de datos. Consulte [Recover from catastrophic data loss](azure-stack-backup-recover-data.md) (Recuperación después de una pérdida de datos grave).