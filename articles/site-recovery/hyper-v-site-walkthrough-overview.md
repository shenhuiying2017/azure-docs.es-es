---
title: "Replicación de máquinas virtuales de Hyper-V en Azure con Azure Site Recovery | Microsoft Docs"
description: "Describe cómo coordinar la replicación, la conmutación por error y la recuperación de máquinas virtuales de Hyper-V locales en Azure."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: efddd986-bc13-4a1d-932d-5484cdc7ad8d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: da10b213bc2543942b5ac77cf5c5d8547c00220c
ms.contentlocale: es-es
ms.lasthandoff: 06/23/2017

---

# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure"></a>Replicación de máquinas virtuales de Hyper-V (sin VMM) en Azure 

> [!div class="op_single_selector"]
> * [Portal de Azure](site-recovery-hyper-v-site-to-azure.md)
> * [Azure clásico](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell: administrador de recursos](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

Este artículo proporciona información general sobre los pasos necesarios para replicar máquinas virtuales de Hyper-V locales en Azure, mediante [Azure Site Recovery](site-recovery-overview.md) en Azure Portal. En esta implementación, las máquinas virtuales de Hyper-V no están administradas por System Center Virtual Machine Manager (VMM).


Cuando haya terminado de leer este artículo, publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="step-1-review-architecture-and-prerequisites"></a>Paso 1: revisión de la arquitectura y requisitos previos

Antes de empezar la implementación, revise la arquitectura del escenario y asegúrese de comprender todos los componentes que necesita implementar.

Vaya al [paso 1: revisión de la arquitectura](hyper-v-site-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>Paso 2: revisión de los requisitos previos

Asegúrese de satisfacer los requisitos previos de cada componente de la implementación:

- **Requisitos previos de Azure**: necesita una cuenta de Microsoft Azure, redes de Azure y cuentas de almacenamiento.
- **Requisitos previos de Hyper-V local**: asegúrese de que los hosts de Hyper-V están preparados para la implementación de Site Recovery.
- **Máquinas virtuales replicadas**: las máquinas virtuales que desee replicar deben cumplir con los requisitos de Azure.

Vaya al [paso 2: comprobación de los requisitos previos y las limitaciones](hyper-v-site-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>Paso 3: planeamiento de la capacidad

Si está realizando una implementación completa, debe averiguar qué recursos de replicación necesita. Hay un par de herramientas disponibles para ayudarle a hacerlo. Vaya al paso 2. Si está realizando una configuración rápida para probar el entorno, puede omitir este paso.

Vaya al [paso 3: planeamiento de la capacidad](hyper-v-site-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>Paso 4: planeamiento de las redes

Debe planear las redes para asegurarse de que las máquinas virtuales de Azure estén conectadas a redes después de que se produzca la conmutación por error y de que tengan la dirección IP correcta.

Vaya al [paso 4: planeamiento de las redes](hyper-v-site-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>Paso 5: preparación de los recursos de Azure

Antes de empezar hay que configurar las redes y el almacenamiento de Azure. Puede hacerlo durante la implementación, pero se recomienda hacerlo antes de empezar.

Vaya al [paso 5: preparación de Azure](hyper-v-site-walkthrough-prepare-azure.md)


## <a name="step-6-prepare-hyper-v"></a>Paso 6: preparación de Hyper-V

Asegúrese de que los servidores de Hyper-V cumplen los requisitos de implementación de Site Recovery.

Vaya al [paso 6: preparación de Hyper-V](hyper-v-site-walkthrough-prepare-hyper-v.md)

## <a name="step-7-set-up-a-vault"></a>Paso 7: configuración de un almacén

Debe configurar un almacén de Recovery Services para organizar y administrar la replicación. Al configurar el almacén, es necesario especificar lo que se quiere replicar y en dónde.

Vaya al [paso 7: creación de un almacén](hyper-v-site-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>Paso 8: configuración de los valores de origen y destino

Configurar el origen y de destino que se usa para la replicación. La configuración de origen incluye la adición de hosts de Hyper-V a un sitio de Hyper-V, la instalación del proveedor de Site Recovery y del agente de Recovery Services en cada host de Hyper-V y el registro del sitio en el almacén de Recovery Services.

Vaya al [paso 8: configuración de los valores de origen y destino](hyper-v-site-walkthrough-source-target.md)

## <a name="step-9-set-up-a-replication-policy"></a>Paso 9: configuración de una directiva de replicación

Configurar una directiva para especificar la configuración de replicación para máquinas virtuales de Hyper-V en el almacén.

Vaya al [paso 9: configuración de una directiva de replicación](hyper-v-site-walkthrough-replication.md)


## <a name="step-10-enable-replication"></a>Paso 10: habilitación de la replicación

Una vez se dispone de una directiva de replicación, después de la habilitación, se produce la replicación inicial de la máquina virtual.

Vaya al [paso 10: habilitación de la replicación](hyper-v-site-walkthrough-enable-replication.md)

## <a name="step-11-run-a-test-failover"></a>Paso 11: ejecución de una conmutación por error de prueba

Una vez que termina la replicación inicial y que se está ejecutando la replicación diferencial, puede ejecutar una conmutación por error de prueba para asegurarse de que todo funciona según lo previsto.

Vaya al [paso 11: ejecución de una conmutación por error de prueba](hyper-v-site-walkthrough-test-failover.md)

