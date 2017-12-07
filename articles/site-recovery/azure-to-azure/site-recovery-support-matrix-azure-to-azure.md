---
title: "Matriz de compatibilidad de Azure Site Recovery para la replicación de Azure a Azure | Microsoft Docs"
description: "Se resumen los sistemas operativos compatibles y las configuraciones para la replicación de Azure Site Recovery de máquinas virtuales (VM) de Azure de una región a otra en caso de que sea necesario realizar una recuperación ante desastres."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: 46ca545cc73d7b8118aae4662c31965c7db87df5
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-azure-to-azure"></a>Matriz de compatibilidad de Azure Site Recovery para la replicación de Azure a Azure


>[!NOTE]
>
> La replicación de Site Recovery en máquinas virtuales de Azure se encuentra actualmente en versión preliminar.

En este artículo se resumen las configuraciones y los componentes admitidos en Azure Site Recovery al replicar y recuperar máquinas virtuales de Azure de una región a otra.

## <a name="user-interface-options"></a>Opciones de la interfaz de usuario

**Interfaz de usuario** |  **Se admite/no se admite**
--- | ---
**Portal de Azure** | Compatible
**Portal clásico** | No compatible
**PowerShell** | No se admite actualmente.
**API de REST** | No se admite actualmente.
**CLI** | No se admite actualmente.


## <a name="resource-move-support"></a>Compatibilidad con el movimiento de recursos.

**Tipo de movimiento de recursos** | **Se admite/no se admite** | **Comentarios**  
--- | --- | ---
**Mover el almacén entre grupos de recursos** | No compatible |No se puede mover el almacén de servicios de recuperación entre grupos de recursos.
**Mover los servicios Compute, Storage y Network entre grupos de recursos** | No compatible |Si mueve una máquina virtual (o sus componentes asociados, como Storage y Network) después de habilitar la replicación, deberá deshabilitar la replicación y habilitarla de nuevo en la máquina virtual.



## <a name="support-for-deployment-models"></a>Compatibilidad con modelos de implementación

**Modelo de implementación** | **Se admite/no se admite** | **Comentarios**  
--- | --- | ---
**Clásico** | Compatible | Solo puede replicar una máquina virtual clásica y recuperarla como máquina virtual clásica. No puede recuperarla como una máquina virtual de Resource Manager. Tampoco se admite la implementación de una máquina virtual clásica sin una red virtual y directamente en una región de Azure.
**Resource Manager** | Compatible |

>[!NOTE]
>
> - No se admite la replicación de máquinas virtuales de Azure de una suscripción a otra para escenarios de recuperación ante desastres.
> - No se admite la migración de máquinas virtuales de Azure entre suscripciones.
> - No se admite la migración de máquinas virtuales de Azure dentro de la misma región.
> - No se admite la migración de máquinas virtuales de Azure del modelo de implementación clásico al modelo de implementación de Resource Manager.

## <a name="support-for-replicated-machine-os-versions"></a>Compatibilidad con las versiones de SO de las máquinas replicadas

Esta compatibilidad es aplicable a cualquier carga de trabajo que se ejecute en el SO mencionado.

#### <a name="windows"></a>Windows

- Windows Server 2016 (Server Core y Server con Experiencia de escritorio)*
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 con al menos SP1

>[!NOTE]
>
> \* No se admite Windows Server 2016 Nano Server.

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3
- CentOS 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3
- Servidor Ubuntu 14.04 LTS[ (versiones de kernel admitidas)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Servidor Ubuntu 16.04 LTS[ (versiones de kernel admitidas)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7
- Debian 8
- Oracle Enterprise Linux 6.4 y 6.5, en ejecución en el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3)
- SUSE Linux Enterprise Server 11 SP3
- SUSE Linux Enterprise Server 11 SP4

(No se admite la actualización de máquinas de replicación de SLES 11 SP3 a SLES 11 SP4. Si se ha actualizado una máquina replicada de SLES 11SP3 a SLES 11 SP4, debe deshabilitar la replicación y volver a proteger la máquina después de la actualización).

>[!NOTE]
>
> Los servidores Ubuntu que utilizan inicio de sesión y autenticación basados en contraseña y que utilizan el paquete de inicialización en la nube para configurar máquinas virtuales en la nube, pueden tener el inicio de sesión basado en contraseña deshabilitado tras una conmutación por error (en función de dicha inicialización). El inicio de sesión basado en contraseña puede habilitarse de nuevo en la máquina virtual mediante el restablecimiento de la contraseña en el menú de configuración (en la sección SOPORTE TÉCNICO + SOLUCIÓN DE PROBLEMAS) de la máquina virtual conmutada por error en Azure Portal.

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Versiones de kernel de Ubuntu admitidas para máquinas virtuales de Azure

**Versión** | **Versión de Mobility service** | **Versión de kernel** |
--- | --- | --- |
14.04 LTS | 9.9 | 3.13.0-24-generic a 3.13.0-117-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-75-generic |
14.04 LTS | 9.10 | 3.13.0-24-generic a 3.13.0-121-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-81-generic |
14.04 LTS | 9.11 | 3.13.0-24-generic a 3.13.0-125-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-83-generic |
16.04 LTS | 9.10 | 4.4.0-21-generic a 4.4.0-81-generic,<br/>4.8.0-34-generic a 4.8.0-56-generic,<br/>4.10.0-14-generic a 4.10.0-24-generic |
16.04 LTS | 9.11 | 4.4.0-21-generic a 4.4.0-83-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-27-generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Sistemas de archivos y configuraciones de almacenamiento de invitado admitidos en máquinas virtuales de Azure que ejecutan el sistema operativo Linux

* Sistemas de archivos: ext3, ext4, ReiserFS (solo Suse Linux Enterprise Server), XFS
* Administrador de volúmenes: LVM2
* Software de múltiples rutas: asignador de dispositivos

## <a name="region-support"></a>Regiones admitidas

Puede replicar y recuperar máquinas virtuales entre dos regiones cualesquiera dentro del mismo clúster geográfico.

**Clúster geográfico** | **Regiones de Azure**
-- | --
América | Centro de Canadá y este de Canadá, centro-sur de EE. UU., centro-oeste de EE. UU., este de EE. UU., este de EE. UU. 2, oeste de EE. UU., oeste de EE. UU. 2 centro de EE. UU., centro-norte de EE. UU.
Europa | Oeste de Reino Unido, Sur de Reino Unido, Europa del Norte, Europa Occidental
Asia | India del Sur, centro de la India, Sudeste Asiático, Asia Oriental, Japón Oriental, Japón Occidental, Corea Central, Corea del Sur
Australia   | Este de Australia, Sudeste de Australia

>[!NOTE]
>
> En la región sur de Brasil, solo puede replicar y conmutar por error a las regiones de centro-sur de EE. UU., centro-oeste de EE. UU., este de EE. UU., este de EE. UU. 2, oeste de EE. UU., oeste de EE. UU. 2 y centro-norte de EE. UU. y conmutar por recuperación.


## <a name="support-for-compute-configuration"></a>Compatibilidad con la configuración de Compute

**Configuración** | **No admite/no se admite** | **Comentarios**
--- | --- | ---
Tamaño | Cualquier tamaño de máquina virtual de Azure con 2 núcleos de CPU y 1 GB de RAM | Consulte los [tamaños de máquina virtual de Azure](../../virtual-machines/windows/sizes.md)
Conjuntos de disponibilidad | Compatible | Si usa la opción predeterminada durante el paso para habilitar la replicación en el portal, el conjunto de disponibilidad se crea automáticamente en función de la configuración de la región de origen. Puede cambiar el conjunto de disponibilidad de destino en "Elemento replicado > Configuración > Compute and Network (Proceso y red) > Conjunto de disponibilidad" en cualquier momento.
Máquinas virtuales con ventaja de uso híbrido (HUB) | Compatible | Si la máquina virtual de origen tiene habilitada una licencia HUB, en la conmutación por error de prueba o la máquina virtual de conmutación por error también se usa la licencia HUB.
Conjuntos de escalado de máquinas virtuales | No compatible |
Imágenes de la galería de Azure (publicadas por Microsoft) | Compatible | Se admiten siempre y cuando la máquina virtual se ejecute en un sistema operativo compatible con Site Recovery
Imágenes de la Galería de Azure (publicadas por terceros) | Compatible | Se admiten siempre y cuando la máquina virtual se ejecute en un sistema operativo compatible con Site Recovery.
Imágenes personalizadas (publicados de terceros) | Compatible | Se admiten siempre y cuando la máquina virtual se ejecute en un sistema operativo compatible con Site Recovery.
Máquinas virtuales migradas con Site Recovery | Compatible | Si es una máquina de VMware o física que se migra a Azure mediante Site Recovery, deberá desinstalar la versión antigua del servicio de movilidad y reiniciar la máquina antes de replicarla en otra región de Azure.

## <a name="support-for-storage-configuration"></a>Compatibilidad con la configuración de Storage

**Configuración** | **No admite/no se admite** | **Comentarios**
--- | --- | ---
Tamaño de disco máximo del sistema operativo | 2048 GB | Consulte [Discos usados por las máquinas virtuales](../../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms).
Tamaño máximo del disco de datos | 4095 GB | Consulte [Discos usados por las máquinas virtuales](../../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms).
Número de discos de datos | Hasta 64, que es el admitido por un tamaño de máquina virtual específico de Azure. | Consulte los [tamaños de máquina virtual de Azure](../../virtual-machines/windows/sizes.md)
Disco temporal | Siempre se excluyen de la replicación | El disco temporal se excluye de la replicación siempre. Como recomienda Azure, no se deben colocar los datos persistentes en los discos temporales. Consulte [Discos temporales en máquinas virtuales de Azure](../../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk) para más información.
Velocidad de cambio de datos en el disco | Máximo de 6 Mbps por disco | Si la velocidad media de cambio de los datos en el disco sobrepasa los 6 Mbps continuamente, la replicación no mantendrá el ritmo. Sin embargo, si es una ráfaga de datos ocasional y la velocidad de cambio de los datos es superior a 6 Mbps durante algún tiempo y desciende, la replicación mantendrá el ritmo. En este caso, podría ver puntos de recuperación ligeramente retrasados.
Discos en cuentas de almacenamiento estándar | Compatible |
Discos en cuentas de almacenamiento premium | Compatible | Si una máquina virtual tiene discos repartidas entre cuentas de almacenamiento estándar y premium, puede seleccionar una cuenta de almacenamiento de destino diferente para cada disco a fin de garantizar que tenga la misma configuración de almacenamiento en la región de destino.
Discos administrados estándar | No compatible |  
Discos administrados premium | No compatible |
Espacios de almacenamiento | Compatible |         
Cifrado en reposo (SSE) | Compatible | Para las cuentas de almacenamiento de destino y de almacenamiento en caché, puede seleccionar una cuenta de almacenamiento habilitada para SSE.     
Azure Disk Encryption (ADE) | No compatible |
Agregar/quitar disco en caliente | No compatible | Si agrega o quita un disco de datos en la máquina virtual, deberá deshabilitar la replicación y habilitarla de nuevo para la máquina virtual.
Excluir el disco | No compatible|   El disco temporal se excluye de forma predeterminada.
LRS | Compatible |
GRS | Compatible |
RA-GRS | Compatible |
ZRS | No compatible |  
Almacenamiento en frío y en caliente | No compatible | Los discos de máquina virtual no admiten el almacenamiento temporal y permanente.
Puntos de conexión del servicio Virtual Network (redes virtuales y firewalls de Azure Storage)  | No | No se permite el acceso a determinadas redes virtuales de Azure en cuentas de almacenamiento en caché usadas para almacenar datos replicados. 

>[!IMPORTANT]
> Asegúrese de tener en cuenta los objetivos de escalabilidad y rendimiento del disco de la máquina virtual para máquinas virtuales [Linux](../../virtual-machines/linux/disk-scalability-targets.md) o [Windows](../../virtual-machines/windows/disk-scalability-targets.md) para evitar cualquier problema de rendimiento. Si sigue la configuración predeterminada, Site Recovery creará las cuentas de almacenamiento y discos necesarios en función de la configuración de origen. Si personaliza y selecciona su propia configuración, asegúrese de seguir los objetivos de escalabilidad y rendimiento del disco para las máquinas virtuales de origen.

## <a name="support-for-network-configuration"></a>Compatibilidad con la configuración de red
**Configuración** | **No admite/no se admite** | **Comentarios**
--- | --- | ---
Tarjeta de interfaz de red (NIC) | Hasta el número máximo de NIC admitidas por un tamaño específico de máquina virtual de Azure. | Las NIC se crean cuando la máquina virtual se crea como parte de la operación de conmutación por error o conmutación por error de prueba. El número de tarjetas NIC en la máquina virtual de conmutación por error viene determinado por el número de tarjetas NIC que haya en la máquina virtual de origen en el momento de habilitar la replicación. El hecho de agregar o quitar tarjetas NIC después de habilitar la replicación, no influye sobre el número de tarjetas NIC en la máquina virtual de conmutación por error.
Equilibrador de carga de Internet | Compatible | Deberá asociar el equilibrador de carga configurado previamente con un script de automatización de Azure de un plan de recuperación.
Equilibrador de carga interno | Compatible | Deberá asociar el equilibrador de carga configurado previamente con un script de automatización de Azure de un plan de recuperación.
Dirección IP pública| Compatible | Deberá asociar a la NIC una IP pública ya existente o una nueva mediante un script de automatización de Azure de un plan de recuperación.
NSG en NIC (Resource Manager)| Compatible | Deberá asociar el NSG a la NIC con un script de automatización de Azure de un plan de recuperación.  
NSG en una subred (Resource Manager y modelo clásico)| Compatible | Deberá asociar el NSG a la NIC con un script de automatización de Azure de un plan de recuperación.
NSG en una máquina virtual (modelo clásico)| Compatible | Deberá asociar el NSG a la NIC con un script de automatización de Azure de un plan de recuperación.
IP reservada (IP estática)/retener la IP de origen | Compatible | Si la NIC de la máquina virtual de origen tiene una configuración de IP estática y la subred de destino tiene la misma IP disponible, se asigna a la máquina virtual de conmutación por error. Si la subred de destino no tiene la misma IP disponible, una de las direcciones IP disponibles de la subred se reserva para esta máquina virtual. Puede especificar una IP fija de su elección en "Elemento replicado > Configuración > Compute and Network (Proceso y red) > Interfaces de red". Puede seleccionar la NIC y especificar la subred y la dirección IP de su elección.
IP dinámica| Compatible | Si la NIC de la máquina virtual de origen tiene una configuración de IP dinámica, la NIC de la máquina virtual de conmutación por error es también dinámica de forma predeterminada. Puede especificar una IP fija de su elección en "Elemento replicado > Configuración > Compute and Network (Proceso y red) > Interfaces de red". Puede seleccionar la NIC y especificar la subred y la dirección IP de su elección.
Integración de Traffic Manager | Compatible | Puede configurar previamente Traffic Manager de forma que el tráfico se dirija regularmente al punto de conexión de la región de origen y al punto de conexión de la región de destino en caso de conmutación por error.
DNS administrado por Azure | Compatible |
DNS personalizado  | Compatible |    
Proxy no autenticado | Compatible | Consulte el [documento de instrucciones sobre redes](site-recovery-azure-to-azure-networking-guidance.md).    
Proxy autenticado | No compatible | Si la máquina virtual usa un proxy autenticado para la conectividad saliente, no se puede replicar mediante Azure Site Recovery.    
VPN de sitio a sitio local (con o sin ExpressRoute)| Compatible | Asegúrese de que los UDR y NSG estén configurados de manera que el tráfico de Site Recovery no se dirija al entorno local. Consulte el [documento de instrucciones sobre redes](site-recovery-azure-to-azure-networking-guidance.md).  
Conexión de red virtual a red virtual | Compatible | Consulte el [documento de instrucciones sobre redes](site-recovery-azure-to-azure-networking-guidance.md).  


## <a name="next-steps"></a>Pasos siguientes
- Aprenda más en las [instrucciones sobre redes para replicar máquinas virtuales de Azure](site-recovery-azure-to-azure-networking-guidance.md).
- Comience a proteger las cargas de trabajo mediante la [replicación de máquinas virtuales de Azure](azure-to-azure-quickstart.md).
