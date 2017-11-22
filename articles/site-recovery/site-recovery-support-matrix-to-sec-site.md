---
title: "Matriz de compatibilidad de la replicación en un sitio secundario con Azure Site Recovery | Microsoft Docs"
description: Resumen de los sistemas operativos y los componentes compatibles con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: da120d8e325867eaf9eb8b9be1ae8d9152db54c4
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2017
---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>Matriz de compatibilidad de la replicación en un sitio secundario con Azure Site Recovery

En este artículo se resumen los elementos que se admiten cuando se usa el servicio [Azure Site Recovery](site-recovery-overview.md) para realizar la replicación en un sitio local secundario.

## <a name="supported-scenarios"></a>Escenarios admitidos

**Implementación** | **Detalles** 
--- | ---
**De VMware a VMware** | Recuperación ante desastres de máquinas virtuales VMware locales en un sitio secundario de VMware.<br/><br/> Descargue la [guía del usuario de InMage Scout](https://aka.ms/asr-scout-user-guide).
**De Hyper-V a Hyper-V** | Recuperación ante desastres de máquinas virtuales de Hyper-V locales pertenecientes a nubes VMM en una nube VMM secundaria.<br></br> No se admite sin VMM.



  

## <a name="host-servers"></a>Servidores host

**Implementación** | **Soporte técnico**
--- | ---
**Servidor de máquina virtual de VMware o físico** | vCenter 5.5, 6.0 y 6.5 (compatible solo con características de 5.5)
**Hyper-V con VMM** | Windows Server 2016 y Windows Server 2012 R2 con las actualizaciones más recientes.<br/><br/> Los hosts de Windows Server 2016 deben administrarse mediante VMM 2016.<br/><br/> Actualmente, no se admiten las nubes VMM 2016 que combinan hosts de Windows Server 2016 y 2012 R2.<br/><br/> Actualmente no se admiten implementaciones que incluyan una actualización de una nube VMM 2012 R2 a System Center 2016.


## <a name="support-for-replicated-machine-os-versions"></a>Compatibilidad con las versiones de SO de las máquinas replicadas

En la tabla siguiente se resume la compatibilidad del sistema operativo con las máquinas replicadas con Site Recovery. Cualquier carga de trabajo puede ejecutarse en el sistema operativo compatible.

**Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | ---
Windows Server 2016 de 64 bits, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 con SP1 como mínimo<br/><br/> Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1 o 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1 o 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 o 6.8 que ejecuten el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 u 11 SP4  | Cualquier sistema operativo invitado [compatible con Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)

## <a name="linux-machine-storage"></a>Almacenamiento de máquinas Linux

Solo se pueden replicar máquinas Linux con el almacenamiento siguiente:

- Sistema de archivos: EXT3, ETX4, ReiserFS, XFS
- Software de múltiples rutas: asignador de dispositivos
- Administrador de volúmenes: LVM2
- No se admiten servidores físicos con almacenamiento de controlador HP CCISS.
- El sistema de archivos ReiserFS solo se admite en SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration"></a>Network configuration (Configuración de red)

### <a name="hosts"></a>Hosts

**Configuración** | **Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Formación de equipos NIC | Sí | Sí
VLAN | Sí | Sí
IPv4 | Sí | Sí
IPv6 | No | No

### <a name="guest-vms"></a>VM invitadas

**Configuración** | **Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Formación de equipos NIC | No | No
IPv4 | Sí | Sí
IPv6 | No | No
Dirección IP estática (Windows) | Sí | Sí
Dirección IP estática (Linux) | Sí | Sí
Varias NIC | Sí | Sí


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Almacenamiento de host

**Storage (host)** | **Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | --- | ---
NFS | Sí | N/D
SMB 3.0 | N/D | Sí
SAN (ISCSI) | Sí | Sí
Varias rutas (MPIO) | Sí | Sí

### <a name="guest-or-physical-server-storage"></a>Almacenamiento de servidor físico o invitado

**Configuración** | **Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | --- | ---
VMDK | Sí | N/D
VHD/VHDX | N/D | Sí (hasta 16 discos)
VM de 2 generación | N/D | Sí
Disco en clúster compartido | Sí  | No
Disco cifrado | No | No
UEFI| Sí | N/D
NFS | No | No
SMB 3.0 | No | No
RDM | Sí | N/D
Disco > 1 TB | Sí | Sí
Volumen con disco en bandas > 1 TB<br/><br/> LVM | Sí | Sí
Espacios de almacenamiento | No | Sí
Agregar/quitar disco en caliente | Sí | No
Excluir el disco | Sí | Sí
Varias rutas (MPIO) | N/D | Sí

## <a name="vaults"></a>Almacenes

**Acción** | **Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Migrar los almacenes entre los grupos de recursos (dentro de las suscripciones o entre ellas) | No | No
Migrar el almacenamiento, la red y las VM de Azure entre los grupos de recursos (dentro de las suscripciones o entre ellas) | No | No

## <a name="provider-and-agent"></a>Proveedor y agente

**Name** | **Descripción** | **La versión más reciente** | **Detalles**
--- | --- | --- | --- | ---
**Proveedor de Azure Site Recovery** | Coordina las comunicaciones entre los servidores locales y Azure <br/><br/> Se instala en servidores VMM locales o en servidores de Hyper-V si no hay ningún servidor VMM | 5.1.19 ([disponible en el portal](http://aka.ms/downloaddra)) | [Características y correcciones más recientes](https://support.microsoft.com/kb/3155002)
**Servicio de movilidad** | Coordina la replicación entre servidores VMware locales o servidores físicos y el sitio secundario<br/><br/> Se instala en cada VM de VMware o servidor físico que desea replicar  | N/D (disponible en el portal) | N/D


## <a name="next-steps"></a>Pasos siguientes

- [Replicar las máquinas virtuales de Hyper-V en nubes VMM en una nube secundaria](tutorial-vmm-to-vmm.md)
- [Replicación de máquinas virtuales de VMware y servidores físicos en un sitio secundario](tutorial-vmware-to-vmware.md)
