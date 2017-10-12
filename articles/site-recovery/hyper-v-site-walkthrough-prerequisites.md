---
title: "Revisión de los requisitos previos para la replicación de Hyper-V en Azure (sin System Center VMM) mediante Azure Site Recovery | Microsoft Docs"
description: "Describe los requisitos previos para configurar la replicación, la conmutación por error y la recuperación de máquinas virtuales de Hyper-V locales en Azure con Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7ef3fb46-52f5-4c8a-b1a1-658c2305762a
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: cbb5d3598ef91512991d7d1e9f854eb12980752b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-2-review-the-prerequisites-for-hyper-v-without-vmm-to-azure-replication"></a>Paso 2: revisión de los requisitos previos para la replicación de Hyper-V (sin VMM) en Azure

En la tabla se resumen los requisitos previos.


**Requisito previo** | **Detalles** 
--- | --- 
**Las tablas de Azure** | Obtenga información sobre los [requisitos de Azure](site-recovery-prereq.md#azure-requirements)
**Servidores locales** | [Más información](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-to-azure-no-vmm) sobre los requisitos de los hosts de Hyper-V locales.
**Máquinas virtuales de Hyper-V locales** | Las máquinas virtuales que desee replicar deben ejecutar un [sistema operativo compatible](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) y cumplir los [requisitos previos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**Direcciones URL de Azure** | Los hosts de Hyper-V necesitan tener acceso a estas direcciones URL:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Si tiene reglas de firewall basadas en direcciones IP, asegúrese de que permitan la comunicación con Azure.<br/></br> Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).<br/></br> Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU. (se usan para Access Control y para Identity Management).



## <a name="next-steps"></a>Pasos siguientes

- Si va a realizar una implementación completa, vaya al [Paso 3: Planeamiento de la capacidad](hyper-v-site-walkthrough-capacity.md).
- Si va a realizar una implementación de prueba sencilla, vaya al [Paso 4: Planeamiento de las redes](hyper-v-site-walkthrough-network.md).
