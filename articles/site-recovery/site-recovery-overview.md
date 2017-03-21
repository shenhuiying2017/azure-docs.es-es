---
title: "¿Qué es Site Recovery? | Microsoft Docs"
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
ms.date: 03/14/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: d8e4e4bb7dd1e40d8c561adba04b8346fcb2127d
ms.lasthandoff: 03/15/2017


---
# <a name="what-is-site-recovery"></a>¿Qué es Site Recovery?

¡Bienvenido al servicio Azure Site Recovery! Este artículo proporciona una descripción general del servicio.

Las interrupciones tienen su origen en fenómenos naturales y errores de funcionamiento. Su organización necesita una estrategia de recuperación ante desastres y continuidad del negocio (BCDR) que permita que durante los tiempos de inactividad planeados e imprevistos, los datos estén protegidos, las aplicaciones se mantengan disponibles y la actividad recupere las condiciones de trabajo normales lo antes posible.

Azure Recovery Services colabora con su estrategia de BCDR. El servicio [Azure Backup](https://docs.microsoft.com/en-us/azure/backup/) mantiene los datos seguros y recuperables. Site Recovery replica cargas de trabajo, las conmuta por error y las recupera, de manera que estén disponibles cuando se produzca un error.

## <a name="what-does-site-recovery-provide"></a>¿Qué ofrece Site Recovery?

- **Recuperación ante desastres en la nube**: puede replicar las cargas de trabajo que se ejecutan en las máquinas virtuales y servidores físicos en Azure, en lugar de hacerlo en un sitio secundario. Esto elimina el costo y la complejidad de mantener un centro de datos secundario.
- **Replicación flexible para entornos híbridos**: puede replicar cualquier carga de trabajo que se ejecuta en máquinas virtuales de Hyper-V, máquinas virtuales VMware y servidores físicos Windows/Linux locales.
- **Migración**: puede usar Site Recovery para migrar instancias de AWS locales a máquinas virtuales de Azure, o bien para migrar máquinas virtuales de Azure entre regiones de Azure.
- **BCDR simplificada**: puede implementar la replicación desde una ubicación única en Azure Portal.  Puede ejecutar conmutaciones por error o conmutaciones por recuperación simples de una o varias máquinas.
- **Resistencia**: Site Recovery organiza la replicación y la conmutación por error, sin interceptar los datos de aplicaciones.
Los datos replicados se almacenan en Azure Storage con toda la resistencia que proporciona. Cuando se produce la conmutación por error, las máquinas virtuales de Azure en función de los datos replicados.
- **Rendimiento de la replicación**: Site Recovery proporciona una frecuencia de replicación de tan solo 30 segundos para Hyper-V y replicación continua para VMware. Puede establecer umbrales de objetivo de punto de recuperación (RPO) para controlar la frecuencia de creación de los puntos de recuperación de datos, y también puede reducir el objetivo de tiempo de recuperación (RTO) con el proceso de recuperación automatizado de Site Recovery e integración con [Azure Traffic Manager](https://azure.microsoft.com/en-us/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)
- **Coherencia con la aplicación**: las máquinas se replican mediante instantáneas coherentes con la aplicación. Además de capturar los datos del disco, las instantáneas coherentes con la aplicación capturan todos los datos en memoria y todas las transacciones en curso.
- **Pruebas sin interrupción**: puede ejecutar fácilmente conmutaciones por error de prueba para permitir maniobras de recuperación ante desastres sin que los entornos de producción se vean afectados.
- **Conmutación por error y recuperación flexible**: puede ejecutar conmutaciones por error planeadas para interrupciones previstas sin pérdida de datos o conmutaciones por error imprevistas con una pérdida de datos mínima (según la frecuencia de replicación) ante desastres inesperados. Puede conmutar por recuperación a su sitio principal fácilmente cuando vuelva a estar disponible.
- **Planes de recuperación personalizados**: los planes de recuperación le permiten modelar y personalizar la conmutación por error y la recuperación de aplicaciones de niveles múltiples que se distribuyen en varias máquinas virtuales. Ordena los grupos dentro de planes y agrega scripts y acciones manuales. Los planes de recuperación se pueden integrar con runbooks de Azure Automation.
- **Aplicaciones de niveles múltiples**: puede crear planes de recuperación para la conmutación por error y recuperación en secuencia de aplicaciones de niveles múltiples. Puede agrupar las máquinas en distintos niveles (por ejemplo, base de datos, Web, aplicación) dentro de un plan de recuperación y personalizar la manera en que cada grupo ejecuta la conmutación por error y se inicia.
* **Integración con tecnologías de BCDR existentes**: Site Recovery se integra con otras tecnologías de BCDR. Por ejemplo, puede utilizar Site Recovery para proteger el back-end de SQL Server de cargas de trabajo corporativas, incluida la compatibilidad nativa para SQL Server AlwaysOn, a fin de administrar la conmutación por error de grupos de disponibilidad.
* **Integración con la biblioteca de automatización**: una rica biblioteca de Azure Automation proporciona scripts específicos de la aplicación y preparados para la producción que pueden descargarse e integrarse con Site Recovery.
* **Administración de red simple**: la administración de red avanzada en Site Recovery y Azure simplifica los requisitos de red para una aplicación, incluida la reserva de direcciones IP, la configuración de equilibradores de carga o la integración de Azure Traffic Manager para conmutaciones de red eficientes.


## <a name="whats-supported"></a>¿Qué se admite?

**Compatible** | **Detalles**
--- | ---
**¿Qué regiones se admiten para Site Recovery?** | [Regiones admitidas](https://azure.microsoft.com/en-us/regions/services/) |
**¿Qué puedo replicar?** | Máquinas virtuales de VMware, máquinas virtuales de Hyper-V y servidores físicos Windows y Linux locales.
**¿Qué sistemas operativos necesitan las máquinas replicadas?** | [Sistemas operativos admitidos](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) para máquinas virtuales de VMware<br/><br/> En el caso de las máquinas virtuales de Hyper-V, se admite cualquier [SO invitado](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) compatible con Hyper-V y Azure.<br/><br/> [Sistemas operativos](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) para servidores físicos
**¿A dónde puedo replicar?** | En Azure Storage o en un centro de datos secundario<br/><br/> En el caso de Hyper-V, solo las máquinas virtuales en hosts de Hyper-V que se administran en nubes de System Center VMM se pueden replicar a un centro de datos secundario.
**¿Qué hosts o servidores VMware necesito?** | [Servidores vCenter o hosts vSphere compatibles](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers) pueden administrar las máquinas virtuales de VMware que quiere replicar.
**¿Qué cargas de trabajo puedo replicar?** | Puede replicar cualquier carga de trabajo que se ejecute en una máquina de replicación admitida. Además, el equipo de Site Recovery ha realizado pruebas específicas para [diversas aplicaciones](site-recovery-workload.md#workload-summary).


## <a name="which-azure-portal"></a>¿Qué portal de Azure?

* Site Recovery se puede implementar tanto en el [Azure Portal](https://portal.azure.com) más reciente como en el [Portal de Azure clásico](https://manage.windowsazure.com/) .
* En el Portal de Azure clásico, Site Recovery se puede usar con el modelo de administración de servicios clásico.
* En Azure Portal, puede admitir el modelo clásico, o bien el [modelo de implementación Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md) más reciente.
- El portal clásico solo se debe usar para mantener las implementaciones existentes de Site Recovery. No se pueden crear almacenes nuevos en el portal clásico.

## <a name="next-steps"></a>Pasos siguientes
* Más información sobre [compatibilidad con cargas de trabajo](site-recovery-workload.md).
* Más información sobre [arquitectura y componentes de Site Recovery](site-recovery-components.md)

