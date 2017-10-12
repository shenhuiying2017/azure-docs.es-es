---
title: "Replicación de máquinas virtuales VMware en Azure con Azure Site Recovery | Microsoft Docs"
description: "Ofrece una introducción a los pasos necesarios para replicar cargas de trabajo que se ejecutan en máquinas virtuales VMware en Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: db6f5f95929503e82a529dba26b56af1edb0767f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-vmware-vms-to-azure-with-site-recovery"></a>Replicación de máquinas virtuales VMware en Azure con Site Recovery

En este artículo se ofrece información general sobre los pasos necesarios para replicar máquinas virtuales VMware en Azure con el servicio [Azure Site Recovery](site-recovery-overview.md) de Azure Portal.


![Proceso de implementación](./media/vmware-walkthrough-overview/vmware-to-azure-process.png)

**Figura 1: Resumen del proceso de implementación**

## <a name="step-1-review-architecture-and-prerequisites"></a>Paso 1: Revisión de la arquitectura y los requisitos previos

Antes de empezar la implementación, revise la arquitectura del escenario y asegúrese de comprender todos los componentes que necesita implementar.

Vaya al [paso 1: revisión de la arquitectura](vmware-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>Paso 2: revisión de los requisitos previos

Asegúrese de satisfacer los requisitos previos de cada componente de la implementación:

- **Requisitos previos de Azure**: necesita una cuenta de Microsoft Azure, redes de Azure y cuentas de almacenamiento.
- **Componentes locales de Site Recovery**: necesita una máquina que ejecute componentes locales de Site Recovery.
- **Requisitos previos de VMware**: necesita configurar cuentas para que Site Recovery pueda acceder a los servidores y las máquinas virtuales VMware.
- **Máquinas virtuales replicadas**: las máquinas virtuales que quiera replicar tienen que cumplir con los requisitos de Azure y tener el componente Mobility Service instalado.

Ir a [Paso 2: Revisión de los requisitos previos y las limitaciones](vmware-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>Paso 3: Planeamiento de la capacidad

Si está realizando una implementación completa, debe averiguar qué recursos de replicación necesita. Hay un par de herramientas disponibles para ayudarle a hacerlo. Vaya al paso 2. Si está realizando una configuración rápida para probar el entorno, puede omitir este paso.

Vaya al [paso 3: planeamiento de la capacidad](vmware-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>Paso 4: planeamiento de las redes

Debe planear las redes para asegurarse de que las máquinas virtuales de Azure estén conectadas a redes después de que se produzca la conmutación por error y de que tengan la dirección IP correcta.

Vaya al [paso 4: planeamiento de las redes](vmware-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>Paso 5: preparación de los recursos de Azure

Antes de empezar hay que configurar las redes y el almacenamiento de Azure. Puede hacerlo durante la implementación, pero se recomienda hacerlo antes de empezar.

Vaya al [Paso 5: Preparación de Azure](vmware-walkthrough-prepare-azure.md).


## <a name="step-6-prepare-vmware"></a>Paso 6: Preparación de VMware

Debe configurar las cuentas que Site Recovery va a usar:

- Acceda a los servidores de virtualización VMware para detectar automáticamente las máquinas virtuales.
- Acceda a las máquinas virtuales para instalar Mobility Service. Cada máquina virtual que quiera replicar debe tener instalado el agente de Mobility Service para que pueda habilitar la replicación en él.

Ir al [Paso 6: Preparación de VMware](vmware-walkthrough-prepare-vmware.md)

## <a name="step-7-set-up-a-vault"></a>Paso 7: Configuración de un almacén

Debe configurar un almacén de Recovery Services para organizar y administrar la replicación. Cuando configure el almacén, especifique lo que quiere replicar y en dónde.

Ir a [Paso 7: Configuración de un almacén](vmware-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>Paso 8: Configuración de los valores de origen y destino

Configure el origen y el destino que se usan para la replicación. La configuración del origen incluye la ejecución de la instalación unificada para instalar los componentes locales de Site Recovery.

Ir a [Paso 8: Configuración del origen y el destino](vmware-walkthrough-source-target.md)

## <a name="step-9-set-up-a-replication-policy"></a>Paso 9: Configuración de una directiva de replicación

Configure una directiva que especifique la configuración de la replicación para las máquinas virtuales VMware del almacén.

Ir al [Paso 9: Configuración de una directiva de replicación](vmware-walkthrough-replication.md)

## <a name="step-10-install-the-mobility-service"></a>Paso 10: Instalación de Mobility Service

Mobility Service debe instalarse en cada máquina que quiera replicar. Hay varias maneras de configurar el servicio, con instalación de inserción o de extracción.

Ir a [Paso 10: Instalación de Mobility Service](vmware-walkthrough-install-mobility.md)

## <a name="step-11-enable-replication"></a>Paso 11: Habilitación de la replicación

Una vez que Mobility Service se está ejecutando en una máquina virtual, puede habilitar su replicación. Después de habilitarla, se produce la replicación inicial de la máquina virtual.

Ir a [Paso 11: Habilitación de la replicación](vmware-walkthrough-enable-replication.md)

## <a name="step-12-run-a-test-failover"></a>Paso 12: Ejecución de una conmutación por error de prueba

Una vez que termina la replicación inicial y se está ejecutando la replicación diferencial, puede ejecutar una conmutación por error de prueba para asegurarse de que todo funciona según lo previsto.

Ir a [Paso 12: Ejecución de una conmutación por error de prueba](vmware-walkthrough-test-failover.md)
