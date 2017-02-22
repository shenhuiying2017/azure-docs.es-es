---
title: "Matriz de compatibilidad de la replicación en un sitio secundario con Azure Site Recovery | Microsoft Docs"
description: Resumen de los sistemas operativos y los componentes compatibles con Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/08/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 2541236d84100ed7889d06f9b0580fcbc55ecfdb
ms.openlocfilehash: f9443b633601272c79739c92995d53ba1a7d2b4e


---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>Matriz de compatibilidad de la replicación en un sitio secundario con Azure Site Recovery

> [!div class="op_single_selector"]
> * [Replicación en Azure](site-recovery-support-matrix-to-azure.md)
> * [Replicación en una ubicación local](site-recovery-support-matrix-to-sec-site.md)

En este artículo se resumen los elementos que se admiten cuando usa Azure Site Recovery para replicar en un sitio local secundario.

## <a name="deployment-options"></a>Opciones de implementación

**Implementación** | **Servidores físicos o de VMware** | **Hyper-V (sin VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
**Portal de Azure** | Máquinas virtuales de VMware locales en sitios secundario de VMware.<br/><br/> Descargue la [Guía del usuario de InMage Scout](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) (no disponible en Azure Portal). | No compatible | VM de Hyper-V en nubes VMM en una nube VMM secundaria local.<br/><br/> Solo replicación de Hyper-V estándar. SAN no es compatible.
**Portal clásico** | Solo en el modo mantenimiento. No se pueden crear almacenes nuevos. | No compatible | Solo en modo de mantenimiento
**PowerShell** | No compatible | No compatible | Compatible

## <a name="on-premises-servers"></a>Servidores locales

### <a name="virtualization-servers"></a>Servidores de virtualización

**Implementación** | **Soporte técnico**
--- | ---
**Servidor de máquina virtual de VMware o físico** | vSphere 6.0, 5.5 o 5.1 con la última actualización
**Hyper-V (con VMM)** | VMM 2016 y VMM 2012 R2

  >[!Note]
  > Actualmente, no se admiten las nubes VMM 2016 que combinan hosts de Windows Server 2016 y 2012 R2.

### <a name="host-servers"></a>Servidores host

**Implementación** | **Soporte técnico**
--- | ---
**Servidor de máquina virtual de VMware o físico** | vCenter 5.5 o 6.0 (compatible solo con características de 5.5) 
**Hyper-V (sin VMM)** | Configuración no admitida para la replicación en un sitio secundario
**Hyper-V con VMM** | Windows Server 2016 y Windows Server 2012 R2 con las actualizaciones más recientes.<br/><br/> Los hosts de Windows Server 2016 deben administrarse mediante VMM 2016.

## <a name="support-for-replicated-machine-os-versions"></a>Compatibilidad con las versiones de SO de las máquinas replicadas
En la tabla siguiente se resume la compatibilidad con los sistemas operativos en los distintos escenarios de implementación que se encuentran cuando se usa Azure Site Recovery. Esta compatibilidad es aplicable a cualquier carga de trabajo que se ejecute en el SO mencionado.

**Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 R2 con al menos SP1<br/><br/> Red Hat Enterprise Linux 6.7, 7.1 y 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 7.0, 7.1 y 7.2 <br/><br/> Oracle Enterprise Linux 6.4 o 6.5 que ejecuten el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | Cualquier sistema operativo invitado [compatible con Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)

>[!Note]
>Solo se pueden replicar las máquinas Linux con el siguiente almacenamiento: sistema de archivos (EXT3, ETX4, ReiserFS, XFS); software de múltiples rutas / asignador de dispositivos; administrador de volúmenes (LVM2).
>No se admiten servidores físicos con almacenamiento de controlador HP CCISS.
>El sistema de archivos ReiserFS solo se admite en SUSE Linux Enterprise Server 11 SP3.

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


## <a name="storage"></a>Almacenamiento

### <a name="host-storage"></a>Almacenamiento de host

**Almacenamiento (host)** | **Servidores físicos o de VMware** | **Hyper-V (con VMM)**
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

Obtenga información sobre los [requisitos previos de implementación](site-recovery-prereq.md).



<!--HONumber=Feb17_HO2-->


