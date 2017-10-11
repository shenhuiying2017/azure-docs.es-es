---
title: "¿Qué es Azure Site Recovery? | Microsoft Docs"
description: "Proporciona información general sobre el servicio de Azure Site Recovery y resume escenarios de implementación."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/25/2017
ms.author: raynew
ms.openlocfilehash: aa657c92f347f7529affee78ad1842e5e066b74d
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="what-is-site-recovery"></a>¿Qué es Site Recovery?

¡Bienvenido al servicio Azure Site Recovery! Este artículo proporciona una descripción general del servicio.

## <a name="business-continuity-and-disaster-recovery-bcdr-with-azure-recovery-services"></a>Continuidad empresarial y recuperación ante desastres (BCDR) con Azure Recovery Services

Las organizaciones tienen que saber cómo mantener los datos seguros y las aplicaciones y cargas de trabajo en funcionamiento cuando se producen interrupciones tanto planeadas como no planeadas.

Azure Recovery Services colabora con su estrategia de BCDR:

- **Servicio Site Recovery**: Site Recovery ayuda a garantizar la continuidad empresarial, ya que mantiene las aplicaciones en funcionamiento tanto en las máquinas virtuales como en los servidores físicos disponibles si un sitio deja de funcionar. Site Recovery replica las cargas de trabajo que se ejecutan en máquinas virtuales y servidores físicos para que estén disponibles en una ubicación secundaria si el sitio principal no está disponible. Recupera las cargas de trabajo en el sitio principal cuando vuelva a estar en funcionamiento.
- **Servicio Backup**: Además, el servicio [Azure Backup](https://docs.microsoft.com/azure/backup/) mantiene los datos seguros y recuperables, ya que realiza copias de seguridad de los mismos en Azure.

Site Recovery puede administrar la replicación de:

- Máquinas virtuales de Azure que se replican entre regiones de Azure.
- Máquinas virtuales locales y servidores físicos que se replican en Azure o en un sitio secundario.


## <a name="what-does-site-recovery-provide"></a>¿Qué ofrece Site Recovery?

**Característica** | **Detalles**
--- | ---
**Implementación de una solución de BCDR simple** | Mediante Site Recovery se pueden configurar y administrar la replicación, la conmutación por error y la conmutación por recuperación desde una sola ubicación en Azure Portal.
**Replicación de máquinas virtuales de Azure** | La estrategia BCDR se puede configurar para que las máquinas virtuales de Azure se repliquen entre distintas regiones de Azure.
**Replicación de máquinas locales fuera del sitio** | Las máquinas virtuales y los servidores físicos se pueden replicar en Azure o en una ubicación local secundaria. Si se replican en Azure, se elimina el costo y la complejidad de mantener un centro de datos secundario.
**Replicación de cualquier carga de trabajo** | La replicación de cualquier carga de trabajo que se ejecuta en máquinas virtuales de Azure compatibles, máquinas virtuales de Hyper-V locales, máquinas virtuales VMware y servidores físicos Windows o Linux.
**Mantener los datos resistentes y seguros** | Site Recovery coordina la replicación sin interceptar los datos de las aplicaciones. Los datos replicados se almacenan en Azure Storage con toda la resistencia que proporciona. Cuando se produce la conmutación por error, las máquinas virtuales de Azure en función de los datos replicados.
**Cumplimiento de RTO y RPO** | Mantenga los objetivos de tiempo de recuperación (RTO) y los objetivos de punto de recuperación (RPO) dentro de los límites de la organización. Site Recovery proporciona una replicación continua tanto a las máquinas virtuales de Azure como a las máquinas virtuales VMware con una frecuencia de tan solo 30 segundos para Hyper-V. Para reducir aún más los objetivos de tiempo de recuperación (RTO), realice la integración con [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Mantener la coherencia de aplicaciones a través de la conmutación por error** | Los puntos de recuperación se pueden configurar con instantáneas coherentes con la aplicación. Las instantáneas coherentes con la aplicación capturan los datos del disco, todos los datos en que hay en la memoria y todas las transacciones en curso.
**Prueba sin interrupciones** | Puede ejecutar fácilmente conmutaciones por error de prueba para permitir maniobras de recuperación ante desastres, sin que ello afecte a la replicación en curso.
**Ejecución de conmutaciones por error flexibles** | Puede ejecutar conmutaciones por error planeadas para interrupciones planeadas sin pérdida de datos o conmutaciones por error no planeadas con una pérdida de datos mínima (según la frecuencia de replicación) ante desastres inesperados. Puede conmutar por recuperación a su sitio principal fácilmente cuando vuelva a estar disponible.
**Creación de planes de recuperación** | Puede personalizar y secuenciar la conmutación por error y la recuperación de aplicaciones de niveles múltiples en varias máquinas virtuales con planes de recuperación. Agrupe las máquinas en planes y agregue scripts y acciones manuales. Los planes de recuperación se pueden integrar con runbooks de Azure Automation.
**Integración con las tecnologías de BCDR existentes** | Site Recovery se integra con otras tecnologías de BCDR. Por ejemplo, puede utilizar Site Recovery para proteger el back-end de SQL Server de cargas de trabajo corporativas, incluida la compatibilidad nativa para SQL Server AlwaysOn, a fin de administrar la conmutación por error de grupos de disponibilidad.
**Integración con la biblioteca de Automation** | Una ingente biblioteca de Azure Automation proporciona scripts específicos de la aplicación y preparados para la producción que se pueden descargar e integrarse con Site Recovery.
**Administración de la configuración de la red** | Site Recovery se integra con Azure para simplificar la administración de la red de una aplicación, lo que incluye la reserva de direcciones IP, la configuración de equilibradores de carga o la integración de Azure Traffic Manager, con el fin de que los cambios de red sean eficientes.


## <a name="what-can-i-replicate"></a>¿Qué puedo replicar?

**Compatible** | **Detalles**
--- | ---
**¿Qué puedo replicar?** | Máquinas virtuales de Azure entre regiones de Azure (en versión preliminar)<br/><br/>  Máquinas virtuales VMware, máquinas virtuales de Hyper-V y servidores físicos (Windows y Linux) locales a Azure<br/<br/> Máquinas virtuales de VMware, máquinas virtuales de Hyper-V y servidores físicos a un sitio secundario. En el caso de las máquinas virtuales de Hyper-V, solo se admite la replicación a un sitio secundario si System Center VMM administra los hosts de Hyper-V.
**¿Qué regiones se admiten para Site Recovery?** | [Regiones admitidas](https://azure.microsoft.com/regions/services/) |
**¿Qué sistemas operativos necesitan las máquinas replicadas?** | [Requisitos de máquinas virtuales de Azure](site-recovery-support-matrix-azure-to-azure.md#support-for-replicated-machine-os-versions)<br></br>[Requisitos de máquinas virtuales VMWare](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> En el caso de las máquinas virtuales de Hyper-V, se admite cualquier [SO invitado](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) compatible con Hyper-V y Azure.<br/><br/> [Requisitos del servidor físico](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**¿Qué hosts o servidores VMware necesito?** | Las máquinas virtuales de VMware se pueden ubicar en [hosts de vSphere/servidores de vCenter compatibles](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)
**¿Qué cargas de trabajo puedo replicar?** | Puede replicar cualquier carga de trabajo que se ejecute en una máquina de replicación admitida. Además, el equipo de Site Recovery ha realizado pruebas específicas para [diversas aplicaciones](site-recovery-workload.md#workload-summary).


## <a name="azure-portal-considerations"></a>Consideraciones de Azure Portal

* Site Recovery se puede implementar en [Azure Portal](https://portal.azure.com).
* En el Portal de Azure clásico, Site Recovery se puede administrar con el modelo de administración de servicios clásico.
- El portal clásico solo se debe usar para mantener las implementaciones existentes de Site Recovery. No se pueden crear almacenes nuevos en el portal clásico.

## <a name="next-steps"></a>Pasos siguientes
* Más información sobre [compatibilidad con cargas de trabajo](site-recovery-workload.md).
* Introducción a la [replicación de máquinas virtuales de Azure entre regiones](site-recovery-azure-to-azure.md), la [replicación de VMware en Azure](vmware-walkthrough-overview.md) o la [replicación de Hyper-V en Azure](hyper-v-site-walkthrough-overview.md).
