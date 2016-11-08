---
title: ¿Cómo funciona Azure Site Recovery? | Microsoft Docs
description: Este artículo proporciona información general sobre la arquitectura de Site Recovery
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: cfreeman
editor: ''

ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/13/2016
ms.author: raynew

---
# <a name="how-does-azure-site-recovery-work?"></a>¿Cómo funciona Azure Site Recovery?
Lea este artículo para conocer la arquitectura del servicio Azure Site Recovery y los componentes que hacen que funcione.

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Información general
Necesita una estrategia de recuperación ante desastres y continuidad del negocio (BCDR) para mantener en funcionamiento y disponibles las cargas de trabajo y los datos durante los tiempos de inactividad planeados y no planeados, y recuperar las condiciones de funcionamiento normales lo antes posible. 

Site Recovery es un servicio de Azure que contribuye a su estrategia de recuperación ante desastres y continuidad del negocio. Este servicio organiza la replicación de máquinas virtuales y servidores físicos locales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error a la ubicación secundaria para mantener disponibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal. Información general en [¿Qué es Site Recovery?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery en el Portal de Azure
Azure tiene dos [modelos de implementación](../resource-manager-deployment-model.md) diferentes para crear recursos y trabajar con ellos: el modelo de Azure Resource Manager y el modelo clásico de administración de servicios. Azure también tiene dos portales: el [Portal de Azure clásico](https://manage.windowsazure.com/) que admite el modelo de implementación clásico y [Azure Portal](https://portal.azure.com) que es compatible con ambos modelos de implementación.

* Site Recovery está disponible tanto en el portal clásico como en el Portal de Azure.
* En el Portal de Azure clásico, Site Recovery se puede usar con el modelo de administración de servicios clásico.
* En Azure Portal, se pueden usar tanto el modelo de implementación clásica como el modelo de Resource Manager. 

La información de este artículo se aplica tanto a la implementación clásica como a la del Portal de Azure. La tabla resume todas las diferencias.

| **Replicar** | **Portal de Azure** | **Portal clásico** |
| --- | --- | --- |
| **Replicación de VM de VMware en Azure** |Proceso de implementación simplificado.<br/><br/> Conmute por error VM en almacenamiento clásico o basado en Resource Manager.<br/><br/> Replique en almacenamiento clásico o basado en Resource Manager.<br/><br/> Use redes clásicas o de Resource Manager para conectar las VM de Azure después de la conmutación por error.<br/><br/> Use almacenamiento con redundancia local o almacenamiento con redundancia geográfica. |Conmute por error solo en almacenamiento clásico.<br/><br/> Use únicamente redes clásicas para conectar VM después de la conmutación por error.<br/><br/> Use almacenamiento con redundancia geográfica. |
| **Replicación de VM de Hyper-V (sin VMM) a Azure** |Proceso de implementación simplificado.<br/><br/> Conmute por error VM en almacenamiento clásico o basado en Resource Manager.<br/><br/> Replique en almacenamiento clásico o basado en Resource Manager.<br/><br/> Use redes clásicas o de Resource Manager para conectar las VM de Azure después de la conmutación por error. | |
| **Replicación de máquinas virtuales de Hyper-V (con VMM) a Azure** |Proceso de implementación simplificado.<br/><br/> Conmute por error VM en almacenamiento clásico o basado en Resource Manager.<br/><br/> Replique en almacenamiento clásico o basado en Resource Manager.<br/><br/> Use redes clásicas o de Resource Manager para conectar las VM de Azure después de la conmutación por error.<br/><br/> Debe configurar la asignación de red |Conmute por error solo en almacenamiento clásico.<br/><br/> Use únicamente redes clásicas para conectar VM después de la conmutación por error. |
| **Replicación de máquinas virtuales de Hyper-V (con VMM) a un sitio secundario** |Proceso de implementación simplificado.<br/><br/> Conmute por error VM en almacenamiento clásico o basado en Resource Manager.<br/><br/> Replique en almacenamiento clásico o basado en Resource Manager.<br/><br/> Use redes clásicas o de Resource Manager para conectar las VM de Azure después de la conmutación por error.<br/><br/> Debe configurar la asignación de red |Conmute por error solo en almacenamiento clásico.<br/><br/> Use únicamente redes clásicas para conectar VM después de la conmutación por error.<br/><br/> Puede configurar la asignación de almacenamiento. <br/><br/> No se admite la replicación de SAN. |

## <a name="deployment-scenarios"></a>Escenarios de implementación
Site Recovery se puede implementar para organizar la replicación en varios escenarios:

* **Réplica de VM de VMware**: las máquinas virtuales de VMware locales se pueden replicar en Azure o en un centro de datos secundario.
* **Réplica de máquinas físicas**: las máquinas físicas que ejecutan Windows o Linux se pueden replicar en Azure Storage o en un centro de datos secundario. El proceso para la replicación de máquinas físicas es prácticamente el mismo que el proceso para la replicación de VM de VMware.
* **Réplica de VM de Hyper-V**: si los hosts de Hyper-V se administran en nubes de VMM de System Center, las VM se pueden replicar en Azure o en un centro de datos secundario. Si los hosts no se administran con VMM, solo puede replicar en Azure. Puede replicar las VM de Hyper-V que no estén administradas por VMM en Azure Storage.
* **Migración de VM**: Site Recovery se puede usar para [migrar VM de IaaS de Azure](site-recovery-migrate-azure-to-azure.md) de una región a otra o para [migrar instancias de Windows en Amazon Web Services (AWS)](site-recovery-migrate-aws-to-azure.md) a VM de IaaS de Azure. Actualmente no se admite la replicación completa. Puede replicar para la migración (conmutación por error), pero no conmutar por recuperación. 

Site Recovery puede replicar la mayoría de las aplicaciones que se ejecutan en estas máquinas virtuales y estos servidores físicos. En [¿Qué cargas de trabajo se pueden proteger con Azure Site Recovery?](site-recovery-workload.md)

## <a name="replicate-vmware-virtual-machines-to-azure"></a>Replicación de máquinas virtuales de VMware en Azure
![Mejorada](./media/site-recovery-components/arch-enhanced.png)

| **Componente** | **Detalles** |
| --- | --- |
| **Las tablas de Azure** |**Cuenta**: necesitará una cuenta de Azure.<br/><br/> **Almacenamiento**: necesitará una cuenta de almacenamiento de Azure para almacenar los datos replicados. Puede usar una cuenta clásica o una cuenta de almacenamiento de Resource Manager. Cuando la implementación se realiza en el Portal de Azure, la cuenta puede ser LRS o GRS. Los datos replicados se almacenan en el almacenamiento de Azure y las máquinas virtuales de Azure se ponen en marcha cuando se produce la conmutación por error.<br/><br/> **Red**: necesitará una red virtual de Azure a la que se conectarán las VM de Azure cuando se creen en la conmutación por error. En Azure Portal, se pueden crear redes en el modelo de almacenamiento de administrador de servicios clásico o en el modelo de Resource Manager. |
| **Servidor de configuración local** |Necesitará una máquina con Windows Server 2012 R2 local que ejecute el servidor de configuración y otros componentes de Site Recovery.<br/><br/> Debe ser una VM de VMware de alta disponibilidad.<br/><br/> Entre los componentes del servidor se encuentran los siguientes:<br/><br/> **Servidor de configuración**: coordina la comunicación entre el entorno local y Azure, además de administrar la replicación y recuperación de datos.<br/><br/> **Servidor de procesos**: actúa como puerta de enlace de replicación. Recibe datos de replicación de máquinas de origen protegidas, los optimiza con almacenamiento en caché, compresión y cifrado, y los envía al Almacenamiento de Azure. También controla la instalación de inserción del Servicio de movilidad en máquinas protegidas y realiza al detección automática de máquinas virtuales de VMware. A medida que crezca la implementación, podrá agregar más servidores de procesos dedicados independientes para controlar mayores volúmenes de tráfico de replicación.<br/><br/> **Servidor de destino maestro**: controla los datos de replicación durante la conmutación por recuperación desde Azure. |
| **Servidores de virtualización locales** |Uno o varios hosts de vSphere. También se recomienda un servidor vCenter para administrar los hosts. |
| **VM que replicar** |Todas las VM de VMware que desee replicar en Azure deberán tener instalado el componente del Servicio de movilidad. Este servicio captura las escrituras de datos en la máquina y las reenvía al servidor de procesos. Este componente se puede instalar manualmente, o bien puede insertarlo e instalarlo automáticamente el servidor de procesos cuando se habilita la replicación en una máquina. |

* [Más información](site-recovery-vmware-to-azure.md#azure-prerequisites) acerca de los requisitos para la implementación del Portal de Azure.
* [Más información](site-recovery-failback-azure-to-vmware.md) acerca de la conmutación por recuperación en el Portal de Azure.

## <a name="replicate-physical-servers-to-azure"></a>Replicación de servidores físicos a Azure
![Mejorada](./media/site-recovery-components/arch-enhanced.png)

| **Componente** | **Detalles** |
| --- | --- |
| **Las tablas de Azure** |**Cuenta**: necesitará una cuenta de Azure.<br/><br/> **Almacenamiento**: necesitará una cuenta de almacenamiento de Azure para almacenar los datos replicados. Puede usar una cuenta clásica o una cuenta de almacenamiento de Resource Manager. Cuando la implementación se realiza en el Portal de Azure, la cuenta puede ser LRS o GRS. Los datos replicados se almacenan en el almacenamiento de Azure y las máquinas virtuales de Azure se ponen en marcha cuando se produce la conmutación por error.<br/><br/> **Red**: necesitará una red virtual de Azure a la que se conectarán las VM de Azure cuando se creen en la conmutación por error. En Azure Portal, se pueden crear redes en el modelo de almacenamiento de administrador de servicios clásico o en el modelo de Resource Manager. |
| **Servidor de configuración local** |Necesitará una máquina con Windows Server 2012 R2 local que ejecute el servidor de configuración y otros componentes de Site Recovery.<br/><br/> El servidor puede ser físico o virtual.<br/><br/> Entre los componentes del servidor se encuentran los siguientes:<br/><br/> **Servidor de configuración**: coordina la comunicación entre el entorno local y Azure, además de administrar la replicación y recuperación de datos.<br/><br/> **Servidor de procesos**: actúa como puerta de enlace de replicación. Recibe datos de replicación de máquinas de origen protegidas, los optimiza con almacenamiento en caché, compresión y cifrado, y los envía al Almacenamiento de Azure. También controla la instalación de inserción del Servicio de movilidad en máquinas protegidas y realiza al detección automática de máquinas virtuales de VMware. A medida que crezca la implementación, podrá agregar más servidores de procesos dedicados independientes para controlar mayores volúmenes de tráfico de replicación.<br/><br/> **Servidor de destino maestro**: controla los datos de replicación durante la conmutación por recuperación desde Azure. |
| **Máquinas que replicar** |Todas las máquinas físicas Windows o Linux que desee replicar en Azure deberán tener instalado el componente del Servicio de movilidad. Este servicio captura las escrituras de datos en la máquina y las reenvía al servidor de procesos. Este componente se puede instalar manualmente, o bien puede insertarlo e instalarlo automáticamente el servidor de procesos cuando se habilita la replicación en una máquina. |
| **Conmutación por recuperación** |No se admite la conmutación por recuperación de físico a físico. Esto significa que si realiza la conmutación por error de los servidores físicos a Azure y luego desea realizar la conmutación por recuperación, debe realizarla a una VM de VMware. No se puede realizar la conmutación por recuperación a un servidor físico. Necesitará una VM de Azure a la que realizar la conmutación por recuperación y si no ha implementado el servidor de configuración como una VM de VMware, será preciso que configure un servidor de destino maestro independiente como una VM de VMware. Esto es necesario porque el servidor de destino maestro interactúa y se adjunta al almacenamiento de VMware para restaurar los discos a una máquina virtual de VMware. |

* [Más información](site-recovery-vmware-to-azure.md#azure-prerequisites) acerca de los requisitos para la implementación del Portal de Azure.
* [Más información](site-recovery-failback-azure-to-vmware.md) acerca de la conmutación por recuperación en el Portal de Azure.

## <a name="replicate-hyper-v-vms-not-managed-by-vmm-to-azure"></a>Replicación en Azure de VM de Hyper-V no administradas por VMM
![Sitio de Hyper-V a Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

| **Componente** | **Detalles** |
| --- | --- |
| **Las tablas de Azure** |**Cuenta**: necesitará una cuenta de Azure.<br/><br/> **Almacenamiento**: necesitará una cuenta de almacenamiento de Azure para almacenar los datos replicados. Puede usar una cuenta clásica o una cuenta de almacenamiento de Resource Manager. La cuenta debe ser de redundancia geográfica. Los datos replicados se almacenan en el almacenamiento de Azure y las máquinas virtuales de Azure se ponen en marcha cuando se produce la conmutación por error.<br/><br/> **Red**: necesitará una red virtual de Azure a la que se conectarán las VM de Azure cuando se creen en la conmutación por error. |
| **Hosts de Hyper-V y VM** |Uno o varios hosts de Hyper-V que ejecutan una o varias VM.<br/><br/> El proveedor de Site Recovery y el agente de Recovery Services se instalan en cada host durante la implementación. |

* [Más información](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) acerca de los requisitos para la implementación del Portal de Azure.
* [Más información](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) acerca de los requisitos para la implementación del portal clásico.

## <a name="replicate-hyper-v-vms-managed-by-vmm-to-azure"></a>Replicación en Azure de VM de Hyper-V administradas por VMM
![VMM en Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

| **Componente** | **Detalles** |
| --- | --- |
| **Las tablas de Azure** |**Cuenta**: necesitará una cuenta de Azure.<br/><br/> **Almacenamiento**: necesitará una cuenta de almacenamiento de Azure para almacenar los datos replicados. Puede usar una cuenta clásica o una cuenta de almacenamiento de Resource Manager. La cuenta debe ser de redundancia geográfica. Los datos replicados se almacenan en el almacenamiento de Azure y las máquinas virtuales de Azure se ponen en marcha cuando se produce la conmutación por error.<br/><br/> **Red**: necesitará una red virtual de Azure a la que se conectarán las VM de Azure cuando se creen en la conmutación por error. |
| **Servidor VMM** |Necesitará uno o más servidores VMM locales, con una o varias nubes privadas. El proveedor de Site Recovery se instala en cada servidor durante la implementación. |
| **Hosts de Hyper-V y VM** |Uno o varios hosts de Hyper-V que ejecutan una o varias VM.<br/><br/> El agente de Recovery Services se instala en cada host durante la implementación. |

* [Más información](site-recovery-vmm-to-azure.md#azure-requirements) acerca de los requisitos para la implementación del Portal de Azure.
* [Más información](site-recovery-vmm-to-azure-classic.md#before-you-start) acerca de los requisitos para la implementación del portal clásico.

## <a name="replicate-vmware-virtual-machines-or-physical-server-to-a-secondary-site"></a>Replicación de máquinas virtuales de VMware o servidores físicos en un sitio secundario
![VMware a VMware](./media/site-recovery-components/vmware-to-vmware.png)

| **Componente** | **Detalles** |
| --- | --- |
| **Las tablas de Azure** |**Cuenta**: este escenario se implementa mediante InMage Scout. Para obtenerlo, necesitará una cuenta de Azure.<br/><br/> Después de crear un almacén de Site Recovery, descargue InMage Scout e instale las actualizaciones más recientes para configurar la implementación. |
| **Sitio principal** |**Servidor de proceso**: configure el componente de servidor de proceso en el sitio principal para administrar el almacenamiento en caché, la compresión y la optimización de los datos. También controla la instalación por inserción del agente unificada en las máquinas que desea proteger.<br/><br/> **VMware ESX/ESXi y vCenter**: necesitará un hipervisor de VMware EXS/ESXi y, opcionalmente, un servidor VMware vCenter.<br/><br/> **VM** |
| **Sitio secundario** |**Servidor de configuración**: el servidor de configuración es el primer componente que se instala, y se instala en el sitio secundario para administrar, configurar y supervisar la implementación, ya sea mediante el sitio web de administración o la consola de vContinuum. Solo hay un servidor de configuración en una implementación y debe instalarse en una máquina que se ejecute en Windows Server 2012 R2.<br/><br/> **Servidor de vContinuum (sitio secundario)**: se instala en la misma ubicación que el servidor de configuración. Proporciona una consola para administrar y supervisar su entorno protegido. En una instalación predeterminada, el servidor de vContinuum es el primer servidor de destino maestro y tiene instalado Unified Agent.<br/><br/> **Servidor de destino maestro**: el servidor de destino maestro contiene los datos replicados. Recibe los datos del servidor de proceso, crea una máquina de réplica en el sitio secundario y contiene los puntos de retención de datos. El número de servidores de destino maestros que necesita depende del número de equipos que va a proteger. Si desea realizar una conmutación por recuperación al sitio principal, necesitará un servidor de destino maestro allí también. |

Para replicar VM de VMware o servidores físicos en un sitio secundario, descargue InMage Scout, que se incluye en la suscripción de Azure Site Recovery. Puede descargarse desde Azure Portal o desde el Portal de Azure clásico.

Configure los servidores de componentes de cada sitio (configuración, proceso y destino principal) e instale Unified Agent en las máquinas que desee replicar. Después de la replicación inicial, el agente de cada máquina envía los cambios de replicación diferencial al servidor de procesos. Por su parte, el servidor de procesos optimiza los datos y los transfiere al servidor de destino maestro del sitio secundario. El servidor de configuración administra el proceso de replicación.

## <a name="replicate-hyper-v-vms-managed-by-vmm-to-a-secondary-site"></a>Replicación en un sitio secundario de VM de Hyper-V administradas por VMM
![De local a local](./media/site-recovery-components/arch-onprem-onprem.png)

| **Componente** | **Detalles** |
| --- | --- |
| **Las tablas de Azure** |**Cuenta**: necesitará una cuenta de Azure. |
| **Servidor VMM** |Se recomienda un servidor VMM en el sitio principal y otro en el sitio secundario. Cada servidor necesita una o más nubes privadas.<br/><br/> Durante la implementación, se instala al proveedor de Azure Site Recovery en el servidor VMM. |
| **Hosts de Hyper-V y VM** |Uno o más hosts de Hyper-V que se ejecutan en las nubes de VMM en el sitio principal y el sitio secundario<br/><br/> Cada host debe tener una o más VM que replicar.<br/><br/>. El agente de Recovery Services se instala en cada host durante la implementación. |

* [Más información](site-recovery-vmm-to-vmm.md#azure-prerequisites) acerca de los requisitos de implementación en el Portal de Azure.
* [Más información](site-recovery-vmm-to-vmm-classic.md#before-you-start) acerca de los requisitos para la implementación del Portal de Azure clásico.

## <a name="replicate-hyper-v-vms-managed-by-vmm-to-a-secondary-site-using-san-replication"></a>Replicación en un sitio secundario de VM de Hyper-V administradas por VMM mediante la replicación de SAN
![Replicación de SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

Puede replicar VM de Hyper-V que se administran en nubes de VMM en un sitio secundario que use la replicación de SAN mediante el portal clásico. Este escenario no se admite actualmente en Azure Portal.

| **Componente** | **Detalles** |
| --- | --- |
| **Las tablas de Azure** |**Cuenta**: necesitará una cuenta de Azure. |
| **Servidor VMM** |Se recomienda un servidor VMM en el sitio principal y otro en el sitio secundario. Cada servidor necesita una o más nubes privadas.<br/><br/> Durante la implementación, se instala al proveedor de Azure Site Recovery en el servidor VMM. |
| **SAN** |Una matriz de SAN admitida administrada por el servidor VMM principal.<br/><br/> La SAN debe compartir infraestructura de red con otra matriz de SAN en el sitio secundario. |
| **Hosts de Hyper-V y VM** |Uno o más hosts de Hyper-V que se ejecutan en las nubes de VMM en el sitio principal y el sitio secundario<br/><br/> Cada host debe tener una o más VM que replicar.<br/><br/>. El agente de Recovery Services se instala en cada host durante la implementación. |

En este escenario, durante la implementación de Site Recovery, será preciso que el proveedor de Azure Site Recovery se instale en los servidores VMM. El proveedor coordina y organiza la replicación con el servicio Site Recovery a través de Internet. Los datos se replican entre las matrices de almacenamiento principal y secundaria mediante replicación SAN sincrónica.

[Más información](site-recovery-vmm-san.md#before-you-start) acerca de los requisitos para la implementación.

## <a name="hyper-v-protection-lifecycle"></a>Ciclo de vida de protección de Hyper-V
Este flujo de trabajo muestra el proceso de protección, replicación y conmutación por error de máquinas virtuales Hyper-V.

1. **Habilitar la protección**: se configura el almacén de Site Recovery, se configuran las opciones de replicación para una nube de VMM o un sitio de Hyper-V y se habilita la protección de las máquinas virtuales. Se inicia un trabajo llamado **Habilitar la protección** y se puede supervisar en la pestaña **Trabajos**. El trabajo comprueba que el equipo cumple los requisitos previos y después se invoca el método [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) donde se configura la replicación en Azure con la configuración establecida. El trabajo **Habilitar la protección** también invoca el método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) para inicializar una replicación completa de la máquina virtual.
2. **Replicación inicial**: se toma una instantánea de la máquina virtual y se replican los discos duros virtuales uno por uno hasta que todos se copian en Azure o en el centro de datos secundario. El tiempo necesario para completar esta tarea depende del tamaño de la máquina virtual, del ancho de banda de la red y del método de replicación inicial. Si se producen cambios en el disco mientras la replicación inicial está en curso, el seguimiento de replicaciones de Réplica de Hyper-V realiza un seguimiento de esos cambios en registros de replicación de Hyper-V (.hrl) que se encuentran en la misma carpeta que los discos. Cada disco tiene un archivo .hrl asociado que se enviará al almacenamiento secundario. Tenga en cuenta que los archivos de instantáneas y de registro consumen recursos de disco mientras la replicación inicial está en curso. Cuando la replicación inicial finaliza, se elimina la instantánea de la VM y se sincronizan y se combinan los cambios diferenciales del disco en el registro.
3. **Finalizar la protección**: una vez que finaliza la replicación inicial, el trabajo **Finalizar la protección** configura la red y otras opciones posteriores a la replicación, con el fin de que la máquina virtual esté protegida. Si va a replicar en Azure, debe ajustar la configuración de la máquina virtual para que esté preparada para conmutación por error. En este momento puede ejecutar una conmutación por error de prueba para comprobar que todo funciona según lo esperado.
4. **Replicación**: después de la replicación inicial comienza la sincronización diferencial según la configuración de replicación.
   * **Error de replicación**: si se produce un error en la replicación diferencial y una replicación completa sería costosa, en términos de ancho de banda o de tiempo, se produce una resincronización. Por ejemplo, si los archivos .hrl alcanzan el 50% del tamaño del disco, la máquina virtual se marcará para su resincronización. La resincronización minimiza la cantidad de datos que se envían; para ello, calcula las sumas de comprobación de las máquinas virtuales de origen y destino y envía solo los datos diferenciales. Una vez finalizada la resincronización, se reanudará la replicación diferencial. De forma predeterminada, la resincronización está programada para ejecutarse automáticamente fuera del horario de oficina, pero puede resincronizar una máquina virtual manualmente.
   * **Error de replicación**: si se produce un error de replicación, se realiza un reintento de forma predefinida. Si el error es irrecuperable, como un error de autenticación o de autorización, o una máquina de réplica se encuentra en un estado no válido, no se realizará ningún reintento. Si se produce un error recuperable, como un error de red o poco espacio en disco o memoria, se realizarán reintentos, con intervalos cada vez mayores entre cada reintento (1, 2, 4, 8, 10 y después cada 30 minutos).
5. **Conmutaciones por error planeadas y no planeadas**: las conmutaciones por error planeadas o no planeadas se pueden ejecutar cuando sea necesario. Si ejecuta una conmutación por error planeada, las máquinas virtuales de origen se apagan para que no haya pérdida de datos. Después de que se crean máquinas virtuales de réplica, se colocan en estado pendiente de confirmación. Para completar la conmutación por error será preciso que las confirmen, a menos que vaya a realizar una réplica con SAN, en cuyo caso la confirmación es automática. Una vez configurado y en marcha el sitio primario, puede producirse la conmutación por recuperación. Si replicó en Azure, la replicación inversa es automática. De lo contrario, inicie una replicación inversa de forma manual.

![flujo de trabajo](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Pasos siguientes
[Preparación de la implementación](site-recovery-best-practices.md)

<!--HONumber=Oct16_HO2-->


