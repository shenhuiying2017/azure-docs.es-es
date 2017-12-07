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
ms.date: 11/27/2017
ms.author: raynew
ms.openlocfilehash: f645150aa7b93ebbd98899cc8fdd495dfeaf3076
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/29/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migración de máquinas virtuales de IaaS de Azure entre regiones de Azure con Azure Site Recovery

Bienvenido a Azure Site Recovery. Utilice este artículo si quiere migrar máquinas virtuales de Azure de una región de Azure a otra.
>[!NOTE]
>
> Para replicar máquinas virtuales de Azure en otra región en caso de migración y recuperación ante desastres, consulte [este documento](../site-recovery-azure-to-azure.md). La replicación de Site Recovery en máquinas virtuales de Azure se encuentra actualmente en versión preliminar.


## <a name="prerequisites"></a>Requisitos previos
Requisitos para realizar esta implementación:

* **Máquinas virtuales IaaS**: las máquinas virtuales que desea migrar. Migrará estas máquinas virtuales tratándolas como máquinas físicas.

## <a name="deployment-steps"></a>Pasos de implementación
En esta sección se describen los pasos de implementación en el nuevo Portal de Azure.

1. [Cree un almacén](../site-recovery-azure-to-azure.md#create-a-recovery-services-vault).
2. [Habilite la replicación]../(site-recovery-azure-to-azure.md) en las máquinas virtuales que desee migrar y elija Azure como origen.
  >[!NOTE]
  >
  > Actualmente, no se admite la replicación nativa de máquinas virtuales de Azure mediante discos administrados. Puede usar la opción "Physical to Azure" (Físico en Azure) de [este documento](../site-recovery-vmware-to-azure.md) para migrar máquinas virtuales con discos administrados.
3. [Ejecute una conmutación por error](../site-recovery-failover.md). Una vez completada la replicación inicial, puede ejecutar una conmutación por error desde una región de Azure a otra. Si lo desea, puede crear un plan de recuperación y una conmutación por error para migrar varias máquinas virtuales entre las regiones. [Obtenga más información](../site-recovery-create-recovery-plans.md) sobre los planes de recuperación.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre otros escenarios de replicación en [¿Qué es Site Recovery?](../site-recovery-overview.md)
