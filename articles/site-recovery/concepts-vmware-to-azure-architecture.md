---
title: "VMware para la replicación de la arquitectura en Azure con Azure Site Recovery | Microsoft Docs"
description: "Este artículo proporciona información general de los componentes y la arquitectura usados al replicar máquinas virtuales de VMware locales en Azure con el servicio Azure Site Recovery."
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/19/2017
ms.author: raynew
ms.openlocfilehash: 1c991298d8f59c7f161b965541571b4c8ac3d8f9
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="vmware-to-azure-replication-architecture"></a>Arquitectura de replicación de VMware a Azure

En este artículo se describen la arquitectura y los procesos que se usan al replicar, conmutar por error y recuperar máquinas virtuales VMware entre un sitio VMware local y Azure, mediante el servicio [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Componentes de la arquitectura

En la siguiente tabla y gráfico se proporciona una visión general de los componentes que se usaron para la replicación de VMware en Azure.  

**Componente** | **Requisito** | **Detalles**
--- | --- | ---
**Las tablas de Azure** | Suscripción de Azure, cuenta de Azure Storage y red de Azure. | Los datos replicados desde las máquinas virtuales locales se almacenan en la cuenta de almacenamiento. Las máquinas virtuales de Azure se crean con los datos replicados cuando se ejecuta una conmutación por error desde el entorno local en Azure. Las máquinas virtuales de Azure se conectan a la red virtual de Azure cuando se crean.
**Servidor de configuración** | Se implementa una sola máquina virtual VMware local para que ejecute todos los componentes locales de Site Recovery. La máquina virtual ejecuta el servidor de configuración, el servidor de procesos y el servidor de destino maestro. | El servidor de configuración coordina la comunicación entre el entorno local y Azure, además de administrar la replicación de datos.
 **Servidor de proceso**:  | Se instala de forma predeterminada junto con el servidor de configuración. | Actúa como puerta de enlace de replicación. Recibe datos de replicación, los optimiza con almacenamiento en caché, compresión y cifrado y los envía al almacenamiento de Azure.<br/><br/> El servidor de procesos también instala Mobility Service en las máquinas virtuales que quiere replicar, además de realizar la detección automática de máquinas virtuales en servidores de VMware locales.<br/><br/> A medida que crece la implementación, puede agregar más servidores de procesos independientes para controlar mayores volúmenes de tráfico de replicación.
 **Servidor de destino principal** | Se instala de forma predeterminada junto con el servidor de configuración. | Controla los datos de replicación durante la conmutación por recuperación desde Azure.<br/><br/> En el caso de las implementaciones de gran tamaño, puede agregar un servidor de destino maestro independiente adicional para la conmutación por recuperación.
**Servidores de VMware** | Las máquinas virtuales VMware se hospedan en servidores ESXi de vSphere locales. Se recomienda un servidor vCenter para administrar los hosts. | Durante la implementación de Site Recovery, se agregan servidores VMware al almacén de Recovery Services.
**Máquinas replicadas** | El Servicio de movilidad se instala en cada máquina virtual de VMware que se replique. | Se recomienda permitir la instalación automática desde el servidor de procesos. Además, puede instalar manualmente el servicio o usar un método de implementación automatizada, como System Center Configuration Manager. 

**Arquitectura de VMware a Azure**

![Componentes](./media/concepts-vmware-to-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Proceso de replicación

1. Configure la implementación, incluidos los componentes de Azure y locales. En el almacén de Recovery Services, especifica el origen y el destino de la replicación, configura el servidor de configuración, crea una directiva de replicación y habilita la replicación.
2. Las máquinas se replican de acuerdo con la directiva de replicación y una copia inicial de los datos de máquina virtual se replica en Azure Storage.
3. Una vez terminada la replicación inicial, comienza la de los cambios incrementales en Azure. Las marcas de revisión de una máquina se conservan en un archivo .hrl.
    - Las máquinas se comunican con el servidor de configuración en el puerto HTTPS 443 entrante para la administración de la replicación.
    - Las máquinas envían los datos de replicación al servidor de procesos en el puerto HTTPS 9443 entrante (se puede modificar).
    - El servidor de configuración organiza la administración de la replicación con Azure a través del puerto HTTPS 443 saliente.
    - El servidor de procesos recibe datos de las máquinas de origen, los optimiza y los cifra y los envía al almacenamiento de Azure a través del puerto 443 saliente.
    - Si habilita la coherencia entre varias máquinas virtuales, las máquinas del grupo de replicación se comunican entre sí a través del puerto 20004. La implementación de varias máquinas virtuales se usa si agrupa varias máquinas en grupos de replicación que tienen puntos de recuperación coherentes con los bloqueos y coherentes con la aplicación cuando conmutan por error. Esto resulta útil si las máquinas ejecutan la misma carga de trabajo y necesitan ser coherentes.
4. El tráfico se replica en los puntos de conexión públicos del almacenamiento de Azure a través de Internet. Como alternativa, puede usar el [emparejamiento público](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) de Azure ExpressRoute. No se admite la replicación del tráfico a través de una VPN de sitio a sitio desde un sitio local en Azure.


**Proceso de replicación de VMware a Azure**

![Proceso de replicación](./media/concepts-vmware-to-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proceso de conmutación por error y conmutación por recuperación

Una vez que la replicación está configurada y que se ejecutó una exploración de la recuperación ante desastres (conmutación por error de prueba) para comprobar que todo funciona según lo previsto, puede ejecutar la conmutación por error y la conmutación por recuperación según sea necesario.

1. Puede conmutar por error una única máquina o crear planes de recuperación para realizar la conmutación por error de varias máquinas virtuales.
2. Cuando se ejecuta una conmutación por error, se crean máquinas virtuales de Azure a partir de los datos replicados en Azure Storage.
3. Después de desencadenar la conmutación por error inicial, debe confirmarla para que se inicie. Para ello, acceda a la carga de trabajo desde la máquina virtual de Azure.

Cuando el sitio local principal esté disponible de nuevo, podrá realizar una conmutación por recuperación.
1. Debe configurar una infraestructura de conmutación por recuperación, que incluya:
    - **Servidor de procesos temporal en Azure**: para realizar una conmutación por error desde Azure, configure una máquina virtual de Azure que actúe como servidor de procesos para controlar la replicación desde Azure. Dicha máquina virtual se puede eliminar cuando finalice la conmutación por recuperación.
    - **Conexión VPN**: para la conmutación por recuperación, necesita una conexión VPN (o Azure ExpressRoute) desde la red de Azure al sitio local.
    - **Servidor de destino maestro independiente**: de manera predeterminada, el servidor de destino maestro que se instaló con el servidor de configuración, en la máquina virtual de VMware, controla la conmutación por recuperación. Sin embargo, si necesita la conmutación por recuperación en grandes volúmenes de tráfico, debe configurar un servidor de destino maestro local independiente para este propósito.
    - **Directiva de conmutación por recuperación**: para replicar de nuevo en el sitio local, necesita una directiva de conmutación por recuperación. Esta directiva se creó automáticamente cuando creó la directiva de replicación desde el entorno local a Azure.
2. Una vez instalados los componentes, la conmutación por recuperación se produce en tres fases:
    - Fase 1: Vuelva a proteger las máquinas virtuales de modo que realicen la replicación desde Azure de vuelta a las máquinas virtuales VMware locales.
    - Fase 2: Ejecute una conmutación por error en el sitio local.
    - Fase 3: .Una vez que las cargas de trabajo realizaron la conmutación por recuperación, vuelve a habilitar la réplica para las máquinas virtuales locales.

**Conmutación por recuperación VMware desde Azure**

![Conmutación por recuperación](./media/concepts-vmware-to-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>pasos siguientes

Siga [este tutorial](tutorial-vmware-to-azure.md) para habilitar la replicación de VMware en Azure.
