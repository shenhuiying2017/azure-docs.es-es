---
title: Matriz de compatibilidad de Azure Site Recovery | Microsoft Docs
description: Resumen de los sistemas operativos y los componentes compatibles con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 1bbcc13c-ea21-4349-9ddf-0d7dfdcdcbfb
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/04/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 1a2ea0753d12915075c055f9983773618bf6777d
ms.openlocfilehash: 85c6a2296a4487ae549321d3ff07129bcbab5adf


---
# <a name="azure-site-recovery-support-matrix"></a>Matriz de compatibilidad de Azure Site Recovery

En este artículo se resumen los sistemas operativos y los componentes compatibles con Azure Site Recovery. Cada escenario de implementación tiene una lista de requisitos previos y componentes compatibles en el artículo de implementación correspondiente, pero en este tema se resumen todos ellos.

## <a name="support-for-azure-replication-scenarios"></a>Compatibilidad con escenarios de replicación de Azure

**Implementación** | **Servidores físicos o de VMware** | **Hyper-V (sin VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
**Portal de Azure** | Máquinas virtuales de VMware locales en Azure Storage con Resource Manager o redes y almacenamiento clásicos.<br/><br/> Realice la conmutación por error en las máquinas virtuales clásicas o basadas en Resource Manager. | Máquinas virtuales de Hyper-V locales (no en nubes VMM) en Azure Storage con Resource Manager o redes y almacenamiento clásicos.<br/><br/> Realice la conmutación por error en las máquinas virtuales clásicas o basadas en Resource Manager. | Máquinas virtuales de Hyper-V locales en nubes VMM en Azure Storage con Resource Manager o redes y almacenamiento clásicos.<br/><br/> Realice la conmutación por error en las máquinas virtuales clásicas o basadas en Resource Manager.
**Portal clásico** | Solo en el modo mantenimiento. No se pueden crear almacenes nuevos. | Solo en el modo mantenimiento. | Solo en el modo mantenimiento.
**PowerShell** | No se admite actualmente. | Compatible | Compatible


## <a name="support-for-secondary-site-replication-scenarios"></a>Compatibilidad con escenarios de replicación de sitios secundarios

**Implementación** | **Servidores físicos o de VMware** | **Hyper-V (sin VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
**Portal de Azure** | Máquinas virtuales de VMware locales en sitios secundario de VMware.<br/><br/> Descarga de la Guía de ayuda (http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf): guía del usuario de InMage Scout. No está disponible en el Portal de Azure. | No compatible | Máquinas virtuales de Hyper-V en nubes VMM en una nube VMM secundaria local<br/><br/> Solo replicación de Hyper-V (SAN no compatible)
**Portal clásico** | Solo en el modo mantenimiento. No se pueden crear almacenes nuevos. | Solo en el modo mantenimiento. | Solo en el modo mantenimiento.
**PowerShell** | No compatible. | N/D | Compatible



## <a name="support-for-virtualization-server-operating-systems"></a>Sistemas operativos compatibles para servidores de virtualización

### <a name="host-servers-replicate-to-azure"></a>Servidores host (se replican en Azure)

**Servidor de máquina virtual de VMware o físico** | **Hyper-V (sin VMM)** | **Hyper-V (con VMM)**
--- | --- | ---
vCenter 5.5 o 6.0 (compatible solo con características de 5.5)  <br/><br/> vSphere 6.0, 5.5 o 5.1 con las últimas actualizaciones | Windows Server 2016 y Windows Server 2012 R2 con las últimas actualizaciones<br/><br/> No se admite actualmente un sitio de Hyper-V que combine hosts que ejecutan Windows Server 2016 y 2012 R2. | Windows Server 2016 y Windows Server 2012 R2 con las últimas actualizaciones<br/><br/> Los hosts de Windows Server 2016 deben administrarse mediante VMM que ejecuta System Center 2016.<br/><br/> No se admite actualmente una nube VMM 2016 que combine hosts de Windows Server 2016 y 2012 R2.

### <a name="host-servers-replicate-to-secondary-site"></a>Servidores host (replicación al sitio secundario)

**Servidor de máquina virtual de VMware o físico** | **Hyper-V (con VMM)**
--- | --- | ---
vCenter 5.5 o 6.0 (compatible solo con características de 5.5)  <br/><br/> vSphere 6.0, 5.5 o 5.1 con las últimas actualizaciones | Windows Server 2016, Windows Server 2012 R2 o Windows Server 2012 con las últimas actualizaciones.<br/><br/> Los hosts de Windows Server 2016 deben administrarse mediante VMM que ejecuta System Center 2016.<br/><br/> No se admite actualmente una nube VMM 2016 que combine hosts de Windows Server 2016.


## <a name="support-for-replicated-machines"></a>Compatibilidad con máquinas replicadas

### <a name="machines-replicate-to-azure"></a>Máquinas (se replican en Azure)

Las máquinas virtuales de deben cumplir los [requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

**Requisito** | **Servidores físicos o de VMware** | **Hyper-V (sin VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
Lo que se replica | Cualquier carga de trabajo en máquinas virtuales Windows o Linux | Cualquier carga de trabajo | Cualquier carga de trabajo
Sistema operativo del host | Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 R2 con al menos SP1<br/><br/> Red Hat Enterprise Linux 6.7, 7.1 y 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 7.0, 7.1 y 7.2 <br/><br/> Oracle Enterprise Linux 6.4 y 6.5, en ejecución en el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/> Almacenamiento requerido: sistema de archivos (EXT3, ETX4, ReiserFS, XFS); asignador de software y dispositivos de múltiples rutas (múltiples rutas); administrador de volúmenes: (LVM2). No se admiten servidores físicos con almacenamiento de controlador HP CCISS. El sistema de archivos ReiserFS solo se admite en SUSE Linux Enterprise Server 11 SP3. | Cualquier sistema operativo invitado [compatible con Azure](https://technet.microsoft.com/library/cc794868.aspx) | Cualquier sistema operativo invitado [compatible con Azure](https://technet.microsoft.com/library/cc794868.aspx)


### <a name="machines-replicate-to-secondary-site"></a>Máquinas (se replican al sitio secundario)

**Requisito** | **Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Lo que se replica | Cualquier carga de trabajo en máquinas virtuales Windows o Linux | Cualquier carga de trabajo | Cualquier carga de trabajo
Sistema operativo del host | Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 R2 con al menos SP1<br/><br/> Red Hat Enterprise Linux 6.7, 7.1 y 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 7.0, 7.1 y 7.2 <br/><br/> Oracle Enterprise Linux 6.4 y 6.5, en ejecución en el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/> Almacenamiento requerido: sistema de archivos (EXT3, ETX4, ReiserFS, XFS); asignador de software y dispositivos de múltiples rutas (múltiples rutas); administrador de volúmenes: (LVM2).<br/><br/> No se admiten servidores físicos con almacenamiento de controlador HP CCISS. El sistema de archivos ReiserFS solo se admite en SUSE Linux Enterprise Server 11 SP3. | Cualquier sistema operativo invitado compatible con Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)


## <a name="support-for-provider-and-agent"></a>Compatibilidad con el proveedor y el agente

**Name** | **Descripción** | **La versión más reciente** | **Detalles**
--- | --- | --- | --- | ---
**Proveedor de Azure Site Recovery** | Coordina las comunicaciones entre los servidores locales y el sitio secundario o de Azure  <br/><br/> Se instala en servidores VMM locales o en servidores de Hyper-V si no hay ningún servidor VMM | 5.1.1700 (disponible en el portal) | [Características y correcciones más recientes](https://support.microsoft.com/kb/3155002)
**Configuración unificada de Azure Site Recovery (VMware a Azure)** | Coordina las comunicaciones entre servidores de VMware locales y Azure  <br/><br/> Se instala en servidores de VMware locales | 9.3.4246.1 (disponible en el portal) | [Características y correcciones más recientes](https://support.microsoft.com/kb/3155002)
**Servicio de movilidad** | Coordina la replicación entre servidores de VMware locales o servidores físicos y el sitio secundario o Azure<br/><br/> Se instala en cada máquina virtual de VMware o servidor físico que se va a replicar  | NA (disponible en el portal) | .
**Agente de Microsoft Azure Recovery Services (MARS)** | Coordina la replicación entre máquinas virtuales de Hyper-V y Azure<br/><br/> Se instala en servidores de Hyper-V locales (con o sin un servidor VMM) | |

## <a name="support-for-networking"></a>Compatibilidad con redes

### <a name="networking-replicate-to-azure"></a>Redes (se replican en Azure)

**Redes de host** | **Servidores físicos o de VMware** | **Hyper-V (sin VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
Formación de equipos NIC | Sí<br/><br/>No compatible en máquinas físicas| Sí | Sí
VLAN | Sí | Sí | Sí
IPv4 | Sí | Sí | Sí
IPv6 | No | No | No

**Redes de máquina virtual invitada** | **Servidores físicos o de VMware** | **Hyper-V (sin VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
Formación de equipos NIC | No | No | No
IPv4 | Sí | Sí | Sí
IPv6 | No | No | No
Dirección IP estática (Windows) | Sí | Sí | Sí
Dirección IP estática (Linux) | No | No | No
Varias NIC | Sí | Sí | Sí

**Redes de Azure** | **Servidores físicos o de VMware** | **Hyper-V (sin VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
ExpressRoute | Sí | Sí | Sí
ILB | Sí | Sí | Sí
ELB | Sí |  |
Traffic Manager | Sí | Sí | Sí
Varias NIC | Sí | Sí | Sí
IP reservada | Sí | Sí | Sí
IPv4 | Sí | Sí | Sí
Conservar dirección IP de origen | Sí | Sí | Sí

### <a name="networking-replicate-to-secondary-site"></a>Redes (se replican al sitio secundario)

**Redes de host** | **Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Formación de equipos NIC | Sí | Sí
VLAN | Sí | Sí
IPv4 | Sí | Sí
IPv6 | No | No

**Redes de máquina virtual invitada** | **Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Formación de equipos NIC | No | No
IPv4 | Sí | Sí
IPv6 | No | No
Dirección IP estática (Windows) | Sí | Sí
Dirección IP estática (Linux) | Sí | Sí
Varias NIC | Sí | Sí


## <a name="support-for-storage"></a>Compatibilidad con el almacenamiento

### <a name="storage-replicate-to-azure"></a>Almacenamiento (se replica en Azure)

**Almacenamiento (host)** | **Servidores físicos o de VMware** | **Hyper-V (sin VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
NFS | Sí para VMware<br/><br/> No para servidores físicos | N/D | N/D
SMB 3.0 | N/D | Sí | Sí
SAN (ISCSI) | Sí | Sí | Sí
Varias rutas (MPIO) | Sí para VMware<br/><br/> NA en servidores físicos | Sí | Sí

**Almacenamiento (servidor de máquina virtual invitada o físico)** | **Servidores físicos o de VMware** | **Hyper-V (sin VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
VMDK | Sí | N/D | N/D
VHD/VHDX | N/D | Sí | Sí
VM de 2 generación | N/D | Sí | Sí
Disco en clúster compartido | Sí para VMware<br/><br/> NA en servidores físicos | No | No
Disco cifrado | No | No | No
EFI/UEFI| No | Sí | Sí
NFS | No | No | No
SMB 3.0 | No | No | No
RDM | Sí<br/><br/> NA en servidores físicos | N/D | N/D
Disco > 1 TB | No | No | No
Volumen con disco en bandas > 1 TB<br/><br/> LVM | Sí | Sí | Sí
Espacios de almacenamiento | No | Sí | Sí
Agregar/quitar disco en caliente | No | No | No
Excluir el disco | Sí | No | No
Varias rutas (MPIO) | N/D | Sí | Sí

**Almacenamiento de Azure** | **Servidores físicos o de VMware** | **Hyper-V (sin VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
LRS | Sí | Sí | Sí
GRS | Sí | Sí | Sí
Almacenamiento de acceso esporádico | No | No | No
Almacenamiento de acceso frecuente| No | No | No
Cifrado en reposo | Sí | Sí | Sí
Premium Storage | Sí | No | No
Servicio Import/Export | No | No | No


### <a name="storage-replicate-to-secondary-site"></a>Almacenamiento (se replica al sitio secundario)

**Almacenamiento (host)** | **Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | --- | ---
NFS | Sí | N/D
SMB 3.0 | N/D | Sí
SAN (ISCSI) | Sí | Sí
Varias rutas (MPIO) | Sí | Sí

**Almacenamiento (servidor de máquina virtual invitada o físico)** | **Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | --- | ---
VMDK | Sí | N/D
VHD/VHDX | N/D | Sí (hasta 16 discos)
VM de 2 generación | N/D | Sí
Disco en clúster compartido | Sí  | No
Disco cifrado | No | No
UEFI| No | N/D
NFS | No | No
SMB 3.0 | No | No
RDM | Sí | N/D
Disco > 1 TB | No | Sí
Volumen con disco en bandas > 1 TB<br/><br/> LVM | Sí | Sí
Espacios de almacenamiento | No | Sí
Agregar/quitar disco en caliente | No | No
Excluir el disco | No | No
Varias rutas (MPIO) | N/D | Sí

## <a name="support-for-recovery-services-vault-actions"></a>Compatibilidad con acciones de almacén de Recovery Services

### <a name="vaults-replicate-to-azure"></a>Almacenes (se replican en Azure)

**Acción** | **Servidores físicos o de VMware** | **Hyper-V (sin VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
Mover el almacén entre grupos de recursos<br/><br/> Entre las suscripciones | No | No | No
Mover el almacenamiento, la red y las máquinas virtuales de Azure entre grupos de recursos<br/><br/> Entre las suscripciones | No | No | No

### <a name="vaults-replicate-to-secondary-site"></a>Almacenes (se replican al sitio secundario)

**Acción** | **Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Mover el almacén entre grupos de recursos<br/><br/> Entre las suscripciones | No | No
Mover el almacenamiento, la red y las máquinas virtuales de Azure entre grupos de recursos<br/><br/> Entre las suscripciones | No | No


## <a name="support-for-azure-compute-replicate-to-azure"></a>Compatibilidad con el proceso de Azure (se replica en Azure)

**Característica de proceso** | **Servidores físicos o de VMware** | **Hyper-V (sin VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
Clústeres invitados de discos compartidos | No | No | No
Conjuntos de disponibilidad | No | No | No
CONCENTRADOR | Sí | Sí | Sí

## <a name="support-for-azure-vms"></a>Compatibilidad con las máquinas virtuales de Azure

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








## <a name="next-steps"></a>Pasos siguientes
[Preparación de la implementación](site-recovery-best-practices.md)



<!--HONumber=Jan17_HO2-->


