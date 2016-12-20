---
title: Azure Government Backup| Microsoft Docs
description: "En este artículo se proporciona información general sobre las características de Azure Backup disponibles en Azure Government."
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
ms.assetid: a7622135-8790-4be4-a02a-7b9ac8a4996f
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/11/2016
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: 0095a95afc14de42c1160a73139a0f059cd758dd
ms.openlocfilehash: 3b3ecaef33aefc89e5a5c1cd351566d21246d0b8


---
# <a name="azure-government-backup"></a>Azure Government Backup

Se ofrece una introducción al servicio Azure Backup y se enumeran las características de backup disponibles en Azure Government. Azure Backup es el servicio de Azure que puede usar para realizar una copia de seguridad de los datos (protegerlos) y recuperarlos en la nube de Microsoft. Proteger los datos en Azure no solo significa hacer una copia de seguridad de ellos en la nube, sino restaurarlos en la nube o en una instalación local. Azure Backup proporciona estas ventajas principales:

- Administración automática del almacenamiento
- Escalado ilimitado
- Varias opciones de almacenamiento
- Transferencia de datos ilimitada
- Cifrado de datos
- Copia de seguridad coherente con la aplicación
- Retención a largo plazo

Si no está familiarizado con Azure Backup y desea una introducción a las características disponibles, lea el artículo [¿Qué es Azure Backup?](../backup/backup-introduction-to-azure-backup.md)

> [!IMPORTANT]
> Actualmente, Azure Government Backup admite implementaciones de Service Manager, lo que se conoce como el modelo de implementación clásica. Aún no se admiten implementaciones de Resource Manager. Consulte el artículo siguiente para ver la [diferencia entre los mdoelos de implementación clásica y de Azure Resource Manager](../resource-manager-deployment-model.md).

[!INCLUDE [learn-about-backup-deployment models](../../includes/backup-deployment-models.md)]

## <a name="azure-backup-components-available-in-azure-government-backup"></a>Componentes de Azure Backup disponibles en Azure Government Backup

Puede usar Azure Backup para proteger: archivos, carpetas, volúmenes, máquinas virtuales, aplicaciones y cargas de trabajo. Según lo que quiera proteger y dónde existan esos datos, usará un componente de Azure Backup u otro. Las siguientes secciones contienen vínculos a artículos de la documentación pública de Azure Backup para cada componente.

En cada artículo se explica cómo usar el componente de Azure Backup en el portal de la versión clásica.

### <a name="windows-server-and-windows-computers"></a>Equipos de Windows Server y Windows

- [Creación de una copia de seguridad de equipos cliente de Windows Server y Windows](../backup/backup-configure-vault-classic.md)
- [Restauración de equipos cliente de Windows Server y Windows](../backup/backup-azure-restore-windows-server.md)
- [Administración de equipos cliente de Windows Server y Windows](../backup/backup-azure-manage-windows-server.md)
- [Uso de PowerShell para hacer copias de seguridad de Windows Server](../backup/backup-client-automation-classic.md)

### <a name="virtual-machines"></a>Máquinas virtuales

- [Preparación del entorno de la máquina virtual](../backup/backup-azure-vms-prepare.md)
- [Copia de seguridad de máquinas virtuales](../backup/backup-azure-vms-first-look.md)
- [Restauración de máquinas virtuales](../backup/backup-azure-restore-vms.md)
- [Administración de máquinas virtuales](../backup/backup-azure-manage-vms-classic.md)
- [Uso de PowerShell para realizar copias de seguridad de máquinas virtuales](../backup/backup-azure-vms-classic-automation.md)

### <a name="system-center-data-protection-manager"></a>System Center Data Protection Manager

- [Copia de seguridad de System Center Data Protection Manager](../backup/backup-azure-dpm-introduction-classic.md)

### <a name="azure-backup-server"></a>Servidor de Copia de seguridad de Azure

- [Servidor de Copia de seguridad de Azure](../backup/backup-azure-microsoft-azure-backup-classic.md)

Azure Backup Server es un componente de Azure Backup que funciona de forma parecida a System Center Data Protection Manager (DPM). Con el servidor de copia de seguridad de Azure, puede proteger cargas de trabajo de aplicaciones como máquinas virtuales de Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange y clientes Windows desde una única consola.

## <a name="next-steps"></a>Pasos siguientes

Si no está seguro de por dónde comenzar, empiece por el artículo [Creación de una copia de seguridad de un servidor o cliente de Windows con el modelo de implementación clásica](../backup/backup-configure-vault-classic.md). Este tutorial le guía por los pasos para configurar un proyecto de copia de seguridad en un equipo o servidor de Windows.

Si ya sabe que puede usar copia de Azure Backup, pero desea conocer los costos, consulte la [página de precios de backup](http://azure.microsoft.com/pricing/details/backup/). Hay una lista de preguntas más frecuentes que puede proporcionar información útil. Tenga en cuenta también que hay dos regiones de Azure Government en el menú desplegable **Región**.



<!--HONumber=Nov16_HO3-->


