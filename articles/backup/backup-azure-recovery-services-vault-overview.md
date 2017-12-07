---
title: "Introducción a los almacenes de Recovery Services | Microsoft Docs"
description: "Información general y comparación entre los almacenes de Recovery Services y los de Azure Backup."
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.assetid: 38d4078b-ebc8-41ff-9bc8-47acf256dc80
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/15/2017
ms.author: markgal;arunak;sogup
ms.openlocfilehash: ad685744595ab86def8d226d7c2fb5455af98da4
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="recovery-services-vaults-overview"></a>Introducción a los almacenes de Recovery Services

En este artículo se describen las características de un almacén de Recovery Services. Un almacén de Recovery Services es una entidad de almacenamiento de Azure que aloja datos. Normalmente, los datos son copias de datos o información de configuración de máquinas virtuales (VM), cargas de trabajo, servidores o estaciones de trabajo. Puede usar almacenes de Recovery Services para almacenar datos de copia de seguridad de varios servicios de Azure como máquinas virtuales de IaaS (Linux o Windows) y Azure SQL Database. Los almacenes de Recovery Services son compatibles con System Center DPM, Windows Server, Azure Backup Server y muchos más. Los almacenes de Recovery Services facilitan la tarea de organizar los datos de copia de seguridad, al mismo tiempo que reduce al mínimo su sobrecarga administrativa. 

Dentro de una suscripción de Azure, puede crear hasta 25 almacenes de Recovery Services por región.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Comparación de almacenes de Recovery Services y de Backup

Si sigue teniendo almacenes de Backup, estos se actualizarán automáticamente a almacenes de Recovery Services. En noviembre de 2017, todos los almacenes de Backup se habrán actualizado a almacenes de Recovery Services. 

Los almacenes de Recovery Services se basan en el modelo de Azure Resource Manager, mientras que los almacenes de Backup se basan en el modelo de Azure Service Manager. Al actualizar un almacén de Backup a un almacén de Recovery Services, los datos de copia de seguridad permanecen intactos durante el proceso de actualización y después del mismo. Los almacenes de Recovery Services proporcionan características que no están disponibles para los almacenes de Backup, como:

- **Funcionalidades mejoradas para ayudar a proteger datos de copia de seguridad** : con los almacenes de Recovery Services, Azure Backup proporciona funcionalidades de seguridad para proteger las copias de seguridad en la nube. Estas características de seguridad garantizan que puede proteger las copias de seguridad y recuperar datos de forma segura, incluso si los servidores de producción y copia de seguridad están en peligro. [Más información](backup-azure-security-feature.md)

- **Supervisión central para el entorno de TI híbrido**: con los almacenes de Recovery Services, puede supervisar no solo sus [máquinas virtuales de IaaS de Azure](backup-azure-manage-vms.md), sino también sus [recursos locales](backup-azure-manage-windows-server.md#manage-backup-items) desde un portal central. [Más información](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **Control de acceso basado en rol (RBAC)**: RBAC permite un control muy detallado de la administración de acceso en Azure. [Azure proporciona diversas funciones integradas](../active-directory/role-based-access-built-in-roles.md) y Azure Backup tiene tres [roles integrados para administrar puntos de recuperación](backup-rbac-rs-vault.md). Los almacenes de Recovery Services son compatibles con RBAC, que restringe el acceso de copia de seguridad y restauración al conjunto definido de roles de usuario. [Más información](backup-rbac-rs-vault.md)

- **Protección de todas las configuraciones de Azure Virtual Machines**: los almacenes de Recovery Services protegen las máquinas virtuales basadas en Resource Manager, incluidos discos Premium, Managed Disks y máquinas virtuales cifradas. La actualización de un almacén de Backup a un almacén de Recovery Services proporciona la oportunidad de actualizar máquinas virtuales basadas en Service Manager a máquinas virtuales basadas en Resource Manager. Al actualizar el almacén, puede conservar los puntos de recuperación de máquinas virtuales basadas en Service Manager y configurar la protección de las máquinas virtuales actualizadas (habilitadas para Resource Manager). [Más información](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **Restauración instantánea para máquinas virtuales de IaaS**: con los almacenes de Recovery Services, puede restaurar archivos y carpetas desde una máquina virtual de IaaS sin tener que restaurar toda la máquina virtual, lo que permite unos tiempos de restauración más rápidos. La restauración instantánea para máquinas virtuales de IaaS está disponible tanto para máquinas virtuales Windows como Linux. [Más información](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

## <a name="managing-your-recovery-services-vaults-in-the-portal"></a>Administración de almacenes de Recovery Services en el portal
La creación y administración de almacenes de Recovery Services en Azure Portal es fácil porque el servicio Backup está integrado en el menú Configuración de Azure. Esta integración significa que se puede crear o administrar un almacén de Recovery Services *en el contexto del servicio de destino*. Por ejemplo, para ver los puntos de recuperación para una máquina virtual, selecciónela y haga clic en **Copia de seguridad** en el menú Configuración. Aparece la información de copia de seguridad específica de esa máquina virtual. En el ejemplo siguiente, **ContosoVM** es el nombre de la máquina virtual. **ContosoVM-demovault** es el nombre del almacén de Recovery Services. No es necesario que recuerde el nombre del almacén de Recovery Services que almacena los puntos de recuperación, ya que puede tener acceso a esta información en la máquina virtual.  

![Máquinas virtuales de detalles de almacén de Recovery Services](./media/backup-azure-recovery-services-vault-overview/rs-vault-in-context.png)

Si hay varios servidores protegidos con el mismo almacén de Recovery Services, puede ser más lógico mirar en el almacén de Recovery Services. Puede buscar todos los almacenes de Recovery Services de la suscripción y elegir uno de la lista.

Las secciones siguientes contienen vínculos a artículos que explican cómo usar un almacén de Recovery Services en cada tipo de actividad.

### <a name="back-up-data"></a>Copia de seguridad de datos
- [Copia de seguridad de máquinas virtuales de Azure](backup-azure-vms-first-look-arm.md)
- [Copia de seguridad de Windows Server o Windows Workstation](backup-try-azure-backup-in-10-mins.md)
- [Preparación para la copia de seguridad de cargas de trabajo de DPM en Azure](backup-azure-dpm-introduction.md)
- [Preparación para la copia de seguridad de cargas de trabajo mediante Azure Backup Server](backup-azure-microsoft-azure-backup.md)

### <a name="manage-recovery-points"></a>Administración de puntos de recuperación
- [Administrar copias de seguridad de máquina virtual de Azure](backup-azure-manage-vms.md)
- [Administración de archivos y carpetas](backup-azure-manage-windows-server.md)

### <a name="restore-data-from-the-vault"></a>Restauración de datos desde el almacén
- [Recuperación de archivos de máquinas virtuales de Azure](backup-azure-restore-files-from-vm.md)
- [Restauración de máquinas virtuales de Azure](backup-azure-arm-restore-vms.md)

### <a name="secure-the-vault"></a>Seguridad del almacén
- [Seguridad de los datos de copia de seguridad en la nube en los almacenes de Recovery Services](backup-azure-security-feature.md)



## <a name="next-steps"></a>Pasos siguientes
Use los artículos siguientes para realizar estos pasos:</br>
[Copia de seguridad de una máquina virtual de IaaS](backup-azure-arm-vms-prepare.md)</br>
[Copia de seguridad de Azure Backup Server](backup-azure-microsoft-azure-backup.md)</br>
[Copia de seguridad de Windows Server](backup-configure-vault.md)
