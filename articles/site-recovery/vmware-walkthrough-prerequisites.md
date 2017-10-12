---
title: "Requisitos previos de VMware para la replicación en Azure con Azure Site Recovery | Microsoft Docs"
description: "Se resumen los requisitos previos para la replicación de las cargas de trabajo que se ejecutan en máquinas virtuales VMware en Azure con el servicio Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 318156ba-793b-48d0-98d4-cc5436bf28a3
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 7a82e58d9ff9208130c43fcd11d03dcc3238696a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="step-2-review-the-prerequisites-for-vmware-to-azure-replication"></a>Paso 2: Revisión de los requisitos previos de VMware para la replicación en Azure

Lea los requisitos previos que se resumen en la tabla.

**Requisito previo** | **Detalles**
--- | ---
**Las tablas de Azure** | Aprenda acerca de los [requisitos de Azure](site-recovery-prereq.md#azure-requirements)
**Servidor de configuración local** | Necesita una máquina virtual de VMware con Windows Server 2012 R2 o posterior. Configure este servidor durante la implementación de Site Recovery.<br/><br/> De forma predeterminada, el servidor de procesos y el servidor de destino maestro también están instalados en esta máquina virtual. Al escalar una verticalmente, podría necesitar un servidor de procesos independiente, con los mismos requisitos que el servidor de configuración.<br/><br/> Aprenda más sobre estos componentes [aquí](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements).
**Servidores de VMware locales** | Uno o más servidores de VMware vSphere, con 6.5, 6.0, 5.5 o 5.1 con las últimas actualizaciones. Los servidores se deberían encontrar en la misma red que el servidor de configuración (o en un servidor de procesos independiente).<br/><br/> Se recomienda un servidor vCenter para administrar hosts, con 6.5, 6.0 o 5.5 con las últimas actualizaciones.
**Máquinas virtuales locales** | Las máquinas virtuales que desee replicar deben ejecutar un [sistema operativo compatible](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) y cumplir los [requisitos previos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). Las máquinas virtuales deberían ejecutar las herramientas de VMware.
**URLs** | El servidor de configuración necesita acceso a estas direcciones URL:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Si tiene reglas de firewall basadas en direcciones IP, asegúrese de que permitan la comunicación con Azure.<br/></br> Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).<br/></br> Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU. (se usan para Access Control y para Identity Management).<br/><br/> Permita esta dirección URL para la descarga de MySQL: http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
**Servicio de movilidad** | Instalado en cada máquina virtual replicada.




## <a name="limitations"></a>Limitaciones

Asegúrese de que comprende las limitaciones que se resumen en la tabla antes de realizar la implementación.

**Limitación** | **Detalles**
--- | ---
**Las tablas de Azure** | Las cuentas de almacenamiento y de red deben estar en la misma región que el almacén.<br/><br/> Si usa una cuenta de Premium Storage, también necesita una cuenta de almacenamiento estándar para almacenar los registros de replicación.<br/><br/> No se puede replicar en cuentas premium en el centro y sur de la India.
**Servidor de configuración local** | El tipo de adaptador de máquina virtual de VMware debe ser VMXNET3. Si no lo es, [instale esta actualización](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1).<br/><br/> Se debe instalar vSphere PowerCLI 6.0.<br/><br> La máquina no debe ser un controlador de dominio. La máquina debe tener una dirección IP estática.<br/><br/> El nombre de host debe tener 15 caracteres o menos, y el sistema operativo debe estar en inglés.
**VMware** | Site Recovery no admite las nuevas características de vCenter y vSphere 6.5 y 6.0, como Cross vCenter vMotion, volúmenes virtuales y DRS de almacenamiento.
**VM** | Compruebe las [limitaciones de máquinas virtuales de Azure](site-recovery-prereq.md#azure-requirements).<br/><br/> No se pueden replicar máquinas virtuales con discos cifrados ni tampoco con arranque UEFI/EFI.<br/><br> No se admiten los clústeres de disco compartido. Si la máquina virtual de origen tiene formación de equipos NIC, se convierte en una sola NIC después de la conmutación por error.<br/><br/> Si las máquinas virtuales tienen un disco iSCSI, Site Recovery lo convierte en un archivo de disco duro virtual después de la conmutación por error. Si la máquina virtual de Azure puede acceder al destino iSCSI, se conecta a él y ve tanto el destino iSCSI como el disco duro virtual. Si sucede esto, desconecte el destino iSCSI.<br/><br/> Si desea habilitar la coherencia de múltiples máquinas virtuales, lo cual permite que las máquinas virtuales que ejecuten la misma carga de trabajo se recuperen juntas en un punto de datos coherente, abra el puerto 20004 en la máquina virtual.<br/><br/> Windows debe instalarse en la unidad C. El disco del sistema operativo debe ser básico y no dinámico. El disco de datos puede ser dinámico.<br/><br/> Los archivos /etc/hosts de Linux de las máquinas virtuales deben contener entradas que asignen el nombre de host local a direcciones IP asociadas con todos los adaptadores de red. El nombre del host, los puntos de montaje, los nombres de dispositivo, las rutas de acceso de sistema y los nombres de archivo (/etc/, /usr) deben estar en inglés únicamente.<br/><br/> Se admiten tipos específicos de [almacenamiento Linux](site-recovery-support-matrix-to-azure.md#support-for-storage).<br/><br/>Cree o establezca **disk.enableUUID=true** en la configuración de máquina virtual. Esto proporciona un UUID coherente para el VMDK, para que se monte correctamente, y se asegura de que solamente se transfieran los cambios delta de vuelta a la máquina local durante la conmutación por recuperación, sin replicación completa.


## <a name="next-steps"></a>Pasos siguientes

- Si va a realizar una implementación completa, vaya al [Paso 3: Planeamiento de la capacidad](vmware-walkthrough-capacity.md).
- Si va a realizar una implementación de prueba sencilla, vaya al [Paso 4: Planeamiento de las redes](vmware-walkthrough-network.md).
