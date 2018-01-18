---
title: "Conmutación por error y conmutación por recuperación de servidores físicos replicados en Azure con Site Recovery | Microsoft Docs"
description: "Obtenga información sobre cómo realizar una conmutación por error de servidores físicos en Azure y una conmutación por recuperación en el sitio local con Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 08/01/2018
ms.author: raynew
ms.openlocfilehash: 2fbad3b53319c8dc8be3480162a4fa67bab94306
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Conmutación por error y conmutación por recuperación de servidores físicos replicados en Azure

En este tutorial se describe cómo realizar una conmutación por error de un servidor físico en Azure. Después de realizar la conmutación por error, deberá hacer la conmutación por recuperación del servidor en el sitio local cuando esté disponible. 

## <a name="preparing-for-failover-and-failback"></a>Preparación de conmutación por error y conmutación por recuperación

Los servidores físicos replicados en Azure mediante Site Recovery solo pueden realizar conmutaciones por recuperación como máquinas virtuales de VMware. Esto significa que necesita una infraestructura de VMware para realizar conmutaciones por recuperación. 

La conmutación por error y la conmutación por recuperación constan de cuatro fases:

1. **Conmutación por error en Azure**: se conmutan por error las máquinas del sitio local a Azure.
2. **Volver a proteger máquinas virtuales de Azure**: vuelva a proteger las máquinas virtuales de Azure de modo que comiencen a replicarse de nuevo en las máquinas virtuales locales de VMware.
3. **Conmutación por error en el entorno local**: ejecute una conmutación por error para realizar una conmutación por recuperación desde Azure.
4. **Volver a proteger máquinas virtuales locales**: después de que los datos se hayan conmutado por recuperación, vuelva a proteger las máquinas virtuales locales de VMware conmutadas por recuperación para que comiencen a replicarse en Azure.

## <a name="verify-server-properties"></a>Comprobar lar propiedades del servidor

Compruebe las propiedades del servidor y asegúrese de que cumpla con los [requisitos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) de las máquinas virtuales de Azure.

1. En **Elementos protegidos**, haga clic en **Elementos replicados** y seleccione la máquina.

2. En el panel **Elemento replicado**, puede ver un resumen de la información del equipo, el estado de mantenimiento y los puntos de recuperación disponibles más recientes. Haga clic en **Propiedades** para ver más detalles.
3. En **Proceso y red**, puede modificar el nombre de Azure, el grupo de recursos, el tamaño de destino, el [conjunto de disponibilidad](../virtual-machines/windows/tutorial-availability-sets.md) y la [configuración de discos administrados](#managed-disk-considerations).
4. Puede ver y modificar la configuración de red, incluida la red o subred en la que se va a ubicar la máquina virtual de Azure después de la conmutación por error y la dirección IP que se le va a asignar.
5. En **Discos** puede ver información sobre los discos de datos y el sistema operativo del equipo.

## <a name="run-a-failover-to-azure"></a>Ejecutar una conmutación por error en Azure.

1. En **Configuración** > **Elementos replicados**, haga clic en la máquina > **Conmutación por error**.
2. En **Conmutación por error**, seleccione un **punto de recuperación** en el que realizar la conmutación por error. Puede seleccionar una de las siguientes opciones:
   - **Último** (valor predeterminado): esta opción procesa primero todos los datos enviados a Site Recovery. Ofrece el objetivo de punto de recuperación (RPO) mínimo, ya que la máquina virtual de Azure creada después de la conmutación por error tiene todos los datos replicados en Site Recovery al desencadenarse la conmutación por error.
   - **Procesado más recientemente**: con esta opción se realiza una conmutación por error de la máquina al último punto de recuperación que procesó Site Recovery. Esta opción proporciona un objetivo de tiempo de recuperación (RTO) bajo, ya que no se invierte tiempo en el procesamiento de datos sin procesar.
   - **Más reciente coherente con la aplicación**: esta opción realiza una conmutación por error de la máquina al punto de recuperación más reciente coherente con la aplicación que haya procesado Site Recovery.
   - **Personalizado**: especifique un punto de recuperación.

3. Seleccione **Apague la máquina antes de comenzar con la conmutación por error** si desea que Site Recovery intente apagar la máquina de origen antes de desencadenar la conmutación por error. La conmutación por error continúa aunque se produzca un error de cierre. Puede seguir el progreso de la conmutación por error en la página **Trabajos**.
4. Si está preparado para conectarse a la máquina virtual de Azure, hágalo para validarla después de la conmutación por error.
5. Después de la comprobación, **confirme** la conmutación por error. Esta acción elimina todos los puntos de recuperación disponibles.

> [!WARNING]
> No cancele una conmutación por error en curso. Antes de que comience la conmutación por error, se detendrá la replicación de la máquina. Si cancela la conmutación por error esta se detiene, pero no replica de nuevo la máquina.
> En cuanto a los servidores físicos, el proceso de conmutación por error adicional puede tardar de ocho a diez minutos en completarse. 

## <a name="create-a-process-server-in-azure"></a>Creación de un servidor de procesos en Azure

El servidor de procesos recibe datos de la máquina virtual de Azure y los envía al sitio local. Es necesaria una red de baja latencia entre el servidor de procesos y la máquina protegida.

- Para fines de prueba, si tiene una conexión de Azure ExpressRoute, puede usar el servidor de procesos local instalado automáticamente en el servidor de configuración.
- Si tiene una conexión VPN o está realizando una conmutación por recuperación en un entorno de producción, debe configurar una máquina virtual de Azure como un servidor de procesos basado en Azure para la conmutación por recuperación.
- Para configurar un servidor de procesos en Azure, siga las instrucciones de [este artículo](site-recovery-vmware-setup-azure-ps-resource-manager.md).

## <a name="configure-the-master-target-server"></a>Configuración del servidor de destino maestro

El servidor de destino maestro recibe los datos de conmutación por recuperación de forma predeterminada. Además, se ejecuta con el servidor de configuración local.

- Si la máquina virtual de VMware a la que se le realiza la conmutación por recuperación está en un host ESXi que administra VMware vCenter Server, el servidor de destino maestro debe tener acceso al almacén de datos de la máquina virtual (VMDK) para escribir los datos replicados en los discos de la máquina virtual. Asegúrese de que el almacén de datos de la máquina virtual esté montado en el host del destino maestro con acceso de lectura y escritura.
- Si vCenter Server no administra el host ESXi, el servicio Site Recovery crea una nueva máquina virtual durante la reprotección. Esta máquina virtual se crea en el host ESX, en el cual se crea a su vez la máquina virtual de destino maestra. El disco duro de la máquina virtual debe estar en un almacén de datos que sea accesible para el host en el que se esté ejecutando el servidor de destino maestro.
- En cuanto a las máquinas físicas a las que debe realizar una conmutación por recuperación, es necesario completar la detección del host en el que se ejecuta el servidor de destino maestro antes de poder volver a proteger la máquina.
- Otra opción (si es que existe la máquina virtual local para realizar la conmutación por recuperación) es eliminar esta máquina antes de realizar una conmutación por recuperación. En este caso, la conmutación por recuperación crea una nueva máquina virtual en el mismo host que el host ESX de destino maestro. Cuando se conmuta por recuperación a una ubicación alternativa, los datos se recuperan en el mismo almacén de datos y el mismo host ESX que los usados por el servidor de destino principal local.
- No se puede usar Storage vMotion en el servidor de destino maestro. Si lo hace, la conmutación por recuperación no funcionará, ya que los discos no están disponibles para él. Excluya los servidores de destino maestros de la lista de vMotion.

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

