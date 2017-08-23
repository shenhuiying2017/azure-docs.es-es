---
title: "Replicación de máquinas virtuales de Hyper-V en un sitio secundario de VMM con Azure Site Recovery | Microsoft Docs"
description: "Proporciona información general sobre la replicación de máquinas virtuales de Hyper-V en un sitio secundario de VMM mediante Azure Portal."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 476ca82a-8f5c-4498-9dcf-e1011d60ed59
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: b422dd2cf23426de2f154a553b38509082536309
ms.contentlocale: es-es
ms.lasthandoff: 08/01/2017

---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>Replicación de máquinas virtuales de Hyper-V (en nubes VMM) en un sitio de VMM secundario

> [!div class="op_single_selector"]
> * [Portal de Azure](site-recovery-vmm-to-vmm.md)
> * [Portal clásico](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell: administrador de recursos](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

En este artículo se proporciona información general de los pasos necesarios para replicar máquinas virtuales de Hyper-V local administradas en nubes de System Center Virtual Machine Manager (VMM) en una ubicación secundaria de VMM mediante [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.

Publique cualquier comentario que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="step-1-review-the-scenario-architecture"></a>Paso 1: Revisión de la arquitectura del escenario

Antes de empezar la implementación, revise la arquitectura del escenario y asegúrese de comprender todos los componentes que debe implementar.

Vaya al [Paso 1: Revisión de la arquitectura](vmm-to-vmm-walkthrough-architecture.md).

## <a name="step-2-review-prerequisites-and-limitations"></a>Paso 2: Revisión de los requisitos previos y las limitaciones

Asegúrese de que comprende las limitaciones y los requisitos previos de la implementación.

**Requisitos previos de Azure**: necesita una suscripción de Microsoft Azure y un almacén de Azure Recovery Services para organizar y administrar la replicación.
**Servidores VMM locales y hosts de Hyper-V**: asegúrese de que los servidores VMM y los hosts de Hyper-V son compatibles y están preparados para la implementación de Site Recovery.

Vaya al [Paso 2: Comprobación de los requisitos previos y las limitaciones](vmm-to-vmm-walkthrough-prerequisites.md).

## <a name="step-3-plan-networking"></a>Paso 3: Planeamiento de redes

Debe llevar a cabo algunas tareas de planeación de redes para garantizar que puede configurar la asignación entre las redes de máquina virtual de VMM al implementar el escenario.

Vaya al [Paso 3: Planeamiento de las redes](vmm-to-vmm-walkthrough-network.md).


## <a name="step-4-prepare-vmm-and-hyper-v"></a>Paso 4: Preparación de VMM y de Hyper-V

Prepare los hosts de Hyper-V y los servidores VMM para la implementación de Site Recovery.

Vaya al [Paso 4: Preparación de los servidores locales](vmm-to-vmm-walkthrough-vmm-hyper-v.md).

## <a name="step-5-set-up-a-vault"></a>Paso 5: Configuración de un almacén

Configure un almacén de Recovery Services. El almacén contiene valores de configuración y organiza la replicación.

Vaya al [paso 5: Configuración de un almacén](vmm-to-vmm-walkthrough-create-vault.md).

## <a name="step-6-set-up-source-and-target-settings"></a>Paso 6: Configuración de los valores de origen y destino

Configure las ubicaciones de VMM para la replicación de origen y destino. Agregue los servidores VMM al almacén y descargue los archivos de instalación de los componentes de Site Recovery. Ejecute la configuración del proveedor de Azure Site Recovery en el servidor VMM. La configuración instala el proveedor en el servidor VMM y registra el servidor en el almacén. Instale el agente de Microsoft Azure Recovery Services en cada host de Hyper-V.

Vaya al [Paso 6: Configuración de los valores de origen y destino](vmm-to-vmm-walkthrough-source-target.md).

## <a name="step-7-configure-network-mapping"></a>Paso 7: Configuración de la asignación de red

Asigne redes de máquina virtual de VMM en las ubicaciones de origen y de destino. Tras la conmutación por error, las máquinas virtuales se crean en la red de destino que se asigna a la red de máquina virtual de origen en la que se encuentra la máquina virtual de Hyper-V de origen.

Vaya al [Paso 7: Configuración de la asignación de red](vmm-to-vmm-walkthrough-network-mapping.md).


## <a name="step-8-set-up-a-replication-policy"></a>Paso 8: Configurar una directiva de replicación

Especifique cómo se replicarán las máquinas virtuales entre las ubicaciones de VMM.

Vaya al [Paso 8: Configuración de una directiva de replicación](vmm-to-vmm-walkthrough-replication.md).


## <a name="step-9-enable-replication-for-vms"></a>Paso 9: Habilitación de la replicación para máquinas virtuales

Seleccione las máquinas virtuales que quiere replicar. Al habilitarse una máquina virtual para su replicación, se desencadena la replicación inicial en el sitio secundario, seguida de la replicación diferencial en curso.

Vaya al [Paso 9: Habilitación de la replicación](vmm-to-vmm-walkthrough-enable-replication.md).


## <a name="step-10-run-a-test-failover"></a>Paso 10: Ejecución de una conmutación por error de prueba

Ejecute una conmutación por error de prueba para asegurarse de que todo funcione de la forma esperada.

Vaya al [Paso 10: Ejecución de una conmutación por error de prueba](vmm-to-vmm-walkthrough-test-failover.md).

