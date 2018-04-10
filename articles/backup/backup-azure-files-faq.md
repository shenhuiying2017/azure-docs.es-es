---
title: Preguntas frecuentes sobre la copia de seguridad de archivos de Azure
description: En este artículo se proporcionan detalles acerca de cómo proteger los recursos compartidos de archivos de Azure.
services: backup
keywords: No agregue ni edite palabras clave sin consultar a su responsable de SEO Champ.
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.service: backup
ms.workload: storage-backup-recovery
manager: carmonm
ms.openlocfilehash: 8093275ec9e9cce6d9a765bf1bfc434fecdb6ea7
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2018
---
# <a name="questions-about-backing-up-azure-files"></a>Preguntas acerca de la copia de seguridad de archivos de Azure
En este artículo se ofrecen respuestas a preguntas habituales acerca de la copia de seguridad de archivos de Azure. En algunas de las respuestas, hay vínculos a artículos que tienen información completa. También se pueden publicar preguntas sobre el servicio Azure Backup en el [foro de debate](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Para examinar rápidamente las secciones de este artículo, use los vínculos de la derecha que aparecen debajo de **En este artículo**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Configuración del trabajo de copia de seguridad de archivos de Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares-br"></a>¿Por qué no puedo ver algunas de las cuentas de almacenamiento que quiero proteger, que contienen recursos compartidos de archivos de Azure válidos? <br/>
Durante la versión preliminar, la copia de seguridad de los recursos compartidos de archivos de Azure no admite todos los tipos de cuentas de almacenamiento. Consulte esta lista de [aquí](troubleshoot-azure-files.md#preview-boundaries) para ver las cuentas de almacenamiento admitidas.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>¿Por qué no veo algunos de mis recursos compartidos de archivos de Azure en la cuenta de almacenamiento cuando intento configurar la copia de seguridad? <br/>
Compruebe si el recurso compartido de archivos de Azure ya está protegido en el mismo almacén de Recovery Services o si se ha eliminado recientemente.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync-br"></a>¿Puedo proteger recursos compartidos de archivos conectado a un grupo de sincronización en Azure File Sync? <br/>
Sí. La protección de recursos compartidos de archivos de Azure conectados a grupos de sincronización está habilitada y forma parte de la versión preliminar pública.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-did-not-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Al intentar realizar la copia de seguridad de recursos compartidos de archivos, hice clic en una cuenta de almacenamiento para detectar los recursos compartidos de archivos de ella. Sin embargo, no los he protegido. ¿Cómo puedo proteger estos recursos compartidos de archivos con cualquier otro almacén?
Al intentar realizar una copia de seguridad, si selecciono una cuenta de almacenamiento para detectar los recursos compartidos que hay en ella, se registra la cuenta de almacenamiento con el almacén desde el que se realiza esto. Si elige proteger los recursos compartidos de archivos con un almacén diferente, [anule el registro](troubleshoot-azure-files.md#configuring-backup) de la cuenta de almacenamiento seleccionada de este almacén.

### <a name="can-i-change-the-vault-to-which-i-backup-my-file-shares"></a>¿Puedo cambiar el almacén en el que realizo la copia de seguridad de mis recursos compartidos de archivos?
Sí. Sin embargo, deberá [detener la protección](backup-azure-files.md#stop-protecting-an-azure-file-share) desde el almacén conectado, y [anular el registro](troubleshoot-azure-files.md#configuring-backup) de esta cuenta de almacenamiento y, a continuación, protegerla desde un almacén distinto.

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>¿En qué zonas geográficas se puede realizar una copia de seguridad de los recursos compartidos de archivos de Azure? <br/>
La copia de seguridad de recursos compartidos de archivos de Azure se encuentra actualmente en versión preliminar y solo está disponible en las siguientes zonas geográficas: 
-   Sudeste de Australia (ASE) 
- Sur de Brasil (BRS)
- Centro de Canadá (CNC)
-   Este de Canadá (CE)
-   Centro de EE. UU. (CUS)
-   Asia Oriental (EA)
-   Australia Oriental (AE) 
-   Este de EE. UU. (EUS)
-   Este de EE. UU. 2 (EUS2)
- Este de Japón (JPE)
- Oeste de Japón (JPE)
-   India central (INC) 
- India del Sur (INS)
- Corea del Sur (KRS)
-   Centro-norte de EE. UU. (NCUS) 
-   Europa del Norte (NE) 
-   Centro-sur de EE. UU. (SCUS) 
-   Asia Suroriental (SEA)
-   Sur de Reino Unido (UKS) 
-   Oeste de Reino Unido (UKW) 
-   Europa Occidental (WE) 
-   Oeste de EE. UU. (WUS)
-   Centro-oeste de EE. UU. (WCUS)
-   Oeste de Estados Unidos 2 (WUS 2)

Escriba a [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) si tiene que usarla en una zona geográfica específica que no se haya indicado anteriormente.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vaultbr"></a>¿Cuántos recursos compartidos de archivos de Azure se pueden proteger en un almacén?<br/>
Durante la versión preliminar, puede proteger los recursos compartidos de archivos de Azure de hasta 25 cuentas de almacenamiento por almacén. También puede proteger hasta 200 recursos compartidos de archivos de Azure en un único almacén.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>¿Puedo proteger dos recursos compartidos de archivos diferentes desde la misma cuenta de almacenamiento en almacenes diferentes?
Nº Todos los recursos compartidos de archivos de una cuenta de almacenamiento solo se pueden proteger en el mismo almacén.

## <a name="backup"></a>Backup

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>¿Cuántas copias de seguridad a petición se pueden realizar por recurso compartido de archivos? <br/>
En cualquier momento dado, puede tener hasta 200 instantáneas de un recurso compartido de archivos. El límite incluye las instantáneas que toma Azure Backup como se define en la directiva. Si las copias de seguridad empiezan a generar errores después de alcanzar el límite, elimine los puntos de restauración a petición para que las próximas copias de seguridad sean correctas.

### <a name="after-enabling-virtual-networks-on-my-storage-account-the-backup-of-file-shares-in-the-account-started-failing-why"></a>Después de habilitar las redes virtuales en mi cuenta de almacenamiento, la copia de seguridad de los recursos compartidos de archivos de la cuenta comienzan a generar errores. ¿Por qué?
La copia de seguridad de recursos compartidos de archivos de Azure no admite cuentas de almacenamiento que tengan habilitadas las redes virtuales. Deshabilite las redes virtuales en las cuentas de almacenamiento para poder realizar copias de seguridad correctas. 

## <a name="restore"></a>Restore

### <a name="can-i-recover-from-a-deleted-azure-file-share-br"></a>¿Se pueden recuperar los recursos compartidos de archivos de Azure eliminados? <br/>
Cuando se elimina un recurso compartido de archivos de Azure, se muestra la lista de copias de seguridad que se eliminarán y se solicita una confirmación. No se puede restaurar un recurso compartido de archivos de Azure eliminado.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share-br"></a>¿Puedo restaurar las copias de seguridad si detuve la protección en un recurso compartido de archivos de Azure? <br/>
Sí. Si ha elegido **Retener datos de copia de seguridad** cuando detuvo la protección, puede restaurarlas desde todos los puntos de restauración existentes.

## <a name="manage-backup"></a>Administración de copias de seguridad

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>¿Puedo acceder a las instantáneas realizadas por las instancias de Azure Backup y montarlas? <br/>
Es posible acceder a todas las instantáneas realizadas por Azure Backup desde el portal, PowerShell o la CLI. Para más información acerca de las instantáneas de recurso compartido de Azure Files, consulte [Información general de instantáneas de recurso compartido de Azure Files (versión preliminar)](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>¿Cuál es la retención máxima que puedo configurar para las copias de seguridad? <br/>
La copia de seguridad de recursos compartidos de archivos de Azure ofrece la posibilidad de retener las copias de seguridad diarias durante 120 días como máximo.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share-br"></a>¿Qué ocurre cuando se cambia la directiva de copia de seguridad de un recurso compartido de archivos de Azure? <br/>
Cuando se aplica una nueva directiva en los recursos compartidos de archivos, se siguen las opciones de programación y retención de la nueva directiva. Si se amplía la retención, los puntos de recuperación existentes se marcarán para mantenerlos de acuerdo con la nueva directiva. Si se reduce la retención, se marcan para eliminarse, y se eliminan, en el siguiente trabajo de limpieza.

## <a name="see-also"></a>Otras referencias
Esta información es solo sobre la copia de seguridad de archivos de Azure. Para más información sobre otras áreas de Azure Backup, consulte algunas de estas otras preguntas frecuentes acerca de las copias de seguridad:
-  [Preguntas más frecuentes sobre el almacén de Recovery Services](backup-azure-backup-faq.md)
-  [Preguntas más frecuentes sobre la copia de seguridad de máquinas virtuales de Azure](backup-azure-vm-backup-faq.md)
-  [Preguntas más frecuentes sobre Azure Backup Agent](backup-azure-file-folder-backup-faq.md)
