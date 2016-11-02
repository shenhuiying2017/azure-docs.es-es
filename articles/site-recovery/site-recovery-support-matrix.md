<properties
    pageTitle="Matriz de compatibilidad de Azure Site Recovery | Microsoft Azure"
    description="Resumen de los sistemas operativos y los componentes compatibles con Azure Site Recovery."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/05/2016"
    ms.author="raynew"/>


# <a name="azure-site-recovery-support-matrix"></a>Matriz de compatibilidad de Azure Site Recovery

En este artículo se resumen los sistemas operativos y los componentes compatibles con las implementaciones de Azure Site Recovery. Cada escenario de implementación tiene una lista de requisitos previos y componentes compatibles en el artículo de implementación correspondiente, pero en este tema se resumen todos ellos.

## <a name="supported-operating-systems-for-virtualization-servers"></a>Sistemas operativos compatibles para servidores de virtualización


**Origen** | **Destino** | **Sistema operativo del host**
---|---|---|--- 
**Hosts de Hyper-V (sin VMM)** | Las tablas de Azure | Windows Server 2012 R2 con las últimas actualizaciones
**Hosts de Hyper-V (con VMM)** | Las tablas de Azure | Windows Server 2012 R2 con las últimas actualizaciones
**Hosts de Hyper-V (con VMM)** | Sitio secundario de VMM | Al menos Windows Server 2012 con las últimas actualizaciones
**VCenter o hosts de VMware** | Las tablas de Azure | vCenter 5.5 o 6.0 (compatible solo con características de 5.5)  <br/><br/>  vSphere 6.0, 5.5 o 5.1 con las últimas actualizaciones
**VCenter o hosts de VMware** | Sitio secundario de VMware | vCenter 5.5 o 6.0 (compatible solo con características de 5.5)  <br/><br/>  vSphere 6.0, 5.5 o 5.1 con las últimas actualizaciones

## <a name="supported-requirements-for-replicated-machines"></a>Requisitos de compatibilidad para las máquinas replicadas

**Origen** | **Lo que se replica** | **Destino** | **Sistema operativo del host**
---|---|---|--- 
**Máquinas virtuales de Hyper-V** | Cualquier carga de trabajo | Las tablas de Azure | Cualquier sistema operativo invitado [compatible con Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> Las máquinas virtuales deben cumplir los [requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Máquinas virtuales de Hyper-V (con VMM)** | Cualquier carga de trabajo | Las tablas de Azure | Cualquier sistema operativo invitado [compatible con Azure](https://technet.microsoft.com/library/cc794868.aspx)<br/><br/> Las máquinas virtuales deben cumplir los [requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Máquinas virtuales de Hyper-V (con VMM)** | Cualquier carga de trabajo | Sitio secundario de VMM | Cualquier sistema operativo invitado [compatible con Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)
**Máquinas virtuales de VMware** | Cualquier carga de trabajo que se ejecuta en la máquina virtual Windows | Las tablas de Azure | Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 R2 con al menos SP1<br/><br/> Las máquinas virtuales deben cumplir los [requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Máquinas virtuales de VMware** | Cualquier carga de trabajo que se ejecuta en la máquina virtual de Linux | Las tablas de Azure | Red Hat Enterprise Linux 6.7, 7.1 y 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 7.0, 7.1 y 7.2 <br/><br/> Oracle Enterprise Linux 6.4 y 6.5, en ejecución en el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/>  Almacenamiento requerido: sistema de archivos (EXT3, ETX4, ReiserFS, XFS); asignador de software y dispositivos de múltiples rutas (múltiples rutas); administrador de volúmenes: (LVM2). No se admiten servidores físicos con almacenamiento de controlador HP CCISS. El sistema de archivos ReiserFS solo se admite en SUSE Linux Enterprise Server 11 SP3.<br/><br/> Las máquinas virtuales deben cumplir los [requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements)
**Máquinas virtuales de VMware** | Cualquier carga de trabajo que se ejecuta en la máquina virtual Windows | Sitio secundario de VMware | Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 R2 con al menos SP1
**Máquinas virtuales de VMware** | Cualquier carga de trabajo que se ejecuta en la máquina virtual de Linux | Sitio secundario de VMware | Red Hat Enterprise Linux 6.7, 7.1 y 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 7.0, 7.1 y 7.2 <br/><br/> Oracle Enterprise Linux 6.4 y 6.5, en ejecución en el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/>  Almacenamiento requerido: sistema de archivos (EXT3, ETX4, ReiserFS, XFS); asignador de software y dispositivos de múltiples rutas (múltiples rutas); administrador de volúmenes: (LVM2). No se admiten servidores físicos con almacenamiento de controlador HP CCISS. El sistema de archivos ReiserFS solo se admite en SUSE Linux Enterprise Server 11 SP3.
**Servidores físicos** | Cualquier carga de trabajo que se ejecuta en Windows | Las tablas de Azure | Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 con SP1 al menos
**Servidores físicos** | Cualquier carga de trabajo que se ejecuta en Linux | Las tablas de Azure | Red Hat Enterprise Linux 6.7,7.1,7.2  <br/><br/> CentOS 6.5, 6.6, 6.7, 7.0, 7.1 y 7.2 <br/><br/> Oracle Enterprise Linux 6.4 y 6.5, en ejecución en el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/>  Almacenamiento requerido: sistema de archivos (EXT3, ETX4, ReiserFS, XFS); asignador de software y dispositivos de múltiples rutas (múltiples rutas); administrador de volúmenes: (LVM2). No se admiten servidores físicos con almacenamiento de controlador HP CCISS. El sistema de archivos ReiserFS solo se admite en SUSE Linux Enterprise Server 11 SP3.
**Servidores físicos** | Cualquier carga de trabajo que se ejecuta en Windows | Sitio secundario | Windows Server 2012 R2 de 64 bits, Windows Server 2012, Windows Server 2008 con SP1 al menos
**Servidores físicos** | Cualquier carga de trabajo que se ejecuta en Linux | Sitio secundario | Red Hat Enterprise Linux 6.7,7.1,7.2  <br/><br/> CentOS 6.5, 6.6, 6.7, 7.0, 7.1 y 7.2 <br/><br/> Oracle Enterprise Linux 6.4 y 6.5, en ejecución en el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/>  Almacenamiento requerido: sistema de archivos (EXT3, ETX4, ReiserFS, XFS); asignador de software y dispositivos de múltiples rutas (múltiples rutas); administrador de volúmenes: (LVM2). No se admiten servidores físicos con almacenamiento de controlador HP CCISS. El sistema de archivos ReiserFS solo se admite en SUSE Linux Enterprise Server 11 SP3.


## <a name="provider-versions"></a>Versiones de proveedor

**Name** | **Descripción** | **La versión más reciente** | **Soporte técnico** | **Detalles**
---|---|---|---| ---
**Proveedor de Azure Site Recovery** | Coordina las comunicaciones entre los servidores locales y el sitio secundario o de Azure  <br/><br/>  Se instala en servidores VMM locales o en servidores de Hyper-V si no hay ningún servidor VMM | 5.1.1700 (disponible en el portal) | [Características y correcciones más recientes](https://support.microsoft.com/kb/3155002)
**Configuración unificada de Azure Site Recovery (VMware a Azure)** | Coordina las comunicaciones entre servidores de VMware locales y Azure  <br/><br/>  Se instala en servidores de VMware locales | 9.3.4246.1 (disponible en el portal) | [Características y correcciones más recientes](https://support.microsoft.com/kb/3155002)
**Servicio de movilidad** | Coordina la replicación entre servidores de VMware locales o servidores físicos y el sitio secundario o Azure | NA (disponible en el portal) | Se instala en cada máquina virtual de VMware o servidor físico que se va a replicar. **Agente de Microsoft Azure Recovery Services (MARS)** | Coordina la replicación entre máquinas virtuales de Hyper-V y Azure<br/><br/>  Se instala en servidores de Hyper-V locales (con o sin un servidor VMM) | 2.0.8689.0 (disponible en el portal) | Azure Site Recovery y Azure Backup usan este agente. [Más información] (https://support.microsoft.com/en-us/kb/2997692)

## <a name="next-steps"></a>Pasos siguientes

[Preparación de la implementación](site-recovery-best-practices.md)




<!--HONumber=Oct16_HO2-->


