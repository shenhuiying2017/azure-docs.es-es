---
title: "Revisar la arquitectura para la replicación de VMware en Azure | Microsoft Docs"
description: "Este artículo proporciona información general de los componentes y la arquitectura usados al replicar máquinas virtuales de VMware locales en Azure con el servicio Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27.017
ms.author: raynew
ms.openlocfilehash: 2bbab5f1ac0efe9632ad6c818504584e2503cf15
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-1-review-the-architecture-for-vmware-replication-to-azure"></a>Paso 1: Revisión de la arquitectura para la replicación de VMware en Azure

En este artículo se describen los componentes y los procesos usados al replicar máquinas virtuales de VMware locales en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md).

Publique cualquier comentario en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="architectural-components"></a>Componentes de la arquitectura

En la tabla se resumen los componentes necesarios.

**Componente** | **Requisito** | **Detalles**
--- | --- | ---
**Las tablas de Azure** | Necesita una suscripción de Azure, una cuenta de Azure Storage y una red de Azure. | Los datos replicados se almacenan en la cuenta de almacenamiento. Las máquinas virtuales de Azure se crean con los datos replicados cuando se produce la conmutación por error desde el sitio local. Las máquinas virtuales de Azure se conectan a la red virtual de Azure cuando se crean.
**Servidor de configuración** | Un único servidor de administración local (máquina virtual de VMware) que ejecuta todos componentes locales de Site Recovery para la implementación. Estos incluyen un servidor de configuración, un servidor de procesos y un servidor de destino maestro. | El componente servidor de configuración coordina las comunicaciones entre el entorno local y Azure y administra la replicación de datos.
 **Servidor de proceso**:  | Se instala de forma predeterminada en el servidor de configuración. | Actúa como puerta de enlace de replicación. Recibe datos de replicación, los optimiza con almacenamiento en caché, compresión y cifrado y los envía al almacenamiento de Azure.<br/><br/> El servidor de procesos también controla la instalación de inserción del Servicio de movilidad en máquinas protegidas y realiza la detección automática de máquinas virtuales de VMware.<br/><br/> A medida que crezca la implementación, podrá agregar más servidores de procesos dedicados independientes para controlar mayores volúmenes de tráfico de replicación.
 **Servidor de destino principal** | Se instala de forma predeterminada en el servidor de configuración local. | Controla los datos de replicación durante la conmutación por recuperación desde Azure.<br/><br/> Si los volúmenes de tráfico de la conmutación por recuperación son altos, puede implementar un servidor de destino maestro independiente para la conmutación por recuperación.
**Servidores de VMware** | Las máquinas virtuales de VMware se hospedan en servidores de vSphere ESXi y se recomienda un servidor de vCenter para administrar los hosts. | Añada servidores de VMware al almacén de Recovery Services.
**Máquinas replicadas** | El Servicio de movilidad se instalará en cada máquina virtual de VMware que se replique. Se puede instalar manualmente en cada equipo o con una instalación de inserción desde el servidor de procesos.

**Figura 1: VMware en componentes de Azure**

![Componentes](./media/vmware-walkthrough-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Proceso de replicación

1. Configure la implementación, incluidos los componentes de Azure y locales. En el almacén de Recovery Services, especifique el origen y el destino de la replicación, configure el servidor de configuración, cree una directiva de replicación, implemente el Servicio de movilidad, habilite la replicación y ejecute una conmutación por error de prueba.
2. Las máquinas se replican de acuerdo con la directiva de replicación y una copia inicial de los datos se replica en Azure Storage.
3. Una vez terminada la replicación inicial, comienza la de los cambios incrementales en Azure. Las marcas de revisión de una máquina se conservan en un archivo .hrl.
    - Las máquinas que están en replicación se comunican con el servidor de configuración en el puerto HTTPS 443 entrante para la administración de la replicación.
    - Las máquinas en replicación envían datos de replicación al servidor de procesos del puerto HTTPS 9443 entrante (se puede modificar).
    - El servidor de configuración organiza la administración de la replicación con Azure a través del puerto HTTPS 443 saliente.
    - El servidor de procesos recibe datos de las máquinas de origen, los optimiza y los cifra y los envía al almacenamiento de Azure a través del puerto 443 saliente.
    - Si habilita la coherencia entre varias máquinas virtuales, las máquinas del grupo de replicación se comunican entre sí a través del puerto 20004. La implementación de varias máquinas virtuales se usa si agrupa varias máquinas en grupos de replicación que tienen puntos de recuperación coherentes con los bloqueos y coherentes con la aplicación cuando conmutan por error. Esto resulta útil si las máquinas ejecutan la misma carga de trabajo y necesitan ser coherentes.
4. El tráfico se replica en los puntos de conexión públicos del almacenamiento de Azure a través de Internet. Como alternativa, puede usar el [emparejamiento público](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) de Azure ExpressRoute. No se admite la replicación del tráfico a través de una VPN de sitio a sitio desde un sitio local en Azure.


**Figura 2: VMware en replicación de Azure**

![Mejorada](./media/vmware-walkthrough-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proceso de conmutación por error y conmutación por recuperación

1. Después de comprobar que la conmutación por error de prueba funciona según lo previsto, puede ejecutar las conmutaciones por error no planeadas en Azure según sea necesario. No se admite la conmutación por error planeada.
2. Puede conmutar por error una única máquina o crear [planes de recuperación](site-recovery-create-recovery-plans.md) para realizar la conmutación por error de varias máquinas virtuales.
3. Cuando se ejecuta una conmutación por error, las máquinas virtuales de la réplica se crean en Azure. Realice una conmutación por error para iniciar el acceso a la carga de trabajo desde la máquina virtual de Azure de la réplica.
4. Cuando el sitio local principal esté disponible de nuevo, podrá realizar una conmutación por recuperación. Configure una infraestructura de conmutación por recuperación, empiece a replicar la máquina desde el sitio secundario al principal y ejecute una conmutación por error no planeada desde el sitio secundario. Después de ejecutar esta conmutación por error, los datos volverán a la ubicación local y deberá habilitar la replicación en Azure de nuevo. [Más información](site-recovery-failback-azure-to-vmware.md)

Hay varios requisitos para la conmutación por recuperación:


- **Servidor de procesos temporal en Azure**: si desea realizar la conmutación por recuperación desde Azure después de una conmutación por error, será preciso que configure una máquina virtual de Azure como servidor de procesos para que controle la replicación desde Azure. Dicha máquina virtual se puede eliminar cuando finalice la conmutación por recuperación.
- **Conexión VPN**: para realizar la conmutación por recuperación, necesitará que se haya configurado una conexión VPN (o Azure ExpressRoute) desde la red de Azure y el sitio local.
- **Servidor de destino maestro local independiente**: el servidor de destino maestro local controla la conmutación por recuperación. El servidor de destino maestro se instala de forma predeterminada en el servidor de administración, pero si se va a realizar la conmutación por recuperación de grandes volúmenes de tráfico, se debe configurar un servidor de destino maestro local independiente para este propósito.
- **Directiva de conmutación por recuperación**: para replicar de nuevo en el sitio local, necesita una directiva de conmutación por recuperación. Esta se crea automáticamente al crear la directiva de replicación.
- **Infraestructura de VMware**: debe conmutar por recuperación a una máquina virtual de VMware local. Esto significa que necesita una infraestructura de VMware local, incluso si está replicando servidores físicos locales a Azure.

**Figura 3: VMware y conmutación por recuperación física**

![Conmutación por recuperación](./media/vmware-walkthrough-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Pasos siguientes

Vaya a [Paso 2: Comprobación de los requisitos previos y las limitaciones](vmware-walkthrough-prerequisites.md)
