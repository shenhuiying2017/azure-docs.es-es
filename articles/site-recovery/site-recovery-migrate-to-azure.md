---
title: "Migración de máquinas virtuales locales y servidores físicos a Azure con Site Recovery | Microsoft Docs"
description: "En este tutorial se describe cómo migrar máquinas virtuales locales y servidores físicos a Azure con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: 423a1727efb0e1fd54eb0f8d5971ace3f8efc6cb
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="migrate-to-azure-with-site-recovery"></a>Migración a Azure con Site Recovery

Lea este artículo para obtener información sobre el uso del servicio [Azure Site Recovery](site-recovery-overview.md) para migrar servidores físicos y máquinas virtuales (VM) locales a máquinas virtuales de Azure.

## <a name="before-you-start"></a>Antes de comenzar

Vea este vídeo para obtener una introducción rápida de los pasos necesarios para migrar a Azure.
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]


## <a name="what-do-we-mean-by-migration"></a>¿Qué entendemos por migración?

Puede implementar Site Recovery para la replicación y migración de servidores físicos y máquinas virtuales locales.

- Al realizar la replicación, se configuran las máquinas locales para replicar de forma regular en Azure. A continuación, cuando se produce una interrupción, se conmutan por error las máquinas desde el sitio local a Azure y se accede a ellas desde allí. Cuando el sitio local está disponible de nuevo, puede realizar una conmutación por recuperación desde Azure.
- Cuando use Site Recovery para la migración, replique las máquinas locales en Azure. A continuación, conmútelas por error desde el sitio local a Azure y finalice el proceso de migración. No hay implicada ninguna conmutación por recuperación.  

## <a name="what-can-site-recovery-migrate"></a>¿Qué se puede migrar con Site Recovery?

Puede:

- **Migrar desde un entorno local**: migre las máquinas virtuales Hyper-V locales, las máquinas virtuales VMware y los servidores físicos a Azure. Después de la migración, las cargas de trabajo que se ejecutan en las máquinas locales se ejecutarán en las máquinas virtuales de Azure. 
- **Migrar dentro de Azure**: migre las máquinas virtuales de Azure entre regiones de Azure. 
- **Migrar AWS**: migre instancias de Windows AWS a máquinas virtuales IaaS de Azure. 

## <a name="migrate-from-on-premises-to-azure"></a>Migración desde un entorno local a Azure

Para migrar máquinas virtuales VMware locales, máquinas virtuales Hyper-V y servidores físicos, se siguen casi los mismos pasos que en una replicación completa. 


## <a name="migrate-between-azure-regions"></a>Migración entre regiones de Azure

Para migrar máquinas virtuales de Azure entre regiones, se siguen casi los mismos pasos que en una migración completa.

1. [Habilite la replicación](azure-to-azure-tutorial-enable-replication.md) de las máquinas que desea migrar.
2. [Ejecute una conmutación por error de prueba rápida](azure-to-azure-tutorial-dr-drill.md) para asegurarse de que todo funciona.
3. Después, [ejecute una conmutación por error no planeada](azure-to-azure-tutorial-failover-failback.md) con la opción **Migración completa**.
4. Después de completar la migración, puede [configurar la replicación para la recuperación ante desastres](site-recovery-azure-to-azure-after-migration.md), desde la región de Azure a la que ha migrado, en una región secundaria.



## <a name="migrate-aws-to-azure"></a>Migración de AWS a Azure

Puede migrar instancias de AWS a máquinas virtuales de Azure.
- En este escenario solo se admite la migración. En otras palabras, puede replicar las instancias de AWS y conmutarlas por error a Azure, pero no podrá realizar una conmutación por recuperación.
- Las instancias de AWS se controlan de la misma manera que los servidores físicos en lo que respecta a la migración. Configure un almacén de Recovery Services, implemente un servidor de configuración local para administrar la replicación, agréguelo al almacén y especifique la configuración de replicación.
- Habilite la replicación para las máquinas que desea migrar y ejecute una conmutación por error de prueba rápida. Después, ejecute una conmutación por error no planeada con la opción **Completar migración**.






## <a name="next-steps"></a>Pasos siguientes

- [Migración de máquinas locales a Azure](tutorial-migrate-on-premises-to-azure.md)
- [Migración de máquinas virtuales de una región de Azure a otra](site-recovery-migrate-azure-to-azure.md)
- [Migración de AWS a Azure](tutorial-migrate-aws-to-azure.md)
