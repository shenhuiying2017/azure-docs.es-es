---
title: Azure Government Backup| Microsoft Docs
description: "En este artículo se proporciona información general sobre las características de Azure Backup disponibles en Azure Government."
services: azure-government
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: a7622135-8790-4be4-a02a-7b9ac8a4996f
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 1/5/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: fa00142a9e89c5ad2630f688ea9771a1a542c052
ms.openlocfilehash: e5f89f845302ecb890caa50dd8f86503b29f1154


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

[!INCLUDE [learn-about-backup-deployment models](../../includes/backup-deployment-models.md)]

## <a name="azure-backup-components-available-in-azure-government-backup"></a>Componentes de Azure Backup disponibles en Azure Government Backup

Puede usar Azure Backup para proteger: archivos, carpetas, volúmenes, máquinas virtuales, aplicaciones y cargas de trabajo. Según lo que quiera proteger y dónde existan esos datos, usará un componente de Azure Backup u otro. Las siguientes secciones contienen vínculos a artículos de la documentación pública de Azure Backup para cada componente. Las secciones se dividen por portal clásico o Azure Portal. Utilice la versión de Azure Portal si tiene previsto usar las implementaciones de Resource Manager.

### <a name="using-windows-server-and-windows-computers-in-azure-portal"></a>Uso de equipos de Windows Server y Windows en Azure Portal

- [Creación de una copia de seguridad de equipos cliente de Windows Server y Windows](../backup/backup-configure-vault.md)
- [Restauración de equipos cliente de Windows Server y Windows](../backup/backup-azure-restore-windows-server.md)
- [Administración de equipos cliente de Windows Server y Windows](../backup/backup-azure-manage-windows-server.md)
- [Uso de PowerShell para hacer copias de seguridad de Windows Server](../backup/backup-client-automation.md)

### <a name="using-windows-server-and-windows-computers-in-classic-portal"></a>Uso de equipos de Windows Server y Windows en el portal clásico

- [Creación de una copia de seguridad de equipos cliente de Windows Server y Windows](../backup/backup-configure-vault-classic.md)
- [Restauración de equipos cliente de Windows Server y Windows](../backup/backup-azure-restore-windows-server-classic.md)
- [Administración de equipos cliente de Windows Server y Windows](../backup/backup-azure-manage-windows-server-classic.md)
- [Uso de PowerShell para hacer copias de seguridad de Windows Server](../backup/backup-client-automation-classic.md)

### <a name="using-virtual-machines-in-azure-portal"></a>Uso de Virtual Machines en Azure Portal

- [Preparación del entorno de la máquina virtual](../backup/backup-azure-arm-vms-prepare.md)
- [Copia de seguridad de máquinas virtuales](../backup/backup-azure-vms-first-look-arm.md)
- [Restauración de máquinas virtuales](../backup/backup-azure-arm-restore-vms.md)
- [Administración de máquinas virtuales](../backup/backup-azure-manage-vms.md)
- [Uso de PowerShell para realizar copias de seguridad de máquinas virtuales](../backup/backup-azure-vms-automation.md)

### <a name="using-virtual-machines-in-classic-portal"></a>Uso de Virtual Machines en el portal clásico

- [Preparación del entorno de la máquina virtual](../backup/backup-azure-vms-prepare.md)
- [Copia de seguridad de máquinas virtuales](../backup/backup-azure-vms-first-look.md)
- [Restauración de máquinas virtuales](../backup/backup-azure-restore-vms.md)
- [Administración de máquinas virtuales](../backup/backup-azure-manage-vms-classic.md)
- [Uso de PowerShell para realizar copias de seguridad de máquinas virtuales](../backup/backup-azure-vms-classic-automation.md)

### <a name="using-system-center-data-protection-manager-in-azure-portal"></a>Uso de System Center Data Protection Manager en Azure Portal

- [Copia de seguridad de System Center Data Protection Manager](../backup/backup-azure-dpm-introduction.md)

### <a name="using-system-center-data-protection-manager-in-classic-portal"></a>Uso de System Center Data Protection Manager en el portal clásico

- [Copia de seguridad de System Center Data Protection Manager](../backup/backup-azure-dpm-introduction-classic.md)

### <a name="using-azure-backup-server-in-azure-portal"></a>Uso del servidor de copia de seguridad de Azure en Azure Portal

El servidor de copia de seguridad de Azure es un componente de Azure Backup que funciona de forma parecida a System Center Data Protection Manager (DPM) con una excepción: el servidor de copia de seguridad de Azure no puede guardar datos en cinta. Con el servidor de copia de seguridad de Azure, puede proteger cargas de trabajo de aplicaciones como máquinas virtuales de Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange y clientes Windows desde una única consola. El servidor de copia de seguridad de Azure no requiere una licencia de System Center.

- [Servidor de Copia de seguridad de Azure](../backup/backup-azure-microsoft-azure-backup.md)

### <a name="using-azure-backup-server-in-classic-portal"></a>Uso del servidor de copia de seguridad de Azure en el portal clásico

- [Servidor de Copia de seguridad de Azure](../backup/backup-azure-microsoft-azure-backup-classic.md)


## <a name="next-steps"></a>Pasos siguientes

Si no está seguro de por dónde comenzar, empiece por el artículo [Creación de una copia de seguridad de un servidor o cliente de Windows con el modelo de implementación clásica](../backup/backup-configure-vault-classic.md). Este tutorial le guía por los pasos para configurar un proyecto de copia de seguridad en un equipo o servidor de Windows.

Si ya sabe que puede usar copia de Azure Backup, pero desea conocer los costos, consulte la [página de precios de backup](http://azure.microsoft.com/pricing/details/backup/). Hay una lista de preguntas más frecuentes que puede proporcionar información útil. Tenga en cuenta también que hay dos regiones de Azure Government en el menú desplegable **Región**.



<!--HONumber=Jan17_HO1-->


