---
title: "Matriz de compatibilidad para la replicación de Hyper-V en Azure | Microsoft Docs"
description: "Se resumen los requisitos y componentes compatibles para la replicación de Hyper-V en Azure con Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2017
ms.author: raynew
ms.openlocfilehash: 5918c56c2b7d01c884bf846e3a7d621b3393bb96
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="support-matrix-for-hyper-v-replication-to-azure"></a>Matriz de compatibilidad para la replicación de Hyper-V en Azure


En este artículo se resumen los ajustes y los componentes compatibles para la recuperación ante desastres de máquinas virtuales de Hyper-V locales en Azure mediante el servicio [Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Escenarios admitidos

**Escenario** | **Detalles**
--- | --- 
**Hyper-V con VMM** | Ahora puede realizar la recuperación ante desastres en Azure de las máquinas virtuales que se ejecutan en los hosts de Hyper-V administrados en el tejido de System Center Virtual Machine Manager (VMM)<br/><br/> Puede implementar este escenario en Azure Portal o mediante PowerShell.<br/><br/> Cuando VMM administra los hosts de Hyper-V, también puede realizar la recuperación ante desastres en un sitio local secundario. Lea [este artículo](tutorial-vmm-to-vmm.md) para más información sobre este escenario.
**Hyper-V sin VMM** | Ahora puede realizar la recuperación ante desastres en Azure de las máquinas virtuales que se ejecutan en los hosts de Hyper-V no administrados por VMM.<br/><br/> Puede implementar este escenario en Azure Portal o mediante PowerShell. 


## <a name="on-premises-servers"></a>Servidores locales

**Servidor** | **Requisitos** | **Detalles**
--- | --- | ---
**Hyper-V (que se ejecuta sin VMM)** | Windows Server 2016 y Windows Server 2012 R2 con las actualizaciones más recientes. | Cuando se configura un sitio de Hyper-V en Site Recovery, no se admite la combinación de hosts que se ejecutan en Windows Server 2016 y 2012 R2.<br/><br/> En el caso de las máquinas virtuales en un host que ejecuta Windows Server 2016, no se admite la recuperación en una ubicación alternativa.
**Hyper-V (que se ejecuta con VMM)** | VMM 2016, VMM 2012 R2 | Si se usa VMM, los hosts de Windows Server 2016 se deben administrar en VM 2016.<br/><br/> No se admite actualmente una nube de VMM que combine hosts de Hyper-V en ejecución en Windows Server 2016 y 2012 R2.<br/><br/> No se admiten los entornos que incluyan una actualización de un servidor existente de VMM 2012 R2 a 2016.


## <a name="replicated-vms"></a>Máquinas virtuales replicadas


En la tabla siguiente se resume la compatibilidad de las máquinas virtuales. Site Recovery admite todas las cargas de trabajo que se ejecutan en un sistema operativo compatible. 

 **Componente** | **Detalles**
--- | ---
Configuración de VM | Las máquinas virtuales que se replican en Azure deben cumplir con los [requisitos de Azure](#failed-over-azure-vm-requirements).
Sistema operativo invitado | Cualquier sistema operativo invitado [compatible con Azure](https://technet.microsoft.com/library/cc794868.aspx).<br/><br/> No se admite Windows Server 2016 Nano Server.




## <a name="hyper-v-network-configuration"></a>Configuración de la red de Hyper-V

**Componente** | **Hyper-V con VMM** | **Hyper-V sin VMM**
--- | --- | ---
Red de host: formación de equipos NIC | Sí
Red de host: VLAN | Sí
Red de host: IPv4 | Sí
Red de host: IPv6 | Sin 
Red de máquina virtual invitada: formación de equipos NIC | Sin 
Red de máquina virtual invitada: IPv4 | Sí
Red de máquina virtual invitada: IPv6 | Sin 
Red de máquina virtual invitada: dirección IP estática (Windows) | Sí
Red de máquina virtual invitada: dirección IP estática (Linux) | Sin 
Red de máquina virtual invitada: varios NIC | Sí



## <a name="azure-vm-network-configuration-after-failover"></a>Configuración de la red de máquina virtual de Azure (después de la conmutación por error)

**Componente** | **Hyper-V con VMM** | **Hyper-V sin VMM**
--- | --- | ---
ExpressRoute | Sí | Sí
ILB | Sí | Sí
ELB | Sí | Sí
Traffic Manager | Sí | Sí
Varias NIC | Sí | Sí
IP reservada | Sí | Sí
IPv4 | Sí | Sí
Conservar la dirección IP de origen | Sí | Sí
Puntos de conexión de servicio de red virtual<br/><br/> (Redes virtuales y firewalls de Azure Storage) | Sin  | Sin 


## <a name="hyper-v-host-storage"></a>Almacenamiento de host de Hyper-V

**Storage** | **Hyper-V con VMM** | Hyper-V sin VMM
--- | --- | --- | ---
NFS | N/D | N/D
SMB 3.0 | Sí | Sí
SAN (ISCSI) | Sí | Sí
Varias rutas (MPIO). Probado con:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4<br/><br/> EMC PowerPath DSM para CLARiiON | Sí | Sí

## <a name="hyper-v-vm-guest-storage"></a>Almacenamiento de invitado de máquina virtual de Hyper-V

**Storage** | **Hyper-V con VMM** | Hyper-V sin VMM
--- | --- | ---
VMDK | N/D | N/D
VHD/VHDX | Sí | Sí
VM de generación 2 | Sí | Sí
EFI/UEFI| Sí | Sí
Disco en clúster compartido | Sin  | Sin 
Disco cifrado | Sin  | Sin 
NFS | N/D | N/D
SMB 3.0 | Sin  | Sin 
RDM | N/D | N/D
Disco > 1 TB | Sí, hasta 4095 GB | Sí, hasta 4095 GB
Disco: sector físico y lógico de 4 K | No compatible: Gen 1/Gen 2 | No compatible: Gen 1/Gen 2
Disco: sector lógico de 4 K y sector físico de 512 bytes | Sí |  Sí
Volumen con disco en bandas > 1 TB<br/><br/> Administración de volúmenes lógicos (LVM) | Sí | Sí
Espacios de almacenamiento | Sí | Sí
Agregar/quitar disco en caliente | Sin  | Sin 
Excluir el disco | Sí | Sí
Varias rutas (MPIO) | Sí | Sí

## <a name="azure-storage"></a>Almacenamiento de Azure

**Componente** | **Hyper-V con VMM** | **Hyper-V without VMM**
--- | --- | ---
LRS | Sí | Sí
GRS | Sí | Sí
RA-GRS | Sí | Sí
Almacenamiento de acceso esporádico | Sin  | Sin 
Almacenamiento de acceso frecuente| Sin  | Sin 
Blobs en bloques | Sin  | Sin 
Cifrado en reposo (SSE)| Sí | Sí
Premium Storage | Sí | Sí
Servicio Import/Export | Sin  | Sin 
Puntos de conexión de servicio de VNET (VNET y firewalls de Azure Storage), en el destino para almacenar en caché la cuenta de almacenamiento que se usa para los datos de replicación | Sin  | Sin 


## <a name="azure-compute-features"></a>Características de proceso de Azure

**Característica** | **Hyper-V con VMM** | **Hyper-V sin VMM**
--- | --- | ---
Conjuntos de disponibilidad | Sí | Sí
CONCENTRADOR | Sí | Sí  
Discos administrados | Sí, para la conmutación por error<br/><br/> No se admite la conmutación por recuperación de los discos administrados | Sí, para la conmutación por error<br/><br/> No se admite la conmutación por recuperación de los discos administrados

## <a name="azure-vm-requirements"></a>Requisitos de VM de Azure

Las máquinas virtuales locales que se replican en Azure deben cumplir con los requisitos de VM de Azure que se resumen en esta tabla.

**Componente** | **Requisitos** | **Detalles**
--- | --- | ---
**Sistema operativo invitado** | Site Recovery es compatible con todos los sistemas operativos [admitidos por Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Arquitectura del sistema operativo invitado** | 64 bits | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Tamaño del disco del sistema operativo** | Hasta 2048 GB para máquinas virtuales de generación 1.<br/><br/> Hasta 300 GB para máquinas virtuales de generación 2.  | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Número de discos del sistema operativo** | 1 | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Número de discos de datos** | 16 o menos  | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Tamaño de VHD del disco de datos** | Hasta 4095 GB | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Adaptadores de red** | Se admiten varios adaptadores |
**VHD compartido** | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Disco FC** | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
**Formato de disco duro** | VHD  <br/><br/> VHDX | Site Recovery convierte automáticamente VHDX en VHD cuando se conmuta por error en Azure. Cuando se realiza la conmutación por recuperación en local, las máquinas virtuales siguen usando el formato VHDX.
**BitLocker** | No compatible | Debe deshabilitar BitLocker antes de habilitar la replicación para una máquina virtual.
**Nombre de la máquina virtual** | Entre 1 y 63 caracteres. Restringido a letras, números y guiones. El nombre de la VM debe empezar y terminar con una letra o un número. | Actualice el valor de las propiedades de la máquina virtual en Site Recovery.
**Tipo de máquina virtual** | Generación 1<br/><br/> Generación 2 - Windows | Las VM de generación 2 con un tipo de disco de SO básico, que incluye uno o dos volúmenes de datos con el formato VHDX y menos de 300 GB de espacio en disco, son compatibles.<br></br>No se admiten las VM Linux de generación 2. [Más información](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="recovery-services-vault-actions"></a>Acciones del almacén de Recovery Services

**Acción** |  **Hyper-V con VMM** | **Hyper-V sin VMM**
--- | --- | --- 
Mover el almacén entre grupos de recursos<br/><br/> Entre las suscripciones | Sin  | Sin  
Mover el almacenamiento, la red y las máquinas virtuales de Azure entre grupos de recursos<br/><br/> Entre las suscripciones | Sin  | Sin  


## <a name="provider-and-agent"></a>Proveedor y agente

Para garantizar que la implementación sea compatible con la configuración que se muestra en este artículo, asegúrese de ejecutar las versiones más recientes de proveedor y agente.

**Name** | **Descripción** | **Detalles**
--- | --- | --- | --- | ---
**Proveedor de Azure Site Recovery** | Coordina las comunicaciones entre los servidores locales y Azure <br/><br/> Hyper-V con VMM: instalado en servidores VMM<br/><br/> Hyper-V sin VMM: instalado en hosts de Hyper-V| Versión más reciente: 5.1.2700.1 (disponible en el portal)<br/><br/> [Características y correcciones más recientes](https://aka.ms/latest_asr_updates)
**Agente de Microsoft Azure Recovery Services (MARS)** | Coordina la replicación entre máquinas virtuales de Hyper-V y Azure<br/><br/> Se instala en servidores de Hyper-V locales (con o sin VMM) | Agente más reciente disponible en el portal






## <a name="next-steps"></a>pasos siguientes
Obtenga información sobre cómo [preparar Azure](tutorial-prepare-azure.md) para la recuperación ante desastres de las máquinas virtuales de Hyper-V locales. 
