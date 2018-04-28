---
title: Matriz de compatibilidad para la replicación de máquinas virtuales de VMware y servidores físicos en Azure con Azure Site Recovery | Microsoft Docs
description: Aquí se resumen los sistemas operativos y los componentes compatibles para replicar máquinas virtuales de VMware y servidores físicos en Azure mediante Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2018
ms.author: raynew
ms.openlocfilehash: b2a6e3052c64ab6a2865a0c24a4876cb2b98d1a8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>Matriz de compatibilidad para la replicación de VMware y servidores físicos en Azure

En este artículo se resumen los ajustes y los componentes admitidos para la recuperación ante desastres de máquinas virtuales de VMware en Azure mediante [Azure Site Recovery](site-recovery-overview.md).

## <a name="replication-scenario"></a>Escenario de replicación

**Escenario** | **Detalles**
--- | ---
Máquinas virtuales de VMware | Replicación de máquinas virtuales de VMware locales en Azure. Puede implementar este escenario en Azure Portal o mediante PowerShell.
Servidores físicos | Replicación de servidores Windows/Linux físicos locales en Azure. Puede implementar este escenario en Azure Portal.

## <a name="on-premises-virtualization-servers"></a>Servidores de virtualización locales

**Servidor** | **Requisitos** | **Detalles**
--- | --- | ---
VMware | vCenter Server 6.5, 6.0 o 5.5, o vSphere 6.5, 6.0 o 5.5 | Se recomienda usar vCenter Server.<br/><br/> Se recomienda que los hosts de vSphere y los servidores vCenter se encuentren en la misma red que el servidor de procesos. De forma predeterminada, los componentes del servidor de procesos se ejecutan en el servidor de configuración, por lo esta será la red en la que se configurará el servidor de configuración, a menos que elija un servidor de procesos especializado. 
Física | N/D

## <a name="site-recovery-configuration-server"></a>Servidor de configuración de Site Recovery

El servidor de configuración es una máquina local que ejecuta componentes de Site Recovery locales, incluido el servidor de configuración, el servidor de procesos y el servidor de destino maestro. Para la replicación de VMware se configura el servidor de configuración con todos los requisitos, mediante una plantilla OVF para crear una máquina virtual de VMware. Para la replicación de un servidor físico, la máquina del servidor de configuración se configura manualmente.

**Componente** | **Requisitos**
--- |---
Núcleos de CPU | 8 
RAM | 12 GB
Número de discos | 3 discos<br/><br/> Los discos incluyen el disco del sistema operativo, el disco de memoria caché del servidor de procesos y la unidad de retención para la conmutación por recuperación.
Espacio libre en el disco | 600 GB de espacio necesarios para la memoria caché del servidor de procesos.
Espacio libre en el disco | 600 GB de espacio necesarios para la unidad de retención.
Sistema operativo  | Windows Server 2012 R2 o Windows Server 2016 | 
Configuración regional del sistema operativo | Español (es-es) 
PowerCLI | Se debe instalar [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0").
Roles de Windows Server | No habilite: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
Directivas de grupo| No habilite: <br> - Impedir el acceso al símbolo del sistema. <br> - Impedir el acceso a herramientas de edición del Registro. <br> - Confiar en la lógica de datos adjuntos de archivos. <br> - Activar la ejecución de scripts. <br> [Más información](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Asegúrese de lo siguiente:<br/><br/> - No hay ningún sitio web preexistente predeterminado <br> - Habilitar la [autenticación anónima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Habilitar la configuración de [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br> - No hay ningún sitio web o aplicación preexistente que escuche en el puerto 443<br>
Tipo de NIC | VMXNET3 (cuando se implementa como una máquina virtual VMware) 
Tipo de dirección IP | estática 
Puertos | 443 se usa para la orquestación del canal de control<br>9443 se usa para el transporte de datos

## <a name="replicated-machines"></a>Máquinas replicadas

Site Recovery admite la replicación de cualquier carga de trabajo que se ejecute en una máquina compatible.

**Componente** | **Detalles**
--- | ---
Configuración del equipo | Las máquinas que se replican en Azure deben cumplir con los [requisitos de Azure](#azure-vm-requirements).
Sistema operativo Windows | Windows Server 2016 de 64 bits (Server Core, Server con Experiencia de escritorio), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 con al menos SP1. No se admite Windows 2016 Nano Server.
Sistema operativo Linux | Red Hat Enterprise Linux 5.2 a 5.11, 6.1 a 6.9 y 7.0 a 7.4 <br/><br/>CentOS 5.2 a 5.11, 6.1 a 6.9 y 7.0 a 7.4 <br/><br/>Servidor Ubuntu 14.04 LTS[ (versiones de kernel admitidas)](#ubuntu-kernel-versions)<br/><br/>Servidor Ubuntu 16.04 LTS[ (versiones de kernel admitidas)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8[ (versiones de kernel admitidas)](#debian-kernel-versions)<br/><br/>Oracle Enterprise Linux 6.4, 6.5 que ejecuten el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/>SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4 <br/><br/>No se admite la actualización de máquinas replicadas de SP3 a SP4. Para actualizar, deshabilite la replicación y habilítela de nuevo después de la actualización.

>[!NOTE]
>
> - En las distribuciones de Linux, solo se admiten kernels de stock que forman parte del lanzamiento o la actualización de la versión secundaria de la distribución.
>
> - La actualización de máquinas protegidas entre distribuciones de versiones principales de Linux no se admite. Para actualizar, deshabilite la replicación, actualice el sistema operativo y, a continuación, habilite la replicación de nuevo.
>

### <a name="ubuntu-kernel-versions"></a>Versiones de kernel de Ubuntu


**Versión compatible** | **Versión de Azure Site Recovery Mobility Service** | **Versión de kernel** |
--- | --- | --- |
14.04 LTS | 9.11 | 3.13.0-24-generic a 3.13.0-128-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-91-generic |
14.04 LTS | 9.12 | 3.13.0-24-generic a 3.13.0-132-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-generic a 3.13.0-137-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-104-generic |
14.04 LTS | 9.14 | 3.13.0-24-generic a 3.13.0-142-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-116-generic |
16.04 LTS | 9.11 | 4.4.0-21-generic a 4.4.0-91-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-32-generic |
16.04 LTS | 9.12 | 4.4.0-21-generic a 4.4.0-96-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-35-generic |
16.04 LTS | 9.13 | 4.4.0-21-generic a 4.4.0-104-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-42-generic |
16.04 LTS | 9.14 | 4.4.0-21-generic a 4.4.0-116-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic to 4.13.0-36-generic,<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure to 4.13.0-1011-azure |


### <a name="debian-kernel-versions"></a>Versiones de kernel de Debian


**Versión compatible** | **Versión de Azure Site Recovery Mobility Service** | **Versión de kernel** |
--- | --- | --- |
Debian 7 | 9.14 | 3.2.0-4-amd64 a 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 8 | 9.14 | 3.16.0-4-amd64 a 3.16.0-5-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.5-amd64 |


## <a name="linux-file-systemsguest-storage"></a>Sistemas de archivos Linux/almacenamiento de invitados

**Componente** | **Compatible**
--- | ---
Sistemas de archivos | ext3, ext4, XFS.
Administrador de volúmenes | LVM2.
Software de múltiples rutas | Mapeador de dispositivo.
Dispositivos de almacenamiento paravirtualizados | No se admiten dispositivos que se hayan exportado con controladores paravirtualizados.
Dispositivos de E/S de bloque multicola | No compatible.
Servidores físicos con controlador de almacenamiento HP CCISS | No compatible.
Directorios | Todos estos directorios (si se configuran como sistemas de archivos o particiones independientes) deben ubicarse en el mismo disco de sistema operativo del servidor de origen: /(root), /boot, /usr, /usr/local, /var, /etc.</br></br> /boot debe estar en una partición de disco y no estar en un volumen LVM.<br/><br/>
Espacio en disco necesario| 2 GB en la partición /root <br/><br/> 250 MB en la carpeta de instalación
XFSv5 | Las características de XFSv5 en sistemas de archivos XFS, como la suma de comprobación de metadatos, se admiten a partir de la versión 9.10 de Mobility Service. Puede usar la utilidad xfs_info para comprobar el superbloque XFS de la partición. Si ftype está establecido en 1, entonces se usan las características de XFSv5.



## <a name="network"></a>Red

**Componente** | **Compatible**
--- | ---
Formación de equipos de adaptador de red de la red de host | Se admite en máquinas virtuales de VMware. <br/><br/>No se admite en la replicación de máquinas físicas.
VLAN de la red de host | Sí.
IPv4 de la red de host | Sí.
IPv6 de la red de host | Nº
Formación de equipos de adaptador de red de la red de invitado o de servidor | Nº
IPv4 de la red de invitado o de servidor | Sí.
IPv6 de la red de invitado o de servidor | Nº
IP estática de la red de invitado o de servidor (Windows) | Sí.
IP estática de la red de invitado o de servidor (Linux) | Sí. <br/><br/>Las máquinas virtuales se configuran para usar DHCP en la conmutación por recuperación.
Varios adaptadores de red de la red de invitado o de servidor | Sí.


## <a name="azure-vm-network-after-failover"></a>Red de máquinas virtuales de Azure (después de la conmutación por error)

**Componente** | **Compatible**
--- | ---
Azure ExpressRoute | Sí
ILB | Sí
ELB | Sí
Administrador de tráfico de Azure | Sí
Varias NIC | Sí
Dirección IP reservada | Sí
IPv4 | Sí
Conservar la dirección IP de origen | Sí
Puntos de conexión del servicio Azure Virtual Network<br/><br/> (Redes virtuales y firewalls de Azure Storage) | Sin 

## <a name="storage"></a>Storage
**Componente** | **Compatible**
--- | ---
NFS de host | Sí para VMware<br/><br/> No para servidores físicos
SAN de host (ISCSI) | Sí
Varias rutas de host (MPIO) | Sí, probado con DSM de Microsoft, EMC PowerPath 5.7 SP4, EMC PowerPath DSM para CLARiiON.
VMDK de invitado/servidor | Sí
EFI/UEFI de invitado/servidor| Parcial (migración a Azure solo para Windows Server 2012 y, posteriormente, máquinas virtuales de VMware). </br></br> Consulte la nota al final de la tabla.
Disco de clúster compartido de invitado/servidor | Sin 
Disco cifrado de invitado/servidor | Sin 
NFS de invitado/servidor | Sin 
SMB 3.0 de invitado/servidor | Sin 
RDM de invitado/servidor | Sí<br/><br/> N/D para servidores físicos
Disco de invitado/servidor > 1 TB | Sí<br/><br/>Hasta 4095 GB
Disco de invitado/servidor con tamaño de sector físico de 4K y lógico de 4K | Sí
Disco de invitado/servidor con tamaño de sector físico de 512 bytes y lógico de 4K | Sí
Volumen de invitado/servidor con disco seccionado > 4 TB <br><br/>Administración de volúmenes lógicos (LVM)| Sí
Invitado/servidor: espacios de almacenamiento | Sin 
Invitado/servidor: adición/eliminación de disco en caliente | Sin 
Invitado/servidor: disco de exclusión | Sí
Varias rutas (MPIO) de invitado/servidor | N/D

> [!NOTE]
> Es posible migrar a Azure las máquinas virtuales de VMware con arranque UEFI que ejecuten Windows Server 2012 o versiones posteriores. Se aplican las restricciones que se indican a continuación:

> - Se admite solo la migración a Azure. No se admite la conmutación por recuperación a sitios de VMware locales.
> - El servidor no debe tener más de cuatro particiones en el disco del sistema operativo.
> - Requiere la versión de Mobility Service 9.13 o una posterior.
> - No se admite para servidores físicos.

## <a name="azure-storage"></a>Almacenamiento de Azure

**Componente** | **Compatible**
--- | ---
Almacenamiento con redundancia local | Sí
Almacenamiento con redundancia geográfica | Sí
Almacenamiento con redundancia geográfica con acceso de lectura | Sí
Almacenamiento de acceso esporádico | Sin 
Almacenamiento de acceso frecuente| Sin 
Blobs en bloques | Sin 
Cifrado en reposo (Cifrado del servicio Storage)| Sí
Premium Storage | Sí
Servicio Import/Export | Sin 
Puntos de conexión del servicio Virtual Network<br/><br/> Redes virtuales y firewalls de Azure Storage configurados en la cuenta de almacenamiento o la cuenta de almacenamiento en caché de destino (se usa para almacenar datos de replicación) | Sin 
Cuentas de almacenamiento de uso general v2 (niveles de acceso frecuente y esporádico) | Sin 

## <a name="azure-compute"></a>Azure Compute

**Característica** | **Compatible**
--- | ---
Conjuntos de disponibilidad | Sí
CONCENTRADOR | Sí
Discos administrados | Sí

## <a name="azure-vm-requirements"></a>Requisitos de VM de Azure

Las máquinas virtuales locales que se replican en Azure deben cumplir con los requisitos de VM de Azure que se resumen en esta tabla. Cuando Site Recovery ejecuta una comprobación de requisitos previos, se producirá un error si no se cumplen algunos de los requisitos.

**Componente** | **Requisitos** | **Detalles**
--- | --- | ---
Sistema operativo invitado | Comprobar los [sistemas operativos compatibles](#replicated machines). | Se produce un error en la comprobación si no es compatible. 
Arquitectura del sistema operativo invitado | 64 bits | Se produce un error en la comprobación si no es compatible. 
Tamaño del disco del sistema operativo | Hasta 2048 GB | Se produce un error en la comprobación si no es compatible. 
Número de discos del sistema operativo | 1 | Se produce un error en la comprobación si no es compatible.  
Número de discos de datos | 64 o menos | Se produce un error en la comprobación si no es compatible.  
Tamaño del disco de datos | Hasta 4095 GB | Se produce un error en la comprobación si no es compatible. 
Adaptadores de red | Se admiten varios adaptadores. | 
VHD compartido | No compatible. | Se produce un error en la comprobación si no es compatible. 
Disco FC | No compatible. | Se produce un error en la comprobación si no es compatible. 
BitLocker | No compatible. | Debe deshabilitar BitLocker antes de habilitar la replicación de una máquina. | 
Nombre de la máquina virtual | Entre 1 y 63 caracteres.<br/><br/> Restringido a letras, números y guiones.<br/><br/> El nombre de la máquina debe empezar y terminar con una letra o un número. |  Actualice el valor de las propiedades de la máquina en Site Recovery.


## <a name="vault-tasks"></a>Tareas de almacén

**Acción** | **Compatible**
--- | ---
Mover el almacén entre grupos de recursos<br/><br/> Entre las suscripciones | Sin 
Mover el almacenamiento, la red y las máquinas virtuales de Azure entre grupos de recursos<br/><br/> Entre las suscripciones | Sin 


## <a name="mobility-service"></a>Mobility Service

**Name** | **Descripción** | **La versión más reciente** | **Detalles**
--- | --- | --- | --- | ---
Instalación unificada de Azure Site Recovery | Coordina las comunicaciones entre servidores de VMware locales y Azure  <br/><br/> Se instala en servidores de VMware locales | 9.12.4653.1 (disponible en el portal) | [Características y correcciones más recientes](https://aka.ms/latest_asr_updates)
Mobility Service | Coordina la replicación entre servidores de VMware locales o servidores físicos y el sitio secundario o Azure<br/><br/> Se instala en cada máquina virtual de VMware o servidor físico que se va a replicar | 9.12.4653.1 (disponible en el portal) | [Características y correcciones más recientes](https://aka.ms/latest_asr_updates)


## <a name="next-steps"></a>Pasos siguientes
[Aprenda](tutorial-prepare-azure.md) a preparar Azure para la recuperación ante desastres de las máquinas virtuales de VMware.
