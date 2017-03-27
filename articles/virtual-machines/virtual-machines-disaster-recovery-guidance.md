---
title: "Escenarios de recuperación ante desastres para las máquinas virtuales de Azure | Microsoft Docs"
description: "Obtenga información sobre qué hacer si se produce una interrupción del servicio de Azure que afecta a las máquinas virtuales de Azure."
services: virtual-machines
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: kmouss;aglick
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b1fb88277f6da86c023cd63af0d7c2c5681f8be0
ms.lasthandoff: 03/21/2017


---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>Qué hacer si se produce una interrupción del servicio de Azure que afecta a las máquinas virtuales de Azure
En Microsoft, hacemos todo lo posible para garantizar que nuestros servicios estén siempre disponibles cuando los necesite. En ocasiones, debido a factores externos que escapan de nuestro control, se producen interrupciones de servicio no planeadas.

Microsoft proporciona Acuerdos de Nivel de Servicio para sus servicios como un compromiso en cuanto al tiempo de actividad y la conectividad. Puede encontrar el Acuerdo de Nivel de Servicio para los diferentes servicios de Azure en [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).

Azure ya integra en su plataforma muchas características que admiten aplicaciones de alta disponibilidad. Para obtener más información sobre estos servicios, lea [Recuperación ante desastres y alta disponibilidad para aplicaciones de Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

En este artículo se expone un escenario real de recuperación ante desastres en el que toda una región experimenta una interrupción debido a un desastre natural importante o a una interrupción del servicio generalizada. Se trata de casos muy infrecuentes, pero debe estar preparado para la posibilidad de que se produzca una interrupción en toda una región. Si una región completa experimenta una interrupción del servicio, las copias con redundancia local de los datos estarían temporalmente no disponibles. Si ha habilitado la replicación geográfica, se almacenan en una región distinta tres copias adicionales de los blobs y las tablas de Almacenamiento de Azure. En caso de una interrupción completa en una región o de un desastre en el que la región primaria no sea recuperable, Azure reasignará todas las entradas DNS a la región de replicación geográfica.

> [!NOTE]
> Tenga en cuenta que no tiene ningún control sobre este proceso y que solo se producirá si se produce una interrupción del servicio en toda la región. Por este motivo, también debe confiar en otras estrategias de copia de seguridad específicas de la aplicación para lograr el máximo nivel de disponibilidad. Para obtener más información, consulte la sección sobre las [estrategias de datos para la recuperación ante desastres](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications#data-strategies-for-disaster-recovery).
>
>

Para ayudarlo a administrar estos eventos poco frecuentes, le proporcionamos las siguientes orientaciones para máquinas virtuales de Azure destinadas a los casos de interrupción del servicio en toda una región donde se ha implementado la aplicación de máquina virtual de Azure.

## <a name="option-1-wait-for-recovery"></a>Opción 1: esperar a que se recupere el servicio
En este caso, no se requieren acciones por su parte. Sabe que trabajaremos con rapidez para que el servicio de Azure vuelva a estar disponible. El estado actual del servicio se puede ver en el [panel de estado de los servicios de Azure](https://azure.microsoft.com/status/).

> [!NOTE]
> Esta es la mejor opción si no ha configurado Azure Site Recovery, la copia de seguridad de máquina virtual, el almacenamiento con redundancia geográfica con acceso de lectura o el almacenamiento con redundancia geográfica antes de la interrupción. Si ha configurado el almacenamiento con redundancia geográfica o el almacenamiento con redundancia geográfica con acceso de lectura para la cuenta de almacenamiento donde se almacenan los discos duros virtuales de la máquina virtual (VHD), puede fijarse en cómo recuperar el VHD de la imagen base y tratar de aprovisionar una nueva máquina virtual desde ahí. Esta no es la mejor opción, ya que no hay ninguna garantía de sincronización de datos, a menos que se utilice la copia de seguridad de máquina virtual de Azure Site Recovery. Por lo tanto, no se garantiza que esta opción funcione.
>
>

Para aquellos clientes que desean acceder de inmediato a las máquinas virtuales, están disponibles las dos opciones siguientes.  

> [!NOTE]
> Tenga en cuenta que en las dos es posible que se produzca una pérdida de datos.     
>
>

## <a name="option-2-restore-a-vm-from-a-backup"></a>Opción 2: restaurar una máquina virtual desde una copia de seguridad
En el caso de los clientes que tienen una copia de seguridad de máquina virtual configurada, puede restaurar dicha máquina virtual desde su punto de copia de seguridad y recuperación.

Para saber cómo restaurar una máquina virtual nueva desde Copia de seguridad de Azure, consulte [Restauración de máquinas virtuales en Azure](../backup/backup-azure-restore-vms.md).

Si necesita ayuda para planear la infraestructura de copia de seguridad de máquinas virtuales de Azure, lea el artículo [Planeación de la infraestructura de copia de seguridad de máquinas virtuales en Azure](../backup/backup-azure-vms-introduction.md).

## <a name="option-3-initiate-a-failover-by-using-azure-site-recovery"></a>Opción 3: iniciar una conmutación por error con Azure Site Recovery
Si ha configurado Azure Site Recovery para utilizar las máquinas virtuales de Azure afectadas, puede restaurarlas desde sus réplicas. Estas réplicas pueden residir en Azure o en el equipo local. En este caso, puede crear una nueva máquina virtual desde su réplica existente. Si desea restaurar las máquinas virtuales desde una réplica de Azure Site Recovery, consulte [Migración de máquinas virtuales de IaaS de Azure entre regiones de Azure con Azure Site Recovery](../site-recovery/site-recovery-migrate-azure-to-azure.md).

> [!NOTE]
> Aunque el sistema operativo de máquina virtual de Azure y los discos de datos se repliquen en un VHD secundario, si están en una cuenta de almacenamiento con redundancia geográfica o de almacenamiento con redundancia geográfica con acceso de lectura, cada disco duro virtual se replica por separado. Este nivel de replicación no garantiza la coherencia entre los discos duros virtuales replicados. Si su aplicación o las bases de datos que usan estos discos de datos tienen dependencias entre sí, no se garantiza que todos los discos duros virtuales se repliquen como una instantánea. Tampoco se garantiza que la réplica del disco duro virtual desde el almacenamiento con redundancia geográfica o el almacenamiento con redundancia geográfica con acceso de lectura dé lugar a una instantánea coherente entre aplicaciones para arrancar la máquina virtual.
>
>

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre cómo implementar una estrategia de alta disponibilidad y recuperación ante desastres, consulte [Recuperación ante desastres y alta disponibilidad para aplicaciones de Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Para obtener unos conocimientos técnicos detallados sobre las funcionalidades de una plataforma de nube, consulte [Guía técnica sobre resistencia en Azure](../resiliency/resiliency-technical-guidance.md).

Para obtener información sobre cómo realizar copias de seguridad de máquinas virtuales, consulte [Copia de seguridad de máquinas virtuales de Azure](../backup/backup-azure-vms.md).

Si desea aprender a utilizar Azure Site Recovery para organizar y automatizar la protección de las máquinas físicas (y virtuales) Windows y Linux que se ejecutan en máquinas virtuales de Hyper-V y VMWare, consulte [Azure Site Recovery](https://azure.microsoft.com/documentation/learning-paths/site-recovery/).

Si las instrucciones no están claras, o si desea que Microsoft realice las operaciones en su lugar, póngase en contacto con el [servicio de asistencia al cliente](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

