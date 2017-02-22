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
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: f82634af931a1e9a9646c5631ebd0e5923a0adcc
ms.openlocfilehash: cbb6de4587871c40c9d4e97c9fb2a88eab4945a6


---
# <a name="migrate-to-azure-with-site-recovery"></a>Migración a Azure con Site Recovery

Lea este artículo para obtener información general sobre el uso del servicio de Azure Site Recovery para la migración de máquinas virtuales y servidores físicos.

Las organizaciones necesitan una estrategia de recuperación ante desastres y continuidad empresarial (BCDR) que determine cómo seguirán en funcionamiento y disponibles las aplicaciones, las cargas de trabajo y los datos durante los tiempos de inactividad planeados y no planeados, y cómo recuperar las condiciones de funcionamiento normales lo antes posible. Su estrategia de BCDR se centra en soluciones que mantengan los datos empresariales seguros y recuperables, y garanticen que las cargas de trabajo estarán disponibles continuamente en caso de desastre.

Site Recovery es un servicio de Azure que contribuye a su estrategia de BCDR mediante la coordinación de la replicación de servidores físicos locales y máquinas virtuales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error a la ubicación secundaria para mantener disponibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal. Más información en [¿Qué es Site Recovery?](site-recovery-overview.md)

En este artículo se describe cómo realizar la implementación en [Azure Portal](https://portal.azure.com). El [Portal de Azure clásico](https://manage.windowsazure.com/) se puede utilizar para conservar los almacenes de Site Recovery existentes, pero no podrá crear almacenes nuevos.

Publique sus comentarios en la parte inferior de este artículo. Formule cualquier pregunta técnica en el [Foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="what-do-we-mean-by-migration"></a>¿Qué entendemos por migración?

Puede implementar Site Recovery para la replicación completa de máquinas virtuales locales y servidores físicos, en Azure o en un sitio secundario. Replique máquinas, realice la conmutación por error de estas desde el sitio principal cuando se produzcan interrupciones y realice la conmutación por recuperación al sitio principal una vez que se recupere. Además de la replicación completa, puede usar Site Recovery para migrar máquinas virtuales y servidores físicos a Azure, de forma que los usuarios puedan acceder a la carga de trabajo de la máquina desde máquinas virtuales de Azure. La migración conlleva la replicación y la conmutación por error desde el sitio principal a Azure. Sin embargo, a diferencia de la replicación completa, no incluye un mecanismo de conmutación por recuperación.

## <a name="what-can-site-recovery-migrate"></a>¿Qué se puede migrar con Site Recovery?

Puede:

- Migre cargas de trabajo en ejecución en máquinas virtuales de Hyper-V locales, máquinas virtuales de VMware y servidores físicos, para que pasen a ejecutarse en máquinas virtuales de Azure. También puede hacer la replicación completa y la conmutación por recuperación en este escenario.
- Migre [máquinas virtuales de IaaS de Azure](site-recovery-migrate-azure-to-azure.md) entre regiones de Azure. Actualmente, en este escenario solo se admite la migración, lo que significa que no se admite la conmutación por recuperación.
- Migre [instancias de AWS Windows](site-recovery-migrate-aws-to-azure.md) a máquinas virtuales de IaaS de Azure. Actualmente, en este escenario solo se admite la migración, lo que significa que no se admite la conmutación por recuperación.

## <a name="migrate-on-premises-vms-and-physical-servers"></a>Migración de máquinas virtuales locales y servidores físicos

Para migrar máquinas virtuales de Hyper-V locales, máquinas virtuales de VMware y servidores físicos, se siguen casi los mismos pasos que los de una replicación normal. Configure un almacén de Recovery Services, configure los servidores de administración necesarios (según lo que desee migrar), agréguelos en el almacén y especifique la configuración de replicación. Habilite la replicación para las máquinas que desea migrar y ejecute una conmutación por error de prueba rápida para asegurarse de que todo funciona como debería.

Después de comprobar que funciona el entorno de replicación, use una conmutación por error planeada o no planeada en función de lo que su escenario [admita](site-recovery-failover.md#failover-and-failback). Para la migración, no es necesario ejecutar una conmutación por error ni eliminar nada. En su lugar, seleccione la opción **Completar migración** de cada máquina que desea migrar. La acción **Completar migración** termina el proceso de migración, quita la replicación de la máquina y se detiene la facturación de la máquina en Site Recovery.

## <a name="migrate-between-azure-regions"></a>Migración entre regiones de Azure

Puede migrar máquinas virtuales de Azure entre regiones con Site Recovery. En este escenario solo se admite la migración. En otras palabras, puede replicar las máquinas virtuales de Azure y conmutarlas por error a otra región, pero no podrá realizar una conmutación por recuperación. En este escenario, configure un almacén de Recovery Services, implemente un servidor de configuración local para administrar la replicación, agréguelo al almacén y especifique la configuración de replicación. Habilite la replicación para las máquinas que desea migrar y ejecute una conmutación por error de prueba rápida. Después, ejecute una conmutación por error no planeada con la opción **Completar migración**.

## <a name="migrate-aws-to-azure"></a>Migración de AWS a Azure

Puede migrar instancias de AWS a máquinas virtuales de Azure. En este escenario solo se admite la migración. En otras palabras, puede replicar las instancias de AWS y conmutarlas por error a Azure, pero no podrá realizar una conmutación por recuperación. Las instancias de AWS se controlan de la misma manera que los servidores físicos en lo que respecta a la migración. Configure un almacén de Recovery Services, implemente un servidor de configuración local para administrar la replicación, agréguelo al almacén y especifique la configuración de replicación. Habilite la replicación para las máquinas que desea migrar y ejecute una conmutación por error de prueba rápida. Después, ejecute una conmutación por error no planeada con la opción **Completar migración**.




## <a name="next-steps"></a>Pasos siguientes

- [Migración de máquinas virtuales VMware a Azure](site-recovery-vmware-to-azure.md)
- [Migración de servidores físicos a Azure](site-recovery-vmware-to-azure.md)
- [Migración de máquinas virtuales de Hyper-V de nubes VMM a Azure](site-recovery-vmm-to-azure.md)
- [Migración de máquinas virtuales de Hyper-V (sin VMM) a Azure](site-recovery-hyper-v-site-to-azure.md)
- [Migración de máquinas virtuales de Azure entre regiones de Azure](site-recovery-migrate-azure-to-azure.md)
- [Migración de instancias de AWS a Azure](site-recovery-migrate-aws-to-azure.md)



<!--HONumber=Jan17_HO4-->


