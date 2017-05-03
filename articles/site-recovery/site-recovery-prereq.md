---
title: "Requisitos previos para replicación en Azure con Azure Site Recovery | Microsoft Docs"
description: "En este artículo se resumen los requisitos previos para replicar máquinas virtuales y máquinas físicas en Azure con el servicio Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/27/2017
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 5ff598af73b6be727753ecac5b99f28bae19a417
ms.lasthandoff: 04/17/2017

---

#  <a name="prerequisites-for-replication-to-azure-by-using-azure-site-recovery"></a>Requisitos previos para la replicación en Azure con Azure Site Recovery


El servicio Azure Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres (BCDR) al organizar la replicación de servidores físicos y máquinas virtuales locales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, podrá realizar la conmutación por error a una ubicación secundaria para mantener disponibles las aplicaciones y cargas de trabajo. Puede realizar la conmutación por recuperación a la ubicación principal cuando vuelva a su funcionamiento normal. Consulte [¿Qué es Site Recovery?](site-recovery-overview.md) para más información sobre Site Recovery.

En este artículo se resumen los requisitos previos necesarios para comenzar la replicación de Site Recovery en Azure.

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="azure-requirements"></a>Requisitos de Azure

**Requisito** | **Detalles**
--- | ---
**Cuenta de Azure** | Una cuenta de [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Servicio Site Recovery** | Para más información sobre los precios de Site Recovery, consulte los [precios de Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/). |
**Almacenamiento de Azure** | Necesita una cuenta de almacenamiento de Azure para almacenar datos replicados; esta debe estar en la misma región que el almacén de Recovery Services. Los datos replicados se almacenan en almacenamiento de Azure y las máquinas virtuales de Azure se crean cuando se produce la conmutación por error.<br/><br/> Según el modelo de recursos que desee usar para las máquinas virtuales de Azure conmutadas por error, puede configurar una cuenta en el [modelo de Azure Resource Manager](../storage/storage-create-storage-account.md) o en el [clásico](../storage/storage-create-storage-account-classic-portal.md).<br/><br/>Puede usar [almacenamiento con redundancia geográfica](../storage/storage-redundancy.md#geo-redundant-storage) o almacenamiento con redundancia local. Se recomienda el almacenamiento con redundancia geográfica para que los datos sean resistentes si se produce una interrupción regional o si no se puede recuperar la región primaria.<br/><br/> Puede usar los niveles de almacenamiento Estándar o Premium. [Premium Storage](https://docs.microsoft.com/azure/storage/storage-premium-storage) se usa normalmente para las máquinas virtuales que necesitan un alto rendimiento constante de E/S y latencia baja para hospedar cargas de trabajo intensivas de E/S. Si usa Premium Storage para los datos replicados, necesitará también una cuenta de almacenamiento estándar para almacenar los registros de replicación que capturan los cambios continuos en los datos locales.<br/><br/>
**Limitaciones de almacenamiento** | No se pueden mover cuentas de almacenamiento usadas en Site Recovery entre grupos de recursos, ni tampoco dentro de la misma suscripción o entre distintas.<br/><br/> La replicación no se admite en las cuentas de Premium Storage en las regiones Centro de la India e India del Sur.
**Red de Azure** | Necesita una red de Azure a la que las máquinas virtuales de Azure se conecten después de la conmutación por error y esta debe encontrarse en la misma región que el almacén de Recovery Services.<br/><br/> En Azure Portal, puede crear redes con el [modelo de Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o el [clásico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).<br/><br/> Si realiza la replicación desde System Center Virtual Machine Manager en Azure, configurará la asignación de red entre redes de máquinas virtuales de Virtual Machine Manager y redes de Azure para asegurarse de que las máquinas virtuales de Azure se conecten a las redes adecuadas después de la conmutación por error.
**Limitaciones de la red** | No se pueden mover cuentas de red usadas en Site Recovery entre grupos de recursos, ni tampoco dentro de la misma suscripción o entre distintas.
**Asignación de red** | Si replica máquinas virtuales de Hyper-V en nubes de Virtual Machine Manager, es preciso que configure la asignación de red, de forma que las máquinas virtuales de Azure se conecten a las redes apropiadas cuando se creen después de la conmutación por error.

>[!NOTE]
>En las siguientes secciones se describen los requisitos previos para los distintos componentes del entorno del cliente. Para más información sobre la compatibilidad con configuraciones específicas, lea la [matriz de compatibilidad](site-recovery-support-matrix.md).
>

## <a name="disaster-recovery-of-vmware-virtual-machines-or-physical-windows-or-linux-servers-to-azure"></a>Recuperación ante desastres de máquinas virtuales de VMware o servidores físicos de Windows o Linux en Azure
A continuación, se muestran los componentes necesarios para la recuperación ante desastres de máquinas virtuales de VMware o servidores físicos de Windows o Linux, además de los mencionados en [Requisitos de Azure](#Azure requirements).


### <a name="configuration-server-or-additional-process-server-you-will-need-to-set-up-an-on-premises-machine-as-the-configuration-server-to-coordinate-communications-between-the-on-premises-site-and-azure-and-to-manage-data-replication-brbr"></a>**Servidor de configuración o servidores de procesos adicionales**: deberá configurar una máquina local como el servidor de configuración para coordinar las comunicaciones entre el sitio local y Azure, y para administrar la replicación de datos. <br></br>

1. **Host de VMware vCenter o vSphere**

| **Componente** | **Requisitos** |
| --- | --- |
| **vSphere** | Uno o varios hipervisores de VMware vSphere.<br/><br/>Los hipervisores deben ejecutar la versión 6.0, 5.5 o 5.1 de vSphere con las últimas actualizaciones.<br/><br/>Se recomienda que los hosts de vSphere y los servidores vCenter se encuentren en la misma red que el servidor de procesos. Se trata de la red en la que se encuentra el servidor de configuración, a menos que haya configurado un servidor de procesos dedicado. |
| **vCenter** | Se recomienda implementar un servidor VMware vCenter para administrar los hosts vSphere. Debe ejecutar vCenter versión 6.0 o 5.5 con las actualizaciones más recientes.<br/><br/>**Limitación**: Site Recovery no admite vMotion de vCenter. Storage DRS y Storage vMotion tampoco se admiten en máquinas virtuales de destino maestras después de una operación para volver a proteger.||

1. **Requisitos previos de máquinas replicadas**


| **Componente** | **Requisitos** |
| --- | --- |
| **Locales** (máquinas virtuales de VMware) | Las máquinas virtuales replicadas deben tener las herramientas de VMware instaladas y en ejecución.<br/><br/> Las máquinas virtuales deben cumplir los [requisitos previos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) para crear máquinas virtuales de Azure.<br/><br/>La capacidad de disco individual en máquinas protegidas no debe ser superior a 1023 GB. <br/><br/>Se necesitan como mínimo 2 GB de espacio disponible en la unidad de instalación para instalar los componentes.<br/><br/>El puerto 20004 se debe abrir en el firewall local de la máquina virtual si desea habilitar la coherencia de múltiples máquinas virtuales.<br/><br/>Los nombres de las máquinas deben tener entre 1 y 63 caracteres (letras, números y guiones). El nombre debe comenzar con una letra o un número y terminar con una letra o un número. Después de haber habilitado la replicación de una máquina, puede modificar el nombre de Azure.<br/><br/> |
| **Máquinas Windows** (físicas o de VMware) | El equipo debe ejecutar un sistema operativo de 64 bits compatible: Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 con SP1 como mínimo.<br/><br/> El sistema operativo debe instalarse en la unidad C. El disco del sistema operativo debe ser un disco básico de Windows y no ser dinámico. El disco de datos puede ser dinámico.<br/><br/>|
| **Equipos con Linux** (físicos o VMware) | Necesita un sistema operativo de 64 bits compatible: Red Hat Enterprise Linux 6.7, 6.8, 7.1 o 7.2; Centos 6.5, 6.6, 6.7, 6.8, 7.0, 7.1 o 7.2; Oracle Enterprise Linux 6.4 o 6.5 con un kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3), SUSE Linux Enterprise Server 11 SP3 y SUSE Linux Enterprise Server 11 SP4.<br/><br/>Los archivos /etc/hosts de las máquinas protegidas deben contener entradas que asignen el nombre de host local a direcciones IP asociadas con todos los adaptadores de red.<br/><br/>Si desea conectarse a la máquina virtual de Azure con Linux después de la conmutación por error mediante un cliente Secure Shell (ssh), asegúrese de que el servicio de Secure Shell en la máquina protegida esté configurado para iniciarse automáticamente con el arranque del sistema y que las reglas de firewall permitan un conexión ssh con ella.<br/><br/>El nombre del host, los puntos de montaje, los nombres de dispositivo, las rutas de acceso de sistema de Linux y los nombres de archivo (por ejemplo, /etc/, /usr) deben estar en inglés únicamente.<br/><br/>Todos los directorios siguientes (si se configuran como sistemas de archivos o particiones independientes) deben ubicarse en el mismo disco (el disco de sistema operativo) del servidor de origen: / (root), /boot, /usr, /usr/local, /var, /etc.<br/><br/>ASR no admite las características de XFS v5, como la suma de comprobación de metadatos, en sistemas de archivos XFS. Asegúrese de que los sistemas de archivos XFS no usan ninguna característica de v5. Puede usar la utilidad xfs_info para comprobar el superbloque XFS para la partición. Si ftype está establecido en 1, entonces se usan las características de XFSv5.<br/><br/>En servidores Red Hat Enterprise Linux 7 y CentOS 7, la utilidad lsof debe estar instalada y disponible.<br/><br/>


## <a name="disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-virtual-machine-manager"></a>Recuperación ante desastres de máquinas virtuales de Hyper-V en Azure (sin Virtual Machine Manager)

A continuación, se muestran los componentes necesarios para la recuperación ante desastres de máquinas virtuales de Hyper-V en nubes Virtual Machine Manager, además de los mencionados en [Requisitos de Azure](#Azure requirements).

| **Requisito previo** | **Detalles** |
| --- | --- |
| **Host de Hyper-V** |Uno o varios servidores locales que ejecuten Windows Server 2012 R2 con las últimas actualizaciones y el rol Hyper-V habilitado, o bien Microsoft Hyper-V Server 2012 R2.<br/><br/>Los servidores de Hyper-V deben contener una o más máquinas virtuales.<br/><br/>Los servidores de Hyper-V deben estar conectados a Internet, directamente o a través de un proxy.<br/><br/>Los servidores de Hyper-V deben tener correcciones mencionadas en la actualización [KB2961977](https://support.microsoft.com/kb/2961977) instalada.
|**Proveedor y agente**| Durante la implementación de Azure Site Recovery, se instala Proveedor de Azure Site Recovery. Con la instalación del proveedor, también se instalará Agente de Azure Recovery Services en cada servidor de Hyper-V donde se ejecuten máquinas virtuales que desee proteger. <br/><br/>Todos los servidores de Hyper-V de un almacén de Site Recovery deben tener las mismas versiones del proveedor y del agente.<br/><br/>El proveedor necesitará conectarse a Azure Site Recovery a través de Internet. El tráfico puede enviarse directamente o a través de un proxy. No se admite el proxy basado en HTTPS. El servidor proxy debe permitir el acceso a:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>Si tiene reglas de firewall basadas en direcciones IP en el servidor, asegúrese de que permitan la comunicación con Azure.<br/><br/> Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).<br/><br/> Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y los del oeste de EE. UU. (se usan para el control de acceso y la administración de identidades).


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-virtual-machine-manager-clouds-to-azure"></a>Recuperación ante desastres de máquinas virtuales de Hyper-V en nubes Virtual Machine Manager en Azure

A continuación, se muestran los componentes necesarios para la recuperación ante desastres de máquinas virtuales de Hyper-V en nubes Virtual Machine Manager, además de los mencionados en [Requisitos de Azure](#Azure requirements).

| **Requisito previo** | **Detalles** |
| --- | --- |
| **Virtual Machine Manager** |Uno o varios servidores Virtual Machine Manager con **System Center 2012 R2 o posterior**. Cada servidor Virtual Machine Manager debe tener una o más nubes configuradas. <br/><br/>La nube debe contener:<br>- Uno o varios grupos host Virtual Machine Manager.<br/>- Uno o más servidores host de Hyper-V o clústeres en cada grupo de hosts.<br/><br/>Para más información sobre cómo configurar nubes Virtual Machine Manager, consulte [How to create a cloud in VMM 2012](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx) (Cómo crear una nube en VMM 2012). |
| **Hyper-V** |Los servidores host de Hyper-V deben ejecutar al menos Windows Server 2012 R2 con el rol Hyper-V o Microsoft Hyper-V Server 2012 R2 y tener instaladas las actualizaciones más recientes.<br/><br/> Un servidor de Hyper-V debe contener una o varias máquinas virtuales.<br/><br/> Un servidor host o clúster de Hyper-V que incluya las máquinas virtuales que desea replicar se debe administrar en una nube Virtual Machine Manager.<br/><br/>Los servidores de Hyper-V deben estar conectados a Internet, directamente o a través de un proxy.<br/><br/>Los servidores de Hyper-V deben tener instaladas las correcciones que se mencionan en el artículo [2961977](https://support.microsoft.com/kb/2961977).<br/><br/>Los servidores host de Hyper-V necesitan acceso a Internet para la replicación de datos en Azure. |
| **Proveedor y agente** |Durante la implementación de Azure Site Recovery, instale Proveedor de Azure Site Recovery en el servidor Virtual Machine Manager y Agente de Recovery Services en hosts de Hyper-V. El proveedor y el agente deben conectarse a Azure por Internet, directamente o a través de un proxy. No se admite un proxy basado en HTTPS. El servidor proxy del servidor Virtual Machine Manager y los hosts de Hyper-V deben permitir el acceso a: <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Si tiene reglas de firewall basadas en direcciones IP en el servidor Virtual Machine Manager, asegúrese de que permitan la comunicación con Azure.<br/><br/> Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).<br/><br/>Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y los del oeste de EE. UU. (se usan para el control de acceso y la administración de identidades).<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>Requisitos previos de máquinas replicadas
| **Componente** | **Detalles** |
| --- | --- |
| **Máquinas virtuales protegidas** | Site Recovery es compatible con todos los sistemas operativos admitidos por [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).<br/><br/>Las máquinas virtuales deben cumplir los [requisitos previos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) para crear máquinas virtuales de Azure. Los nombres de las máquinas deben tener entre 1 y 63 caracteres (letras, números y guiones). El nombre debe comenzar con una letra o un número y terminar con una letra o un número. <br/><br/>Puede modificar el nombre después de habilitar la replicación para la máquina virtual. <br/><br/> La capacidad de disco individual en máquinas protegidas no debe ser superior a 1023 GB. Una máquina virtual puede tener hasta 16 discos (es decir, hasta 16 TB).<br/><br/>


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-virtual-machine-manager-clouds-to-a-customer-owned-site"></a>Recuperación ante desastres de máquinas virtuales de Hyper-V en nubes Virtual Machine Manager en un sitio propiedad del cliente

A continuación, se muestran los componentes necesarios para la recuperación ante desastres de máquinas virtuales de Hyper-V en nubes Virtual Machine Manager en un sitio propiedad del cliente, además de los mencionados en [Requisitos de Azure](#Azure requirements).

| **Componentes** | **Detalles** |
| --- | --- |
| **Virtual Machine Manager** |  Se recomienda implementar un servidor Virtual Machine Manager en el sitio principal y otro en el sitio secundario.<br/><br/> Puede [replicar máquinas virtuales entre nubes en un único servidor VMM](site-recovery-vmm-to-vmm.md#prepare-for-single-server-deployment). Para ello, necesita al menos dos nubes configuradas en el servidor Virtual Machine Manager.<br/><br/> Los servidores Virtual Machine Manager deben estar ejecutando, como mínimo, System Center 2012 SP1 con las actualizaciones más recientes.<br/><br/> Cada servidor Virtual Machine Manager debe tener al menos una o varias nubes. Además, en todas las nubes se debe haber establecido el perfil de capacidad de Hyper-V. <br/><br/>Las nubes deben contener uno o varios grupos host Virtual Machine Manager. Para más información sobre cómo configurar nubes Virtual Machine Manager, consulte [Preparación de la implementación de Azure Site Recovery](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric). |
| **Hyper-V** | Los servidores Hyper-V deben estar ejecutando, como mínimo, Windows Server 2012 con el rol Hyper-V y tener instaladas las actualizaciones más recientes.<br/><br/> Un servidor de Hyper-V debe contener una o varias máquinas virtuales.<br/><br/>  Los servidores host de Hyper-V deben estar ubicados en grupos host de las nubes de VMM principal y secundaria.<br/><br/> Si ejecuta Hyper-V en un clúster con Windows Server 2012 R2, se recomienda instalar la [actualización 2961977](https://support.microsoft.com/kb/2961977).<br/><br/> Si ejecuta Hyper-V en un clúster con Windows Server 2012 y tiene un clúster basado en una dirección IP estática, el agente de clúster no se crea automáticamente. Debe configurarlo manualmente. Para más información sobre el agente de clúster, consulte [Configure replica broker role (cluster to cluster replication)](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx) (Configurar el rol de agente de réplicas [replicación de clúster a clúster]). |
| **Proveedor** | Durante la implementación de Site Recovery, se instala Proveedor de Azure Site Recovery en servidores Virtual Machine Manager. El proveedor se comunica con Site Recovery mediante HTTPS 443 para organizar la replicación. La replicación de datos se produce entre los servidores Hyper-V principal y secundario mediante la conexión LAN o VPN.<br/><br/> El proveedor que se ejecuta en el servidor Virtual Machine Manager necesita acceso a estas URL:<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>El proveedor debe permitir la comunicación del firewall entre los servidores Virtual Machine Manager y los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653), así como el protocolo HTTPS (443). |


## <a name="url-access"></a>Acceso a direcciones URL
Estas direcciones URL deben estar disponibles desde los servidores de host de Hyper-V, VMM y VMware.

|**URL** | **VMM a VMM** | **VMM a Azure** | **Hyper-V en Azure** | **VMware a Azure** |
|--- | --- | --- | --- | --- |
|``*.accesscontrol.windows.net`` | PERMITIR | Permitir | Permitir | Permitir |
|``*.backup.windowsazure.com`` | No se requiere | Permitir | Permitir | Permitir |
|``*.hypervrecoverymanager.windowsazure.com`` | Permitir | Permitir | Permitir | Permitir |
|``*.store.core.windows.net`` | Permitir | Permitir | Permitir | Permitir |
|``*.blob.core.windows.net`` | No se requiere | Permitir | Permitir | Permitir |
|``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | No se requiere | No se requiere | No se requiere | Permitir la descarga SQL |
|``time.windows.com`` | PERMITIR | Permitir | Permitir | Permitir|
|``time.nist.gov`` | Permitir | Permitir | Permitir | PERMITIR |

