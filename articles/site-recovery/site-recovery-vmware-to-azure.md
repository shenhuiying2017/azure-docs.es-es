<properties
	pageTitle="Replicación de máquinas virtuales de VMware y servidores físicos en Azure con Azure Site Recovery en el Portal de Azure | Microsoft Azure"
	description="Se describe cómo implementar Azure Site Recovery para orquestar la replicación, la conmutación por error y la recuperación de máquinas virtuales de VMware locales y servidores físicos de Windows o Linux en Azure con la utilización del Portal de Azure."
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
	ms.topic="article"
	ms.date="05/09/2016"
	ms.author="raynew"/>

# Replicación en Azure de máquinas físicas y máquinas virtuales de VMware con Azure Site Recovery con la utilización del Portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](site-recovery-vmware-to-azure.md)
- [Azure clásico](site-recovery-vmware-to-azure-classic.md)
- [Azure clásico (heredado)](site-recovery-vmware-to-azure-classic-legacy.md)

Bienvenido a Azure Site Recovery. Utilice este artículo si desea replicar en Azure máquinas virtuales de VMware locales o servidores físicos de Windows/Linux con Azure Site Recovery en el Portal de Azure.

> [AZURE.NOTE] Azure tiene dos [modelos de implementación](../resource-manager-deployment-model.md) diferentes para crear recursos y trabajar con ellos: Azure Resource Manager (ARM) y el clásico. Azure también tiene dos portales: el Portal de Azure clásico que admite el modelo de implementación clásico y el Portal de Azure que es compatible con ambos modelos de implementación.

Site Recovery en el Portal de Azure proporciona una serie de nuevas características:

- Los servicios de Copia de seguridad de Azure y Azure Site Recovery se combinan en un único almacén de Servicios de recuperación para que pueda configurar y administrar la continuidad empresarial y la recuperación ante desastres (BCDR) desde una sola ubicación. En un panel unificado puede supervisar y administrar operaciones en los sitios locales y en la nube pública de Azure.
- Los usuarios con suscripciones de Azure aprovisionadas con el programa Proveedor de soluciones en la nube (CSP) pueden administrar ya las operaciones de Site Recovery en el Portal de Azure.
- Site Recovery en el Portal de Azure puede replicar máquinas en cuentas de almacenamiento de ARM. En una conmutación por error, Site Recovery crea máquinas virtuales basadas en ARM en Azure.
- Site Recovery sigue siendo compatible con la replicación en cuentas de almacenamiento clásicas. En una conmutación por error, Site Recovery crea máquinas virtuales mediante el modelo clásico.

Después de leer este artículo, publique los comentarios en la parte inferior de los comentarios de Disqus. Formule cualquier pregunta técnica en el [Foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Información general

Las organizaciones necesitan una estrategia de recuperación ante desastres y continuidad empresarial (BCDR) que determine cómo seguirán en funcionamiento y disponibles las aplicaciones, las cargas de trabajo y los datos durante los tiempos de inactividad planeados y no planeados, y cómo recuperar las condiciones de funcionamiento normales lo antes posible. Su estrategia de BCDR se centra en soluciones que mantengan los datos empresariales seguros y recuperables, y garanticen que las cargas de trabajo estarán disponibles continuamente en caso de desastre.

Site Recovery es un servicio de Azure que contribuye a su estrategia de BCDR mediante la coordinación de la replicación de servidores físicos locales y máquinas virtuales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error a la ubicación secundaria para mantener disponibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal. Más información en [¿Qué es Site Recovery?](site-recovery-overview.md)

Este artículo proporciona toda la información necesaria para replicar en Azure máquinas virtuales de VMware locales y servidores físicos de Windows/Linux. Incluye información general sobre la arquitectura, información de planeamiento y pasos de implementación para la configuración de Azure, los servidores locales, la configuración de replicación y el planeamiento de la capacidad. Después de configurar la infraestructura, puede habilitar la replicación en los equipos que desea proteger y comprobar que funciona la conmutación por error.

## Ventajas empresariales

- Site Recovery proporciona protección fuera del sitio para cargas de trabajo empresariales y aplicaciones que se ejecutan en servidores físicos y máquinas virtuales de VMware.
- El portal de Servicios de recuperación proporciona una ubicación única para configurar, administrar y supervisar la replicación, la conmutación por error y la recuperación.
- Site Recovery puede detectar automáticamente las máquinas virtuales de VMware agregadas a los hosts de vSphere.
- Puede ejecutar fácilmente conmutaciones por error desde su infraestructura local a Azure y conmutaciones por recuperación (restauración) desde Azure a servidores de máquinas virtuales de VMware del sitio local.
- Puede habilitar varias máquinas virtuales y crear grupos de replicación para que las cargas de trabajo de la aplicación en capas entre varias máquinas se repliquen al mismo tiempo. Todas las máquinas de un grupo de replicación tienen puntos de recuperación coherentes con los bloqueos y coherentes con la aplicación cuando conmutan por error. Para la conmutación por error, puede recopilar varias máquinas en planes de recuperación, para que las cargas de trabajo de la aplicación conmuten por error al mismo tiempo.

## Arquitectura del escenario

Estos son los componentes del escenario:

- **Servidor de configuración**: una máquina local que coordina la comunicación y administra los procesos de replicación y recuperación de datos. En esta máquina ejecutará un archivo de instalación única para instalar estos componentes adicionales y el servidor de configuración:
	- **Servidor de procesos**: actúa como una puerta de enlace de replicación. Recibe datos de replicación provenientes de máquinas de origen, los optimiza con almacenamiento en caché, compresión y cifrado y los envía al almacenamiento de Azure. También controla la instalación de inserción del Servicio de movilidad en máquinas protegidas y realiza al detección automática de máquinas virtuales de VMware. El servidor de procesos predeterminado está instalado en el servidor de configuración. Puede implementar servidores de procesos independientes adicionales para escalar la implementación.
	- **Servidor de destino maestro**: controla los datos de replicación durante la conmutación por recuperación desde Azure.

- **Mobility Service**: este componente está implementado en cada máquina (máquina virtual de VMware o servidor físico) que desea replicar en Azure. Captura las escrituras de datos en la máquina y los reenvía al servidor de procesos.
- **Azure**: no es necesario crear ninguna máquina virtual de Azure para controlar la replicación y la conmutación por error en Azure. No necesita una suscripción, una cuenta de almacenamiento para almacenar los datos replicados y una red virtual de Azure para que las máquinas virtuales de Azure se conecten a una red después de la conmutación por error. La red y la cuenta de almacenamiento deben estar en la misma región que el almacén de Servicios de recuperación.
- **Conmutación por recuperación**: cuando esté listo para realizar la conmutación por recuperación desde Azure al sitio local después de una conmutación por error, necesitará crear una máquina virtual de Azure como un servidor de procesos temporal. Puede eliminarlo una vez finalizada la conmutación por recuperación. Para la conmutación por recuperación, también necesitará una conexión VPN (o Azure ExpressRoute) entre el sitio local y la red de Azure donde se encuentran las máquinas virtuales de Azure. Si el tráfico de conmutación por recuperación es intenso, es posible que también necesite configurar una máquina local del servidor de destino maestro dedicado. Para el tráfico menos intenso, se puede utilizar el servidor de destino maestro predeterminado que se ejecuta en el servidor de configuración.


En el gráfico se muestra cómo interactúan estos componentes.

![arquitectura](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

**Figura 1: VMware/servidor físico en Azure** (creada por Henry Robalino)

## Requisitos previos de Azure

Aquí está lo que necesita en Azure para implementar este escenario.

**Requisito previo** | **Detalles**
--- | ---
**Cuenta de Azure**| Necesitará una cuenta de [Microsoft Azure](http://azure.microsoft.com/). Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) sobre los precios de Site Recovery.
**Almacenamiento de Azure** | Los datos replicados se almacenan en el almacenamiento de Azure y las máquinas virtuales de Azure se crean cuando se produce la conmutación por error. <br/><br/>Para almacenar datos necesitará una cuenta de almacenamiento estándar o premium en la misma región que el almacén de Servicios de recuperación.<br/><br/>Puede usar una cuenta de almacenamiento LRS o GRS. Se recomienda GRS para que los datos sean resistentes si se produce una interrupción regional o si no se puede recuperar la región principal. [Más información](../storage/storage-redundancy.md).<br/><br/> El [almacenamiento premium](../storage/storage-premium-storage.md) se utiliza normalmente para las máquinas virtuales que necesitan un alto rendimiento constante de E/S y latencia baja para hospedar cargas de trabajo intensivas de E/S.<br/><br/> Si desea utilizar una cuenta premium para almacenar los datos replicados, también necesitará una cuenta de almacenamiento estándar para almacenar registros de replicación que capturan los cambios continuos de los datos locales.<br/><br/> Tenga en cuenta que las cuentas de almacenamiento creadas en el Portal de Azure no pueden moverse entre grupos de replicación.<br/><br/> [Más información sobre](../storage/storage-introduction.md) almacenamiento de Azure.
**Red de Azure** | Necesitará una red virtual de Azure a la que se conectarán las máquinas virtuales de Azure cuando se produzca la conmutación por error. La red virtual de Azure debe estar en la misma región que el almacén de Servicios de recuperación.
**Conmutación por recuperación desde Azure** | Necesitará un servidor de procesos temporal configurado como una máquina virtual de Azure. Se puede crear cuando esté listo para realizar la conmutación por recuperación y eliminarlo después de completar dicha conmutación.<br/><br/> Para la conmutación por recuperación, necesitará una conexión VPN (o Azure ExpressRoute) desde la red de Azure al sitio local.

## Requisitos previos del servidor de configuración

Va a configurar una máquina local como el servidor de configuración.

**Requisito previo** | **Detalles**
--- | ---
**Servidor de configuración**| Necesita una máquina virtual o física local que ejecute Windows Server 2012 R2. Todos los componentes locales de Site Recovery están instalados en esta máquina.<br/><br/>Para la replicación de máquinas virtuales de VMware se recomienda implementar el servidor como una máquina virtual de VMware de alta disponibilidad. Si va a replicar máquinas físicas, la máquina puede ser un servidor físico.<br/><br/> La conmutación por recuperación en el sitio local desde Azure siempre será en máquinas virtuales de VMware, independientemente de si realizó conmutación por error de máquinas virtuales o servidores físicos. Si no implementa el servidor de configuración como una máquina virtual de VMware, necesitará configurar un servidor de destino maestro independiente como una máquina virtual de VMware para recibir tráfico de conmutación por recuperación.<br/><br/>Si el servidor es una máquina virtual de VMware, el tipo de adaptador de red debe ser VMXNET3. Si utiliza otro tipo de adaptador de red, necesitará instalar una [actualización de VMware](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1) en el servidor vSphere 5.5.<br/><br/>El servidor debe tener una dirección IP estática.<br/><br/>El servidor no debe ser un controlador de dominio.<br/><br/>El nombre de host del servidor debe tener 15 caracteres o menos.<br/><br/>El sistema operativo debe estar en inglés únicamente.<br/><br/> Necesitará instalar VMware vSphere PowerCLI 6.0. en el servidor de configuración.<br/><br/>El servidor de configuración necesita acceso a Internet. Se requiere acceso de salida como sigue:<br/><br/>acceso temporal en HTTP 80 durante la instalación de los componentes de Site Recovery (para descargar MySQL)<br/><br/>acceso de salida continuo en HTTPS 443 para la administración de replicación<br/><br/>acceso de salida continuo en HTTPS 9443 para el tráfico de replicación (se puede modificar este puerto)<br/><br/>El servidor también necesitará acceso a las siguientes direcciones URL para que se pueda conectar a Azure: *.hypervrecoverymanager.windowsazure.com; *.accesscontrol.windows.net; *.backup.windowsazure.com; *.blob.core.windows.net; *.store.core.windows.net<br/><br/>Si tiene reglas de firewall basadas en direcciones IP en el servidor, compruebe que las reglas permitan la comunicación con Azure. Debe permitir los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el protocolo HTTPS (433).<br/><br/>Permita los intervalos de direcciones IP para la región de Azure de su suscripción y para el oeste de EE. UU.<br/><br/>Permita esta dirección URL para descargar MySQL: .http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.


## Requisitos previos de host vCenter/vSphere de VMware

**Requisito previo** | **Detalles**
--- | ---
**vSphere**| Necesita uno o varios hipervisores de VMware vSphere.<br/><br/>Los hipervisores deben ejecutar las versiones 6.0, 5.5 o 5.1 de vSphere con las últimas actualizaciones.<br/><br/>Recomendamos que los hosts vSphere y los servidores vCenter se encuentren en la misma red que el servidor de procesos (se trata de la red en que está ubicado el servidor de configuración a menos que se haya configurado un servidor de procesos dedicado).
**vCenter** | Se recomienda implementar un servidor VMware vCenter para administrar los hosts vSphere. Debe ejecutar la versión 6.0 o 5.5 de vCenter con las últimas actualizaciones.<br/><br/>Tenga en cuenta que Site Recovery no admite las nuevas características de vCenter y vSphere 6.0, como Cross vCenter vMotion, volúmenes virtuales y DRS de almacenamiento. La compatibilidad de Site Recovery está limitada a las características que también estaban disponibles en la versión 5.5.


## Requisitos previos de equipos protegidos

**Requisito previo** | **Detalles**
--- | ---
**Máquinas virtuales de VMware locales)** | Las máquinas virtuales de VMware que desea proteger deben tener herramientas de VMware instaladas y en ejecución.<br/><br/> Las máquinas que desea proteger deben cumplir con los [requisitos previos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) para crear máquinas virtuales de Azure.<br/><br/>La capacidad de disco individual en máquinas protegidas no debe ser superior a 1023 GB. Una máquina virtual puede tener hasta 64 discos (es decir, hasta 64 TB). <br/><br/>No se admiten los clústeres invitados de discos compartidos.<br/><br/>No se admite el arranque de Unified Extensible Firmware Interface (UEFI)/Extensible Firmware Interface (EFI).<br/><br/>Los nombres de las máquinas deben tener entre 1 y 63 caracteres (letras, números y guiones). El nombre debe comenzar con una letra o un número y terminar con una letra o un número. Después de haber habilitado la replicación de una máquina, puede modificar el nombre de Azure.<br/><br/>Si la máquina virtual de origen tiene formación de equipos NIC, se convertirá en una sola NIC después de la conmutación por error en Azure.<br/><br/>Si las máquina virtuales protegidas tienen un disco iSCSI, Site Recovery convierte el disco iSCSI de la máquina virtual protegida en un archivo VHD cuando la máquina virtual conmuta por error en Azure. Si la máquina virtual de Azure puede llegar al destino iSCSI, se conectará con él y, principalmente, verá dos discos: el disco VHD en la máquina virtual de Azure y el disco iSCSI de origen. En este caso deberá desconectar el destino iSCSI que aparece en la máquina virtual de Azure.
**Equipos Windows (físico o VMware)** | El equipo debe ejecutar un sistema operativo de 64 bits compatible: Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 con SP1 como mínimo.<br/><br/> El sistema operativo debe instalarse en la unidad C:\\. El disco del sistema operativo debe ser un disco básico de Windows y no dinámico. El disco de datos puede ser dinámico.<br/><br/>Site Recovery admite máquinas virtuales con un disco RDM. Durante la conmutación por recuperación, Site Recovery reutilizará el disco RDM si la máquina virtual de origen y el disco RDM originales están disponibles. Si no están disponibles, durante la conmutación por recuperación, Site Recovery creará un nuevo archivo VMDK para cada disco.
**Equipos Linux** | Necesitará un sistema operativo compatible de 64 bits: Red Hat Enterprise Linux 6.7; Centos 6.5, 6.6,6.7; Oracle Enterprise Linux 6.4, 6.5 con la ejecución del kernel de Red Hat compatible o Unbreakable Enterprise Kernel Release 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>Los archivos /etc/hosts de las máquinas protegidas deben contener entradas que asignen el nombre de host local a las direcciones IP asociadas con todos los adaptadores de red.<br/><br/>Si quiere conectarse a una máquina virtual de Azure que ejecute Linux tras la conmutación por error con la utilización de un cliente de Secure Shell (ssh), asegúrese de que el servicio de Secure Shell en la máquina protegida está configurado para iniciarse automáticamente en el arranque del sistema y que las reglas de firewall permiten una conexión ssh a ella.<br/><br/>El nombre de host, los puntos de montaje, los nombres de dispositivo, las rutas de acceso de sistema de Linux y los nombres de archivo (por ejemplo, /etc/; /usr) deben estar en inglés únicamente.<br/><br/>La protección solo puede habilitarse para máquinas con Linux con el almacenamiento siguiente: sistema de archivos (EXT3, ETX4, ReiserFS, XFS); Multipath software-Device Mapper (múltiples rutas de acceso); administrador de volúmenes: (LVM2). No se admiten servidores físicos con almacenamiento de controlador HP CCISS. El sistema de archivos ReiserFS solo se admite en SUSE Linux Enterprise Server 11 SP3.<br/><br/>Site Recovery admite máquinas virtuales con un disco RDM. Durante la conmutación por recuperación para Linux, Site Recovery no reutiliza el disco RDM. En cambio, crea un nuevo archivo VMDK para cada disco RDM correspondiente.<br/><br/>Asegúrese de establecer la configuración de disk.enableUUID=true en los parámetros de configuración de la máquina virtual de VMware. Si no existe, cree la entrada. Es necesario a fin de proporcionar un UUID uniforme al VMDK, para que se monte correctamente. Agregar esta configuración también garantiza que los cambios diferenciales solo se transfieran a local durante la conmutación por recuperación, y no una replicación completa.
**Servicio de movilidad** | **Windows**: para insertar automáticamente una instancia de Mobility Service en máquinas virtuales que ejecutan Windows, deberá proporcionar una cuenta de administrador (administrador local en el equipo Windows) para que el servidor de procesos pueda realizar una instalación de inserción.<br/><br/> **Linux**: para insertar automáticamente una instancia de Mobility Service en máquinas virtuales que ejecutan Linux, necesitará crear una cuenta que el servidor de procesos pueda utilizar para realizar una instalación de inserción.<br/><br/> De manera predeterminada, se replican todos los discos en una máquina. Para [excluir un disco de la replicación](#exclude-disks-from-replication), la instancia de Mobility Service debe instalarse manualmente en la máquina antes de habilitar la replicación.

## Preparación de la implementación

Para preparar la implementación deberá:

1. [Configurar una red de Azure](#set-up-an-azure-network) en la que las máquinas virtuales de Azure se encuentran cuando se ponen en marcha después de la conmutación por error. Además, para realizar la conmutación por recuperación, necesitará configurar una conexión VPN (o Azure ExpressRoute) desde la red de Azure al sitio local.
2. [Configurar una cuenta de almacenamiento de Azure](#set-up-an-azure-storage-account) para datos replicados.
3. [Preparar una cuenta](#prepare-an-account-for-automatic-discovery) en el servidor vCenter o en los hosts de vSphere, para que Site Recovery pueda detectar automáticamente las máquinas virtuales de VMware que se agregan.
4. [Preparar el servidor de configuración](#prepare-the-configuration-server) para asegurarse de que puede acceder a las direcciones URL necesarias e instalar vSphere PowerCLI 6.0.


### Configuración de una red de Azure

- La red debe estar en la misma región de Azure que aquella en la que va a implementar el almacén de Servicios de recuperación.
- Según el modelo de recursos que desee usar para las máquinas virtuales de Azure conmutadas por error, va a configurar la red de Azure en [modo ARM](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o en [modo clásico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Para realizar una conmutación por recuperación desde Azure en el sitio de VMware local, necesita una conexión VPN (o una conexión Azure ExpressRoute) desde la red de Azure en que se encuentran las máquinas virtuales de Azure replicadas en la red local en que se encuentra el servidor de configuración.
- [Más información](../vpn-gateway/vpn-gateway-site-to-site-create.md) sobre los modelos de implementación compatibles para conexiones VPN de sitio a sitio y sobre la [configuración de una conexión](../vpn-gateway/vpn-gateway-site-to-site-create.md#create-your-virtual-network).
- También puede configurar [Azure ExpressRoute](../expressroute/expressroute-introduction.md). [Más información](../expressroute/expressroute-howto-vnet-portal-classic.md) sobre cómo configurar una red de Azure con ExpressRoute.

### Configuración de una cuenta de almacenamiento de Azure

- Necesitará una cuenta de almacenamiento estándar o premium para almacenar los datos replicados en Azure. La cuenta debe estar en la misma región que el almacén de Servicios de recuperación. Según el modelo de recursos que desee usar para las máquinas virtuales de Azure conmutadas por error, configurará una cuenta en [modo ARM](../storage/storage-create-storage-account.md) o en [modo clásico](../storage/storage-create-storage-account-classic-portal.md).
- Si utiliza una cuenta premium para los datos replicados, necesita crear una cuenta de almacenamiento estándar adicional para almacenar los registros de replicación que capturan los cambios continuos de los datos locales.  

### Preparación de una cuenta de detección automática

El servidor de procesos de Site Recovery puede detectar automáticamente las máquinas virtuales de VMware en hosts de vSphere o en un servidor vCenter que administra los hosts. Para ejecutar credenciales de Site Recovery para detección automática que puedan acceder al servidor VMware. Esto es relevante si va a replicar solo máquinas físicas.

1. Para usar una cuenta dedicada para la detección automática, cree un rol (por ejemplo, Azure\_Site\_Recovery) en el nivel de vCenter con los [permisos necesarios](#vmware-account-permissions).
2. Cree un nuevo usuario en el servidor vCenter o en el host vSphere y asigne el rol al usuario. Más adelante permitirá a Site Recovery obtener información sobre estas credenciales, a fin de poder realizar la detección automática.

	>[AZURE.NOTE] Una cuenta de usuario de vCenter que cuenta con el rol de solo lectura puede ejecutar la conmutación por error, pero no puede apagar las máquinas de origen protegidas. Si desea apagar esas máquinas, necesitará el rol [Azure\_Site\_Recovery](#vmware-account-permissions). Si solo migra máquinas virtuales desde VMware a Azure y no necesita realizar conmutación por recuperación, el rol de solo lectura es suficiente.

### Preparación del servidor de configuración

1.	Asegúrese de que la máquina que se utiliza para el servidor de configuración cumple con los [requisitos previos](#configuration-server-prerequisites). En concreto, asegúrese de que la máquina está conectada a Internet con estas opciones:

	- Permitir el acceso a estas direcciones URL: *.hypervrecoverymanager.windowsazure.com; *.accesscontrol.windows.net; *.backup.windowsazure.com; *.blob.core.windows.net; *.store.core.windows.net
- Permitir el acceso a [http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi](http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi) para descargar MySQL.
	- Permitir la comunicación del firewall en Azure con los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el protocolo HTTPS (433).

2.	Descargue e instale [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) en el servidor de configuración.


## Creación de un almacén de Servicios de recuperación

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Nueva** > **Administración** > **Backup and Site Recovery (OMS)** (Copia de seguridad y recuperación del sitio (OMS)). También puede hacer clic en **Examinar** > **Almacén de Servicios de recuperación** > **Agregar**.

	![Almacén nuevo](./media/site-recovery-vmware-to-azure/new-vault3.png)

3. En **Nombre**, especifique un nombre descriptivo para identificar el almacén. Si tiene más de una suscripción, seleccione una de ellas.
4. [Cree un nuevo grupo de recursos](../resource-group-portal.md#create-resource-group) o seleccione uno existente. Especifique una región de Azure. Las máquinas se replicarán en esta región. Tenga en cuenta que las redes y el almacenamiento de Azure utilizados para Site Recovery deberán estar en la misma región. Para comprobar las regiones admitidas, consulte Disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Si desea acceder rápidamente al almacén desde el panel, haga clic en **Anclar al panel** y, luego, en **Crear**.

	![Almacén nuevo](./media/site-recovery-vmware-to-azure/new-vault-settings.png)

El nuevo almacén aparecerá en **Panel** > **Todos los recursos** y en la hoja principal de **Almacenes de servicios de recuperación**.

## Introducción

Site Recovery proporciona una experiencia introductoria diseñada para ayudarlo a ponerse en marcha lo más rápido posible. Comprueba los requisitos previos y lo guía a través de los pasos necesarios para implementar Site Recovery.

Seleccionará el tipo de máquinas que desea replicar y la ubicación donde desea replicarlas. Configure la infraestructura, incluidos los servidores locales, la configuración de Azure, las directivas de replicación y el planeamiento de capacidad. Si ya cuenta con la infraestructura, habilite la replicación para máquinas virtuales y servidores físicos. Puede ejecutar conmutaciones por error para máquinas específicas o crear planes de recuperación para conmutar por error varias máquinas.

Comience la experiencia de introducción decidiendo cómo desea implementar Site Recovery. El flujo descrito en esta introducción cambia ligeramente en función de los requisitos de replicación.


## Paso 1: Seleccione los objetivos de protección

Seleccione aquello que desea replicar y la ubicación en donde se va a realizar la replicación.

1. En la hoja **Almacenes de servicios de recuperación**, seleccione el almacén y haga clic en **Configuración**.
2. En **Configuración** > **Introducción**, haga clic en **Site Recovery** > **Step 1: Prepare Infrastructure** (Paso 1: Preparación de la infraestructura) > **Protection goal** (Objetivo de protección).

	![Elegir objetivos](./media/site-recovery-vmware-to-azure/choose-goals.png)

3. En **Protection goal** (Objetivo de protección), seleccione **To Azure** (En Azure) y seleccione **Yes, with VMware vSphere Hypervisor** (Sí, con VMware vSphere Hypervisor). y, a continuación, haga clic en **Aceptar**.

	![Elegir objetivos](./media/site-recovery-vmware-to-azure/choose-goals2.png)


## Paso 2: Configuración del entorno de origen

Configure el servidor de configuración y regístrelo en el almacén de Servicios de recuperación. Si va a replicar máquinas virtuales de VMware, especifique la cuenta de VMware que está usando para la detección automática.

1. Haga clic en **Step 1: Prepare Infrastructure** (Paso 1: Preparación de la infraestructura) > **Origen**. En **Prepare source** (Preparar origen), si no tiene un servidor de configuración, haga clic en **+Servidor de configuración** para agregar uno.

	![Configurar origen](./media/site-recovery-vmware-to-azure/set-source1.png)

2. En la hoja **Agregar servidor**, compruebe que **Servidor de configuración** aparezca en **Tipo de servidor**.
3. Antes de configurar el servidor de configuración, compruebe los [requisitos previos](#configuration-server-prerequisites). En concreto, compruebe que la máquina puede acceder a las direcciones URL necesarias.
4.	Descargue el archivo de instalación unificada de Site Recovery.
5.	Descargue la clave de registro del almacén. Se le solicitará cuando ejecute la instalación unificada. La clave será válida durante 5 días a partir del momento en que se genera.

	![Configurar origen](./media/site-recovery-vmware-to-azure/set-source2.png)

6.	En la máquina que usa como el servidor de configuración, ejecute la instalación unificada para instalar el servidor de configuración, el servidor de procesos y el servidor de destino maestro.


### Ejecución de la instalación unificada de Site Recovery

1.	Ejecute el archivo de instalación unificada.
2.	En **Antes de comenzar**, seleccione **Install the configuration server and process server** (Instalar el servidor de configuración y el servidor de procesos).

	![Antes de comenzar](./media/site-recovery-vmware-to-azure/combined-wiz1.png)

3. En **Third-Party Software License** (Licencia de software de terceros), haga clic en **Acepto** para descargar e instalar MySQL.

	![Software de terceros](./media/site-recovery-vmware-to-azure/combined-wiz2.png)

4. En **Configuración de Internet**, especifique cómo se conectará el proveedor que se ejecuta en el servidor de configuración a Azure Site Recovery a través de Internet.

	- Si quiere conectarse con el proxy configurado actualmente en la máquina, seleccione **Connect with existing proxy settings** (Conectarse con la configuración de proxy existente).
	- Si quiere que el proveedor se conecte directamente, seleccione **Connect directly without a proxy** (Conectarse directamente sin un proxy).
	- Si el proxy existente requiere autenticación, o quiere utilizar un proxy personalizado para la conexión del proveedor, seleccione **Connect with custom proxy settings** (Conectarse con una configuración de proxy personalizada).
		- Si utiliza un proxy personalizado, deberá especificar la dirección, el puerto y las credenciales.
		- Si utiliza un servidor proxy, se deberían haber permitido ya las direcciones URL descritas en los [requisitos previos](#configuration-server-prerequisites).

	![Firewall](./media/site-recovery-vmware-to-azure/combined-wiz3.png)

5. En **Comprobación de requisitos previos**, el programa de instalación ejecuta una comprobación para asegurarse de que se pueda ejecutar la instalación. Si aparece una advertencia sobre la **comprobación de la sincronización de tiempo global**, compruebe que la hora del reloj del sistema (configuración de **fecha y hora**) es la misma que la zona horaria.

	![Requisitos previos](./media/site-recovery-vmware-to-azure/combined-wiz4.png)

6. En **MySQL Configuration** (Configuración de MySQL), cree credenciales para iniciar sesión en la instancia de servidor MySQL que se va a instalar.

	![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz5.png)

7. En **Detalles del entorno**, seleccione si replicará máquinas virtuales de VMware. Si realiza la configuración, compruebe si PowerCLI 6.0 está instalado.

	![MySQL](./media/site-recovery-vmware-to-azure/combined-wiz6.png)

8. En **Install Location** (Ubicación de instalación), seleccione dónde quiera instalar los archivos binarios y almacenar la memoria caché. Puede seleccionar una unidad que tenga al menos 5 GB de almacenamiento disponible, pero se recomienda usar una unidad de memoria caché con 600 GB o más de espacio libre.

	![Ubicación de instalación](./media/site-recovery-vmware-to-azure/combined-wiz7.png)

9. En **Selección de red**, especifique el agente de escucha (adaptador de red y puerto SSL) en el que el servidor de configuración enviará y recibirá los datos de replicación. Puede modificar el puerto predeterminado (9443). Además de este puerto, se abrirá el puerto 443 para enviar y recibir información sobre la orquestación de replicación. No se debe utilizar el puerto 443 para recibir tráfico de replicación.


	![Selección de red](./media/site-recovery-vmware-to-azure/combined-wiz8.png)

10. En **Registro**, busque y seleccione la clave de registro que descargó del almacén.

	![Registro](./media/site-recovery-vmware-to-azure/combined-wiz9.png)

11.  En **Resumen**, revise la información y haga clic en **Instalar**. Se genera una frase de contraseña cuando finaliza la instalación. La necesitará al habilitar la replicación, así que cópiela y manténgala en una ubicación segura.

	![Resumen](./media/site-recovery-vmware-to-azure/combined-wiz10.png)

12.  Después de finalizar el registro, el servidor aparecerá en la hoja **Configuración** > **Servidores** del almacén.



#### Ejecución de la configuración desde la línea de comandos

Puede configurar el servidor de configuración a partir de la línea de comandos:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Cuando finalice la instalación, para completar el registro:

1. Inicie una aplicación denominada "Microsoft Azure Recovery Services Shell" en el menú Inicio de Windows.
2. En la ventana de comandos que se abre, ejecute el siguiente conjunto de comandos para definir la configuración del servidor proxy.

		PS C:\Windows\System32> $pwd = ConvertTo-SecureString -String ProxyUserPassword
		PS C:\Windows\System32> Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
		PS C:\Windows\System32> net stop obengine.exe

Parámetros:

- /ServerMode: Obligatorio. Especifica si se deben instalar los servidores de configuración y de procesos, o solo el servidor de procesos. Valores de entrada: CS, PS.
- InstallLocation: Obligatorio. La carpeta donde se instalan los componentes.
- /MySQLCredsFilePath. Obligatorio. La ruta de acceso al archivo en que se almacenan las credenciales del servidor MySQL. El archivo debe tener este formato:
	- [MySQLCredentials]
	- MySQLRootPassword = "<Password>"
	- MySQLUserPassword = "<Password>"
- /VaultCredsFilePath. Obligatorio. Ubicación del archivo de credenciales del almacén.
- /EnvType. Obligatorio. Tipo de instalación. Valores: VMware, NonVMware
- /PSIP y /CSIP. Obligatorio. Dirección IP del servidor de procesos y del servidor de configuración.
- /PassphraseFilePath. Obligatorio. Ubicación del archivo de frase de contraseña.
- /ByPassProxy. Opcional. Especifica si el servidor de configuración se conectar a Azure sin proxy.
- /ProxySettingsFilePath. Opcional. Configuración del proxy (el proxy predeterminado requiere autenticación, o un proxy personalizado). El archivo debe tener este formato:
	- [ProxySettings]
	- ProxyAuthentication = "Sí/No"
	- Proxy IP = "dirección IP >"
	- ProxyPort = "<Port>"
	- ProxyUserName="<User Name>"
	- ProxyPassword="<Password>"
- DataTransferSecurePort. Opcional. Número de puerto que se va a usar para los datos de replicación.
- SkipSpaceCheck. Opcional. Omitir comprobación de espacio en memoria caché.
- AcceptThirdpartyEULA. Obligatorio. La marca implica la aceptación de los términos de licencia de terceros.
- ShowThirdpartyEULA. Obligatorio. Muestra los términos de licencia de terceros. Si se proporciona como entrada, se omiten todos los demás parámetros.

### Agregar la cuenta de VMware utilizada para la detección automática

 Al preparar la implementación, debe haber [creado una cuenta de VMware](#prepare-an-account-for-automatic-discovery) que Site Recovery pueda usar para la detección automática. Agregue esta cuenta como sigue:

1. Abra **CSPSConfigtool.exe**. Esta herramienta está disponible como acceso directo en el escritorio y se encuentra en la carpeta [UBICACIÓN DE INSTALACIÓN]\\home\\svsystems\\bin.
2. Haga clic en **Administrar cuentas** > **Agregar cuenta**.

	![Agregar cuenta](./media/site-recovery-vmware-to-azure/credentials1.png)

3. En **Detalles de la cuenta**, agregue la cuenta que se utilizará para la detección automática. Tenga en cuenta que el nombre de la cuenta podría tardar 15 minutos o más en aparecer en el portal. Para que se actualice inmediatamente, haga clic en **Servidores de configuración** > Nombre de servidor > **Actualizar servidor**.

	![Detalles](./media/site-recovery-vmware-to-azure/credentials2.png)

### Conexión a hosts de vSphere y servidores vCenter

Si va a replicar máquinas virtuales de VMware, conéctese a los hosts de vSphere y a los servidores vCenter.

1. Compruebe que el servidor de configuración tiene acceso de red a los hosts de vSphere y servidores vCenter.
2. Haga clic en **Prepare infrastructure** (Preparar infraestructura) > **Origen**. En **Prepare source** (Preparar origen) seleccione el servidor de configuración y haga clic en **+vCenter** para agregar un host vSphere o un servidor vCenter.
3. En **Add vCenter** (Agregar vCenter), especifique un nombre descriptivo para el host vSphere o el servidor vCenter y especifique la dirección IP o el FQDN del servidor. Deje el puerto 443 a menos que los servidores de VMware estén configurados para escuchar las solicitudes en un puerto diferente. A continuación, seleccione la cuenta que se usará para conectarse al servidor VMware. Haga clic en **Aceptar**.

	![VMware](./media/site-recovery-vmware-to-azure/vmware-server.png)

	>[AZURE.NOTE] Si agrega el servidor vCenter o el host vSphere con una cuenta que no tiene privilegios de administrador en el servidor vCenter o el servidor host, asegúrese de que la cuenta tiene habilitados los siguientes privilegios: Centro de datos, Almacén de datos, Carpeta, Host, Red, Recurso, Máquina virtual, Conmutador distribuido de vSphere. Además, el servidor vCenter necesita el privilegio Vistas de almacenamiento.

Site Recovery se conecta a los servidores de VMware con la configuración especificada y detecta las máquinas virtuales.

## Paso 3: Configuración del entorno de destino

Compruebe que tiene una cuenta de almacenamiento para la replicación y una red de Azure a la que las máquinas virtuales de Azure se conectarán tras la conmutación por error.

1.	Haga clic en **Prepare infrastructure** (Preparar infraestructura) > **Destino** y seleccione la suscripción de Azure que desea utilizar.
2.	Especifique el modelo de implementación que desea usar para las máquinas virtuales después de la conmutación por error.
3.	Site Recovery comprueba que tiene una o más cuentas de almacenamiento y redes compatibles.

	![Destino](./media/site-recovery-vmware-to-azure/gs-target.png)

4.	Si no ha creado una cuenta de almacenamiento y desea crear una con ARM, haga clic en **+Cuenta de almacenamiento** para hacerlo directamente. En la hoja **Crear cuenta de almacenamiento**, especifique el nombre, el tipo, la suscripción y la ubicación de la cuenta. La cuenta debe estar en la misma región que el almacén de Servicios de recuperación.

	![Almacenamiento](./media/site-recovery-vmware-to-azure/gs-createstorage.png)

	Observe lo siguiente:

	- Si desea crear una cuenta de almacenamiento mediante el modelo clásico, lo hará en el Portal de Azure. [Más información](../storage/storage-create-storage-account-classic-portal.md)
	- Si utiliza una cuenta de almacenamiento premium para los datos replicados, deberá configurar una cuenta de almacenamiento estándar adicional para los registros de replicación del almacén que capturan los cambios continuos de los datos locales.

4.	Seleccione una red de Azure. Si no ha creado una red y desea crear una con ARM, haga clic en **+Red** para hacerlo directamente. En la hoja **Crear red virtual**, especifique el nombre, el intervalo de direcciones, los detalles de subred, la suscripción y la ubicación de la red. La red debe estar en la misma ubicación que el almacén de Servicios de recuperación.

	![Red](./media/site-recovery-vmware-to-azure/gs-createnetwork.png)

	Si desea crear una red mediante el modelo clásico, lo hará en el Portal de Azure. [Más información](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

## Paso 4: Configuración de las opciones de replicación

1. Para crear una nueva directiva de replicación, haga clic en **Prepare infrastructure** (Preparar infraestructura) > **Configuración de la replicación** > **+Create and associate** (+Crear y asociar).
2. En **Create and associate policy** (Crear y asociar directiva), especifique un nombre de directiva.
3. En **Umbral de RPO**, especifique el límite de RPO. Se generarán alertas cuando la replicación continua supera este límite.
5. En **Retención de punto de recuperación**, especifique, en horas, el tiempo que estará disponible el período de retención para cada punto de recuperación. Los equipos protegidos se pueden recuperar en cualquier punto dentro de un período. Se admite una retención de hasta 24 horas para máquinas replicadas en almacenamiento premium.
6. En **Frecuencia de instantánea coherente con la aplicación** especifique la frecuencia (en minutos) con la que se crearán puntos de recuperación que contengan las instantáneas coherentes con la aplicación.
7. Cuando se crea una directiva de replicación, de forma predeterminada una directiva de conciliación se crea automáticamente para la conmutación por recuperación. Por ejemplo, si la directiva de replicación es **rep-policy**, la directiva de conmutación por recuperación será **rep-policy-failback**. Esta directiva no se usa hasta que inicie una conmutación por recuperación.  
8. Haga clic en **Aceptar** para crear la directiva.

	![Directiva de replicación](./media/site-recovery-vmware-to-azure/gs-replication2.png)

9. Cuando se crea una nueva directiva, esta se asocia automáticamente con el servidor de configuración. Haga clic en **Aceptar**.

	![Directiva de replicación](./media/site-recovery-vmware-to-azure/gs-replication3.png)


## Paso 5: Pleaneamiento de capacidad

Ahora que tiene la infraestructura básica configurada, puede planear la capacidad y averiguar si necesitará recursos adicionales.

Site Recovery proporciona una herramienta de planeación de capacidad para ayudarle a asignar los recursos adecuados para el entorno de origen, los componentes de recuperación del sitio, las redes y el almacenamiento. Puede ejecutar la herramienta de planeación en modo rápido para obtener resultados basados en un promedio de máquinas virtuales, discos y almacenamiento o en el modo detallado en el que podrá especificar las cifras en el nivel de carga de trabajo. Antes de empezar necesitará:

- Recopilar información sobre su entorno de replicación, incluidas las máquinas virtuales, discos por máquina virtual y almacenamiento por disco.
- Calcular la tasa de cambio (renovación) diaria para los datos replicados. Puede usar la [aplicación de planeamiento de capacidad de vSphere](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) para facilitar la tarea.

1.	Haga clic en **Descargar** para descargar la herramienta y luego ejecútela. [Lea el artículo](site-recovery-capacity-planner.md) que acompaña a la herramienta.
2.	Cuando haya terminado, seleccione **Yes** (Sí) en **Have you completed capacity planning**? (¿Ha completado el planeamiento de capacidad?).

	![Planificación de capacidad](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)

En la tabla siguiente se indican una serie de puntos para ayudarlo con el planeamiento de capacidad para este escenario.


**Componente** | **Detalles**
--- | --- | ---
**Replicación** | **Tasa máxima de cambios diaria**: una máquina protegida solo puede usar un servidor de procesos, y un servidor de procesos puede gestionar una tasa de cambios diaria de hasta 2 TB. Por lo tanto, la tasa máxima de cambios diaria que admite una máquina virtual es de 2 TB.<br/><br/> **Rendimiento máximo**: una máquina replicada puede pertenecer a una cuenta de almacenamiento en Azure. Una cuenta de almacenamiento estándar puede controlar un máximo de 20 000 solicitudes por segundo y se recomienda mantener la cantidad de E/S por segundo en una máquina de origen en 20 000. Por ejemplo, si tiene una máquina de origen con 5 discos y cada disco genera 120 E/S por segundo (8 K de tamaño) en el origen, se encontrará dentro del límite de 500 de Azure por E/S por segundo por disco. La cantidad de cuentas de almacenamiento necesarias = E/S por segundo de origen total/20 000.
**Servidor de configuración** | El servidor de configuración debe poder controlar la capacidad de tasa de cambios diaria en todas las cargas de trabajo que se ejecutan en máquinas protegidas, y necesita el ancho de banda suficiente para realizar una replicación continua de los datos en el almacenamiento de Azure.<br/><br/> Como procedimiento recomendado, se sugiere que el servidor de configuración debe encontrarse en el mismo segmento de LAN y red que las máquinas que desea proteger. De todos modos, puede estar en una red distinta, pero las máquinas que desea proteger deben contar con la visibilidad de red L3 en ella.<br/><br/> En la tabla siguiente resumen las recomendaciones de tamaño para el servidor de configuración.
**Servidor de proceso** | El primer servidor de procesos está instalado en el servidor de configuración de forma predeterminada. Puede implementar servidores de procesos adicionales para escalar el entorno. Tenga en cuenta que:<br/><br/> El servidor de procesos recibe datos de replicación provenientes de las máquinas virtuales y los optimiza con almacenamiento en caché, compresión y cifrado antes de enviarlos a Azure. La máquina del servidor de procesos debe tener los recursos suficientes para realizar estas tareas.<br/><br/> El servidor de proceso utiliza la caché basada en disco. Se recomienda tener un disco de caché independiente con 600 GB o más de capacidad para controlar los cambios en los datos almacenados ante la eventualidad de una interrupción o un cuello de botella en la red.

### Recomendaciones de tamaño del servidor de configuración

**CPU** | **Memoria** | **Tamaño del disco de caché** | **Frecuencia de cambio de datos** | **Máquinas protegidas**
--- | --- | --- | --- | ---
8 vCPU (2 sockets * 4 núcleos @ 2,5 GHz) | 16 GB | < 300 GB | 500 GB o menos | Replicar menos de 100 máquinas.
12 vCPU (2 sockets * 6 núcleos @ 2,5 GHz) | 18 GB | 600 GB | 500 GB a 1 TB | Replicar entre 100 y 150 máquinas.
16 vCPU (2 sockets * 8 núcleos @ 2,5 GHz) | 32 GB | 1 TB | 1 TB a 2 TB | Replicar entre 150 y 200 máquinas.
Implementar otro servidor de procesos | | | 2 TB | Implemente servidores de procesos adicionales si replica más de 200 máquinas o si la tasa de cambios de datos diaria supera los 2 TB.

Donde:

- Cada máquina de origen está configurada con 3 discos de 100 GB cada una.
- Usamos almacenamiento de pruebas comparativas de 8 unidades SAS de 10 K RPM con RAID 10 para las mediciones de disco de caché.

### Recomendaciones de tamaño para el servidor de procesos

Si necesita proteger más de 200 máquinas o si la tasa de cambios diaria es mayor que 2 TB, puede agregar servidores de procesos adicionales para controlar la carga de replicación. Para escalar horizontalmente, puede:

- Aumentar el número de servidores de configuración. Por ejemplo, puede proteger hasta 400 máquinas con dos servidores de configuración.
- Agregar servidores de procesos adicionales y utilizarlos para controlar el tráfico en lugar (o además) del servidor de configuración.

Esta tabla describe un escenario en el cual:

- No pretende utilizar el servidor de configuración como un servidor de procesos.
- Ha configurado un servidor de procesos adicional.
- Ha configurado máquinas virtuales protegidas para utilizar el servidor de procesos adicional.
- Cada máquina de origen protegida está configurada con tres discos de 100 GB cada uno.

**Servidor de configuración** | **Servidores de procesos adicionales**| **Tamaño del disco de caché** | **Frecuencia de cambio de datos** | **Máquinas protegidas**
--- | --- | --- | --- | ---
8 vCPU (2 sockets * 4 núcleos @ 2,5 GHz), 16 GB de memoria | 4 vCPU (2 sockets * 2 núcleos @ 2,5 GHz), 8 GB de memoria | < 300 GB | 250 GB o menos | Replicar 85 máquinas o menos.
8 vCPU (2 sockets * 4 núcleos @ 2,5 GHz), 16 GB de memoria | 8 vCPU (2 sockets * 4 núcleos @ 2,5 GHz), 12 GB de memoria | 600 GB | 250 GB a 1 TB | Replicar entre 85 y 150 máquinas.
12 vCPU (2 sockets * 6 núcleos @ 2,5 GHz), 18 GB de memoria | 12 vCPU (2 sockets * 6 núcleos @ 2,5 GHz), 24 GB de memoria | 1 TB | 1 TB a 2 TB | Replicar entre 150 y 225 máquinas.


La manera en que escala los servidores dependerá de su preferencia con respecto a un modelo de escalado vertical o escalado horizontal. Puede escalar verticalmente con la implementación de algunos servidores de procesos y de configuración de alto nivel, mientras que, para escalar horizontalmente, debe implementar más servidores con menos recursos. Por ejemplo, si necesita proteger 220 máquinas, podría elegir una de las siguientes opciones:

- Configure el servidor de configuración con 12 vCPU y 18 GB de memoria, un servidor de procesos adicional con 12 vCPU y 24 GB de memoria y, además, configure las máquinas protegidas para que solo utilicen el servidor de procesos adicional.
- O bien, podría configurar dos servidores de configuración (2 con 8 vCPU y 16 GB de RAM) y dos servidores de procesos adicionales (1 con 8 vCPU y 1 con 4 vCPU para controlar 135 + 85 (220) máquinas) y configurar las máquinas protegidas para que solo utilicen los servidores de procesos adicionales.

[Siga estas instrucciones](#deploy-additional-process-servers) para configurar un servidor de procesos adicional.

### Consideraciones sobre el ancho de banda de red

Puede utilizar la herramienta de planeación de capacidad para calcular el ancho de banda necesario para la replicación (replicación inicial y, a continuación, la diferencial). Para controlar el uso de ancho de banda de la replicación tiene algunas opciones:

- **Limitar ancho de banda**: el tráfico de VMware que se replica en Azure pasa a través de un servidor de procesos específico. También puede limitar el ancho de banda en las máquinas que se ejecutan como servidores de procesos.
- **Influir en el ancho de banda**: puede influir en el ancho de banda utilizado para la replicación mediante un par de claves del Registro.
	- El valor del Registro **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows Azure Backup\\UploadThreadsPerVM** especifica el número de subprocesos que se utilizan para la transferencia de datos (replicación inicial o diferencial) de un disco. Un valor mayor aumenta el ancho de banda de red utilizado para la replicación.
	- **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows Azure Backup\\DownloadThreadsPerVM** especifica el número de subprocesos usados para la transferencia de datos durante la conmutación por recuperación.

#### Limitar ancho de banda

1. Abra el complemento MMC de Copia de seguridad de Microsoft Azure en la máquina que actúa como el servidor de procesos. De manera predeterminada, hay disponible un acceso directo para Copia de seguridad de Microsoft Azure en el escritorio o en la siguiente ruta de acceso: C:\\Program Files\\Microsoft Azure Recovery Services Agent\\bin\\wabadmin.
2. En el complemento, haga clic en **Cambiar propiedades**.

	![Limitar ancho de banda](./media/site-recovery-vmware-to-azure/throttle1.png)

3. En la pestaña **Limitación**, seleccione **Habilitar límite de uso del ancho de banda de Internet para las operaciones de copia de seguridad** y defina los límites durante las horas de trabajo y las de no trabajo. Los intervalos válidos van de 512 Kbps a 102 Mbps por segundo.

	![Limitar ancho de banda](./media/site-recovery-vmware-to-azure/throttle2.png)

También puede utilizar el cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) para establecer la limitación. Aquí tiene un ejemplo:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica que no se requiere ninguna limitación.


#### Control del uso de ancho de banda de red

1. En el Registro, vaya a **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows Azure Backup\\Replication**.
	- Para influir en el tráfico de ancho de banda en un disco de replicación, modifique el valor de **UploadThreadsPerVM** o cree la clave en caso de que no exista.
	- Para influir en el ancho de banda para el tráfico de conmutación por recuperación de Azure, modifique el valor **DownloadThreadsPerVM**.
2. El valor predeterminado es 4. En una red "sobreaprovisionada", se deben cambiar los valores predeterminados de estas claves de registro. El valor máximo es 32. Supervise el tráfico para optimizar el valor.

## Paso 6: Replicación de aplicaciones

Asegúrese de que las máquinas que desea replicar están preparadas para la instalación de Mobility Service y después habilite la replicación.

### Instalación de Mobility Service

El primer paso para habilitar la protección de las máquinas virtuales y los servidores físicos es instalar el servicio de movilidad. Puede hacerlo de dos maneras:

- **Procesar inserción de servidor**: al habilitar la replicación en una máquina, inserte e instale el componente Mobility Service desde el servidor de procesos. Tenga en cuenta que la instalación de inserción no se producirá si las máquinas ya ejecutan una versión actualizada del componente.
- **Inserción empresarial**: instale automáticamente el componente con el proceso de inserción empresarial, como WSUS o System Center Configuration Manager. Configure el servidor de configuración antes de realizar esta tarea.
- **Instalación manual**: instale el componente manualmente en cada máquina que desea replicar. Configure el servidor de configuración antes de realizar esta tarea.


#### Preparación de la inserción automática en máquinas Windows

A continuación, le mostramos cómo preparar las máquinas Windows para que el servidor de procesos pueda instalar automáticamente el servicio de movilidad.

1.  Cree una cuenta que el servidor de procesos pueda utilizar para tener acceso a la máquina. La cuenta debe tener privilegios de administrador (local o dominio) y solo se utiliza para la instalación de inserción.

	>[AZURE.NOTE] Si no utiliza una cuenta de dominio, deberá deshabilitar el control de acceso de usuario remoto en la máquina virtual. Para ello, en HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System del registro, agregue la entrada LocalAccountTokenFilterPolicy DWORD con un valor de 1. Para agregar la entrada del Registro desde un tipo CLI **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  En el Firewall de Windows de la máquina que desea proteger, seleccione **Permitir una aplicación o una característica a través de Firewall**. Habilite **Compartir archivos e impresoras** y el **Instrumental de administración de Windows**. Para las máquinas que pertenecen a un dominio, puede configurar el firewall con un GPO.

	![Configuración de firewall](./media/site-recovery-vmware-to-azure/mobility1.png)

2. Agregue la cuenta que creó:

	- Abra **cspsconfigtool**. Esta herramienta está disponible como acceso directo en el escritorio y se encuentra en la carpeta [UBICACIÓN DE INSTALACIÓN]\\home\\svsystems\\bin.
	- En la pestaña **Administrar cuentas**, haga clic en **Agregar cuenta**.
	- Agregue la cuenta que creó. Después de agregar la cuenta, deberá proporcionar las credenciales para habilitar la replicación de una máquina.


#### Preparación para la inserción automática en los servidores Linux

1.	Asegúrese de que la máquina con Linux que quiere proteger sea compatible, tal como se describe en [Requisitos previos de equipos protegidos](#protected-machine-prerequisites). Asegúrese de que haya conectividad de red entre la máquina de Linux y el servidor de procesos.

2.	Cree una cuenta que el servidor de procesos pueda utilizar para tener acceso a la máquina. La cuenta debe ser un usuario raíz en el servidor de origen de Linux y solo se utiliza para la instalación de inserción.

	- Abra **cspsconfigtool**. Esta herramienta está disponible como acceso directo en el escritorio y se encuentra en la carpeta [UBICACIÓN DE INSTALACIÓN]\\home\\svsystems\\bin.
	- En la pestaña **Administrar cuentas**, haga clic en **Agregar cuenta**.
	- Agregue la cuenta que creó. Después de agregar la cuenta, deberá proporcionar las credenciales para habilitar la replicación de una máquina.

3.	Compruebe que el archivo /etc/hosts del servidor Linux de origen contiene entradas que asignan el nombre de host local a las direcciones IP asociadas con todos los adaptadores de red.
4.	Instale los paquetes openssh, openssh-server, openssl más recientes en la máquina que desea replicar.
5.	Asegúrese de que SSH está habilitado y ejecutándose en el puerto 22.
6.	Habilite la autenticación de la contraseña y del subsistema SFTP en el archivo sshd\_config:

	- Inicie sesión como root.
	- En el archivo /etc/ssh/sshd\_config, encuentre la línea que comienza con **PasswordAuthentication**.
	- Quite la marca de comentario de la línea y cambie el valor de **no** a **yes**.
	- Busque la línea que comienza por **Subsystem** y quite la marca de comentario.

		![Linux](./media/site-recovery-vmware-to-azure/mobility2.png)


#### Instalación manual de Mobility Service

Los instaladores están disponibles en **C:\\Archivos de programa (x86)\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository**.

Sistema operativo de origen | Archivo de instalación del servicio de movilidad
--- | ---
Windows Server 64 (solo 64 bits) | Microsoft-ASR\_UA\_9.*.0.0\_Windows\_* release.exe
CentOS 6.4, 6.5, 6.6 (solo 64 bits) | Microsoft-ASR\_UA\_9.*.0.0\_RHEL6-64\_*release.tar.gz SUSE Linux Enterprise Server 11 SP3 (solo 64 bits) | Microsoft-ASR\_UA\_9.*.0.0\_SLES11-SP3-64\_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (solo 64 bits) | Microsoft-ASR\_UA\_9.*.0.0\_OL6-64\_*release.tar.gz


#### Instalar manualmente en un servidor Windows


1. Descargue y ejecute el instalador pertinente.
2. En **Antes de comenzar**, seleccione **Mobility Service**.

	![Servicio de movilidad](./media/site-recovery-vmware-to-azure/mobility3.png)

3. En **Configuration Server Details** (Detalles del servidor de configuración), especifique la dirección IP del servidor de configuración y la frase de contraseña que se generó al ejecutar la instalación unificada. Puede recuperar la frase de contraseña mediante la ejecución de **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –n** en el servidor de configuración.

	![Servicio de movilidad](./media/site-recovery-vmware-to-azure/mobility6.png)

4. En **Install Location** (Ubicación de instalación), deje la configuración predeterminada y haga clic en **Siguiente** para comenzar la instalación.
5. En **Installation Progress** (Progreso de la instalación), supervise la instalación y reinicie la máquina, si así se le solicita. Después de instalar el servicio, la actualización del estado en el portal puede tardar unos 15 minutos.

También puede instalar desde la línea de comandos:

UnifiedAgent.exe [/Role <agente/destino maestro>] [/InstallLocation <directorio de instalación>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <ruta de acceso al archivo de frase de contraseña>] [/LogFilePath <Log File Path>]

Donde:

- /Role: Obligatorio. Especifica si se debe instalar el servicio de movilidad.
- /InstallLocation: Obligatorio. Especifica dónde instalar el servicio.
- /PassphraseFilePath: Obligatorio. Frase de contraseña del servidor de configuración.
- /LogFilePath: Obligatorio. Ubicación de los archivos de configuración de registro.


#### Instalación manual en un servidor Linux:

1. Copie el archivo tar adecuado según la tabla anterior en la máquina Linux que desea replicar.
2. Abra un programa de shell y extraiga el archivo TAR comprimido en una ruta de acceso local mediante la ejecución de `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`.
3. Cree un archivo passphrase.txt en el directorio local en el que extrajo el contenido del archivo tar. Para ello, copie la frase de contraseña desde C:\\ProgramData\\Microsoft Azure Site Recovery\\private\\connection.passphrase en el servidor de configuración y guárdela en el archivo passphrase.txt mediante la ejecución de *`echo <passphrase> >passphrase.txt`* en el shell.
4. Instale Mobility Service; para ello, ejecute *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especifique la dirección IP interna del servidor de configuración y asegúrese de que el puerto 443 está seleccionado. Después de instalar el servicio, la actualización del estado en el portal puede tardar unos 15 minutos.

**También puede instalarlo desde la línea de comandos**:

1. Copie la frase de contraseña desde C:\\Archivos de programa (x86)\\InMage Systems\\private\\connection en el servidor de configuración y guárdela como "passphrase.txt" en el servidor de configuración. Luego, ejecute estos comandos. En nuestro ejemplo, la dirección IP del servidor de configuración es 104.40.75.37 y el puerto HTTPS debe ser 443:

Para instalar en un servidor de producción:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Para instalar en el servidor de destino maestro:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


### Habilitar replicación

#### Antes de comenzar

Si va a replicar máquinas virtuales de VMware, tenga en cuenta lo siguiente:

- Las máquinas virtuales de VMware se detectan cada 15 minutos y podrían tardar 15 minutos o más en aparecer en el portal después de su detección. Del mismo modo, la detección podría tardar 15 minutos o más si agrega un servidor vCenter o host vSphere nuevos.
- Los cambios de entorno en la máquina virtual (como la instalación de herramientas de VMware) también pueden tardar 15 minutos o más en actualizarse en el portal.
- Puede consultar la hora de la detección más reciente de las máquinas virtuales de VMware en el campo **Last Contact At** (Último contacto a las) correspondiente al servidor vCenter/host vSphere en la hoja **Servidores de configuración**.
- Para agregar máquinas de replicación sin esperar a la detección programada, resalte el servidor de configuración (no haga clic en él) y haga clic en el botón **Actualizar**.
- Al habilitar la replicación, si la máquina está preparada, el servidor de procesos instala automáticamente Mobility Service en ella.

#### Excluir discos de la replicación

Cuando se habilita la replicación, de forma predeterminada se replican todos los discos en una máquina. Puede excluir discos de la replicación. Por ejemplo, es posible que no desee replicar los discos con datos temporales, o los datos que se actualizan cada vez que una máquina o aplicación se reinicia (por ejemplo, pagefile.sys o tempdb de SQL Server). Si desea excluir discos, tenga en cuenta que:

- Solo puede excluir discos que ya tienen instalado Mobility Service. Necesitará [instalar manualmente Mobility Service](#install-the-mobility-service-manually) porque este servicio solo se instala con el mecanismo de inserción después de habilitar la replicación.
- Solo se pueden excluir los discos básicos de la replicación. No se pueden excluir los discos dinámicos ni del sistema operativo.
- Una vez habilitada la replicación, no puede agregar ni quitar discos para la replicación. Si desea agregar o excluir un disco, necesitará deshabilitar la protección de la máquina y luego volver a habilitarla.
- Si excluye un disco necesario para que una aplicación funcione, después de la conmutación por error a Azure, debe crearlo manualmente en Azure para poder ejecutar la aplicación replicada. También puede integrar Automatización de Azure en un plan de recuperación para crear el disco durante la conmutación por error de la máquina.
- Se producirá una conmutación por recuperación de los discos creados manualmente en Azure. Por ejemplo, si realiza una conmutación por recuperación de tres discos y crea dos directamente en Azure, los cinco experimentarán una conmutación por recuperación. No se pueden excluir discos creados manualmente de la conmutación por recuperación.

**Ahora habilite la replicación como sigue**:

1. Haga clic en **Step 2: Replicate application** (Paso 2: Replicación de la aplicación) > **Origen**. Después de habilitar la replicación por primera vez, haga clic en **+Replicar** en el almacén para habilitar la replicación de máquinas adicionales.
2. En la hoja **Origen** > **Origen**, seleccione el servidor de configuración.
3. En **Tipo de máquina**, seleccione **Máquinas virtuales** o **Máquinas físicas**.
4. En **vCenter/vSphere Hypervisor**, seleccione el servidor vCenter que administra el host vSphere, o bien seleccione el host. Esta configuración es relevante si va a replicar máquinas físicas.
5. Seleccione el servidor de procesos. Si no ha creado ningún servidor de procesos adicional, este será el nombre del servidor de configuración. A continuación, haga clic en **Aceptar**.

	![Habilitar replicación](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. En **Destino** seleccione la suscripción de almacén y, en **Modelo de implementación posterior a la conmutación por error**, seleccione el modelo (administración de recursos o clásico) que desea utilizar en Azure después de la conmutación por error.
7. Seleccione la cuenta de almacenamiento de Azure que desea usar para los datos de replicación. Observe lo siguiente:

	- Puede seleccionar una cuenta de almacenamiento estándar o premium. Si selecciona una cuenta premium, debe especificar una cuenta de almacenamiento estándar adicional para los registros de replicación en curso. Las cuentas deben estar en la misma región que el almacén de Servicios de recuperación.
	- Si desea utilizar una cuenta de almacenamiento diferente de las que tiene, puede [crear una](#set-up-an-azure-storage-account). Para crear una cuenta de almacenamiento mediante el modelo de ARM, haga clic en **Crear nueva**. Si desea crear una cuenta de almacenamiento mediante el modelo clásico, lo hará en el [Portal de Azure](../storage/storage-create-storage-account-classic-portal.md).

8. Seleccione la red y la subred de Azure a la que se conectarán las máquinas virtuales de Azure cuando se pongan en marcha después de la conmutación por error. La red virtual de Azure debe estar en la misma región que el almacén de Servicios de recuperación. Seleccione la opción **Configure now for selected machines** (Configurar ahora para las máquinas seleccionadas) para aplicar la configuración de red a todas las máquinas que seleccione para proteger. Seleccione **Configure later** (Configurar más tarde) para seleccionar la red de Azure por máquina. Si no dispone de una red, debe [crear una](#set-up-an-azure-network). Para crear una red mediante el modelo de ARM, haga clic en **Crear nueva**. Si desea crear una red mediante el modelo clásico, hágalo [en el Portal de Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Seleccione una subred si es posible. y, a continuación, haga clic en **Aceptar**.

	![Habilitar replicación](./media/site-recovery-vmware-to-azure/enable-replication3.png)

9. En **Máquinas virtuales** > **Seleccionar máquinas virtuales**, haga clic y seleccione cada máquina que desea replicar. Solo puede seleccionar aquellas máquinas en las que se pueda habilitar la replicación. y, a continuación, haga clic en **Aceptar**.

	![Habilitar replicación](./media/site-recovery-vmware-to-azure/enable-replication5.png)

10. En **Propiedades** > **Configurar propiedades**, seleccione la cuenta que utilizará el servidor de procesos para instalar automáticamente Mobility Service en la máquina. De manera predeterminada se replican todos los discos. Haga clic en **All Disks** (Todos los discos) y desactive todos los discos que no desea replicar. y, a continuación, haga clic en **Aceptar**. Puede establecer propiedades adicionales más adelante.

	![Habilitar replicación](./media/site-recovery-vmware-to-azure/enable-replication6.png)

11. En **Configuración de la replicación** > **Configure replication settings**(Establecer configuración de replicación), compruebe que la directiva de replicación correcta está seleccionada. Puede modificar la configuración de la directiva de replicación en **Configuración** > **Directivas de replicación** > Nombre de directiva > **Editar configuración**. Los cambios aplicados a una directiva también se aplicarán a las máquinas nuevas y replicadas.

12. Habilite **Coherencia de múltiples VM** si desea recopilar las máquinas en un grupo de replicación y especifique un nombre para el grupo. y, a continuación, haga clic en **Aceptar**. Observe lo siguiente:

	- Todas las máquinas de un grupo de replicación se replican al mismo tiempo y comparten puntos de recuperación coherentes con los bloqueos y coherentes con la aplicación cuando conmutan por error.
	- Se recomienda que recopile las máquinas virtuales y los servidores físicos juntos para que reflejen las cargas de trabajo. Habilitar la coherencia de múltiples VM puede afectar al rendimiento de la carga de trabajo y solo debe utilizarse si las máquinas ejecutan la misma carga de trabajo y necesita coherencia.

	![Habilitar replicación](./media/site-recovery-vmware-to-azure/enable-replication7.png)

13. Haga clic en **Habilitar replicación**. Puede hacer un seguimiento del progreso del trabajo de **Habilitar protección** en **Configuración** > **Trabajos** > **Site Recovery jobs** (Trabajos de Site Recovery). La máquina estará preparada para la conmutación por error después de que finalice el trabajo de **Finalizar protección**.

> [AZURE.NOTE] Si la máquina está preparada para la instalación de inserción, el componente Mobility Service se instalará cuando la protección esté habilitada. Después de que el componente se ha instalado en la máquina, se inicia un trabajo de protección y se produce un error. Debe reiniciar manualmente cada máquina después del error. Una vez que reinicie, el trabajo de protección vuelve a comenzar y se produce la replicación inicial.

### Visualización y administración de las propiedades de la máquina virtual

Es recomendable que compruebe las propiedades de la máquina de origen. Recuerde que el nombre de la máquina virtual debe cumplir los [Requisitos para las máquinas virtuales de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Haga clic en **Configuración** > **Elementos replicados** > y seleccione la máquina. En la hoja **Essentials** se muestra información sobre la configuración y el estado de las máquinas.

2. En **Propiedades**, puede ver la información de replicación y conmutación por error de la máquina virtual.

	![Habilitar replicación](./media/site-recovery-vmware-to-azure/test-failover2.png)

3. En **Compute and Network** (Proceso y red) > **Compute properties** (Propiedades de proceso), puede especificar el nombre y el tamaño de destino de la máquina virtual de Azure. Modifique el nombre para que cumpla con los requisitos de Azure si es necesario. También puede ver y agregar la información sobre la red, la subred y la dirección IP de destino que se asignarán a la máquina virtual de Azure. Tenga en cuenta lo siguiente:

	- Puede establecer la dirección IP de destino. Si no proporciona una dirección, la máquina conmutada por error usará DHCP. Si establece una dirección que no está disponible en el momento de la conmutación por error, la conmutación no funcionará. Se puede utilizar la misma dirección IP de destino para la conmutación por error de prueba si la dirección está disponible en la red.
	- El número de adaptadores de red viene determinado por el tamaño que especifique para la máquina virtual de destino, de la siguiente manera:

		- Si el número de adaptadores de red en el equipo de origen es menor o igual al número de adaptadores permitido para el tamaño de la máquina de destino, el destino tendrá el mismo número de adaptadores que el origen.
		- Si el número de adaptadores para la máquina virtual de origen supera el número permitido para el tamaño de destino, entonces se utilizará el tamaño máximo de destino.
		- Por ejemplo, si una máquina de origen tiene dos adaptadores de red y el tamaño de la máquina de destino es compatible con cuatro, el equipo de destino tendrá dos adaptadores. Si el equipo de origen tiene dos adaptadores pero el tamaño de destino compatible solo admite uno, el equipo de destino tendrá solo un adaptador. 	
	- Si la máquina virtual tiene varios adaptadores de red, todos ellos se conectarán a la misma red.

	![Habilitar replicación](./media/site-recovery-vmware-to-azure/test-failover4.png)

4. En **Discos** puede ver los discos de datos y del sistema operativo en la máquina virtual que se va a replicar.


## Paso 7: Prueba de la implementación

Para probar la implementación, puede ejecutar una conmutación por error de prueba para una sola máquina virtual o un plan de recuperación que contenga una o varias máquinas virtuales.


### Preparación para la conmutación por error

- Para ejecutar una conmutación por error de prueba, le recomendamos que cree una nueva red de Azure que esté aislada de la red de producción de Azure (este es el comportamiento predeterminado cuando se crea una nueva red de Azure). [Más información](site-recovery-failover.md#run-a-test-failover) sobre la ejecución de conmutaciones por error de prueba.
- Para obtener el mejor rendimiento cuando realice una conmutación por error en Azure, instale el agente de Azure en la máquina protegida. Hace más rápido el arranque y ayuda a solucionar problemas. Instale el agente de [Linux](https://github.com/Azure/WALinuxAgent) o de [Windows](http://go.microsoft.com/fwlink/?LinkID=394789).
- Para probar completamente la implementación necesitará una infraestructura para que la máquina replicada funcione según lo esperado. Si desea probar Active Directory y DNS puede crear una máquina virtual como controlador de dominio con DNS y replicar esta en Azure con Azure Site Recovery. Más información en las [consideraciones de la conmutación por error de prueba para Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Asegúrese de que el servidor de configuración se encuentre en ejecución. De lo contrario, se producirá un error de conmutación por error.
- Si ha excluido discos de la replicación, debe crearlos manualmente en Azure después de la conmutación por error para que la aplicación se ejecute según lo esperado.
- Si desea ejecutar una conmutación por error no planeada en lugar de una de prueba tenga en cuenta lo siguiente:

	- Si es posible, debe apagar las máquinas primarias antes de ejecutar una conmutación por error no planeada. Esto garantiza que la máquina de origen y de réplica no se ejecuten al mismo tiempo. Si replica máquinas virtuales de VMware, puede especificar que Site Recovery debe hacer su mayor esfuerzo para apagar las máquinas de origen. En función del estado del sitio primario, esto podría funcionar o no. Si replica servidores físicos, Site Recovery no ofrece esta opción.
	- Cuando ejecuta una conmutación por error no planeada, se detiene la replicación de datos desde las máquinas principales para que no se transfiera ningún diferencial de datos después de que comienza una conmutación por error no planeada. Además, si ejecuta una conmutación por error no planeada en un plan de recuperación, se ejecutará hasta que haya finalizado, incluso si se produce un error.

### Preparación para la conexión a las máquinas virtuales de Azure después de la conmutación por error

Si desea conectarse a máquinas virtuales de Azure mediante RDP después de la conmutación por error, asegúrese de que hace lo siguiente:

**En la máquina local antes de la conmutación por error**:

- Para acceder a través de Internet, habilite el protocolo RDP, asegúrese de que se agregan las reglas TCP y UDP para **Público**, y asegúrese de que el protocolo RDP está permitido en **Firewall de Windows** -> **Aplicaciones y características permitidas** para todos los perfiles.
- Para acceder a través de una conexión de sitio a sitio, habilite el protocolo RDP en la máquina y asegúrese de que está permitido en **Firewall de Windows** -> **Aplicaciones y características permitidas** para redes de **Dominio** y **Privadas**.
- Instale el [agente de máquina virtual de Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) en la máquina local.
- [Instale manualmente la instancia de Mobility Service](#install-the-mobility-service-manually) en las máquinas, en lugar de usar el servidor de procesos para insertar el servicio automáticamente. Esto se debe a que la instalación de inserción solo se produce después de habilitar la máquina para la replicación.
- Asegúrese de que la directiva SAN del sistema operativo está establecida en OnlineAll. [Más información](https://support.microsoft.com/kb/3031135)
- Desactive el servicio IPSec antes de ejecutar la conmutación por error.

**En la máquina virtual de Azure después de la conmutación por error**:

- Agregue un punto de conexión público para el protocolo RDP (puerto 3389) y especifique las credenciales de inicio de sesión.
- Asegúrese de no tener ninguna directiva de dominio que impida que se conecte a una máquina virtual mediante una dirección pública.
- Intente conectarse. Si no se puede conectar, compruebe que se está ejecutando la máquina virtual. Para obtener más sugerencias de solución de problemas, consulte este [artículo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


Si desea acceder a una máquina virtual de Azure con Linux después de la conmutación por error mediante un cliente de Secure Shell (ssh), haga lo siguiente:

**En la máquina local antes de la conmutación por error**:

- Asegúrese de que el servicio de Secure Shell en la máquina virtual de Azure está configurado para iniciarse automáticamente en el arranque del sistema.
- Compruebe que las reglas de firewall permiten una conexión SSH.

**En la máquina virtual de Azure después de la conmutación por error**:

- Las reglas del grupo de seguridad de red de la máquina virtual conmutada por error y la subred de Azure a la que esta se conecta necesitan permitir las conexiones entrantes al puerto SSH.
- Debe crearse un punto de conexión público para permitir las conexiones entrantes en el puerto SSH (de forma predeterminada es el puerto TCP 22).
- Si se accede a la máquina virtual a través de una conexión VPN (Express Route o VPN de sitio a sitio), el cliente puede utilizarse para conectarse directamente a la máquina virtual a través de SSH.



## Ejecución de una conmutación por error de prueba

1. Para conmutar por error una sola máquina, en **Configuración** > **Elementos replicados**, haga clic en el icono Máquina virtual > **+Conmutación por error**.

	![Conmutación por error de prueba](./media/site-recovery-vmware-to-azure/test-failover1.png)

2. Para conmutar por error un plan de recuperación, en **Configuración** > **Planes de recuperación**, haga clic con el botón derecho en el plan > **Conmutación por error de prueba**. Para crear un plan de recuperación, [siga estas instrucciones](site-recovery-create-recovery-plans.md).

3. En **Conmutación por error de prueba**, seleccione la red de Azure a la que se conectarán las máquinas virtuales después de la conmutación por error.
4. Haga clic en **Aceptar** para iniciar la conmutación por error. Puede hacer un seguimiento del progreso haciendo clic en la máquina virtual para abrir sus propiedades o en el trabajo de **Conmutación por error de prueba** en Nombre de almacén > **Configuración** > **Trabajos** > **Site Recovery jobs** (Trabajos de Site Recovery).
5. Cuando la conmutación por error alcance el estado **Completar prueba**, haga lo siguiente:

	1. Vea la máquina virtual de réplica en el portal de Azure. Compruebe que la máquina virtual se inicia correctamente.
	2. Si está configurando para acceder a máquinas virtuales desde la red local puede iniciar una conexión de Escritorio remoto a la máquina virtual.
	3. Haga clic en **Completar prueba** para terminarla.

		![Conmutación por error de prueba](./media/site-recovery-vmware-to-azure/test-failover6.png)


	4. Haga clic en **Notas** para registrar y guardar las observaciones asociadas a la conmutación por error de prueba.
	5. Haga clic en **La conmutación por error de prueba se ha completado** para limpiar automáticamente el entorno de prueba. Cuando haga esto, la conmutación por error de prueba mostrará el estado **Completado**.
	6.  Se eliminarán todos los elementos o las máquinas virtuales que Site Recovery creó automáticamente durante la conmutación por error de prueba. No se eliminarán los elementos adicionales que haya creado para la conmutación por error de prueba.

	> [AZURE.NOTE] Si una conmutación por error de prueba continua durante más de dos semanas, se dará por terminada de manera forzada.


6. Cuando se complete la conmutación por error, debería ver la máquina de réplica de Azure en el Portal de Azure > **Máquinas virtuales**. Debe asegurarse de que la máquina virtual tiene el tamaño adecuado, que se ha conectado a la red correspondiente y que se está ejecutando.
7. Si se [preparó para las conexiones después de la conmutación por error](#prepare-to-connect-to-azure-vms-after-failover), debe ser capaz de conectarse a la máquina virtual de Azure.

## Supervisión de la implementación

Le mostramos cómo puede supervisar la configuración y el estado de la implementación de Site Recovery:

1. Haga clic en el nombre del almacén para acceder al panel de **Essentials**. En este panel puede ver los trabajos de Site Recovery, el estado de la replicación, los planes de recuperación, y el estado y los eventos del servidor. Puede personalizar Essentials para mostrar los iconos y los diseños que sean más útiles para usted, incluido el estado de otros almacenes de Copia de seguridad y Site Recovery.

![Essentials](./media/site-recovery-vmware-to-azure/essentials.png)

2. En el icono de **Estado** puede supervisar los servidores del sitio (servidores VMM o de configuración) que están experimentando el problema y los eventos provocados por Site Recovery en las últimas 24 horas.
3. Puede administrar y supervisar la replicación en los iconos de **Elementos replicados**, **Planes de recuperación** y **Site Recovery Jobs** (Trabajos de Site Recovery). Puede ver más detalles de los trabajos en **Configuración** -> **Trabajos** -> **Site Recovery Jobs** (Trabajos de Site Recovery).


## Implementar servidores de procesos adicionales

Si debe escalar horizontalmente la implementación a más de 200 máquinas de origen o si la tasa de renovación diaria total supera los 2 TB, necesitará servidores de procesos adicionales para controlar el volumen del tráfico.

Consulte las [recomendaciones de tamaño de los servidores de procesos](#size-recommendations-for-the-process-server) y luego siga estas instrucciones para configurar el servidor de procesos. Después de configurar el servidor, debe migrar las máquinas de origen para que lo utilicen.

### Instalación de un servidor de procesos adicional

1. En **Configuración** > **Site Recovery servers** (Servidores de Site Recovery), haga clic en el servidor de configuración > **Servidor de procesos**.

	![Agregar servidores de procesos](./media/site-recovery-vmware-to-azure/migrate-ps1.png)

2. En **Tipo de servidor**, haga clic en **Process server (on-premises)** (Servidor de procesos - local).

	![Agregar servidores de procesos](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

3. Descargue el archivo de instalación unificada de Site Recovery y ejecútelo para instalar el servidor de procesos y registrarlo en el almacén.
4. En **Antes de comenzar**, seleccione **Agregar servidores de procesos adicionales para el escalado horizontal de la implementación**.
5. Complete el asistente tal y como hizo cuando [instaló](#step-2-set-up-the-source-environment) el servidor de configuración.

	![Agregar servidores de procesos](./media/site-recovery-vmware-to-azure/add-ps1.png)

6. En **Configuration Server Details** (Detalles del servidor de configuración), especifique la dirección IP del servidor de configuración y la frase de contraseña. Para obtener la frase de contraseña, ejecute **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –n** en el servidor de configuración.

	![Agregar servidores de procesos](./media/site-recovery-vmware-to-azure/add-ps2.png)

### Migrar máquinas para utilizar el nuevo servidor de procesos

1. En **Configuración** > **Site Recovery servers** (Servidores de Site Recovery), haga clic en el servidor de configuración y luego expanda **Servidores de procesos**.

	![Actualizar servidor de procesos](./media/site-recovery-vmware-to-azure/migrate-ps2.png)

2. Haga clic con el botón derecho en el servidor de procesos que se utiliza actualmente y luego haga clic en **Cambiar**.

	![Actualizar servidor de procesos](./media/site-recovery-vmware-to-azure/migrate-ps3.png)

3. En **Seleccionar servidor de procesos de destino**, seleccione el nuevo servidor de procesos que desea usar y luego seleccione las máquinas virtuales que controlarán el nuevo servidor de procesos. Haga clic en el icono de información para obtener detalles sobre el servidor. Aparece el espacio promedio que se necesita para replicar cada máquina virtual seleccionada en el nuevo servidor de procesos para ayudarlo a tomar decisiones relacionadas con la carga. Haga clic en la marca de verificación para comenzar a replicar en el nuevo servidor de procesos.

## Permisos de cuenta de VMware

El servidor de procesos puede detectar automáticamente las máquinas virtuales en un servidor vCenter. Para ejecutar la detección automática, deberá [definir un rol (Azure\_Site\_Recovery)](#prepare-an-account-for-automatic-discovery) para permitir que Site Recovery acceda al servidor VMware. Tenga en cuenta que si solo debe migrar máquinas de VMware a Azure y no necesita realizar la conmutación por recuperación desde Azure, basta con que defina un rol de solo lectura. En la tabla siguiente se resumen los permisos de rol necesarios.

**Rol** | **Detalles** | **Permisos**
--- | --- | ---
Rol Azure\_Site\_Recovery | Detección de máquinas virtuales de VMware |Asigne estos privilegios para el servidor vCenter:<br/><br/>Almacén de datos-> Asignar espacio, Examinar almacén de datos, Operaciones de archivo de bajo nivel, Quitar archivo, Actualizar archivos de máquina virtual<br/><br/>Red-> Asignación de red<br/><br/>Recurso -> Asignar máquina virtual a un grupo de recursos, Migrar máquina virtual apagada, Migrar máquina virtual encendida<br/><br/>Tareas -> Crear tarea, actualizar tarea<br/><br/>Máquina virtual -> Configuración<br/><br/>Máquina virtual -> Interactuar -> Responder pregunta, Conexión de dispositivo, Configurar soporte físico de CD, Configurar soporte físico de disquete, Apagar, Encender, Instalación de herramientas de VMware<br/><br/>Máquina virtual -> Inventario -> Crear, Registrar, Anular registro<br/><br/>Máquina virtual -> Aprovisionamiento -> Permitir descarga de máquina virtual, Permitir carga de archivos de máquina virtual<br/><br/>Máquina virtual -> Instantáneas -> Quitar instantáneas
Rol de usuario de vCenter | Detección/conmutación por error de VMware sin apagar la máquina virtual de origen | Asigne estos privilegios para el servidor vCenter:<br/><br/>Objeto de centro de datos –> Propagar a objeto secundario, rol=solo lectura <br/><br/>El usuario se asigna en el nivel de centro de datos y, por lo tanto, tiene acceso a todos los objetos en el centro de datos. Si quiere restringir el acceso, asigne el rol **Sin acceso** con **Propagar a objeto secundario** a los objetos secundarios (hosts vSphere, almacenes de datos, máquinas virtuales y redes).
Rol de usuario de vCenter | Conmutación por error y conmutación por recuperación | Asigne estos privilegios para el servidor vCenter:<br/><br/>Objeto de centro de datos: Propagar a objeto secundario, rol=Azure\_Site\_Recovery<br/><br/>El usuario se asigna en el nivel de centro de datos y, por lo tanto, tiene acceso a todos los objetos del centro de datos. Si quiere restringir el acceso, asigne el rol **Sin acceso** con **Propagar a objeto secundario** al objeto secundario (hosts vSphere, almacenes de datos, máquinas virtuales y redes). 
## Pasos siguientes

- [Más información](site-recovery-failover.md) sobre los diferentes tipos de conmutación por error.
- [Aprenda más sobre la conmutación por recuperación](site-recovery-failback-azure-to-vmware.md) para que las máquinas a las que se realizó la conmutación por error y que se ejecutan en Azure vuelvan al entorno local.

## Avisos e información de software de terceros

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”). Microsoft is the not original author of the Third Party Code. The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only. This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

<!---HONumber=AcomDC_0608_2016-->