---
title: "Migración de máquinas locales a Azure con Azure Site Recovery | Microsoft Docs"
description: "En este artículo se describe cómo migrar máquinas locales a Azure mediante Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/07/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ee9397406cbca21d8bd53019d9daac5a037f508c
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migración de máquinas locales a Azure

Además de utilizar el servicio [Azure Site Recovery](site-recovery-overview.md) para administrar y coordinar la recuperación ante desastres de máquinas locales y máquinas virtuales de Azure para garantizar la continuidad empresarial y la recuperación ante desastres (BCDR), también puede usar Site Recovery para administrar la migración de máquinas locales a Azure.


En este tutorial se muestra cómo migrar máquinas virtuales locales y servidores físicos a Azure. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Selección de un objetivo de replicación
> * Configure el entorno de origen y destino
> * Configurar una directiva de replicación
> * Habilitar replicación
> * Ejecute una migración de prueba para asegurarse de que todo funciona de la forma esperada
> * Ejecutar una conmutación por error única en Azure

Este es el tercer tutorial de una serie. En este tutorial se da por hecho que ya ha realizado las tareas de los tutoriales anteriores:

1. [Preparación de Azure](tutorial-prepare-azure.md)
2. Prepare los servidores de [VMware](tutorial-prepare-on-premises-vmware.md) o Hyper-V locales.

Antes de empezar, le recomendamos que revise las arquitecturas del escenario de recuperación ante desastres de [VMware](concepts-vmware-to-azure-architecture.md) o [Hyper-V](concepts-hyper-v-to-azure-architecture.md).


## <a name="prerequisites"></a>requisitos previos

No se admiten dispositivos que se hayan exportado con controladores paravirtualizados.


## <a name="create-a-recovery-services-vault"></a>Creación de un almacén de Recovery Services

1. Inicie sesión en [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Haga clic en **Nuevo** > **Supervisión y administración** > **Backup y Site Recovery**.
3. En el apartado **Nombre**, especifique el nombre descriptivo **ContosoVMVault**. Si tiene más de una suscripción, seleccione la apropiada.
4. Cree un grupo de recursos denominado **ContosoRG**.
5. Especifique una región de Azure. Para comprobar las regiones admitidas, consulte la disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Para acceder rápidamente al almacén desde el panel, haga clic en **Anclar al panel** y, después, en **Crear**.

   ![Almacén nuevo](./media/tutorial-migrate-on-premises-to-azure/onprem-to-azure-vault.png)

El nuevo almacén se agrega al **Panel**, en **Todos los recursos**, y en la página principal **Almacenes de Recovery Services**.



## <a name="select-a-replication-goal"></a>Selección de un objetivo de replicación

Seleccione aquello que desea replicar y la ubicación donde se va a realizar la replicación.
1. Haga clic en **Almacenes de Recovery Services** > almacén.
2. En el menú de recursos, haga clic en **Site Recovery** > **Preparar la infraestructura** > **Objetivo de protección**.
3. En **Objetivo de protección**, seleccione el contenido que quiera migrar.
    - **VMware**: seleccione **To Azure (En Azure)** > **Yes, with VMware vSphere Hypervisor** (Sí, con VMware vSphere Hypervisor).
    - En **Máquina física**: seleccione **To Azure (En Azure)** > **No virtualizado/Otro**.
    - **Hyper-V**: seleccione **To Azure (En Azure)** > **Yes, with Hyper-V (Sí, con Hyper-V)**. Si VMM administra las máquinas virtuales de Hyper-V, seleccione **Sí**.


## <a name="set-up-the-source-environment"></a>Configuración del entorno de origen

- [Configure](tutorial-vmware-to-azure.md#set-up-the-source-environment) el entorno de origen para las máquinas virtuales de VMware.
- [Configure](tutorial-physical-to-azure.md#set-up-the-source-environment) el entorno de origen para los servidores físicos.
- [Configure](tutorial-hyper-v-to-azure.md#set-up-the-source-environment) el entorno de origen para las máquinas virtuales de Hyper-V.

## <a name="set-up-the-target-environment"></a>Configuración del entorno de destino

Seleccione y compruebe los recursos de destino.

1. Haga clic en **Preparar infraestructura** > **Destino** y seleccione la suscripción de Azure que quiere usar.
2. Especifique el modelo de implementación de Resource Manager.
3. Site Recovery comprueba que tiene una o más redes y cuentas de Azure Storage compatibles.

## <a name="set-up-a-replication-policy"></a>Configurar una directiva de replicación

- [Configure una directiva de replicación](tutorial-vmware-to-azure.md#create-a-replication-policy) para las máquinas virtuales de VMware.
- [Configure una directiva de replicación](tutorial-physical-to-azure.md#create-a-replication-policy) para los servidores físicos.
- [Configure una directiva de replicación](tutorial-hyper-v-to-azure.md#set-up-a-replication-policy) para las máquinas virtuales de Hyper-VM.


## <a name="enable-replication"></a>Habilitar replicación

- [Habilite la replicación](tutorial-vmware-to-azure.md#enable-replication) para las máquinas virtuales de VMware.
- [Habilite la replicación](tutorial-physical-to-azure.md#enable-replication) de los servidores físicos.
- [Habilite la replicación](tutorial-hyper-v-to-azure.md#enable-replication) de las máquinas virtuales de Hyper-V.


## <a name="run-a-test-migration"></a>Ejecutar una migración de prueba

Ejecute una [conmutación por error de prueba](tutorial-dr-drill-azure.md) en Azure para asegurarse de que todo funciona de la forma esperada.


## <a name="migrate-to-azure"></a>Migración a Azure

Ejecute una conmutación por error para las máquinas que desea migrar.

1. En **Configuración** > **Elementos replicados**, haga clic en la máquina > **Conmutación por error**.
2. En **Conmutación por error**, seleccione un **punto de recuperación** en el que realizar la conmutación por error. Seleccione el punto de recuperación más reciente.
3. La configuración de la clave de cifrado no es importante para este escenario.
4. Seleccione **Apague la máquina antes de comenzar con la conmutación por error**. Site Recovery intentará apagar las máquinas virtuales de origen antes de desencadenar la conmutación por error. La conmutación por error continúa aunque se produzca un error de cierre. Puede seguir el progreso de la conmutación por error en la página **Trabajos**.
5. Compruebe que la máquina virtual de Azure aparece en Azure según lo previsto.
6. En **Elementos replicados**, haga clic con el botón derecho en la máquina virtual > **Completar migración**. Con esta acción se completa el proceso de migración, y se detienen la replicación de la máquina virtual y la facturación de Site Recovery para la VM.

    ![Completar migración](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **No cancele una conmutación por error en curso**: antes de iniciar la conmutación por error, se detiene la replicación de la máquina virtual. Si se cancela una conmutación por error en curso, la conmutación por error se detiene, pero no se replica la máquina virtual de nuevo.

En algunos escenarios, la conmutación por error requiere un procesamiento adicional que tarda aproximadamente de ocho a diez minutos en completarse. Puede observar tiempos de conmutación por error de prueba más largos en los servidores físicos, las máquinas de VMware Linux, las máquinas virtuales de VMware que no tienen el servicio DHCP habilitado y las máquinas virtuales de VMware que no tienen los siguientes controladores de arranque: storvsc, vmbus, storflt, intelide y atapi.


## <a name="next-steps"></a>pasos siguientes

En este tutorial se migran máquinas virtuales locales a máquinas virtuales de Azure. Ya puede configurar la opción de recuperación ante desastres para las máquinas virtuales de Azure.

> [!div class="nextstepaction"]
> [Configurar la recuperación ante desastres](site-recovery-azure-to-azure-after-migration.md) para máquinas virtuales de Azure después de realizar la migración de un sitio local.
