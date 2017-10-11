---
title: "Revisión de la arquitectura para la replicación de Hyper-V a un sitio secundario con Azure Site Recovery | Microsoft Docs"
description: "Este artículo proporciona información general de la arquitectura usada para replicar máquinas virtuales de Hyper-V locales a un sistema sitio System Center VMM secundario con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 07099161-4cc7-4f32-8eb9-2a71bbf0750b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: b78cd0d5a5395873afaddc8856004775f447e8ea
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="step-1-review-the-architecture-for-hyper-v-replication-to-a-secondary-site"></a>Paso 1: Revisión de la arquitectura para la replicación de Hyper-V a un sitio secundario

En este artículo se describen los componentes y procesos que tienen lugar al replicar máquinas virtuales de Hyper-V locales en nubes de System Center Virtual Machine Manager (VMM) a un sitio VMM secundario utilizando el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.

Publique cualquier comentario que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="architectural-components"></a>Componentes de la arquitectura

Esto es lo que necesita para la replicación de máquinas virtuales Hyper-V en un sitio secundario VMM.

**Componente** | **Ubicación** | **Detalles**
--- | --- | ---
**Las tablas de Azure** | Suscripción de Azure. | Cree un almacén de Recovery Services en la suscripción de Azure, para orquestar y administrar la replicación entre ubicaciones de VMM.
**Servidor VMM** | Necesita una ubicación principal de VMM y otra secundaria. | Se recomienda un servidor VMM en el sitio principal y otro en el sitio secundario. 
**Servidor de Hyper-V** |  Uno o varios servidores host de Hyper-V en las nubes VMM principal y secundaria. | Los datos se replican entre los servidores host de Hyper-V principales y secundarios a través de la LAN o VPN mediante autenticación Kerberos o de certificado.  
**Máquinas virtuales de Hyper-V** | En el servidor de host de Hyper-V. | El servidor host de origen debe tener, como mínimo, una máquina virtual que se desee replicar.

## <a name="replication-process"></a>Proceso de replicación

1. Configure la cuenta de Azure, cree un almacén de Recovery Services y especifique lo que desea replicar.
2. Configure los ajustes de replicación del origen y el destino, que incluye la instalación del proveedor de Azure Site Recovery en servidores VMM y del agente de Microsoft Azure Recovery Services en cada host de Hyper-V.
3. Cree una directiva de replicación para la nube de VMM de origen. La directiva se aplica a todas las máquinas virtuales ubicadas en los hosts de la nube.
4. Habilite la replicación para cada VMM y se produce la replicación inicial de una máquina virtual de acuerdo a los ajustes que haya elegido.
5. Después de la replicación inicial, empieza la de los cambios diferenciales. Las marcas de revisión de un elemento se conservan en un archivo .hrl.


![De local a local](./media/vmm-to-vmm-walkthrough-architecture/arch-onprem-onprem.png)

## <a name="failover-and-failback-process"></a>Proceso de conmutación por error y conmutación por recuperación

1. Puede ejecutar una [conmutación por error](site-recovery-failover.md), planeada o no, entre sitios locales. Si ejecuta una conmutación por error planeada, las máquinas virtuales de origen se apagan para garantizar que no se pierdan datos.
2. Puede conmutar por error una única máquina o crear [planes de recuperación](site-recovery-create-recovery-plans.md) para organizar la conmutación por error de varias máquinas.
4. Si realiza una conmutación por error no planeada en un sitio secundario, tras ella las máquinas de la ubicación secundaria no están habilitadas para su protección ni replicación. Si ejecutó una conmutación por error planeada, tras ella las máquinas de la ubicación secundaria están protegidas.
5. Después, ejecute una conmutación por error para iniciar el acceso a la carga de trabajo desde la máquina virtual de la réplica.
6. Cuando el sitio principal esté disponible de nuevo, inicie la replicación inversa para replicar desde el sitio secundario al principal. La replicación inversa pone las máquinas virtuales en un estado protegido pero el centro de datos secundario sigue siendo la ubicación activa.
7. Para que el sitio principal se convierta de nuevo en la ubicación activa, debe iniciar una conmutación por error planeada desde el secundario al principal, seguida de otra replicación inversa.



## <a name="next-steps"></a>Pasos siguientes

Ir a [Paso 2: Revisión de los requisitos previos y las limitaciones](vmm-to-vmm-walkthrough-prerequisites.md).
