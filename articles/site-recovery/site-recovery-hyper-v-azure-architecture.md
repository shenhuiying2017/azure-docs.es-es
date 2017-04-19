---
title: "Funcionamiento de la replicación de Hyper-V en Azure en Site Recovery | Microsoft Docs"
description: "En este artículo se proporciona información general sobre cómo funciona la replicación de Hyper-V en Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/05/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 9adf266c6a2ac00c3aaa34e2a29aefe34abe2871
ms.lasthandoff: 04/18/2017


---
# <a name="how-does-hyper-v-replication-to-azure-work"></a>Funcionamiento de la replicación de Hyper-V en Azure

Lea este artículo para comprender la arquitectura y los flujos de trabajo para la replicación de Hyper-V en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md).

Publique cualquier comentario que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

En Azure puede replicar lo siguiente:
- **Hyper-V con VMM**: máquinas virtuales ubicadas en hosts de Hyper-V locales que se administran en nubes de System Center Virtual MAchine Manager (VMM). Los hosts pueden ejecutar cualquier [sistema operativo compatible](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). Puede replicar máquinas virtuales de Hyper-V que ejecuten cualquier sistema operativo invitado [compatible con Hyper-V y Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- **Hyper-V sin VMM**: máquinas virtuales locales que se encuentran en hosts de Hyper-V que no se administran en nubes de VMM. Los hosts pueden ejecutar cualquiera de los [sistemas operativos compatibles](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions). Puede replicar máquinas virtuales de Hyper-V que ejecuten cualquier sistema operativo invitado [compatible con Hyper-V y Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).


## <a name="architectural-components"></a>Componentes de la arquitectura

**Ámbito** | **Componente** | **Detalles**
--- | --- | ---
**Las tablas de Azure** | En Azure, necesitará una cuenta de Microsoft Azure, una cuenta de almacenamiento de Azure y una red de Azure. | Las de almacenamiento y red pueden basarse en cuentas de Resource Manager o cuentas clásicas.<br/><br/> Los datos replicados se almacenan en la cuenta de almacenamiento y las máquinas virtuales de Azure se crean con los datos replicados cuando se produce la conmutación por error desde el sitio local.<br/><br/> Las máquinas virtuales de Azure se conectan a la red virtual de Azure cuando se crean.
**Servidor VMM** | Hosts de Hyper-V ubicados en nubes de VMM | Si los hosts de Hyper-V se administran en nubes de VMM, registre el servidor de VMM en el almacén de Recovery Services.<br/><br/> En el servidor de VMM, instale el proveedor de Site Recovery para organizar la replicación con Azure.<br/><br/> Necesita redes de máquinas virtuales y lógicas configuradas para configurar a su vez la asignación de red. Una red de VM debe estar vinculada a una red lógica asociada con la nube.
**Host de Hyper-V** | Los servidores de Hyper-V pueden implementarse con o sin servidor de VMM. | Si no hay ningún servidor VMM, el proveedor de Site Recovery se instala en el host para organizar la replicación con Site Recovery a través de Internet. Si hay servidor de VMM, el proveedor se instala en él y no en el host.<br/><br/> El agente de Recovery Services se instala en el host para controlar la replicación de datos.<br/><br/> Las comunicaciones del proveedor y el agente son seguras y cifradas. También se cifran los datos replicados en el almacenamiento de Azure.
**Máquinas virtuales de Hyper-V** | Necesita una o varias máquinas virtuales en el servidor host de Hyper-V. | No es necesario instalar nada explícitamente en máquinas virtuales.

## <a name="deployment-steps"></a>Pasos de implementación

1. **Azure**: configure los componentes de Azure. Se recomienda que configure las cuentas de almacenamiento y de red antes de comenzar la implementación de Site Recovery.
2. **Almacén**: cree un almacén de Recovery Services para Site Recovery y configúrelo, incluidas las opciones de origen y de destino, una directiva de replicación, finalmente, habilite la replicación.
3. **Origen y destino**:
    - **Hosts de Hyper-V en nubes de VMM**: como parte de la configuración específica del origen, descargue e instale el proveedor de Azure Site Recovery en el servidor de VMM y el agente de Azure Site Recovery en todos los hosts de Hyper-V. El origen será el servidor de VMM. El destino es Azure.
    - Hosts de Hyper-V sin VMM: al configurar el origen, descargue e instale el proveedor y el agente en los hosts de Hyper-V. Durante la implementación, recopilará los hosts en un sitio de Hyper-V y especificará este sitio como origen. El destino es Azure.

    ![Replicación de Hyper-V o VMM en Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)
    ![Replicación del sitio de Hyper-V en Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


4. **Directiva de replicación**: cree una directiva de replicación para el sitio de Hyper-V o la nube de VMM. La directiva se aplica a todas las máquinas virtuales ubicadas en los hosts del sitio o la nube.
5. **Habilitación de la replicación**: habilite la replicación de máquinas virtuales de Hyper-V. La replicación inicial se produce según la configuración de la directiva de replicación. Los cambios en los datos se registran y la replicación de los cambios diferenciales en Azure comienza después de que finaliza la replicación inicial. Las marcas de revisión de un elemento se conservan en un archivo .hrl.
6. **Conmutación por error de prueba**: ejecute una conmutación por error de prueba para asegurarse de que todo funciona según lo previsto.

Más información acerca de la implementación:
- [Introducción a la replicación de máquinas virtuales de Hyper-V en Azure (con VMM)](site-recovery-vmm-to-azure.md)
- [Introducción a la replicación de máquinas virtuales de Hyper-V en Azure (sin VMM)](site-recovery-hyper-v-site-to-azure.md)

## <a name="hyper-v-replication-workflow"></a>Flujo de trabajo de replicación de Hyper-V

### <a name="enable-protection"></a>Habilitar protección

1. Después de habilitar la protección de una máquina virtual de Hyper-V, en el Azure Portal o de forma local, la opción **Habilitar la protección** se inicia.
2. El trabajo comprueba que la máquina cumpla los requisitos previos e invoca [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) para que configure la replicación con su configuración.
3. El trabajo comienza la replicación inicial con la invocación del método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) para inicializar una replicación completa de la máquina virtual y enviar los discos virtuales de la máquina virtual a Azure.
4. Puede supervisar el trabajo en la pestaña **Trabajos**.
        ![Lista Trabajos](media/site-recovery-hyper-v-azure-architecture/image1.png)
        ![Detalles de Habilitar protección](media/site-recovery-hyper-v-azure-architecture/image2.png)

### <a name="initial-replication"></a>Replicación inicial

1. Cuando se desencadena la replicación inicial, se toma una [instantánea de la máquina virtual de Hyper-V](https://technet.microsoft.com/library/dd560637.aspx).
2. Los discos duros virtuales se replican uno a uno hasta que se copian todos en Azure. Esto puede tardar unos minutos, en función del tamaño de la máquina virtual y el ancho de banda de la red. Para optimizar el uso de la red, consulte cómo [administrar el uso de ancho de banda de red para la protección en entornos locales en Azure](https://support.microsoft.com/kb/3056159).
3. Si se producen cambios en el disco con la replicación inicial en curso, el seguimiento de replicaciones de Réplica de Hyper-V realiza un seguimiento de esos cambios en los registros de replicación de Hyper-V (.hrl). Estos archivos se encuentran en la misma carpeta que los discos. Cada disco tiene un archivo .hrl asociado que se enviará al almacenamiento secundario.
4. Los archivos de instantáneas y de registro consumen recursos de disco mientras la replicación inicial está en curso.
5. Cuando finalice la replicación inicial, se elimina la instantánea de la máquina virtual. Los cambios diferenciales en el registro del disco se sincronizan y se combinan en el disco primario.


### <a name="finalize-protection"></a>Fin de la protección

1. Una vez finalizada la replicación inicial, el trabajo **Finalizar la protección en la máquina virtual** configura la red y otras opciones posteriores a la replicación para proteger la máquina virtual.
    ![Trabajo de finalización de la protección](media/site-recovery-hyper-v-azure-architecture/image3.png)
2. Si va a replicar en Azure, debe ajustar la configuración de la máquina virtual para que esté preparada para conmutación por error. En este momento puede ejecutar una conmutación por error de prueba para comprobar que todo funciona según lo esperado.

### <a name="delta-replication"></a>Replicación diferencial

1. Después de la replicación inicial, comienza la sincronización diferencial según la configuración de replicación.
2. La herramienta de seguimiento de la replicación de Réplica de Hyper-V supervisa los cambios realizados en un disco duro virtual como archivos .hrl. Cada disco configurado para la replicación tiene un archivo .hrl asociado. Este registro se envía a la cuenta de almacenamiento del cliente una vez completada la replicación inicial. Cuando un registro se encuentra en tránsito hacia Azure, los cambios en el disco principal se siguen en otro archivo de registro del mismo directorio.
3. Durante la replicación inicial y diferencial puede supervisar la máquina virtual en su vista. [Más información](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines).  

### <a name="replication-synchronization"></a>Sincronización de las replicaciones

1. Si se produce un error en la replicación diferencial y una replicación completa sería costosa en términos de ancho de banda o de tiempo, se marca una máquina virtual para la resincronización. Por ejemplo, si los archivos .hrl alcanzan el 50 % del tamaño del disco, la máquina virtual se marcará para repetir la sincronización.
2.  La resincronización reduce al mínimo la cantidad de datos que se envían; para ello, calcula las sumas de comprobación de las máquinas virtuales de origen y de destino, y envía solo los datos diferenciales. La resincronización utiliza un algoritmo de fragmentación de bloques fijos donde los archivos de origen y destino se dividen en fragmentos fijos. Se generan sumas de comprobación para cada fragmento y, luego, se comparan para determinar qué bloques del origen se deben aplicar al destino.
3. Una vez finalizada la resincronización, se debe reanudar la replicación diferencial normal. De forma predeterminada, la resincronización está programada para ejecutarse automáticamente fuera del horario de oficina, pero puede resincronizar una máquina virtual manualmente. Por ejemplo, si se produce una interrupción de la red o de otro tipo, la resincronización se puede reanudar. Para ello, seleccione la máquina virtual en el portal > **Volver a sincronizar**.

    ![Resincronización manual](media/site-recovery-hyper-v-azure-architecture/image4.png)


### <a name="retries"></a>Reintentos

Si se produce un error de replicación, se realiza un reintento de forma predefinida. Esta lógica se puede clasificar en dos categorías:

**Categoría** | **Detalles**
--- | ---
**Errores irrecuperables** | No se realiza ningún reintento. El estado de la máquina virtual será **Crítico** y se requiere la intervención del administrador. Ejemplos de estos errores: división de la cadena de disco duro virtual; estado no válido para la réplica de máquina virtual; errores de autenticación de red (de autorización); sin detección de errores en la máquina virtual (para servidores de Hyper-V independientes)
**Errores recuperables** | Los reintentos se producen con cada intervalo de replicación mediante el uso de la interrupción exponencial, lo que aumenta el intervalo de reintentos desde el inicio del primer intento en 1, 2, 4, 8 y 10 minutos. Si el error persiste, reintente cada 30 minutos. Ejemplos: errores de red; errores de espacio insuficiente en disco; memoria insuficiente |

## <a name="protection-and-recovery-lifecycle"></a>Ciclo de vida de la protección y la recuperación

![flujo de trabajo](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Pasos siguientes

- [Comprobación de los requisitos previos de implementación](site-recovery-prereq.md)
- Solución de problemas con:
    - [Supervisión y solución de problemas de protección](site-recovery-monitoring-and-troubleshooting.md)
    - [Ayuda del soporte técnico de Microsoft](site-recovery-monitoring-and-troubleshooting.md#reach-out-for-microsoft-support)
    - [Errores comunes y soluciones](site-recovery-monitoring-and-troubleshooting.md#common-azure-site-recovery-errors-and-their-resolutions)

