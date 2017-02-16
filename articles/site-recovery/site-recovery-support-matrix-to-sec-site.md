---
title: "Matriz de compatibilidad de Azure Site Recovery para la replicación en sitios secundarios | Microsoft Docs"
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
ms.date: 01/24/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: e858a70f2ce173eb6b99cbe7a130abb5fdc265e1
ms.openlocfilehash: 0a2c3bec5fc6fb44b4baddc393d6f9387bbb5b94


---
# <a name="azure-site-recovery-support-matrix-for-replicating-to-customer-owned-secondary-site"></a>Matriz de compatibilidad de Azure Site Recovery para la replicación en sitios secundarios propiedad del cliente

> [!div class="op_single_selector"]
> * [Replicación en Azure](site-recovery-support-matrix-to-azure.md)
> * [Replicación en un sitio secundario propiedad del cliente](site-recovery-support-matrix-to-sec-site.md)

[Aquí](site-recovery-best-practices.md) encontrará una lista de los requisitos previos de Azure Site Recovery, y el artículo siguiente resume los componentes y las configuraciones admitidos en Azure Site Recovery para la replicación y recuperación en sitios secundarios propiedad del cliente.

## <a name="support-for-deployment-options"></a>Compatibilidad con opciones de implementación

**Implementación** | **Servidores físicos o de VMware** | **Hyper-V (sin VMM)** | **Hyper-V (con VMM)**
--- | --- | --- | ---
**Portal de Azure** | Máquinas virtuales de VMware locales en sitios secundario de VMware.<br/><br/> Descarga de la Guía de ayuda (http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf): guía del usuario de InMage Scout. No está disponible en el Portal de Azure. | No compatible | Máquinas virtuales de Hyper-V en nubes VMM en una nube VMM secundaria local<br/><br/> Solo replicación de Hyper-V (SAN no compatible)
**Portal clásico** | Solo en el modo mantenimiento. No se pueden crear almacenes nuevos. | No compatible | Solo en el modo mantenimiento.
**PowerShell** | No compatible. | No compatible | Compatible



## <a name="support-for-datacenter-management-servers"></a>Compatibilidad con servidores de administración de centro de datos

### <a name="virtualization-management-entities"></a>Entidades de administración de virtualización

**Implementación** | **Soporte técnico**
--- | ---
**Servidor de máquina virtual de VMware o físico** | vSphere 6.0, 5.5 o 5.1 con la última actualización
**Hyper-V (con VMM)** | SCVMM 2016 y SCVMM 2012 R2

  >[!Note]
  > Actualmente, no se admite una nube SCVMM 2016 que combine hosts de Windows Server 2016 y 2012 R2.

### <a name="host-servers"></a>Servidores host

**Implementación** | **Soporte técnico**
--- | ---
**Servidor de máquina virtual de VMware o físico** | vCenter 5.5 o 6.0 (compatible solo con características de 5.5) 
**Hyper-V (sin VMM)** | Configuración no admitida para la replicación en un sitio secundario
**Hyper-V con VMM** | Windows Server 2016 y Windows Server 2012 R2 con las últimas actualizaciones<br/><br/> Los hosts de Windows Server 2016 deben administrarse mediante SCVMM 2016.

## <a name="support-for-replicated-machine-machine-os-versions"></a>Compatibilidad con las versiones de SO de las máquinas replicadas
La tabla siguiente resume la compatibilidad con los sistemas operativos en distintos escenarios de implementación cuando se usa Azure Site Recovery. Esta compatibilidad es **aplicable a cualquier carga de trabajo** que se ejecute en el sistema operativo mencionado.

**Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 R2 con al menos SP1<br/><br/> Red Hat Enterprise Linux 6.7, 7.1 y 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 7.0, 7.1 y 7.2 <br/><br/> Oracle Enterprise Linux 6.4 y 6.5, en ejecución en el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | Cualquier sistema operativo invitado [compatible con Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)

>[!Note]
>**Compatibilidad con almacenamiento para versiones Linux** Sistema de archivos (EXT3, EXT4, ReiserFS, XFS) Mapeador de dispositivo-Software de múltiples rutas (múltiples rutas) Administrador de volúmenes: LVM2 **No** se admiten servidores físicos con almacenamiento de controlador HP CCISS.
>El sistema de archivos ReiserFS solo se admite en SUSE Linux Enterprise Server 11 SP3.

## <a name="support-for-network"></a>Compatibilidad con redes
La tabla siguiente resume la compatibilidad con la configuración de red en distintos escenarios de implementación cuando se usa Azure Site Recovery.

### <a name="host-network-configuration"></a>Configuración de red del servidor host

**Configuración** | **Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Formación de equipos NIC | Sí | Sí
VLAN | Sí | Sí
IPv4 | Sí | Sí
IPv6 | No | No

### <a name="guest-vm-network-configuration"></a>Configuración de red de la máquina virtual invitada

**Configuración** | **Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Formación de equipos NIC | No | No
IPv4 | Sí | Sí
IPv6 | No | No
Dirección IP estática (Windows) | Sí | Sí
Dirección IP estática (Linux) | Sí | Sí
Varias NIC | Sí | Sí


## <a name="support-for-storage"></a>Compatibilidad con el almacenamiento
La tabla siguiente resume la compatibilidad con la configuración del almacenamiento en distintos escenarios de implementación cuando se usa Azure Site Recovery para replicar a Azure.

### <a name="host-storage-configuration"></a>Configuración de almacenamiento del servidor host

**Almacenamiento (host)** | **Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | --- | ---
NFS | Sí | N/D
SMB 3.0 | N/D | Sí
SAN (ISCSI) | Sí | Sí
Varias rutas (MPIO) | Sí | Sí

### <a name="guest-physical-server-storage-configuration"></a>Configuración de almacenamiento del servidor físico e invitado

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

## <a name="support-for-recovery-services-vault-actions"></a>Compatibilidad con acciones de almacén de Recovery Services

**Acción** | **Servidores físicos o de VMware** | **Hyper-V (con VMM)**
--- | --- | ---
Mover el almacén entre grupos de recursos<br/><br/> Entre las suscripciones | No | No
Mover el almacenamiento, la red y las máquinas virtuales de Azure entre grupos de recursos<br/><br/> Entre las suscripciones | No | No

## <a name="support-for-provider-and-agent"></a>Compatibilidad con proveedores y agentes

**Name** | **Descripción** | **La versión más reciente** | **Detalles**
--- | --- | --- | --- | ---
**Proveedor de Azure Site Recovery** | Coordina las comunicaciones entre los servidores locales y Azure <br/><br/> Se instala en servidores VMM locales o en servidores de Hyper-V si no hay ningún servidor VMM | 5.1.19 ([disponible en el portal](http://aka.ms/downloaddra)) | [Características y correcciones más recientes](https://support.microsoft.com/kb/3155002)
**Servicio de movilidad** | Coordina la replicación entre servidores de VMware locales o servidores físicos y el sitio secundario<br/><br/> Se instala en cada máquina virtual de VMware o servidor físico que se va a replicar  | NA (disponible en el portal) | N/D


## <a name="next-steps"></a>Pasos siguientes
[Preparación de la implementación](site-recovery-best-practices.md)



<!--HONumber=Jan17_HO4-->


