---
title: "Replicación de máquinas virtuales de VMware y servidores físicos en Azure en el portal clásico | Microsoft Docs"
description: "En este artículo se describe cómo implementar Azure Site Recovery para organizar la replicación, la conmutación por error y la recuperación de máquinas virtuales de VMware locales y servidores físicos de Windows o Linux en Azure."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: a9022c1f-43c1-4d38-841f-52540025fb46
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 37291b16a966c17b684a4622f15d0393a5a5ce6d
ms.openlocfilehash: 0984ccc8163c94f56517e65919f8ea089a3e8b5f
ms.lasthandoff: 02/27/2017


---
# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery"></a>Replicación de máquinas virtuales de VMware y servidores físicos en Azure con Azure Site Recovery
> [!div class="op_single_selector"]
> * [Portal de Azure](site-recovery-vmware-to-azure.md)
> * [Portal clásico](site-recovery-vmware-to-azure-classic.md)
> * [Portal clásico (heredado)](site-recovery-vmware-to-azure-classic-legacy.md)
>
>

El servicio Azure Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Las máquinas se pueden replicar a Azure o a un centro de datos secundario local. Para obtener una introducción rápida, lea [¿Qué es Azure Site Recovery?](site-recovery-overview.md).

## <a name="overview"></a>Información general
En este artículo se describe cómo:

* **Replicar máquinas virtuales de VMware en Azure**: implemente Site Recovery para coordinar la replicación, la conmutación por error y la recuperación de máquinas virtuales de VMware locales en Azure Storage.
* **Replicar servidores físicos en Azure**: implemente Azure Site Recovery para coordinar la replicación, la conmutación por error y la recuperación de los servidores físicos locales de Windows y Linux en Azure.

> [!NOTE]
> En este artículo, se describe cómo realizar la replicación en Azure. Si desea replicar máquinas virtuales de VMware o servidores físicos de Windows o Linux en un centro de datos secundario, consulte cómo [replicar VMware en VMware con Site Recovery](site-recovery-vmware-to-vmware.md).
>
>

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="enhanced-deployment"></a>Implementación mejorada
En este artículo, se incluyen instrucciones para realizar una implementación mejorada en el Portal de Azure clásico. Se recomienda que utilice esta versión para todas las implementaciones nuevas. Si ya realizó la implementación con la versión heredada anterior, se recomienda migrar a la versión nueva. Para más información acerca de la migración, consulte cómo [replicar VMware en Azure con Site Recovery usando el portal clásico heredado](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment).

La implementación mejorada es una actualización importante. A continuación, presentamos un resumen de las mejoras realizadas:

* **Sin máquinas virtuales de infraestructura de Azure**: los datos se replican directamente en una cuenta de almacenamiento de Azure. Además, no es necesario configurar ninguna máquina virtual de infraestructura (como un servidor de configuración o servidor de destino principal) para la replicación y la conmutación por error, tal y como se necesitaba en la implementación heredada.  
* **Instalación unificada**: una instalación única proporciona configuración y escalabilidad únicas para los componentes locales.
* **Implementación segura**: se cifra todo el tráfico y las comunicaciones de administración se envían a través de HTTPS 443.
* **Puntos de recuperación**: compatibilidad con puntos de recuperación coherentes tras bloqueo y coherentes con la aplicación para entornos de Windows y Linux, además de compatibilidad con configuraciones coherentes con una sola máquina virtual y con varias máquinas virtuales.
* **Conmutación por error de prueba**: compatibilidad con conmutación por error de prueba que no provoca interrupciones en Azure, sin afectar la producción ni pausar la replicación.
* **Conmutación por error no planeada**: compatibilidad con conmutación por error no planeada en Azure con una opción mejorada para apagar automáticamente las máquinas virtuales antes de la conmutación por error.
* **Conmutación por recuperación**: conmutación por recuperación integrada que solo replica los cambios diferenciales de vuelta en el sitio local.
* **vSphere 6.0**: compatibilidad limitada para las implementaciones de VMware vSphere 6.0.

## <a name="how-does-site-recovery-help-protect-virtual-machines-and-physical-servers"></a>¿De qué manera Site Recovery ayuda a proteger máquinas virtuales y servidores virtuales?
* Los administradores de VMware pueden configurar medidas de seguridad externas para proteger Azure de las aplicaciones y las cargas de trabajo de negocios que se ejecutan en máquinas virtuales de VMware. Los administradores de servidores pueden replicar servidores locales de Windows y Linux en Azure.
* La consola de Azure Site Recovery proporciona una ubicación única para configurar y administrar de manera simple los procesos de replicación, conmutación por error y recuperación.
* Si replica máquinas virtuales de VMware administradas por un servidor de vCenter, Site Recovery puede detectar automáticamente esas máquinas virtuales. Si las máquinas se encuentran en un host ESXi, Site Recovery detecta las máquinas virtuales existentes en el host.
* Si ejecuta fácilmente conmutaciones por error desde su infraestructura local a Azure, puede realizar conmutaciones por recuperación (restauración) desde Azure a servidores de máquina virtual de VMware en el sitio local.
* Puede configurar planes de recuperación que agrupen las cargas de trabajo de aplicaciones organizadas en niveles en distintas máquinas. Si realiza la conmutación por recuperación de esos planes, Site Recovery proporcionará coherencia para varias máquinas virtuales, con la finalidad de recuperar las máquinas virtuales que ejecutan las mismas cargas de trabajo a un punto de datos coherente.

## <a name="supported-operating-systems"></a>Sistemas operativos compatibles
### <a name="windows-64-bit-only"></a>Windows (solo&64; bits)
* Windows Server 2008 R2 SP1 y versiones posteriores
* Windows Server 2012
* Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux (solo&64; bits)
* Red Hat Enterprise Linux 6.7, 7.1 y 7.2
* CentOS 6.5, 6.6, 6.7, 7.0, 7.1 y 7.2
* Oracle Enterprise Linux 6.4 y 6.5, en ejecución en el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3)
* SUSE Linux Enterprise Server 11 SP3

## <a name="scenario-architecture"></a>Arquitectura del escenario
Componentes del escenario:

* **Un servidor de administración local**: el servidor de administración ejecuta los componentes de Site Recovery.
  * **Servidor de configuración**: coordina la comunicación y administra los procesos de replicación y recuperación de datos.
  * **Servidor de procesos**: actúa como una puerta de enlace de replicación. Recibe datos provenientes de máquinas de origen, los optimiza con almacenamiento en caché, compresión y cifrado y envía los datos de replicación a Azure Storage. También controla la instalación de inserción del servicio de Movilidad en máquinas protegidas y realiza al detección automática de máquinas virtuales de VMware.
  * **Servidor de destino maestro**: controla los datos de replicación durante la conmutación por recuperación desde Azure.
    También puede implementar un servidor de administración que actúa solo como servidor de procesos para escalar la implementación.
* **Mobility Service**: este componente está implementado en cada máquina (servidor físico o máquina virtual de VMware) que desea replicar en Azure. Captura las escrituras de datos en la máquina y los reenvía al servidor de procesos.
* **Azure**: no es necesario crear ninguna máquina virtual de Azure para controlar la replicación y la conmutación por error. El servicio Site Recovery controla la administración de datos y los datos se replican directamente al almacenamiento de Azure. Las máquinas virtuales replicadas de Azure se ponen en marcha automáticamente solo cuando se produce la conmutación por error en Azure. Sin embargo, si desea realizar la conmutación por recuperación desde Azure al sitio local, deberá configurar una máquina virtual de Azure para que actúe como un servidor de procesos.

El siguiente gráfico (creado por Henry Robalino) muestra cómo interactúan estos componentes:

![Arquitectura de componentes](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

## <a name="capacity-planning"></a>Planificación de capacidad
Cuando planea la capacidad, debe considerar lo siguiente:

* **El entorno de origen**: los requisitos de la máquina de origen y la infraestructura de VMware o la planeación de capacidad.
* **El servidor de administración**: planeación de los servidores de administración locales que ejecutan los componentes de Site Recovery.
* **Ancho de banda de red desde el origen hasta el destino**: planeación del ancho de banda de red requerido para la replicación entre el origen y Azure.

### <a name="source-environment-considerations"></a>Consideraciones sobre el entorno de origen
* **Frecuencia diaria de cambios máxima**: un equipo protegido solo puede utilizar un único servidor de procesos. Un único servidor de procesos puede gestionar hasta 2 TB de cambios de datos diariamente. Por lo tanto, la frecuencia diaria de cambio de datos máxima que admite una máquina protegida es de 2 TB.
* **Rendimiento máximo**: una máquina replicada puede pertenecer a una cuenta de almacenamiento en Azure. Una cuenta de almacenamiento estándar puede controlar un máximo de 20 000 solicitudes por segundo y se recomienda mantener la cantidad de E/S por segundo en una máquina de origen en 20 000. Por ejemplo, si tiene una máquina de origen con 5 discos y cada disco genera 120 E/S por segundo (8 KB de tamaño) en el origen, se encontrará dentro del límite de 500 de E/S por segundo por disco de Azure. El número de cuentas de almacenamiento necesarias = E/S por segundo de origen total/20&000;.

### <a name="management-server-considerations"></a>Consideraciones sobre el servidor de administración
El servidor de administración ejecuta los componentes de Site Recovery que controlan la optimización, la replicación y la administración de datos. Debe poder controlar la capacidad de frecuencia diaria de cambios en todas las cargas de trabajo que se ejecutan en máquinas protegidas y cuenta con el ancho de banda suficiente para realizar una replicación continua de los datos en Azure Storage. Concretamente:

* El servidor de procesos recibe datos de replicación provenientes de las máquinas protegidas y los optimiza con almacenamiento en caché, compresión y cifrado antes de enviarlos a Azure. El servidor de administración debe tener los recursos suficientes para realizar estas tareas.
* El servidor de procesos utiliza la memoria caché basada en disco. Se recomienda tener un disco de caché independiente con 600 GB o más de capacidad para controlar los cambios en los datos almacenados ante la eventualidad de una interrupción o un cuello de botella en la red. Durante la implementación, puede configurar la caché en cualquier unidad que tenga, al menos, 5 GB de almacenamiento disponible, aunque se recomiendan 600 GB como mínimo.
* Como procedimiento recomendado, el servidor de administración debe encontrarse en el mismo segmento de LAN y red que las máquinas que desea proteger. No obstante, puede estar en una red distinta, pero las máquinas que desea proteger deben contar con la visibilidad de red L3 en ella.

La tabla siguiente resume las recomendaciones de tamaño para el servidor de administración:

| **CPU del servidor de administración** | **Memoria** | **Tamaño del disco de caché** | **Frecuencia de cambio de datos** | **Máquinas protegidas** |
| --- | --- | --- | --- | --- |
| 8 vCPU (2 sockets * 4 núcleos a 2,5 GHz) |16 GB |<&300; GB |500 GB o menos |Implemente un servidor de administración con esta configuración para replicar menos de 100 máquinas. |
| 12 vCPU (2 sockets * 6 núcleos a 2,5 GHz) |18 GB |600 GB |500 GB a 1 TB |Implemente un servidor de administración con esta configuración para replicar entre 100 y 150 máquinas. |
| 16 vCPU (2 sockets * 8 núcleos a 2,5 GHz) |32 GB |1 TB |1 TB a 2 TB |Implemente un servidor de administración con esta configuración para replicar entre 150 y 200 máquinas. |
| Implementar otro servidor de procesos | | |2 TB |Implemente servidores de procesos adicionales si replica más de 200 máquinas o si la tasa de cambios de datos diaria supera los 2 TB. |

Donde:

* Cada máquina de origen está configurada con 3 discos de 100 GB cada una.
* Usamos almacenamiento de pruebas comparativas de 8 unidades SAS de 10 000 RPM con RAID 10 para las mediciones de disco de caché.

### <a name="network-bandwidth-from-source-to-target"></a>Ancho de banda de red desde el origen hasta el destino
Asegúrese de calcular el ancho de banda necesario para la replicación inicial y la replicación diferencial con la [herramienta de planeamiento de capacidad](site-recovery-capacity-planner.md).

#### <a name="throttling-bandwidth-used-for-replication"></a>Limitación del ancho de banda usado para la replicación
El tráfico de VMware replicado en Azure pasa por un servidor de procesos específico. Puede limitar el ancho de banda disponible para la replicación de Site Recovery en ese servidor de la siguiente manera:

1. Abra el complemento MMC de Copia de seguridad de Microsoft Azure en el servidor de administración principal o en un servidor de administración que ejecuta servidores de procesos aprovisionados adicionales. De forma predeterminada, se crea un acceso directo para Microsoft Azure Backup en el escritorio. También se encuentra en C:\Archivos de programa\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. En el complemento, haga clic en **Cambiar propiedades**.

    ![Cambio de propiedades para limitar ancho de banda](./media/site-recovery-vmware-to-azure-classic/throttle1.png)
3. En la pestaña **Limitación** , especifique el ancho de banda que se puede usar para la replicación de Site Recovery y la programación aplicable.

    ![Limitación de ancho de banda para replicación](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

También puede definir la limitación con PowerShell. Este es un ejemplo:

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

#### <a name="maximizing-bandwidth-usage"></a>Maximización del uso del ancho de banda
Para aumentar el ancho de banda que Azure Site Recovery utiliza para realizar la replicación, debe cambiar una clave de registro.

La clave siguiente controla la cantidad de subprocesos por disco de replicación que se utilizan cuando se realiza la replicación:

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 En una red sobreaprovisionada, se deben cambiar los valores predeterminados de esta clave de registro. Se admite un máximo de 32.  

Para más información acerca de la planeación de capacidad detallada, consulte [Site Recovery Capacity Planner](site-recovery-capacity-planner.md).

### <a name="additional-process-servers"></a>Servidores de procesos adicionales
Si necesita proteger más de 200 máquinas o si la frecuencia diaria de cambios es mayor que 2 TB, puede agregar servidores adicionales para controlar la carga. Para escalar horizontalmente, puede:

* Aumentar la cantidad de servidores de administración. Por ejemplo, puede proteger hasta 400 máquinas con dos servidores de administración.
* Agregar servidores de procesos adicionales y utilizarlos para controlar el tráfico en lugar (o además) del servidor de administración.

Esta tabla describe un escenario en el cual:

* Configura el servidor de administración original para usarlo solo como servidor de configuración.
* Configura un servidor de procesos adicional.
* Configura máquinas virtuales protegidas para utilizar el servidor de procesos adicional.
* Cada máquina de origen protegida está configurada con tres discos de 100 GB cada uno.

| **Servidor de administración original**<br/><br/>(servidor de configuración) | **Servidores de procesos adicionales** | **Tamaño del disco de caché** | **Frecuencia de cambio de datos** | **Máquinas protegidas** |
| --- | --- | --- | --- | --- |
| 8 vCPU (2 sockets * 4 núcleos a 2,5 GHz), 16 GB de RAM |4 vCPU (2 sockets * 2 núcleos a 2,5 GHz), 8 GB de RAM |<&300; GB |250 GB o menos |Puede replicar 85 máquinas o menos. |
| 8 vCPU (2 sockets * 4 núcleos a 2,5 GHz), 16 GB de RAM |8 vCPU (2 sockets * 4 núcleos a 2,5 GHz), 12 GB de RAM |600 GB |250 GB a 1 TB |Puede replicar entre 85 y 150 máquinas. |
| 12 vCPU (2 sockets * 6 núcleos a 2,5 GHz), 18 GB de RAM |12 vCPU (2 sockets * 6 núcleos a 2,5 GHz), 24 GB de RAM |1 TB |1 TB a 2 TB |Puede replicar entre 150 y 225 máquinas. |

La forma de escalar los servidores depende de si prefiere un modelo de escalado vertical u horizontal. Para escalar verticalmente, implementa algunos servidores de procesos y administración de alto nivel, mientras que, para escalar horizontalmente, implementa más servidores con menos recursos. Por ejemplo, si necesita proteger 220 máquinas, podría elegir una de las siguientes opciones:

* Configure el servidor de administración original con 12 vCPU y 18 GB de RAM. Configure un servidor de procesos adicional con 12 vCPU y 24 GB de RAM. Configure máquinas protegidas para que utilicen solo el servidor de procesos adicional.
* Configure dos servidores de administración (2 x 8 vCPU, 16 GB de RAM) y dos servidores de procesos adicionales (1 x 8 vCPU y 1 x 4 vCPU para controlar 220 máquinas [135 + 85]). Configure máquinas protegidas para que utilicen solo los servidores de procesos adicionales.

Siga las instrucciones de [Implementar servidores de procesos adicionales](#deploy-additional-process-servers) para configurar un servidor de procesos adicional.

## <a name="before-you-start-deployment"></a>Antes de comenzar la implementación
Las tablas siguientes resumen los requisitos previos para implementar este escenario.

### <a name="azure-prerequisites"></a>Requisitos previos de Azure
| **Requisito previo** | **Detalles** |
| --- | --- |
| **Cuenta de Azure** |Necesita una cuenta de [Microsoft Azure](https://azure.microsoft.com/) . Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/). Para más información sobre los precios de Site Recovery, consulte [Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/). |
| **Almacenamiento de Azure** |Necesitará una cuenta de almacenamiento de Azure para almacenar los datos replicados. Los datos replicados se almacenan en Azure Storage y las máquinas virtuales de Azure se ponen en marcha cuando se produce la conmutación por error. <br/><br/>Necesita una [cuenta de almacenamiento con redundancia geográfica de tipo estándar](../storage/storage-redundancy.md#geo-redundant-storage). La cuenta debe encontrarse en la misma región que el servicio Site Recovery y debe estar asociada a la misma suscripción. La replicación en cuentas de Premium Storage no se admite actualmente y no debería utilizarse.<br/><br/>No se admite el traslado de cuentas de almacenamiento creadas mediante [Azure Portal](../storage/storage-create-storage-account.md) entre grupos de recursos. Para más información, consulte [Introducción a Microsoft Azure Storage](../storage/storage-introduction.md).<br/><br/> |
| **Red de Azure** |Necesitará una red virtual de Azure a la que se conectarán las máquinas virtuales de Azure cuando se produzca la conmutación por error. La red virtual de Azure debe estar en la misma región que el almacén de Site Recovery.<br/><br/>Si desea realizar la conmutación por recuperación después de la conmutación por error a Azure, necesitará una conexión VPN (o Azure ExpressRoute) configurada de la red de Azure al sitio local. |

### <a name="on-premises-prerequisites"></a>Requisitos previos locales
| **Requisito previo** | **Detalles** |
| --- | --- |
| **Servidor de administración** |Necesita un servidor local con Windows 2012 R2 que se ejecute en una máquina virtual o en un servidor físico. Todos los componentes de Site Recovery locales están instalados en este servidor de administración.<br/><br/> Se recomienda implementar el servidor como una máquina virtual de VMware de alta disponibilidad. La conmutación por recuperación en el sitio local desde Azure siempre será en máquinas virtuales de VMware, independientemente de si realizó conmutación por error de máquinas virtuales o servidores físicos. Si no configura el servidor de administración como una máquina virtual de VMware, debe configurar un servidor de destino principal independiente como una máquina virtual de VMware para recibir el tráfico de conmutación por recuperación.<br/><br/>El servidor no debe ser un controlador de dominio.<br/><br/>El servidor debe tener una dirección IP estática.<br/><br/>El nombre de host del servidor debe tener 15 caracteres o menos.<br/><br/>La configuración regional dl sistema operativo deben estar inglés únicamente.<br/><br/>El servidor de administración requiere acceso a Internet.<br/><br/>Necesita acceso saliente desde el servidor como se indica a continuación: acceso temporal en HTTP 80 durante la instalación de los componentes de Site Recovery (para descargar MySQL); acceso de salida continuo en HTTPS 443 para la administración de replicación; acceso de salida continuo en HTTPS 9443 para el tráfico de replicación (este puerto se puede modificar).<br/><br/> Asegúrese de poder tener acceso a estas direcciones URL desde el servidor de administración: <br/>- \*.hypervrecoverymanager.windowsazure.com<br/>- \*.accesscontrol.windows.net<br/>- \*.backup.windowsazure.com<br/>- \*.blob.core.windows.net<br/>- \*.store.core.windows.net<br/>- https://www.msftncsi.com/ncsi.txt<br/>- [ https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>Si tiene reglas de firewall basadas en direcciones IP en el servidor, compruebe que las reglas permitan la comunicación con Azure. Deberá permitir los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653) y el puerto HTTPS (443). También debe incluir en la lista blanca los intervalos de direcciones IP correspondientes a la región de Azure de su suscripción y al oeste de EE. UU. La dirección URL [https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi") es para descargar MySQL. |
| **VMware vCenter/host ESXi** |Necesita uno o varios hipervisores ESX/ESXi de VMware vSphere que administren sus máquinas virtuales de VMware que ejecuten la versión 6.0, 5.5 o 5.1 de ESX/ESXi con las actualizaciones más recientes.<br/><br/> Se recomienda implementar un servidor de VMware vCenter para administrar los hosts ESXi. Debe ejecutar la versión 6.0 o 5.5 de vCenter con las últimas actualizaciones.<br/><br/>Tenga en cuenta que Site Recovery no admite las nuevas características de vCenter y vSphere 6.0, como Cross vCenter vMotion, volúmenes virtuales y DRS de almacenamiento. La compatibilidad de Site Recovery está limitada a las características que también estaban disponibles en la versión 5.5. |
| **Máquinas protegidas** |**Las tablas de Azure**<br/><br/>Las máquinas que desee proteger deben cumplir los [requisitos previos de Azure](site-recovery-prereq.md) para la creación de máquinas virtuales de Azure.<br><br/>Si desea conectarse a las máquinas virtuales de Azure después de la conmutación por error, tendrá que habilitar las conexiones de Escritorio remoto en el firewall local.<br/><br/>La capacidad de disco individual en máquinas protegidas no debe ser superior a 1023 GB. Una máquina virtual puede tener hasta 64 discos (es decir, hasta 64 TB). Si tiene discos con una capacidad mayor que 1 TB, considere la posibilidad de usar una replicación de base de datos, como Oracle Data Guard o SQL Server AlwaysOn.<br/><br/>Mínimo de 2 GB de espacio disponible en la unidad de instalación para la instalación de los componentes.<br/><br/>No se admiten clústeres de invitados de disco compartido. Si tiene una implementación en clúster, considere la posibilidad de usar una replicación de base de datos, como Oracle Data Guard o SQL Server AlwaysOn.<br/><br/>No se admite el arranque de Unified Extensible Firmware Interface (UEFI)/Extensible Firmware Interface (EFI).<br/><br/>Los nombres de las máquinas deben tener entre 1 y 63 caracteres (letras, números y guiones). El nombre debe comenzar con una letra o un número y terminar con una letra o un número. Después de proteger una máquina, puede modificar el nombre de Azure.<br/><br/>**Máquinas virtuales de VMware**<br/><br>Debe instalar VMware vSphere PowerCLI 6.0 en el servidor de administración (servidor de configuración).<br/><br/>Las máquinas virtuales de VMware que desea proteger deben tener herramientas de VMware instaladas y en ejecución.<br/><br/>Si la máquina virtual de origen tiene formación de equipos NIC, se convierte en una sola NIC después de la conmutación por error a Azure.<br/><br/>Si las máquinas virtuales protegidas tienen un disco iSCSI, Site Recovery convierte el disco iSCSI de la máquina virtual protegida en un archivo VHD cuando la máquina virtual conmuta por error en Azure. Si la máquina virtual de Azure puede llegar al destino iSCSI, se conectará con él y, principalmente, verá dos discos: el disco VHD en la máquina virtual de Azure y el disco iSCSI de origen. En este caso debe desconectar el destino iSCSI que aparece en la máquina virtual de Azure que ha conmutado por error.<br/><br/>Para más información acerca de los permisos de usuario de VMware que Site Recovery necesita, consulte [Permisos de VMware para el acceso a vCenter](#vmware-permissions-for-vcenter-access).<br/><br/> **Máquinas de Windows Server (en máquina virtual de VMware o servidor físico)**<br/><br/>El servidor debe ejecutar un sistema operativo de 64 bits compatible: Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 con SP1 como mínimo.<br/><br/>El sistema operativo debe estar instalado en la unidad C y el disco del sistema operativo debe ser un disco básico de Windows (el sistema operativo no debe estar instalado en un disco dinámico de Windows).<br/><br/>Para equipos con Windows Server 2008 R2, debe tener .NET Framework 3.5.1 instalado.<br/><br/>Tendrá que proporcionar una cuenta de administrador (debe ser un administrador local en el equipo de Windows) para la instalación de inserción de Mobility Service en los servidores de Windows. Si la cuenta proporcionada no es una cuenta de dominio, deberá deshabilitar el control de acceso de usuarios remotos en el equipo local. Para más información, consulte [Instalar el servicio de movilidad con la instalación de inserción](#install-the-mobility-service-with-push-installation).<br/><br/>Site Recovery admite máquinas virtuales con un disco RDM. Durante la conmutación por recuperación, Site Recovery reutilizará el disco RDM si la máquina virtual de origen y el disco RDM original están disponibles. Si no están disponibles, durante la conmutación por recuperación, Site Recovery creará un nuevo archivo VMDK para cada disco.<br/><br/>**Equipos Linux**<br/><br/>Necesita un sistema operativo de 64 bits compatible: Red Hat Enterprise Linux 6.7; CentOS 6.5, 6.6 o 6.7; Oracle Enterprise Linux 6.4 o 6.5 con el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>Los archivos /etc/hosts de las máquinas protegidas deben contener entradas que asignen el nombre de host local a direcciones IP asociadas con todos los adaptadores de red. <br/><br/>Si desea conectarse a una máquina virtual de Azure con Linux después de la conmutación por error mediante un cliente Secure Shell (ssh), asegúrese de que el servicio de Secure Shell en la máquina protegida esté configurado para iniciarse automáticamente con el arranque del sistema y que las reglas de firewall permitan una conexión ssh con ella.<br/><br/>La protección solo puede habilitarse para máquinas con Linux con el almacenamiento siguiente: sistema de archivos (EXT3, EXT4, ReiserFS, XFS), software de múltiples rutas o Device Mapper (múltiples rutas) y administrador de volúmenes (LVM2). No se admiten servidores físicos con almacenamiento de controlador HP CCISS. El sistema de archivos ReiserFS solo se admite en SUSE Linux Enterprise Server 11 SP3.<br/><br/>Site Recovery admite máquinas virtuales con un disco RDM. Durante la conmutación por recuperación para Linux, Site Recovery no reutiliza el disco RDM. En lugar de eso, crea un nuevo archivo VMDK para cada disco RDM correspondiente. |

Solo en el caso de máquina virtual Linux, asegúrese de establecer la opción disk.enableUUID=true de los parámetros de configuración de la máquina virtual en VMware. Si la fila no existe, agréguela. Este paso es necesario a fin de proporcionar un UUID uniforme al VMDK, para que se monte correctamente. Sin esta opción, la conmutación por recuperación conllevará una descarga completa, aunque la máquina virtual esté disponible localmente. Si agrega esta opción, garantizará que solo se transfieran los cambios diferenciales durante la conmutación por recuperación.

## <a name="step-1-create-a-vault"></a>Paso 1: Creación de un almacén
1. Inicie sesión en el [Portal de Azure](https://manage.windowsazure.com/).
2. Expanda **Data Services** > **Recovery Services** y haga clic en **Almacén de Site Recovery**.
3. Haga clic en **Crear nuevo	** > **Creación rápida**.
4. En **Nombre**, escriba un nombre descriptivo para identificar el almacén.
5. En **Región**, seleccione la región geográfica del almacén. Para ver las regiones admitidas, consulte [Detalles de precios de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Haga clic en **Crear almacén**.
    ![Crear almacén](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como **Activo** en la página principal de **Recovery Services**.

## <a name="step-2-set-up-an-azure-network"></a>Paso 2: Configuración de una red de Azure
Configure una red de Azure para que las máquinas virtuales de Azure se conecten a una red después de la conmutación por error, de manera tal que la conmutación por recuperación en el sitio local funcione según lo esperado.

1. En Azure Portal, seleccione **Crear red virtual** y especifique el nombre de red, el intervalo de direcciones IP y el nombre de subred.
2. Si necesita realizar conmutación por recuperación, añada VPN/ExpressRoute a la red. Puede agregar VPN/ExpressRoute a la red incluso después de la conmutación por error.

Para más información sobre redes de Azure, consulte [Información general sobre redes virtuales](../virtual-network/virtual-networks-overview.md).

> [!NOTE]
> La [Migración de redes](../azure-resource-manager/resource-group-move-resources.md) entre grupos de recursos dentro de la misma suscripción o entre suscripciones no se admite en el caso de redes usadas para implementar Site Recovery.
>
>

## <a name="step-3-install-the-vmware-components"></a>Paso 3: Instalación de los componentes de VMware
Si desea replicar máquinas virtuales de VMware, siga estos pasos en el servidor de administración:

1. [Descargue](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) e instale VMware vSphere PowerCLI 6.0.
2. Reinicie el servidor.

## <a name="step-4-download-a-vault-registration-key"></a>Paso 4: Descarga de una clave de registro de almacén
1. En el servidor de administración, abra la consola de Site Recovery en Azure. En la página **Recovery Services**, haga clic en el almacén para abrir la página **Inicio rápido**. También puede abrir la página **Inicio rápido** en cualquier momento haciendo clic en el icono.

    ![Icono de Inicio rápido](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)
2. En la página **Inicio rápido**, haga clic en **Prepare Target Resources** (Preparar los recursos de destino)  > **Descargar una clave de registro**. El archivo de registro se genera automáticamente. Es válido durante&5; días a partir del momento en que se genera.

## <a name="step-5-install-the-management-server"></a>Paso 5: Instalación del servidor de administración
> [!TIP]
> Asegúrese de poder tener acceso a estas direcciones URL desde el servidor de administración:
>
> * *.hypervrecoverymanager.windowsazure.com
> * *.accesscontrol.windows.net
> * *.backup.windowsazure.com
> * *.blob.core.windows.net
> * *.store.core.windows.net
> * https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi
> * https://www.msftncsi.com/ncsi.txt
>
>



>[!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Setup-Registration/player]



1. En la página **Inicio rápido** , descargue el archivo de instalación unificada en el servidor.
2. Ejecute el archivo de instalación para comenzar la configuración en el **Asistente para la instalación unificada de Site Recovery**.
3. En **Antes de comenzar**, seleccione **Install the configuration server and process server** (Instalar el servidor de configuración y el servidor de procesos).

   ![Antes de empezar](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)
4. En **Third-Party Software License** (Licencia de software de terceros), haga clic en **Acepto** para descargar e instalar MySQL.

    ![Software de terceros](./media/site-recovery-vmware-to-azure-classic/combined-wiz105.PNG)
5. En **Registro**, busque y seleccione la clave de registro que descargó del almacén.

    ![Registro](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)
6. En **Configuración de Internet** , especifique cómo se conectará el proveedor que se ejecuta en el servidor de configuración a Azure Site Recovery a través de Internet.

   * Si quiere conectarse con el proxy configurado actualmente en la máquina, seleccione **Connect with existing proxy settings**(Conectarse con la configuración de proxy existente).
   * Si quiere que el proveedor se conecte directamente, seleccione **Connect directly without a proxy** (Conectarse directamente sin un proxy).
   * Si el proxy existente requiere autenticación, o quiere utilizar un proxy personalizado para la conexión del proveedor, seleccione **Connect with custom proxy settings** (Conectarse con una configuración de proxy personalizada).
     * Si utiliza un proxy personalizado, deberá especificar la dirección, el puerto y las credenciales.
     * Si utiliza un servidor proxy, se deberían haber permitido ya las siguientes direcciones URL:
       * *.hypervrecoverymanager.windowsazure.com    
       * *.accesscontrol.windows.net
       * *.backup.windowsazure.com
       * *.blob.core.windows.net
       * *.store.core.windows.net

    ![Firewall](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

1. En **Comprobación de requisitos previos**, el programa de instalación realiza una comprobación para asegurarse de que se pueda ejecutar la instalación.

    ![Requisitos previos](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

     Si aparece una advertencia sobre la **comprobación de la sincronización de hora global**, compruebe que la hora del reloj del sistema (configuración de **fecha y hora**) es la misma que la de la zona horaria.

     ![Problema de sincronización de hora](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

1. En **MySQL Configuration** (Configuración de MySQL), cree credenciales para iniciar sesión en la instancia de servidor MySQL que se va a instalar.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)
2. En **Detalles del entorno**, seleccione si replicará máquinas virtuales de VMware. En caso afirmativo, el programa de instalación comprueba si PowerCLI 6.0 está instalado.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)
3. En **Ubicación de instalación**, seleccione dónde quiere instalar los archivos binarios y almacenar la memoria caché. Puede seleccionar una unidad que tenga al menos 5 GB de espacio disponible en disco, pero se recomienda usar una unidad de memoria caché con 600 GB o más de espacio disponible.

   ![Ubicación de instalación](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)
4. En **Selección de red**, especifique el agente de escucha (adaptador de red y puerto SSL) en el que el servidor de configuración enviará y recibirá los datos de replicación. Puede modificar el puerto predeterminado (9443). Además de este puerto, un servidor web utilizará el puerto 443 que organiza las operaciones de replicación. No use el puerto 443 para recibir tráfico de replicación.

    ![Selección de red](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)



1. En **Resumen**, revise la información y haga clic en **Instalar**. Se genera una frase de contraseña cuando finaliza la instalación. La necesitará al habilitar la replicación, así que cópiela y manténgala en una ubicación segura.

   ![Resumen](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)


> [!WARNING]
> Debe instalarse el proxy del agente del servicio de recuperación de Microsoft Azure.
> Una vez completada la instalación, inicie el Shell de Microsoft Azure Recovery Services en el menú Inicio de Windows. En la ventana de comandos que se abre, ejecute el siguiente conjunto de comandos para definir la configuración del servidor proxy.
>
>
    $pwd = ConvertTo-SecureString -String ProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
>


### <a name="run-setup-from-the-command-line"></a>Ejecución de la configuración desde la línea de comandos
También puede ejecutar el asistente unificado desde la línea de comandos, de la siguiente manera:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Donde:

* /ServerMode: Obligatorio. Especifica si la instalación debe instalar los servidores de configuración y de procesos o solo el servidor de procesos (se usa para instalar servidores de procesos adicionales). Valores de entrada: CS, PS.
* InstallDrive: Obligatorio. Especifica la carpeta donde se instalan los componentes.
* /MySQLCredFilePath. Obligatorio. Especifica la ruta de acceso a un archivo donde se almacenan las credenciales del servidor MySQL. Obtenga la plantilla para crear el archivo.
* /VaultCredFilePath. Obligatorio. Ubicación del archivo de credenciales del almacén.
* /EnvType. Obligatorio. Tipo de instalación. Valores: VMware, NonVMware.
* /PSIP y /CSIP. Obligatorio. Dirección IP del servidor de procesos y del servidor de configuración.
* /PassphraseFilePath. Obligatorio. Ubicación del archivo de frase de contraseña.
* /ByPassProxy. Opcional. Especifica el servidor de administración que se conecta a Azure sin proxy.
* /ProxySettingsFilePath. Opcional. Especifica la configuración de un proxy personalizado (ya sea el proxy predeterminado en el servidor que requiere autenticación o un proxy personalizado).

## <a name="step-6-set-up-credentials-for-the-vcenter-server"></a>Paso 6: Configuración de las credenciales para el servidor vCenter
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Discovery/player]
>
>

El servidor de procesos detectará automáticamente las máquinas virtuales de VMware que administra un servidor vCenter. Para realizar la detección automática, Site Recovery necesita una cuenta y credenciales que puedan tener acceso al servidor vCenter. Esto no es relevante si solo va a replicar servidores físicos.

Para establecer la cuenta y las credenciales:

1. En el servidor vCenter, cree un rol (**Azure_Site_Recovery**) en el nivel de vCenter con los [permisos necesarios](#vmware-permissions-for-vcenter-access).
2. Asigne el rol **Azure_Site_Recovery** a un usuario de vCenter.

   > [!NOTE]
   > Una cuenta de usuario de vCenter que cuenta con el rol de solo lectura puede ejecutar la conmutación por error sin tener que apagar las máquinas de origen protegidas. Si desea apagar esas máquinas, necesitará el rol Azure_Site_Recovery. Si solo migra máquinas virtuales desde VMware a Azure y no necesita realizar conmutación por recuperación, el rol de solo lectura es suficiente.
   >
   >
3. Para agregar la cuenta, abra **cspsconfigtool**. Esta herramienta está disponible como acceso directo en el escritorio y se encuentra en la carpeta [UBICACIÓN DE INSTALACIÓN]\home\svsystems\bin.
4. En la pestaña **Administrar cuentas**, haga clic en **Agregar cuenta**.

    ![Agregar cuenta](./media/site-recovery-vmware-to-azure-classic/credentials1.png)
5. En **Detalles de la cuenta** , agregue las credenciales que se podrán usar para tener acceso al servidor vCenter. El nombre de la cuenta podría tardar más de 15 minutos en aparecer en el portal. Para que se actualice inmediatamente, haga clic en **Actualizar** en la pestaña **Servidores de configuración**.

    ![Detalles](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## <a name="step-7-add-vcenter-servers-and-esxi-hosts"></a>Paso 7: Incorporación de servidores vCenter y hosts ESXi
Si replica máquinas virtuales de VMware, deberá agregar un servidor vCenter (o un host ESXi).

1. En la pestaña **Servidores** > **Servidores de configuración**, seleccione **Agregar servidor vCenter**.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)
2. Agregue los detalles del servidor vCenter o del host ESXi, el nombre de la cuenta que especificó para tener acceso al servidor vCenter en el paso anterior y el servidor de procesos que se usará para detectar las máquinas virtuales de VMware que administra el servidor vCenter. El servidor vCenter o el host ESXi deben estar en la misma red que el servidor en el que está instalado el servidor de procesos.

   > [!NOTE]
   > Si agrega el servidor vCenter o el host ESXi con una cuenta que no tiene privilegios de administrador en el servidor vCenter o el servidor host, asegúrese de que las cuentas de vCenter o ESXi tienen habilitados los siguientes privilegios: Centro de datos, Almacén de datos, Carpeta, Host, Red, Recurso, Máquina virtual y Conmutador distribuido de vSphere. El servidor vCenter debe tener habilitado el privilegio Vistas de almacenamiento.
   >
   >

    ![Agregar servidor vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)
3. Cuando se complete la detección, el servidor vCenter aparecerá en la pestaña **Servidores de configuración**.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)

## <a name="step-8-create-a-protection-group"></a>Paso 8: Creación de un grupo de protección
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Protection/player]
>
>

Los grupos de protección son agrupaciones lógicas de máquinas virtuales o servidores físicos que desea proteger con la misma configuración de protección. Aplique la configuración de protección a un grupo de protección y esa configuración se aplicará a todas las máquinas (virtuales o físicas) que agregue al grupo.

1. Vaya a **Elementos protegidos** > **Grupo de protección** y haga clic en el icono para agregar un grupo de protección.

    ![Crear un grupo de protección](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)
2. En la página **Especificar configuración de grupos de protección**, especifique un nombre para el grupo. En la lista desplegable **Desde**, seleccione el servidor de configuración en el que desea crear el grupo. El **destino** es Microsoft Azure.

    ![Configuración del grupo de protección](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)
3. En la página **Especificar valores de replicación**, configure las opciones de replicación que se usarán en todas las máquinas del grupo.

    ![Replicación del grupo de protección](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

   * **Coherencia de múltiples VM**: si se activa esta opción, se crean puntos de recuperación coherentes con las aplicaciones compartidas en las máquinas del grupo de protección. Esta configuración es especialmente importante cuando todas las máquinas del grupo de protección ejecutan la misma carga de trabajo. Se recuperarán todos los equipos en el mismo punto de datos. Esta opción está disponible tanto si replica máquinas virtuales de VMware como servidores físicos (Windows o Linux).
   * **Umbral de RPO**: define el RPO. Se generan alertas cuando la replicación de protección de datos continua supera el valor del umbral de RPO configurado.
   * **Retención de punto de recuperación**: especifica el período de retención. Los equipos protegidos se pueden recuperar en cualquier punto dentro de este período.
   * **Frecuencia de la instantánea de coherencia de la aplicación**: especifica la frecuencia con la que se crearán puntos de recuperación que contengan instantáneas coherentes con la aplicación.

Al seleccionar la marca de verificación, se creará un grupo de protección con el nombre que ha especificado. Además, se crea un segundo grupo de protección con el nombre *protection-group-name*-Failback. Este grupo de protección se usa si realiza conmutación por recuperación en el sitio local después de la conmutación por error en Azure. En la página **Elementos protegidos** , puede supervisar los grupos de protección a medida que se crean.

## <a name="step-9-install-the-mobility-service"></a>Paso 9: Instalación del servicio de movilidad
El primer paso para habilitar la protección de las máquinas virtuales y los servidores físicos es instalar el servicio de movilidad. Puede hacerlo de dos maneras:

* Insertar e instalar automáticamente el servicio en cada máquina desde el servidor de proceso. Cuando añade máquinas a un grupo de protección que ya ejecutan una versión apropiada de Mobility Service, no se producirá la instalación de inserción. También puede instalar automáticamente el servicio mediante un método de inserción empresarial, como WSUS o System Center Configuration Manager. Antes de hacerlo, asegúrese de que el servidor de administración esté configurado.
* Instale manualmente el servicio en cada máquina que desee proteger. Antes de hacerlo, asegúrese de que el servidor de administración esté configurado.

### <a name="install-the-mobility-service-with-push-installation"></a>Instalar el servicio de movilidad con la instalación de inserción
Al añadir máquinas a un grupo de protección, Mobility Service se inserta automáticamente y el servidor de procesos lo instala en cada máquina.

#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Preparación de la inserción automática en máquinas Windows
Para preparar las máquinas Windows para que el servidor de procesos pueda instalar automáticamente Mobility Service:

1. Cree una cuenta que el servidor de procesos pueda utilizar para acceder a la máquina. La cuenta debe tener privilegios de administrador (local o de dominio). Estas credenciales solo se utilizan para la instalación de inserción de Mobility Service.

   > [!NOTE]
   > Si no utiliza una cuenta de dominio, deberá deshabilitar el control de acceso de usuario remoto en la máquina local. Para ello, en el Registro, en HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, añada la entrada LocalAccountTokenFilterPolicy DWORD con un valor de 1. Para añadir la entrada del Registro desde un comando abierto de la CLI o mediante PowerShell, escriba **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.
   >
   >
2. Para el Firewall de Windows de la máquina que quiere proteger, seleccione **Permitir una aplicación o una característica a través de Firewall** y habilite **Compartir archivos e impresoras** e **Instrumental de administración de Windows**. Para las máquinas que pertenecen a un dominio, puede configurar la directiva de firewall con un objeto de directiva de grupo.

   ![Configuración de firewall](./media/site-recovery-vmware-to-azure-classic/mobility1.png)
3. Añada la cuenta que ha creado:

   * Abra **cspsconfigtool**. Esta herramienta está disponible como acceso directo en el escritorio y se encuentra en la carpeta [UBICACIÓN DE INSTALACIÓN]\home\svsystems\bin.
   * En la pestaña **Administrar cuentas**, haga clic en **Agregar cuenta**.
   * Añada la cuenta que ha creado. Una vez que agregue la cuenta, deberá proporcionar las credenciales cuando añada una máquina a un grupo de protección.

#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Preparación para la inserción automática en los servidores Linux
1. Asegúrese de que la máquina con Linux que quiere proteger sea compatible, tal como se describe en [Requisitos previos locales](#on-premises-prerequisites). Asegúrese de que hay conectividad de red entre la máquina que desea proteger y el servidor de administración que ejecuta el servidor de procesos.
2. Cree una cuenta que el servidor de procesos pueda utilizar para acceder a la máquina. La cuenta debe ser un usuario raíz en el servidor Linux de origen. Estas credenciales solo se utilizan para la instalación de inserción de Mobility Service.

   * Abra **cspsconfigtool**. Esta herramienta está disponible como acceso directo en el escritorio y se encuentra en la carpeta [UBICACIÓN DE INSTALACIÓN]\home\svsystems\bin.
   * En la pestaña **Administrar cuentas**, haga clic en **Agregar cuenta**.
   * Añada la cuenta que ha creado. Una vez que agregue la cuenta, deberá proporcionar las credenciales cuando añada una máquina a un grupo de protección.
3. Compruebe que el archivo /etc/hosts del servidor Linux de origen contiene entradas que asignan el nombre de host local a las direcciones IP asociadas con todos los adaptadores de red.
4. Instale los paquetes openssh, openssh-server y openssl más recientes en la máquina que desea proteger.
5. Asegúrese de que SSH está habilitado y ejecutándose en el puerto 22.
6. Habilite la autenticación de la contraseña y del subsistema SFTP en el archivo sshd_config:

   * Inicie sesión como root.
   * En el archivo /etc/ssh/sshd_config, encuentre la línea que comienza con PasswordAuthentication.
   * Quite la marca de comentario de la línea y cambie el valor de **no** a **yes**.
   * Busque la línea que comienza por **Subsystem** y quite la marca de comentario.

     ![Valor predeterminado de invalidación de Linux de ningún subsistema](./media/site-recovery-vmware-to-azure-classic/mobility2.png)

### <a name="install-the-mobility-service-manually"></a>Instalación manual de Mobility Service
Los instaladores están disponibles en C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository.

| Sistema operativo de origen | Archivo de instalación del servicio de movilidad |
| --- | --- |
| Windows Server&64; (solo&64; bits) |Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe |
| CentOS 6.4, 6.5, 6.6 (solo 64 bits) |Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz |
| SUSE Linux Enterprise Server 11 SP3 (solo 64 bits) |Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz |
| Oracle Enterprise Linux 6.4, 6.5 (solo 64 bits) |Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz |

#### <a name="install-the-mobility-service-manually-on-a-windows-server"></a>Instalación manual de Mobility Service en un servidor Windows
1. Descargue y ejecute el instalador pertinente.
2. En **Antes de comenzar**, seleccione **Mobility Service**.

    ![Instalación de Mobility Service](./media/site-recovery-vmware-to-azure-classic/mobility3.png)
3. En **Configuration Server Details** (Detalles del servidor de configuración), especifique la dirección IP del servidor de administración y la frase de contraseña que se generó cuando instaló los componentes del servidor de administración. Para recuperar la frase de contraseña, ejecute **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –n** en el servidor de administración.

    ![Mobility Service](./media/site-recovery-vmware-to-azure-classic/mobility6.png)
4. En **Ubicación de instalación**, deje la ubicación predeterminada y haga clic en **Siguiente** para comenzar la instalación.
5. En **Progreso de la instalación**, compruebe la instalación y reinicie la máquina, si así se le solicita.

También puede escribir el siguiente texto en la línea de comandos para realizar la instalación:

    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]

En el comando anterior:

* /Role: Obligatorio. Especifica si se debe instalar el servicio de movilidad.
* /InstallLocation: Obligatorio. Especifica dónde instalar el servicio.
* /PassphraseFilePath: Obligatorio. Especifica la frase de contraseña del servidor de configuración.
* /LogFilePath: Obligatorio. Especifica la ubicación del archivo de configuración de registro.

#### <a name="uninstall-the-mobility-service-manually"></a>Desinstalación manual de Mobility Service
Puede desinstalar Mobility Service mediante **Desinstalar o cambiar un programa** en el Panel de Control o mediante la línea de comandos.

Este es el comando que debe usar para desinstalar Mobility Service con la línea de comandos:

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="change-the-ip-address-of-the-management-server"></a>Modificación de la dirección IP del servidor de administración
Después de ejecutar el asistente, puede modificar la dirección IP del servidor de administración de la siguiente manera:

1. Abra el archivo hostconfig.exe (que se encuentra en el escritorio).
2. En la pestaña **Global**, cambie la dirección IP del servidor de administración.

   > [!NOTE]
   > Modifique únicamente la dirección IP del servidor de administración. El número de puerto para las comunicaciones del servidor de administración debe ser 443 y se debe dejar habilitada la opción **Usar HTTPS**. No cambie la frase de contraseña.
   >
   >

    ![Dirección IP del servidor de administración](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### <a name="install-the-mobility-service-manually-on-a-linux-server"></a>Instalación manual de Mobility Service en un servidor Linux
1. Copie el archivo tar adecuado en la máquina Linux que desea proteger. Consulte la tabla de [Instalación manual de Mobility Service](#install-the-mobility-service-manually) para determinar qué archivo tar se debe usar.
2. Abra un programa de shell y extraiga el archivo TAR comprimido en una ruta de acceso local mediante la ejecución de `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Cree un archivo con el nombre passphrase.txt en el directorio local en el que extrajo el contenido del archivo tar. Para ello, copie la frase de contraseña desde C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase en el servidor de administración y guárdela en el archivo passphrase.txt mediante la ejecución de *`echo <passphrase> >passphrase.txt`* en el shell.
4. Introduzca el siguiente comando para instalar Mobility Service: *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*
5. Especifique la dirección IP interna del servidor de administración y asegúrese de seleccionar el puerto 443.

#### <a name="install-the-mobility-service-from-the-command-line"></a>Instalación de Mobility Service desde la línea de comandos

Copie la frase de contraseña desde C:\Program Files (x86)\InMage Systems\private\connection en el servidor de administración y guárdela como "passphrase.txt" en el servidor de administración. Luego, ejecute los siguientes comandos: En nuestro ejemplo, la dirección IP del servidor de administración es 104.40.75.37 y el puerto HTTPS es 443:

Para instalar en un servidor de producción:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Para instalar en el servidor de destino maestro:

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## <a name="step-10-enable-protection-for-a-machine"></a>Paso 10: Habilitación de la protección de una máquina
Para habilitar la protección, agregue máquinas virtuales y servidores físicos a un grupo de protección. Antes de empezar, tenga en cuenta lo siguiente si protege máquinas virtuales de VMware:

* Las máquinas virtuales de VMware se detectan cada 15 minutos y podrían tardar más de 15 minutos en aparecer en el portal de Site Recovery después de su detección.
* Los cambios de entorno en la máquina virtual (como la instalación de herramientas de VMware) también pueden demorar más de 15 minutos en actualizarse en Site Recovery.
* Puede consultar la hora de la detección más reciente de las máquinas virtuales de VMware en el campo **Último contacto a las** correspondiente al servidor vCenter / host ESXi en la pestaña **Servidores de configuración**.
* Si añade un servidor vCenter o un host ESXi después de crear un grupo de protección, es posible que transcurran más de 15 minutos hasta que se actualice el portal de Azure Site Recovery y aparezcan las máquinas virtuales en el cuadro de diálogo **Add machines to a protection group** (Agregar máquinas a un grupo de protección).
* Si desea continuar inmediatamente con la incorporación de equipos al grupo de protección, sin esperar la detección programada, resalte el servidor de configuración (no haga clic en él) y haga clic en el botón **Actualizar**.

Además:

* Se recomienda diseñar los grupos de protección para que reflejen las cargas de trabajo. Por ejemplo, añada máquinas que ejecuten una aplicación específica al mismo grupo.
* Cuando agrega máquinas a un grupo de protección, el servidor de procesos inserta e instala automáticamente el servicio de movilidad, si todavía no está instalado. Deberá preparar el mecanismo de inserción tal como se describe en el paso anterior.

### <a name="add-machines-to-a-protection-group"></a>Incorporación de los equipos a un grupo de protección

1. Vaya a **Elementos protegidos** > **Grupo de protección** > **Máquinas** > **Agregar máquinas**.
2. Si protege máquinas virtuales de VMware, en **Seleccionar máquinas virtuales**, seleccione un servidor vCenter que administre las máquinas virtuales (o el host EXSi en el que se ejecutan) y después seleccione las máquinas.

    ![Habilitar protección para las máquinas virtuales](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)
3. Si protege servidores físicos, en **Seleccionar máquinas virtuales**, abra el Asistente para **agregar máquinas físicas** y proporcione la dirección IP y un nombre descriptivo. A continuación, seleccione la familia de sistemas operativos.

   ![Habilitación de la protección de servidores físicos](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)
4. En **Specify Target Resources** (Especificar recursos de destino), seleccione la cuenta de almacenamiento que utiliza para la replicación y seleccione si la configuración se debe usar para todas las cargas de trabajo. Por el momento, no se admiten cuentas de Premium Storage.

   > [!NOTE]
   > No se admite el traslado de cuentas de almacenamiento creadas mediante [Azure Portal](../storage/storage-create-storage-account.md) entre grupos de recursos.                           
   > No se admite la [migración de cuentas de almacenamiento](../azure-resource-manager/resource-group-move-resources.md) entre grupos de recursos de la misma suscripción o entre suscripciones para cuentas de almacenamiento usadas para implementar Site Recovery.
   >
   >

    ![Configuración de los ajustes de destino](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)
5. En **Especificar cuentas** , seleccione la cuenta que [configuró](#install-the-mobility-service-with-push-installation) para la instalación automática de Mobility Service.

    ![Especificación de las cuentas](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)
6. Haga clic en la marca de verificación para terminar de agregar equipos al grupo de protección y para iniciar la replicación inicial en cada equipo.

   > [!NOTE]
   > Si se ha preparado la instalación de inserción, Mobility Service se instala automáticamente en las máquinas que no tienen este servicio a medida que se añaden al grupo de protección. Una vez que se instala el servicio, se inicia un trabajo de protección y se produce un error. Después del error, deberá reiniciar manualmente cada máquina que haya tenido instalado Mobility Service. Una vez que reinicie, el trabajo de protección vuelve a comenzar y se produce la replicación inicial.
   >
   >

Puede supervisar el estado en la página **Trabajos** .

![Supervisión del estado en la página Trabajos](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

El estado de protección también se puede supervisar en **Elementos protegidos** > *nombre del grupo de protección* > **Máquinas virtuales**. Una vez que se complete la replicación inicial y que los datos estén sincronizados, el estado de la máquina cambia a **Protegido**.

![Supervisión del estado en Elementos protegidos](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)

## <a name="step-11-set-protected-machine-properties"></a>Paso 11: Establecimiento de las propiedades de la máquina protegida
1. Cuando las máquinas ya tienen el estado **Protegido**, puede configurar sus propiedades de conmutación por error. En los detalles del grupo de protección, seleccione la máquina y abra la pestaña **Configurar**.
2. Site Recovery sugiere automáticamente las propiedades para la máquina virtual de Azure y detecta la configuración de la red local.

    ![Establecer propiedades de máquina virtual](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)
3. Puede cambiar las siguientes opciones:

   * **Nombre de la máquina virtual de Azure**: el nombre que tendrá la máquina en Azure después de la conmutación por error. El nombre debe cumplir con los requisitos de Azure.
   * **Tamaño de la máquina virtual de Azure**: el número de adaptadores de red viene determinado por el tamaño que especifique para la máquina virtual de destino. Para más información sobre los tamaños y adaptadores, consulte las [tablas de tamaños](../virtual-machines/virtual-machines-linux-sizes.md#size-tables). Observe lo siguiente:

     * Cuando modifique el tamaño de una máquina virtual y guarde la configuración, el número de adaptadores de red cambiará la próxima vez que abra la pestaña **Configurar**. El número mínimo de adaptadores de red en máquinas virtuales de destino es igual al número mínimo de adaptadores de red en una máquina virtual de origen. El número máximo de adaptadores de red se determina por el tamaño de la máquina virtual.
       * Si el número de adaptadores de red en la máquina de origen es menor o igual al número de adaptadores permitido para el tamaño de la máquina de destino, el destino tendrá el mismo número de adaptadores que el origen.
       * Si el número de adaptadores para la máquina virtual de origen supera el número permitido para el tamaño de destino, entonces se utilizará el tamaño máximo de destino.
        Por ejemplo, si una máquina de origen tiene dos adaptadores de red y el tamaño de la máquina de destino admite cuatro, el equipo de destino tendrá dos adaptadores. Si la máquina de origen tiene dos adaptadores pero el tamaño de destino compatible solo admite uno, la máquina de destino tendrá solo un adaptador.
     * Si la máquina virtual tiene varios adaptadores de red, todos ellos deben conectarse a la misma red de Azure.
   * **Red de Azure**: debe especificar una red de Azure a la que se conectarán las máquinas virtuales de Azure después de la conmutación por error. Si no especifica ninguna, las máquinas virtuales de Azure no se conectarán a ninguna red. Además, debe especificar una red de Azure si desea realizar conmutación por recuperación desde Azure al sitio local. La conmutación por recuperación requiere una conexión VPN entre una red de Azure y una red local.
   * **Dirección IP/subred de Azure**: para cada adaptador de red, seleccione la subred a la que se debe conectar la máquina virtual de Azure. Tenga en cuenta que si el adaptador de red de la máquina de origen está configurado para utilizar una dirección IP estática, puede especificar una dirección IP estática para la máquina virtual de Azure. Si no proporciona una dirección IP estática, se asignará cualquier dirección IP que se encuentre disponible. Si se especifica la dirección IP de destino, pero ya la usa otra máquina virtual en Azure, la conmutación por error presentará errores. Si el adaptador de red de la máquina de origen está configurado para utilizar DHCP, esta será la configuración para Azure.      

## <a name="step-12-create-a-recovery-plan-and-run-a-failover"></a>Paso 12: Creación de un plan de recuperación y ejecución de una conmutación por error
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Failover/player]
>
>

Puede ejecutar una conmutación por error para una sola máquina, o bien puede hacerlo para varias máquinas virtuales que realicen la misma tarea o ejecuten la misma carga de trabajo. Para ejecutar una conmutación por error de varias máquinas al mismo tiempo, debe agregarlas a un plan de recuperación.

Para crear un plan de recuperación:

1. En la página **Planes de recuperación**, haga clic en **Add Recovery Plan** (Agregar plan de recuperación) y agregue un plan de recuperación. Especifique los detalles del plan y seleccione **Azure** como destino.

 ![Configurar plan de recuperación](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)
2. En **Seleccionar máquina virtual**, seleccione un grupo de protección y después seleccione las máquinas del grupo que se van a agregar al plan de recuperación.

 ![Agregar máquinas virtuales](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

Puede personalizar el plan para crear grupos y organizar en secuencia el orden en que se realizará la conmutación por error de las máquinas que se encuentran en el plan de recuperación. También puede agregar scripts y avisos para las acciones manuales. Los scripts se pueden crear manualmente o mediante [runbooks de Azure Automation](site-recovery-runbook-automation.md). Para más información acerca de la personalización de los planes de recuperación, consulte [Creación de planes de recuperación](site-recovery-create-recovery-plans.md).

## <a name="run-a-failover"></a>Ejecución de la conmutación por error
Antes de ejecutar una conmutación por error:

* Asegúrese de que el servidor de administración esté en ejecución y disponible. De lo contrario, la conmutación por error presentará errores.
* Si ejecuta una conmutación por error no planeada:

  * Si es posible, debe apagar las máquinas principales antes de ejecutar una conmutación por error no planeada. Esto garantiza que la máquina de origen y de réplica no se ejecuten al mismo tiempo. Si replica máquinas virtuales de VMware, cuando ejecute una conmutación por error no planeada, podrá especificar que Site Recovery debe intentar apagar las máquinas de origen. En función del estado del sitio principal, esto podría funcionar o no. Si replica servidores físicos, Site Recovery no ofrece esta opción.
  * Una conmutación por error no planeada detiene la replicación de datos desde las máquinas principales para que no se transfiera ningún diferencial de datos después de que comience una conmutación por error no planeada.
  * Si desea conectarse a la máquina virtual de réplica en Azure tras la conmutación por error, habilite la conexión a Escritorio remoto en la máquina de origen antes de ejecutar la conmutación por error. A continuación, permita la conexión RDP a través del firewall. También debe permitir RDP en el punto de conexión público de la máquina virtual de Azure después de la conmutación por error. Siga los [procedimientos recomendados](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) para garantizar que RDP funciona después de una conmutación por error.

> [!NOTE]
> Para obtener el mejor rendimiento cuando realice una conmutación por error a Azure, asegúrese de que instaló el agente de Azure en la máquina protegida. Esto permite que la máquina arranque con mayor rapidez y ayuda a diagnosticar problemas. El agente de Azure está disponible para [Linux](https://github.com/Azure/WALinuxAgent) y [Windows](http://go.microsoft.com/fwlink/?LinkID=394789).
>
>

### <a name="run-a-test-failover"></a>Ejecución de una conmutación por error de prueba
Ejecute una conmutación por error de prueba para simular los procesos de conmutación por error y recuperación en una red aislada que no afecte a su entorno de producción y permita que la replicación normal continúe con normalidad. La conmutación por error de prueba se inicia en el origen y puede ejecutarla de dos maneras:

* **Sin especificar una red de Azure**: si ejecuta una conmutación por error de prueba sin una red, la prueba comprobará que las máquinas virtuales se inician y aparecen correctamente en Azure. Las máquinas virtuales no se conectarán a una red de Azure después de la conmutación por error.
* **Especificando una red de Azure**: este tipo de conmutación por error comprueba que todo el entorno de replicación aparezca según lo esperado y que las máquinas virtuales de Azure estén conectadas a la red especificada.

Para ejecutar una prueba de conmutación por error:

1. En la página **Planes de recuperación**, seleccione el plan y haga clic en **Probar conmutación por error**.

 ![Selección del plan](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)
2. En **Confirmar conmutación por error de prueba**, elija **Ninguna** a fin de indicar que no quiere utilizar una red de Azure para la conmutación por error de prueba, o bien seleccione la red a la que las máquinas virtuales de prueba se conectarán después de la conmutación por error. Haga clic en la marca de verificación para iniciar la conmutación por error.

 ![Realización de una selección](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)
3. Supervise el progreso de la conmutación por error en la pestaña **Trabajos** .

 ![Supervisión de progreso](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)
4. Cuando se complete la conmutación por error, también debería ver la máquina de réplica de Azure en Azure Portal, en **Máquinas virtuales**. Si desea iniciar una conexión RDP a la máquina virtual de Azure, deberá abrir el puerto 3389 en el punto de conexión de la máquina virtual.
5. Una vez que termine, cuando la conmutación por error alcance la fase **Completar** prueba, haga clic en **Completar prueba** para finalizar. En **Notas**, registre y guarde las observaciones asociadas a la conmutación por error de prueba.
6. Haga clic en **La conmutación por error de prueba se ha completado** para limpiar automáticamente el entorno de prueba. Tras ello, la conmutación por error de prueba mostrará el estado **Completado**. Se eliminarán todos los elementos o las máquinas virtuales que se crearon durante la conmutación por error de prueba. Si una conmutación por error de prueba continua durante más de dos semanas, se forzará su finalización.

### <a name="run-an-unplanned-failover"></a>Ejecución de una conmutación por error no planeada
La conmutación por error no planeada se inicia desde Azure y se puede realizar incluso si el sitio primario no está disponible.

1. En la página **Planes de recuperación**, seleccione el plan y haga clic en **Conmutación por error** > **Conmutación por error no planeada**.

 ![Selección del plan](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)
2. Si replica máquinas virtuales de VMware, puede intentar apagar las máquinas virtuales locales. Esta medida no garantiza resultados, y la conmutación por error proseguirá, independientemente de si esta medida tiene éxito o no. Si no tiene éxito, los detalles de error aparecerán en la pestaña **Trabajos** en **Unplanned Failover Jobs** (Trabajos de conmutación por error no planeada).

 ![Opción para apagar máquinas virtuales locales](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

 > [!NOTE]
 > Esta opción no está disponible si replica servidores físicos. Deberá intentar apagarlos manualmente si es posible.
 >
 >

3. En **Confirmar conmutación por error**, compruebe la dirección de conmutación por error (a Azure) y seleccione el punto de recuperación que quiera usar para ella. Si habilitó varias máquinas virtuales al configurar las propiedades de replicación, puede realizar la recuperación al punto de recuperación coherente con el bloqueo o la aplicación más reciente. También puede seleccionar **Punto de recuperación personalizado** para realizar la recuperación a un momento dado anterior. Haga clic en la marca de verificación para iniciar la conmutación por error.

 ![Confirmación de dirección de conmutación por error](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)
4. Espere que se complete el trabajo de conmutación por error no planeada. Puede supervisar el progreso de la conmutación por error en la pestaña **Trabajos** . Incluso si se producen errores durante una conmutación por error no planeada, el plan de recuperación se ejecuta hasta que se completa. También debería ver la máquina de réplica de Azure en Azure Portal, en **Máquinas virtuales**.

### <a name="connect-to-replicated-azure-virtual-machines-after-failover"></a>Conectarse a máquinas virtuales replicadas de Azure después de la conmutación por error
Para conectarse a las máquinas virtuales replicadas en Azure después de la conmutación por error, necesita lo siguiente:

- Una conexión a Escritorio remoto habilitada en la máquina principal.
- Firewall de Windows en la máquina principal configurado para permitir RDP.
- RDP añadido al punto de conexión público para la máquina virtual de Azure.

Para más información acerca de cómo configurar esto, consulte [Troubleshooting remote desktop connection after failover using ASR](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) (Solución de problemas de la conexión a Escritorio remoto tras conmutar por error con ASR).

## <a name="deploy-additional-process-servers"></a>Implementar servidores de procesos adicionales
Si debe escalar horizontalmente la implementación a más de 200 máquinas de origen o si la tasa de renovación diaria total supera los 2 TB, necesitará servidores de procesos adicionales para controlar el volumen del tráfico. Para configurar un servidor de procesos adicional, compruebe los requisitos que aparecen en [Servidores de procesos adicionales](#additional-process-servers) y, después, configure el servidor de procesos con las siguientes instrucciones. Después de configurar el servidor, puede configurar las máquinas de origen para que lo utilicen.

### <a name="set-up-an-additional-process-server"></a>Configuración de un servidor de procesos adicional
Para configurar un servidor de procesos adicional, siga estos pasos:

* Ejecute el asistente unificado para configurar un servidor de administración solo como servidor de procesos.
* Si desea administrar la replicación de datos solo mediante el nuevo servidor de procesos, deberá migrar las máquinas protegidas.

### <a name="install-the-process-server"></a>Instalación del servidor de procesos
1. En la página **Inicio rápido**, descargue el archivo de instalación unificada para la instalación de los componentes de Site Recovery. Ejecute la configuración.
2. En **Antes de comenzar**, seleccione **Add additional process servers to scale out deployment** (Agregar servidores de procesos adicionales para el escalado horizontal de la implementación).

 ![Agregar servidores de procesos](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)
3. Complete el asistente tal como lo hizo al [configurar](#step-5-install-the-management-server) el primer servidor de administración.
4. En **Configuration Server Details** (Detalles del servidor de configuración), especifique la dirección IP del servidor de administración original donde instaló el servidor de configuración e introduzca la frase de contraseña. Si no tiene la frase de contraseña, ejecute **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –n** en el servidor de administración original para recuperarla.

 ![Detalles del servidor de configuración](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrar máquinas para utilizar el nuevo servidor de procesos
1. Abra **Servidores de configuración** > **Servidor** > *nombre del servidor de administración original* > **Detalles del servidor**.

 ![Detalles del servidor](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)
2. En la lista **Servidores de procesos**, seleccione **Cambiar servidor de procesos** junto al servidor que desea modificar.

 ![Actualización del servidor de procesos](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)
3. Seleccione **Cambiar servidor de procesos**, seleccione **Servidor de procesos de destino** y, a continuación, seleccione el nuevo servidor de administración. A continuación, seleccione las máquinas virtuales que va a controlar el nuevo servidor de procesos. Haga clic en el icono de información para obtener detalles sobre el servidor. Aparece el espacio promedio que se necesita para replicar cada máquina virtual seleccionada en el nuevo servidor de procesos para ayudarlo a tomar decisiones relacionadas con la carga. Haga clic en la marca de verificación para comenzar a replicar en el nuevo servidor de procesos.

 ![Cambio del servidor de procesos](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)

## <a name="vmware-permissions-for-vcenter-access"></a>Permisos de VMware para el acceso a vCenter
El servidor de procesos puede detectar automáticamente las máquinas virtuales en un servidor vCenter. Para ejecutar la detección automática, deberá definir un rol (Azure_Site_Recovery) en el nivel de vCenter para permitir que Site Recovery tenga acceso al servidor vCenter. Si solo debe migrar máquinas de VMware a Azure y no necesita realizar la conmutación por recuperación desde Azure, basta con que defina un rol de solo lectura. Configure los permisos tal como se describe en [Paso 6: Configuración de las credenciales para el servidor vCenter](#step-6-set-up-credentials-for-the-vcenter-server). En la tabla siguiente se resumen los permisos de rol:

| **Rol** | **Detalles** | **Permisos** |
| --- | --- | --- |
| Rol Azure_Site_Recovery |Detección de máquinas virtuales de VMware |Asigne estos privilegios para el servidor vCenter:<br/><br/>Almacén de datos: asignar espacio, examinar almacén de datos, operaciones de archivo de bajo nivel, quitar archivo, actualizar archivos de máquina virtual<br/><br/>Red: asignación de red<br/><br/>Recurso: asignar máquina virtual a grupo de recursos, migrar máquina virtual apagada, migrar máquina virtual encendida<br/><br/>Tareas: crear tarea, actualizar tarea<br/><br/>Máquina virtual > Configuración<br/><br/>Máquina virtual > Interactuar > Responder a pregunta, conexión de dispositivos, configurar soporte de CD, configurar soporte de disquete, apagar, encender, instalación de herramientas de VMware<br/><br/>Máquina virtual > Inventario > Crear, registrar, anular registro<br/><br/>Máquina virtual > Aprovisionamiento > Permitir descarga de máquina virtual, permitir carga de archivos de máquina virtual<br/><br/>Máquina virtual > Instantáneas > Quitar instantáneas |
| Rol de usuario de vCenter |Detección/conmutación por error de VMware sin apagar la máquina virtual de origen |Asigne estos privilegios para el servidor vCenter:<br/><br/>Objeto de centro de datos > Propagar al objeto secundario, rol = solo lectura <br/><br/>El usuario se asigna en el nivel de centro de datos y, por lo tanto, tiene acceso a todos los objetos de este. Si quiere restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) a los objetos secundarios (hosts ESX, almacenes de datos, máquinas virtuales y redes). |
| Rol de usuario de vCenter |Conmutación por error y conmutación por recuperación |Asigne estos privilegios para el servidor vCenter:<br/><br/>Objeto de centro de datos –> Propagar a objeto secundario, rol = Azure_Site_Recovery<br/><br/>El usuario se asigna en el nivel de centro de datos y, por lo tanto, tiene acceso a todos los objetos de este.  Si quiere restringir el acceso, asigne el rol **Sin acceso** con **Propagate to child object** (Propagar a objeto secundario) al objeto secundario (hosts ESX, almacenes de datos, máquinas virtuales y redes). |

## <a name="third-party-software-notices-and-information"></a>Avisos e información de software de terceros
<!--Do Not Translate or Localize-->

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

## <a name="next-steps"></a>Pasos siguientes
[Obtenga más información sobre la conmutación por recuperación](site-recovery-failback-azure-to-vmware-classic.md) para que las máquinas a las que se realizó la conmutación por error y que se ejecutan en Azure vuelvan al entorno local.

