---
title: VMware para la replicación de la arquitectura en Azure con Azure Site Recovery | Microsoft Docs
description: Este artículo proporciona información general sobre los componentes y la arquitectura que se utilizan para replicar máquinas virtuales locales de VMware en Azure con Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/19/2018
ms.author: raynew
ms.openlocfilehash: c1aa89f14edab7d0e560c20d6bc48480aff1631f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
ms.locfileid: "30184588"
---
# <a name="vmware-to-azure-replication-architecture"></a>Arquitectura de replicación de VMware a Azure

En este artículo, se describe la arquitectura y los procesos que se usan al replicar, conmutar por error y recuperar máquinas virtuales de VMware entre un sitio de VMware local y Azure mediante [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Componentes de la arquitectura

En la siguiente tabla y gráfico se proporciona una visión general de los componentes que se usaron para la replicación de VMware en Azure.

**Componente** | **Requisito** | **Detalles**
--- | --- | ---
**Las tablas de Azure** | Una suscripción a Azure, una cuenta de Azure Storage y una red de Azure. | Los datos replicados desde las máquinas virtuales locales se almacenan en la cuenta de almacenamiento. Las máquinas virtuales de Azure se crean con los datos replicados cuando se ejecuta una conmutación por error desde el entorno local en Azure. Las máquinas virtuales de Azure se conectan a la red virtual de Azure cuando se crean.
**Equipo del servidor de configuración** | Una sola máquina local. Se recomienda ejecutarla como una máquina virtual de VMware que pueda implementarse desde una plantilla de OVF descargada.<br/><br/> La máquina ejecuta todos los componentes locales de Site Recovery, incluido el servidor de configuración, el servidor de procesos y el servidor de destino maestro. | **Servidor de configuración**: coordina la comunicación entre el entorno local y Azure, además de administrar la replicación de datos.<br/><br/> **Servidor de procesos**: se instala de forma predeterminada en el servidor de configuración. Recibe los datos de la replicación; los optimiza mediante almacenamiento en caché, compresión y cifrado, y los envía a Azure Storage. El servidor de procesos también instala Azure Site Recovery Mobility Service en las máquinas virtuales que se van a replicar y realiza la detección automática de las máquinas locales. A medida que crece la implementación, puede agregar más servidores de procesos independientes para controlar mayores volúmenes de tráfico de replicación.<br/><br/> **Servidor de destino maestro**: instalado en el servidor de configuración de forma predeterminada. Controla los datos de replicación durante la conmutación por recuperación desde Azure. En el caso de las implementaciones de gran tamaño, puede agregar un servidor de destino maestro independiente adicional para la conmutación por recuperación.
**Servidores de VMware** | Las máquinas virtuales VMware se hospedan en servidores ESXi de vSphere locales. Se recomienda un servidor vCenter para administrar los hosts. | Durante la implementación de Site Recovery, se agregan servidores VMware al almacén de Recovery Services.
**Máquinas replicadas** | Mobility Service está instalado en cada una de las máquinas virtuales de VMware que se van a replicar. | Se recomienda permitir la instalación automática desde el servidor de procesos. Si lo desea, también puede instalar manualmente el servicio o usar un método de implementación automatizada, como System Center Configuration Manager.

**Arquitectura de VMware a Azure**

![Componentes](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="configuration-steps"></a>Pasos de configuración

Los pasos generales para configurar VMware para la migración o recuperación ante desastres de Azure son los siguientes:

1. **Configuración de los componentes de Azure**. Necesita una cuenta de Azure con los permisos adecuados, una cuenta de almacenamiento de Azure, una red virtual de Azure y un almacén de Recovery Services. [Más información](tutorial-prepare-azure.md).
2. **Configuración del entorno local**. Entre ellas se incluye la configuración de una cuenta en el servidor de VMware para que Site Recovery pueda detectar automáticamente las máquinas virtuales que desea replicar, la configuración de una cuenta que se pueda utilizar para instalar el componente Mobility Service en las máquinas virtuales que desea replicar y la comprobación de que los servidores de VMware y las máquinas virtuales cumplen con los requisitos previos. Opcionalmente, también puede prepararse para conectarse a estas máquinas virtuales de Azure después de la conmutación por error. Site Recovery replica los datos de las máquinas virtuales a una cuenta de almacenamiento de Azure y crea máquinas virtuales de Azure con los datos cuando se ejecute una conmutación por error a Azure. [Más información](vmware-azure-tutorial-prepare-on-premises.md).
3. **Configuración de la replicación**. Elija dónde quiere replicar. Configure el entorno de replicación de origen mediante la configuración de una única máquina virtual de VMware local (el servidor de configuración) que ejecute todos los componentes de Site Recovery locales que necesite. Después de la configuración, registre la máquina del servidor de configuración en el almacén de Recovery Services. Después, seleccione la configuración de destino. [Más información](vmware-azure-tutorial.md).
4. **Creación de una directiva de replicación**. Va a crear una directiva de replicación que especifica cómo debe producirse la replicación. 
    - **Umbral de RPO**: esta configuración de supervisión indica que si la replicación no se produce en el tiempo especificado, se emite una alerta (y opcionalmente un correo electrónico). Por ejemplo, si establece el umbral RPO en 30 minutos y un problema impide la replicación durante 30 minutos, se genera un evento. Esta configuración no afecta a la replicación. La replicación es continua y los puntos de recuperación se crean cada pocos minutos.
    - **Retención**: la retención del punto de recuperación especifica cuánto tiempo se deben mantener los puntos de recuperación en Azure. Puede especificar un valor entre 0 y 24 horas para Premium Storage, o hasta 72 horas para Standard Storage. Puede realizar la conmutación por error al último punto de recuperación o a un punto almacenado si establece el valor por encima de cero. Después de la ventana de retención, los puntos de recuperación se purgan.
    - **Instantáneas coherentes con los bloqueos**: de forma predeterminada, Site Recovery toma las instantáneas coherentes con los bloqueos y crea puntos de recuperación con ellos cada pocos minutos. Un punto de recuperación es frente a bloqueos si todos los componentes de datos interrelacionados son coherentes de orden de escritura, tal como estaban en el momento en que se creó el punto de recuperación. Para comprenderlo todo mejor, imagínese el estado de los datos en el disco duro del equipo después de un corte de energía o evento similar. Un punto de recuperación coherente con los bloqueos por lo general es suficiente si la aplicación está diseñada para recuperarse de un bloqueo sin incoherencia en los datos.
    - **Instantáneas coherentes con la aplicación**: si este valor no es cero, la instancia de Mobility Service que se ejecuta en la máquina virtual intenta generar puntos de recuperación e instantáneas coherentes con el sistema de archivos. Una vez completada la replicación inicial, se realiza la primera instantánea. Después, las instantáneas se realizan a la frecuencia que especifique. Un punto de recuperación es coherente con la aplicación si, además de ser coherente con el orden de escritura, las aplicaciones que se ejecutan completan todas sus operaciones y vacían sus búferes en el disco (modo de inactividad de aplicación). Se recomiendan puntos de recuperación coherentes la aplicación para aplicaciones de bases de datos como SQL, Oracle y Exchange. Si una instantánea coherente con los bloqueos es suficiente, este valor se puede establecer en 0.  
    - **Coherencia con varias máquinas virtuales**: también puede crear un grupo de replicación. Después, cuando se habilita la replicación, puede recopilar las máquinas virtuales de ese grupo. Todas las máquinas virtuales de un grupo de replicación se replican al mismo tiempo y comparten puntos de recuperación coherentes con los bloqueos y coherentes con la aplicación cuando han conmutado por error. Debe utilizar esta opción con cuidado, ya que puede afectar al rendimiento de la carga de trabajo, ya que es necesario recopilar instantáneas en varios equipos. Haga esto únicamente si las máquinas virtuales ejecutan la misma carga de trabajo y necesitan ser coherentes, y las máquinas virtuales tienen renovaciones similares. Puede agregar hasta ocho máquinas virtuales a un grupo. 
5. **Habilitación de la replicación de máquinas virtuales**. Por último, va a habilitar la replicación para las máquinas virtuales de VMware locales. Si ha creado una cuenta para instalar Mobility Service y especifica que la recuperación del sitio debe hacer una instalación de inserción, se instalará en cada máquina virtual para que la replicación se habilita el servicio de movilidad. [Más información](vmware-azure-tutorial.md#enable-replication). Si ha creado un grupo de replicación para la coherencia con varias máquinas virtuales, puede agregar las máquinas virtuales a ese grupo.
6. **Conmutación por error de prueba**. Después de que todo esté configurado, puede hacer una conmutación por error de prueba para comprobar que las máquinas virtuales conmutan por error a Azure como se esperaba. [Más información](tutorial-dr-drill-azure.md).
7. **Conmutación por error**. Si simplemente está migrando las máquinas virtuales a Azure: ejecute una conmutación por error completa para hacerlo. Si está configurando la recuperación ante desastres, puede ejecutar una conmutación por error completa cuando sea necesario. Para una recuperación ante desastres completa, después de la conmutación por error a Azure, puede volver a realizar una conmutación por recuperación en su sitio local cuando esté disponible. [Más información](vmware-azure-tutorial-failover-failback.md).

## <a name="replication-process"></a>Proceso de replicación

1. Cuando habilita la replicación para una máquina virtual, esta comienza a replicarse de acuerdo con la directiva de replicación. 
2. El tráfico se replica en los puntos de conexión públicos de Azure Storage a través de Internet. Como alternativa, puede usar Azure ExpressRoute con [emparejamiento público](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). No se admite la replicación del tráfico entre un sitio local y Azure a través de una red privada virtual (VPN) de sitio a sitio.
3. Una copia inicial de los datos de la máquina virtual se replica en el almacenamiento de Azure.
4. Una vez terminada la replicación inicial, comienza la de los cambios incrementales en Azure. Las marcas de revisión de una máquina se conservan en un archivo .hrl.
5. Comunicación se realiza como se indica a continuación:

    - Las máquinas virtuales se comunican con el servidor de configuración local en el puerto HTTPS 443 entrante para la administración de la replicación.
    - El servidor de configuración organiza la replicación con Azure a través del puerto HTTPS 443 saliente.
    - Las máquinas virtuales envían datos de replicación al servidor de procesos (que se ejecuta en la máquina del servidor de configuración) en el puerto HTTPS 9443 entrante. Este puerto se puede modificar.
    - El servidor de procesos recibe datos de replicación, los optimiza y los cifra para enviarlos después a Azure Storage a través del puerto 443 de salida.


**Proceso de replicación de VMware a Azure**

![Proceso de replicación](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proceso de conmutación por error y conmutación por recuperación

Una vez que la replicación está configurada y tras ejecutar una exploración de la recuperación ante desastres (conmutación por error de prueba) para comprobar que todo funciona según lo previsto, puede ejecutar la conmutación por error y la conmutación por recuperación, según sea necesario.

1. Va a conmutar por error una única máquina o crear planes de recuperación para conmutar por error varias máquinas virtuales. La ventaja de un plan de recuperación en lugar de la conmutación por error de una única máquina incluye:
    - Puede modelar las dependencias de aplicaciones con la inclusión de todas las máquinas virtuales por la aplicación en un único plan de recuperación.
    - Puede agregar scripts, runbooks de Azure y ponerlos en pausa para acciones manuales.
2. Después de desencadenar la conmutación por error inicial, debe confirmarla para que se inicie. Para ello, acceda a la carga de trabajo desde la máquina virtual de Azure.
3. Cuando el sitio local principal esté disponible de nuevo, podrá prepararse para la conmutación por recuperación. Para realizar la conmutación por recuperación, debe configurar una infraestructura de conmutación por recuperación, que incluya:

    * **Servidor de procesos temporal de Azure**: para realizar una conmutación por recuperación desde Azure, debe configurar una máquina virtual de Azure para que actúe como servidor de procesos y controle la replicación desde Azure. Dicha máquina virtual se puede eliminar cuando finalice la conmutación por recuperación.
    * **Conexión VPN**: para realizar una conmutación por recuperación, necesita una conexión VPN (o ExpressRoute) entre la red de Azure y el sitio local.
    * **Servidor de destino maestro independiente**: de manera predeterminada, el servidor de destino maestro que se instaló con el servidor de configuración en la máquina virtual local de VMware controla la conmutación por recuperación. Si necesita realizar la conmutación por recuperación en grandes volúmenes de tráfico, debe configurar un servidor de destino maestro local diferente para este propósito.
    * **Directiva de conmutación por recuperación**: para replicar de nuevo en el sitio local, necesita una directiva de conmutación por recuperación. Esta directiva se creó automáticamente junto con la directiva de replicación entre el entorno local y Azure.
4. Una vez instalados los componentes, la conmutación por recuperación se produce en tres acciones:

    - Fase 1: Vuelva a proteger las máquinas virtuales de modo que realicen la replicación desde Azure de vuelta a las máquinas virtuales VMware locales.
    -  Fase 2: Ejecute una conmutación por error en el sitio local.
    - Fase 3: Una vez que las cargas de trabajo han conmutado por recuperación, debe habilitar de nuevo la replicación de las máquinas virtuales locales.
    
 
**Conmutación por recuperación VMware desde Azure**

![Conmutación por recuperación](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Pasos siguientes

Siga [este tutorial](vmware-azure-tutorial.md) para habilitar la replicación de VMware en Azure.
