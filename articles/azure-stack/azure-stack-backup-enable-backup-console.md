---
title: "Habilitación de la copia de seguridad de Azure Stack desde el portal de administración | Microsoft Docs"
description: "Habilite el servicio Infrastructure Backup con el portal de administración para que Azure Stack se pueda restaurar si se produce un error."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: a5a9757d871c343ba663862de7b6d75b9dd21c31
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Habilitación de la copia de seguridad de Azure Stack desde el portal de administración

*Se aplica a: sistemas integrados de Azure Stack y kit de desarrollo de Azure Stack*

Habilite el servicio Infrastructure Backup con el portal de administración para que Azure Stack pueda generar copias de seguridad. Puede utilizar estas copias de seguridad para restaurar el entorno si se produce une rror.

## <a name="enable-backup"></a>Habilitación de la copia de seguridad

1. Abra el portal de administración de Azure Stack en [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).
2. Seleccione **Más servicios** > **Infrastructure backup** (Copia de seguridad de infraestructura). Elija **Configuración** en la hoja **Copia de seguridad de infraestructura**.

    ![Azure Stack: configuración del controlador de copia de seguridad](media\azure-stack-backup\azure-stack-backup-settings.png).

3. Escriba la ruta de acceso a la **ubicación de almacenamiento de la copia de seguridad**. Debe utilizar una cadena de convención de nomenclatura universal (UNC) para la ruta de acceso de un recurso compartido de archivos hospedado en un dispositivo independiente. Una cadena UNC especifica la ubicación de recursos como archivos compartidos o dispositivos. Para el servicio, puede usar una dirección IP. Para garantizar la disponibilidad de los datos de copia de seguridad en caso de que se produzca un desastre, el dispositivo debe estar en una ubicación independiente.
    > [!Note]  
    > Puede usar un nombre de dominio completo en lugar de la dirección IP si el entorno admite la resolución de nombres de la red de infraestructura de Azure Stack para su entorno empresarial.
4. Escriba el **nombre de usuario** utilizando el dominio y el nombre de usuario. Por ejemplo, `Contoso\administrator`.
5. Escriba la **Contraseña** del usuario.
5. Escriba la contraseña de nuevo para **Confirmar la contraseña**.
6. Proporcione una clave precompartida en la casilla **Clave de cifrado**. Los archivos de copia de seguridad se cifran mediante esta clave. Asegúrese de almacenar esta clave en una ubicación segura. Una vez que configure esta clave por primera vez o la rote en el futuro, no podrá verla desde esta interfaz. Para obtener más instrucciones sobre cómo generar una clave precompartida, siga los scripts de [Habilitación de la copia de seguridad para Azure Stack con PowerShell](azure-stack-backup-enable-backup-powershell.md#generate-a-new-encryption-key). 
7. Seleccione **Aceptar** para guardar la configuración del controlador de copia de seguridad.

Para ejecutar una copia de seguridad, debe descargar las herramientas de Azure Stack y, a continuación, ejecutar el cmdlet de PowerShell **Start-AzSBackup** en el nodo de administración de Azure Stack. Para obtener más información, consulte [Copia de seguridad de Azure Stack](azure-stack-backup-back-up-azure-stack.md ).

## <a name="next-steps"></a>pasos siguientes

 - Aprenda a ejecutar una copia de seguridad: consulte [Copia de seguridad de Azure Stack](azure-stack-backup-back-up-azure-stack.md ).
- Aprenda a comprobar que la copia de seguridad se ejecutó: consulte [Confirm backup completed in administration portal](azure-stack-backup-back-up-azure-stack.md ) (Confirmación de copia de seguridad completada en el portal de administración).
