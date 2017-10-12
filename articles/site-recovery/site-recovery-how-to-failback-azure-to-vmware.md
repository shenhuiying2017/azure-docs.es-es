---
title: "Conmutación por recuperación de Azure a VMware | Microsoft Docs"
description: "Después de que se produzca una conmutación por error de máquinas virtuales en Azure, puede iniciar una conmutación por recuperación para volver a ponerlas en el entorno local. Conozca los pasos para realizar una conmutación por recuperación."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: ruturajd
ms.openlocfilehash: 1ca34b262a51b694cb9541750588bbea139eeae1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Conmutación por recuperación de Azure a un sitio local

En este artículo se describe cómo conmutar por recuperación máquinas virtuales de Azure Virtual Machines al sitio local. Siga las instrucciones que se describen en este artículo para conmutar por recuperación máquinas virtuales de VMware o servidores físicos de Windows o Linux después de que se hayan conmutado por error del sitio local a Azure según el tutorial [Replicación de máquinas virtuales de VMware y servidores físicos en Azure con Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).

> [!WARNING]
> Si ha [finalizado la migración](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), ha trasladado la máquina virtual a otro grupo de recursos o ha eliminado la máquina virtual de Azure, no puede entonces realizar la conmutación por recuperación. Si deshabilita la protección de la máquina virtual, no es posible realizar la conmutación por recuperación.

> [!NOTE]
> Si ha conmutado máquinas virtuales de VMware, no podrá realizar la conmutación por recuperación en un host de Hyper-v.

## <a name="overview-of-failback"></a>Introducción a la conmutación por recuperación
Así es cómo funciona la conmutación por recuperación. Después de conmutar por error a Azure, conmuta por recuperación a su sitio local en unas cuantas fases:

1. [Vuelva a proteger](site-recovery-how-to-reprotect.md) las máquinas virtuales en Azure para que comience su replicación en máquinas virtuales de VMware en el sitio local. Como parte de este proceso, también debe:
    1. Configurar un destino maestro local: uno de Windows para las máquinas virtuales Windows y un [destino maestro de Linux](site-recovery-how-to-install-linux-master-target.md) para las máquinas virtuales Linux.
    2. Configurar un [servidor de procesos](site-recovery-vmware-setup-azure-ps-resource-manager.md).
    3. Iniciar de nuevo la [protección](site-recovery-how-to-reprotect.md). Con este proceso, se apagará la máquina virtual local y se sincronizarán los datos de la máquina virtual de Azure con los discos locales.
5. Una vez que las máquinas virtuales de Azure se estén replicando en el sitio local, inicie una conmutación por error de Azure al entorno local.

Después de que los datos se hayan conmutado por recuperación, vuelva a proteger las máquinas virtuales locales que se conmutaran por recuperación, para que comiencen a replicarse en Azure.

El siguiente vídeo es una breve introducción a la conmutación por error de Azure a un sitio local.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

### <a name="fail-back-to-the-original-or-alternate-location"></a>Conmutación por recuperación a la ubicación original o alternativa

Si ha conmutado por error una máquina virtual de VMware, puede conmutar por recuperación a la misma máquina virtual de origen local si aún existe. En este escenario solo se replicarán de nuevo los cambios. Este escenario se conoce como recuperación de ubicación original. Si no existe máquina virtual local, será una recuperación a ubicación alternativa.

> [!NOTE]
> Puede realizar la conmutación por recuperación solo en el servidor de configuración y vCenter originales. No puede implementar un nuevo servidor de configuración y realizar la conmutación por recuperación usándolo. Además, no puede agregar un nuevo vCenter al servidor de configuración existente y realizar la conmutación por recuperación en el nuevo vCenter.

#### <a name="original-location-recovery"></a>Recuperación de ubicación original

Si conmuta por recuperación la máquina virtual original, deben cumplirse las siguientes condiciones:
* Si la máquina virtual se administra desde un servidor vCenter, el host ESX de destino principal debe tener acceso al almacén de datos de esta.
* Si la máquina virtual está en un host ESX, pero no se administra desde vCenter, su disco duro debe estar en un almacén de datos accesible para el host de destino principal.
* Si la máquina virtual está en un host ESX y no usa vCenter, debe completar la detección del host ESX de destino principal antes de volver a protegerla. Lo mismo se aplica si también conmuta por recuperación a servidores físicos.
* Puede realizar una conmutación por recuperación a una red de área de almacenamiento virtual (vSAN) o un disco basado en la asignación de dispositivos sin procesar (RDM) si los discos ya existen y están conectados a la máquina virtual en local.

#### <a name="alternate-location-recovery"></a>Recuperación de ubicación alternativa
Si no existe máquina virtual local antes de volver a proteger la máquina virtual, el escenario se denomina recuperación a ubicación alternativa. El flujo de trabajo de reprotección vuelve a crear la máquina virtual local. Esto también hará que se produzca una descarga de datos completa.

* Cuando se conmuta por recuperación a una ubicación alternativa, la máquina virtual se recupera en el mismo host ESX en el que se implementara el servidor de destino principal. El almacén de datos donde se creó el disco será el mismo que se seleccionó al volver a proteger la máquina virtual.
* Solo se puede conmutar por recuperación a un almacén de datos del sistema de archivos de máquina virtual (VMFS) o vSAN. Si tiene un RDM, la reprotección y la conmutación por recuperación no funcionarán.
* La reprotección implica una gran transferencia de datos inicial seguida de los cambios. Este proceso se produce porque no existe máquina virtual local. Deben volver a replicarse todos los datos. Esta reprotección también tardará más tiempo que la recuperación de ubicación original.
* No se puede conmutar por recuperación a discos basados en RDM. En un almacén de datos VMFS/vSAN solo pueden crearse discos de máquina virtual (VMDK) nuevos.

Cuando una máquina física conmuta por error en Azure, solo puede conmutar por recuperación como una máquina virtual de VMware (conocida también como P2A2V). Este flujo se enmarca en la recuperación de ubicación alternativa.

* Un servidor físico Windows Server 2008 R2 SP1, si está protegido y se ha realizado la conmutación por error a Azure, no se puede recuperar.
* Asegúrese detectar al menos un servidor de destino principal junto con los hosts ESX/ESXi necesarios para la conmutación por recuperación.

## <a name="have-you-completed-reprotection"></a>¿Ha completado la reprotección?
Antes de continuar, complete los pasos de reprotección para que las máquinas virtuales terminen de replicarse y se pueda volver a iniciar una conmutación por error en un sitio local. Para más información, consulte [Reprotección desde Azure a un entorno local](site-recovery-how-to-reprotect.md).

## <a name="prerequisites"></a>Requisitos previos

> [!IMPORTANT]
> Durante la conmutación por error en Azure, es posible que no se pueda acceder al sitio local y, por lo tanto, el servidor de configuración puede estar no disponible o apagado. Durante la reprotección y la conmutación por recuperación, el servidor de configuración local debe estar ejecutándose y en un estado correcto de conexión.

* Para la conmutación por recuperación se necesita un servidor de configuración local. El servidor debe estar en estado de ejecución y conectado al servicio de modo que su estado sea correcto. Para que esta funcione, durante la conmutación por recuperación, la máquina virtual debe encontrarse en la base de datos del servidor de configuración. Por lo tanto, asegúrese de realizar una copia de seguridad programada periódica del servidor. En caso de desastre, tendrá que restaurarla con la misma dirección IP para que la conmutación por recuperación funcione.
* El servidor de destino principal no debe tener ninguna instantánea antes de desencadenar la reprotección/conmutación por recuperación.

## <a name="steps-to-fail-back"></a>Pasos para la conmutación por recuperación

> [!IMPORTANT]
> Antes de iniciar la conmutación por recuperación, asegúrese de que ha completado la reprotección de las máquinas virtuales. Es necesario que las máquinas virtuales estén protegidas y que su estado sea **correcto**. Para más información sobre cómo reproteger las máquinas virtuales, consulte el artículo sobre [reprotección](site-recovery-how-to-reprotect.md).

1. En la página de elementos replicados, seleccione la máquina virtual y haga clic con el botón derecho para seleccionar **Conmutación por error no planeada**.
2. En **Confirmar conmutación por error**, compruebe la dirección de conmutación por error (de Azure) y seleccione el punto de recuperación que quiere usar para esta (el último o el último coherente con la aplicación). El punto coherente con la aplicación se encuentra detrás del último momento dado y provoca pérdidas de datos.
3. Durante la conmutación por error, las máquinas virtuales de Azure en Site Recovery se cerrarán. Después de comprobar que la conmutación por recuperación se ha completado como estaba previsto, puede comprobar que las máquinas virtuales de Azure se hayan cerrado.

### <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>¿A qué punto de recuperación puedo conmutar por recuperación las máquinas virtuales?

Durante el proceso, tiene dos opciones de conmutación por recuperación de la máquina virtual o el plan de recuperación.

Si selecciona el punto de recuperación procesado más recientemente en el tiempo, todas las máquinas virtuales conmutarán por error a su punto disponible más reciente en el tiempo. En caso de que haya un grupo de replicación en el plan de recuperación, las máquinas virtuales de este grupo conmutarán por error a su punto independiente más reciente en el tiempo.

No se puede conmutar por recuperación una máquina virtual hasta que tenga al menos un punto de recuperación. No se puede conmutar por recuperación un plan de recuperación hasta que todas sus máquinas virtuales tengan al menos un punto de recuperación.

> [!NOTE]
> El punto de recuperación más reciente es un punto de recuperación preparadas para el bloqueo.

Si selecciona el punto de recuperación coherente con la aplicación, solo se recuperará una máquina virtual al punto de recuperación coherente con la aplicación más reciente mediante conmutación por recuperación. En el caso de un plan de recuperación con un grupo de replicación, todos los grupos de replicación se recuperarán a su punto de recuperación disponible común.
Tenga en cuenta que los puntos de recuperación coherentes con la aplicación pueden ser anteriores en el tiempo, por lo que podrían perderse datos.

### <a name="what-happens-to-vmware-tools-post-failback"></a>¿Qué ocurre con las herramientas de VMware después de la conmutación por recuperación?

Durante la conmutación por error a Azure, no pueden ejecutar las herramientas de VMware en la máquina virtual de Azure. Si se trata de una máquina virtual Windows, ASR deshabilita las herramientas de VMware durante la conmutación por error. Si es una máquina virtual Linux, ASR desinstala las herramientas de VMware durante la conmutación por error.

Durante la conmutación por recuperación de la máquina virtual Windows, se vuelven a habilitar las herramientas de VMware. De forma similar, para una máquina virtual Linux, las herramientas de VMware se vuelven a instalar en la máquina durante la conmutación por recuperación.

## <a name="next-steps"></a>Pasos siguientes

Una vez finalizada la conmutación por recuperación, debe confirmar la máquina virtual para asegurarse de que se eliminen las máquinas virtuales en Azure.

### <a name="commit"></a>Confirmación
Se requiere confirmación para quitar la máquina virtual conmutada por error de Azure.
Haga clic con el botón derecho en el elemento protegido y después en **Confirmar**. Un trabajo quitará las máquinas virtuales anteriores que se hayan conmutado por error en Azure.

### <a name="reprotect-from-on-premises-to-azure"></a>Reprotección desde un entorno local a Azure

Una vez confirmado, la máquina virtual vuelve al sitio local, pero no estará protegida. Para volver a iniciar la replicación en Azure, haga lo siguiente:

1. En **Almacén** > **Configuración** > **Elementos replicados**, seleccione las máquinas virtuales objeto de la conmutación por recuperación y haga clic en **Reproteger**.
2. Asigne el valor del servidor de procesos que deba utilizarse para volver a enviar datos a Azure.
3. Haga clic en **Aceptar** para volver a proteger el trabajo.

> [!NOTE]
> Después del arranque de una máquina virtual local, el agente tarda algún tiempo en registrar de nuevo en el servidor de configuración (hasta 15 minutos). Durante este tiempo, la reprotección produce errores y devuelve un mensaje de error que indica que el agente no está instalado. Espere unos minutos y vuelva a intentarlo.

Una vez finalizado el trabajo de reprotección, la máquina virtual se replica de nuevo en Azure y puede realizar una conmutación por error.

## <a name="common-issues"></a>Problemas comunes
Asegúrese de que vCenter está en estado conectado antes de realizar una conmutación por recuperación. En caso contrario, al desconectar los discos y adjuntarlos a la máquina virtual se producirá un error.
