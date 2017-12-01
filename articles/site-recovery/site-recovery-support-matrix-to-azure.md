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
ms.date: 10/30/2017
ms.author: rajanaki
ms.openlocfilehash: 0302b4f8f4171d288a7e7c62de036c6f1cec8212
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-on-premises-to-azure"></a>Matriz de compatibilidad de Azure Site Recovery para la replicación desde local a Azure


En este artículo se resumen los componentes y las configuraciones compatibles con Azure Site Recovery cuando se replica y recupera en Azure. Para más información sobre los requisitos de Azure Site Recovery, vea los [requisitos previos](site-recovery-prereq.md).

> [!NOTE]
> Asegúrese de actualizar a la versión más reciente del proveedor y el agente de Site Recovery para conseguir compatibilidad con las actualizaciones de la matriz de compatibilidad.


## <a name="support-for-deployment-options"></a>Compatibilidad con opciones de implementación

**Implementación** | **Servidores físicos o de VMware** | **Hyper-V (con o sin Virtual Machine Manager)** |
--- | --- | ---
**Portal de Azure** | VM de VMware locales en Azure Storage, con redes y almacenamiento clásicos o Azure Resource Manager.<br/><br/> Realice la conmutación por error en las VM clásicas o basadas en Resource Manager. | Máquinas virtuales de Hyper-V locales en Azure Storage con Resource Manager o redes y almacenamiento clásicos.<br/><br/> Realice la conmutación por error en las VM clásicas o basadas en Resource Manager.
**Portal clásico** | Solo en el modo mantenimiento. No se pueden crear almacenes nuevos. | Solo en el modo mantenimiento.
**PowerShell** | No se admite actualmente. | Compatible


## <a name="support-for-datacenter-management-servers"></a>Compatibilidad con servidores de administración de centro de datos

### <a name="virtualization-management-entities"></a>Entidades de administración de virtualización

**Implementación** | **Soporte técnico**
--- | ---
**Servidor de máquina virtual de VMware o físico** | vCenter 6.5, 6.0 o 5.5
**Hyper-V (con Virtual Machine Manager)** | System Center Virtual Machine Manager 2016 y System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > No se admite actualmente una nube de System Center Virtual Machine Manager 2016 que combine hosts de Windows Server 2016 y 2012 R2.
  > Las configuraciones que incluyen la actualización de un SCVMM 2012 R2 a 2016 existente no son compatibles actualmente.
### <a name="host-servers"></a>Servidores host

**Implementación** | **Soporte técnico**
--- | ---
**Servidor de máquina virtual de VMware o físico** | vSphere 6.5, 6.0 y 5.5
**Hyper-V (con o sin Virtual Machine Manager)** | Windows Server 2016 y Windows Server 2012 R2 con las actualizaciones más recientes.<br></br>Si se usa SCVMM, los hosts de Windows Server 2016 debe administrarlos SCVMM 2016.


  >[!Note]
  >No se admite actualmente un sitio de Hyper-V que combine hosts que ejecutan Windows Server 2016 y 2012 R2. Actualmente, no se admite la recuperación en una ubicación alternativa de las máquinas virtuales en un host de Windows Server 2016.

## <a name="support-for-replicated-machine-os-versions"></a>Compatibilidad con las versiones de SO de las máquinas replicadas

Las máquinas virtuales que están protegidas deben cumplir los [requisitos de Azure](#failed-over-azure-vm-requirements) cuando se replican en Azure.
La tabla siguiente resume la compatibilidad con los sistemas operativos de las máquinas replicadas en distintos escenarios de implementación cuando se usa Azure Site Recovery. Esta compatibilidad es aplicable a cualquier carga de trabajo que se ejecute en el SO mencionado.

 **Servidores físicos o de VMware** | **Hyper-V (con o sin VMM)** |
--- | --- |
Windows Server 2016 de 64 bits (Server Core, Server con Experiencia de escritorio)\*, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 con al menos SP1<br/><br/> Red Hat Enterprise Linux: 5.2 a 5.11, 6.1 a 6.9 y 7.0 a 7.3 <br/><br/>CentOS: 5.2 a 5.11, 6.1 a 6.9 y 7.0 a 7.3 <br/><br/>Servidor Ubuntu 14.04 LTS[ (versiones de kernel admitidas)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Servidor Ubuntu 16.04 LTS[ (versiones de kernel admitidas)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4 y 6.5, en ejecución en el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>(No se admite la actualización de máquinas de replicación de SLES 11 SP3 a SLES 11 SP4. Si se ha actualizado una máquina replicada de SLES 11SP3 a SLES 11 SP4, debe deshabilitar la replicación y volver a proteger la máquina después de la actualización). | Cualquier sistema operativo invitado [compatible con Azure](https://technet.microsoft.com/library/cc794868.aspx)

>[!NOTE]
>
> \* No se admite Windows Server 2016 Nano Server.

>[!IMPORTANT]
>(Se aplica a los servidores físicos/VMware que se replican en Azure).
>
> En servidores Red Hat Enterprise Linux Server 7+ y CentOS 7+, la versión de kernel 3.10.0-514 se admite a partir de la versión 9.8 del servicio Azure Site Recovery Mobility.<br/><br/>
> Los clientes con el kernel 3.10.0-514 que dispongan de una versión de Mobility Service anterior a la 9.8, deben deshabilitar la replicación, actualizar la versión de Mobility Service a la 9.8 y volver a habilitar la replicación.


### <a name="supported-ubuntu-kernel-versions-for-vmwarephysical-servers"></a>Versiones de kernel de Ubuntu admitidas para servidores VMware y Physical

**Versión** | **Versión de Mobility service** | **Versión de kernel** |
--- | --- | --- |
14.04 LTS | 9.9 | 3.13.0-24-generic a 3.13.0-117-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-75-generic |
14.04 LTS | 9.10 | 3.13.0-24-generic a 3.13.0-121-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic a 3.13.0-128-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-91-generic |
14.04 LTS | 9.12 | 3.13.0-24-generic a 3.13.0-132-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-96-generic |
16.04 LTS | 9.10 | 4.4.0-21-generic a 4.4.0-81-generic,<br/>4.8.0-34-generic a 4.8.0-56-generic,<br/>4.10.0-14-generic a 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-generic a 4.4.0-91-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-32-generic |
16.04 LTS | 9.12 | 4.4.0-21-generic a 4.4.0-96-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-35-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-linux-vmwarephysical-servers"></a>Sistemas de archivos compatibles y configuraciones de almacenamiento de invitado en Linux (servidores físicos/VMware)

Los sistemas de archivos y el software de configuración de almacenamiento siguientes son compatibles con servidores Linux que se ejecutan en servidores físicos o VMware:
* Sistemas de archivos: ext3, ext4, ReiserFS (solo Suse Linux Enterprise Server), XFS
* Administrador de volúmenes: LVM2
* Software de múltiples rutas: asignador de dispositivos

No se admiten dispositivos de almacenamiento de paravirtualizados (dispositivos exportados por controladores paravirtualizados).<br/>
No se admiten dispositivos de E/S de bloque multicola.<br/>
No se admiten servidores físicos con controlador de almacenamiento HP CCISS.<br/>

>[!Note]
> En servidores Linux, todos los directorios siguientes (si se configuran como sistemas de archivos o particiones independientes) deben ubicarse en el mismo disco (el disco de sistema operativo) del servidor de origen: / (root), /boot, /usr, /usr/local, /var, /etc.<br/><br/>
> Las características de XFSv5 en sistemas de archivos XFS, como la suma de comprobación de metadatos, se admiten a partir de la versión 9.10 de Mobility Service. Si está usando características de XFSv5, asegúrese de que está ejecutando la versión 9.10 de Mobility Service o una versión posterior. Puede usar la utilidad xfs_info para comprobar el superbloque XFS para la partición. Si ftype está establecido en 1, entonces se usan las características de XFSv5.
>


## <a name="support-for-network-configuration"></a>Compatibilidad con la configuración de red
La tabla siguiente resume la compatibilidad con la configuración de red en distintos escenarios de implementación que usan Azure Site Recovery para replicarse en Azure.

### <a name="host-network-configuration"></a>Configuración de red del servidor host

**Configuración** | **Servidores físicos o de VMware** | **Hyper-V (con o sin Virtual Machine Manager)**
--- | --- | ---
Formación de equipos NIC | Sí<br/><br/>No se admite cuando se replican las máquinas virtuales| Sí
VLAN | Sí | Sí
IPv4 | Sí | Sí
IPv6 | No | No

### <a name="guest-vm-network-configuration"></a>Configuración de red de la máquina virtual invitada

**Configuración** | **Servidores físicos o de VMware** | **Hyper-V (con o sin Virtual Machine Manager)**
--- | --- | ---
Formación de equipos NIC | No | No
IPv4 | Sí | Sí
IPv6 | No | No
Dirección IP estática (Windows) | Sí | Sí
Dirección IP estática (Linux) | Sí <br/><br/>Las máquinas virtuales se configuran para usar DHCP en la conmutación por recuperación  | No
Varias NIC | Sí | Sí

### <a name="failed-over-azure-vm-network-configuration"></a>Configuración de red de la máquina virtual de Azure a la que se conmuta por error

**Redes de Azure** | **Servidores físicos o de VMware** | **Hyper-V (con o sin Virtual Machine Manager)**
--- | --- | ---
ExpressRoute | Sí | Sí
ILB | Sí | Sí
ELB | Sí | Sí
Traffic Manager | Sí | Sí
Varias NIC | Sí | Sí
IP reservada | Sí | Sí
IPv4 | Sí | Sí
Conservar dirección IP de origen | Sí | Sí
Puntos de conexión del servicio Virtual Network (redes virtuales y firewalls de Azure Storage) | No | No


## <a name="support-for-storage"></a>Compatibilidad con el almacenamiento
La tabla siguiente resume la compatibilidad con la configuración de almacenamiento en distintos escenarios de implementación que usan Azure Site Recovery para replicarse en Azure.

### <a name="host-storage-configuration"></a>Configuración de almacenamiento del servidor host

**Configuración** | **Servidores físicos o de VMware** | **Hyper-V (con o sin Virtual Machine Manager)**
--- | --- | --- | ---
NFS | Sí para VMware<br/><br/> No para servidores físicos | N/D
SMB 3.0 | N/D | Sí
SAN (ISCSI) | Sí | Sí
Varias rutas (MPIO)<br></br>Probado con: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM para CLARiiON | Sí | Sí

### <a name="guest-or-physical-server-storage-configuration"></a>Configuración de almacenamiento del servidor físico o invitado

**Configuración** | **Servidores físicos o de VMware** | **Hyper-V (con o sin Virtual Machine Manager)**
--- | --- | ---
VMDK | Sí | N/D
VHD/VHDX | N/D | Sí
VM de 2 generación | N/D | Sí
EFI/UEFI| No | Sí
Disco en clúster compartido | No | No
Disco cifrado | No | No
NFS | No | N/D
SMB 3.0 | No | No
RDM | Sí<br/><br/> N/D para servidores físicos | N/D
Disco > 1 TB | Sí<br/><br/>Hasta 4095 GB | Sí<br/><br/>Hasta 4095 GB
Disco con tamaño de sector físico 4K y lógico 4K | Sí | No compatible con máquinas virtuales de Generación 1<br/><br/>No compatible con máquinas virtuales de Generación 2
Disco con tamaño de sector físico de 512 bytes y lógico 4K | Sí |  Sí
Volumen con disco en bandas > 1 TB<br/><br/> Administración de volúmenes lógicos (LVM) | Sí | Sí
Espacios de almacenamiento | No | Sí
Agregar/quitar disco en caliente | No | No
Excluir el disco | Sí | Sí
Varias rutas (MPIO) | N/D | Sí

**Almacenamiento de Azure** | **Servidores físicos o de VMware** | **Hyper-V (con o sin Virtual Machine Manager)**
--- | --- | ---
LRS | Sí | Sí
GRS | Sí | Sí
RA-GRS | Sí | Sí
Almacenamiento de acceso esporádico | No | No
Almacenamiento de acceso frecuente| No | No
Blobs en bloques | No | No
Cifrado en reposo (SSE)| Sí | Sí
Premium Storage | Sí | Sí
Servicio Import/Export | No | No
Puntos de conexión del servicio Virtual Network (firewalls de Azure Storage y Virtual Network) configurados en la cuenta de almacenamiento de destino o la cuenta de almacenamiento en la memoria caché usadas para el almacenamiento de los datos de replicación | No | No


## <a name="support-for-azure-compute-configuration"></a>Compatibilidad con la configuración del proceso de Azure

**Característica de proceso** | **Servidores físicos o de VMware** | **Hyper-V (con o sin Virtual Machine Manager)**
--- | --- | ---
Conjuntos de disponibilidad | Sí | Sí
CONCENTRADOR | Sí | Sí  
Discos administrados | Sí | Sí<br/><br/>La conmutación por recuperación en local a partir de VM de Azure con discos administrados no se admite actualmente.

## <a name="failed-over-azure-vm-requirements"></a>Requisitos de la máquina virtual de Azure a la que se conmuta por error

Puede implementar Site Recovery para replicar máquinas virtuales y servidores físicos que ejecuten cualquier sistema operativo compatible con Azure. Incluye la mayoría de las versiones de Windows y Linux. Las VM locales que desea replicar debe cumplir los siguientes requisitos de Azure cuando se replica en Azure.

**Entidad** | **Requisitos** | **Detalles**
--- | --- | ---
**Sistema operativo invitado** | Replicación de Hyper-V en Azure: Site Recovery es compatible con todos los sistemas operativos [admitidos por Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para la replicación de servidores físicos y de VMware, consulte los [requisitos previos](site-recovery-vmware-to-azure-classic.md) | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Arquitectura del sistema operativo invitado** | 64 bits | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Tamaño del disco del sistema operativo** | Hasta 2048 GB si se están replicando **VM de VMware o servidores físicos en Azure**.<br/><br/>Hasta 2048 GB para VM de **la generación 1 de Hyper-V**.<br/><br/>Hasta 300 GB para VM de **la generación 2 de Hyper-V**.  | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Número de discos del sistema operativo** | 1 | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Número de discos de datos** | 64 o menos si está va a replicar **VM de VMware a Azure**; 16 o menos si va a replicar **VM de Hyper-V a Azure** | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Tamaño de VHD del disco de datos** | Hasta 4095 GB | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
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
**Proveedor de Azure Site Recovery** | Coordina las comunicaciones entre los servidores locales y Azure <br/><br/> Se instala en servidores de Virtual Machine Manager locales o en servidores de Hyper-V si no hay ningún servidor de Virtual Machine Manager | 5.1.2700.1 (disponible en el portal) | [Características y correcciones más recientes](https://aka.ms/latest_asr_updates)
**Configuración unificada de Azure Site Recovery (VMware a Azure)** | Coordina las comunicaciones entre servidores de VMware locales y Azure  <br/><br/> Se instala en servidores de VMware locales | 9.12.4653.1 (disponible en el portal) | [Características y correcciones más recientes](https://aka.ms/latest_asr_updates)
**Servicio de movilidad** | Coordina la replicación entre servidores de VMware locales o servidores físicos y el sitio secundario o Azure<br/><br/> Se instala en cada máquina virtual de VMware o servidor físico que se va a replicar  | 9.12.4653.1 (disponible en el portal) | [Características y correcciones más recientes](https://aka.ms/latest_asr_updates)
**Agente de Microsoft Azure Recovery Services (MARS)** | Coordina la replicación entre máquinas virtuales de Hyper-V y Azure<br/><br/> Se instala en servidores de Hyper-V locales (con o sin un servidor VMM) | Agente más reciente (disponible en el portal) |






## <a name="next-steps"></a>Pasos siguientes
[Comprobación de los requisitos previos](site-recovery-prereq.md)
