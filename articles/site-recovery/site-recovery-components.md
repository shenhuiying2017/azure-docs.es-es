---
title: "¿Cómo funciona Azure Site Recovery? | Microsoft Docs"
description: "Este artículo proporciona información general sobre la arquitectura de Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/05/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 94250becb238adad7983d4b45939eb988411fca3


---
# <a name="how-does-azure-site-recovery-work"></a>¿Cómo funciona Azure Site Recovery?
Lea este artículo para conocer la arquitectura subyacente del servicio Azure Site Recovery y los componentes que hacen que funcione.

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Información general
Las organizaciones necesitan una estrategia de recuperación ante desastres y continuidad empresarial (BCDR) que determine cómo seguirán en funcionamiento y disponibles las aplicaciones, las cargas de trabajo y los datos durante los tiempos de inactividad planeados y no planeados, y cómo recuperar las condiciones de funcionamiento normales lo antes posible. Su estrategia de BCDR se centra en soluciones que mantengan los datos empresariales seguros y recuperables, y garanticen que las cargas de trabajo estarán disponibles continuamente en caso de desastre.

Site Recovery es un servicio de Azure que contribuye a su estrategia de BCDR mediante la coordinación de la replicación de servidores físicos locales y máquinas virtuales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error a la ubicación secundaria para mantener disponibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal. Más información en [¿Qué es Site Recovery?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery en el Portal de Azure
Azure tiene dos [modelos de implementación](../resource-manager-deployment-model.md) diferentes para crear recursos y trabajar con ellos: el modelo de Azure Resource Manager y el modelo clásico de administración de servicios. Azure también tiene dos portales: el [Portal de Azure clásico](https://manage.windowsazure.com/) que admite el modelo de implementación clásico y [Azure Portal](https://portal.azure.com) que es compatible con ambos modelos de implementación.

Site Recovery está disponible tanto en el portal clásico como en el Portal de Azure. En el Portal de Azure clásico, Site Recovery se puede usar con el modelo de administración de servicios clásico. En el Portal de Azure, se pueden usar tanto el modelo de implementación clásica como el modelo de Resource Manager. [Más información](site-recovery-overview.md#site-recovery-in-the-azure-portal) acerca de la implementación con el Portal de Azure.

La información de este artículo se aplica a las tanto a la implementación clásica como a la del Portal de Azure. Las diferencias se indicarán cuando corresponda.

## <a name="deployment-scenarios"></a>Escenarios de implementación
Site Recovery se puede implementar para organizar la replicación en varios escenarios:

* **Réplica de máquinas virtuales de VMware**: las máquinas virtuales VMware locales se pueden replicar en Azure o en un centro de datos secundario.
* * **Réplica de máquinas físicas**: las máquinas físicas que ejecutan Windows o Linux se pueden replicar en Azure o en un centro de datos secundario. El proceso para la replicación de máquinas físicas es prácticamente el mismo que el proceso para la replicación de máquinas virtuales de VMware
* **Réplica de máquinas virtuales de Hyper-V (sin VMM)**: las máquinas virtuales de Hyper-V que no administrada VMM se pueden replicar en Azure.
* **Réplica de máquinas virtuales de Hyper-V administradas en nubes de System Center VMM**: las máquinas virtuales de Hyper-V locales que se ejecutan en servidores host de Hyper-V en nubes de VMM se pueden replicar en Azure o en un centro de datos secundario. La réplica se puede realizar mediante la réplica de Hyper-V estándar o mediante la replicación de SAN.
* **Migración de VM**: Site Recovery se puede usar para [migrar VM de IaaS de Azure](site-recovery-migrate-azure-to-azure.md) de una región a otra o para [migrar instancias de Windows en Amazon Web Services (AWS)](site-recovery-migrate-aws-to-azure.md) a VM de IaaS de Azure. Actualmente solo se admite la migración, lo que significa que estás máquinas virtuales se pueden conmutar por error, pero no es posible conmutarlas por recuperación.

Site Recovery puede replicar la mayoría de las aplicaciones que se ejecutan en estas máquinas virtuales y estos servidores físicos. En [¿Qué cargas de trabajo se pueden proteger con Azure Site Recovery?](site-recovery-workload.md)

## <a name="replicate-to-azure-vmware-virtual-machines-or-physical-windowslinux-servers"></a>Réplica en Azure: máquinas virtuales de VMware o servidores físicos con Windows/Linux
Hay dos maneras de replicar las máquinas virtuales de VMware con Site Recovery.

* **Mediante el Portal de Azure**: al implementar Site Recovery en el Portal de Azure puede conmutar por error máquinas virtuales en el almacenamiento de administrador de servicios clásico o en Resource Manager. La replicación de máquinas virtuales de VMware en el Portal de Azure ofrece varias ventajas, incluida la capacidad de realizar la replicación en almacenamiento clásico o de Resource Manager en Azure. [Más información](site-recovery-vmware-to-azure.md).
* **Mediante el portal clásico**: Site Recovery se puede implementar en el portal clásico mediante una experiencia mejorada. Se debe usar para todas las implementaciones nuevas en el portal clásico. En esta implementación solo se pueden conmutar por error máquinas virtuales en el almacenamiento clásico de Azure, pero no en el almacenamiento de Resource Manager. [Más información](site-recovery-vmware-to-azure-classic.md). También hay un [experiencia heredada](site-recovery-vmware-to-azure-classic-legacy.md) para configurar la replicación de VMware en el portal clásico. No debe utilizarse para nuevas implementaciones.  Si ya ha realizado una implementación mediante la experiencia heredada, [obtenga información acerca de la migración](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) a la implementación mejorada.

Lo requisitos arquitectónicos para implementar Site Recovery para replicar máquinas virtuales de VMware/servidores físicos en el Portal de Azure o en Portal de Azure clásico (mejorada) son similares, solo hay un par de diferencias:

* Si la implementación se realiza en el Portal de Azure, la replicación se puede hacer en un almacenamiento basado en Resource Manager y se pueden usar las redes de Resource Manager para conectar las máquinas virtuales de Azure después de la conmutación por error.
* Si la implementación se realiza en el Portal de Azure, se admiten tanto el almacenamiento LRS como el almacenamiento GRS. En el portal clásico se requiere GRS.
* El proceso de implementación es más simple y fácil de usar en el Portal de Azure.

Esto es lo que necesita:

* **Cuenta de Azure**: necesitará una cuenta de Microsoft Azure.
* **Almacenamiento de Azure**: necesitará una cuenta de Almacenamiento de Azure para almacenar los datos replicados. Puede usar una cuenta clásica o una cuenta de almacenamiento de Resource Manager. Cuando la implementación se realiza en el Portal de Azure, la cuenta puede ser LRS o GRS. Los datos replicados se almacenan en el almacenamiento de Azure y las máquinas virtuales de Azure se ponen en marcha cuando se produce la conmutación por error.
* **Red de Azure**: necesitará una red virtual de Azure a la que se conectarán las máquinas virtuales de Azure cuando se crean en la conmutación por error. En el Portal de Azure pueden ser las redes creadas en el modelo de almacenamiento de administrador de servicios clásico o en el modelo de Resource Manager.
* **Servidor de configuración local**: necesitará una máquina con Windows Server 2012 R2 local que ejecuta el servidor de configuración y otros componentes de Site Recovery. Si va a replicar máquinas virtuales de VMware, debe ser una máquina virtual VMware con alta disponibilidad. Si desea replicar servidores físicos, la máquina puede ser física. Estos componentes de Site Recovery se instalará en la máquina:
  * **Servidor de configuración**: coordina la comunicación entre el entorno local y Azure, además de administrar la replicación y recuperación de datos.
  * **Servidor de procesos**: actúa como puerta de enlace de replicación. Recibe datos de replicación de máquinas de origen protegidas, los optimiza con almacenamiento en caché, compresión y cifrado, y los envía al Almacenamiento de Azure. También controla la instalación de inserción del Servicio de movilidad en máquinas protegidas y realiza al detección automática de máquinas virtuales de VMware. A medida que crezca la implementación, podrá agregar más servidores de procesos dedicados independientes para controlar mayores volúmenes de tráfico de replicación.
  * **Servidor de destino maestro**: controla los datos de replicación durante la conmutación por recuperación desde Azure.
* **Máquinas virtuales WMware o servidores físicos que se replican**: todas las máquinas que desee replicar en Azure deberán tener instalado el componente Mobility Service. Este servicio captura las escrituras de datos en la máquina y las reenvía al servidor de procesos. Este componente se puede instalar manualmente, o bien puede insertarlo e instalarlo automáticamente el servidor de procesos cuando se habilita la replicación en una máquina.
* **Hosts de vSphere y servidor de vCenter**: necesitará uno o varios servidores de host de vSphere en los que se ejecuten máquinas virtuales de VMware. Se recomienda implementar un servidor vCenter para administrar dichos hosts.
* **Conmutación por recuperación**: esto es lo que necesita:
  * **No se admite la conmutación por recuperación de físico a físico**: esto significa que si realiza la conmutación por error de los servidores físicos a Azure y luego desea realizar la conmutación por recuperación, debe realizarla a una máquina virtual de VMware. No se puede realizar la conmutación por recuperación a un servidor físico. Necesitará una máquina virtual de Azure a la que realizar la conmutación por recuperación y si no ha implementado el servidor de configuración como una máquina virtual de VMware, será preciso que configure un servidor de destino maestro independiente como una máquina virtual de VMware. Esto es necesario porque el servidor de destino maestro interactúa y se adjunta al almacenamiento de VMware para restaurar los discos a una máquina virtual de VMware.
  * * **Servidor de procesos temporal en Azure**: si desea realizar la conmutación por recuperación desde Azure después de una conmutación por error, será preciso que configure una máquina virtual de Azure como servidor de procesos para que controle la replicación desde Azure. Dicha máquina virtual se puede eliminar cuando finalice la conmutación por recuperación.
  * **Conexión VPN**: para realizar la conmutación por recuperación, necesitará que se haya configurado una conexión VPN (o Azure ExpressRoute) desde la red de Azure y el sitio local.
  * **Servidor de destino maestro local independiente**: el servidor de destino maestro local controla la conmutación por recuperación. El servidor de destino maestro se instala de forma predeterminada en el servidor de administración, pero si se va a realizar la conmutación por recuperación de grandes volúmenes de tráfico, se debe configurar un servidor de destino maestro local independiente para este propósito.

**Arquitectura general**

![Mejorada](./media/site-recovery-components/arch-enhanced.png)

**Componentes de implementación**

![Mejorada](./media/site-recovery-components/arch-enhanced2.png)

**Conmutación por recuperación**

![Conmutación tras recuperación mejorada](./media/site-recovery-components/enhanced-failback.png)

* [Más información](site-recovery-vmware-to-azure.md#azure-prerequisites) acerca de los requisitos para la implementación del Portal de Azure.
* [Más información](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) acerca de los requisitos de la implementación mejorada en el portal clásico.
* [Más información](site-recovery-failback-azure-to-vmware.md) acerca de la conmutación por recuperación en Azure Portal.
* [Más información](site-recovery-failback-azure-to-vmware-classic.md) acerca de la conmutación por recuperación en el portal clásico.

## <a name="replicate-to-azure-hyperv-vms-not-managed-by-vmm"></a>Replicación en Azure: máquinas virtuales de Hyper-V no administradas por VMM
Puede replicar las máquinas virtuales de Hyper-V que no están administradas por System Center VMM en Azure con Site Recovery como se indica a continuación:

* **Mediante el Portal de Azure**: al implementar Site Recovery en el Portal de Azure puede conmutar por error máquinas virtuales en el almacenamiento clásico o en Resource Manager. [Más información](site-recovery-hyper-v-site-to-azure.md).
* **Mediante el portal clásico**: Site Recovery se puede implementar en el portal clásico. En esta implementación solo se pueden conmutar por error máquinas virtuales en el almacenamiento clásico de Azure, pero no en el almacenamiento de Resource Manager. [Más información](site-recovery-hyper-v-site-to-azure-classic.md).

La arquitectura de ambas implementaciones es similar, salvo que:

* Si la implementación se realiza en el Portal de Azure, la replicación se puede hacer en un almacenamiento de Resource Manager y se pueden usar las redes de Resource Manager para conectar las máquinas virtuales de Azure después de la conmutación por error.
* El proceso de implementación es más simple y fácil de usar en el Portal de Azure.

Esto es lo que necesita:

* **Cuenta de Azure**: necesitará una cuenta de Microsoft Azure.
* **Almacenamiento de Azure**: necesitará una cuenta de Almacenamiento de Azure para almacenar los datos replicados. En el Portal de Azure, puede usar una cuenta clásica o una cuenta de almacenamiento de Resource Manager. En el portal clásico, solo se pueden usar cuentas clásicas. Los datos replicados se almacenan en el almacenamiento de Azure y las máquinas virtuales de Azure se crean cuando se produce la conmutación por error.
* **Red de Azure**: necesitará una red de Azure a la que se conectarán las máquinas virtuales de Azure cuando se crean después de la conmutación por error.
* **Host de Hyper-V**: necesitará uno o varios servidores host de Hiper-V con Windows Server 2012 R2. Durante la implementación de Site Recovery, instalará el proveedor de Azure Site Recovery y el Agente de Microsoft Azure Recovery Services en el host.
* **Máquinas virtuales de Hyper-V**: necesitará una o varias máquinas virtuales en el servidor host de Hyper-V. El proveedor de Azure Site Recovery y el agente de Servicios de recuperación de Microsoft Azure en el servidor host de Hyper-V durante la implementación de Site Recovery. El proveedor coordina y organiza la replicación con el servicio Site Recovery a través de Internet. El agente se encarga de la replicación de datos a través de HTTPS 443. Las comunicaciones del proveedor y el agente son seguras y cifradas. También se cifran los datos replicados en el almacenamiento de Azure.

**Arquitectura general**

![Sitio de Hyper-V a Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

* [Más información](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) acerca de los requisitos para la implementación del Portal de Azure.
* [Más información](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) acerca de los requisitos para la implementación del portal clásico.

## <a name="replicate-to-azure-hyperv-vms-managed-by-vmm"></a>Replicación en Azure: máquinas virtuales de Hyper-V administradas por VMM
Las máquinas virtuales de Hyper-V en nubes de VMM se pueden replicar en Azure con Site Recovery como se indica a continuación:

* **Mediante el Portal de Azure**: al implementar Site Recovery en el Portal de Azure puede conmutar por error máquinas virtuales en el almacenamiento clásico o en Resource Manager. [Más información](site-recovery-vmm-to-azure.md).
* **Mediante el portal clásico**: Site Recovery se puede implementar en el portal clásico. En esta implementación solo se pueden conmutar por error máquinas virtuales en el almacenamiento clásico de Azure, pero no en el almacenamiento de Resource Manager. [Más información](site-recovery-vmm-to-azure-classic.md).

La arquitectura de ambas implementaciones es similar, salvo que:

* Si la implementación se realiza en el Portal de Azure, la replicación se puede hacer en un almacenamiento basado en Resource Manager y se pueden usar las redes de Resource Manager para conectar las máquinas virtuales de Azure después de la conmutación por error.
* El proceso de implementación es más simple y fácil de usar en el Portal de Azure.

Esto es lo que necesita:

* **Cuenta de Azure**: necesitará una cuenta de Microsoft Azure.
* **Almacenamiento de Azure**: necesitará una cuenta de Almacenamiento de Azure para almacenar los datos replicados. En el Portal de Azure, puede usar una cuenta clásica o una cuenta de almacenamiento de Resource Manager. En el portal clásico, solo se pueden usar cuentas clásicas. Los datos replicados se almacenan en el almacenamiento de Azure y las máquinas virtuales de Azure se crean cuando se produce la conmutación por error.
* **Red de Azure**: será preciso que configure la asignación de red de forma que las máquinas virtuales de Azure estén conectadas a las redes apropiadas cuando se crean después de la conmutación por error.
* **Servidor VMM**: se necesitan uno o varios servidores VMM locales que se ejecuten en System Center 2012 R2 y configurados con una o varias nubes privadas. Si la implementación se realiza en el Portal de Azure portal, se necesitarán redes lógicas y de máquinas virtuales para poder configurar la asignación de red. En el portal clásico es opcional.  Una red de VM debe estar vinculada a una red lógica asociada con la nube.
* **Host de Hyper-V**: necesitará uno o varios servidores host de Hiper-V con Windows Server 2012 R2 en la nube de VMM.
* **Máquinas virtuales de Hyper-V**: necesitará una o varias máquinas virtuales en el servidor host de Hyper-V.

**Arquitectura general**

![VMM a Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

* [Más información](site-recovery-vmm-to-azure.md#azure-requirements) acerca de los requisitos para la implementación del Portal de Azure.
* [Más información](site-recovery-vmm-to-azure-classic.md#before-you-start) acerca de los requisitos para la implementación del portal clásico.

## <a name="replicate-to-a-secondary-site-vmware-virtual-machines-or-physical-servers"></a>Replicación en un sitio secundario: máquinas virtuales de VMware o servidores físicos
Para replicar máquinas virtuales de VMware o servidores físicos en un sitio secundario, descargue InMage Scout, que se incluye en la suscripción de Azure Site Recovery. Se puede descargar desde el Portal de Azure o desde el Portal de Azure clásico.

Configure los servidores de componentes de cada sitio (configuración, proceso y destino principal) e instale Unified Agent en las máquinas que desee replicar. Después de la replicación inicial el agente de cada máquina envía los cambios de replicación diferencial al servidor de procesos. Por su parte, el servidor de procesos optimiza los datos y los transfiere al servidor de destino maestro del sitio secundario. El servidor de configuración administra el proceso de replicación.

Esto es lo que necesita:

**Cuenta de Azure**: este escenario se implementa mediante InMage Scout. Para obtenerlo, necesitará una suscripción a Azure. Después de crear un almacén de Site Recovery descargue InMage Scout e instale las actualizaciones más recientes para configurar la implementación.
**Servidor de procesos (sitio principal)**: configure el componente de servidor de procesos en el sitio principal que controla el almacenamiento en caché, la compresión y la optimización de los datos. También controla la instalación por inserción del agente unificada en las máquinas que desea proteger.
**VMware ESX/ESXi y servidor vCenter (servidor principal)**: si va a proteger máquinas virtuales de VMware, necesitará un hipervisor VMware EXS/ESXi y, opcionalmente, un servidor de VMware vCenter que administre hipervisores.

* **Máquinas virtuales/servidores físicos (servidor principal)**: las máquinas virtuales de VMware o los servidores físicos con Windows o Linux que desee proteger deberán tener Unified Agent instalado. También se instala el agente unificado en las máquinas que actúan de servidor de destino maestro. Dicho agente actúa como un proveedor de comunicación entre todos los componentes.
* * **Servidor de configuración (sitio secundario)**: el servidor de configuración es el primer componente que se instala, y se instala en el sitio secundario para administrar, configurar y supervisar la implementación, ya sea mediante el sitio web de administración o la consola de vContinuum. Solo hay un servidor de configuración en una implementación y debe instalarse en una máquina que se ejecute en Windows Server 2012 R2.
* **Servidor de vContinuum (sitio secundario)**: se instala en la misma ubicación (sitio secundario) que el servidor de configuración. Proporciona una consola para administrar y supervisar su entorno protegido. En una instalación predeterminada, el servidor de vContinuum es el primer servidor de destino maestro y tiene instalado Unified Agent.
* **Servidor de destino maestro (sitio secundario)**: el servidor de destino maestro contiene los datos replicados. Recibe los datos del servidor de proceso, crea una máquina de réplica en el sitio secundario y contiene los puntos de retención de datos. El número de servidores de destino maestros que necesita depende del número de equipos que va a proteger. Si desea realizar una conmutación por recuperación al sitio principal, necesitará un servidor de destino maestro allí también.

**Arquitectura general**

![VMware a VMware](./media/site-recovery-components/vmware-to-vmware.png)

## <a name="replicate-to-a-secondary-site-hyperv-vms-managed-by-vmm"></a>Replicación en un sitio secundario: máquinas virtuales de Hyper-V administradas por VMM
Puede replicar las máquinas virtuales de Hyper-V que están administradas por System Center VMM en un centro de datos secundario con Site Recovery como se indica a continuación:

* **Mediante el Portal de Azure**: cuando se implementa Site Recovery en el Portal de Azure. [Más información](site-recovery-hyper-v-site-to-azure.md).
* **Mediante el portal clásico**: Site Recovery se puede implementar en el portal clásico. [Más información](site-recovery-hyper-v-site-to-azure-classic.md).

La arquitectura de ambas implementaciones es similar, salvo que:

* Si realiza la implementación en el Portal de Azure, debe configurar la asignación de red. Esto es opcional en el portal clásico.
* El proceso de implementación es más simple y fácil de usar en el Portal de Azure.
* * Si realiza la implementación en el Portal de Azure clásico, la [asignación de almacenamiento](site-recovery-storage-mapping.md) estará disponible.

Esto es lo que necesita:

* **Cuenta de Azure**: necesitará una cuenta de Microsoft Azure.
* **Servidor VMM**: se recomienda que haya un servidor VMM en el sitio principal y otro en el sitio secundario, y que cada uno contenga al menos una nube privada de VMM. El servidor debe ejecutar al menos System Center 2012 SP1 con las actualizaciones más recientes y que esté conectado a Internet. Las nubes deben tener establecido el perfil de funcionalidad de Hyper-V. El proveedor de Azure Site Recovery se instalará en el servidor VMM. El proveedor coordina y organiza la replicación con el servicio Site Recovery a través de Internet. Las comunicaciones entre el proveedor y Azure son seguras y cifradas.
* **Servidor Hyper-V**: los servidores host de Hyper-V se deben encontrar en las nubes VMM principal y secundaria. Los servidores host se deben ejecutar como mínimo en Windows Server 2012 con las últimas actualizaciones instaladas y estar conectados a Internet. Los datos se replican entre los servidores host de Hyper-V principales y secundarios a través de la LAN o VPN mediante autenticación Kerberos o de certificado.  
* **Máquinas protegidas**: el servidor host de Hyper-V de origen debe tener, como mínimo, una máquina virtual que se desee proteger.

**Arquitectura general**

![De local a local](./media/site-recovery-components/arch-onprem-onprem.png)

* [Más información](site-recovery-vmm-to-vmm.md#azure-prerequisites) acerca de los requisitos de implementación en el Portal de Azure.
* * [Más información](site-recovery-vmm-to-vmm-classic.md#before-you-start) acerca de los requisitos para la implementación del Portal de Azure clásico.

## <a name="replicate-to-a-secondary-site-with-san-replication-hyperv-vms-managed-by-vmm"></a>Replicación en un sitio secundario con la replicación de SAN: máquinas virtuales de Hyper-V administradas por VMM
Puede replicar máquinas virtuales de Hyper-V que se administran en nubes VMM en un sitio secundario que use la replicación de SAN mediante el Portal de Azure clásico. Este escenario no se admite actualmente en el nuevo Portal de Azure.

En este escenario, durante la implementación de Site Recovery, será preciso que el proveedor de Azure Site Recovery se instale en los servidores VMM. El proveedor coordina y organiza la replicación con el servicio Site Recovery a través de Internet. Los datos se replican entre las matrices de almacenamiento principal y secundaria mediante replicación SAN sincrónica.

Esto es lo que necesita:

**Cuenta de Azure**: necesitará una suscripción de Azure

* **Matriz SAN**: una [matriz SAN admitida](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) administrada por el servidor VMM principal. La SAN comparte infraestructura de red con otra matriz de SAN en el sitio secundario.
* **Servidor VMM**: se recomienda que haya un servidor VMM en el sitio principal y otro en el sitio secundario, y que cada uno contenga al menos una nube privada de VMM. El servidor debe ejecutar al menos System Center 2012 SP1 con las actualizaciones más recientes y que esté conectado a Internet. Las nubes deben tener establecido el perfil de funcionalidad de Hyper-V.
* **Servidor de Hyper-V**: los servidores host de Hyper-V se encuentran en las nubes VMM principal y secundaria. Los servidores host se deben ejecutar como mínimo en Windows Server 2012 con las últimas actualizaciones instaladas y estar conectados a Internet.
* **Máquinas protegidas**: el servidor host de Hyper-V de origen debe tener, como mínimo, una máquina virtual que se desee proteger.

**Arquitectura de replicación de SAN**

![Replicación de SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

[Más información](site-recovery-vmm-san.md#before-you-start) acerca de los requisitos para la implementación.

### <a name="onpremises"></a>Local
## <a name="hyperv-protection-lifecycle"></a>Ciclo de vida de protección de Hyper-V
Este flujo de trabajo muestra el proceso de protección, replicación y conmutación por error de máquinas virtuales Hyper-V.

1. **Habilitar la protección**: se configura el almacén de Site Recovery, se configuran las opciones de replicación para una nube de VMM o un sitio de Hyper-V y se habilita la protección de las máquinas virtuales. Se inicia un trabajo llamado **Habilitar la protección** y se puede supervisar en la pestaña **Trabajos**. El trabajo comprueba que el equipo cumple los requisitos previos y después se invoca el método [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) donde se configura la replicación en Azure con la configuración establecida. El trabajo **Habilitar la protección** también invoca el método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) para inicializar una replicación completa de la máquina virtual.
2. **Replicación inicial**: se toma una instantánea de la máquina virtual y se replican los discos duros virtuales uno por uno hasta que todos se copian en Azure o en el centro de datos secundario. El tiempo necesario para completar esta tarea depende del tamaño de la máquina virtual, del ancho de banda de la red y del método de replicación inicial. Si se producen cambios en el disco mientras la replicación inicial está en curso, el seguimiento de replicaciones de Réplica de Hyper-V realiza un seguimiento de esos cambios en registros de replicación de Hyper-V (.hrl) que se encuentran en la misma carpeta que los discos. Cada disco tiene un archivo .hrl asociado que se enviará al almacenamiento secundario. Tenga en cuenta que los archivos de instantáneas y de registro consumen recursos de disco mientras la replicación inicial está en curso. Cuando la replicación inicial finaliza, se elimina la instantánea de la máquina virtual y se sincronizan y se combinan los cambios diferenciales del disco en el registro.
3. **Finalizar la protección**: una vez que finaliza la replicación inicial, el trabajo **Finalizar la protección** configura la red y otras opciones posteriores a la replicación, con el fin de que la máquina virtual esté protegida. Si va a replicar en Azure, debe ajustar la configuración de la máquina virtual para que esté preparada para conmutación por error. En este momento puede ejecutar una conmutación por error de prueba para comprobar que todo funciona según lo esperado.
4. **Replicación**: después de la replicación inicial comienza la sincronización diferencial según la configuración de replicación.
   * **Error de replicación**: si se produce un error en la replicación diferencial y una replicación completa sería costosa, en términos de ancho de banda o de tiempo, se produce una resincronización. Por ejemplo, si los archivos .hrl alcanzan el 50% del tamaño del disco, la máquina virtual se marcará para su resincronización. La resincronización minimiza la cantidad de datos que se envían; para ello, calcula las sumas de comprobación de las máquinas virtuales de origen y destino y envía solo los datos diferenciales. Una vez finalizada la resincronización, se reanudará la replicación diferencial. De forma predeterminada, la resincronización está programada para ejecutarse automáticamente fuera del horario de oficina, pero puede resincronizar una máquina virtual manualmente.
   * **Error de replicación**: si se produce un error de replicación, se realiza un reintento de forma predefinida. Si el error es irrecuperable, como un error de autenticación o de autorización, o una máquina de réplica se encuentra en un estado no válido, no se realizará ningún reintento. Si se produce un error recuperable, como un error de red o poco espacio en disco o memoria, se realizarán reintentos, con intervalos cada vez mayores entre cada reintento (1, 2, 4, 8, 10 y después cada 30 minutos).
5. **Conmutaciones por error planeadas y no planeadas**: las conmutaciones por error planeadas o no planeadas se pueden ejecutar cuando sea necesario. Si ejecuta una conmutación por error planeada, las máquinas virtuales de origen se apagan para que no haya pérdida de datos. Después de que se crean máquinas virtuales de réplica, se colocan en estado pendiente de confirmación. Para completar la conmutación por error será preciso que las confirmen, a menos que vaya a realizar una réplica con SAN, en cuyo caso la confirmación es automática. Una vez configurado y en marcha el sitio primario, puede producirse la conmutación por recuperación. Si replicó en Azure, la replicación inversa es automática. De lo contrario, inicie una replicación inversa de forma manual.

![flujo de trabajo](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Pasos siguientes
[Preparación de la implementación](site-recovery-best-practices.md)




<!--HONumber=Nov16_HO2-->


