---
title: "Conmutación por error y conmutación por recuperación de servidores físicos y máquinas virtuales de VMware replicados en Azure con Site Recovery | Microsoft Docs"
description: "Obtenga información sobre cómo conmutar por error servidores físicos y máquinas virtuales de VMware en Azure y conmutar por recuperación en el sitio local con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 45f1c61189b338d38681c8e93be01953da65913f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-and-fail-back-vmware-vms-and-physical-servers-replicated-to-azure"></a>Conmutación por error y conmutación por recuperación de servidores físicos y máquinas virtuales de VMware replicados en Azure

En este tutorial se describe cómo conmutar por error una máquina virtual de VMware en Azure. Después de que ha conmutado por error, conmutará por recuperación a su sitio local cuando esté disponible. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Comprobar las propiedades de máquinas virtuales de VMware para comprobar que se cumplen los requisitos de Azure.
> * Ejecutar una conmutación por error en Azure.
> * Crear un servidor de proceso y un servidor de destino maestro para la conmutación por recuperación.
> * Volver a proteger máquinas virtuales de Azure en el sitio local.
> * Realizar la conmutación por error de Azure en el entorno local.
> * Volver a proteger las máquinas virtuales locales para iniciar de nuevo la replicación en Azure

Este es el quinto tutorial de una serie. En este tutorial se da por hecho que ya ha realizado las tareas de los tutoriales anteriores.

1. [Preparación de Azure](tutorial-prepare-azure.md)
2. [Preparación de instancias locales de VMware](tutorial-prepare-on-premises-vmware.md)
3. [Configuración de la recuperación ante desastres](tutorial-vmware-to-azure.md)
4. [Obtención de detalles de recuperación ante desastres](tutorial-dr-drill-azure.md)

## <a name="preparing-for-failover-and-failback"></a>Preparación de conmutación por error y conmutación por recuperación

Asegúrese de que no hay instantáneas en la máquina virtual. La máquina virtual local está apagada durante la reprotección.
Esto ayuda a garantizar la coherencia de datos durante la replicación. No encienda la máquina virtual una vez finalizada la reprotección. Use el mismo servidor de destino maestro para reproteger un grupo de replicación. Si usa otro servidor de destino maestro para reproteger un grupo de replicación, el servidor no puede proporcionar un momento dado común.

La conmutación por error y la conmutación por recuperación constan de cuatro fases:

1. **Conmutación por error en Azure**: se conmutan por error las máquinas del sitio local a Azure.
2. **Reprotección de máquinas virtuales de Azure**: vuelva a proteger las máquinas virtuales de Azure de modo que comiencen a replicarse de nuevo en las máquinas virtuales locales de VMware.
3. **Conmutación por error en el entorno local**: ejecute una conmutación por error para realizar una conmutación por recuperación desde Azure.
4. **Reprotección en máquinas virtuales locales**: después de que los datos se hayan conmutado por recuperación, vuelva a proteger las máquinas virtuales locales conmutadas por recuperación para que comiencen a replicarse en Azure.

## <a name="verify-vm-properties"></a>Verificación de las propiedades de la máquina virtual

Compruebe las propiedades de la máquina virtual y asegúrese de que la máquina virtual cumpla con los [requisitos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. En **Elementos protegidos**, haga clic en **Elementos replicados** > VM.

2. En el panel **Elemento replicado**, puede ver un resumen de la información de la máquina virtual, el estado de mantenimiento y los puntos de recuperación disponibles más recientes. Haga clic en **Propiedades** para ver más detalles.

3. En **Proceso y red**, puede modificar el nombre de Azure, el grupo de recursos, el tamaño de destino, el [conjunto de disponibilidad](../virtual-machines/windows/tutorial-availability-sets.md) y la [configuración de discos administrados](#managed-disk-considerations).

4. Puede ver y modificar la configuración de red, incluida la red o subred en la que se va a ubicar la máquina virtual de Azure después de la conmutación por error y la dirección IP que se le va a asignar.

5. En **Discos** puede ver información sobre los discos de datos y el sistema operativo de la máquina virtual.

## <a name="run-a-failover-to-azure"></a>Ejecutar una conmutación por error en Azure.

1. En **Configuración** > **Elementos replicados**, haga clic en VM > **Conmutación por error**.

2. En **Conmutación por error**, seleccione un **punto de recuperación** en el que realizar la conmutación por error. Puede seleccionar una de las siguientes opciones:
   - **Último** (valor predeterminado): esta opción procesa primero todos los datos enviados a Site Recovery. Ofrece el objetivo de punto de recuperación (RPO) mínimo, ya que la máquina virtual de Azure creada después de la conmutación por error tiene todos los datos replicados en Site Recovery al desencadenarse la conmutación por error.
   - **Procesado más recientemente**: con esta opción se realiza una conmutación por error de la máquina virtual al último punto de recuperación procesado por Site Recovery. Esta opción proporciona un objetivo de tiempo de recuperación (RTO) bajo, ya que no se invierte tiempo en el procesamiento de datos sin procesar.
   - **Más reciente coherente con la aplicación**: esta opción conmuta por error la máquina virtual al punto de recuperación más reciente coherente con la aplicación que haya procesado Site Recovery.
   - **Personalizado**: especifique un punto de recuperación.

3. Seleccione **Shut down machine before beginning failover** (Apagar la máquina antes de comenzar la conmutación por error) para intentar apagar las máquinas virtuales de origen antes de desencadenar la conmutación por error. La conmutación por error continúa aunque se produzca un error de cierre. Puede seguir el progreso de la conmutación por error en la página **Trabajos**.

4. Si está preparado para conectarse a la máquina virtual de Azure, hágalo para validarla después de la conmutación por error.

5. Después de la comprobación, **confirme** la conmutación por error. Este proceso eliminará todos los puntos de recuperación disponibles.

> [!WARNING]
> **No cancele una conmutación por error en curso**: antes de iniciar la conmutación por error, se detiene la replicación de la máquina virtual.
> Si se cancela una conmutación por error en curso, la conmutación por error se detiene, pero no replica la máquina virtual de nuevo.

En algunos escenarios, la conmutación por error requiere un procesamiento adicional que tarda aproximadamente de ocho a diez minutos en completarse. Puede observar tiempos de conmutación por error de prueba más largos en los servidores físicos, las máquinas de VMware Linux, las máquinas virtuales de VMware que no tienen el servicio DHCP habilitado y las máquinas virtuales de VMware que no tienen los siguientes controladores de arranque: storvsc, vmbus, storflt, intelide y atapi.

## <a name="create-a-process-server-in-azure"></a>Creación de un servidor de procesos en Azure

El servidor de procesos recibe datos de la máquina virtual de Azure y los envía al sitio local. Se requiere una red de baja latencia entre el servidor de procesos y la máquina virtual protegida.

- Para fines de prueba, si tiene una conexión de Azure ExpressRoute, puede usar el servidor de procesos local instalado automáticamente en el servidor de configuración.
- Si tiene una conexión VPN o está realizando una conmutación por recuperación en un entorno de producción, debe configurar una máquina virtual de Azure como un servidor de procesos basado en Azure para la conmutación por recuperación.
- Para configurar un servidor de procesos en Azure, siga las instrucciones de [este artículo](site-recovery-vmware-setup-azure-ps-resource-manager.md).

## <a name="configure-the-master-target-server"></a>Configuración del servidor de destino maestro

De forma predeterminada, el servidor de destino maestro se ejecuta en el servidor de configuración local. Para este tutorial, estamos usando el servidor maestro predeterminado. El servidor de destino maestro recibe los datos de conmutación por recuperación.

Si la máquina virtual está en un host ESXi administrado por un servidor vCenter, el servidor de destino maestro debe tener acceso al almacén de datos de la máquina virtual (VMDK) para escribir los datos replicados en los discos de máquina virtual. Asegúrese de que el almacén de datos de la máquina virtual esté montado en el host del destino maestro con acceso de lectura y escritura.

Si la máquina virtual está en un ESXi que no está administrado por un servidor vCenter, el servicio Site Recovery crea una nueva máquina virtual durante la reprotección. Esta máquina virtual se crea en el host ESX en el que se crea el destino maestro.
El disco duro de la máquina virtual debe estar en un almacén de datos que sea accesible para el host en el que se esté ejecutando el servidor de destino maestro.

Si la máquina virtual no usa vCenter, debe completar la detección del host en el que se ejecute el servidor de destino maestro para poder volver a proteger la máquina. Lo mismo se aplica si realiza la conmutación por recuperación en servidores físicos. Otra opción (si existe la máquina virtual local) es eliminarla antes de realizar una conmutación por recuperación. En este caso, la conmutación por recuperación crea una nueva máquina virtual en el mismo host que el host ESX de destino maestro. Cuando se conmuta por recuperación a una ubicación alternativa, los datos se recuperan en el mismo almacén de datos y el mismo host ESX que los usados por el servidor de destino principal local.

No se puede usar Storage vMotion en el servidor de destino maestro. Si lo hace, la conmutación por recuperación no funcionará, ya que los discos no están disponibles para él. Excluya los servidores de destino maestros de la lista de vMotion.

## <a name="reprotect-azure-vms"></a>Reprotección de las máquinas virtuales de Azure

En este procedimiento se da por supuesto que la máquina virtual local no está disponible y está volviendo a proteger en una ubicación alternativa.

1. En **Configuración** > **Elementos replicados**, haga clic con el botón derecho en la máquina virtual que se ha conmutado por error > **Reproteger**.
2. En **Reproteger**, compruebe que la opción **De Azure a local** esté seleccionada.
3. Especifique el servidor de destino maestro local y el servidor de procesos.

4. En **Almacén de datos**, seleccione el almacén de datos de destino maestro en el que quiera recuperar los discos en el entorno local. Use esta opción cuando se haya eliminado la máquina virtual local y sea necesario crear discos nuevos. Esta configuración se omite si los discos ya existen, pero debe especificar un valor.
5. Seleccione la unidad de retención de destino maestra. La directiva de conmutación por recuperación se selecciona automáticamente.
6. Haga clic en **Aceptar** para comenzar con la reprotección. Comienza un trabajo para replicar la máquina virtual desde Azure en el sitio local. Puede realizar el seguimiento del progreso en la pestaña **Trabajos** .

> [!NOTE]
> Si desea recuperar la máquina virtual de Azure en una máquina virtual local existente, el almacén de datos de la máquina virtual local debe montarse con acceso de lectura/escritura en el host ESXi del servidor de destino maestro.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Ejecución de una conmutación por error desde Azure en el entorno local

Para volver a replicar en el entorno local, se usa directiva de conmutación por recuperación. Esta directiva se genera automáticamente cuando crea una directiva de replicación para la replicación en Azure:

- La directiva se asocia automáticamente al servidor de configuración.
- La directiva no se puede modificar.
- Los valores de la directiva son:
    - Umbral RPO = 15 minutos
    - Retención de punto de recuperación = 24 horas
    - Frecuencia de las instantáneas coherentes con la aplicación = 60 minutos

Ejecute la conmutación por error de la manera siguiente:

1. En la página **Elementos replicados**, haga clic con el botón derecho en la máquina y seleccione **Conmutación por error no planeada**.
2. En **Confirmar conmutación por error**, compruebe que la dirección de la conmutación por error sea desde Azure.

3. Seleccione el punto de recuperación que desee usar para la conmutación por error. Un punto de recuperación coherente con la aplicación se produce antes del punto más reciente en el tiempo y provocará pérdida de datos. Cuando se ejecuta la conmutación por error, Site Recovery apaga las máquinas virtuales de Azure y arranca la máquina virtual local. Habrá cierto tiempo de inactividad, por lo que debe elegir la hora adecuada.
4. Haga clic con el botón derecho en la máquina y haga clic en **Confirmar**. Esto desencadenará un trabajo en el que se quitarán las máquinas virtuales de Azure.
5. Compruebe que las máquinas virtuales de Azure se hayan apagado según lo previsto.


## <a name="reprotect-on-premises-machines-to-azure"></a>Reprotección de máquinas locales en Azure

Los datos ahora deben volver a estar en el sitio local, pero no se está replicando en Azure. Puede volver a iniciar la replicación en Azure de la siguiente forma:

1. En Almacén > **Configuración** >**Elementos replicados**, seleccione las máquinas virtuales que han sido objeto de la conmutación por recuperación y haga clic en **Reproteger**.
2. Seleccione el servidor de procesos que se usa para enviar los datos replicados a Azure y haga clic en **Aceptar**.

Una vez completada la reprotección, la máquina virtual se vuelve a replicar en Azure y podrá ejecutar una conmutación por error según sea necesario.
