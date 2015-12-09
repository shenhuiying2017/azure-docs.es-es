<properties
	pageTitle="¿Cómo funciona Azure Site Recovery?"
	description="Este artículo proporciona información general sobre la arquitectura de Site Recovery"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/29/2015"
	ms.author="raynew"/>

# ¿Cómo funciona Azure Site Recovery?

## Información acerca de este artículo

Este artículo describe la arquitectura subyacente de Site Recovery y los componentes que hacen que funcione. Después de leer este artículo, puede publicar sus preguntas en el [Foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Información general

Las organizaciones necesitan una estrategia de recuperación ante desastres y continuidad del negocio (BCDR) que determine cómo seguirán disponibles las aplicaciones, las cargas de trabajo y los datos durante los tiempos de inactividad planeados y no planeados, y cómo recuperar las condiciones de trabajo normales lo antes posible. Gran parte de su estrategia de BCDR se centrará en soluciones que mantengan los datos empresariales seguros y recuperables, y las cargas de trabajo disponibles continuamente en caso de desastre.

Site Recovery es un servicio de Azure que contribuye a su estrategia de BCDR mediante la coordinación de la replicación de servidores físicos locales y máquinas virtuales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error al sitio secundario para mantener disponibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal.

Site Recovery se puede usar en diversos escenarios y puede proteger varias cargas de trabajo.

- **Protección de máquinas virtuales de VMware**: para proteger máquinas virtuales de VMware locales puede replicarlas en Azure o en un centro de datos secundario.- **Protección de máquinas virtuales de Hyper-V**: para proteger máquinas virtuales de Hyper-V locales puede replicarlas en la nube (Azure) o en un centro de datos secundario.  
- **Protección de servidores físicos**: para proteger máquinas físicas que ejecutan Windows o Linux, puede replicarlas en Azure o en un centro de datos secundario.
- **Migración de máquinas virtuales**: puede usar Site Recovery para migrar máquinas virtuales de Azure IaaS de una región a otra o para migrar instancias de Windows AWS a máquinas virtuales de Azure IaaS.

Puede obtener un resumen completo de las implementaciones admitidas en [¿Qué es Azure Site Recovery?](site-recovery-overview.md) y [¿Qué cargas de trabajo puede proteger Azure Site Recovery?](site-recovery-workload.md)

## Replicación entre un servidor físico local o una máquina virtual de VMware y Azure

Si desea proteger máquinas virtuales de VMware o máquinas físicas de Windows/Linux replicándolas a Azure, esto es lo que necesitará.

**Ubicación** | **Lo que necesita** 
--- | --- 
 Local | **Servidor de proceso**: este servidor optimiza los datos de máquinas virtuales de VMware o máquinas físicas de Windows/Linux protegidas antes de enviarlos a Azure. También controla la instalación de inserción del componente Mobility Service en máquinas protegidas y realiza la detección automática de máquinas virtuales de VMware. <br/><br/> **Servidor vCenter de VMware**: si va a proteger máquinas virtuales de VMware, necesitará un servidor vCenter de VMwave que administre los hipervisores de vSphere<br/><br/> **Servidor ESX**: si va a proteger las máquinas virtuales de VMware, necesitará un servidor que ejecute ESX/ESXi versión 5.1 o 5.5 con las actualizaciones más recientes.<br/><br/> **Máquinas**: si va a proteger VMware deberá tener máquinas virtuales de VMware con herramientas de VMware instaladas y en ejecución. Si va a proteger máquinas físicas, deben ejecutar un sistema operativo Windows o Linux compatible. Consulte [lo que se admite](site-recovery-vmware-to-azure/#before-you-start). <br/><br/> **Mobility Service**: se instala en máquinas que desea proteger para capturar los cambios y comunicarlos con el servidor de procesos. <br/><br/>Componentes de terceros: esta implementación depende de algunos [componentes de terceros](http://download.microsoft.com/download/C/D/7/CD79E327-BF5A-4026-8FF4-9EB990F9CEE2/Third-Party_Notices.txt).
Azure | **Servidor de configuración**: máquina virtual de Azure Estándar A3 estándar que coordina la comunicación entre las máquinas protegidas, el servidor de procesos y los servidores de destino maestros en Azure. Configura la replicación y coordina la recuperación cuando se produce la conmutación por error. <br/><br/>**Servidor de destino maestro**: máquina virtual de Azure que contiene los datos replicados de las máquinas protegidos mediante VHD adjuntos creados en el almacenamiento de blobs de su cuenta de almacenamiento de Azure. Un servidor de destino maestro de conmutación por recuperación se ejecuta en instalaciones locales, por lo que puede producir una conmutación por recuperación de máquinas virtuales de Azure a máquinas virtuales de VMware. <br/><br/> **Almacén de Site Recovery**: al menos un almacén de Azure Site Recovery (configurado con una suscripción al servicio Site Recovery) <br/><br/> **Red virtual**: una red de Azure donde se encuentran el servidor de configuración y los servidores de destino maestros, en la misma suscripción y región que el servicio Site Recovery. <br/><br/> **Almacenamiento de Azure**: cuenta de almacenamiento de Azure para almacenar los datos replicados. Debe ser una cuenta premium o una cuenta estándar con redundancia geográfica, en la misma región que la suscripción a Site Recovery.


En este escenario, las comunicaciones se pueden producir mediante una conexión VPN a los puertos internos de la red Azure (mediante una VPN de sitio a sitio o Azure ExpressRoute) o mediante una conexión segura de Internet a los puntos de conexión públicos asignados en el servicio en la nube de Azure para las máquinas virtuales de configuración y de servidor de destino maestro.

En máquinas protegidas, Mobility Service envía los datos de replicación al servidor de procesos y envía los metadatos de replicación al servidor de configuración. El servidor de procesos se comunica con el servidor de configuración para la administración y el control de la información. Envía la información de replicación al servidor de destino maestro y optimiza y envía los datos replicados al servidor de destino maestro.

## Replicación de máquinas virtuales de Hyper-V en Azure (con VMM)

Si estas máquinas virtuales se encuentran en un host Hyper-V que se administra en una nube de System Center VMM, necesitará lo siguiente para poder replicarlas en Azure.

**Ubicación** | **Lo que necesita** 
--- | --- 
Local | **Servidor VMM**: al menos un servidor VMM con al menos una nube privada de VMM. El proveedor de Azure Site Recovery se instalará en cada servidor VMM<br/><br/>**Servidor Hyper-V**: al menos un servidor host Hyper-V ubicado en la nube VMM. En cada servidor Hyper-V, se instalará el agente de Servicios de recuperación de Microsoft. <br/><br/>**Máquinas virtuales**: al menos una máquina virtual en ejecución en el servidor Hyper-V. No se instala nada en la máquina virtual.
Azure | **Almacén de Site Recovery**: al menos un almacén de Azure Site Recovery (configurado con una suscripción al servicio Site Recovery) <br/><br/>**Cuenta de almacenamiento**: una cuenta de almacenamiento de Azure en la misma suscripción que el servicio Site Recovery. Las máquinas replicadas se almacenan en el almacenamiento de Azure. 

En este escenario, el proveedor que se ejecuta en el servidor VMM coordina y organiza la replicación con el servicio Site Recovery a través de Internet. Los datos se replican entre el agente de Servicios de recuperación que se ejecuta en el servidor Hyper-V local y el almacenamiento de Azure a través de HTTPS 443. Las comunicaciones del proveedor y el agente son seguras y cifradas. También se cifran los datos replicados en el almacenamiento de Azure.

![De VMM local a Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

## Replicación de máquinas virtuales de Hyper-V en Azure (sin VMM)

Si las máquinas virtuales no están administradas por un servidor de System Center VMM, esto es lo que debe hacer para replicarlas en Azure

**Ubicación** | **Lo que necesita**
--- | --- 
 Local | **Servidor Hyper-V**: al menos un servidor host Hyper-V. En cada servidor Hyper-V, se instalará el proveedor de Azure Site Recovery y el agente de Servicios de recuperación de Microsoft. <br/><br/>**Máquinas virtuales**: al menos una máquina virtual en ejecución en el servidor Hyper-V. No se instala nada en la máquina virtual.
Azure | **Almacén de Site Recovery**: al menos un almacén de Azure Site Recovery (configurado con una suscripción al servicio Site Recovery) <br/><br/>**Cuenta de almacenamiento**: una cuenta de almacenamiento de Azure en la misma suscripción que el servicio Site Recovery. Las máquinas replicadas se almacenan en el almacenamiento de Azure.

En este escenario, el proveedor en ejecución en el servidor Hyper-V coordina y organiza la replicación con el servicio de Site Recovery a través de Internet. Los datos se replican entre el agente de Servicios de recuperación que se ejecuta en el servidor Hyper-V local y el almacenamiento de Azure a través de HTTPS 443. Las comunicaciones del proveedor y el agente son seguras y cifradas. También se cifran los datos replicados en el almacenamiento de Azure.

![De VMM local a Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)



## Replicación de máquinas virtuales de Hyper-V en un centro de datos secundario

Si desea replicar las máquinas virtuales de Hyper-V en un centro de datos secundario para protegerlas, deberá hacer lo siguiente. Tenga en cuenta que solo puede hacerlo si el servidor host Hyper-V se administra en una nube de System Center VMM.

**Ubicación** | **Lo que necesita** 
--- | --- 
 Local | **Servidor VMM**: un servidor VMM en el sitio principal y otro en el sitio secundario. El proveedor de Azure Site Recovery se instalará en cada servidor VMM.<br/><br/>**Servidor Hyper-V**: al menos un servidor host Hyper-V ubicado en una nube de VMM en los sitios primario y secundario. No se instala nada en los servidores Hyper-V <br/><br/> **Máquinas virtuales**: al menos una máquina virtual en ejecución en el servidor Hyper-V. No se instala nada en la máquina virtual.
Azure | **Almacén de Site Recovery**: al menos un almacén de Azure Site Recovery (configurado con una suscripción al servicio Site Recovery). 

En este escenario, el proveedor en el servidor VMM coordina y organiza la replicación con el servicio Site Recovery a través de Internet. Los datos se replican entre los servidores host Hyper-V principales y secundarios a través de Internet mediante autenticación Kerberos o de certificado. Las comunicaciones tanto del proveedor como entre los servidores host Hyper-V son seguras y cifradas.

![De local a local](./media/site-recovery-components/arch-onprem-onprem.png)

## Replicación de máquinas virtuales de Hyper-V en un centro de datos secundario con replicación SAN

Si las máquinas virtuales se encuentran en un host Hyper-V que se administra en una nube de System Center VMM y usa almacenamiento SAN, necesitará lo siguiente para replicar entre dos centros de datos.

**Ubicación** | **Lo que necesita** 
--- | --- 
 Centro de datos principal | **Matriz SAN**: una [matriz SAN admitida](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) administrada por el servidor VMM principal. La matriz SAN comparte una infraestructura de red con otra matriz de SAN en el sitio secundario <br/><br/> **Servidor VMM**: al menos un servidor VMM con una o más nubes de VMM y grupos de replicación configurados. El proveedor de Azure Site Recovery se instalará en cada servidor VMM. <br/><br/> **Servidor Hyper-V**: al menos un servidor host Hyper-V con máquinas virtuales, situado en un grupo de replicación. Nada se instala en los servidores host Hyper-V.<br/><br/> **Máquinas virtuales**: al menos una máquina virtual en ejecución en el servidor host Hyper-V. No se instala nada en la máquina virtual. 
Centro de datos secundario | **Matriz SAN**: una [matriz SAN admitida](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) administrada por el servidor VMM secundario. <br/><br/>**Servidor VMM**: al menos un servidor VMM con una o más nubes VMM.<br/><br/> **Servidor Hyper-V**: al menos un servidor host Hyper-V. 
Azure | **Almacén de Site Recovery**: al menos un almacén de Azure Site Recovery (configurado con una suscripción al servicio Site Recovery)

En este escenario, el proveedor en el servidor VMM coordina y organiza la replicación con el servicio Site Recovery a través de Internet. Los datos se replican entre las matrices de almacenamiento principal y secundaria mediante replicación SAN sincrónica.

![De local a local](./media/site-recovery-components/arch-onprem-onprem-san.png)



## Ciclo de vida de protección de Hyper-V

Este flujo de trabajo muestra el proceso de protección, replicación y conmutación por error de máquinas virtuales Hyper-V.

1. **Habilitar la protección**: se configura el almacén de Site Recovery, se configuran las opciones de replicación para una nube de VMM o un sitio de Hyper-V y se habilita la protección de las máquinas virtuales. Se inicia un trabajo llamado **Habilitar la protección** y se puede supervisar en la pestaña **Trabajos**. El trabajo comprueba que el equipo cumple los requisitos previos y después se invoca el método [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) donde se configura la replicación en Azure con la configuración establecida. El trabajo **Habilitar la protección** también invoca el método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) para inicializar una replicación completa de la máquina virtual.
2. **Replicación inicial**: se toma una instantánea de la máquina virtual y se replican los discos duros virtuales uno por uno hasta que todos se copian en Azure o en el centro de datos secundario. El tiempo para completar esta tarea depende del ancho de banda de red, del tamaño y del método de replicación inicial elegido. Si se producen cambios en el disco mientras la replicación inicial está en curso, el seguimiento de replicaciones de Réplica de Hyper-V realiza un seguimiento de esos cambios en registros de replicación de Hyper-V (.hrl) que se encuentran en la misma carpeta que los discos. Cada disco tiene un archivo .hrl asociado que se enviará al almacenamiento secundario. Tenga en cuenta que los archivos de instantáneas y de registro consumen recursos de disco mientras la replicación inicial está en curso. Cuando la replicación inicial finaliza, se elimina la instantánea de la máquina virtual y se sincronizan y se combinan los cambios diferenciales del disco en el registro.
3. **Finalizar la protección**: una vez finalizada la replicación inicial, el trabajo **Finalizar la protección** configura la red y otras opciones posteriores a la replicación, y la máquina virtual se protege. Si va a replicar en Azure, debe ajustar la configuración de la máquina virtual para que esté preparada para conmutación por error. En este momento puede ejecutar una conmutación por error de prueba para comprobar que todo funciona según lo esperado.
4. **Replicación**: después de la replicación inicial, se produce una sincronización diferencial con el método y la configuración de replicación. 
	- **Error de replicación**: si se produce un error en la replicación diferencial y una réplica completa sería costosa en términos de ancho de banda o tiempo, se produce una resincronización. Por ejemplo, si los archivos .hrl alcanzan el 50% del tamaño del disco, la máquina virtual se marcará para resincronización. La resincronización minimiza la cantidad de datos que se envían; para ello, calcula las sumas de comprobación de las máquinas virtuales de origen y destino y envía solo los datos diferenciales. Una vez finalizada la resincronización, se debe reanudar la replicación diferencial. De forma predeterminada, la resincronización está programada para ejecutarse automáticamente fuera del horario de oficina, pero puede resincronizar una máquina virtual manualmente.
	- **Error de replicación**: si se produce un error de replicación, se realiza un reintento de forma predefinida. Si el error es irrecuperable, como un error de autenticación o de autorización, o una máquina de réplica tiene un estado no válido, no se realizará ningún reintento. Si se produce un error irrecuperable, como un error de red o espacio en disco o memoria insuficiente, se producirán reintentos con un intervalo cada vez mayor entre cada reintento (1, 2, 4, 8, 10 y después cada 30 minutos).
4. **Conmutaciones por error planeadas y no planeadas**: las conmutaciones por error planeadas o no planeadas se ejecutan cuando es necesario. Si ejecuta una conmutación por error planeada, asegúrese de que las máquinas virtuales de origen están apagadas para que no haya pérdida de datos. Una vez creadas las máquinas virtuales de réplica, están en estado pendiente de confirmación. Deberá confirmarlas para completar la conmutación por error a menos que vaya a replicar con SAN, en cuyo caso la confirmación es automática. Una vez configurado y en marcha el sitio primario, puede producirse la conmutación por recuperación. Si replicó en Azure, la replicación inversa es automática. De lo contrario, inicie una replicación inversa.
 

![flujo de trabajo](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## Replicación de máquinas virtuales de VMware y servidores físicos en Azure

Puede replicar máquinas virtuales de VMware y servidores físicos (Windows o Linux) en Azure mediante una conexión de sitio a sitio VPN o a través de Internet.

### Replicación mediante una conexión de sitio a sitio VPN (o ExpressRoute) en Azure

![De una máquina de VMware o física a Azure a través de Internet](./media/site-recovery-components/arch-onprem-azure-vmware-vpn.png)

#### Replicación a través de Internet

![De una máquina de VMware o física a Azure a través de Internet](./media/site-recovery-components/arch-onprem-azure-vmware-internet.png)

## Replicación entre servidores físicos locales o máquinas virtuales de VMware en centros de datos principales y secundarios

Si para proteger máquinas virtuales de VMware o máquinas físicas de Windows/Linux desea replicarlas entre dos centros de datos locales, esto es lo que necesita.

**Ubicación** | **Lo que necesita** 
--- | --- 
 Centro de datos principal local | **Servidor de proceso**: configure el componente de servidor de proceso en el sitio principal para administrar el almacenamiento en caché, la compresión y la optimización de los datos. También controla la instalación por inserción del agente unificada en las máquinas que desea proteger. <br/><br/> **Protección de VMware**: si va a proteger máquinas virtuales de VMware, necesitará un hipervisor EXS/ESXi de VMware o un servidor vCenter de VMware que administre varios hipervisores<br/><br/> **Protección de servidores físicos**: si está protegiendo máquinas físicas, deben ejecutar Windows o Linux. <br/><br/> **Unified Agent**: se instala en las máquinas que desea proteger y en la máquina que actúa como servidor de destino maestro. Actúa como un proveedor de comunicación entre los componentes de InMage.
Centro de datos secundario local | **Servidor de configuración**: el servidor de configuración es el primer componente que se instala, y se instala en el sitio secundario para administrar, configurar y supervisar la implementación, ya sea mediante el sitio web de administración o la consola de vContinuum. El servidor de configuración también incluye el mecanismo de inserción para una implementación remota de Unified Agent. Hay un solo servidor de configuración único en una implementación y debe instalarse en una máquina que ejecuta Windows Server 2012 R2. <br/><br/> **Servidor vContinuum**: instálelo en la misma ubicación (sitio secundario) que el servidor de configuración. Proporciona una consola para administrar y supervisar su entorno protegido. En una instalación predeterminada, el servidor vContinuum es el primer servidor de destino maestro y tiene instalado Unified Agent. <br/><br/> **Servidor de destino maestro**: el servidor de destino maestro contiene los datos replicados. Recibe los datos del servidor de proceso, crea una máquina de réplica en el sitio secundario y contiene los puntos de retención de datos. El número de servidores de destino maestros que necesita depende del número de equipos que va a proteger. Si desea realizar una conmutación por recuperación al sitio principal, necesitará un servidor de destino maestro allí también. 
Azure | **Almacén de Site Recovery**: al menos un almacén de Azure Site Recovery (configurado con una suscripción al servicio Site Recovery). Descargue InMage Scout para configurar la implementación después de crear el almacén. Instale también la actualización más reciente para todos los servidores del componente InMage.


En este escenario, los cambios diferenciales de la replicación se envían desde Unified Agent que se ejecuta en el equipo protegido al servidor de proceso. El servidor de proceso optimiza estos datos y loso transfiere al servidor de destino maestro en el sitio secundario. El servidor de configuración administra el proceso de replicación.




## Pasos siguientes

[Prepárese para la implementación](site-recovery-best-practices.md).

<!---HONumber=AcomDC_1203_2015-->