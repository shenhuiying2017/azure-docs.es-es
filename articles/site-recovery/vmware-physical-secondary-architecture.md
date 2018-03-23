---
title: Arquitectura de replicación de servidor físico o VMware en Azure Site Recovery | Microsoft Docs
description: Este artículo proporciona información general de los componentes y la arquitectura usados al replicar máquinas virtuales de VMware locales o servidores físicos de Windows o Linux a un sitio secundario de VMware con Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/06/2018
ms.author: raynew
ms.openlocfilehash: 97a990aa3ed9043280888900d8fc7b604b6c22b5
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="vmware-vmphysical-server-to-vmware-replication-architecture"></a>Arquitectura de replicación de servidor físico o VMware en VMware

En este artículo, se describe la arquitectura y los procesos que se usan al replicar, conmutar por error y recuperar máquinas virtuales de VMware locales, o servidores físicos de Windows o Linux, en un sitio secundario de VMware mediante [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Componentes de la arquitectura

**Ámbito** | **Componente** | **Detalles**
--- | --- | ---
**Las tablas de Azure** | Este escenario se implementa con InMage Scout. | Para obtener InMage Scout, necesitará una suscripción a Azure.<br/><br/> Después de crear un almacén de Recovery Services descargue InMage Scout e instale las actualizaciones más recientes para configurar la implementación.
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



## <a name="next-steps"></a>Pasos siguientes

[Configuración](vmware-physical-secondary-disaster-recovery.md) de la recuperación ante desastres de máquinas virtuales de VMware y servidores físicos en un sitio secundario.
