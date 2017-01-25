---
title: "Requisitos previos de implementación de Azure Site Recovery | Microsoft Docs"
description: "En este artículo se describen los requisitos previos para configurar la replicación con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/04/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 92abadf68e4acbd9daae9e15dcfdce53563f6eb2
ms.openlocfilehash: a407f5ebe096db74820ff93354a636bc63894b1d

---

# <a name="site-recovery-deployment-prerequisites"></a>Requisitos previos de implementación de Site Recovery

Las organizaciones necesitan una estrategia de recuperación ante desastres y continuidad empresarial (BCDR) que determine cómo seguirán en funcionamiento y disponibles las aplicaciones, las cargas de trabajo y los datos durante los tiempos de inactividad planeados y no planeados, y cómo recuperar las condiciones de funcionamiento normales lo antes posible. Site Recovery es un servicio de Azure que contribuye a su estrategia de BCDR mediante la coordinación de la replicación de servidores físicos locales y máquinas virtuales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error a la ubicación secundaria para mantener disponibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal. Más información en [¿Qué es Site Recovery?](site-recovery-overview.md)

En este artículo se resumen los requisitos previos de implementación para escenarios de replicación de Site Recovery.  

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="azure-deployment-models"></a>Modelos de implementación de Azure

Azure tiene dos [modelos de implementación](../azure-resource-manager/resource-manager-deployment-model.md) diferentes para crear recursos y trabajar con ellos: el modelo de Azure Resource Manager y el clásico. Azure también tiene dos portales: el [Portal de Azure clásico](https://manage.windowsazure.com/) que admite el modelo de implementación clásico y [Azure Portal](https://ms.portal.azure.com/) que es compatible con ambos modelos de implementación.

Los nuevos escenarios de Site Recovery deben implementarse en [Azure Portal](https://portal.azure.com). En este portal se proporcionan nuevas características y una experiencia de implementación mejorada. Los almacenes pueden mantenerse en el portal clásico, pero no se pueden crear.


## <a name="azure-account-requirements"></a>Requisitos de cuenta de Azure

**Requisito** | **Detalles**
--- | --- 
**Cuenta de Azure** | Una cuenta de [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) sobre los precios de Site Recovery.


## <a name="azure-storage-requirements"></a>Requisitos de almacenamiento de Azure

Los datos replicados se almacenan en almacenamiento de Azure y las máquinas virtuales de Azure se crean cuando se produce la conmutación por error.

**Requisito** | **Detalles**
--- | --- 
[Cuenta de Azure Storage](../storage/storage-introduction.md) | Puede usar [GRS](../storage/storage-redundancy.md#geo-redundant-storage) o almacenamiento LRS.<br/><br/> Se recomienda GRS para que los datos sean resistentes si se produce una interrupción regional o si no se puede recuperar la región principal. [Más información](https://docs.microsoft.com/en-us/azure/storage/storage-redundancy)
**Portal de Azure** | En Azure Portal, puede usar una cuenta de almacenamiento clásica o de almacenamiento de Resource Manager.
**Premium Storage** | [Premium Storage ](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage) se admite si replica servidores físicos o VM de VMware en Azure mediante Azure Portal.<br/><br/> Premium Storage se usa normalmente para las máquinas virtuales que necesitan un alto rendimiento constante de E/S y baja latencia para hospedar cargas de trabajo intensivas de E/S.<br/><br/> Si usa Premium Storage, necesitará también una cuenta de almacenamiento estándar para almacenar los registros de replicación que capturan los cambios continuos en los datos locales.
**Limitaciones de almacenamiento** | El portal clásico solo admite GRS.<br/><br/> Las cuentas de almacenamiento creadas en Azure Portal no pueden moverse entre grupos de recursos.<br/><br/> La replicación no se admite en las cuentas de Premium Storage en las regiones Centro de la India e India del Sur.

## <a name="azure-network-requirements"></a>Requisitos de red de Azure

Se necesita una red de Azure a la que las VM de Azure se conecten cuando se creen después de la conmutación por error.

**Requisito** | **Detalles**
--- | ---
**Región de la red** | La red debe estar en la misma región que el almacén.
**Portal de Azure** | En Azure Portal, puede usar una red de Resource Manager o una red clásica.
**Asignación de red** | Si realiza la replicación desde VMM a Azure, configurará la [asignación de red](site-recovery-network-mapping.md) entre redes de máquinas virtuales VMM y redes de Azure, para asegurarse de que las máquinas virtuales de Azure conectarse a las redes adecuadas después de la conmutación por error.


## <a name="vmware-replication-requirements-to-azure"></a>Requisitos de replicación de VMware (para Azure)

**Componente** | **Requisito**
--- | ---
**Servidor de configuración** | Una máquina virtual o física local que ejecute Windows Server 2012 R2. Todos los componentes de Site Recovery local deben estar instalados en esta máquina.<br/><br/>Para la replicación de máquinas virtuales de VMware, se recomienda implementar el servidor como una máquina virtual de VMware de alta disponibilidad. <br/><br/>Si el servidor es una máquina virtual de VMware, el tipo de adaptador de red debe ser VMXNET3. Si usa otro tipo de adaptador de red, instale una [actualización de VMware](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1) en el servidor de vSphere 5.5.<br/><br/>El servidor debe tener una dirección IP estática.<br/><br/>El servidor no debe ser un controlador de dominio.<br/><br/>El nombre de host del servidor debe contener 15 caracteres o menos.<br/><br/>El sistema operativo deben estar inglés únicamente.<br/><br/> Instale VMware vSphere PowerCLI 6.0. Server.<br/><br/>El servidor de configuración necesita acceso a Internet. Se requiere el acceso de salida de la manera siguiente:<br/><br/>Acceso temporal en HTTP 80 durante la instalación de los componentes de Site Recovery (para descargar MySQL)<br/><br/>Acceso de salida continuo en HTTPS 443 para la administración de replicación<br/><br/>Acceso de salida continuo en HTTPS 9443 para el tráfico de replicación (este puerto se puede modificar)<br/><br/> Permita esta dirección URL para la descarga de MySQL: ``http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi``<br/><br/> El servidor también necesita acceder a [estas direcciones URL](#requirements-for-url-access)
**Servidor de proceso** | Se instala de forma predeterminada en el servidor de configuración.<br/><br/> Actúa como puerta de enlace de replicación. Recibe datos de replicación de máquinas de origen protegidas, los optimiza con almacenamiento en caché, compresión y cifrado, y los envía al Almacenamiento de Azure. También controla la instalación de inserción del Servicio de movilidad en máquinas protegidas y realiza al detección automática de máquinas virtuales de VMware. A medida que crezca la implementación, podrá agregar más servidores de procesos dedicados independientes para controlar mayores volúmenes de tráfico de replicación.
**Servidor de destino principal** | Se instala de forma predeterminada en el servidor de configuración. Controla los datos de replicación durante la conmutación por recuperación desde Azure.
**Hosts de vSphere** | Uno o varios hipervisores de VMware vSphere.<br/><br/>Los hipervisores deben ejecutar la versión 6.0, 5.5 o 5.1 de vSphere con las últimas actualizaciones.<br/><br/>Se recomienda que los hosts de vSphere y los servidores vCenter se encuentren en la misma red que el servidor de procesos (se trata de la red en que está ubicado el servidor de configuración a menos que se haya configurado un servidor de procesos dedicado). 
**Servidores vCenter** | Se recomienda implementar un servidor VMware vCenter para administrar los hosts vSphere. Debe ejecutar vCenter versión 6.0 o 5.5 con las actualizaciones más recientes.<br/><br/>**Limitación**: Site Recovery no admite las nuevas características de vCenter y vSphere 6.0, como Cross vCenter vMotion, volúmenes virtuales y DRS de almacenamiento. La compatibilidad de Site Recovery está limitada a las características que también estaban disponibles en la versión 5.5.

### <a name="vmware-vm-requirements-to-azure"></a>Requisitos de VM de VMware (para Azure)

**Componente** | **Requisito**
--- | ---
**Máquinas virtuales de VMware** | Las máquinas virtuales replicadas deben tener las herramientas de VMware instaladas y en ejecución.<br/><br/> Las máquinas virtuales deben cumplir los [requisitos previos de Azure](site-recovery-support-matrix.md#support-for-azure-vms) para crear máquinas virtuales de Azure.<br/><br/>La capacidad de disco individual en máquinas protegidas no debe ser superior a 1023 GB. Una máquina virtual puede tener hasta 64 discos (es decir, hasta 64 TB). <br/><br/>Mínimo de 2 GB de espacio disponible en la unidad de instalación para la instalación de los componentes.<br/><br/>**Limitación**: no se admite la protección de máquinas virtuales con discos cifrados.<br/><br/>**Limitación**: no se admiten clústeres de invitados de disco compartido.<br/><br/>El **puerto 20004** se debe abrir en el firewall local de la máquina virtual, si desea habilitar la coherencia de varias máquinas virtuales.<br/><br/>No se admiten las máquinas que tienen el arranque de Unified Extensible Firmware Interface (UEFI)/Extensible Firmware Interface (EFI).<br/><br/>Los nombres de las máquinas deben tener entre 1 y 63 caracteres (letras, números y guiones). El nombre debe comenzar con una letra o un número y terminar con una letra o un número. Después de haber habilitado la replicación de una máquina, puede modificar el nombre de Azure.<br/><br/>Si la máquina virtual de origen tiene la formación de equipos NIC, se convierte en una sola NIC después de la conmutación por error a Azure.<br/><br/>Si las máquina virtuales protegidas tienen un disco iSCSI, Site Recovery convierte el disco iSCSI de la máquina virtual protegida en un archivo VHD cuando la máquina virtual conmuta por error a Azure. Si la máquina virtual de Azure puede llegar al destino iSCSI, se conectará con él y, principalmente, verá dos discos: el disco VHD en la máquina virtual de Azure y el disco iSCSI de origen. En este caso deberá desconectar el destino iSCSI que aparece en la máquina virtual de Azure.
**VM que ejecutan Windows** | Las máquinas de Windows deben ejecutar un sistema operativo de 64 bits [admitido](site-recovery-support-matrix.md#support-for-replicated-machines).<br/><br/> El sistema operativo debe instalarse en la unidad C:\.<br/><br/> El disco del sistema operativo debe ser un disco básico de Windows y no dinámico. El disco de datos puede ser dinámico.<br/><br/> Site Recovery admite máquinas virtuales con un disco RDM. Durante la conmutación por recuperación, Site Recovery reutiliza el disco RDM si la máquina virtual de origen y el disco RDM original están disponibles. Si no están disponibles, durante la conmutación por recuperación, Site Recovery creará un nuevo archivo VMDK para cada disco.
**VM que ejecutan Linux** | Las máquinas de Linux deben ejecutar un [sistema operativo admitido](site-recovery-support-matrix.md#support-for-replicated-machines).<br/><br/> Los archivos /etc/hosts de las máquinas protegidas deben contener entradas que asignen el nombre de host local a direcciones IP asociadas con todos los adaptadores de red.<br/><br/> El nombre del host, los puntos de montaje, los nombres de dispositivo, las rutas de acceso de sistema de Linux y los nombres de archivo (por ejemplo, /etc/, /usr) deben estar en inglés únicamente.<br/><br/> El almacenamiento debe ser [compatible](site-recovery-support-matrix.md#support-for-replicated-machines).<br/><br/> Site Recovery admite máquinas virtuales con un disco RDM.  Durante la conmutación por recuperación para Linux, Site Recovery no reutiliza el disco RDM. En cambio, crea un nuevo archivo VMDK para cada disco RDM correspondiente.<br/><br/> Asegúrese de establecer la configuración de disk.enableUUID=true en los parámetros de configuración de la máquina virtual de VMware. Si no existe, cree la entrada. Es necesario a fin de proporcionar un UUID uniforme al VMDK, para que se monte correctamente. Agregar esta configuración también garantiza que los cambios diferenciales solo se transfieran a local durante la conmutación por recuperación, y no una replicación completa.



## <a name="physical-servers-replication-requirements-to-azure"></a>Requisitos de replicación de servidores físicos (para Azure)

**Componente** | **Requisito**
--- | ---
**Servidor de configuración** | Una máquina virtual o física local que ejecute Windows Server 2012 R2. Todos los componentes de Site Recovery local deben estar instalados en esta máquina.<br/><br/> Para la replicación de máquina física, esta puede ser un servidor físico.<br/><br/>  El servidor de configuración necesita acceso a Internet. Se requiere el acceso de salida de la manera siguiente:<br/><br/> Acceso temporal en HTTP 80 durante la instalación de los componentes de Site Recovery (para descargar MySQL)<br/><br/>Acceso de salida continuo en HTTPS 443 para la administración de replicación<br/><br/>Acceso de salida continuo en HTTPS 9443 para el tráfico de replicación (este puerto se puede modificar)<br/><br/> Permita esta dirección URL para la descarga de MySQL: ``http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi``<br/><br/> El servidor también necesita acceder a [estas direcciones URL](#requirements-for-url-access)
**Conmutación por recuperación** | La conmutación por recuperación de Azure siempre se efectúa a máquinas virtuales de VMware, aunque replicara un servidor físico. Si no implementa el servidor de configuración como una máquina virtual de VMware, no será necesario configurar una máquina virtual de VMware para recibir el tráfico de conmutación por recuperación antes de realizar dicha recuperación.

### <a name="physical-machine-requirements-to-azure"></a>Requisitos de máquina física (para Azure)

**Componente** | **Requisito**
--- | ---
**Servidores físicos que ejecutan Windows** | Las máquinas de Windows deben ejecutar un sistema operativo de 64 bits [admitido](site-recovery-support-matrix.md#support-for-replicated-machines).<br/><br/> El sistema operativo debe instalarse en la unidad C:\.<br/><br/> El disco del sistema operativo debe ser un disco básico de Windows y no dinámico. El disco de datos puede ser dinámico.<br/><br/> Site Recovery admite máquinas virtuales con un disco RDM. Durante la conmutación por recuperación, Site Recovery reutiliza el disco RDM si la máquina virtual de origen y el disco RDM original están disponibles. Si no están disponibles, durante la conmutación por recuperación, Site Recovery creará un nuevo archivo VMDK para cada disco.
**Servidores físicos que ejecutan Linux** | Las máquinas de Linux deben ejecutar un [sistema operativo admitido](site-recovery-support-matrix.md#support-for-replicated-machines).<br/><br/> Los archivos /etc/hosts de las máquinas protegidas deben contener entradas que asignen el nombre de host local a direcciones IP asociadas con todos los adaptadores de red.<br/><br/> El nombre del host, los puntos de montaje, los nombres de dispositivo, las rutas de acceso de sistema de Linux y los nombres de archivo (por ejemplo, /etc/, /usr) deben estar en inglés únicamente.<br/><br/> El almacenamiento debe ser [compatible](site-recovery-support-matrix.md#support-for-replicated-machines).

## <a name="hyper-v-replication-requirements-to-azure"></a>Requisitos de replicación de Hyper-V (para Azure)

**Componente** | **Requisito**
--- | ---
**VMM (opcional)** | Si lo desea, puede replicar VM en hosts de Hyper-V administrados en nubes de VMM.<br/><br/> Si no usa VMM, recopilará uno o más clústeres o hosts de Hyper-V en sitios de Hyper-V y configurará la replicación para un sitio.<br/><br/> Si usa VMM, se debe ejecutar en System Center 2012 R2.<br/><br/> Si usa VMM, cada servidor VMM debe tener una o más nubes configuradas. Una nube debe contener uno o varios grupos de host de VMM y uno o varios clústeres o servidores de host de Hyper-V en cada grupo de host.<br/><br/> El servidor VMM debe estar conectado a Internet, con acceso a las [direcciones URL requeridas](#requirements-for-url-access).<br/><br/> Si tiene reglas de firewall basadas en direcciones IP en el servidor VMM, compruebe que las reglas permitan la comunicación con Azure.<br/><br/> Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).<br/><br/> Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU.
**Hosts de Hyper-V** | Los servidores host de Hyper-V deben estar ejecutando al menos **Windows Server 2012 R2** con el rol de Hyper-V y tener instaladas las actualizaciones más recientes de **Microsoft Hyper-V Server 2012 R2**.<br/><br/> Un servidor de Hyper-V debe contener una o varias máquinas virtuales.<br/><br/>Los servidores de Hyper-V deben estar conectados a Internet, directamente o a través de un proxy.<br/><br/>Los servidores de Hyper-V deben tener instaladas las correcciones que se mencionan en el [artículo KB2961977](https://support.microsoft.com/kb/2961977).<br/><br/>Los servidores host de Hyper-V necesitan acceso a Internet para la replicación de datos en Azure, incluido el acceso a [direcciones URL requeridas](#requirements-for-url-access). Además, debe permitir los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).<br/><br/> Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU.<br/><br/> Si hay hosts de Hyper-V en nubes de VMM, asegúrese de instalar la corrección descrita en [KB 2961977](https://support.microsoft.com/en-us/kb/2961977)

### <a name="hyper-v-vm-requirements-to-azure"></a>Requisitos de VM de Hyper-V (para Azure)

**Componente** | **Requisito**
--- | ---
**Cumplimiento normativo de VM** |Antes de conmutar por error una máquina virtual, tiene que asegurarse de que el nombre que se asignará a la máquina virtual de Azure cumple con los [requisitos previos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). Puede modificar el nombre después de habilitar la replicación para la máquina virtual.
**Disco** | La capacidad del disco individual en máquinas protegidas no debe ser superior a 1023 GB. Una máquina virtual puede tener hasta 64 discos (es decir, hasta 64 TB).<br/><br/> No se admiten clústeres de invitados de disco compartido.
**UEFI** | No se admite el arranque de Unified Extensible Firmware Interface (UEFI)/Extensible Firmware Interface (EFI).
**Formación de equipos NIC** | Si la máquina virtual de origen tiene la formación de equipos NIC, se convierte en una sola NIC después de la conmutación por error a Azure.
**Dirección IP estática de Linux** | No se admite la protección de máquinas virtuales de Hyper-V que ejecuten Linux con una dirección IP estática.



## <a name="vmwarephysical-servers-replication-requirements-secondary-site"></a>Requisitos de replicación de servidores VMware y físicos (sitio secundario)

El producto InMage controla este escenario, que se puede descargar desde el almacén de Site Recovery cuando opta por replicar máquinas virtuales de VMware o servidores físicos en un sitio secundario. Tenga en cuenta que este escenario no está disponible en Azure Portal.

**Componente** | **Requisito**
--- | ---
**Sitio principal** | Se necesitan un servidor de procesos para controlar el almacenamiento en caché, la compresión y la optimización de datos. Este servidor también controla la instalación de inserción del agente unificado que se necesita en cada máquina que desea replicar.<br/><br/> Si desea replicar máquinas virtuales de VMware necesita uno o varios servidores VMware ESX o ESXi y, opcionalmente, un servidor VMware vCenter.
**Sitio secundario** | Se necesita una instalación de servidor de configuración en el sitio secundario, para configurar y administrar su entorno.<br/><br/> Un servidor de destino maestro está instalado en el sitio secundario para contener los datos replicados.<br/><br/> Un servidor vContinuum se instala de forma predeterminada en el servidor de destino maestro y proporciona una consola para administrar y supervisar el entorno.

### <a name="vmware-vmphysical-machine-requirements-secondary-site"></a>Requisitos de máquina física o máquina virtual de VMware (sitio secundario)

Compruebe los requisitos en la [matriz de compatibilidad de InMage](https://aka.ms/asr-scout-cm).


## <a name="hyper-v-replication-requirements-secondary-site"></a>Requisitos de replicación de Hyper-V (sitio secundario)

**Componente** | **Requisito**
--- | ---
**VMM** | Para replicar VM de Hyper-V en un sitio secundario, el host de Hyper-V se debe administrar en nubes de System Center VMM. <br/><br/> VMM debe estar ejecutando, como mínimo, System Center 2012 SP1 con las actualizaciones más recientes.<br/><br/> Se recomienda un servidor VMM en el sitio principal y otro del mismo tipo en el secundario. Puede replicar entre [nubes distintas en el mismo servidor VMM](https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-single-vmm), pero será necesario llevar a cabo alguna configuración en este escenario.<br/><br/> El servidor VMM debe estar conectado a Internet, con acceso a las [direcciones URL requeridas](#requirements-for-url-access).<br/><br/> Si tiene reglas de firewall basadas en direcciones IP en el servidor VMM, compruebe que las reglas permitan la comunicación con Azure.<br/><br/> Permita los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el puerto HTTPS (443).<br/><br/> Permita los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y del oeste de EE. UU.
**Hyper-V** | Los servidores Hyper-V deben ejecutar, como mínimo, Windows Server 2012 con el rol Hyper-V y tener instaladas las actualizaciones más recientes.<br/><br/> El servidor Hyper-V debe contener una o varias VM.<br/><br/> Los hosts de Hyper-V deben estar ubicados en grupos host en los servidores VMM principal y secundario.<br/><br/> Si ejecuta Hyper-V en un clúster de Windows Server 2012 R2, debe instalar la actualización [2961977](https://support.microsoft.com/kb/2961977). Si tiene un clúster de Hyper-V en Windows Server 2012, el agente de clúster no se crea automáticamente si tiene un clúster basado en una dirección IP estática. [Más información](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx) para realizar la configuración manualmente.

### <a name="hyper-v-vm-requirements-secondary-site"></a>Requisitos de VM de Hyper-V (sitio secundario)

**Componente** | **Detalles**
--- | ---
**Nube de VMM** | Las VM deben estar ubicadas en el host de Hyper-V en nubes de VMM.






## <a name="url-access-requirements"></a>Requisitos de acceso de las direcciones URL

Estas direcciones URL deben estar disponibles desde los servidores de host de Hyper-V, VMM y VMware.

**URL** | **VMM a VMM** | **VMM a Azure** | **Hyper-V en Azure** | **VMware a Azure** 
--- | --- | --- | --- | ---
``*.accesscontrol.windows.net`` | PERMITIR | Permitir | Permitir | Permitir
``*.backup.windowsazure.com`` | No se requiere | Permitir | Permitir | Permitir
``*.hypervrecoverymanager.windowsazure.com`` | Permitir | Permitir | Permitir | Permitir
``*.store.core.windows.net`` | Permitir | Permitir | Permitir | Permitir
``*.blob.core.windows.net`` | No se requiere | Permitir | Permitir | Permitir
``https://www.msftncsi.com/ncsi.txt`` | Permitir | Permitir | Permitir | Permitir
``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | No se requiere | No se requiere | No se requiere | Permitir la descarga SQL
``time.windows.com`` | PERMITIR | Permitir | Permitir | Permitir
``time.nist.gov`` | Permitir | Permitir | Permitir | PERMITIR

## <a name="failover-requirements"></a>Requisitos de conmutación por error

**Escenario** | **Prueba** | **Planeada** | **No planeada**
--- | --- | --- | ---
VMware en Azure | Compatible | No compatible | Compatible
Físico en Azure | Compatible | No compatible | Compatible
Hyper-V (VMM) en Azure | Compatible | Compatible | Compatible
Hyper-V (no VMM) en Azure | Compatible | Compatible | No compatible
Hyper-V (nube o VMM principal) en secundario | Compatible | Compatible | Compatible

## <a name="failback-requirements"></a>Requisitos de conmutación por recuperación

**Escenario** | **Prueba** | **Planeada** | **No planeada**
--- | --- | --- | ---
**Azure en VMware** | No compatible | No compatible | Compatible
**Azure en físico** | No compatible | No compatible | Solo se admite en VMware
**Azure en VMM** | No compatible | Compatible | No compatible
**Azure en Hyper-V (no VMM)** | No compatible | Compatible | No compatible
**Hyper-V (nube o VMM secundario) en principal** | Compatible | Compatible | Compatible

### <a name="failback-from-azure-to-vmware-vms"></a>Conmutación por recuperación de Azure en máquinas virtuales de VMware

**Componente** | **Detalles**
--- | ---
**Servidor de procesos temporal en Azure** | Si desea realizar la conmutación por recuperación desde Azure después de una conmutación por error, será preciso que configure una VM de Azure como servidor de procesos para que controle la replicación desde Azure. Dicha máquina virtual se puede eliminar cuando finalice la conmutación por recuperación.
**Conexión VPN** | Para realizar la conmutación por recuperación, necesitará que se haya configurado una conexión VPN (o Azure ExpressRoute) desde la red de Azure y el sitio local.
**Servidor de destino maestro local independiente** | El servidor de destino maestro local controla la conmutación por recuperación. El servidor de destino maestro se instala de forma predeterminada en el servidor de administración, pero si se va a realizar la conmutación por recuperación de grandes volúmenes de tráfico, se debe configurar un servidor de destino maestro local independiente para este propósito.



### <a name="failback-from-azure-to-physical-machines"></a>Conmutación por recuperación desde Azure en máquinas físicas

**Componente** | **Detalles**
--- | ---
**Conmutación por recuperación de físico a físico** | No es una opción admitida. Esto significa que si realiza la conmutación por error de los servidores físicos a Azure y luego desea realizar la conmutación por recuperación, debe realizarla a una VM de VMware. No se puede realizar la conmutación por recuperación a un servidor físico.
**VM** | Necesita una máquina virtual de VMware local para realizar la conmutación por recuperación.
**Servidor de configuración** | Si no implementó el servidor de configuración local como una VM de VMware, será necesario configurar un servidor de destino maestro independiente como una máquina virtual de VMware. Esto es necesario porque el servidor de destino maestro interactúa y se adjunta al almacenamiento de VMware para restaurar los discos a una máquina virtual de VMware.



## <a name="deployment-optimization"></a>Optimización de la implementación

Aplique las siguientes sugerencias para optimizar y escalar la implementación.

- **Tamaño del volumen del sistema operativo**: cuando se replica una máquina virtual en Azure, el volumen del sistema operativo tiene que ser inferior a 1 TB. Si tiene más volúmenes, puede moverlos manualmente a otro disco antes de comenzar la implementación.
- **Tamaño de disco de datos**: si se está replicando en Azure, podrá tener hasta 64 discos de datos en una máquina virtual, cada una con un máximo de 1 TB. Puede replicar y conmutar por error de manera eficaz una máquina virtual de 64 TB aproximadamente.
- **Límites del plan de recuperación**: Site Recovery puede escalar a miles de máquinas virtuales. Los planes de recuperación están diseñados como un modelo para las aplicaciones que deben conmutar por error entre sí por lo que se limita el número de máquinas en un plan de recuperación a 50.
- **Límites de servicio de Azure**: cada suscripción de Azure incluye un conjunto de límites predeterminados sobre núcleos, servicios en la nube, etc. Le recomendamos que ejecute una conmutación por error de prueba para validar la disponibilidad de los recursos de la suscripción. Puede modificar estos límites a través de soporte técnico de Azure.
- **Planeamiento de capacidad**: obtenga información sobre el [planeamiento de la capacidad](site-recovery-capacity-planner.md) de Site Recovery.
- **Ancho de banda de replicación**: si tiene poco ancho de banda de replicación, tenga en cuenta lo siguiente:
- **ExpressRoute**: Site Recovery funciona con los optimizadores de ExpressRoute de Azure y WAN, como Riverbed. [Obtenga más información](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sobre ExpressRoute.
- **Tráfico de replicación**: Site Recovery realiza una replicación inicial inteligente usando solo bloques de datos y no todo el VHD. Solo se replican los cambios durante la replicación continua.
- **Tráfico de red**: puede controlar el tráfico de red que se utiliza para la replicación mediante la configuración de [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) con una directiva basada en la dirección IP de destino y el puerto.  Además, si se está replicando en Azure Site Recovery mediante el agente de copia de seguridad de Azure, puede configurar la limitación para ese agente. [Más información](https://support.microsoft.com/kb/3056159).
- **RTO**: si desea medir el objetivo de tiempo de recuperación (RTO) que se puede esperar con Site Recovery, le sugerimos que ejecute una conmutación por error de prueba y vea los trabajos de Site Recovery para analizar la cantidad de tiempo que se tarda en completar las operaciones. Si está conmutando por error en Azure, para obtener un mejor RTO se recomienda que automatice todas las acciones manuales mediante la integración con Automatización de Azure y los planes de recuperación.
- **RPO**: Site Recovery admite un objetivo de punto de recuperación casi sincrónico (RPO) al replicar a Azure. Esto supone suficiente ancho de banda entre el centro de datos y Azure.



## <a name="next-steps"></a>Pasos siguientes
Después de revisar los requisitos de implementación generales, lea los requisitos previos detallados e implemente un escenario.

* [Replicación de máquinas virtuales de VMware en Azure](site-recovery-vmware-to-azure.md)
* [Replicación de servidores físicos a Azure](site-recovery-vmware-to-azure.md)
* [Replicación de servidores de Hyper-V de nubes VMM a Azure](site-recovery-vmm-to-azure.md)
* [Replicación de máquinas virtuales de Hyper-V (sin VMM) a Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replicación de máquinas virtuales de Hyper-V a un sitio secundario](site-recovery-vmm-to-vmm.md)
* [Replicación de máquinas virtuales de Hyper-V a un sitio secundario con SAN](site-recovery-vmm-san.md)
* [Replicación de máquinas virtuales de Hyper-V con un solo servidor VMM](site-recovery-single-vmm.md)



<!--HONumber=Dec16_HO1-->


