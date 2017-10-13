---
title: "Revisión de la arquitectura para la replicación de un servidor físico en Azure con Azure Site Recovery | Microsoft Docs"
description: "En este artículo se proporciona información general de los componentes y la arquitectura que se usan al replicar servidores físicos locales en Azure con el servicio Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 09c9b136-35f5-465e-8d0f-f4c5d5d9f880
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 697fbcdb4c5d5a900deb980db7cca9cf2f9fa36d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-1-review-the-architecture-for-physical-server-replication-to-azure"></a>Step 1: Revisar la arquitectura para la replicación de un servidor físico en Azure

En este artículo se describen los componentes y procesos que se usan cuando se replican servidores físicos Windows o Linux locales en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md).

Publique cualquier comentario en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="architectural-components"></a>Componentes de la arquitectura

En la tabla se resumen los componentes necesarios.

**Componente** | **Requisito** | **Detalles**
--- | --- | ---
**Las tablas de Azure** | Se necesita una cuenta de Azure, una cuenta de Azure Storage y una red de Azure. | Los datos replicados se almacenan en la cuenta de almacenamiento y se crean máquinas virtuales de Azure con los datos replicados cuando se produce la conmutación por error. Las máquinas virtuales de Azure se conectan a la red virtual de Azure cuando se crean.
**Servidor de configuración** | Un único servidor de administración local (servidor físico o máquina virtual de VMware) que ejecuta todos componentes locales de Site Recovery. Estos incluyen un servidor de configuración, un servidor de procesos y un servidor de destino maestro. | El componente servidor de configuración coordina las comunicaciones entre el entorno local y Azure y administra la replicación de datos.
 **Servidor de proceso**  | Se instala de forma predeterminada en el servidor de configuración. | Actúa como puerta de enlace de replicación. Recibe datos de replicación, los optimiza con almacenamiento en caché, compresión y cifrado y los envía al almacenamiento de Azure.<br/><br/> El servidor de procesos también controla la instalación de inserción del servicio Mobility en máquinas protegidas.<br/><br/> También puede agregar más servidores de procesos dedicados independientes para controlar mayores volúmenes de tráfico de replicación.
 **Servidor de destino principal** | Se instala de forma predeterminada en el servidor de configuración local. | Controla los datos de replicación durante la conmutación por recuperación desde Azure.<br/><br/> Si los volúmenes de tráfico de la conmutación por recuperación son altos, puede implementar un servidor de destino maestro independiente para la conmutación por recuperación.
**Servidores replicados** | El componente del servicio Mobility se instala en todos los servidores Windows o Linux que se desee replicar. Se puede instalar manualmente en cada equipo o con una instalación de inserción desde el servidor de procesos.
**Conmutación por recuperación** | Para la conmutación por recuperación se requiere una infraestructura de VMware. No se puede realizar la conmutación por recuperación a un servidor físico.


**Ilustración 1: Componentes de entorno físico a Azure**

![Componentes](./media/physical-walkthrough-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Proceso de replicación

1. Configure la implementación, incluidos los componentes de Azure y locales. En el almacén de Recovery Services, especifique el origen y el destino de la replicación, configure el servidor de configuración, cree una directiva de replicación, implemente el Servicio de movilidad, habilite la replicación y ejecute una conmutación por error de prueba.
2.  Las máquinas se replican de acuerdo con la directiva de replicación y una copia inicial de los datos se replica en Azure Storage.
4. Una vez terminada la replicación inicial, comienza la de los cambios incrementales en Azure. Las marcas de revisión de una máquina se conservan en un archivo .hrl.
    - Las máquinas que están en replicación se comunican con el servidor de configuración en el puerto HTTPS 443 entrante para la administración de la replicación.
    - Las máquinas en replicación envían datos de replicación al servidor de procesos del puerto HTTPS 9443 entrante (se puede modificar).
    - El servidor de configuración organiza la administración de la replicación con Azure a través del puerto HTTPS 443 saliente.
    - El servidor de procesos recibe datos de las máquinas de origen, los optimiza y los cifra y los envía al almacenamiento de Azure a través del puerto 443 saliente.
    - Si habilita la coherencia entre varias máquinas virtuales, las máquinas del grupo de replicación se comunican entre sí a través del puerto 20004. La implementación de varias máquinas virtuales se usa si agrupa varias máquinas en grupos de replicación que tienen puntos de recuperación coherentes con los bloqueos y coherentes con la aplicación cuando conmutan por error. Esto resulta útil si las máquinas ejecutan la misma carga de trabajo y necesitan ser coherentes.
5. El tráfico se replica en los puntos de conexión públicos del almacenamiento de Azure a través de Internet. Como alternativa, puede usar el [emparejamiento público](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) de Azure ExpressRoute. No se admite la replicación del tráfico a través de una VPN de sitio a sitio desde un sitio local en Azure.

**Ilustración 2: Replicación de entorno físico a Azure**

![Mejorada](./media/physical-walkthrough-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proceso de conmutación por error y conmutación por recuperación

Después de comprobar que la conmutación por error de prueba funciona según lo previsto, puede ejecutar las conmutaciones por error no planeadas en Azure según sea necesario. Cuando se ejecuta una conmutación por error, se crean máquinas virtuales de Azure a partir de los datos replicados. Luego, cuando el sitio local principal esté disponible de nuevo, podrá realizar una conmutación por recuperación. Observe lo siguiente:

- No se admite la conmutación por error planeada.
- La conmutación por error se puede realizar en una sola máquina, o bien crear [planes de recuperación](site-recovery-create-recovery-plans.md) para realizar la conmutación por error en máquinas a la vez.
- Realice una conmutación por error para iniciar el acceso a la carga de trabajo desde la máquina virtual de Azure de la réplica.
- Cuando el sitio principal vuelva a estar disponible, replique la máquina desde el sitio secundario al principal. Luego, realice una conmutación por error no planeada desde el sitio secundario. Después de ejecutar esta conmutación por error, los datos volverán a la ubicación local y deberá habilitar la replicación en Azure de nuevo.

Entre los componentes de la conmutación por recuperación se incluyen:

- **Servidor de procesos temporal en Azure**: necesita configurar una máquina virtual de Azure que actúe como servidor de procesos para controlar la replicación desde Azure. Dicha máquina virtual se puede eliminar cuando finalice la conmutación por recuperación.
- **Conexión VPN**: necesita una conmutación VPN (o Azure ExpressRoute) desde la red de Azure al sitio local.
- **Servidor de destino maestro local independiente**: el servidor de destino maestro local (que se instala de manera predeterminada en el servidor de configuración) controla la conmutación por recuperación. Si se realiza la conmutación por recuperación en grandes volúmenes de tráfico, debe configurar un servidor de destino maestro local independiente para este propósito.
- **Directiva de conmutación por recuperación**: necesita una directiva de conmutación por recuperación. Esta se crea automáticamente al crear la directiva de replicación.
- **Infraestructura de VMware**: debe conmutar por recuperación a una máquina virtual de VMware local. Esto significa que necesita una infraestructura de VMware local, incluso si está replicando servidores físicos locales a Azure.

**Ilustración 3: Conmutación por recuperación de servidor físico**

![Conmutación por recuperación](./media/physical-walkthrough-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Pasos siguientes

Vaya a [Paso 2: Comprobación de los requisitos previos y las limitaciones](physical-walkthrough-prerequisites.md)
