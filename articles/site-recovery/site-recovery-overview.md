---
title: Acerca de Azure Site Recovery | Microsoft Docs
description: "Proporciona información general sobre el servicio de Azure Site Recovery y resume escenarios de implementación."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: overview
ms.date: 01/07/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4cc5d849630114577709f8373d08f0ce659e7e65
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="about-site-recovery"></a>Acerca de Site Recovery

¡Bienvenido al servicio Azure Site Recovery! En este artículo se ofrece una rápida información general del servicio.

Como organización, necesita adoptar una estrategia de continuidad empresarial y de recuperación ante desastres (BCDR) que mantenga sus datos seguros, y sus aplicaciones y cargas de trabajo en pleno funcionamiento cuando se produzcan interrupciones planeadas o imprevistas.

Azure Recovery Services colabora con su estrategia de BCDR:

- **Servicio Site Recovery**: Site Recovery ayuda a garantizar la continuidad empresarial manteniendo las aplicaciones y cargas de trabajo empresariales en funcionamiento durante las interrupciones. Site Recovery replica las cargas de trabajo que se ejecutan en máquinas físicas y virtuales desde un sitio principal a una ubicación secundaria. Cuando se produce una interrupción en el sitio principal, se conmuta por error a la ubicación secundaria y se accede desde allí a las aplicaciones. Cuando la ubicación principal vuelva a estar en ejecución, puede realizar la conmutación por recuperación en ella.  
- **Servicio Backup**: el servicio [Azure Backup](https://docs.microsoft.com/azure/backup/) mantiene los datos seguros y recuperables, ya que realiza copias de seguridad de los mismos en Azure.

Site Recovery puede administrar la replicación de:

- Máquinas virtuales de Azure que se replican entre regiones de Azure.
- Máquinas virtuales locales y servidores físicos que se replican en Azure o en un sitio secundario.


## <a name="what-does-site-recovery-provide"></a>¿Qué ofrece Site Recovery?


**Característica** | **Detalles**
--- | ---
**Solución de BCDR simple** | Mediante Site Recovery se pueden configurar y administrar la replicación, la conmutación por error y la conmutación por recuperación desde una sola ubicación en Azure Portal.
**Replicación de máquinas virtuales de Azure** | Puede configurar la recuperación ante desastres de máquinas virtuales de Azure de una región primaria a una secundaria.
**Replicación de máquinas virtuales local** | Las máquinas virtuales y los servidores físicos se pueden replicar en Azure o en un centro de datos local secundario. Si se replican en Azure, se elimina el costo y la complejidad de mantener un centro de datos secundario.
**Replicación de la carga de trabajo** | La replicación de cualquier carga de trabajo que se ejecuta en máquinas virtuales de Azure compatibles, máquinas virtuales de Hyper-V y VMware locales y servidores físicos Windows o Linux.
**Resistencia de datos** | Site Recovery coordina la replicación sin interceptar los datos de las aplicaciones. Cuando se realiza la replicación en Azure, los datos se almacenan en Azure Storage con toda la resistencia que proporciona. Cuando se produce la conmutación por error, las máquinas virtuales de Azure en función de los datos replicados.
**Destinos RTO y el RPO** | Mantenga los objetivos de tiempo de recuperación (RTO) y los objetivos de punto de recuperación (RPO) dentro de los límites de la organización. Site Recovery proporciona una replicación continua tanto a las máquinas virtuales de Azure como a las máquinas virtuales VMware con una frecuencia de tan solo 30 segundos para Hyper-V. Para reducir aún más el RTO, realice la integración con [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Mantener la coherencia de aplicaciones a través de la conmutación por error** | Puede realizar la replicación mediante puntos de configuración con instantáneas coherentes con la aplicación. Estas instantáneas capturan los datos del disco, todos los datos en que hay en la memoria y todas las transacciones en curso.
**Pruebas sin interrupciones** | Puede ejecutar fácilmente maniobras de recuperación ante desastres, sin que ello afecte a la replicación en curso.
**Conmutaciones por error flexibles** | Puede ejecutar conmutaciones por error planeadas para interrupciones planeadas sin pérdida de datos o conmutaciones por error no planeadas con una pérdida de datos mínima (según la frecuencia de replicación) ante desastres inesperados. Puede conmutar por recuperación a su sitio principal fácilmente cuando vuelva a estar disponible.
**Planes de recuperación personalizados** | Con los planes de recuperación, puede personalizar y secuenciar la conmutación por error y la recuperación de aplicaciones de niveles múltiples en varias máquinas virtuales. Puede agrupar las máquinas en un plan de recuperación y, opcionalmente, agregar scripts y acciones manuales. Los planes de recuperación se pueden integrar con runbooks de Azure Automation.
**Integración de BCDR** | Site Recovery se integra con otras tecnologías de BCDR. Por ejemplo, puede utilizar Site Recovery para proteger el back-end de SQL Server de cargas de trabajo corporativas, con compatibilidad nativa para SQL Server AlwaysOn, a fin de administrar la conmutación por error de grupos de disponibilidad.
**Integración de Azure Automation** | Una ingente biblioteca de Azure Automation proporciona scripts específicos de la aplicación y preparados para la producción que se pueden descargar e integrarse con Site Recovery.
**Integración de red** | Site Recovery se integra con Azure para simplificar la administración de la red de una aplicación, lo que incluye la reserva de direcciones IP, la configuración de equilibradores de carga o la integración de Azure Traffic Manager, con el fin de que los cambios de red sean eficientes.


## <a name="what-can-i-replicate"></a>¿Qué puedo replicar?

**Compatible** | **Detalles**
--- | ---
**Escenarios de replicación** | Replique las máquinas virtuales de Azure de una región de Azure a otra.<br/><br/>  Replique las máquinas virtuales VMware, máquinas virtuales de Hyper-V y servidores físicos (Windows y Linux) locales en Azure.<br/><br/> Replique las máquinas virtuales locales de VMware, de Hyper-V administradas por System Center VMM y los servidores físicos en un sitio secundario.
**Regiones** | Revise las [regiones admitidas](https://azure.microsoft.com/regions/services/) para Site Recovery. |
**Máquinas replicadas** | Revise los requisitos de replicación para las [máquinas virtuales de Azure](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions), las [máquinas virtuales locales](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) y los [servidores físicos](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions).
**Servidores o hosts de VMware** | Las máquinas virtuales de VMware que quiere replicar se pueden ubicar en [servidores vCenter o hosts vSphere compatibles](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
**Cargas de trabajo** | Puede replicar cualquier carga de trabajo que se ejecute en una máquina que se admita para la replicación. Además, el equipo de Site Recovery ha realizado pruebas específicas para [diversas aplicaciones](site-recovery-workload.md#workload-summary).



## <a name="next-steps"></a>pasos siguientes
* Más información sobre la [compatibilidad con cargas de trabajo](site-recovery-workload.md).
* Empiece a trabajar con la [replicación de máquina virtual de Azure entre regiones](azure-to-azure-quickstart.md). 
