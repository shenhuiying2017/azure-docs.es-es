---
title: "Replicación de máquinas virtuales de Hyper-V en nubes de VMM en Azure con Azure Site Recovery | Microsoft Docs"
description: "Proporciona información general para replicar máquinas virtuales de Hyper-V en nubes VMM en Azure mediante el servicio Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: af68d21184c137b2b0f1bb4c1afb9bf507e8332d
ms.contentlocale: es-es
ms.lasthandoff: 07/26/2017

---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-site-recovery-in-the-azure-portal"></a>Replicación de máquinas virtuales de Hyper-V que están en nubes VMM en Azure mediante Site Recovery en Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmm-to-azure.md)
> * [Azure clásico](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell clásico](site-recovery-deploy-with-powershell.md)


En este artículo se proporciona información general de los pasos necesarios para replicar máquinas virtuales de Hyper-V local administradas en nubes de System Center Virtual Machine Manager (VMM) en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md) en Azure Portal.

Publique cualquier comentario que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="step-1-review-the-scenario-architecture"></a>Paso 1: Revisión de la arquitectura del escenario

Antes de empezar la implementación, revise la arquitectura del escenario y asegúrese de comprender todos los componentes que necesita implementar.

Vaya al [paso 1: revisión de la arquitectura](vmm-to-azure-walkthrough-architecture.md)

## <a name="step-2-review-prerequisites-and-limitations"></a>Paso 2: Revisión de los requisitos previos y las limitaciones

Asegúrese de que comprende las limitaciones y los requisitos previos de implementación.

**Requisitos previos de Azure**: necesita una cuenta de Microsoft Azure, redes de Azure y cuentas de almacenamiento.
**Servidores VMM locales y hosts de Hyper-V**: asegúrese de que los servidores VMM y los hosts de Hyper-V son compatibles y están preparados para la implementación de Site Recovery.
**Máquinas virtuales replicadas**: compruebe que las máquinas virtuales que desea replicar cumplen con los requisitos de Azure.

Vaya a [Paso 2: Comprobación de los requisitos previos y las limitaciones](vmm-to-azure-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>Paso 3: planeamiento de la capacidad

Si está realizando una implementación completa, debe averiguar qué recursos de replicación necesita. Hay un par de herramientas disponibles para ayudarle a hacerlo. Si está realizando una configuración rápida para probar el entorno, puede omitir este paso.

Vaya a [Paso 3: Planear la capacidad](vmm-to-azure-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>Paso 4: planeamiento de las redes

Debe realizar planeamiento de red para asegurarse de que puede configurar la asignación de red al implementar el escenario, de que las máquinas virtuales de Azure se conectarán a las redes virtuales de Azure tras producirse la conmutación por error y de que son direcciones IP adecuadas asignadas.

Vaya al [paso 4: planeamiento de las redes](vmm-to-azure-walkthrough-network.md)


## <a name="step-5-prepare-azure-resources"></a>Paso 5: preparación de los recursos de Azure

Configure almacenamiento, redes y una cuenta de Azure. Puede hacerlo durante la implementación, pero se recomienda hacerlo antes de empezar.

Vaya al [Paso 5: Preparación de Azure](vmm-to-azure-walkthrough-prepare-azure.md).

## <a name="step-6-prepare-vmm-and-hyper-v"></a>Paso 6: Preparación de VMM y Hyper-V

Prepare los hosts de Hyper-V y los servidores VMM locales para la implementación de Site Recovery.

Ir a [Step 6: Prepare on-premises servers](vmm-to-azure-walkthrough-vmm-hyper-v.md) (Paso 6: Preparación de servidores locales)

## <a name="step-7-set-up-a-vault"></a>Paso 7: configuración de un almacén

Configure un almacén de Recovery Services. El almacén contiene valores de configuración y organiza la replicación.

[Step 7: Set up a vault](vmm-to-azure-walkthrough-create-vault.md) (Paso 7: Configuración de un almacén)

## <a name="step-8-configure-source-and-target-settings"></a>Paso 8: configuración de los valores de origen y destino

Configure las ubicaciones de replicación de origen y destino. Agregue el servidor VMM al almacén y descargue los archivos de instalación para los componentes de Site Recovery. Ejecute la configuración del proveedor de Azure Site Recovery en el servidor VMM. La configuración instala el proveedor en el servidor VMM y registra el servidor en el almacén. Instala el agente de Azure Recovery Services en cada host de Hyper-V.

Ir a [Step 8: Configure source and target settings](vmm-to-azure-walkthrough-source-target.md) (Paso 8: Configuración de los valores de origen y destino)

## <a name="step-9-configure-network-mapping"></a>Paso 9: Configuración de la asignación de red

Asigne redes de máquinas virtuales de VMM locales a redes virtuales de Azure. Tras la conmutación por error, las máquinas virtuales de Azure se crean en la red de Azure que se asigna a la red de máquinas virtuales local en la que se encuentra Hyper-V de origen.

Ir a [Step 9: Configure network mapping](vmm-to-azure-walkthrough-network-mapping.md) (Paso 9: Configuración de la asignación de red)


## <a name="step-10-set-up-a-replication-policy"></a>Paso 10: Configuración de una directiva de replicación

Especifique cómo se replicarán en Azure las máquinas virtuales locales.

Ir a [Step 10: Set up a replication policy](vmm-to-azure-walkthrough-replication.md) (Paso 10: Configuración de una directiva de replicación)


## <a name="step-11-enable-replication-for-vms"></a>Paso 11: Habilitación de la replicación para máquinas virtuales

Seleccione las máquinas virtuales que desea replicar. Al habilitarse una máquina virtual para su replicación, se desencadena la replicación inicial en Azure, seguida de la replicación diferencial en curso.

Ir a [Paso 11: Habilitación de la replicación](vmm-to-azure-walkthrough-enable-replication.md)


## <a name="step-12-run-a-test-failover"></a>Paso 12: Ejecución de una conmutación por error de prueba

Ejecute una conmutación por error de prueba para asegurarse de que todo funcione de la forma esperada.

Ir a [Paso 12: Ejecución de una conmutación por error de prueba](vmm-to-azure-walkthrough-test-failover.md)



