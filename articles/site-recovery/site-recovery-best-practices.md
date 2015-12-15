<properties
	pageTitle="Preparación de la implementación de Azure Site Recovery | Microsoft Azure"
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos ubicados en servidores locales en Azure o en un centro de datos secundario."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="12/07/2015"
	ms.author="raynew"/>

# Preparación de la implementación de Azure Site Recovery

## Información acerca de este artículo

En este artículo se describe cómo preparar la implementación de Azure Site Recovery. Si tiene alguna pregunta después de leer el artículo, publíquela en el [Foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Protección de las máquinas virtuales de Hyper-V

Dispone de un par opciones para implementar la protección de las máquinas virtuales de Hyper-V. Puede replicar las máquinas virtuales de Hyper-V locales en Azure o en un centro de datos secundario. Cada implementación presenta diferentes requisitos.

**Requisito** | **Replicación en Azure (con VMM)** | **Replicación de máquinas virtuales de Hyper-V en Azure (sin VMM)** | **Replicación de máquinas virtuales de Hyper-V en un sitio secundario (con VMM)** | **Detalles**
---|---|---|---|---
**VMM** | VMM ejecutándose en System Center 2012 R2 <br/><br/>Al menos una nube VMM que contenga uno o más grupos de hosts de VMM | N/D | Servidores VMM en el sitio principal y el secundario que se ejecuten como mínimo en System Center 2012 SP1 con las últimas actualizaciones <br/><br/> Al menos una nube en cada servidor VMM. Las nubes deben tener establecido el perfil de capacidad de Hyper-V.<br/><br/> La nube de origen debe tener al menos un grupo de hosts de VMM. | Opcional. No es necesario tener implementado System Center VMM para replicar máquinas virtuales de Hyper-V en Azure pero sí para asegurarse de que el servidor VMM esté configurado correctamente, lo que incluye comprobar que se esté ejecutando la versión correcta de VMM y que las nubes estén configuradas.
**Hyper-V** | Al menos un servidor host de Hyper-V en el centro de datos local que ejecute Windows Server 2012 R2 o posterior | Al menos un servidor de Hyper-V en el sitio de origen y el de destino que ejecute como mínimo Windows Server 2012 R2 y con conexión a Internet<br/><br/> Los servidores de Hyper-V deben encontrarse en un grupo de hosts en una nube VMM. | Al menos un servidor de Hyper-V en el sitio de origen y el de destino que ejecute como mínimo Windows Server 2012 con las actualizaciones más recientes instaladas y con conexión a Internet<br/><br/> Los servidores de Hyper-V deben encontrarse en un grupo de hosts en una nube VMM. | 
**Máquinas virtuales** | Al menos una máquina virtual en el servidor host de Hyper-V de origen | Al menos una máquina virtual en el servidor host de Hyper-V en la nube VMM de origen | Al menos una máquina virtual en el servidor host de Hyper-V en la nube VMM de origen | Las máquinas virtuales que se replican en Azure deben cumplir los [requisitos previos para las máquinas virtuales de Azure](site-recovery-best-practices.md/#virtual-machines).
**Cuenta de Azure** | Necesitará una cuenta de [Azure](http://azure.microsoft.com/) y una suscripción al servicio Site Recovery. | Necesitará una cuenta de [Azure](http://azure.microsoft.com/) y una suscripción al servicio Site Recovery. | N/D | Si no tiene cuenta, comience por una [versión de evaluación gratuita](pricing/free-trial/). Lea acerca de los [precios](pricing/details/site-recovery/) para el servicio.
**Almacenamiento de Azure** | Necesitará una suscripción para una cuenta de Almacenamiento de Azure que tenga habilitada la replicación geográfica. | Necesitará una suscripción para una cuenta de Almacenamiento de Azure que tenga habilitada la replicación geográfica. | N/D | La cuenta debe estar en la misma región que el almacén de Azure Site Recovery y estar asociada a la misma suscripción. [Más información acerca del almacenamiento](../storage/storage-introduction.md).
**Asignación de almacenamiento** | N/D | N/D | Opcionalmente, puede configurar la asignación de almacenamiento para asegurarse de que las máquinas virtuales estén conectadas de forma óptima al almacenamiento tras la conmutación por error. Al replicar entre dos sitios VMM locales, de forma predeterminada la máquina virtual de réplica se almacenará en la ubicación indicada en el servidor host de Hyper-V de destino. Puede configurar la asignación entre las clasificaciones de almacenamiento VMM en los servidores VMM de origen y de destino. | Para usar esta característica, las clasificaciones de almacenamiento deberán estar configuradas antes de empezar la implementación. [Más información](site-recovery-storage-mapping.md).
**Replicación de SAN** | N/D | N/D | Si desea replicar entre dos sitios de VMM locales con replicación de SAN, puede usar el entorno de SAN existente. | Necesitará una [matriz de SAN compatible](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx), y habrá que detectar y clasificar el almacenamiento de SAN en VMM.<br/><br/>Si no está replicando, deberá crear los LUN y asignar almacenamiento en la consola VMM. Si ya está replicando, puede omitir este paso.
**Redes** | Configure una asignación de red para asegurarse de que todas las máquinas que conmuten por error en la misma red Azure se puedan conectar entre sí, con independencia del plan de recuperación al que pertenezcan. Además, si se configura una puerta de enlace de red en la red Azure de destino, las máquinas virtuales se pueden conectar a otras máquinas virtuales locales. Si no configura la asignación de redes, solo se pueden conectar las máquinas que conmutan por error en el mismo plan de recuperación. | N/D | <br/><br/>Configure la asignación de red para asegurarse de que las máquinas virtuales se conecten a las redes adecuadas tras la conmutación por error y de que las máquinas virtuales de réplica estén colocadas de manera óptima en los servidores host de Hyper-V. Si no configura la asignación de red, las máquinas replicadas no se conectarán a ninguna red de máquinas virtuales tras la conmutación por error. | [Obtenga más información](site-recovery-network-mapping) sobre la asignación de redes. <br/><br/> Para configurar la asignación de red con VMM, debe asegurarse de que la red lógica de VMM y las redes de máquinas virtuales estén configuradas correctamente. [Aprenda más](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx) y lea sobre las [redes de máquinas virtuales](https://technet.microsoft.com/library/jj721575.aspx). Además, consulte las [consideraciones sobre redes para VMM](site-recovery-network-design.md/#vmm-design).  
**Proveedores y agentes** | Durante la implementación, se instala al proveedor de Azure Site Recovery en los servidores VMM. En los servidores de Hyper-V ubicados en nubes VMM, se instala el agente de Servicios de recuperación de Azure. | Durante la implementación, se instalan el proveedor de Azure Site Recovery y el agente de Servicios de recuperación de Azure en el servidor host de Hyper-V o el clúster.| Durante la implementación, se instala al proveedor de Azure Site Recovery en los servidores VMM. En los servidores de Hyper-V ubicados en nubes VMM, se instala el agente de Servicios de recuperación de Azure. | Los proveedores y los agentes se conectan a Site Recovery a través de Internet mediante una conexión HTTPS cifrada. No es necesario agregar excepciones de firewall ni crear un proxy específico para la conexión del proveedor pero, si desea usar un proxy personalizado, debe permitir estas direcciones URL a través del firewall antes de comenzar la implementación: <br/><br/> *.hypervrecoverymanager.windowsazure.com <br/><br/> *.accesscontrol.windows.net<br/><br/> *.backup.windowsazure.com <br/><br/> *.blob.core.windows.net <br/><br/>*.store.core.windows.net **Conectividad a Internet** | Solo se necesita conexión a Internet en los servidores VMM. | Solo se necesita conexión a Internet en los servidores host de Hyper-V. | Solo se necesita conexión a Internet en los servidores VMM. | No se necesitar instalar nada en las máquinas virtuales ni conectarlas directamente a Internet.



## Protección de máquinas virtuales de VMware o servidores físicos

Dispone de un par de opciones para implementar la protección de máquinas virtuales de VMware o de servidores físicos Windows o Linux. Puede replicarlos en Azure o en un centro de datos secundario. Cada implementación presenta diferentes requisitos.

**Requisito** | **Replicación de máquinas virtuales de VMware o servidores físicos en Azure** | * **Replicación de máquinas virtuales de VMware o servidores físicos en un sitio secundario**  
---|---|--- 
**Sitio principal** | **Servidor de procesos**: un servidor de Windows dedicado (físico o virtual) | **Servidor de procesos**: un servidor de Windows dedicado (físico o máquina virtual de VMware)<br/><br/>  
**Sitio local secundario** | N/D | **Servidor de configuración**: un servidor de Windows dedicado (físico o virtual) <br/><br/> **Servidor de destino maestro**: un servidor dedicado (físico o virtual). Configúrelo con Windows para proteger equipos Windows, o con Linux para proteger equipos Linux.
**Las tablas de Azure** | **Suscripción**: necesitará una suscripción para el servicio Site Recovery. Lea acerca de los [precios](pricing/details/site-recovery/). <br/><br/> **Cuenta de almacenamiento**: necesitará una cuenta de almacenamiento con la replicación geográfica habilitada. La cuenta debe estar en la misma región que el almacén de Site Recovery y estar asociada a la misma suscripción. [Más información](../storage/storage-introduction.md).<br/><br/> **Servidor de configuración**: debe configurar el servidor de configuración como máquina virtual de Azure.<br/><br/> **Servidor de destino maestro**: debe configurar el servidor de destino maestro como máquina virtual de Azure.<br/><br/> Configúrelo con Windows para proteger equipos Windows, o con Linux para proteger equipos Linux.<br/><br/> **Red virtual de Azure**: necesitará una red virtual de Azure en la que se implementarán el servidor de configuración y el servidor de destino maestro. Debe compartir la misma región y suscripción que el almacén de Azure Site Recovery. | N/D  
**Máquinas virtuales/Servidores físicos** | Al menos una máquina virtual de VMware o un servidor físico de Windows o Linux<br/><br/>Durante la implementación, se instala el servicio de movilidad en cada equipo.| Al menos una máquina virtual de VMware o un servidor físico de Windows o Linux<br/><br/> Durante la implementación, se instala el agente unificado en cada equipo.




## Requisitos para las máquinas virtuales de Azure

Puede implementar Site Recovery para replicar máquinas virtuales y servidores físicos que ejecuten cualquier sistema operativo compatible con Azure. Incluye la mayoría de las versiones de Windows y Linux. Debe asegurarse de que las máquinas virtuales locales que desea proteger cumplan los requisitos de Azure.


**Característica** | **Soporte técnico** | **Detalles**
---|---|---
Sistema operativo host de Hyper-V | Windows Server 2012 R2 | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Sistema operativo de hipervisor de VMware | Con un sistema operativo compatible | [Detalles](site-recovery-vmware-to-azure.md/#before-you-start) 
Sistema operativo invitado | Para la replicación de Hyper-V en Azure, Site Recovery es compatible con todos los sistemas operativos [admitidos por Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para la replicación de servidores físicos y VMware, consulte los [requisitos previos](site-recovery-vmware-to-azure.md/#before-you-start) para Windows y Linux. | Se producirá un error en la comprobación de los requisitos previos si no es compatible. 
Arquitectura del sistema operativo invitado | 64 bits | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Tamaño del disco del sistema operativo | Hasta 1.023 GB | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Número de discos del sistema operativo | 1 | Se producirá un error en la comprobación de los requisitos previos si no es compatible. 
Número de discos de datos | 16 o menos (el valor máximo es una función del tamaño de la máquina virtual que se está creando. 16 = XL) | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Tamaño de VHD del disco de datos | Hasta 1023 GB | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Adaptadores de red | Se admiten varios adaptadores |
Dirección IP estática | Compatible | Si la máquina virtual principal usa una dirección IP estática, puede especificar la dirección IP estática para la máquina virtual que se creará en Azure.
Disco iSCSI | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
VHD compartido | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Disco FC | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Formato de disco duro| VHD <br/><br/> VHDX | Aunque VHDX no se admite en Azure en este momento, Site Recovery convierte automáticamente VHDX en VHD cuando se conmuta por error en Azure. Cuando se realiza la conmutación por recuperación en local, las máquinas virtuales siguen usando el formato VHDX.
Nombre de la máquina virtual| Entre 1 y 63 caracteres. Restringido a letras, números y guiones. El nombre debe empezar y terminar por una letra o un número. | Actualizar el valor de las propiedades de la máquina virtual en Site Recovery
Tipo de máquina virtual | <p>Generación 1</p> <p>Generación 2 - Windows</p> | Se admiten las máquinas virtuales de generación 2 con el tipo de disco de SO de disco básico que incluye uno o dos volúmenes de datos con un formato de disco como VHDX, inferior a 300 GB. No se admiten máquinas virtuales Linux de generación 2. [Más información](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## Optimización de la implementación

Aplique las siguientes sugerencias como ayuda para optimizar y escalar la implementación.

- **Tamaño del volumen del sistema operativo**: cuando se replica una máquina virtual en Azure, el volumen del sistema operativo tiene que ser inferior a 1 TB. Si tiene más volúmenes, puede moverlos manualmente a otro disco antes de comenzar la implementación.
- **Tamaño de disco de datos**: si se está replicando en Azure, podrá tener hasta 32 discos de datos en una máquina virtual, cada una con un máximo de 1 TB. Puede replicar y conmutar por error de manera eficaz una máquina virtual de ~32 TB.
- **Límites del plan de recuperación**: Site Recovery puede escalar a miles de máquinas virtuales. Los planes de recuperación están diseñados como un modelo para las aplicaciones que deben conmutar por error entre sí por lo que se limita el número de máquinas en un plan de recuperación a 50.
- **Límites del servicio Azure**: cada suscripción de Azure incluye un conjunto de límites predeterminados sobre núcleos, servicios en la nube, etc. Le recomendamos que ejecute una conmutación por error de prueba para validar la disponibilidad de los recursos de la suscripción. Puede modificar estos límites a través de soporte técnico de Azure.
- **Planeación de capacidad**: lea sobre [Capacity Planner](http://www.microsoft.com/download/details.aspx?id=39057) si va a replicar máquinas virtuales de Hyper-V.
- **Ancho de banda de replicación**: si tiene poco ancho de banda de replicación, tenga en cuenta lo siguiente:
	- **ExpressRoute**: Site Recovery funciona con los optimizadores de ExpressRoute de Azure y WAN, como Riverbed. [Obtenga más información](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sobre ExpressRoute.
	- **Tráfico de replicación**: Site Recovery realiza una replicación inicial inteligente usando solo bloques de datos y no todo el VHD. Solo se replican los cambios durante la replicación continua.
	- **Tráfico de red**: puede controlar el tráfico de red que se utiliza para la replicación mediante la configuración de [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) con una directiva basada en la dirección IP de destino y el puerto. Además, si se está replicando en Azure Site Recovery mediante el agente Copia de seguridad de Azure. Puede configurar la limitación para ese agente. [Más información](https://support.microsoft.com/kb/3056159).
- **RTO**: si desea medir el objetivo de tiempo de recuperación (RTO) que se puede esperar con Site Recovery, le sugerimos que ejecute una conmutación por error de prueba y vea los trabajos de Site Recovery para analizar la cantidad de tiempo que se tarda en completarse las operaciones. Si está conmutando por error en Azure, para obtener un mejor RTO se recomienda que automatice todas las acciones manuales mediante la integración con Automatización de Azure y los planes de recuperación.
- **RPO**: Site Recovery admite un objetivo de punto de recuperación casi sincrónico (RPO) al replicar a Azure. Esto supone suficiente ancho de banda entre el centro de datos y Azure.





## Pasos siguientes

Después de revisar estas prácticas recomendadas, puede iniciar la implementación de Site Recovery:

- [Configuración de la protección entre un sitio de VMM local y Azure](site-recovery-vmm-to-azure.md)
- [Configuración de la protección entre un sitio de Hyper-V local y Azure](site-recovery-hyper-v-site-to-azure.md)
- [Configuración de la protección entre dos sitios VMM locales](site-recovery-vmm-to-vmm.md)
- [Configuración de la protección entre dos sitios de VMM locales con SAN](site-recovery-vmm-san.md)
- [Configuración de la protección con un único servidor VMM](site-recovery-single-vmm.md)
 

<!---HONumber=AcomDC_1210_2015-->