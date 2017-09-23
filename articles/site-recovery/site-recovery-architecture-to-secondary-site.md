---
title: "Funcionamiento de la replicación de máquinas locales a un sitio local secundario en Azure Site Recovery | Microsoft Docs"
description: "Este artículo proporciona información general de los componentes y la arquitectura usados al replicar máquinas virtuales locales y servidores físicos a un sitio secundario con el servicio Azure Site Recovery."
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
ms.topic: get-started-article
ms.date: 05/29/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: fca95c63964b955db7ddfbe53250702cc8af122e
ms.contentlocale: es-es
ms.lasthandoff: 06/15/2017

---
# <a name="how-does-on-premises-machine-replication-to-a-secondary-site-work-in-site-recovery"></a>Funcionamiento de la replicación de máquinas locales a un sitio secundario en Site Recovery

En este artículo se describen los componentes y procesos que tienen lugar al replicar máquinas virtuales locales y servidores físicos a Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md).

Puede replicar lo siguiente en un sitio local secundario:
- Máquinas virtuales de Hyper-V locales en hosts independientes y clústeres de Hyper-V que se administran en nubes de System Center Virtual Machine Manager (VMM).
- Máquinas virtuales de VMware locales y servidores físicos de Windows y Linux. En este escenario, la replicación se administra con Scout.

Publique cualquier comentario que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="replicate-hyper-v-vms-to-a-secondary-on-premises-site"></a>Replicación de máquinas virtuales de Hyper-V a un sitio local secundario


### <a name="architectural-components"></a>Componentes de la arquitectura

Esto es lo que necesita para la replicación de máquinas virtuales Hyper-V en un sitio secundario.

**Componente** | **Ubicación** | **Detalles**
--- | --- | ---
**Las tablas de Azure** | Necesita una cuenta de Microsoft. |
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

**Figura 1: Replicación de VMM a VMM**

![De local a local](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback-process"></a>Proceso de conmutación por error y conmutación por recuperación

1. Puede ejecutar una [conmutación por error](site-recovery-failover.md), planeada o no, entre sitios locales. Si ejecuta una conmutación por error planeada, las máquinas virtuales de origen se apagan para garantizar que no se pierdan datos.
2. Puede conmutar por error una única máquina o crear [planes de recuperación](site-recovery-create-recovery-plans.md) para organizar la conmutación por error de varias máquinas.
4. Si realiza una conmutación por error no planeada en un sitio secundario, tras ella las máquinas de la ubicación secundaria no están habilitadas para su protección ni replicación. Si ejecutó una conmutación por error planeada, tras ella las máquinas de la ubicación secundaria están protegidas.
5. Después, ejecute una conmutación por error para iniciar el acceso a la carga de trabajo desde la máquina virtual de la réplica.
6. Cuando el sitio principal esté disponible de nuevo, inicie la replicación inversa para replicar desde el sitio secundario al principal. La replicación inversa pone las máquinas virtuales en un estado protegido pero el centro de datos secundario sigue siendo la ubicación activa.
7. Para que el sitio principal se convierta de nuevo en la ubicación activa, debe iniciar una conmutación por error planeada desde el secundario al principal, seguida de otra replicación inversa.




## <a name="replicate-vmware-vmsphysical-servers-to-a-secondary-site"></a>Replicación de máquinas virtuales de VMware o servidores físicos en un sitio secundario

Replique máquinas virtuales de VMware o servidores físicos a un sitio secundario utilizando InMage Scout, mediante estos componentes de arquitectura:


### <a name="architectural-components"></a>Componentes de la arquitectura

**Componente** | **Ubicación** | **Detalles**
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

**Figura 2: Replicación de VMware a VMware**

![VMware a VMware](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="next-steps"></a>Pasos siguientes

Revise la [matriz de compatibilidad](site-recovery-support-matrix-to-sec-site.md).

