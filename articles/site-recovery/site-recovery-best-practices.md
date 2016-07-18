<properties
	pageTitle="Preparación de la implementación de Azure Site Recovery | Microsoft Azure"
	description="En este artículo se describe cómo prepararse para la implementación de la replicación con Azure Site Recovery."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="07/06/2016"
	ms.author="raynew"/>

# Preparación de la implementación de Azure Site Recovery

Lea este artículo para obtener información de alto nivel de los requisitos de implementación de cada escenario de replicación admitido por el servicio Azure Site Recovery. Después de leer los requisitos generales de cada escenario, haga clic en el vínculo a los detalles de implementación específicos en la sección de requisitos previos de cada artículo de la implementación.

Después de leer este artículo, puede publicar algún comentario o formular alguna pregunta al final, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Información general

Las organizaciones necesitan una estrategia de recuperación ante desastres y continuidad del negocio (BCDR) que determine cómo seguirán en funcionamiento y disponibles las aplicaciones, las cargas de trabajo y los datos durante los tiempos de inactividad planeados y no planeados, y cómo recuperar las condiciones de funcionamiento normales lo antes posible. Su estrategia de BCDR se centra en soluciones que mantengan los datos empresariales seguros y recuperables, y las cargas de trabajo disponibles continuamente en caso de desastre.

Site Recovery es un servicio de Azure que contribuye a su estrategia de BCDR mediante la coordinación de la replicación de servidores físicos locales y máquinas virtuales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error al sitio secundario para mantener disponibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal. Site Recovery se puede usar en diversos escenarios y puede proteger varias cargas de trabajo. Más información en [¿Qué es Azure Site Recovery?](site-recovery-overview.md)


## Requisitos para la replicación de máquinas virtuales de Hyper-V

**Componente** | **Replicación en Azure (con VMM)** | **Replicación en Azure (sin VMM)** | **Replicación en un sitio secundario (con VMM)**
---|---|---|---
**VMM** | Uno o más servidores VMM con System Center 2012 R2. El servidor VMM debe tener al menos una nube que contenga uno o más grupos de hosts VMM. | No aplicable | Al menos un servidor VMM que se ejecute en System Center 2012 R2. Se recomienda un servidor VMM en cada sitio. El servidor VMM debe tener al menos una nube que contenga uno o más grupos de hosts VMM. Las nubes deben tener establecido el perfil de funcionalidad de Hyper-V.
**Hyper-V** | Uno o varios servidores host de Hyper-V en el centro de datos local con al menos Windows Server 2012 R2. El servidor de Hyper-V debe encontrarse en un grupo de hosts de una nube VMM. | Uno o varios servidores de Hyper-V en los sitios de origen y destino con al menos Windows Server 2012 R2. | Uno o varios servidores de Hyper-V en los sitios de origen y de destino con al menos Windows Server 2012 con las actualizaciones más recientes. El servidor de Hyper-V debe encontrarse en un grupo de hosts de una nube VMM.
**Máquinas virtuales** | Necesitará al menos una máquina virtual en el servidor de Hyper-V de origen. Las máquinas virtuales que se replican a Azure deben cumplir los [requisitos previos de las máquinas virtuales de Azure](#azure-virtual-machine-requirements). <br> Instale o actualice los [servicios de integración](https://technet.microsoft.com/library/dn798297.aspx) en la VM mediante los pasos indicados [aquí](https://technet.microsoft.com/library/hh846766.aspx#BKMK_step4). | Al menos una máquina virtual en el servidor de Hyper-V de origen. Las máquinas virtuales que se replican en Azure deben cumplir con los [requisitos previos de las máquinas virtuales de Azure](#azure-virtual-machine-requirements). <br> Instale o actualice los [servicios de integración](https://technet.microsoft.com/library/dn798297.aspx) en la VM mediante los pasos indicados [aquí](https://technet.microsoft.com/library/hh846766.aspx#BKMK_step4). | Al menos una VM en la nube VMM de origen. <br> Instale o actualice los [servicios de integración](https://technet.microsoft.com/library/dn798297.aspx) en la VM mediante los pasos indicados [aquí](https://technet.microsoft.com/library/hh846766.aspx#BKMK_step4).
**Cuenta de Azure** | Necesitará una cuenta y una suscripción de [Azure](https://azure.microsoft.com/). | No aplicable | Necesitará una cuenta y una suscripción de [Azure](https://azure.microsoft.com/).
**Almacenamiento de Azure** | Necesitará una [cuenta de almacenamiento de Azure](../storage/storage-redundancy.md#geo-redundant-storage) para almacenar los datos replicados. Los datos replicados se almacenan en el almacenamiento de Azure y las máquinas virtuales de Azure se ponen en marcha cuando se produce la conmutación por error. | No aplicable | Necesitará una [cuenta de almacenamiento de Azure](../storage/storage-redundancy.md#geo-redundant-storage) para almacenar los datos replicados. Los datos replicados se almacenan en el almacenamiento de Azure y las máquinas virtuales de Azure se ponen en marcha cuando se produce la conmutación por error.
**Proveedores y agentes** | Durante la implementación, instalará el proveedor de Azure Site Recovery en servidores VMM y el Agente de Servicios de recuperación de Azure en servidores host de Hyper-V. El proveedor se comunica con Azure Site Recovery. El agente administra la replicación de datos entre los servidores de Hyper-V de origen y destino. No se instala nada en las máquinas virtuales. | Durante la implementación, instalará el proveedor de Azure Site Recovery y el Agente de Servicios de recuperación de Azure en el servidor host de Hyper-V o el clúster. No se instala nada en las máquinas virtuales. | Durante la implementación, instalará el proveedor de Azure Site Recovery en servidores VMM para comunicarse con Azure Site Recovery. La replicación se produce entre los servidores de origen y destino de Hyper-V mediante la LAN o la red privada virtual (VPN).
**Conectividad de proveedor y agente** | Si el proveedor se va a conectar al servicio de Site Recovery mediante un proxy, deberá asegurarse de que el proxy pueda tener acceso a las direcciones URL de Site Recovery. | Si el proveedor se va a conectar a Site Recovery mediante un proxy, deberá asegurarse de que el proxy pueda tener acceso a las direcciones URL de Site Recovery. | Si el proveedor se va a conectar a Site Recovery mediante un proxy, deberá asegurarse de que el proxy pueda tener acceso a las direcciones URL de Site Recovery.
**Conectividad de Internet** | Desde el servidor VMM y los hosts de Hyper-V. | Desde los hosts de Hyper-V. | Solo en el servidor de VMM.
**Asignación de red** | Configure una asignación de red de modo que todas las máquinas que conmuten por error en la misma red de Azure se puedan conectar entre sí, con independencia del plan de recuperación al que pertenezcan. Si hay una puerta de enlace de red en la red de Azure de destino, las máquinas virtuales también se pueden conectar a las máquinas virtuales locales. Si no configura la asignación de redes, solo se pueden conectar las máquinas que conmutan por error en el mismo plan de recuperación. | No aplicable | Configure la asignación de red de modo que las máquinas virtuales estén conectadas a las redes adecuadas tras la conmutación por error y las máquinas virtuales de réplica estén colocadas óptimamente en los servidores host de Hyper-V de destino. Si no configura la asignación de red, las máquinas replicadas no se conectarán a ninguna red de máquinas virtuales tras la conmutación por error.
**Asignación de almacenamiento** | No aplicable | No aplicable | Opcionalmente, puede configurar la asignación de almacenamiento para asegurarse de que las máquinas virtuales estén conectadas óptimamente al almacenamiento tras la conmutación por error (de forma predeterminada, la máquina virtual de réplica se almacenará en la ubicación indicada en el servidor de Hyper-V de destino).
**Replicación de SAN** | No aplicable | No aplicable | Si desea replicar entre dos sitios de VMM locales con replicación de SAN, puede usar el entorno de SAN existente. Consulte las [matrices SAN admitidas](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).
**Más información** | [Requisitos previos de implementación detallados](site-recovery-vmm-to-azure.md#before-you-start) | [Requisitos previos de implementación detallados](site-recovery-hyper-v-site-to-azure.md#before-you-start#before-you-start) | [Requisitos previos de implementación detallados](site-recovery-vmm-to-vmm.md#before-you-start)




## Requisitos de replicación de servidores físicos y máquinas virtuales de VMware

En la tabla se resumen los requisitos para la replicación de máquinas virtuales de VMware y servidores físicos de Windows o Linux en Azure y en un sitio secundario.

>[AZURE.NOTE] Puede replicar máquinas virtuales de VMware y servidores físicos a Azure con un modelo de implementación [mejorado](site-recovery-vmware-to-azure-classic.md) o con un modelo [heredado](site-recovery-vmware-to-azure-classic-legacy.md) que haya usado en implementaciones anteriores. En la tabla siguiente se incluyen los requisitos de implementación del modelo mejorado.

**Componente** | **Replicación a Azure (mejorado)** | **Replicar al sitio secundario**
---|---|---
**Sitio principal local** | Instala un servidor de administración que ejecuta todos los componentes de Site Recovery (configuración, proceso, destino maestro). | Instala un servidor de procesos para el almacenamiento en caché, la compresión y el cifrado de los datos de replicación antes de enviarlo al sitio secundario. Puede instalar a servidores de procesos adicionales para equilibrar la carga o de cara a la tolerancia a errores.
**Sitio secundario local** | No aplicable | Instala un servidor de configuración único que se utiliza para configurar, administrar y supervisar la implementación.<br/><br>Se recomienda instalar un servidor de vContinuum para facilitar la administración del servidor de configuración.<br/><br/>Deberá configurar el servidor de destino maestro como una máquina virtual que se ejecuta en el servidor secundario vSphere.
**VMware vCenter/ESXi** | Si va a replicar máquinas virtuales de VMware (o desea conmutar por recuperación servidores físicos) en el sitio principal, necesitará un hipervisor vSphere ESX/ESXi en el sitio principal. También se recomienda un servidor vCenter para administrar los hosts ESXi. | En el sitio principal y secundario necesitará uno o más hosts ESXi de VMware (y, opcionalmente, un servidor vCenter).
**Conmutación por recuperación** | Para conmutar por recuperación desde Azure, necesita un entorno VMware incluso si va a replicar servidores físicos.<br/><br/>Necesitará configurar un servidor de procesos como máquina virtual de Azure.<br/><br/>El servidor de configuración funciona como servidor de destino maestro, pero si va a conmutar por recuperación grandes volúmenes de tráfico, puede que quiera configurar un servidor de destino maestro local adicional. [Más información](site-recovery-failback-azure-to-vmware-classic.md)| La conmutación por recuperación del sitio secundario al sitio primario es solo para VMware, incluso si se ha conmutado por error una máquina física. Para realizar la conmutación por recuperación, deberá configurar un servidor de destino maestro como máquina virtual en el servidor de vSphere principal.
**Cuenta de Azure** | Necesitará una cuenta y una suscripción de [Azure](https://azure.microsoft.com/). | No aplicable
**Almacenamiento de Azure** | Necesitará una [cuenta de almacenamiento de Azure](../storage/storage-redundancy.md#geo-redundant-storage) para almacenar los datos replicados. Los datos replicados se almacenan en el almacenamiento de Azure y las máquinas virtuales de Azure se ponen en marcha cuando se produce la conmutación por error. | No aplicable
**Red virtual de Azure** | Necesitará una red virtual de Azure a la que se conectarán las máquinas virtuales de Azure cuando se produzca la conmutación por error. Para realizar la conmutación por recuperación después de la conmutación por error, necesitará una conexión VPN (o Azure ExpressRoute) configurada entre la red de Azure y el sitio local. | No aplicable
**Máquinas protegidas** | Al menos una máquina virtual de VMware o un servidor físico de Windows o Linux. Durante la implementación, el servicio de movilidad se instala en cada máquina que quiere replicar. | Al menos una máquina virtual de VMware o un servidor físico de Windows o Linux. Durante la implementación, el agente unificado se instalará en cada equipo que desea replicar.
**Conectividad** | Si el servidor de administración se conecta a Site Recovery mediante un proxy, deberá asegurarse de que el servidor proxy pueda conectarse a direcciones URL específicas. | El servidor de configuración necesita acceso a Internet.
**Más información** | [Requisitos previos de implementación detallados](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment). | [Descargue](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) la guía de usuario de InMage Scout.


## Requisitos para las máquinas virtuales de Azure

Puede implementar Site Recovery para replicar máquinas virtuales y servidores físicos que ejecuten cualquier sistema operativo compatible con Azure. Incluye la mayoría de las versiones de Windows y Linux. Debe asegurarse de que las máquinas virtuales locales que desea proteger cumplan los requisitos de Azure.


**Característica** | **Requisitos** | **Detalles**
---|---|---
Host de Hyper-V | Debe estar ejecutando Windows Server 2012 R2 | La comprobación de los requisitos previos producirá un error si el sistema operativo no es compatible
Hipervisor de VMware | Sistema operativo compatible | [Comprobar los requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
Sistema operativo invitado | Replicación de Hyper-V en Azure: Site Recovery es compatible con todos los sistemas operativos [admitidos por Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para la replicación de servidores físicos y de VMware, consulte los [requisitos previos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) de Windows y Linux. | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Arquitectura del sistema operativo invitado | 64 bits | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Tamaño del disco del sistema operativo | Hasta 1.023 GB | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Número de discos del sistema operativo | 1 | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Número de discos de datos | 16 o menos (el valor máximo es una función del tamaño de la máquina virtual que se está creando. 16 = XL) | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Tamaño de VHD del disco de datos | Hasta 1023 GB | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Adaptadores de red | Se admiten varios adaptadores |
Dirección IP estática | Compatible | Si la máquina virtual principal usa una dirección IP estática, puede especificar la dirección IP estática para la máquina virtual que se creará en Azure. Tenga en cuenta que no se admite la dirección IP estática de una máquina virtual de Linux que se ejecuta en Hyper-v.
Disco iSCSI | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
VHD compartido | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Disco FC | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Formato de disco duro| VHD <br/><br/> VHDX | Aunque VHDX no se admite en Azure en este momento, Site Recovery convierte automáticamente VHDX en VHD cuando se conmuta por error en Azure. Cuando se realiza la conmutación por recuperación en local, las máquinas virtuales siguen usando el formato VHDX.
Nombre de la máquina virtual| Entre 1 y 63 caracteres. Restringido a letras, números y guiones. El nombre debe empezar y terminar por una letra o un número. | Actualizar el valor de las propiedades de la máquina virtual en Site Recovery
Tipo de máquina virtual | <p>Generación 1</p> <p>Generación 2 - Windows</p> | Se admiten las máquinas virtuales de generación 2 con el tipo de disco de SO de disco básico que incluye uno o dos volúmenes de datos con un formato de disco como VHDX, inferior a 300 GB. No se admiten máquinas virtuales Linux de generación 2. [Más información](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## Optimización de la implementación

Aplique las siguientes sugerencias como ayuda para optimizar y escalar la implementación.

- **Tamaño del volumen del sistema operativo**: cuando se replica una máquina virtual en Azure, el volumen del sistema operativo tiene que ser inferior a 1 TB. Si tiene más volúmenes, puede moverlos manualmente a otro disco antes de comenzar la implementación.
- **Tamaño de disco de datos**: si se está replicando en Azure, podrá tener hasta 32 discos de datos en una máquina virtual, cada una con un máximo de 1 TB. Puede replicar y conmutar por error de manera eficaz una máquina virtual de ~32 TB.
- **Límites del plan de recuperación**: Site Recovery puede escalar a miles de máquinas virtuales. Los planes de recuperación están diseñados como un modelo para las aplicaciones que deben conmutar por error entre sí por lo que se limita el número de máquinas en un plan de recuperación a 50.
- **Límites del servicio Azure**: cada suscripción de Azure incluye un conjunto de límites predeterminados sobre núcleos, servicios en la nube, etc. Le recomendamos que ejecute una conmutación por error de prueba para validar la disponibilidad de los recursos de la suscripción. Puede modificar estos límites a través de soporte técnico de Azure.
- **Planeamiento de capacidad**: obtenga información sobre el [planeamiento de la capacidad](site-recovery-capacity-planner.md) de Site Recovery.
- **Ancho de banda de replicación**: si tiene poco ancho de banda de replicación, tenga en cuenta lo siguiente:
	- **ExpressRoute**: Site Recovery funciona con los optimizadores de ExpressRoute de Azure y WAN, como Riverbed. [Obtenga más información](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sobre ExpressRoute.
	- **Tráfico de replicación**: Site Recovery realiza una replicación inicial inteligente usando solo bloques de datos y no todo el VHD. Solo se replican los cambios durante la replicación continua.
	- **Tráfico de red**: puede controlar el tráfico de red que se utiliza para la replicación mediante la configuración de [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) con una directiva basada en la dirección IP de destino y el puerto. Además, si se está replicando en Azure Site Recovery mediante el agente Copia de seguridad de Azure. Puede configurar la limitación para ese agente. [Más información](https://support.microsoft.com/kb/3056159).
- **RTO**: si desea medir el objetivo de tiempo de recuperación (RTO) que se puede esperar con Site Recovery, le sugerimos que ejecute una conmutación por error de prueba y vea los trabajos de Site Recovery para analizar la cantidad de tiempo que se tarda en completarse las operaciones. Si está conmutando por error en Azure, para obtener un mejor RTO se recomienda que automatice todas las acciones manuales mediante la integración con Automatización de Azure y los planes de recuperación.
- **RPO**: Site Recovery admite un objetivo de punto de recuperación casi sincrónico (RPO) al replicar a Azure. Esto supone suficiente ancho de banda entre el centro de datos y Azure.


##Direcciones URL de servicio
Asegúrese de poder acceder a estas direcciones URL desde el servidor:


**URLs** | **VMM a VMM** | **VMM a Azure** | **Sitio Hyper-V a Azure** | **VMware a Azure**
---|---|---|---|---
 *.accesscontrol.windows.net | Acceso requerido | Acceso requerido | Acceso requerido | Acceso requerido
 *.backup.windowsazure.com | | Acceso requerido | Acceso requerido | Acceso requerido
 *.hypervrecoverymanager.windowsazure.com | Acceso requerido | Acceso requerido | Acceso requerido | Acceso requerido
 *.store.core.windows.net | Acceso requerido | Acceso requerido | Acceso requerido | Acceso requerido
 *.blob.core.windows.net | | Acceso requerido | Acceso requerido | Acceso requerido
 https://www.msftncsi.com/ncsi.txt | Acceso requerido | Acceso requerido | Acceso requerido | Acceso requerido
 https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi | | | | Acceso requerido


## Pasos siguientes

Una vez que conoce y puede comparar los requisitos de implementación generales, puede leer los requisitos previos detallados y comenzar a implementar cada escenario.

- [Replicación de máquinas virtuales de VMware en Azure](site-recovery-vmware-to-azure-classic.md)
- [Replicación de servidores físicos a Azure](site-recovery-vmware-to-azure-classic.md)
- [Replicación de servidores de Hyper-V de nubes VMM a Azure](site-recovery-vmm-to-azure.md)
- [Replicación de máquinas virtuales de Hyper-V (sin VMM) a Azure](site-recovery-hyper-v-site-to-azure.md)
- [Replicación de máquinas virtuales de Hyper-V a un sitio secundario](site-recovery-vmm-to-vmm.md)
- [Replicación de máquinas virtuales de Hyper-V a un sitio secundario con SAN](site-recovery-vmm-san.md)
- [Replicación de máquinas virtuales de Hyper-V con un solo servidor VMM](site-recovery-single-vmm.md)

<!---HONumber=AcomDC_0706_2016-->