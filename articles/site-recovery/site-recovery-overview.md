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
	ms.date="05/10/2015" 
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

**Carga de trabajo** | <p>** Replicación de máquinas virtuales de Hyper-V **</p> <p>**(en un sitio secundario) **</p> | <p>** Replicación de máquinas virtuales de Hyper-V **</p> <p>**(en Azure) **</p> | <p>** Replicación de máquinas virtuales de VMware **</p> <p>**(en un sitio secundario) **</p> | <p>** Replicación de máquinas virtuales de VMware **</p> <p>**(en Azure) **</p>
---|---|---|---|---
Active Directory, DNS | Y | Y | Y | Próximamente 
Aplicaciones web (IIS, SQL) | Y | Y | Y | Próximamente
SCOM | Y | Y | Y | Próximamente
SharePoint | Y | Y | Y | Próximamente
<p>SAP</p><p>Replicación de un sitio de SAP en Azure para no clúster</p> | S (probado por Microsoft) | S (probado por Microsoft) | S (probado por Microsoft) | Próximamente
Exchange (no DAG) | Y | Próximamente | Y | Próximamente
Escritorio remoto/VDI | Y | Y | Y | Próximamente 
<p>Linux</p> <p>(sistema operativo y aplicaciones</p> | S (probado por Microsoft) | S (probado por Microsoft) | S (probado por Microsoft) | Próximamente 
Dynamics AX | Y | Y | Y | Próximamente
Dynamics CRM | Próximamente | Próximamente | Y | Próximamente
Oracle | Próximamente | Próximamente | S (probado por Microsoft) | Próximamente


## Características y requisitos 

Esta tabla resume las principales características de Site Recovery y cómo se manejan durante la replicación en Azure, la replicación en un sitio secundario mediante la replicación de Hyper-V predeterminada y el uso de SAN.

<table border="1">
<thead>
<tr>
	<th>Característica</th><th>Replicación en Azure</th>
	<th>Replicación en un sitio secundario (réplica de Hyper-V)</th>
	<th>Replicación en un sitio secundario (SAN)</th>
</tr>
</thead>

<tr>
<td>Replicación de datos</td>
<td><p>Los metadatos acerca de los servidores locales y las máquinas virtuales se almacenan en el almacén de Site Recovery.</p> <p>Los datos replicados se almacenan en el almacenamiento de Azure.</p></td>
<td><p>Los metadatos acerca de los servidores locales y las máquinas virtuales se almacenan en el almacén de Site Recovery.</p> <p>Los datos replicados se almacenan en la ubicación especificada por el servidor de Hyper-V de destino.</p></td>
<td><p>Los metadatos acerca de los servidores locales y las máquinas virtuales se almacenan en el almacén de Site Recovery.</p> <p>Los datos replicados se almacenan en el almacenamiento de la matriz de destino.</p></td>
</tr>

<tr>
<td>Requisitos del almacén</td>
<td><p>Cuenta de Azure con el servicio Site Recovery</p></td>
<td><p>Cuenta de Azure con el servicio Site Recovery</p>
</td><td><p>Cuenta de Azure con el servicio Site Recovery</p></td>
</tr>

<tr>
<td>Replicación</td>
<td>Replique la máquina virtual de host de Hyper-V de origen al Almacenamiento de Azure. Conmutación por recuperación a la ubicación de origen.</td>
<td>Replique la máquina virtual de host de Hyper-V de origen al host de Hyper-V de destino. Conmutación por recuperación a la ubicación de origen.</td>
<td>Replique las máquinas virtuales del dispositivo de almacenamiento de SAN de origen en el dispositivo SAN de destino. Conmutación por recuperación a la ubicación de origen.</td>
</tr>

<tr>
<td>Máquina virtual</td>
<td>Unidad de disco duro de la máquina virtual almacenada en el almacenamiento de Azure</td>
<td>Unidad de disco duro de la máquina virtual almacenada en el host de Hyper-V</td>
<td>Disco duro de la máquina virtual almacenado en la matriz de almacenamiento de SAN</td>
</tr>

<tr>
<td>Almacenamiento de Azure</td>
<td>Necesario para almacenar discos duros de máquinas virtuales replicados</td>
<td>No aplicable</td>
<td>No aplicable</td>
</tr>

<tr>
<td>Matriz de almacenamiento de SAN</td>
<td><p>No aplicable</p></td>
<td>No aplicable</td>
<td>La matriz de almacenamiento de SAN debe estar disponible en los sitios de origen y de destino y administrada por VMM</td>
</tr>

<tr>
<td>Servidor VMM</td>
<td>Solo un servidor VMM en el sitio de origen. </td>
<td>Se recomienda utilizar servidores VMM en los sitios de origen y de destino. Puede replicar entre nubes en un único servidor VMM.</td>
<td>Servidores VMM en sitios de VMM de origen y destino. Las nubes deben contener al menos un clúster de Hyper-V.</td>
</tr>

<tr>
<td>Versión de VMM</td>
<td>System Center 2012 R2</td>
<td><p>System Center 2012 con SP1</p><p>System Center 2012 R2</p></td>
<td><p>System Center 2012 R2 con VMM Update Rollup 5.0</p></td>
</tr>

<tr>
<td>Configuración de VMM</td>
<td><p>Configurar nubes en sitios de origen y de destino</p><p>Configurar redes de VM en sitio de origen y de destino</p><p>Configurar clasificaciones de almacenamiento en sitios de origen y de destino <p>Instalar el proveedor en servidores VMM de origen y de destino</p></td>
<td><p>Configurar nubes en sitio de origen</p><p>Configurar almacenamiento de SAN</p><p>Configurar redes de VM en sitio de origen</p><p>Instalar el proveedor en servidor VMM de origen</p><p>Enable virtual machine protection</p></td>
<td><p>Configurar nubes en sitios de origen y de destino</p><p>Configurar redes de VM en sitios de origen y de destino</p><p>Instalar el proveedor en servidor VMM de origen y de destino</p><p>Enable virtual machine protection</p></td>
</tr>

<tr>
<td><p>Proveedor de Azure Site Recovery</p><p>Se utiliza para conectarse a través de HTTPS a Site Recovery</p></td>
<td>Instalar en servidor VMM de origen</td>
<td>Instalar en servidores VMM de origen y destino</td>
<td>Instalar en servidores VMM de origen y destino</td>
</tr>

<tr>
<td><p>Agente de los Servicios de recuperación de Azure</p><p>Se utiliza para conectarse a través de HTTPS a Site Recovery</p></td>
<td>Instalación en servidores host de Hyper-V</td>
<td>No se requiere</td>
<td>No se requiere</td>
</tr>

<tr>
<td>Puntos de recuperación de la máquina virtual</td>
<td><p>Establecer los puntos de recuperación por hora.</p> <p>Especifica cuánto tiempo se debe mantener un punto de recuperación (de 0 a 24 horas).</p></td>
<td><p>Establecer los puntos de recuperación por cantidad.</p> <p>Especifica cuántos puntos de recuperación adicionales se deben mantener (de 0 a 15). De forma predeterminada, se crea un punto de recuperación cada hora</p></td>
<td>Se establece en la configuración de matriz de almacenamiento</td>
</tr>

<tr>
<td>Asignación de red</td>
<td><p>Asignar redes VM a redes Azure.</p> <p>La asignación de red garantiza que todas las máquinas virtuales que conmutan por error en la misma red VM de origen se pueden conectar después. Además, si hay una puerta de enlace de red en la red Azure de destino, las máquinas virtuales se podrán conectar a las máquinas virtuales locales. </p><p>Si la asignación no está habilitada, solo las máquinas virtuales que conmutan por error en el mismo plan de recuperación se pueden conectar entre sí después a Azure.</p></td>
<td><p>Asignar redes VM de origen a redes VM de destino.</p> <p>La asignación de red se usa para colocar las máquinas virtuales replicadas en servidores host de Hyper-V óptimos, y garantiza que las máquinas virtuales asociadas a la red VM de origen estén asociadas a la red de destino asignada después de la conmutación por error. </p><p>Si la asignación no está habilitada, las máquinas virtuales replicadas no se conectarán a una red.</p></td>
<td><p>Asignar redes VM de origen a redes VM de destino.</p> <p>La asignación de red garantiza que las máquinas virtuales asociadas a la red VM de origen estén asociadas a la red de destino asignada después de la conmutación por error. </p><p>Si la asignación no está habilitada, las máquinas virtuales replicadas no se conectarán a una red.</p></td>
</tr>

<tr>
<td>Asignación de almacenamiento</td>
<td>No aplicable</td>
<td><p>Asigna las clasificaciones de almacenamiento en los servidores VMM de origen a las clasificaciones de almacenamiento en los servidores VMM de destino.</p> <p>Con la asignación habilitada, los discos duros de las máquinas virtuales en la clasificación de almacenamiento de origen estarán ubicados en la clasificación de almacenamiento de destino después de la conmutación por error.</p><p>Si la asignación de almacenamiento no está habilitada, los discos duros virtuales replicados se almacenarán en la ubicación predeterminada en el servidor host de Hyper-V de destino.</p></td>
<td><p>Asignaciones entre matrices de almacenamiento y grupos en sitios principales y secundarios.</p></td>
</tr>

</table>

## Pasos siguientes

Una vez finalizada la esta información general, [lea las prácticas recomendadas](site-recovery-best-practices.md) que le ayudarán a empezar a trabajar con la planeación de la implementación.
 

<!---HONumber=July15_HO2-->