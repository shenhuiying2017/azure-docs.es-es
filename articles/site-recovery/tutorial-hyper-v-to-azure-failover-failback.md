---
title: "Conmutación por error y conmutación por recuperación de máquinas virtuales de Hyper-V replicadas en Azure con Site Recovery | Microsoft Docs"
description: "Obtenga información sobre cómo conmutar por error máquinas virtuales de Hyper-V en Azure y conmutar por recuperación en el sitio local con Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2017
ms.author: raynew
ms.openlocfilehash: 390fe98bc718da4fe07f580bbf1dcbffbf8fc1ba
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Conmutar por error y conmutar por recuperación máquinas virtuales de Hyper-V replicadas en Azure

En este tutorial se describe cómo conmutar por error una máquina virtual de Hyper-V en Azure. Después de que ha conmutado por error, conmutará por recuperación a su sitio local cuando esté disponible. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Verificar las propiedades de máquinas virtuales de Hyper-V para comprobar que se cumplen los requisitos de Azure.
> * Ejecutar una conmutación por error en Azure.
> * Volver a proteger máquinas virtuales de Azure en el sitio local.
> * Conmutación por recuperación de Azure al entorno local
> * Volver a proteger las máquinas virtuales locales para iniciar de nuevo la replicación en Azure

Este es el quinto tutorial de una serie. En este tutorial se da por hecho que ya ha realizado las tareas de los tutoriales anteriores.

1. [Preparación de Azure](tutorial-prepare-azure.md)
2. [Preparación de VMware local](tutorial-prepare-on-premises-hyper-v.md)
3. Configuración de la recuperación ante desastres de [máquinas virtuales de Hyper-V](tutorial-hyper-v-to-azure.md) o de [máquinas virtuales de Hyper-V administradas en nubes VMM de System Center](tutorial-hyper-v-vmm-to-azure.md)
4. [Exploración de la recuperación ante desastres](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Preparación de la conmutación por error y la conmutación por recuperación

Asegúrese de que no hay instantáneas en la máquina virtual y de que la máquina virtual local se ha desactivado durante la reprotección. Esto ayuda a garantizar la coherencia de datos durante la replicación. No encienda la máquina virtual una vez finalizada la reprotección. 

La conmutación por error y la conmutación por recuperación constan de cuatro fases:

1. **Conmutación por error en Azure**: se conmutan por error las máquinas del sitio local a Azure.
2. **Reprotección de máquinas virtuales de Azure**: vuelva a proteger las máquinas virtuales de Azure de modo que comiencen a replicarse de nuevo en las máquinas virtuales locales de Hyper-V.
3. **Conmutación por error a un sitio local**: ejecute una conmutación por error desde Azure al sitio local, cuando esté disponible.
4. **Reprotección en máquinas virtuales locales**: después de que los datos se hayan conmutado por recuperación, vuelva a proteger las máquinas virtuales locales para que comiencen a replicarse en Azure.

## <a name="verify-vm-properties"></a>Comprobar las propiedades de la máquina virtual

Compruebe las propiedades de la máquina virtual y asegúrese de que la máquina virtual cumpla con los [requisitos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. En **Elementos protegidos**, haga clic en **Elementos replicados** > <nombre de la máquina virtual>.

2. En el panel **Elemento replicado**, puede revisar la información sobre la máquina virtual, el estado de mantenimiento y los puntos de recuperación disponibles más recientes. Haga clic en **Propiedades** para ver más detalles.
     - En **Proceso y red**, puede modificar la configuración de la máquina virtual y de la red, incluida la red/subred en que se ubicará la No se admite la conmutación por recuperación de Managed Disks desde Azure a Hyper-V.
   máquina virtual de Azure después de la conmutación por error y la dirección que se le asignará.
    - En **Discos** puede ver información sobre los discos de datos y el sistema operativo de la máquina virtual.

## <a name="fail-over-to-azure"></a>Conmutación por error a Azure

1. En **Configuración** > **Elementos replicados**, haga clic en VM > **Conmutación por error**.
2. En **Conmutación por error**, seleccione el punto de recuperación **más reciente**. 
3. Seleccione **Apague la máquina antes de comenzar con la conmutación por error**. Site Recovery intentará apagar las máquinas virtuales de origen antes de desencadenar la conmutación por error. La conmutación por error continúa aunque se produzca un error de cierre. Puede seguir el progreso de la conmutación por error en la página **Trabajos**.
4. Una vez verificada la conmutación por error, haga clic en **Confirmar**. Esta acción elimina todos los puntos de recuperación disponibles.

> [!WARNING]
> **No cancele una conmutación por error en curso**: antes de iniciar la conmutación por error, se detiene la replicación de la máquina virtual. Si se cancela una conmutación por error en curso, la conmutación por error se detiene, pero no se replica la máquina virtual de nuevo.

## <a name="reprotect-azure-vms"></a>Reprotección de las máquinas virtuales de Azure

1. En **AzureVMVault** > **Elementos replicados**, haga clic con el botón derecho en la máquina virtual que se conmutó por error y, luego, seleccione **Volver a proteger**.
2. Verifique que la dirección de protección se establece en **De Azure a local**.
3. La máquina virtual local está apagada durante la reprotección, para ayudar a garantizar la coherencia de los datos. No la encienda una vez finalizada la reprotección.
4. Después del proceso de reprotección, la máquina virtual empieza a replicarse desde Azure en el sitio local.



## <a name="fail-over-from-azure-and-reprotect-the-on-premises-vm"></a>Conmutación por error desde Azure y reprotección de la máquina virtual local

Realice la conmutación por error de Azure al sitio local y empiece a replicar las máquinas virtuales del sitio local a Azure.

1. En **Configuración** > **Elementos replicados**, haga clic en VM > **Conmutación por error planeada**.
2. En **Confirmar conmutación por error planeada**, compruebe la dirección de la conmutación por error (de Azure) y seleccione las ubicaciones de origen y de destino.
3. Seleccione **Sincronizar datos antes de la conmutación por error (sincronizar solo cambios diferenciales)**. Esta opción minimiza el tiempo de inactividad de la máquina virtual, ya que se sincroniza sin necesidad de apagarla.
4. Inicie la conmutación por error. Puede seguir el progreso de la conmutación por error en la pestaña **Trabajos** .
5. Una vez que la sincronización de datos inicial haya finalizado y esté listo para apagar las máquinas virtuales de Azure, haga clic en **Trabajos** > nombre del trabajo de conmutación por error planeado > **Completar conmutación por error**. La máquina virtual de Azure se apagará, se transferirán los cambios más recientes en el entorno local y se iniciará la máquina virtual local.
6. Inicie sesión en la máquina virtual local para comprobar que está disponible según lo previsto.
7. La máquina virtual local se encuentra ahora en un estado de **confirmación pendiente**. Haga clic en **Confirmar**. Esto elimina las máquinas virtuales de Azure y sus discos, y prepara la máquina virtual local para la replicación inversa.
Para iniciar la replicación de la máquina virtual local en Azure, habilite la **replicación inversa**. Esto desencadena la replicación de cambios diferenciales que se han producido desde que se apagó la máquina virtual de Azure.  
