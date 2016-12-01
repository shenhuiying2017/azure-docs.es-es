---
title: Preparación de la implementación de Azure Site Recovery | Microsoft Docs
description: En este artículo se describe cómo prepararse para la implementación de la replicación con Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: tysonn

ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/05/2016
ms.author: raynew

---
# <a name="prepare-for-azure-site-recovery-deployment"></a>Preparación de la implementación de Azure Site Recovery
Lea este artículo para obtener información de alto nivel de los requisitos de implementación de cada escenario de replicación admitido por el servicio Azure Site Recovery. Después de leer los requisitos generales para cada escenario, puede consultar los detalles de implementación específicos de cada caso.

Después de leer este artículo, puede publicar algún comentario o formular alguna pregunta al final, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Información general
Las organizaciones necesitan una estrategia de recuperación ante desastres y continuidad empresarial (BCDR) que determine cómo seguirán en funcionamiento y disponibles las aplicaciones, las cargas de trabajo y los datos durante los tiempos de inactividad planeados y no planeados, y cómo recuperar las condiciones de funcionamiento normales lo antes posible. Su estrategia de BCDR se centra en soluciones que mantengan los datos empresariales seguros y recuperables, y garanticen que las cargas de trabajo estarán disponibles continuamente en caso de desastre. 

Site Recovery es un servicio de Azure que contribuye a su estrategia de BCDR mediante la coordinación de la replicación de servidores físicos locales y máquinas virtuales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error a la ubicación secundaria para mantener disponibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal. Más información en [¿Qué es Site Recovery?](site-recovery-overview.md)

## <a name="select-your-deployment-model"></a>Selección del modelo de implementación
Azure tiene dos [modelos de implementación](../resource-manager-deployment-model.md) diferentes para crear recursos y trabajar con ellos: el modelo de Azure Resource Manager y el modelo clásico de administración de servicios. Azure también tiene dos portales: el [Portal de Azure clásico](https://manage.windowsazure.com/) que admite el modelo de implementación clásico y [Azure Portal](https://ms.portal.azure.com/) que es compatible con ambos modelos de implementación.

Site Recovery está disponible tanto en el portal clásico como en el Portal de Azure. En el Portal de Azure clásico, Site Recovery se puede usar con el modelo de administración de servicios clásico. En el Portal de Azure, se pueden usar tanto el modelo de implementación clásica como el modelo de Resource Manager. [Más información](site-recovery-overview.md#site-recovery-in-the-azure-portal) acerca de la implementación con el Portal de Azure.

Cuando esté eligiendo un modelo de implementación, tenga en cuenta lo siguiente:

* Se recomienda que utilice el [Portal de Azure](https://portal.azure.com) y el modelo de Resource Manager siempre que sea posible.
* Site Recovery proporciona una experiencia de introducción más sencilla e intuitiva en el Portal de Azure.
* Con el Portal de Azure puede replicar máquinas tanto en el modelo de almacenamiento clásico como en el de Resource Manager en Azure. Además, en el Portal de Azure puede utilizar cuentas de almacenamiento LRS o GRS.
* El Portal de Azure combina los servicios de copia de seguridad y recuperación del sitio en un único almacén de Servicios de recuperación para que pueda configurar y administrar los servicios BCDR desde una sola ubicación.
* Los usuarios con suscripciones de Azure aprovisionadas con el programa Proveedor de soluciones en la nube (CSP) pueden administrar ahora las operaciones de Site Recovery en el Portal de Azure.
* La replicación de máquinas físicas o máquinas virtuales de VMware en Azure mediante el Portal de Azure ofrece una serie de nuevas características, como la compatibilidad para almacenamiento premium y la capacidad de excluir discos concretos de la replicación.

## <a name="select-your-deployment-scenario"></a>Selección de su escenario de implementación
| **Implementación** | **Detalles** | **Portal de Azure** | **Portal clásico** | **PowerShell** |
| --- | --- | --- | --- | --- |
| **Máquinas virtuales de VMware en Azure** |Replique máquinas virtuales de VMware en almacenamiento de Azure. |En el Portal de Azure, las máquinas virtuales pueden conmutar por error en el almacenamiento clásico o de Resource Manager.<br/><br/> La implementación en el [Portal de Azure](site-recovery-vmware-to-azure.md) ofrece una experiencia de implementación optimizada y una serie de características ventajosas. |En el Portal de Azure clásico puede implementar con el [modelo mejorado](site-recovery-vmware-to-azure-classic.md) y conmutar por error en Azure Storage clásico.<br/><br/>  También hay un modelo heredado que no debe usarse para nuevas implementaciones. |PowerShell no se admite actualmente. |
| **Máquinas virtuales de Hyper-V en Azure** |Máquinas virtuales de Hyper-V en almacenamiento de Azure. Las máquinas virtuales pueden estar en hosts de Hyper-V administrados en nubes de VMM de System Center, o sin VMM. |En el Portal de Azure, las máquinas virtuales pueden conmutar por error en el almacenamiento clásico o de Resource Manager.<br/><br/>  El Portal de Azure proporciona una experiencia de implementación optimizada. [Más información](site-recovery-vmm-to-azure.md) acerca de la replicación de máquinas virtuales de Hyper-V en nubes de VMM. [Más información](site-recovery-hyper-v-site-to-azure.md) acerca de la replicación de máquinas virtuales de Hyper-V (sin VMM). |En el Portal de Azure clásico puede conmutar las máquinas virtuales por error en el almacenamiento de Azure clásico. |Se admite la implementación de PowerShell. |
| **Servidores físicos en Azure** |Replique servidores físicos de Windows/Linux en almacenamiento de Azure. |En el Portal de Azure, los servidores pueden conmutar por error en el almacenamiento clásico o de Resource Manager.<br/><br/> La implementación en el [Portal de Azure](site-recovery-vmware-to-azure.md) proporciona una experiencia de implementación simplificada y numerosas características ventajosas. |En el Portal de Azure clásico puede implementar con el [modelo mejorado](site-recovery-vmware-to-azure-classic.md) y conmutar por error en Azure Storage clásico.<br/><br/>  También hay un modelo heredado que no debe usarse para nuevas implementaciones. |Actualmente no se admite la implementación de PowerShell. |
| **Máquinas virtuales de VMware o servidores físicos en un sitio secundario* |Replique máquinas virtuales de VMware o servidores físicos de Windows/Linux en un sitio secundario. [Obtenga más información y descargue](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) la guía de usuario de InMage Scout. |No está disponible en el Portal de Azure. |En el portal clásico descargará InMage Scout desde un almacén de Site Recovery. |Actualmente no se admite la implementación de PowerShell. |
| **Máquinas virtuales de Hyper-V en un sitio secundario** |Replique las máquinas virtuales de Hyper-V de nubes VMM en una nube secundaria. |En el [Portal de Azure](site-recovery-vmm-to-vmm.md) puede replicar máquinas virtuales de Hyper-V en nubes VMM en un sitio secundario únicamente mediante Réplica de Hyper-V. SAN no se admite actualmente. |En el Portal de Azure clásico puede replicar máquinas virtuales de Hyper-V en nubes VMM en un sitio secundario mediante [Réplica de Hyper-V](site-recovery-vmm-to-vmm-classic.md) o [Replicación SAN](site-recovery-vmm-san.md). |Se admite la implementación de PowerShell. |

## <a name="check-what-you-need-for-deployment"></a>Requisitos para realizar la implementación
### <a name="replicate-to-azure"></a>Replicación en Azure
| **Requisito** | **Detalles** |
| --- | --- |
| **Cuenta de Azure** |Necesitará una cuenta de [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) sobre los precios de Site Recovery. |
| **Almacenamiento de Azure** |Los datos replicados se almacenan en el almacenamiento de Azure y las máquinas virtuales de Azure se crean cuando se produce la conmutación por error. Para replicar a Azure, necesitará un [cuenta de Azure Storage](../storage/storage-introduction.md).<br/><br/>Si está implementando Site Recovery en el portal clásico, necesitará una o varias [cuentas de almacenamiento GRS estándar](../storage/storage-redundancy.md#geo-redundant-storage).<br/><br/> Si va a implementar en Azure Portal, puede usar almacenamiento GRS o LRS.<br/><br/>  Si está replicando servidores físicos o máquinas virtuales de VMware en el Portal de Azure, se admite el almacenamiento premium. Tenga en cuenta que si está usando una cuenta de almacenamiento premium, necesitará también una cuenta de almacenamiento estándar para almacenar los registros de replicación que capturan los cambios continuos en los datos locales. [Premium Storage](../storage/storage-premium-storage.md) se usa normalmente para las máquinas virtuales que necesitan un alto rendimiento constante de E/S y latencia baja para hospedar cargas de trabajo intensivas de E/S.<br/><br/>  Si desea utilizar una cuenta premium para almacenar los datos replicados, también necesitará una cuenta de almacenamiento estándar para almacenar los registros de replicación que capturan los cambios continuos de los datos locales. |
| **Red de Azure** |Para replicar en Azure, necesitará una red de Azure a la que se conectarán las máquinas virtuales de Azure cuando se creen después de la conmutación por error.<br/><br/>  Si va a implementar en el portal clásico usará una red clásica. Si va a implementar en el Portal de Azure, puede usar una red clásica o de Resource Manager.<br/><br/>  La red debe estar en la misma región que el almacén. |
| **Asignación de red (VMM en Azure)** |Si está replicando desde VMM en Azure, [la asignación de red](site-recovery-network-mapping.md) garantiza que las máquinas virtuales de Azure se conecten a las redes correctas después de la conmutación por error.<br/><br/>  Para configurar la asignación de red debe configurar redes de VM en el portal VMM. |
| **Local** |**Máquinas virtuales de VMware**: necesitará un equipo local con los componentes de Site Recovery, los hosts de VMware vSphere o el servidor de vCenter y las máquinas virtuales que desee replicar. [Más información](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).<br/><br/> **Servidores físicos**: si está replicando servidores físicos, necesitará máquinas locales que ejecuten los componentes de Site Recovery y los servidores físicos que se van a replicar. [Más información](site-recovery-vmware-to-azure.md#configuration-server-prerequisites). Si desea [conmutar por recuperación](site-recovery-failback-azure-to-vmware.md) tras una conmutación por error en Azure, necesitará una infraestructura de VMware.<br/><br/> **Máquinas virtuales de Hyper-V**: si desea replicar máquinas virtuales de Hyper-V en nubes de VMM, necesitará un servidor de VMM y hosts de Hyper-V en los que se encuentran las máquinas virtuales que desea proteger. [Más información](site-recovery-vmm-to-azure.md#on-premises-prerequisites).<br/><br/>  Si desea replicar máquinas virtuales de Hyper-V sin VMM necesitará los hosts de Hyper-V donde se encuentran las máquinas virtuales. [Más información](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites). |
| **Máquinas protegidas** |Las máquinas protegidas que se replicarán en Azure deben cumplir los [requisitos previos de Azure](#azure-virtual-machine-requirements) que se describen a continuación. |

### <a name="replicate-to-a-secondary-site"></a>Replicación en un sitio secundario
| **Requisito** | **Detalles** |
| --- | --- |
| **Cuenta de Azure** |Necesitará una cuenta de [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) sobre los precios de Site Recovery. |
| **Local** |**Máquinas virtuales de VMware**: en el sitio principal necesitará un servidor de procesos para el almacenamiento en caché, la compresión y el cifrado de los datos de replicación antes de enviarlo al sitio secundario. En el sitio secundario podrá instalar un servidor de configuración para administrar y supervisar la implementación y un servidor de destino principal. También se recomienda un servidor de vContinuum para facilitar la administración. Además, necesita uno o más hosts de VMware vSphere y, opcionalmente, un servidor vCenter. [Más información](site-recovery-vmware-to-vmware.md)<br/><br/> **Máquinas virtuales de Hyper-V en nubes VMM**: deberá configurar los servidores VMM y los hosts de Hyper-V que contienen las máquinas virtuales que desea replicar. [Más información](site-recovery-vmm-to-vmm.md#on-premises-prerequisites). |
| **Asignación de red (VMM en VMM)** |Si está replicando desde VMM en VMM, [la asignación de red](site-recovery-network-mapping.md) garantiza que las máquinas virtuales de réplica estén conectadas a las redes adecuadas después de la conmutación por error y que se coloquen de forma óptima en los hosts de Hyper-V. Para configurar la asignación de red debe configurar redes de VM en los servidores VMM. |
| **Asignación de almacenamiento** |Si está replicando desde VMM en VMM puede, opcionalmente, configurar la [asignación de almacenamiento](site-recovery-storage-mapping.md) para especificar el destino de almacenamiento para las máquinas virtuales replicadas. La asignación de almacenamiento se puede configurar tanto para la replicación de Réplica de Hyper-V como para la de SAN.<br/><br/>  Actualmente no se admite la asignación de almacenamiento en el portal de Azure. |

## <a name="verify-url-access"></a>Comprobación del acceso a direcciones URL
Asegúrese de poder tener acceso a estas direcciones URL desde los servidores.

| **URL** | **VMM a VMM** | **VMM a Azure** | **Hyper-V en Azure** | **VMware a Azure** |
| --- | --- | --- | --- | --- |
| *.accesscontrol.windows.net |Permitir |Permitir |Permitir |Permitir |
| *.backup.windowsazure.com |No se requiere |Permitir |Permitir |Permitir |
| *.hypervrecoverymanager.windowsazure.com |Permitir |Permitir |Permitir |Permitir |
| *.store.core.windows.net |Permitir |Permitir |Permitir |Permitir |
| *.blob.core.windows.net |No se requiere |Permitir |Permitir |PERMITIR |
| https://www.msftncsi.com/ncsi.txt |PERMITIR |Permitir |Permitir |PERMITIR |
| https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi |No se requiere |No se requiere |No se requiere |Permitir |

## <a name="azure-virtual-machine-requirements"></a>Requisitos para las máquinas virtuales de Azure
Puede implementar Site Recovery para replicar máquinas virtuales y servidores físicos que ejecuten cualquier sistema operativo compatible con Azure. Incluye la mayoría de las versiones de Windows y Linux. Debe asegurarse de que las máquinas virtuales locales que desea proteger cumplan los requisitos de Azure.

| **Característica** | **Requisitos** | **Detalles** |
| --- | --- | --- |
| Host de Hyper-V |Debe estar ejecutando Windows Server 2012 R2 |La comprobación de los requisitos previos producirá un error si el sistema operativo no es compatible |
| Hipervisor de VMware |Sistema operativo compatible |[Comprobar los requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) |
| Sistema operativo invitado |Replicación de Hyper-V en Azure: Site Recovery es compatible con todos los sistemas operativos [admitidos por Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para la replicación de servidores físicos y de VMware, consulte los [requisitos previos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) |Se producirá un error en la comprobación de los requisitos previos si no es compatible. |
| Arquitectura del sistema operativo invitado |64 bits |Se producirá un error en la comprobación de los requisitos previos si no es compatible. |
| Tamaño del disco del sistema operativo |Hasta 1.023 GB |Se producirá un error en la comprobación de los requisitos previos si no es compatible. |
| Número de discos del sistema operativo |1 |Se producirá un error en la comprobación de los requisitos previos si no es compatible. |
| Número de discos de datos |16 o menos (el valor máximo es una función del tamaño de la máquina virtual que se está creando. 16 = XL) |Se producirá un error en la comprobación de los requisitos previos si no es compatible. |
| Tamaño de VHD del disco de datos |Hasta 1023 GB |Se producirá un error en la comprobación de los requisitos previos si no es compatible. |
| Adaptadores de red |Se admiten varios adaptadores | |
| Dirección IP estática |Compatible |Si la máquina virtual principal usa una dirección IP estática, puede especificar la dirección IP estática para la máquina virtual que se creará en Azure. Tenga en cuenta que no se admite la dirección IP estática de una máquina virtual de Linux que se ejecuta en Hyper-v. |
| Disco iSCSI |No compatible |Se producirá un error en la comprobación de los requisitos previos si no es compatible. |
| VHD compartido |No compatible |Se producirá un error en la comprobación de los requisitos previos si no es compatible. |
| Disco FC |No compatible |Se producirá un error en la comprobación de los requisitos previos si no es compatible. |
| Formato de disco duro |VHD  <br/><br/>  VHDX |Aunque VHDX no se admite en Azure en este momento, Site Recovery convierte automáticamente VHDX en VHD cuando se conmuta por error en Azure. Cuando se realiza la conmutación por recuperación en local, las máquinas virtuales siguen usando el formato VHDX. |
| Bitlocker |No compatible |Se debe deshabilitar BitLocker antes de proteger una máquina virtual. |
| Nombre de la máquina virtual |Entre 1 y 63 caracteres. Restringido a letras, números y guiones. El nombre debe empezar y terminar por una letra o un número. |Actualizar el valor de las propiedades de la máquina virtual en Site Recovery |
| Tipo de máquina virtual |<p>Generación 1</p> <p>Generación 2 - Windows</p> |Se admiten las máquinas virtuales de generación 2 con el tipo de disco de SO de disco básico que incluye uno o dos volúmenes de datos con un formato de disco como VHDX, inferior a 300 GB. No se admiten máquinas virtuales Linux de generación 2. [Más información](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) |

## <a name="optimizing-your-deployment"></a>Optimización de la implementación
Aplique las siguientes sugerencias como ayuda para optimizar y escalar la implementación.

* **Tamaño del volumen del sistema operativo**: cuando se replica una máquina virtual en Azure, el volumen del sistema operativo tiene que ser inferior a 1 TB. Si tiene más volúmenes, puede moverlos manualmente a otro disco antes de comenzar la implementación.
* **Tamaño de disco de datos**: si se está replicando en Azure, podrá tener hasta 32 discos de datos en una máquina virtual, cada una con un máximo de 1 TB. Puede replicar y conmutar por error de manera eficaz una máquina virtual de ~32 TB.
* **Límites del plan de recuperación**: Site Recovery puede escalar a miles de máquinas virtuales. Los planes de recuperación están diseñados como un modelo para las aplicaciones que deben conmutar por error entre sí por lo que se limita el número de máquinas en un plan de recuperación a 50.
* **Límites de servicio de Azure**: cada suscripción de Azure incluye un conjunto de límites predeterminados sobre núcleos, servicios en la nube, etc. Le recomendamos que ejecute una conmutación por error de prueba para validar la disponibilidad de los recursos de la suscripción. Puede modificar estos límites a través de soporte técnico de Azure.
* **Planeamiento de capacidad**: obtenga información sobre el [planeamiento de la capacidad](site-recovery-capacity-planner.md) de Site Recovery.
* **Ancho de banda de replicación**: si tiene poco ancho de banda de replicación, tenga en cuenta lo siguiente:
  * **ExpressRoute**: Site Recovery funciona con los optimizadores de ExpressRoute de Azure y WAN, como Riverbed. [Obtenga más información](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sobre ExpressRoute.
  * **Tráfico de replicación**: Site Recovery realiza una replicación inicial inteligente usando solo bloques de datos y no todo el VHD. Solo se replican los cambios durante la replicación continua.
  * **Tráfico de red**: puede controlar el tráfico de red que se utiliza para la replicación mediante la configuración de [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) con una directiva basada en la dirección IP de destino y el puerto.  Además, si se está replicando en Azure Site Recovery mediante el agente de copia de seguridad de Azure, puede configurar la limitación para ese agente. [Más información](https://support.microsoft.com/kb/3056159).
* **RTO**: si desea medir el objetivo de tiempo de recuperación (RTO) que se puede esperar con Site Recovery, le sugerimos que ejecute una conmutación por error de prueba y vea los trabajos de Site Recovery para analizar la cantidad de tiempo que se tarda en completar las operaciones. Si está conmutando por error en Azure, para obtener un mejor RTO se recomienda que automatice todas las acciones manuales mediante la integración con Automatización de Azure y los planes de recuperación.
* **RPO**: Site Recovery admite un objetivo de punto de recuperación casi sincrónico (RPO) al replicar a Azure. Esto supone suficiente ancho de banda entre el centro de datos y Azure.

## <a name="service-urls"></a>Direcciones URL de servicio
Asegúrese de poder acceder a estas direcciones URL desde el servidor:

| **URLs** | **VMM a VMM** | **VMM a Azure** | **Sitio Hyper-V a Azure** | **VMware a Azure** |
| --- | --- | --- | --- | --- |
|  \*.accesscontrol.windows.net |Acceso requerido |Acceso requerido |Acceso requerido |Acceso requerido |
|  \*.backup.windowsazure.com | |Acceso requerido |Acceso requerido |Acceso requerido |
|  \*.hypervrecoverymanager.windowsazure.com |Acceso requerido |Acceso requerido |Acceso requerido |Acceso requerido |
|  \*.store.core.windows.net |Acceso requerido |Acceso requerido |Acceso requerido |Acceso requerido |
|  \*.blob.core.windows.net | |Acceso requerido |Acceso requerido |Acceso requerido |
|  https://www.msftncsi.com/ncsi.txt |Acceso requerido |Acceso requerido |Acceso requerido |Acceso requerido |
|  https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi | | | |Acceso requerido |

## <a name="next-steps"></a>Pasos siguientes
Una vez que conoce y puede comparar los requisitos de implementación generales, puede leer los requisitos previos detallados y comenzar a implementar cada escenario.

* [Replicación de máquinas virtuales de VMware en Azure](site-recovery-vmware-to-azure-classic.md)
* [Replicación de servidores físicos a Azure](site-recovery-vmware-to-azure-classic.md)
* [Replicación de servidores de Hyper-V de nubes VMM a Azure](site-recovery-vmm-to-azure.md)
* [Replicación de máquinas virtuales de Hyper-V (sin VMM) a Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replicación de máquinas virtuales de Hyper-V a un sitio secundario](site-recovery-vmm-to-vmm.md)
* [Replicación de máquinas virtuales de Hyper-V a un sitio secundario con SAN](site-recovery-vmm-san.md)
* [Replicación de máquinas virtuales de Hyper-V con un solo servidor VMM](site-recovery-single-vmm.md)

<!--HONumber=Oct16_HO2-->


