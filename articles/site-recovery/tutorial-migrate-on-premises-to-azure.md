---
title: "Migración de máquinas locales a Azure con Azure Site Recovery | Microsoft Docs"
description: "En este artículo se describe cómo migrar máquinas locales a Azure mediante Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/16/2017
ms.author: raynew
ms.openlocfilehash: bbee77e669f49bdebb57121df8672a9253945b3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migración de máquinas locales a Azure

El servicio [Azure Site Recovery](site-recovery-overview.md) administra y coordina la replicación, la conmutación por error y la conmutación por recuperación de máquinas locales y máquinas virtuales (VM) de Azure.

En este tutorial se muestra cómo migrar máquinas virtuales locales y servidores físicos en Azure con Site Recovery. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar los requisitos previos de la implementación
> * Crear un almacén de Recovery Services para Site Recovery
> * Implementar servidores de administración locales
> * Configurar una directiva de replicación y habilitar la replicación
> * Ejecutar una exploración en profundidad de recuperación ante desastres para asegurarse de que todo funciona
> * Ejecutar una conmutación por error única en Azure

## <a name="overview"></a>Información general

Para migrar una máquina debe habilitar la replicación en esta y conmutarla por error a Azure.


## <a name="prerequisites"></a>Requisitos previos

Esto es lo que debe hacer en este tutorial.

- [Preparar](tutorial-prepare-azure.md) recursos de Azure, que incluyan una suscripción de Azure, una red virtual de Azure y una cuenta de almacenamiento.
- [Preparar](tutorial-prepare-on-premises-vmware.md) máquinas virtuales y servidores VMware locales de VMware.
- Tenga en cuenta que no se admiten los dispositivos exportados por controladores paravirtualizados.


## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Recovery Services

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Selección de un objetivo de protección

Seleccione aquello que desea replicar y la ubicación donde se va a realizar la replicación.
1. Haga clic en **Almacenes de Recovery Services** > almacén.
2. En el menú de recursos, haga clic en **Site Recovery** > **Preparar la infraestructura** > **Objetivo de protección**.
3. En **Objetivo de protección**, seleccione:
    - **VMware**: seleccione **To Azure (En Azure)** > **Yes, with VMware vSphere Hypervisor** (Sí, con VMware vSphere Hypervisor).
    - En **Máquina física**: seleccione **To Azure (En Azure)** > **No virtualizado/Otro**.
    - **Hyper-V**: seleccione **To Azure (En Azure)** > **Yes, with Hyper-V (Sí, con Hyper-V)**.


## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen

- [Configure](tutorial-vmware-to-azure.md#set-up-the-source-environment) el entorno de origen para las máquinas virtuales de VMware.
- [Configure](tutorial-physical-to-azure.md#set-up-the-source-environment) el entorno de origen para los servidores físicos.
- [Configure](tutorial-hyper-v-to-azure.md#set-up-the-source-environment) el entorno de origen para las máquinas virtuales de Hyper-V.

## <a name="set-up-the-target-environment"></a>Configuración del entorno de destino

Seleccione y compruebe los recursos de destino.

1. Haga clic en **Preparar infraestructura** > **Destino** y seleccione la suscripción de Azure que quiere usar.
2. Especifique el modelo de implementación de destino.
3. Site Recovery comprueba que tiene una o más redes y cuentas de Azure Storage compatibles.

## <a name="create-a-replication-policy"></a>Creación de una directiva de replicación

- [Configure una directiva de replicación](tutorial-vmware-to-azure.md#create-a-replication-policy) para las máquinas virtuales de VMware.


## <a name="enable-replication"></a>Habilitar replicación

- [Habilite la replicación](tutorial-vmware-to-azure.md#enable-replication) para las máquinas virtuales de VMware.


## <a name="run-a-test-migration"></a>Ejecutar una migración de prueba

Ejecute una [conmutación por error de prueba](tutorial-dr-drill-azure.md) en Azure para asegurarse de que todo funciona de la forma esperada.


## <a name="migrate-to-azure"></a>Migración a Azure

Ejecute una conmutación por error para las máquinas que desea migrar.

1. En **Configuración** > **Elementos replicados**, haga clic en la máquina > **Conmutación por error**.
2. En **Conmutación por error**, seleccione un **punto de recuperación** en el que realizar la conmutación por error. Seleccione el punto de recuperación más reciente.
3. La configuración de la clave de cifrado no es importante para este escenario.
4. Seleccione **Shut down machine before beginning failover** (Apagar la máquina antes de comenzar la conmutación por error) si desea que Site Recovery intente apagar las máquinas virtuales de origen antes de desencadenar la conmutación por error. La conmutación por error continúa aunque se produzca un error de cierre. Puede seguir el progreso de la conmutación por error en la página **Trabajos**.
5. Compruebe que la máquina virtual de Azure aparece en Azure según lo previsto.
6. En **Elementos replicados**, haga clic con el botón derecho en la máquina virtual > **Completar migración**. Con esta acción se completa el proceso de migración, y se detienen la replicación de la máquina virtual y la facturación de Site Recovery para la VM.

    ![Completar migración](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **No cancele una conmutación por error en curso**: antes de iniciar la conmutación por error, se detiene la replicación de la máquina virtual. Si se cancela una conmutación por error en curso, la conmutación por error se detiene, pero no replica la máquina virtual de nuevo.

En algunos escenarios, la conmutación por error requiere un procesamiento adicional que tarda aproximadamente de ocho a diez minutos en completarse. Puede observar tiempos de conmutación por error de prueba más largos en los servidores físicos, las máquinas de VMware Linux, las máquinas virtuales de VMware que no tienen el servicio DHCP habilitado y las máquinas virtuales de VMware que no tienen los siguientes controladores de arranque: storvsc, vmbus, storflt, intelide y atapi.


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Replicación de máquinas virtuales de Azure en otra región después de la migración a Azure](site-recovery-azure-to-azure-after-migration.md)
