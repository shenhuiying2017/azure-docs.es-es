---
title: "Matriz de compatibilidad de Azure Site Recovery para la replicación en Azure | Microsoft Docs"
description: Resumen de los sistemas operativos y los componentes compatibles con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/25/2017
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 511e61552ec0a2c862ebd99fd881f87b56b69398
ms.openlocfilehash: 84eabb9c898d32efeb6e1c4b1f17b683940b4c04
ms.lasthandoff: 02/22/2017


---
# <a name="azure-site-recovery-support-matrix-for-replicating-to-azure"></a>Matriz de compatibilidad de Azure Site Recovery para la replicación en Azure

> [!div class="op_single_selector"]
> * [Replicación en Azure](site-recovery-support-matrix-to-azure.md)
> * [Replicación en un sitio secundario propiedad del cliente](site-recovery-support-matrix-to-sec-site.md)


En este artículo se resumen los componentes y las configuraciones compatibles con Azure Site Recovery cuando se replica y recupera en Azure. Para más información sobre los requisitos de Azure Site Recovery, vea los [requisitos previos](site-recovery-prereq.md).


## <a name="support-for-deployment-options"></a>Compatibilidad con opciones de implementación

**Implementación** | **Servidores físicos o de VMware** | **Hyper-V (sin Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
**Portal de Azure** | VM de VMware locales en Azure Storage, con redes y almacenamiento clásicos o Azure Resource Manager.<br/><br/> Realice la conmutación por error en las VM clásicas o basadas en Resource Manager. | VM de Hyper-V locales (no en nubes de Virtual Machine Manager) en Azure Storage, con redes y almacenamiento clásicos o Resource Manager.<br/><br/> Realice la conmutación por error en las VM clásicas o basadas en Resource Manager. | VM de Hyper-V locales (VM en nubes de Virtual Machine Manager) en Azure Storage, con redes y almacenamiento clásicos o Resource Manager.<br/><br/> Realice la conmutación por error en las VM clásicas o basadas en Resource Manager.
**Portal clásico** | Solo en el modo mantenimiento. No se pueden crear almacenes nuevos. | Solo en el modo mantenimiento. | Solo en el modo mantenimiento.
**PowerShell** | No se admite actualmente. | Compatible | Compatible


## <a name="support-for-datacenter-management-servers"></a>Compatibilidad con servidores de administración de centro de datos

### <a name="virtualization-management-entities"></a>Entidades de administración de virtualización

**Implementación** | **Soporte técnico**
--- | ---
**Servidor de máquina virtual de VMware o físico** | vSphere 6.0, 5.5 o 5.1 con la última actualización
**Hyper-V (con Virtual Machine Manager)** | System Center Virtual Machine Manager 2016 y System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > No se admite actualmente una nube de System Center Virtual Machine Manager 2016 que combine hosts de Windows Server 2016 y 2012 R2.

### <a name="host-servers"></a>Servidores host

**Implementación** | **Soporte técnico**
--- | ---
**Servidor de máquina virtual de VMware o físico** | vCenter 5.5 o 6.0 (compatible solo con características de 5.5) 
**Hyper-V (sin Virtual Machine Manager)** | Windows Server 2016 y Windows Server 2012 R2 con las últimas actualizaciones
**Hyper-V (con Virtual Machine Manager)** | Windows Server 2016 y Windows Server 2012 R2 con las actualizaciones más recientes.<br/><br/> Los hosts de Windows Server 2016 se deben administrar mediante System Center Virtual Machine Manager 2016.

  >[!Note]
  >No se admite actualmente un sitio de Hyper-V que combine hosts que ejecutan Windows Server 2016 y 2012 R2.

## <a name="support-for-replicated-machine-os-versions"></a>Compatibilidad con las versiones de SO de las máquinas replicadas

Las máquinas virtuales que están protegidas deben cumplir los [requisitos de Azure](#failed-over-azure-vm-requirements) cuando se replican en Azure.
La tabla siguiente resume la compatibilidad con los sistemas operativos de las máquinas replicadas en distintos escenarios de implementación cuando se usa Azure Site Recovery. Esta compatibilidad es aplicable a cualquier carga de trabajo que se ejecute en el SO mencionado.

 **Servidores físicos o de VMware** | **Hyper-V (sin Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | ---
Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 R2 con al menos SP1<br/><br/> Red Hat Enterprise Linux 6.7, 6.8, 7.1, 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 6.8, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4 y 6.5, en ejecución en el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | Cualquier sistema operativo invitado [compatible con Azure](https://technet.microsoft.com/library/cc794868.aspx) | Cualquier sistema operativo invitado [compatible con Azure](https://technet.microsoft.com/library/cc794868.aspx)


>[!Note]
>Compatibilidad con almacenamiento para sistema de archivos de versiones Linux (EXT3, ETX4, ReiserFS, XFS), Asignador de dispositivo-software de múltiples rutas, Administrador de volúmenes (LVM2). *No* se admiten servidores físicos con almacenamiento de controlador HP CCISS.
>El sistema de archivos ReiserFS solo se admite en SUSE Linux Enterprise Server 11 SP3.

## <a name="support-for-network-configuration"></a>Compatibilidad con la configuración de red
La tabla siguiente resume la compatibilidad con la configuración de red en distintos escenarios de implementación que usan Azure Site Recovery para replicarse en Azure.

### <a name="host-network-configuration"></a>Configuración de red del servidor host

**Configuración** | **Servidores físicos o de VMware** | **Hyper-V (sin Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
Formación de equipos NIC | Sí<br/><br/>No compatible en máquinas físicas| Sí | Sí
VLAN | Sí | Sí | Sí
IPv4 | Sí | Sí | Sí
IPv6 | No | No | No

### <a name="guest-vm-network-configuration"></a>Configuración de red de la máquina virtual invitada

**Configuración** | **Servidores físicos o de VMware** | **Hyper-V (sin Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
Formación de equipos NIC | No | No | No
IPv4 | Sí | Sí | Sí
IPv6 | No | No | No
Dirección IP estática (Windows) | Sí | Sí | Sí
Dirección IP estática (Linux) | No | No | No
Varias NIC | Sí | Sí | Sí

### <a name="failed-over-azure-vm-network-configuration"></a>Configuración de red de la máquina virtual de Azure a la que se conmuta por error

**Redes de Azure** | **Servidores físicos o de VMware** | **Hyper-V (sin Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
ExpressRoute | Sí | Sí | Sí
ILB | Sí | Sí | Sí
ELB | Sí | Sí | Sí
Traffic Manager | Sí | Sí | Sí
Varias NIC | Sí | Sí | Sí
IP reservada | Sí | Sí | Sí
IPv4 | Sí | Sí | Sí
Conservar dirección IP de origen | Sí | Sí | Sí


## <a name="support-for-storage"></a>Compatibilidad con el almacenamiento
La tabla siguiente resume la compatibilidad con la configuración de almacenamiento en distintos escenarios de implementación que usan Azure Site Recovery para replicarse en Azure.

### <a name="host-storage-configuration"></a>Configuración de almacenamiento del servidor host

**Configuración** | **Servidores físicos o de VMware** | **Hyper-V (sin Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
NFS | Sí para VMware<br/><br/> No para servidores físicos | N/D | N/D
SMB 3.0 | N/D | Sí | Sí
SAN (ISCSI) | Sí | Sí | Sí
Varias rutas (MPIO)<br></br>Probado con: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM para CLARiiON | Sí | Sí | Sí

### <a name="guest-or-physical-server-storage-configuration"></a>Configuración de almacenamiento del servidor físico o invitado

**Configuración** | **Servidores físicos o de VMware** | **Hyper-V (sin Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
VMDK | Sí | N/D | N/D
VHD/VHDX | N/D | Sí | Sí
VM de 2 generación | N/D | Sí | Sí
EFI/UEFI| No | Sí | Sí
Disco en clúster compartido | Sí para VMware<br/><br/> N/D para servidores físicos | No | No
Disco cifrado | No | No | No
NFS | No | N/D | N/D
SMB 3.0 | No | No | No
RDM | Sí<br/><br/> N/D para servidores físicos | N/D | N/D
Disco > 1 TB | No | No | No
Volumen con disco en bandas > 1 TB<br/><br/> Administración de volúmenes lógicos (LVM) | Sí | Sí | Sí
Espacios de almacenamiento | No | Sí | Sí
Agregar/quitar disco en caliente | No | No | No
Excluir el disco | Sí | Sí | Sí
Varias rutas (MPIO) | N/D | Sí | Sí

**Almacenamiento de Azure** | **Servidores físicos o de VMware** | **Hyper-V (sin Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
LRS | Sí | Sí | Sí
GRS | Sí | Sí | Sí
Almacenamiento de acceso esporádico | No | No | No
Almacenamiento de acceso frecuente| No | No | No
Cifrado en reposo (SSE)| Sí | Sí | Sí
Premium Storage | Sí | No | No
Servicio Import/Export | No | No | No


## <a name="support-for-azure-compute-configuration"></a>Compatibilidad con la configuración del proceso de Azure

**Característica de proceso** | **Servidores físicos o de VMware** | **Hyper-V (sin Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
Conjuntos de disponibilidad | No | No | No
CONCENTRADOR | Sí | Sí | Sí

## <a name="failed-over-azure-vm-requirements"></a>Requisitos de la máquina virtual de Azure a la que se conmuta por error

Puede implementar Site Recovery para replicar máquinas virtuales y servidores físicos que ejecuten cualquier sistema operativo compatible con Azure. Incluye la mayoría de las versiones de Windows y Linux. Las VM locales que desea replicar debe cumplir los siguientes requisitos de Azure cuando se replica en Azure.

**Entidad** | **Requisitos** | **Detalles**
--- | --- | ---
**Sistema operativo invitado** | Replicación de Hyper-V en Azure: Site Recovery es compatible con todos los sistemas operativos [admitidos por Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para la replicación de servidores físicos y de VMware, consulte los [requisitos previos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Arquitectura del sistema operativo invitado** | 64 bits | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Tamaño del disco del sistema operativo** | Hasta 1.023 GB | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Número de discos del sistema operativo** | 1 | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Número de discos de datos** | 64 o menos si está va a replicar **VM de VMware a Azure**; 16 o menos si va a replicar **VM de Hyper-V a Azure** | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Tamaño de VHD del disco de datos** | Hasta 1.023 GB | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Adaptadores de red** | Se admiten varios adaptadores |
**VHD compartido** | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Disco FC** | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Formato de disco duro** | VHD  <br/><br/> VHDX | Aunque VHDX no se admite en Azure en este momento, Site Recovery convierte automáticamente VHDX en VHD cuando se conmuta por error en Azure. Cuando se realiza la conmutación por recuperación en local, las máquinas virtuales siguen usando el formato VHDX.
**BitLocker** | No compatible | Se debe deshabilitar BitLocker antes de proteger una máquina virtual.
**Nombre de la máquina virtual** | Entre 1 y 63 caracteres. Restringido a letras, números y guiones. El nombre de la VM debe empezar y terminar con una letra o un número. | Actualice el valor de las propiedades de la máquina virtual en Site Recovery.
**Tipo de máquina virtual** | Generación 1<br/><br/> Generación 2 - Windows | Las VM de generación 2 con un tipo de disco de SO básico, que incluye uno o dos volúmenes de datos con el formato VHDX y menos de 300 GB de espacio en disco, son compatibles.<br></br>No se admiten las VM Linux de generación 2. [Más información](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>Compatibilidad con acciones de almacén de Recovery Services

**Acción** | **Servidores físicos o de VMware** | **Hyper-V (sin Virtual Machine Manager)** | **Hyper-V (con Virtual Machine Manager)**
--- | --- | --- | ---
Mover el almacén entre grupos de recursos<br/><br/> Entre las suscripciones | No | No | No
Mover el almacenamiento, la red y las máquinas virtuales de Azure entre grupos de recursos<br/><br/> Entre las suscripciones | No | No | No


## <a name="support-for-provider-and-agent"></a>Compatibilidad con proveedores y agentes

**Name** | **Descripción** | **La versión más reciente** | **Detalles**
--- | --- | --- | --- | ---
**Proveedor de Azure Site Recovery** | Coordina las comunicaciones entre los servidores locales y Azure <br/><br/> Se instala en servidores de Virtual Machine Manager locales o en servidores de Hyper-V si no hay ningún servidor de Virtual Machine Manager | 5.1.19 ([disponible en el portal](http://aka.ms/downloaddra)) | [Características y correcciones más recientes](https://support.microsoft.com/kb/3155002)
**Configuración unificada de Azure Site Recovery (VMware a Azure)** | Coordina las comunicaciones entre servidores de VMware locales y Azure  <br/><br/> Se instala en servidores de VMware locales | 9.3.4246.1 (disponible en el portal) | [Características y correcciones más recientes](https://support.microsoft.com/kb/3155002)
**Servicio de movilidad** | Coordina la replicación entre servidores de VMware locales o servidores físicos y el sitio secundario o Azure<br/><br/> Se instala en cada máquina virtual de VMware o servidor físico que se va a replicar  | N/D (disponible en el portal) | N/D
**Agente de Microsoft Azure Recovery Services (MARS)** | Coordina la replicación entre máquinas virtuales de Hyper-V y Azure<br/><br/> Se instala en servidores de Hyper-V locales (con o sin un servidor VMM) | Agente más reciente ([disponible en el portal](http://aka.ms/latestmarsagent)) |






## <a name="next-steps"></a>Pasos siguientes
[Comprobación de los requisitos previos](site-recovery-prereq.md)

