---
title: "Migración de máquinas virtuales de IaaS de Azure entre regiones de Azure | Microsoft Docs"
description: "Use Azure Site Recovery para migrar máquinas virtuales de IaaS de Azure de una región de Azure a otra."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: ef2972c077a2b1dd2b2fd6ce53cc6560520ea870
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migración de máquinas virtuales de IaaS de Azure entre regiones de Azure con Azure Site Recovery
## <a name="overview"></a>Información general
Bienvenido a Azure Site Recovery. Utilice este artículo si quiere migrar máquinas virtuales de Azure de una región de Azure a otra. Antes de comenzar, tenga en cuenta lo siguiente:

* Azure tiene dos modelos de implementación diferentes para crear y utilizar recursos: Azure Resource Manager y el portal clásico. Azure también tiene dos portales: el Portal de Azure clásico que admite el modelo de implementación clásico y el Portal de Azure que es compatible con ambos modelos de implementación. Los pasos básicos para la migración son los mismos, con independencia de que vaya a configurar Site Recovery en Resource Manager o en el portal clásico. Sin embargo, las instrucciones de la interfaz de usuario y las capturas de pantalla del artículo son pertinentes para el Portal de Azure.
* **Actualmente, solo puede efectuar la migración de una región a otra. Puede conmutar por error las máquinas virtuales de una región de Azure a otra, pero no podrá volver a realizar este proceso con estas máquinas más adelante.**

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Requisitos previos
Requisitos para realizar esta implementación:

* **Máquinas virtuales IaaS**: las máquinas virtuales que desea migrar. Migrará estas máquinas virtuales tratándolas como máquinas físicas.

## <a name="deployment-steps"></a>Pasos de implementación
En esta sección se describen los pasos de implementación en el nuevo Portal de Azure.

1. [Cree un almacén](site-recovery-vmware-to-azure.md).
2. [Habilite la replicación](site-recovery-vmware-to-azure.md). Habilite la replicación en las máquinas virtuales que quiera migrar y elija Azure como origen. 
3. [ Ejecute una conmutación por error no planeada](site-recovery-failover.md). Una vez completada la replicación inicial, puede ejecutar una conmutación por error no planeada desde una región de Azure a otra. Si lo desea, puede crear un plan de recuperación y una conmutación por error no planeada para migrar varias máquinas virtuales entre las regiones. [Obtenga más información](site-recovery-create-recovery-plans.md) sobre los planes de recuperación.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre otros escenarios de replicación en [¿Qué es Site Recovery?](site-recovery-overview.md)

