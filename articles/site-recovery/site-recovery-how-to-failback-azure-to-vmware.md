---
title: "Conmutación por recuperación desde Azure a Hyper-v | Microsoft Docs"
description: "Después de que se produzca una conmutación por error de máquinas virtuales en Azure, puede iniciar una conmutación por recuperación para volver a poner las máquinas virtuales en el entorno local. Conozca los pasos para realizar una conmutación por recuperación."
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
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: bedb59414ed328fe2fcca2b09f3360a98c98ff60
ms.openlocfilehash: b4bdd27db343bfb4a0e47c432f726ff454f7f55a
ms.lasthandoff: 02/23/2017


---
# <a name="failback-from-azure-to-on-premises"></a>Conmutación por recuperación de Azure a un entorno local
En este artículo se describe cómo conmutar por recuperación máquinas virtuales de Azure al sitio local. Siga las instrucciones que se describen en este artículo cuando esté listo para conmutar por recuperación máquinas virtuales de VMware o servidores físicos de Windows o Linux después de que han conmutado por error desde el sitio local a Azure mediante este [tutorial](site-recovery-vmware-to-azure-classic.md).

## <a name="overview-of-failback"></a>Introducción a la conmutación por recuperación
Así es como funciona la conmutación por recuperación: después de conmutar por error a Azure, puede conmutar por recuperación a su sitio local en unas cuantas fases:

1. [Vuelva a proteger](site-recovery-how-to-reprotect.md) las máquinas virtuales de Azure de modo que comiencen a replicarse otra vez en las de VMware que se ejecutan en el sitio local. Para ello, también debe hacer lo siguiente: 
    1. Configure un destino maestro local: Windows MT para máquinas virtuales Windows y [Linux MT](site-recovery-how-to-install-linux-master-target.md) para máquinas virtuales Linux
    2. Configure un [servidor de procesos](site-recovery-vmware-setup-azure-ps-resource-manager.md)
    3. Y, a continuación, inicie [Volver a proteger](site-recovery-how-to-reprotect.md)
5. Una vez que las máquinas virtuales de Azure se estén replicando en el sitio local, inicie una conmutación por error desde Azure a un entorno local.
  
Después de que los datos se hayan conmutado por recuperación, vuelva a proteger las máquinas virtuales locales a las que conmutó por recuperación, para que comiencen a replicarse en Azure.

Aquí puede ver un vídeo introductorio rápido.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

### <a name="failback-to-the-original-or-alternate-location"></a>Conmutación por recuperación a la ubicación original o alternativa
    
Si ha conmutado por error una máquina virtual de VMware, puede conmutar por recuperación a la misma máquina virtual de origen si aún existe en el entorno local. En este escenario solo se replicarán de nuevo los cambios diferenciales. Este escenario se conoce como recuperación de ubicación original. Si la máquina virtual local no existe, se trata de una recuperación de ubicación alternativa.

#### <a name="original-location-recovery"></a>Recuperación de ubicación original

Si conmuta por recuperación a la máquina virtual original, se necesitan las condiciones siguientes:
* Si la máquina virtual está administrada por un servidor vCenter, el host ESX del destino principal debe tener acceso al almacén de datos de máquinas virtuales.
* Si la máquina virtual está en un host ESX, pero no está administrada por vCenter, el disco duro de la máquina virtual debe estar en un almacén de datos accesible para el host del destino principal.
* Si la máquina virtual está en un host ESX y no usa vCenter, debe completar la detección del host ESX del destino principal antes de volver a protegerla. Lo mismo se aplica si también conmuta por recuperación a servidores físicos.
* Puede conmutar por recuperación a discos basados en vSAN o RDM si estos ya existen y están conectados a la máquina virtual local.

#### <a name="alternate-location-recovery"></a>Recuperación de ubicación alternativa
Si no existe la máquina virtual local antes de completar la reprotección de la máquina virtual, el proceso se conoce como recuperación en ubicación alternativa. En este caso, el flujo de trabajo de reprotección vuelve a crear la máquina virtual local. Esto también hará que se produzca una descarga de datos completa.

* Cuando se conmuta por recuperación a una ubicación alternativa, la máquina virtual se recuperará en el mismo host ESX en el que se implementó el servidor de destino principal. El almacén de datos utilizado para crear el disco será el mismo almacén de datos seleccionado al volver a proteger la máquina virtual.
* Solo se puede realizar una conmutación por recuperación a un almacén de datos de VMFS. Si tiene un vSAN o RDM, la reprotección y la conmutación por recuperación no funcionarán.
* La reprotección implica a una gran transferencia de datos inicial seguida por los cambios diferenciales. Esto se debe a que la máquina virtual no existe en el entorno local, por lo que deben replicarse de nuevo los datos completos. Esta reprotección también tardará más tiempo que la recuperación de la ubicación original.
* No se puede conmutar por recuperación a discos basados en vSAN o RDM. Solo se pueden crear nuevas VMDK en un almacén de datos de VMFS.

Cuando una máquina física conmuta por error en Azure, solo puede conmutar por recuperación como una máquina virtual VMware (conocida también como P2A2V). Este flujo se enmarca en la recuperación de ubicación alternativa.

* No se puede realizar la conmutación por recuperación en las máquinas Windows Server 2008 R2 SP1, si están protegidas y conmutadas por error a Azure.
* Asegúrese detectar al menos un servidor de destino maestro junto con los hosts ESX/ESXi necesarios para la conmutación por recuperación.

## <a name="have-you-completed-reprotection"></a>¿Ha completado la reprotección?
Antes de continuar, complete los pasos de reprotección para que las máquinas virtuales terminen de replicarse y se pueda iniciar una conmutación por error de nuevo en la ubicación local.
[Reprotección desde Azure a un entorno local](site-recovery-how-to-reprotect.md).

## <a name="pre-requisites"></a>Requisitos previos
 
* El servidor de configuración es necesario localmente cuando realiza una conmutación por recuperación. Durante la conmutación por recuperación, la máquina virtual debe encontrarse en la base de datos del servidor de configuración. De lo contrario, la conmutación por recuperación no se realizará correctamente. Por lo tanto, asegúrese de realizar una copia de seguridad programada periódica del servidor. En caso de desastre, tendrá que restaurarla con la misma dirección IP para que funcione la conmutación por recuperación.
* El servidor de destino maestro no debe tener ninguna instantánea antes de desencadenar la conmutación por recuperación.

## <a name="steps-to-failback"></a>Pasos para la conmutación por recuperación

Antes de iniciar la conmutación por recuperación, **asegúrese de que ha completado la reprotección de las máquinas virtuales**. Las máquinas virtuales deben estar estado protegido y su estado debe ser correcto. Obtenga más información sobre cómo reproteger las máquinas virtuales en [Reprotección](site-recovery-how-to-reprotect.md).

1. En la página de elementos replicados, seleccione la máquina virtual y haga clic con el botón derecho para seleccionar **Conmutación por error no planeada**.
2. En **Confirmar conmutación por error** , compruebe la dirección de conmutación por error (de Azure) y seleccione el punto de recuperación que quiere usar para la conmutación por error (el último o el último coherente con la aplicación). El punto coherente con la aplicación estaría detrás del último momento dado y causa algunas pérdidas de datos.
3. Durante la conmutación por error, se cerrarán las máquinas virtuales de Azure en Site Recovery. Después de comprobar que la conmutación por recuperación se ha completado como se esperaba, puede comprobar que las máquinas virtuales de Azure se han cerrado.

### <a name="to-what-recovery-point-can-i-failback-the-vms-to"></a>¿A qué punto de recuperación puedo conmutar por recuperación las máquinas virtuales?

Durante la conmutación por recuperación, tiene dos opciones para conmutar por recuperación la máquina virtual o el plan de recuperación.

Si selecciona el punto de recuperación procesado más recientemente en el tiempo, todas las máquinas virtuales conmutarán por error a su punto disponible más reciente en el tiempo. En caso de que haya un grupo de replicación en el plan de recuperación, cada máquina virtual de este grupo conmutará por error a su punto independiente más reciente en el tiempo.

No se puede conmutar por recuperación una máquina virtual hasta que tiene al menos un punto de recuperación. No se puede conmutar por recuperación un plan de recuperación hasta que todas sus máquinas virtuales tienen al menos un punto de recuperación.

> [!NOTE]
> El último punto de recuperación es un punto de recuperación coherente con los bloqueos.

Si selecciona el punto de recuperación coherente con la aplicación, solo una conmutación por recuperación de máquina virtual se recuperará a su último punto de recuperación coherente con la aplicación. En el caso de un plan de recuperación con un grupo de replicación, cada grupo de replicación se recuperará a su punto de recuperación disponible común.
Tenga en cuenta que los puntos de recuperación coherentes con la aplicación pueden ser anteriores en el tiempo y podrían perderse datos.

## <a name="next-steps"></a>Pasos siguientes

Una vez finalizada la conmutación por recuperación, debe confirmar la máquina virtual para asegurarse de que se eliminen las máquinas virtuales en Azure.

### <a name="commit"></a>Confirmación
Se requiere confirmación para quitar la máquina virtual conmutada por error de Azure.
Haga clic con el botón derecho en el elemento protegido y haga clic en Confirmar. Se desencadenará un trabajo que quitará las anteriores máquinas virtuales que han conmutado por error en Azure.

### <a name="reprotect-from-on-premises-to-azure"></a>Reprotección desde un entorno local a Azure

Una vez completada la confirmación, la máquina virtual volverá al sitio local, pero no estará protegida. Para volver a iniciar la replicación en Azure, vuelva a hacer lo siguiente:

1. En el Almacén > Configuración > Elementos replicados, seleccione las máquinas virtuales que han sido objeto de la conmutación por recuperación y haga clic en **Reproteger**.
2. Asigne el valor de servidor de procesos que debe utilizarse para volver a enviar datos a Azure.
3. Haga clic en Aceptar para volver a proteger el trabajo.

> [!NOTE]
> Después de que una máquina virtual arranca en local, el agente tarda algún tiempo en registrar de nuevo en el servidor de configuración (hasta 15 minutos). Durante este tiempo, verá que la reprotección produce errores y recibirá un mensaje de error que indica que el agente no está instalado. Espere unos minutos y vuelva a intentarlo.

Al finalizar el trabajo de reprotección, la máquina virtual se replica de nuevo en Azure y puede realizar una conmutación por error.

## <a name="common-issues"></a>Problemas comunes
* Asegúrese de que vCenter está conectado antes de realizar una conmutación por recuperación. En caso contrario, la desconexión de los discos y su posterior conexión a la máquina virtual producirá un error.


