---
title: "Preguntas más frecuentes sobre la copia de seguridad de máquinas virtuales de Azure | Microsoft Docs"
description: "Respuestas a preguntas habituales sobre cómo funciona la copia de seguridad de máquinas virtuales de Azure, las limitaciones y lo que sucede cuando se producen cambios en las directivas"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "copia de seguridad de máquinas virtuales de azure, restauración de máquinas virtuales de azure, directiva de copia de seguridad"
ms.assetid: c4cd7ff6-8206-45a3-adf5-787f64dbd7e1
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.openlocfilehash: 1372a9e05cb47f6c68240bffccd46b0fbebb5464
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="questions-about-the-azure-vm-backup-service"></a>Preguntas sobre el servicio de copia de seguridad de máquinas virtuales de Azure
En este artículo se incluyen respuestas a preguntas habituales para ayudarle a comprender rápidamente los componentes del servicio de copia de seguridad de máquinas virtuales de Azure. En algunas de las respuestas, hay vínculos a artículos que tienen información completa. También se pueden publicar preguntas sobre el servicio Copia de seguridad de Azure en el [foro de debate](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="configure-backup"></a>Configuración de la copia de seguridad
### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>¿Admiten los almacenes de Servicios de recuperación máquinas virtuales implementadas con el modelo clásico o máquinas virtuales implementadas con Resource Manager? <br/>
Los almacenes de Servicios de recuperación admiten ambos modelos.  Puede hacer copias de seguridad de una máquina virtual clásica (creada en el portal de clásico) o de una máquina virtual de Resource Manager (creada en Azure Portal) en un almacén de Recovery Services.

### <a name="what-configurations-are-not-supported-by-azure-vm-backup-"></a>¿Qué configuraciones no se admiten en la copia de seguridad de máquinas virtuales de Azure?
Consulte los [sistemas operativos admitidos](backup-azure-arm-vms-prepare.md#supported-operating-system-for-backup) y las [limitaciones de la copia de seguridad de máquinas virtuales](backup-azure-arm-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm).

### <a name="why-cant-i-see-my-vm-in-configure-backup-wizard"></a>¿Por qué no aparece mi máquina virtual en el Asistente para configuración de la copia de seguridad?
En el Asistente para configuración de la copia de seguridad, Azure Backup solo muestra máquinas virtuales con los siguientes criterios:
* No están aún protegidas: para comprobar el estado de copia de seguridad de una máquina virtual, vaya a la hoja de la máquina virtual y compruebe el estado en el menú Configuración de la hoja. Aprenda más sobre cómo [comprobar el estado de copia de seguridad de una máquina virtual](backup-azure-vms-first-look-arm.md#configure-the-backup-job-from-the-vm-management-blade).
* Pertenece a la misma región que la máquina virtual

## <a name="backup"></a>Copia de seguridad
### <a name="will-on-demand-backup-job-follow-same-retention-schedule-as-scheduled-backups"></a>¿Seguirá el trabajo de copia de seguridad a petición la misma programación de retención que las copias de seguridad programadas?
No. Para un trabajo de copia de seguridad a petición debe especificar el intervalo de retención. De forma predeterminada, se conservará durante 30 días cuando se active desde el portal. 

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Recientemente habilité Azure Disk Encryption en algunas máquinas virtuales. ¿Seguirán funcionando mis copias de seguridad?
Para acceder a Key Vault, debe conceder permisos para el servicio Azure Backup. Puede proporcionar estos permisos en PowerShell mediante los pasos que se indican en la sección sobre *cómo habilitar Backup* de la documentación de [PowerShell](backup-azure-vms-automation.md).

### <a name="i-migrated-disks-of-a-vm-to-managed-disks-will-my-backups-continue-to-work"></a>Migré los discos de una máquina virtual a discos administrados. ¿Seguirán funcionando mis copias de seguridad?
Sí, las copias de seguridad funcionan a la perfección y sin necesidad de volver a configurar la copia de seguridad. 

## <a name="restore"></a>Restauración
### <a name="how-do-i-decide-between-restoring-disks-versus-full-vm-restore"></a>¿Cómo decido entre la restauración de discos frente a restauración completa de máquinas virtuales?
Considere la restauración completa de máquinas virtuales de Azure como una opción rápida de creación para máquinas virtuales restauradas. La opción de restauración de máquinas virtuales cambia los nombres de discos, contenedores usados por discos, direcciones IP públicas o nombres de interfaz de red en favor de la unicidad de los recursos que se crean como parte de la creación de una máquina virtual; Tampoco agrega la máquina virtual al conjunto de disponibilidad. 

Use discos de restauración para:
* Personalizar la máquina virtual que se crea a partir de la configuración de restauración a un momento dado, como cambiar el tamaño en la configuración de copia de seguridad
* Agregar configuraciones que no están presentes en el momento de la copia de seguridad 
* Controlar la convención de nomenclatura para los recursos que se crean
* Agregar una máquina virtual al conjunto de disponibilidad
* Tiene una configuración que solo se puede lograr mediante PowerShell o una definición de plantilla declarativa

## <a name="manage-vm-backups"></a>Administrar copias de seguridad de máquina virtual
### <a name="what-happens-when-i-change-a-backup-policy-on-vms"></a>¿Qué ocurre cuando se cambia una directiva de copia de seguridad en las máquinas virtuales?
Cuando se aplica una nueva directiva en las máquinas virtuales, se seguirá la programación y retención de la nueva directiva. Si se amplía la retención, los puntos de recuperación existentes se marcarán para mantenerlos de acuerdo con la nueva directiva. Si se reduce la retención, se marcan para eliminarse, y se eliminan, en el siguiente trabajo de limpieza. 
