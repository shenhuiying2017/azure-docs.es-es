<properties
	pageTitle="Información general sobre Site Recovery" 
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales ubicadas localmente en Azure o en un sitio local secundario." 
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
	ms.date="08/05/2015" 
	ms.author="raynew"/>

#  Información general sobre Site Recovery

El servicio Site Recovery contribuye a una solución sólida de continuidad empresarial y recuperación ante desastres (BCDR) que protege los servidores físicos locales y las máquinas virtuales mediante la coordinación y automatización de la replicación y la conmutación por error en Azure o en centro de datos local secundario.

- **Simplificación**: Site Recovery ayuda a simplificar su estrategia BCDR al facilitar la configuración de la replicación y ejecutar la conmutación por error y la recuperación para sus aplicaciones y cargas de trabajo locales.
- **Replicación**: puede replicar las cargas de trabajo locales en el Almacenamiento de Azure o en un centro de datos secundario. 
- **Almacén**: para administrar la replicación, configure un almacén de Site Recovery en la región de Azure que seleccione. Todos los metadatos se guardan en esa región.
- **Metadatos**: ningún dato de la aplicación se envía a Azure. Site Recovery solo necesita metadatos como los nombres de las máquinas virtuales y de las nubes de VMM para organizar la conmutación por error y la replicación. 
- **Conexión a Azure**: los servidores de administración se comunican con Azure según el escenario de implementación. Por ejemplo, si se están replicando máquinas virtuales que se encuentra en una nube de VMM local, el servidor VMM se comunica con Site Recovery en una conexión HTTPS saliente cifrada. No se requiere conexión de las máquinas virtuales o hosts de Hyper-V.
- **Réplica de Hyper-V**: Azure Site Recovery aprovecha la réplica de Hyper-V para el proceso de replicación; también puede utilizar la replicación de SAN si la replicación se hace entre dos sitios de VMM locales. Site Recovery realiza la replicación inteligente, en la que se replican solo los bloques de datos, no todo el VHD para la replicación inicial. Para la replicación continua solo se replican los cambios diferenciales. Site Recovery admite la transferencia de datos sin conexión y funciona con los optimizadores de WAN.
- **Precios**: puede [obtener más información](pricing/details/site-recovery) sobre los precios de Site Recovery.


## Escenarios de implementación

Esta tabla resume los escenarios de replicación admitidos por Site Recovery.

**Replicación en** | **Replicación desde (local)** | **Detalles** | **Artículo**
---|---|---|---
Las tablas de Azure | Sitio de Hyper-V | Replique la máquina virtual en uno o varios servidores host de Hyper-V locales definidos como un sitio de Hyper-V en Azure. No se requiere ningún servidor VMM. | [Más información](site-recovery-hyper-v-site-to-azure.md)
Las tablas de Azure| Servidor VMM | Replique máquinas virtuales en uno o varios servidores host de Hyper-V locales ubicados en una nube de VMM en Azure. | [Más información](site-recovery-vmm-to-azure.md) 
Las tablas de Azure | Servidor físico de Windows | Replicación de un servidor físico de Windows o Linux en Azure | [Más información](site-recovery-vmware-to-azure.md)
Las tablas de Azure | Máquina virtual de VMware | Replicación de máquinas virtuales de VMware en Azure | [Más información](site-recovery-vmware-to-azure.md)
Centro de datos secundario | Servidor VMM | Replique máquinas virtuales en servidores host de Hyper-V locales ubicados en una nube de VMM en un servidor VMM secundario en otro centro de datos. | [Más información](site-recovery-vmm-to-vmm.md)
Centro de datos secundario | Servidor VMM con SAN | Replique máquinas virtuales en servidores host de Hyper-V locales ubicados en una nube de VMM en un servidor VMM secundario en otro centro de datos mediante la replicación de SAN.| [Más información](site-recovery-vmm-san.md)
Centro de datos secundario | Solo servidor VMM | Replique máquinas virtuales en servidores host de Hyper-V locales ubicados en una nube de VMM en una nube secundaria en el mismo servidor VMM. | [Más información](site-recovery-single-vmm.md) 


## Guía de la carga de trabajo

Las tecnologías de replicación de ASR son compatibles con cualquier aplicación que se ejecuta en una máquina virtual. Hemos llevado a cabo pruebas adicionales en colaboración con los equipos de producto de las aplicaciones para mejorar aún más cada aplicación.

**Carga de trabajo** | <p>**Replicación de máquinas virtuales de Hyper-V**</p> <p>**(para sitio secundario)**</p> | <p>**Replicación de máquinas de Hyper-V virtual**</p> <p>**(para Azure)**</p> | <p>**Replicación de máquinas virtuales de VMware**</p> <p>**(en sitio secundario)**</p> | <p>**Replicación de máquinas virtuales de VMware**</p><p>**(para Azure)**</p> 
---|---|---|---|--- 
Active Directory, DNS | Y | Y | Y | Y 
Aplicaciones web (IIS, SQL) | Y | Y | Y | Y 
SCOM | Y | Y | Y | Y 
Sharepoint | Y | Y | Y |Y 
<p>SAP</p><p>Replicación del sitio de SAP en Azure para sin clúster</p> | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft) 
Exchange (no DAG) | Y | Próximamente | Y | Y 
Escritorio remoto/VDI | Y | Y | Y | N/A 
<p>Linux</p> <p>(aplicaciones y sistema operativo)</p> | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft) 
Dynamics AX | Y | Y | Y | Y 
Dynamics CRM | Y | Próximamente | Y | Próximamente 
Oracle | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft) | Y (probado por Microsoft) 
Servidor de archivos de Windows | Y | Y | Y | Y


## Características y requisitos 

Esta tabla resume las principales características de Site Recovery y cómo se manejan durante la replicación en Azure, la replicación en un sitio secundario mediante la replicación de Hyper-V predeterminada y el uso de SAN.

Característica|Replicación en Azure|Replicación en un sitio secundario (réplica de Hyper-V)|Replicación en un sitio secundario (SAN)
---|---|---|---
Replicación de datos|Los metadatos acerca de los servidores locales y las máquinas virtuales se almacenan en el almacén de Site Recovery.</p> <p>Los datos replicados se almacenan en el almacenamiento de Azure.|Los metadatos acerca de los servidores locales y las máquinas virtuales se almacenan en el almacén de Site Recovery.</p> <p>Los datos replicados se almacenan en la ubicación especificada por el servidor de Hyper-V de destino.|Los metadatos acerca de los servidores locales y las máquinas virtuales se almacenan en el almacén de Site Recovery.</p> <p>Los datos replicados se almacenan en el almacenamiento de la matriz de destino.
Requisitos del almacén|Cuenta de Azure con el servicio Site Recovery|Cuenta de Azure con el servicio Site Recovery|Cuenta de Azure con el servicio Site Recovery
Replicación|Replique la máquina virtual de host de Hyper-V de origen al Almacenamiento de Azure. Conmutación por recuperación a la ubicación de origen.|Replique la máquina virtual de host de Hyper-V de origen al host de Hyper-V de destino. Conmutación por recuperación a la ubicación de origen.|Replique las máquinas virtuales del dispositivo de almacenamiento de SAN de origen en el dispositivo SAN de destino. Conmutación por recuperación a la ubicación de origen.
Máquina virtual|Unidad de disco duro de la máquina virtual almacenada en el almacenamiento de Azure|Unidad de disco duro de la máquina virtual almacenada en el host de Hyper-V|Disco duro de la máquina virtual almacenado en la matriz de almacenamiento de SAN
Almacenamiento de Azure|Necesario para almacenar discos duros de máquinas virtuales replicados|No aplicable|No aplicable
Matriz de almacenamiento de SAN|No aplicable|No aplicable|La matriz de almacenamiento de SAN debe estar disponible en los sitios de origen y de destino y administrada por VMM
Servidor VMM|Solo un servidor VMM en el sitio de origen.|Se recomienda utilizar servidores VMM en los sitios de origen y de destino. Puede replicar entre nubes en un único servidor VMM.|Servidores VMM en sitios de VMM de origen y destino. Las nubes deben contener al menos un clúster de Hyper-V.
Versión de VMM|System Center 2012 R2<p>System Center 2012 con SP1|System Center 2012 R2|System Center 2012 R2 con VMM Update Rollup 5.0
Configuración de VMM|Configuración de nubes en sitios de origen y destino </p><p>Configuración de redes de VM en sitio de origen y destino <p>Configuración de clasificaciones en sitios de origen y destino <p>Instalación del proveedor en servidores VMM de origen y destino|Configuración de nubes en el sitio de origen</p><p>Configuración de almacenamiento de SAN</p><p>Configuración de redes de VM en servidor de VMM de origen</p><p>Instalación del proveedor en el servidor de VMM de origen</p><p>Habilitación de protección de la máquina virtual|Configuración de nubes en sitios de origen y destino</p><p>Configuración de redes de VM en sitios de origen y destino</p><p>Instalación del proveedor en el servidor de VMM de origen y destino</p><p>Habilitación de protección de la máquina virtual
Proveedor de Azure Site Recovery</p><p>Se utiliza para conectarse a través de HTTPS a Site Recovery|Instalar en servidor VMM de origen|Instalar en servidores VMM de origen y destino|Instalar en servidores VMM de origen y destino
Agente de servicios de recuperación de Azure</p><p>Se utiliza para conectarse a través de HTTPS a Site Recovery|Instalación en servidores host de Hyper-V|No se requiere|No se requiere
Puntos de recuperación de la máquina virtual|Establecer los puntos de recuperación por hora.</p> <p>Especifica cuánto tiempo se debe mantener un punto de recuperación (de 0 a 24 horas).|Establecer los puntos de recuperación por cantidad.</p> <p>Especifica cuántos puntos de recuperación adicionales se deben mantener (de 0 a 15). De forma predeterminada, se crea un punto de recuperación cada hora|Se establece en la configuración de matriz de almacenamiento
Asignación de red|Asignar redes de máquina virtual a redes Azure.</p> <p>La asignación de red garantiza que todas las máquinas virtuales que conmutan por error en la misma red de máquina virtual de origen se pueden conectar después. Además, si hay una puerta de enlace de red en la red Azure de destino, las máquinas virtuales se podrán conectar a las máquinas virtuales locales. </p><p>Si la asignación no está habilitada, solo las máquinas virtuales que conmutan por error en el mismo plan de recuperación se pueden conectar entre sí después a Azure.|Asignar redes de máquina virtual de origen a redes de máquina virtual de destino.</p> <p>La asignación de red se usa para colocar las máquinas virtuales replicadas en servidores host de Hyper-V óptimos, y garantiza que las máquinas virtuales asociadas a la red de máquina virtual de origen estén asociadas a la red de destino asignada después de la conmutación por error. </p><p>Si la asignación no está habilitada, las máquinas virtuales replicadas no se conectarán a una red.|Asignar redes de máquina virtual de origen a redes de máquina virtual de destino.</p> <p>La asignación de red garantiza que las máquinas virtuales asociadas a la red VM de origen estén asociadas a la red de destino asignada después de la conmutación por error. </p><p>Si la asignación no está habilitada, las máquinas virtuales replicadas no se conectarán a una red.
Asignación de almacenamiento|No aplicable|Asigna las clasificaciones de almacenamiento en los servidores VMM de origen a las clasificaciones de almacenamiento en los servidores VMM de destino.</p> <p>Con la asignación habilitada, los discos duros de las máquinas virtuales en la clasificación de almacenamiento de origen estarán ubicados en la clasificación de almacenamiento de destino después de la conmutación por error.</p><p>Si la asignación de almacenamiento no está habilitada, los discos duros virtuales replicados se almacenarán en la ubicación predeterminada en el servidor host de Hyper-V de destino.|Asignaciones entre matrices de almacenamiento y grupos en sitios principales y secundarios.

## Pasos siguientes

Una vez finalizada la esta información general, [lea las prácticas recomendadas](site-recovery-best-practices.md) que le ayudarán a empezar a trabajar con la planeación de la implementación.
 

<!---HONumber=Sept15_HO2-->