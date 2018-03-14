---
title: "Conmutación por recuperación de Azure a VMware | Microsoft Docs"
description: "Después de que se produzca una conmutación por error de máquinas virtuales en Azure, puede iniciar una conmutación por recuperación para volver a ponerlas en el entorno local. Conozca los pasos para realizar una conmutación por recuperación."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: nisoneji
ms.openlocfilehash: 50dccf6196b7affd3d21087f851b929d0e850f6d
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/07/2018
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Conmutación por recuperación de Azure a un sitio local

En este artículo se describe cómo conmutar por recuperación máquinas virtuales de Azure Virtual Machines en un entorno de VMware local. Siga las instrucciones que se describen en este artículo para conmutar por recuperación máquinas virtuales de VMware o servidores físicos Windows/Linux tras la conmutación por error desde el sitio local a Azure mediante el tutorial [Conmutación por error en Site Recovery](site-recovery-failover.md).

## <a name="prerequisites"></a>requisitos previos
- Asegúrese de leer los detalles sobre los [diferentes tipos de conmutación por recuperación](concepts-types-of-failback.md) y las advertencias correspondientes.

> [!WARNING]
> Si ha [finalizado la migración](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), ha trasladado la máquina virtual a otro grupo de recursos o ha eliminado la máquina virtual de Azure, no puede entonces realizar la conmutación por recuperación. Si deshabilita la protección de la máquina virtual, no es posible realizar la conmutación por recuperación.

> [!WARNING]
> Un servidor físico Windows Server 2008 R2 SP1, si está protegido y se ha realizado la conmutación por error a Azure, no se puede recuperar.

> [!NOTE]
> Si ha conmutado máquinas virtuales de VMware, no podrá realizar la conmutación por recuperación en un host de Hyper-v.


- Antes de continuar, complete los pasos de reprotección para que las máquinas virtuales terminen de replicarse y se pueda volver a iniciar una conmutación por error en un sitio local. Para más información, consulte [Reprotección desde Azure a un entorno local](site-recovery-how-to-reprotect.md).

- Asegúrese de que vCenter está en estado conectado antes de realizar una conmutación por recuperación. En caso contrario, se producirá un error al desconectar los discos y conectarlos a la máquina virtual.

- Durante la conmutación por error a Azure, es posible que no se pueda acceder al sitio local y, por lo tanto, el servidor de configuración puede estar no disponible o apagado. Durante la reprotección y la conmutación por recuperación, el servidor de configuración local debe estar ejecutándose y en un estado correcto de conexión. 

- Para que esta funcione, durante la conmutación por recuperación, la máquina virtual debe encontrarse en la base de datos del servidor de configuración. Por lo tanto, asegúrese de realizar copias de seguridad del servidor programadas y periódicas. En caso de desastre, tendrá que restaurar el servidor con la dirección IP original para que funcione la conmutación por recuperación.

- El servidor de destino principal no debe tener ninguna instantánea antes de desencadenar la reprotección/conmutación por recuperación.

## <a name="overview-of-failback"></a>Introducción a la conmutación por recuperación
Después de conmutar por error en Azure, podrá conmutar por recuperación en su sitio local siguiendo los pasos que se describen a continuación:

1. [Vuelva a proteger](site-recovery-how-to-reprotect.md) las máquinas virtuales en Azure para que comience su replicación en máquinas virtuales de VMware en el sitio local. Como parte de este proceso, también debe:
    1. Configurar un destino maestro local: uno de Windows para las máquinas virtuales Windows y un [destino maestro de Linux](site-recovery-how-to-install-linux-master-target.md) para las máquinas virtuales Linux.
    2. Configurar un [servidor de procesos](site-recovery-vmware-setup-azure-ps-resource-manager.md).
    3. Iniciar de nuevo la [protección](site-recovery-how-to-reprotect.md). Con este proceso, se apagará la máquina virtual local y se sincronizarán los datos de la máquina virtual de Azure con los discos locales.

1. Cuando las máquinas virtuales de Azure se estén replicando en el sitio local, inicie una conmutación por error de Azure en el entorno local.

1. Después de que los datos se hayan conmutado por recuperación, vuelva a proteger las máquinas virtuales locales para que comiencen a replicarse en Azure.

El siguiente vídeo ofrece una breve introducción a la conmutación por recuperación en un sitio local.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Pasos para la conmutación por recuperación

> [!IMPORTANT]
> Antes de iniciar la conmutación por recuperación, asegúrese de que ha completado la reprotección de las máquinas virtuales. Es necesario que las máquinas virtuales estén protegidas y que su estado sea **correcto**. Para más información sobre cómo reproteger las máquinas virtuales, consulte el artículo sobre [reprotección](site-recovery-how-to-reprotect.md).

1. En la página de elementos replicados, seleccione la máquina virtual y haga clic con el botón derecho para seleccionar **Conmutación por error no planeada**.
2. En **Confirmar conmutación por error**, compruebe la dirección de conmutación por error (de Azure) y seleccione el punto de recuperación que quiere usar para esta (el último o el último coherente con la aplicación). El punto coherente con la aplicación se encuentra detrás del último momento dado y provoca pérdidas de datos.
3. Durante la conmutación por error, las máquinas virtuales de Azure en Site Recovery se cerrarán. Después de comprobar que la conmutación por recuperación se ha completado como estaba previsto, puede comprobar que las máquinas virtuales de Azure se hayan cerrado.
4. Use la opción **Confirmar** para quitar de Azure la máquina virtual que se conmutó por error. Haga clic con el botón derecho en el elemento protegido y, a continuación, en **Confirmar**. Un trabajo quitará las máquinas virtuales anteriores que se hayan conmutado por error en Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>¿A qué punto de recuperación puedo conmutar por recuperación las máquinas virtuales?

Durante el proceso, tiene dos opciones de conmutación por recuperación de la máquina virtual o el plan de recuperación.

- Si selecciona el punto de recuperación procesado más recientemente en el tiempo, todas las máquinas virtuales conmutarán por error a su punto disponible más reciente en el tiempo. En caso de que haya un grupo de replicación en el plan de recuperación, las máquinas virtuales de este grupo conmutarán por error en su punto independiente más reciente.

    No se puede conmutar por recuperación una máquina virtual hasta que tenga al menos un punto de recuperación. No se puede conmutar por recuperación un plan de recuperación hasta que todas sus máquinas virtuales tengan al menos un punto de recuperación.

> [!NOTE]
> El punto de recuperación más reciente es un punto de recuperación preparadas para el bloqueo.

- Si selecciona el punto de recuperación coherente con la aplicación, solo se recuperará una máquina virtual al punto de recuperación coherente con la aplicación más reciente mediante conmutación por recuperación. En el caso de un plan de recuperación con un grupo de replicación, todos los grupos de replicación se recuperarán a su punto de recuperación disponible común.
Los puntos de recuperación coherentes con la aplicación pueden ser anteriores, por lo que podrían perderse datos.

## <a name="what-happens-to-vmware-tools-post-failback"></a>¿Qué ocurre con las herramientas de VMware después de la conmutación por recuperación?

Si se trata de una máquina virtual Windows, Azure Site Recovery deshabilita las herramientas de VMware durante la conmutación por error. Durante la conmutación por recuperación de la máquina virtual Windows, se vuelven a habilitar las herramientas de VMware. 


## <a name="reprotect-from-on-premises-to-azure"></a>Reprotección desde un entorno local a Azure
Tras finalizar la conmutación por recuperación y tras iniciar la confirmación, se eliminarán las máquinas virtuales recuperadas en Azure. Ahora, la máquina virtual volverá a estar disponible en el sitio local, pero no estará protegida. Para volver a iniciar la replicación en Azure, haga lo siguiente:

1. En **Almacén** > **Configuración** > **Elementos replicados**, seleccione las máquinas virtuales objeto de la conmutación por recuperación y haga clic en **Reproteger**.
2. Asigne el valor del servidor de procesos que deba utilizarse para volver a enviar datos a Azure.
3. Haga clic en **Aceptar** para volver a proteger el trabajo.

> [!NOTE]
> Después del arranque de una máquina virtual local, el agente tarda algún tiempo en registrar de nuevo en el servidor de configuración (hasta 15 minutos). Durante este tiempo, la reprotección produce errores y devuelve un mensaje de error que indica que el agente no está instalado. Espere unos minutos y vuelva a intentarlo.

## <a name="next-steps"></a>Pasos siguientes

Una vez finalizado el trabajo de reprotección, la máquina virtual se replica de nuevo en Azure y podrá realizar una [conmutación por error](site-recovery-failover.md) para mover de nuevo las máquinas virtuales a Azure.


