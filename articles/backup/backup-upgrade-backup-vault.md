---
title: "Actualización de un almacén de Backup a un almacén de Recovery Services de Azure Backup | Microsoft Docs"
description: "Actualización del almacén de Backup al almacén de Recovery Services para obtener nuevas características, como copia de seguridad de máquinas virtuales de Resource Manager, seguridad mejorada, copia de seguridad de máquinas virtuales de VMware y copia de seguridad de estado del sistema para servidores Windows"
services: backup
documentationcenter: 
author: trinadhk
manager: vijayts
editor: 
keyword: backup vault; upgrade vault; recovery services vault
ms.assetid: d037a8bf-49f2-4578-974a-3471d87ca278
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: trinadhk, sogup
ms.openlocfilehash: 708983fc2c5264d1213bdb32b665dcccc5ca9df9
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="backup-vault-upgraded-to-recovery-services-vault"></a>Almacén de Backup actualizado al almacén de Recovery Services
En este artículo se proporciona información general sobre Recovery Services, preguntas frecuentes sobre la actualización del almacén existente de Backup al almacén de Recovery Services y los pasos que se deben realizar con posterioridad a la actualización. Un almacén de Recovery Services es el equivalente de Azure Resource Manager de un almacén de Backup que aloja los datos de copia de seguridad. Normalmente, los datos son copias de datos o información de configuración de máquinas virtuales (VM), cargas de trabajo, servidores o estaciones de trabajo, tanto de Azure como locales.

## <a name="what-is-a-recovery-services-vault"></a>¿Qué es un almacén de Recovery Services?
Un almacén de Recovery Services es una entidad de almacenamiento en línea en Azure que se usa para contener datos, como copias de seguridad, puntos de recuperación y directivas de copia de seguridad. Puede usar almacenes de Recovery Services para almacenar datos de copia de seguridad de varios servicios de Azure como máquinas virtuales de IaaS (Linux o Windows) y Azure SQL Database. Los almacenes de Recovery Services son compatibles con System Center DPM, Windows Server, Azure Backup Server y muchos más. Los almacenes de Recovery Services facilitan la tarea de organizar los datos de copia de seguridad, al mismo tiempo que reduce al mínimo su sobrecarga administrativa.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Comparación de almacenes de Recovery Services y de Backup
Los almacenes de Recovery Services se basan en el modelo de Azure Resource Manager, mientras que los almacenes de Backup se basan en el modelo de Azure Service Manager. Al actualizar un almacén de Backup a un almacén de Recovery Services, los datos de copia de seguridad permanecen intactos durante el proceso de actualización y después del mismo. Los almacenes de Recovery Services proporcionan características que no están disponibles para los almacenes de Backup, como:

- **Funcionalidades mejoradas para ayudar a proteger datos de copia de seguridad** : con los almacenes de Recovery Services, Azure Backup proporciona funcionalidades de seguridad para proteger las copias de seguridad en la nube. Estas características de seguridad garantizan que puede proteger las copias de seguridad y recuperar datos de forma segura de las copias de seguridad en la nube, incluso si los servidores de producción y copia de seguridad están en peligro. [Más información](backup-azure-security-feature.md)

- **Supervisión central para el entorno de TI híbrido**: con los almacenes de Recovery Services, puede supervisar no solo sus [máquinas virtuales de IaaS de Azure](backup-azure-manage-vms.md), sino también sus [recursos locales](backup-azure-manage-windows-server.md#manage-backup-items) desde un portal central. [Más información](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Control de acceso basado en rol (RBAC)**: RBAC permite un control muy detallado de la administración de acceso en Azure. [Azure proporciona diversas funciones integradas](../active-directory/role-based-access-built-in-roles.md) y Azure Backup tiene tres [roles integrados para administrar puntos de recuperación](backup-rbac-rs-vault.md). Los almacenes de Recovery Services son compatibles con RBAC, que restringe el acceso de copia de seguridad y restauración al conjunto definido de roles de usuario. [Más información](backup-rbac-rs-vault.md)

- **Protección de todas las configuraciones de Azure Virtual Machines**: los almacenes de Recovery Services protegen las máquinas virtuales basadas en Resource Manager, incluidos discos Premium, Managed Disks y máquinas virtuales cifradas. La actualización de un almacén de Backup a un almacén de Recovery Services proporciona la oportunidad de actualizar máquinas virtuales basadas en Service Manager a máquinas virtuales basadas en Resource Manager. Al actualizar el almacén, puede conservar los puntos de recuperación de máquinas virtuales basadas en Service Manager y configurar la protección de las máquinas virtuales actualizadas (habilitadas para Resource Manager). [Más información](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Restauración instantánea para máquinas virtuales de IaaS**: con los almacenes de Recovery Services, puede restaurar archivos y carpetas desde una máquina virtual de IaaS sin tener que restaurar toda la máquina virtual, lo que permite unos tiempos de restauración más rápidos. La restauración instantánea para máquinas virtuales de IaaS está disponible tanto para máquinas virtuales Windows como Linux. [Más información](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

> [!NOTE]
> Si tiene elementos registrados en un almacén de Backup con un agente MARS anterior a 2.0.9083.0, [descargue la versión más reciente de este agente]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) para aprovechar las ventajas de todas las características del almacén de Recovery Services. 
> 

## <a name="managing-your-recovery-services-vaults"></a>Administración de almacenes de Recovery Services
En las pantallas siguientes se muestra un nuevo almacén de Recovery Services actualizado desde el almacén de Backup, en Azure Portal. El almacén actualizado estará presente en un grupo de recursos predeterminado denominado "Default-RecoveryServices-ResourceGroup-geo". Ejemplo: Si el almacén de Backup estaba situado en el Oeste de EE. UU., se colocará en un grupo de recursos diferente llamado Default-RecoveryServices-ResourceGroup-westus.
> [!NOTE]
> En los clientes de CPS estándar, el grupo de recursos no cambia después de la actualización del almacén y permanece igual que estaba antes de la actualización.

En la primera pantalla se muestra el panel del almacén con las principales entidades para el almacén.
![Ejemplo de almacén de Recovery Services actualizado desde un almacén de Backup](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

En la segunda pantalla se muestran los vínculos de ayuda para empezar a usar el almacén de Recovery Services.

![vínculos de ayuda en la hoja Inicio rápido](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>Pasos posteriores a la actualización
El almacén de Recovery Services admite la especificación de información de zona horaria en la directiva de copia de seguridad. Después de que el almacén se actualice correctamente, vaya a las directivas de Backup desde el menú de configuración del almacén y actualice la información de zona horaria para cada una de las directivas configuradas en dicho almacén. En esta pantalla ya se muestra la hora de programación de copia de seguridad especificada según la zona horaria local utilizada cuando se creó la directiva. 

## <a name="enhanced-security"></a>Mayor seguridad
Cuando un almacén de Backup se actualiza a un almacén de Recovery Services, la configuración de seguridad para ese almacén se activa automáticamente. Cuando la configuración de seguridad está activada, determinadas operaciones, como la eliminación de copias de seguridad o el cambio de una frase de contraseña, requieren un PIN de [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md). Para más información sobre la seguridad mejorada, vea el artículo [Características de seguridad para proteger copias de seguridad híbridas mediante Azure Backup](backup-azure-security-feature.md). Cuando la seguridad mejorada está activada, los datos se conservan hasta catorce días después de eliminar la información del punto de recuperación del almacén. Se factura a los clientes por el almacenamiento de estos datos de seguridad. La retención de datos de seguridad se aplica a los puntos de recuperación obtenidos para el agente de Azure Backup, Azure Backup Server y System Center Data Protection Manager. 

## <a name="gather-data-on-your-vault"></a>Recopilación de datos en el almacén
Cuando haya actualizado a un almacén de Recovery Services, configure los informes para Azure Backup (para máquinas virtuales IaaS y del agente de Microsoft Azure Recovery Services) y use Power BI para acceder a los informes. Para más información sobre la recopilación de datos, vea el artículo [Configuración de informes de Azure Backup](backup-azure-configure-reports.md).

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**¿Afecta el plan de actualización a mis copias de seguridad en curso?**</br>
Nº Las copias de seguridad en curso continúan sin interrupción durante y después de la actualización.

**¿Qué supone esta actualización para las herramientas existentes?**</br>
Debe actualizar la automatización o las herramientas existentes para asegurarse de que el modelo de Resource Manager sigue funcionando después de la actualización. Consulte las guías de referencia de los cmdlets de PowerShell para más información sobre el [modelo de implementación de Resource Manager](backup-client-automation.md).

**¿Puedo revertir la actualización?**</br>
Nº No se admite la reversión una vez actualizados correctamente los recursos.

**¿Puedo ver mi almacén clásico después de la actualización?**</br>
Nº No se puede ver o administrar su almacén clásico después de la actualización. Solo podrá usar el nuevo Azure Portal para todas las acciones de administración en el almacén.

**¿Por qué no puedo ver los servidores protegidos por el agente de MARS en mi almacén actualizado?**</br>
Para ver todos los servidores protegidos por el agente de MARS en su almacén, debe instalar el agente de MARS más reciente. Puede descargar la versión más reciente del agente [aquí]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe).

**No puedo ver la directiva de Backup de los servidores protegidos por el agente de MARS después de la actualización**</br>
La directiva de copia de seguridad del almacén podría estar caducada y, por tanto, no se ha podido sincronizar con el almacén actualizado. Actualice la directiva para asegurarse de que sigue viendo las directivas en el almacén actualizado.
Para actualizar la directiva, vaya al agente de MARS y actualice la directiva de copia de seguridad configurada.

**¿Por qué no puedo actualizar mi directiva de Backup después de la actualización?**</br>
Esto sucede cuando se encuentra en un agente de copia de seguridad antiguo y selecciona que el período de retención mínimo sea inferior al valor mínimo permitido. Cuando un almacén de Backup se actualiza a un almacén de Recovery Services, la configuración de seguridad para ese almacén se activa automáticamente. Para asegurarse de que siempre haya un número válido de puntos de recuperación disponibles, existe un período de retención mínimo que hay que mantener según la característica de seguridad. Para más detalles, consulte [aquí](backup-azure-security-feature.md).
Además, debe actualizar los agentes de Azure Backup a la versión más reciente para aprovechar las ventajas de las características más recientes de Azure Backup.

**He actualizado mi agente, pero sigo sin poder ver los objetos que se sincronizan incluso los días después de la actualización**</br>
Compruebe si ha registrado la misma máquina en varios almacenes. Asegúrese de que se está viendo el mismo almacén en el que está registrado el agente de MARS. Para averiguar en qué almacén está registrado el agente de MARS, abra el Registro de Windows y compruebe el valor de la clave ServiceResourceName en HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config. Ahí aparecerá el almacén registrado en ese agente de MARS. Si la clave ServiceResourceName no está visible en el sistema, póngase en contacto con nosotros para decirnos el valor de las claves ResourceId y MachineId en HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config y le ayudaremos a resolver el problema.

**¿Por qué no puedo ver la información de los trabajos para mis recursos después de la actualización?**</br>
La supervisión de copias de seguridad (agente de MARS e IaaS) es una característica nueva que obtendrá al actualizar el almacén de Backup al almacén de Recovery Services. La información de supervisión tarda hasta 12 horas en sincronizarse con el servicio.

**¿Cómo se informa de un problema?**</br>
Si se produce un error en cualquier parte de la actualización del almacén, tenga en cuenta el identificador de la operación que figura en el error. Soporte técnico de Microsoft trabajará proactivamente para resolver el problema. Puede conectar con el soporte técnico o enviarnos un correo electrónico a rsvaultupgrade@service.microsoft.com con su identificador de suscripción, el nombre del almacén y el identificador de la operación. Intentaremos resolver el problema lo antes posible. No vuelva a intentar la operación a menos que Microsoft se lo indique explícitamente.

## <a name="next-steps"></a>pasos siguientes
Use los artículos siguientes para realizar estos pasos:</br>
[Copia de seguridad de una máquina virtual de IaaS](backup-azure-arm-vms-prepare.md)</br>
[Copia de seguridad de Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Copia de seguridad de Windows Server](backup-configure-vault.md)
