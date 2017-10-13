---
title: "Requisitos previos para replicación en Azure con Azure Site Recovery | Microsoft Docs"
description: "Obtenga información sobre los requisitos previos para replicar máquinas virtuales y físicas en Azure con el servicio Azure Site Recovery."
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
ms.date: 06/23/2017
ms.author: rajanaki
ms.openlocfilehash: 490833c14b6856cdaf6f6bfd2f67ce54fb0414a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
#  <a name="prerequisites-for-replication-from-on-premises-to-azure-by-using-site-recovery"></a>Requisitos previos para replicar máquinas del entorno local a Azure con Azure Site Recovery

> [!div class="op_single_selector"]
> * [Replicación de Azure a Azure](site-recovery-azure-to-azure-prereq.md)
> * [Replicación del entorno local a Azure](site-recovery-prereq.md)

Azure Site Recovery puede respaldar cualquier estrategia de continuidad de negocio y de recuperación ante desastres (BCDR) mediante la coordinación de la replicación de máquinas virtuales de Azure a otra región de Azure. Este servicio también replica servidores físicos locales y máquinas virtuales a la nube (Azure) o a un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, podrá realizar la conmutación por error a una ubicación secundaria con el fin de mantener disponibles las aplicaciones y cargas de trabajo. Podrá llevar a cabo la conmutación por recuperación a la ubicación principal cuando vuelva a su funcionamiento normal. Para obtener más información sobre Site Recovery, consulte [¿Qué es Site Recovery?](site-recovery-overview.md)

En este artículo se resumen los requisitos previos para iniciar la replicación de Site Recovery de una máquina local a Azure.

Puede publicar comentarios en la parte inferior de este artículo, así como obtener respuestas a preguntas técnicas en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="azure-requirements"></a>Requisitos de Azure

**Requisito** | **Detalles**
--- | ---
**Cuenta de Azure** | Una [cuenta de Microsoft Azure](http://azure.microsoft.com/). Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
**Servicio Site Recovery** | Para obtener más información sobre los precios del servicio Azure Site Recovery, consulte [Precios de Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/). |
**Almacenamiento de Azure** | Necesitará una cuenta de Azure Storage para almacenar los datos replicados. La cuenta de almacenamiento debe estar en la misma región que el almacén de Azure Recovery Services. Las máquinas virtuales de Azure se crean cuando se realiza la conmutación por error.<br/><br/> Según el modelo de recursos que quiera usar para la conmutación por error de las máquinas virtuales de Azure, puede configurar una cuenta en el [modelo de implementación de Azure Resource Manager](../storage/common/storage-create-storage-account.md) o en el de [implementación clásica](../storage/common/storage-create-storage-account.md).<br/><br/>Puede usar [almacenamiento con redundancia geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage) o almacenamiento con redundancia local. Se recomienda el almacenamiento con redundancia geográfica. Gracias a él, los datos resisten en caso de una interrupción regional o si no se puede recuperar la región primaria.<br/><br/> Puede usar una cuenta de Azure Storage estándar o Azure Premium Storage. Normalmente, [Premium Storage](https://docs.microsoft.com/azure/storage/storage-premium-storage) se usa para las máquinas virtuales que necesitan un alto volumen constante de E/S y una latencia baja. Con este servicio, una máquina virtual puede hospedar cargas de trabajo intensivas de E/S. Si utiliza Premium Storage para los datos replicados, también necesitará una cuenta de almacenamiento estándar para guardar los registros de replicación que capturan los cambios continuos en los datos locales.<br/><br/>
**Limitaciones de almacenamiento** | No se pueden mover las cuentas de almacenamiento utilizadas en Site Recovery a otro grupo de recursos, ni tampoco a otra suscripción.<br/><br/> En estos momentos, no es posible realizar la replicación a cuentas de Premium Storage en las regiones India central e India del Sur.
**Red de Azure** | Se necesita una red de Azure a la que se conectarán las máquinas virtuales de Azure después de la conmutación por error. La red de Azure debe estar en la misma región que el almacén de Recovery Services.<br/><br/> En Azure Portal, puede crear una red de Azure mediante el [modelo de implementación de Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o el de [implementación clásica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).<br/><br/> Si la replicación se realiza de System Center Virtual Machine Manager (VMM) a Azure, hay que configurar la asignación de red entre redes de Azure y de máquinas virtuales de VMM. Esto garantiza que las máquinas virtuales de Azure puedan conectarse a las redes adecuadas después de la conmutación por error.
**Limitaciones de la red** | No se pueden mover las cuentas de red utilizadas en Site Recovery a otro grupo de recursos, ni tampoco a otra suscripción.
**Asignación de red** | Si se replican máquinas virtuales Microsoft Hyper-V en nubes VMM, hay que configurar la asignación de red. Esto garantiza que las máquinas virtuales de Azure puedan conectarse a las redes adecuadas (cuando se creen) después de la conmutación por error.

> [!NOTE]
> En las siguientes secciones se describen los requisitos previos de los distintos componentes del entorno del cliente. Para obtener más información sobre la compatibilidad con configuraciones específicas, consulte la [matriz de compatibilidad](site-recovery-support-matrix.md).
>

## <a name="disaster-recovery-of-vmware-vms-or-physical-windows-or-linux-servers-to-azure"></a>Recuperación ante desastres de máquinas virtuales VMware o de servidores físicos Windows o Linux en Azure
Se requieren los siguientes componentes para llevar a cabo la recuperación ante desastres de máquinas virtuales VMware, o bien de servidores físicos Windows o Linux. También se necesitan los que se describen en los [requisitos de Azure](#azure-requirements).


### <a name="configuration-server-or-additional-process-server"></a>Servidor de configuración o de proceso adicional

Configure una máquina local como el servidor de configuración para coordinar la comunicación entre el sitio local y Azure. La siguiente tabla muestra los requisitos del sistema y de software de una máquina virtual que se puede configurar como un servidor de configuración.

> [!IMPORTANT]
> Al implementar un servidor de configuración para la protección de máquinas virtuales de VMware, se recomienda implementarlo como una máquina virtual con **alta disponibilidad (HA)**.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

### <a name="vmware-vcenter-or-vsphere-host-prerequisites"></a>Requisitos previos de hosts de VMware vCenter o VMware vSphere

| **Componente** | **Requisitos** |
| --- | --- |
| **vSphere** | Se requieren uno o varios hipervisores de VMware vSphere.<br/><br/>Los hipervisores deben ejecutar la versión 6.0, 5.5 o 5.1 de vSphere con las últimas actualizaciones.<br/><br/>Se recomienda que los hosts de vSphere y los servidores vCenter se encuentren en la misma red que el servidor de proceso. Será la red en la que se encuentra el servidor de configuración, a menos que se haya configurado un servidor de proceso dedicado. |
| **vCenter** | Se recomienda implementar un servidor VMware vCenter para administrar los hosts vSphere. Debe ejecutar la versión 6.0 o 5.5 de vCenter con las últimas actualizaciones.<br/><br/>**Limitación**: Site Recovery no admite la replicación entre instancias de vMotion de vCenter. Storage DRS y Storage vMotion tampoco se admiten en máquinas virtuales de destino maestras después de una operación de reprotección.|

### <a name="replicated-machine-prerequisites"></a>Requisitos previos de máquinas replicadas

| **Componente** | **Requisitos** |
| --- | --- |
| **Máquinas locales** (máquinas virtuales VMware) | Las máquinas virtuales replicadas deben tener las herramientas de VMware instaladas y en ejecución.<br/><br/> Las máquinas virtuales deben cumplir los [requisitos previos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) para poder crear una máquina virtual de Azure.<br/><br/>La capacidad de disco de cada máquina protegida no puede ser superior a 1023 GB. <br/><br/>Se necesitan como mínimo 2 GB de espacio disponible en la unidad de instalación para instalar los componentes.<br/><br/>El puerto 20004 debe estar abierto en el firewall local de la máquina virtual si quiere habilitar la funcionalidad de coherencia de varias máquinas virtuales.<br/><br/>Los nombres de las máquinas deben tener entre 1 y 63 caracteres (se pueden usar letras, números y guiones). El nombre debe comenzar y terminar con una letra o un número. <br/><br/>Después de haber habilitado la replicación de una máquina, puede modificar el nombre de Azure.<br/><br/> |
| **Máquinas Windows** (físicas o de VMware) | La máquina debe ejecutar uno de los siguientes sistemas operativos de 64 bits compatibles: <br/>- Windows Server 2012 R2<br/>- Windows Server 2012<br/>- Windows Server 2008 R2 con SP1 o una versión posterior<br/><br/> El sistema operativo debe instalarse en la unidad C. El disco del sistema operativo debe ser un disco básico de Windows y no uno dinámico. El disco de datos puede ser dinámico.<br/><br/>|
| **Equipos con Linux** (físicos o VMware) | La máquina debe ejecutar uno de los siguientes sistemas operativos de 64 bits compatibles: <br/>- Red Hat Enterprise Linux 5.2 a 5.11, 6.1 a 6.9 y 7.0 a 7.3<br/>- CentOS 5.2 a 5.11, 6.1 a 6.9 y 7.0 a 7.3<br/>- Servidor Ubuntu 14.04 LTS (para ver una lista de versiones de kernel compatibles con Ubuntu, consulte los [sistemas operativos que se admiten](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)).<br/>- Servidor Ubuntu 16.04 LTS (para ver una lista de versiones de kernel compatibles con Ubuntu, consulte los [sistemas operativos que se admiten](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)).<br/>- Debian 7 o Debian 8<br/>- Oracle Enterprise Linux 6.5 o 6.4 con Unbreakable Enterprise Kernel Release 3 (UEK3) o el kernel compatible con Red Hat.<br/>- SUSE Linux Enterprise Server 11 SP4 o SUSE Linux Enterprise Server 11 SP3.<br/><br/>Los archivos /etc/hosts de las máquinas protegidas deben tener entradas que asignen el nombre de host local a las direcciones IP asociadas con todos los adaptadores de red.<br/><br/>Después de realizar la conmutación por error, si quiere conectarse a una máquina virtual de Azure que ejecuta Linux mediante el uso de un cliente de Secure Shell (SSH), asegúrese de que el servicio SSH de la máquina protegida está configurado para iniciarse automáticamente al arrancar el sistema. Compruebe también que las reglas de firewall permitan una conexión SSH a la máquina protegida.<br/><br/>El nombre de host, los puntos de montaje, los nombres de dispositivo, las rutas de acceso de sistema de Linux y los nombres de archivo (por ejemplo, /etc/ y /usr) deben estar en inglés únicamente.<br/><br/>Todos los directorios siguientes (si se configuran como sistemas de archivos o particiones independientes) deben ubicarse en el mismo disco (el disco del sistema operativo) del servidor de origen:<br/>- / (raíz)<br/>- /boot<br/>- /usr<br/>- /usr/local<br/>- /var<br/>- /etc<br/><br/>En estos momentos, las características de la versión 5 de XFS, como la suma de comprobación de metadatos, no son compatibles con Site Recovery en sistemas de archivos XFS. Asegúrese de que los sistemas de archivos XFS no usan ninguna característica de la versión 5. Puede usar la utilidad xfs_info para comprobar el superbloque XFS para la partición. Si **ftype** está establecido en **1**, se usarán las características de la versión 5 de XFS.<br/><br/>En servidores Red Hat Enterprise Linux 7 y CentOS 7, la utilidad lsof debe estar instalada y disponible.<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-to-azure-no-vmm"></a>Recuperación ante desastres de máquinas virtuales Hyper-V en Azure (sin VMM)

Se requieren los siguientes componentes para realizar la recuperación ante desastres de máquinas virtuales Hyper-V en nubes VMM. También se necesitan los que se describen en los [requisitos de Azure](#azure-requirements).

| **Requisito previo** | **Detalles** |
| --- | --- |
| **Host de Hyper-V** |Uno o varios servidores locales deben ejecutar Windows Server 2012 R2 con las últimas actualizaciones y el rol de Hyper-V habilitado, o bien Microsoft Hyper-V Server 2012 R2.<br/><br/>Los servidores Hyper-V deben tener una o varias máquinas virtuales.<br/><br/>Los servidores de Hyper-V deben estar conectados a Internet, directamente o a través de un proxy.<br/><br/>Los servidores Hyper-V deben tener instaladas las correcciones que se describen en el artículo de Knowledge Base [2961977](https://support.microsoft.com/kb/2961977).
|**Proveedor y agente**| Durante la implementación de Site Recovery, se instala el proveedor de Azure Site Recovery. También se instalará el agente de Azure Recovery Services en cada servidor Hyper-V que ejecute las máquinas virtuales que quiera proteger. <br/><br/>Todos los servidores Hyper-V de un almacén de Site Recovery deben tener las mismas versiones del proveedor y del agente.<br/><br/>El proveedor necesitará conectarse a Site Recovery a través de Internet. El tráfico puede enviarse directamente o a través de un proxy. No se admite el proxy basado en HTTPS. El servidor proxy debe permitir el acceso a las siguientes direcciones URL:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>Si tiene reglas de firewall basadas en direcciones IP en el servidor, asegúrese de que permitan la comunicación con Azure.<br/><br/> Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y HTTPS (puerto 443).<br/><br/> Permita los intervalos de direcciones IP correspondientes a la región de Azure de la suscripción y los del oeste de EE. UU. (se usan para controlar los accesos y administrar las identidades).


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-azure"></a>Recuperación ante desastres de máquinas virtuales Hyper-V en nubes VMM en Azure

Se requieren los siguientes componentes para realizar la recuperación ante desastres de máquinas virtuales Hyper-V en nubes VMM. También se necesitan los que se describen en los [requisitos de Azure](#azure-requirements).

| **Requisito previo** | **Detalles** |
| --- | --- |
| **Virtual Machine Manager** |Debe tener uno o varios servidores VMM que ejecuten System Center 2012 R2 o una versión posterior. Cada uno de ellos debe tener una o varias nubes configuradas. <br/><br/>Una nube debe tener lo siguiente:<br/>- Uno o más grupos de hosts de VMM.<br/>- Uno o más servidores host de Hyper-V o clústeres en cada grupo de hosts.<br/><br/>Para obtener más información sobre cómo configurar nubes VMM, consulte [How to Create a Cloud in VMM 2012](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx) (Creación de una nube en VMM 2012). |
| **Hyper-V** |Los servidores host de Hyper-V deben ejecutar como mínimo Windows Server 2012 R2 con el rol de Hyper-V habilitado, o bien Microsoft Hyper-V Server 2012 R2. Deben instalarse las últimas actualizaciones.<br/><br/> Un servidor Hyper-V debe tener una o varias máquinas virtuales.<br/><br/> Los servidores host o clústeres de Hyper-V que incluyan las máquinas virtuales que quiere replicar se deben administrar en una nube VMM.<br/><br/>Los servidores de Hyper-V deben estar conectados a Internet, directamente o a través de un proxy.<br/><br/>Los servidores Hyper-V deben tener instaladas las correcciones que se describen en el artículo de Knowledge Base [2961977](https://support.microsoft.com/kb/2961977).<br/><br/>Los servidores host de Hyper-V necesitan acceso a Internet para la replicación de datos en Azure. |
| **Proveedor y agente** |Durante la implementación de Site Recovery, se instala el proveedor de Azure Site Recovery en el servidor VMM. Instale el agente de Recovery Services en servidores host de Hyper-V. El proveedor y el agente deben conectarse a Azure por Internet, directamente o a través de un proxy. No se admite un proxy basado en HTTPS. El servidor proxy del servidor VMM y los hosts de Hyper-V deben permitir el acceso a lo siguiente: <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Si tiene reglas de firewall basadas en direcciones IP en el servidor VMM, asegúrese de que permitan la comunicación con Azure.<br/><br/> Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y HTTPS (puerto 443).<br/><br/>Permita los intervalos de direcciones IP correspondientes a la región de Azure de la suscripción y los del oeste de EE. UU. (se usan para controlar los accesos y administrar las identidades).<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>Requisitos previos de máquinas replicadas

| **Componente** | **Detalles** |
| --- | --- |
| **Máquinas virtuales protegidas** | Site Recovery es compatible con todos los sistemas operativos admitidos por [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).<br/><br/>Las máquinas virtuales deben cumplir los [requisitos previos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) para poder crear una máquina virtual de Azure. Los nombres de las máquinas deben tener entre 1 y 63 caracteres (se pueden usar letras, números y guiones). El nombre debe comenzar y terminar con una letra o un número. <br/><br/>Puede modificar el nombre de la máquina virtual después de habilitar la replicación para la máquina virtual. <br/><br/> La capacidad de disco de cada máquina protegida no puede ser superior a 1023 GB. Una máquina virtual puede tener hasta 16 discos (un máximo de 16 TB).<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-a-customer-owned-site"></a>Recuperación ante desastres de máquinas virtuales Hyper-V en nubes VMM en un sitio propiedad del cliente

Se requieren los siguientes componentes para realizar la recuperación ante desastres de máquinas virtuales Hyper-V en nubes VMM en un sitio propiedad del cliente. También se necesitan los que se describen en los [requisitos de Azure](#azure-requirements).

| **Componente** | **Detalles** |
| --- | --- |
| **Virtual Machine Manager** |  Se recomienda implementar un servidor VMM en el sitio principal y en el secundario.<br/><br/> Puede [replicar máquinas virtuales entre nubes en un único servidor VMM](site-recovery-vmm-to-vmm.md#prepare-for-single-server-deployment). Para realizar la replicación entre nubes de un mismo servidor VMM, necesitará, al menos, dos nubes configuradas en el servidor VMM.<br/><br/> Los servidores VMM deben ejecutar, como mínimo, System Center 2012 SP1 con las últimas actualizaciones.<br/><br/> Todos los servidores VMM deben tener una o varias nubes. Además, en todas las nubes se debe haber establecido el perfil de capacidad de Hyper-V. <br/><br/>Las nubes deben incluir uno o varios grupos de hosts de VMM. Para obtener más información sobre cómo configurar nubes VMM, consulte [Preparación de la implementación de Azure Site Recovery](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric). |
| **Hyper-V** | Los servidores Hyper-V deben ejecutar, como mínimo, Windows Server 2012 con el rol de Hyper-V habilitado y tener instaladas las últimas actualizaciones.<br/><br/> Un servidor Hyper-V debe tener una o varias máquinas virtuales.<br/><br/>  Los servidores host de Hyper-V deben estar ubicados en grupos host de las nubes de VMM principal y secundaria.<br/><br/> Si ejecuta Hyper-V en un clúster que se encuentra en Windows Server 2012 R2, se recomienda instalar la actualización descrita en el artículo de Knowledge Base [2961977](https://support.microsoft.com/kb/2961977).<br/><br/> Si ejecuta Hyper-V en un clúster de Windows Server 2012 y tiene un clúster basado en una dirección IP estática, el agente de clúster no se creará automáticamente; tendrá que configurarlo manualmente. Para obtener más información sobre el agente de clúster, consulte [Configure replica broker role (cluster to cluster replication)](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx) (Configuración del rol de agente de réplicas [replicación de clúster a clúster]). |
| **Proveedor** | Durante la implementación de Site Recovery, se instala el proveedor de Azure Site Recovery en los servidores VMM. El proveedor se comunica con Site Recovery mediante HTTPS 443 para organizar la replicación. La replicación de datos se produce entre los servidores Hyper-V principal y secundario mediante la conexión LAN o VPN.<br/><br/> El proveedor que se ejecuta en el servidor VMM necesita tener acceso a estas direcciones URL:<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>El proveedor de Site Recovery debe permitir la comunicación del firewall entre los servidores VMM los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653), así como el protocolo HTTPS (puerto 443). |


## <a name="url-access"></a>Acceso a direcciones URL
Estas direcciones URL deben estar disponibles desde los servidores host de Hyper-V, VMM y VMware:

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
