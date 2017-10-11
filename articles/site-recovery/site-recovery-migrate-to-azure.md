---
title: "Migración a Azure con Site Recovery | Microsoft Docs"
description: "Este artículo proporciona información general acerca de la migración de máquinas virtuales y servidores físicos en Azure con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/05/2017
ms.author: raynew
ms.openlocfilehash: f4dfe430fba51bd009431ca72279a21be55e3a40
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="migrate-to-azure-with-site-recovery"></a>Migración a Azure con Site Recovery

Lea este artículo para obtener información general sobre el uso del servicio de Azure Site Recovery para la migración de máquinas virtuales y servidores físicos.

Site Recovery es un servicio de Azure que contribuye a su estrategia de BCDR mediante la coordinación de la replicación de servidores físicos locales y máquinas virtuales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error a la ubicación secundaria para mantener disponibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal. Más información en [¿Qué es Site Recovery?](site-recovery-overview.md) También usa Site Recovery para migrar las cargas de trabajo locales existentes a Azure con el fin de acelerar el viaje a la nube y aprovechar la matriz de características que Azure ofrece.

Para una descripción general de cómo realizar la migración, consulte este vídeo.
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]

En este artículo se describe cómo realizar la implementación en [Azure Portal](https://portal.azure.com). El [Portal de Azure clásico](https://manage.windowsazure.com/) se puede utilizar para conservar los almacenes de Site Recovery existentes, pero no podrá crear almacenes nuevos.

Publique sus comentarios en la parte inferior de este artículo. Formule cualquier pregunta técnica en el [Foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="what-do-we-mean-by-migration"></a>¿Qué entendemos por migración?

Puede implementar Site Recovery para la replicación de servidores físicos y máquinas virtuales locales en Azure o en un sitio secundario. Puede replicar máquinas, conmutarlas por error desde el sitio principal cuando se producen interrupciones, y conmutarlas por recuperación en el sitio principal cuando se recupera. Además de esto, puede usar Site Recovery para migrar máquinas virtuales y servidores físicos a Azure a fin de que los usuarios puedan tener acceso a ellos como máquinas virtuales de Azure. La migración conlleva la replicación y la conmutación por error desde el sitio principal a Azure y un gesto de migración completa.

## <a name="what-can-site-recovery-migrate"></a>¿Qué se puede migrar con Site Recovery?

Puede:

- Migre cargas de trabajo en ejecución en máquinas virtuales de Hyper-V locales, máquinas virtuales de VMware y servidores físicos, para que pasen a ejecutarse en máquinas virtuales de Azure. También puede hacer la replicación completa y la conmutación por recuperación en este escenario.
- Migre [máquinas virtuales de IaaS de Azure](site-recovery-migrate-azure-to-azure.md) entre regiones de Azure. Actualmente, en este escenario solo se admite la migración, lo que significa que no se admite la conmutación por recuperación.
- Migre [instancias de AWS Windows](site-recovery-migrate-aws-to-azure.md) a máquinas virtuales de IaaS de Azure. Actualmente, en este escenario solo se admite la migración, lo que significa que no se admite la conmutación por recuperación.

## <a name="migrate-on-premises-vms-and-physical-servers"></a>Migración de máquinas virtuales locales y servidores físicos

Para migrar máquinas virtuales de Hyper-V locales, máquinas virtuales de VMware y servidores físicos, se siguen casi los mismos pasos que los de una replicación normal.

1. Configuración del almacén de Recovery Services
2. Configure los servidores de administración necesarios (VMware, VMM o Hyper-V, según lo que desee migrar), agréguelos al almacén y especifique la configuración de replicación.
3. Habilite la replicación de las máquinas que desea migrar.
4. Después de la migración inicial, ejecute una conmutación por error de prueba rápida para asegurarse de que todo funciona como debería.
5. Después de comprobar que funciona el entorno de replicación, use una conmutación por error planeada o no planeada en función de lo que su escenario [admita](site-recovery-failover.md). Se recomienda usar una conmutación por error planificada cuando sea posible.
6. Para la migración, no es necesario ejecutar una conmutación por error ni eliminarla. En su lugar, seleccione la opción **Completar migración** de cada máquina que desea migrar.
     - En **Elementos replicados**, haga clic con el botón derecho en la máquina virtual y después haga clic en **Completar migración**. Haga clic en **Aceptar** para finalizar. Puede realizar un seguimiento del progreso de las propiedades de máquina virtual mediante la supervisión del trabajo Completar migración en **Trabajos de Site Recovery**.
     - La acción **Completar migración** termina el proceso de migración, quita la replicación de la máquina y se detiene la facturación de la máquina en Site Recovery.

![Completar migración](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

## <a name="migrate-between-azure-regions"></a>Migración entre regiones de Azure

Puede migrar máquinas virtuales de Azure entre regiones con Site Recovery. En este escenario solo se admite la migración. En otras palabras, puede replicar las máquinas virtuales de Azure y conmutarlas por error a otra región, pero no podrá realizar una conmutación por recuperación. En este escenario, configure un almacén de Recovery Services, implemente un servidor de configuración local para administrar la replicación, agréguelo al almacén y especifique la configuración de replicación. Habilite la replicación para las máquinas que desea migrar y ejecute una conmutación por error de prueba rápida. Después, ejecute una conmutación por error no planeada con la opción **Completar migración**.

## <a name="migrate-aws-to-azure"></a>Migración de AWS a Azure

Puede migrar instancias de AWS a máquinas virtuales de Azure. En este escenario solo se admite la migración. En otras palabras, puede replicar las instancias de AWS y conmutarlas por error a Azure, pero no podrá realizar una conmutación por recuperación. Las instancias de AWS se controlan de la misma manera que los servidores físicos en lo que respecta a la migración. Configure un almacén de Recovery Services, implemente un servidor de configuración local para administrar la replicación, agréguelo al almacén y especifique la configuración de replicación. Habilite la replicación para las máquinas que desea migrar y ejecute una conmutación por error de prueba rápida. Después, ejecute una conmutación por error no planeada con la opción **Completar migración**.




## <a name="next-steps"></a>Pasos siguientes

- [Migración de máquinas virtuales VMware a Azure](site-recovery-vmware-to-azure.md)
- [Migración de máquinas virtuales de Hyper-V de nubes VMM a Azure](site-recovery-vmm-to-azure.md)
- [Migración de máquinas virtuales de Hyper-V (sin VMM) a Azure](site-recovery-hyper-v-site-to-azure.md)
- [Migración de máquinas virtuales de Azure entre regiones de Azure](site-recovery-migrate-azure-to-azure.md)
- [Migración de instancias de AWS a Azure](site-recovery-migrate-aws-to-azure.md)
- [Preparación de máquinas migradas para habilitar la replicación](site-recovery-azure-to-azure-after-migration.md) en otra región para cubrir las necesidades de recuperación ante desastres.
- Empezar a proteger las cargas de trabajo mediante la [replicación de máquinas virtuales de Azure.](site-recovery-azure-to-azure.md)
