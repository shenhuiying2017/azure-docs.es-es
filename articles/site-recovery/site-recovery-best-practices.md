---
title: "Preparación de la implementación de Azure Site Recovery | Microsoft Docs"
description: "En este artículo se describe cómo prepararse para la implementación de la replicación con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/06/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 5ed46a6bd8931d3b8829a053ace12fd219f108c4
ms.openlocfilehash: 6430ae9a97888ef993cd3dd35eea765fe95bb43f

---

# <a name="prepare-for-azure-site-recovery-deployment"></a>Preparación de la implementación de Azure Site Recovery

Las organizaciones necesitan una estrategia de recuperación ante desastres y continuidad empresarial (BCDR) que determine cómo seguirán en funcionamiento y disponibles las aplicaciones, las cargas de trabajo y los datos durante los tiempos de inactividad planeados y no planeados, y cómo recuperar las condiciones de funcionamiento normales lo antes posible. Site Recovery es un servicio de Azure que contribuye a su estrategia de BCDR mediante la coordinación de la replicación de servidores físicos locales y máquinas virtuales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error a la ubicación secundaria para mantener disponibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal. Más información en [¿Qué es Site Recovery?](site-recovery-overview.md)

En este artículo se resumen los requisitos de alto nivel de cada escenario de replicación de Site Recovery y se proporcionan vínculos a tutoriales detallados de implementación.  

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="azure-deployment-models"></a>Modelos de implementación de Azure

Azure tiene dos [modelos de implementación](../azure-resource-manager/resource-manager-deployment-model.md) diferentes para crear recursos y trabajar con ellos: el modelo de Azure Resource Manager y el clásico. Azure también tiene dos portales: el [Portal de Azure clásico](https://manage.windowsazure.com/) que admite el modelo de implementación clásico y [Azure Portal](https://ms.portal.azure.com/) que es compatible con ambos modelos de implementación.

Los nuevos escenarios de Site Recovery deben implementarse en [Azure Portal](https://portal.azure.com). En este portal se proporcionan nuevas características y una experiencia de implementación mejorada. Los almacenes pueden mantenerse en el portal clásico, pero no se pueden crear.


## <a name="deployment-scenarios"></a>Escenarios de implementación

Estos son los elementos que puede replicar con Site Recovery.

| **Implementación** | **Detalles** | **Portal de Azure** | **Portal clásico** | **Implementación de PowerShell** |
| --- | --- | --- | --- | --- |
| [VMware a Azure](site-recovery-vmware-to-azure.md) | Replicación de máquinas virtuales de VMware locales a Azure Storage | Use Resource Manager o las redes y el almacenamiento clásicos. Realice la conmutación por error en las máquinas virtuales clásicas o basadas en Resource Manager. | Solo en el modo mantenimiento. No se pueden crear almacenes nuevos. | No se admite actualmente. |
| [Servidores físicos en Azure](site-recovery-vmware-to-vmware.md) | Replique servidores físicos de Windows/Linux en almacenamiento de Azure. | Use Resource Manager o las redes y el almacenamiento clásicos. Realice la conmutación por error en las máquinas virtuales clásicas o basadas en Resource Manager. | Solo en el modo mantenimiento. No se pueden crear almacenes nuevos.  |
| [Máquinas virtuales de Hyper-V de nubes VMM en Azure](site-recovery-vmm-to-azure.md) | Replique máquinas virtuales de Hyper-V local en nubes VMM en Azure Storage.<br/><br/> | Use Resource Manager o las redes y el almacenamiento clásicos. Realice la conmutación por error en las máquinas virtuales clásicas o basadas en Resource Manager.   | Solo en el modo mantenimiento. No se pueden crear almacenes nuevos. | Compatible |
| [VM de Hyper-V en Azure (sin VMM)](site-recovery-hyper-v-site-to-azure.md) | Replique máquinas virtuales de Hyper-V locales en Azure Storage. | Use Resource Manager o las redes y el almacenamiento clásicos. Realice la conmutación por error en las máquinas virtuales clásicas o basadas en Resource Manager. | Solo en el modo mantenimiento. No se pueden crear almacenes nuevos. | Compatible |
| Servidores físicos o máquinas virtuales de VMware en sitios secundarios(site-recovery-vmware-to-vmware.md) | Replique máquinas virtuales de VMware o servidores físicos de Windows/Linux en un sitio secundario.<br/><br/> [Descargue la guía de ayuda](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) de InMage Scout. | No está disponible en el Portal de Azure. | Descargue InMage Scout desde un almacén de Site Recovery. | No compatible |
| [Máquinas virtuales de Hyper-V en un sitio secundario](site-recovery-vmm-to-vmm.md) | Replique las máquinas virtuales de Hyper-V de nubes VMM en una nube secundaria.  | La replicación utiliza la réplica de Hyper-V estándar. SAN no es compatible. | Realice la replicación mediante la réplica de Hyper-V o la [replicación de SAN](site-recovery-vmm-san.md). | Compatible |

## <a name="requirements-for-replication-to-azure"></a>Requisitos para la replicación en Azure

| **Requisito** | **Detalles** |
| --- | --- |
| **Cuenta de Azure** | Una cuenta de [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) sobre los precios de Site Recovery. |
| **Almacenamiento de Azure** | Los datos replicados se almacenan en almacenamiento de Azure y las máquinas virtuales de Azure se crean cuando se produce la conmutación por error. Debe agregar una [cuenta de Azure Storage](../storage/storage-introduction.md).<br/><br/>En Azure Portal, use [GRS](../storage/storage-redundancy.md#geo-redundant-storage) o el almacenamiento LRS. El portal clásico solo admite GRS.<br/><br/> Si replica servidores físicos o máquinas virtuales de VMware en Azure Portal, se admite Premium Storage.<br/><br/>  |
| **Red de Azure** | Necesita una red de Azure para que las máquinas virtuales de Azure se conecten. <br/><br/> En Azure Portal, puede usar una red clásica o de Resource Manager.<br/><br/> La red debe estar en la misma región que el almacén.<br/><br/> Si realiza la replicación desde VMM a Azure, configurará la [asignación de red](site-recovery-network-mapping.md) entre redes de máquinas virtuales VMM y redes de Azure, para asegurarse de que las máquinas virtuales de Azure conectarse a las redes adecuadas después de la conmutación por error. |
| **Local** |**Máquinas virtuales de VMware**: una máquina local que ejecuta los componentes de Site Recovery. Además, necesita servidores vCenter o hosts de VMware vSphere y las máquinas virtuales que desee replicar. [Más información](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites).<br/><br/> **Servidores físicos**: una máquina local que ejecuta los componentes de Site Recovery y los servidores físicos que desee replicar. [Más información](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites). Si desea [realizar la conmutación por recuperación](site-recovery-failback-azure-to-vmware.md) en servidores físicos desde Azure, necesita una infraestructura de VMware.<br/><br/> **Máquinas virtuales de Hyper-V**: un servidor VMM y hosts de Hyper-V que contienen las máquinas virtuales que desee replicar. [Más información](site-recovery-vmm-to-azure.md#on-premises-prerequisites).<br/><br/> Para replicar máquinas virtuales de Hyper-V sin VMM, solo necesita los hosts de Hyper-V. [Más información](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites). |
| **Máquinas protegidas** | Las máquinas protegidas que se replicarán en Azure deben cumplir los [requisitos previos de Azure](#azure-virtual-machine-requirements) que se describen a continuación. |

## <a name="requirements-for-replication-to-a-secondary-site"></a>Requisitos para realizar la replicación en un sitio secundario

| **Requisito** | **Detalles** |
| --- | --- |
| **Las tablas de Azure** | Una cuenta de [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) sobre los precios de Site Recovery. |
| **Local** |**Máquinas virtuales de VMware**: en el sitio principal necesitará un servidor de procesos para el almacenamiento en caché, la compresión y el cifrado de los datos de replicación. En el sitio secundario podrá instalar un servidor de configuración para administrar y supervisar la implementación y un servidor de destino principal. También se recomienda un servidor vContinuum para facilitar la administración. Además, necesita uno o varios hosts de VMware vSphere y, opcionalmente, un servidor vCenter. [Más información](site-recovery-vmware-to-vmware.md)<br/><br/> **Máquinas virtuales de Hyper-V en nubes VMM**: los servidores VMM y los hosts de Hyper-V que contienen las máquinas virtuales que desea replicar. [Más información](site-recovery-vmm-to-vmm.md#on-premises-prerequisites). |
| **Red (VMM a VMM)** | Si está replicando desde VMM en VMM, configurará [la asignación de red](site-recovery-network-mapping.md) para garantizar que las máquinas virtuales de réplica estén conectadas a las redes adecuadas después de la conmutación por error y que se coloquen de forma óptima en los hosts de Hyper-V. |

## <a name="url-access"></a>Acceso a direcciones URL

Estas direcciones URL deben estar disponibles desde los servidores de host de Hyper-V, VMM y VMware.

**URL** | **VMM a VMM** | **VMM a Azure** | **Hyper-V en Azure** | **VMware a Azure** |
|--- | --- | --- | --- | ---
``*.accesscontrol.windows.net`` | PERMITIR | Permitir | Permitir | Permitir
``*.backup.windowsazure.com`` | No se requiere | Permitir | Permitir | Permitir
``*.hypervrecoverymanager.windowsazure.com`` | Permitir | Permitir | Permitir | Permitir
``*.store.core.windows.net`` | Permitir | Permitir | Permitir | Permitir
``*.blob.core.windows.net`` | No se requiere | Permitir | Permitir | Permitir
``https://www.msftncsi.com/ncsi.txt`` | Permitir | Permitir | Permitir | Permitir
``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | No se requiere | No se requiere | No se requiere | Permitir la descarga SQL
``time.windows.com`` | PERMITIR | Permitir | Permitir | Permitir
``time.nist.gov`` | Permitir | Permitir | Permitir | PERMITIR



## <a name="azure-virtual-machine-requirements"></a>Requisitos para las máquinas virtuales de Azure

Puede implementar Site Recovery para replicar máquinas virtuales y servidores físicos que ejecuten cualquier sistema operativo compatible con Azure. Incluye la mayoría de las versiones de Windows y Linux. Las máquinas virtuales locales que desea replicar debe cumplir los requisitos de Azure.

**Característica** | **Requisitos** | **Detalles**
--- | --- | ---
**Host de Hyper-V** | Debe estar ejecutando Windows Server 2012 R2 o una versión posterior. | La comprobación de los requisitos previos producirá un error si el sistema operativo no es compatible
**Hipervisor de VMware** | Sistema operativo compatible | [Comprobar los requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
**Sistema operativo invitado** | Replicación de Hyper-V en Azure: Site Recovery es compatible con todos los sistemas operativos [admitidos por Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para la replicación de servidores físicos y de VMware, consulte los [requisitos previos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Arquitectura del sistema operativo invitado** | 64 bits | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Tamaño del disco del sistema operativo** | Hasta 1.023 GB | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Número de discos del sistema operativo** | 1 | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Número de discos de datos** | 16 o menos (el valor máximo es una función del tamaño de la máquina virtual que se está creando. 16 = XL) | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Tamaño de VHD del disco de datos** | Hasta 1.023 GB | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Adaptadores de red** | Se admiten varios adaptadores |
**Dirección IP estática** | Compatible | Si la máquina virtual principal usa una dirección IP estática, puede especificar la dirección IP estática para la máquina virtual que se creará en Azure.<br/><br/> No se admiten direcciones IP estáticas de una **máquina virtual Linux con Hyper-V** no se admite.
**Disco iSCSI** | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**VHD compartido** | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Disco FC** | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Formato de disco duro** | VHD  <br/><br/> VHDX | Aunque VHDX no se admite en Azure en este momento, Site Recovery convierte automáticamente VHDX en VHD cuando se conmuta por error en Azure. Cuando se realiza la conmutación por recuperación en local, las máquinas virtuales siguen usando el formato VHDX.
**BitLocker** | No compatible | Se debe deshabilitar BitLocker antes de proteger una máquina virtual.
**Nombre de la máquina virtual** | Entre 1 y 63 caracteres. Restringido a letras, números y guiones. El nombre debe empezar y terminar por una letra o un número. | Actualizar el valor de las propiedades de la máquina virtual en Site Recovery
**Tipo de máquina virtual** | Generación 1<br/><br/> Generación 2 - Windows | Las máquinas virtuales de generación 2 con un tipo de disco de sistema operativo básico, que incluye uno o dos volúmenes de datos con el formato VHDX y menos de 300 GB, son compatibles.<br/><br/>. No se admiten las máquinas virtuales Linux de generación 2. [Más información](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) |

## <a name="deployment-optimization"></a>Optimización de la implementación

Aplique las siguientes sugerencias para optimizar y escalar la implementación.

- **Tamaño del volumen del sistema operativo**: cuando se replica una máquina virtual en Azure, el volumen del sistema operativo tiene que ser inferior a 1 TB. Si tiene más volúmenes, puede moverlos manualmente a otro disco antes de comenzar la implementación.
- **Tamaño de disco de datos**: si se está replicando en Azure, podrá tener hasta 64 discos de datos en una máquina virtual, cada una con un máximo de 1 TB. Puede replicar y conmutar por error de manera eficaz una máquina virtual de 64 TB aproximadamente.
- **Límites del plan de recuperación**: Site Recovery puede escalar a miles de máquinas virtuales. Los planes de recuperación están diseñados como un modelo para las aplicaciones que deben conmutar por error entre sí por lo que se limita el número de máquinas en un plan de recuperación a 50.
- **Límites de servicio de Azure**: cada suscripción de Azure incluye un conjunto de límites predeterminados sobre núcleos, servicios en la nube, etc. Le recomendamos que ejecute una conmutación por error de prueba para validar la disponibilidad de los recursos de la suscripción. Puede modificar estos límites a través de soporte técnico de Azure.
- **Planeamiento de capacidad**: obtenga información sobre el [planeamiento de la capacidad](site-recovery-capacity-planner.md) de Site Recovery.
- **Ancho de banda de replicación**: si tiene poco ancho de banda de replicación, tenga en cuenta lo siguiente:
- **ExpressRoute**: Site Recovery funciona con los optimizadores de ExpressRoute de Azure y WAN, como Riverbed. [Obtenga más información](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sobre ExpressRoute.
- **Tráfico de replicación**: Site Recovery realiza una replicación inicial inteligente usando solo bloques de datos y no todo el VHD. Solo se replican los cambios durante la replicación continua.
- **Tráfico de red**: puede controlar el tráfico de red que se utiliza para la replicación mediante la configuración de [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) con una directiva basada en la dirección IP de destino y el puerto.  Además, si se está replicando en Azure Site Recovery mediante el agente de copia de seguridad de Azure, puede configurar la limitación para ese agente. [Más información](https://support.microsoft.com/kb/3056159).
- **RTO**: si desea medir el objetivo de tiempo de recuperación (RTO) que se puede esperar con Site Recovery, le sugerimos que ejecute una conmutación por error de prueba y vea los trabajos de Site Recovery para analizar la cantidad de tiempo que se tarda en completar las operaciones. Si está conmutando por error en Azure, para obtener un mejor RTO se recomienda que automatice todas las acciones manuales mediante la integración con Automatización de Azure y los planes de recuperación.
- **RPO**: Site Recovery admite un objetivo de punto de recuperación casi sincrónico (RPO) al replicar a Azure. Esto supone suficiente ancho de banda entre el centro de datos y Azure.



## <a name="next-steps"></a>Pasos siguientes
Después de revisar los requisitos de implementación generales, lea los requisitos previos detallados e implemente un escenario.

* [Replicación de máquinas virtuales de VMware en Azure](site-recovery-vmware-to-azure.md)
* [Replicación de servidores físicos a Azure](site-recovery-vmware-to-azure.md)
* [Replicación de servidores de Hyper-V de nubes VMM a Azure](site-recovery-vmm-to-azure.md)
* [Replicación de máquinas virtuales de Hyper-V (sin VMM) a Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replicación de máquinas virtuales de Hyper-V a un sitio secundario](site-recovery-vmm-to-vmm.md)
* [Replicación de máquinas virtuales de Hyper-V con un solo servidor VMM](site-recovery-single-vmm.md)



<!--HONumber=Dec16_HO1-->


