---
title: "¿Cómo funciona Azure Site Recovery? | Microsoft Docs"
description: "Este artículo proporciona información general sobre la arquitectura de Site Recovery"
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
ms.date: 03/14/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 4674985363bc1267449e018ab15a53757a8fd32d
ms.lasthandoff: 03/15/2017


---
# <a name="how-does-azure-site-recovery-work"></a>¿Cómo funciona Azure Site Recovery?

Este artículo describe la arquitectura subyacente del servicio [Azure Site Recovery](site-recovery-overview.md) y los componentes que hacen que funcione.

Publique cualquier comentario que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="replicate-to-azure"></a>Replicación en Azure

En Azure puede replicar lo siguiente:

- **VMware**: máquinas virtuales de VMware locales que se ejecutan en un [host compatible](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). Puede replicar máquinas virtuales de VMware que se ejecutan en [sistemas operativos compatibles](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions).
- **Hyper-V**: máquinas virtuales de Hyper-V locales que se ejecutan en [hosts compatibles](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- **Máquinas físicas**: servidores físicos locales que ejecutan Windows o Linux en [sistemas operativos compatibles](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions). Puede replicar máquinas virtuales de Hyper-V que ejecuten cualquier sistema operativo invitado [compatible con Hyper-V y Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).

## <a name="vmware-to-azure"></a>VMware en Azure

Esto es lo que necesita para la replicación de máquinas virtuales VMware en Azure.

Ámbito | Componente | Detalles
--- | --- | ---
**Las tablas de Azure** | En Azure, necesitará una cuenta de Azure, una cuenta de Azure Storage y una red de Azure. | Las de almacenamiento y red pueden ser cuentas de Resource Manager o cuentas clásicas.<br/><br/>  Los datos replicados se almacenan en la cuenta de almacenamiento y las máquinas virtuales de Azure se crean con los datos replicados cuando se produce la conmutación por error desde el sitio local. Las máquinas virtuales de Azure se conectan a la red virtual de Azure cuando se crean.
**Servidor de configuración** | Un solo servidor de administración (máquina virtual de VMware) ejecuta todos los componentes locales (servidor de configuración, servidor de procesos y servidor de destino principal). | El servidor de configuración coordina la comunicación entre el entorno local y Azure, además de administrar la replicación de datos.
 **Servidor de proceso**:  | Se instala de forma predeterminada en el servidor de configuración. | Actúa como puerta de enlace de replicación. Recibe datos de replicación, los optimiza con almacenamiento en caché, compresión y cifrado y los envía al almacenamiento de Azure.<br/><br/> El servidor de procesos también controla la instalación de inserción del Servicio de movilidad en máquinas protegidas y realiza la detección automática de máquinas virtuales de VMware.<br/><br/> A medida que crezca la implementación, podrá agregar más servidores de procesos dedicados independientes para controlar mayores volúmenes de tráfico de replicación.
 **Servidor de destino principal** | Se instala de forma predeterminada en el servidor de configuración local. | Controla los datos de replicación durante la conmutación por recuperación desde Azure.<br/><br/> Si los volúmenes de tráfico de la conmutación por recuperación son altos, puede implementar un servidor de destino maestro independiente para la conmutación por recuperación.
**Servidores de VMware** | Las máquinas virtuales de VMware se hospedan en servidores de vSphere ESXi y se recomienda un servidor de vCenter para administrar los hosts. | Añada servidores de VMware al almacén de Recovery Services.<br/><br/> I
**Máquinas replicadas** | El Servicio de movilidad se instalará en cada máquina virtual de VMware que desee replicar. Se puede instalar manualmente en cada equipo o con una instalación de inserción desde el servidor de procesos.

**Figura 1: VMware en componentes de Azure**

![Componentes](./media/site-recovery-components/arch-enhanced.png)

### <a name="replication-process"></a>Proceso de replicación

1. Configure la implementación, incluidos los componentes de Azure y un almacén de Recovery Services. En el almacén, especifique el origen y el destino de la replicación, configure el servidor de configuración, agregue servidores de VMware, cree una directiva de replicación, implemente el servicio de movilidad, habilite la replicación y ejecute una prueba de conmutación por error.
2.  Las máquinas se empiezan a replicar de acuerdo con la directiva de replicación y una copia inicial de los datos se replica en el almacenamiento de Azure.
4. La replicación de los cambios diferenciales en Azure comienza después de que finaliza la replicación inicial. Las marcas de revisión de una máquina se conservan en un archivo .hrl.
    - Las máquinas que están en replicación se comunican con el servidor de configuración en el puerto HTTPS 443 entrante para la administración de la replicación.
    - Las máquinas que están en replicación envían datos de replicación al servidor de procesos en el puerto HTTPS 9443 entrante (se puede configurar).
    - El servidor de configuración organiza la administración de la replicación con Azure a través del puerto HTTPS 443 saliente.
    - El servidor de procesos recibe datos de las máquinas de origen, los optimiza y los cifra y los envía al almacenamiento de Azure a través del puerto 443 saliente.
    - Si habilita la coherencia entre varias máquinas virtuales, las máquinas del grupo de replicación se comunican entre sí a través del puerto 20004. La implementación de varias máquinas virtuales se usa si agrupa varias máquinas en grupos de replicación que tienen puntos de recuperación coherentes con los bloqueos y coherentes con la aplicación cuando conmutan por error. Esto resulta útil si las máquinas ejecutan la misma carga de trabajo y necesitan ser coherentes.
5. El tráfico se replica en los puntos de conexión públicos del almacenamiento de Azure a través de Internet. Como alternativa, puede usar el [emparejamiento público](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-circuit-peerings#public-peering) de Azure ExpressRoute. No se admite la replicación del tráfico a través de una VPN de sitio a sitio desde un sitio local en Azure.

**Figura 2: VMware en replicación de Azure**

![Mejorada](./media/site-recovery-components/v2a-architecture-henry.png)

### <a name="failover-and-failback"></a>Conmutación por error y conmutación por recuperación

1. Después de comprobar que la conmutación por error de prueba funciona según lo previsto, puede ejecutar las conmutaciones por error no planeadas en Azure según sea necesario. No se admite la conmutación por error planeada.
2. Puede conmutar por error una única máquina o crear [planes de recuperación](site-recovery-create-recovery-plans.md) para realizar la conmutación por error de varias máquinas virtuales.
3. Cuando se ejecuta una conmutación por error, las máquinas virtuales de la réplica se crean en Azure. Realice una conmutación por error para iniciar el acceso a la carga de trabajo desde la máquina virtual de Azure de la réplica.
4. Cuando el sitio local principal esté disponible de nuevo, podrá realizar una conmutación por recuperación. Configure una infraestructura de conmutación por recuperación, empiece a replicar la máquina desde el sitio secundario al principal y ejecute una conmutación por error no planeada desde el sitio secundario. Después de ejecutar esta conmutación por error, los datos volverán a la ubicación local y deberá habilitar la replicación en Azure de nuevo. [Más información](site-recovery-failback-azure-to-vmware.md)

Hay varios requisitos para la conmutación por recuperación:


- **Servidor de procesos temporal en Azure**: si desea realizar la conmutación por recuperación desde Azure después de una conmutación por error, será preciso que configure una máquina virtual de Azure como servidor de procesos para que controle la replicación desde Azure. Dicha máquina virtual se puede eliminar cuando finalice la conmutación por recuperación.
- **Conexión VPN**: para realizar la conmutación por recuperación, necesitará que se haya configurado una conexión VPN (o Azure ExpressRoute) desde la red de Azure y el sitio local.
- **Servidor de destino maestro local independiente**: el servidor de destino maestro local controla la conmutación por recuperación. El servidor de destino maestro se instala de forma predeterminada en el servidor de administración, pero si se va a realizar la conmutación por recuperación de grandes volúmenes de tráfico, se debe configurar un servidor de destino maestro local independiente para este propósito.
- **Directiva de conmutación por recuperación**: para replicar de nuevo en el sitio local, necesita una directiva de conmutación por recuperación. Esta se crea automáticamente al crear la directiva de replicación.

**Figura 3: VMware y conmutación por recuperación física**

![Conmutación por recuperación](./media/site-recovery-components/enhanced-failback.png)

## <a name="physical-to-azure"></a>Físico en Azure

Al replicar servidores físicos locales en Azure, la replicación utiliza también los mismos componentes y procesos que [VMware a Azure](#vmware-replication-to-azure), pero es necesario tener en cuenta estas diferencias:

- Puede usar un servidor físico para el servidor de configuración, en lugar de una máquina virtual de VMware.
- Necesitará una infraestructura de VMware local para la conmutación por recuperación. No se puede realizar la conmutación por recuperación en una máquina física.

## <a name="hyper-v-to-azure"></a>Hyper-V en Azure

Esto es lo que necesita para la replicación de máquinas virtuales Hyper-V en Azure.

**Ámbito** | **Componente** | **Detalles**
--- | --- | ---
**Las tablas de Azure** | En Azure, necesitará una cuenta de Microsoft Azure, una cuenta de almacenamiento de Azure y una red de Azure. | Las de almacenamiento y red pueden basarse en cuentas de Resource Manager o cuentas clásicas.<br/><br/> Los datos replicados se almacenan en la cuenta de almacenamiento y las máquinas virtuales de Azure se crean con los datos replicados cuando se produce la conmutación por error desde el sitio local.<br/><br/> Las máquinas virtuales de Azure se conectan a la red virtual de Azure cuando se crean.
**Servidor VMM** | Hosts de Hyper-V ubicados en nubes de VMM | Si los hosts de Hyper-V se administran en nubes de VMM, registre el servidor de VMM en el almacén de Recovery Services.<br/><br/> En el servidor de VMM, instale el proveedor de Site Recovery para organizar la replicación con Azure.<br/><br/> Necesita redes de máquinas virtuales y lógicas configuradas para configurar a su vez la asignación de red. Una red de VM debe estar vinculada a una red lógica asociada con la nube.
**Host de Hyper-V** | Los servidores de Hyper-V pueden implementarse con o sin servidor de VMM. | Si no hay ningún servidor VMM, el proveedor de Site Recovery se instala en el host para organizar la replicación con Site Recovery a través de Internet. Si hay servidor de VMM, el proveedor se instala en él y no en el host.<br/><br/> El agente de Recovery Services se instala en el host para controlar la replicación de datos.<br/><br/> Las comunicaciones del proveedor y el agente son seguras y cifradas. También se cifran los datos replicados en el almacenamiento de Azure.
**Máquinas virtuales de Hyper-V** | Necesita una o varias máquinas virtuales en el servidor host de Hyper-V. | No es necesario instalar nada explícitamente en máquinas virtuales.


### <a name="replication-process"></a>Proceso de replicación

1. Configure los componentes de Azure. Se recomienda que configure las cuentas de almacenamiento y de red antes de comenzar la implementación de Site Recovery.
2. Cree un almacén de servicios de replicación para Site Recovery y configure el almacén, incluyendo:
    - Configuración de origen y destino. Si no está administrando hosts de Hyper-V en una nube de VMM, para el destino cree un contenedor de sitios de Hyper-V y agréguele hosts de Hyper-V. Si los hosts de Hyper-V se administran en VMM, el origen es la nube de VMM. El destino es Azure.
    - Instalación del proveedor de Azure Site Recovery y del agente de Microsoft Azure Recovery Services. Si tiene VMM, el proveedor se instalará en este y el agente en cada host de Hyper-V. Si no dispone de VMM, el proveedor y el agente se instalarán en cada host.
    - Cree una directiva de replicación para el sitio de Hyper-V o la nube de VMM. La directiva se aplica a todas las máquinas virtuales ubicadas en los hosts del sitio o la nube.
    - Habilite la replicación de máquinas virtuales de Hyper-V. La replicación inicial se produce según la configuración de la directiva de replicación.
4. Los cambios en los datos se registran y la replicación de los cambios diferenciales en Azure comienza después de que finaliza la replicación inicial. Las marcas de revisión de un elemento se conservan en un archivo .hrl.
5. Ejecute una conmutación por error de prueba para asegurarse de que todo funciona.

### <a name="failover-and-failback-process"></a>Proceso de conmutación por error y conmutación por recuperación

1. Puede ejecutar una [conmutación por error](site-recovery-failover.md), planeada o no, desde máquinas virtuales de Hyper-V locales en Azure. Si ejecuta una conmutación por error planeada, las máquinas virtuales de origen se apagan para garantizar que no se pierdan datos.
2. Puede conmutar por error una única máquina o crear [planes de recuperación](site-recovery-create-recovery-plans.md) para organizar la conmutación por error de varias máquinas.
4. Después de ejecutar la conmutación por error, podrá ver las máquinas virtuales de la réplica creadas en Azure. Puede asignar una dirección IP pública a la máquina virtual si es necesario.
5. Después, ejecute una conmutación por error para iniciar el acceso a la carga de trabajo desde la máquina virtual de Azure de la réplica.
6. Cuando el sitio local principal esté disponible de nuevo, podrá realizar una [conmutación por recuperación](site-recovery-failback-from-azure-to-hyper-v.md). Inicie una conmutación por error planeada desde Azure al sitio principal. Para una conmutación por error planeada puede elegir realizar una conmutación por recuperación en la misma máquina virtual o en una ubicación alternativa y sincronizar los cambios entre Azure y el sitio local para garantizar que no se produzca ninguna pérdida de datos. Cuando las máquinas virtuales se crean en una ubicación local, puede ejecutar la conmutación por error.

**Figura 4: Replicación de sitio de Hyper-V en Azure**

![Componentes](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**Figura 5: Replicación de Hyper-V en nubes de VMM en Azure**

![Componentes](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)


## <a name="replicate-to-a-secondary-site"></a>Replicación en un sitio secundario

Puede replicar lo siguiente en un sitio secundario:

- **VMware**: máquinas virtuales de VMware locales que se ejecutan en un [host compatible](site-recovery-support-matrix-to-sec-site.md#on-premises-servers). Puede replicar máquinas virtuales de VMware que se ejecutan en [sistemas operativos compatibles](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).
- **Máquinas físicas**: servidores físicos locales que ejecutan Windows o Linux en [sistemas operativos compatibles](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).
- **Hyper-V**: máquinas virtuales de Hyper-V locales que se ejecutan en [hosts de Hyper-V compatibles](site-recovery-support-matrix-to-sec-site.md#on-premises-servers) administrados en nubes de VMM y [hosts compatibles](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). Puede replicar máquinas virtuales de Hyper-V que ejecuten cualquier sistema operativo invitado [compatible con Hyper-V y Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).


## <a name="vmwarephysical-to-a-secondary-site"></a>Máquinas VMware o servidores físicos en un sitio secundario

Replique máquinas virtuales de VMware o servidores físicos en un sitio secundario utilizando InMage Scout.

### <a name="components"></a>Componentes

**Ámbito** | **Componente** | **Detalles**
--- | --- | ---
**Las tablas de Azure** | InMage Scout. | Para obtener InMage Scout, necesitará una suscripción a Azure.<br/><br/> Después de crear un almacén de Recovery Services descargue InMage Scout e instale las actualizaciones más recientes para configurar la implementación.
**Servidor de proceso** | Ubicado en el sitio principal. | Implemente el servidor de procesos para controlar el almacenamiento en caché, la compresión y la optimización de datos.<br/><br/> También controla la instalación por inserción del agente unificada en las máquinas que desea proteger.
**Servidor de configuración** | Ubicado en el sitio secundario. | El servidor de configuración administra, configura y supervisa la implementación, ya sea mediante el sitio web de administración o la consola de vContinuum.
**Servidor de vContinuum** | Opcional. Se instala en la misma ubicación que el servidor de configuración. | Proporciona una consola para administrar y supervisar su entorno protegido.
**Servidor de destino principal** | Ubicado en el sitio secundario. | El servidor de destino maestro contiene los datos replicados. Recibe los datos del servidor de proceso, crea una máquina de réplica en el sitio secundario y contiene los puntos de retención de datos.<br/><br/> El número de servidores de destino maestros que necesita depende del número de equipos que va a proteger.<br/><br/> Si desea realizar una conmutación por recuperación al sitio principal, necesita un servidor de destino maestro allí también. El agente unificado se instala en este servidor.
**Servidor de VMware ESX/ESXi y vCenter** |  Las máquinas virtuales se hospedan en hosts ESX/ESXi. Los hosts se administran con un servidor vCenter. | Necesita una infraestructura de VMware para replicar máquinas virtuales de VMware.
**Máquinas virtuales y servidores físicos** |  Unified Agent se instala en las máquinas virtuales de VMware y los servidores físicos que desea replicar. | Dicho agente actúa como un proveedor de comunicación entre todos los componentes.


### <a name="replication-process"></a>Proceso de replicación

1. Configure los servidores de componentes de cada sitio (configuración, proceso y destino principal) e instale Unified Agent en las máquinas que desee replicar.
2. Después de la replicación inicial, el agente de cada máquina envía los cambios de replicación diferencial al servidor de procesos.
3. Por su parte, el servidor de procesos optimiza los datos y los transfiere al servidor de destino maestro del sitio secundario. El servidor de configuración administra el proceso de replicación.

**Figura 6: Replicación de VMware en VMware**

![VMware a VMware](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="hyper-v-to-a-secondary-site"></a>Hyper-V en un sitio secundario

Esto es lo que necesita para la replicación de máquinas virtuales Hyper-V en un sitio secundario.


**Ámbito** | **Componente** | **Detalles**
--- | --- | ---
**Las tablas de Azure** | Necesita una cuenta de Microsoft Azure. |
**Servidor VMM** | Se recomienda un servidor VMM en el sitio principal y otro en el sitio secundario. | Cada servidor VMM debe estar conectado a Internet.<br/><br/> Cada servidor debe tener al menos una nube privada de VMM con el perfil de funcionalidad de Hyper-V.<br/><br/> Instale el proveedor de Azure Site Recovery en el servidor VMM. El proveedor coordina y organiza la replicación con el servicio Site Recovery a través de Internet. Las comunicaciones entre el proveedor y Azure son seguras y cifradas.
**Servidor de Hyper-V** |  Uno o varios servidores host de Hyper-V en las nubes VMM principal y secundaria.<br/><br/> Los servidores deben estar conectados a Internet.<br/><br/> Los datos se replican entre los servidores host de Hyper-V principales y secundarios a través de la LAN o VPN mediante autenticación Kerberos o de certificado.  
**Máquinas virtuales de Hyper-V** | Ubicadas en el servidor host de Hyper-V de origen. | El servidor host de origen debe tener, como mínimo, una máquina virtual que se desee replicar.

### <a name="replication-process"></a>Proceso de replicación

1. Configure la cuenta de Azure.
2. Cree un almacén de servicios de replicación para Site Recovery y configure el almacén, incluyendo:

    - El origen y destino de la replicación (sitios principales y secundarios).
    - Instalación del proveedor de Azure Site Recovery y del agente de Microsoft Azure Recovery Services. El proveedor está instalado en servidores VMM y el agente en cada host de Hyper-V.
    - Cree una directiva de replicación para la nube de VMM de origen. La directiva se aplica a todas las máquinas virtuales ubicadas en los hosts de la nube.
    - Habilite la replicación de máquinas virtuales de Hyper-V. La replicación inicial se produce según la configuración de la directiva de replicación.
4. Los cambios en los datos se registran y la replicación de los cambios diferenciales comienza después de que finaliza la replicación inicial. Las marcas de revisión de un elemento se conservan en un archivo .hrl.
5. Ejecute una conmutación por error de prueba para asegurarse de que todo funciona.

**Figura 7: VMM en replicación de VMM**

![De local a local](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback"></a>Conmutación por error y conmutación por recuperación

1. Puede ejecutar una [conmutación por error](site-recovery-failover.md), planeada o no, entre sitios locales. Si ejecuta una conmutación por error planeada, las máquinas virtuales de origen se apagan para garantizar que no se pierdan datos.
2. Puede conmutar por error una única máquina o crear [planes de recuperación](site-recovery-create-recovery-plans.md) para organizar la conmutación por error de varias máquinas.
4. Si realiza una conmutación por error no planeada en un sitio secundario, tras ella las máquinas de la ubicación secundaria no están habilitadas para su protección ni replicación. Si ejecutó una conmutación por error planeada, tras ella las máquinas de la ubicación secundaria están protegidas.
5. Después, ejecute una conmutación por error para iniciar el acceso a la carga de trabajo desde la máquina virtual de la réplica.
6. Cuando el sitio principal esté disponible de nuevo, inicie la replicación inversa para replicar desde el sitio secundario al principal. La replicación inversa pone las máquinas virtuales en un estado protegido pero el centro de datos secundario sigue siendo la ubicación activa.
7. Para que el sitio principal se convierta de nuevo en la ubicación activa, debe iniciar una conmutación por error planeada desde el secundario al principal, seguida de otra replicación inversa.


## <a name="next-steps"></a>Pasos siguientes

- [Más información](site-recovery-hyper-v-azure-architecture.md) sobre el flujo de trabajo de replicación de Hyper-V.
- [Comprobación de los requisitos previos](site-recovery-prereq.md)

