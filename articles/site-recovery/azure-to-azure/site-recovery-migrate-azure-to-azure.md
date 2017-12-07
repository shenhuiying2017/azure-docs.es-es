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
ms.date: 08/31/2017
ms.author: raynew
ms.openlocfilehash: 805582d89ee906e21fbe588e895ce0fe3a926a66
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migración de máquinas virtuales de IaaS de Azure entre regiones de Azure con Azure Site Recovery

Use este artículo si desea migrar máquinas virtuales de Azure entre regiones de Azure, con el servicio [Site Recovery](../site-recovery-overview.md).

## <a name="prerequisites"></a>Requisitos previos

Necesita las máquinas virtuales de IaaS que desea migrar.

## <a name="deployment-steps"></a>Pasos de implementación

1. [Cree un almacén](azure-to-azure-tutorial-enable-replication.md#create-a-vault).
2. [Habilite la replicación](azure-to-azure-tutorial-enable-replication.md#enable-replication) en las máquinas virtuales que desee migrar y elija Azure como el origen. Actualmente, no se admite la replicación nativa de máquinas virtuales de Azure mediante discos administrados.
3. [Ejecute una conmutación por error](../site-recovery-failover.md). Una vez completada la replicación inicial, puede ejecutar una conmutación por error desde una región de Azure a otra. Si lo desea, puede crear un plan de recuperación y una conmutación por error para migrar varias máquinas virtuales entre las regiones. [Obtenga más información](../site-recovery-create-recovery-plans.md) sobre los planes de recuperación.

## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre otros escenarios de replicación en [¿Qué es Azure Site Recovery?](../site-recovery-overview.md)
