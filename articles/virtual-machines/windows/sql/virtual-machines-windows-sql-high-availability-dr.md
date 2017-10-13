---
title: "Alta disponibilidad y recuperación ante desastres para SQL Server | Microsoft Docs"
description: "Un análisis de los diversos tipos de estrategias HADR de SQL Server en ejecución en máquinas virtuales de Azure."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: mikeray
ms.openlocfilehash: a81b956107ef82f40ad5304808068a7573ca7d27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-and-disaster-recovery-for-sql-server-in-azure-virtual-machines"></a>Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure

Las máquinas virtuales de Microsoft Azure con SQL Server pueden ayudar a reducir el costo de una solución de base de datos de alta disponibilidad y recuperación ante desastres (HADR). La mayoría de las soluciones HADR de SQL Server son compatibles con las máquinas virtuales de Azure, bien como soluciones exclusivas de Azure o híbridas. En una solución exclusiva de Azure, todo el sistema HADR se ejecuta en Azure. En una configuración híbrida, una parte de la solución se ejecuta en Azure y la otra parte se ejecuta localmente en su organización. La flexibilidad del entorno Azure permite migrar total o parcialmente a Azure a fin de satisfacer los requisitos de presupuesto y HADR de sus sistemas de bases de datos de SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="understanding-the-need-for-an-hadr-solution"></a>Descripción de la necesidad de una solución HADR
Depende de usted el garantizar que su sistema de base de datos cuenta con las capacidades HADR que exige el contrato de nivel de servicio (SLA). El hecho de que Azure proporcione mecanismos de alta disponibilidad, como la recuperación del servicio en los servicios en la nube y la detección de recuperación de errores para las máquinas virtuales, no garantiza por sí solo que pueda cumplir con el Acuerdo de Nivel de Servicio deseado. Estos mecanismos protegen la alta disponibilidad de las máquinas virtuales, pero no la alta disponibilidad del SQL Server que se ejecutan en ellas. Es posible que la instancia de SQL Server no funcione a pesar de que la máquina virtual esté en línea y en buen estado. Además, incluso con los mecanismos de alta disponibilidad proporcionados por Azure, es posible que se produzcan tiempos de inactividad de las máquinas virtuales debidos a eventos como la recuperación errores de software o hardware o las actualizaciones del sistema operativo.

Además, el almacenamiento con redundancia geográfica (GRS) de Azure, que se implementa con una característica llamada replicación geográfica, podría no ser una solución para recuperación ante desastres adecuada para sus bases de datos. Dado que la replicación geográfica envía los datos de forma asincrónica, las actualizaciones recientes se pueden perder en caso de desastre. En la sección [La replicación geográfica no se admite para los archivos de datos y de registro en discos independientes](#geo-replication-support) se ofrece más información relativa a las limitaciones de la replicación geográfica.

## <a name="hadr-deployment-architectures"></a>Arquitecturas de implementación HADR
Entre las tecnologías HADR de SQL Server compatibles con Azure se incluyen:

* [Grupos de disponibilidad AlwaysOn (SQL Server)](https://technet.microsoft.com/library/hh510230.aspx)
* [Instancias de clúster de conmutación por error de AlwaysOn (SQL Server)](https://technet.microsoft.com/library/ms189134.aspx)
* [Trasvase de registros](https://technet.microsoft.com/library/ms187103.aspx)
* [Copia de seguridad y restauración de SQL Server con el servicio Azure Blob Storage](https://msdn.microsoft.com/library/jj919148.aspx)
* [Creación de reflejo de la base de datos](https://technet.microsoft.com/library/ms189852.aspx): en desuso en SQL Server 2016

Es posible combinar las tecnologías para implementar una solución SQL Server que posea alta disponibilidad y, al mismo tiempo, capacidades de recuperación ante desastres. Según la tecnología que se use, una implementación híbrida puede requerir un túnel VPN con la red virtual de Azure. Las secciones siguientes muestran algunas de las arquitecturas de implementación de ejemplo.

## <a name="azure-only-high-availability-solutions"></a>Exclusiva de Azure: soluciones de alta disponibilidad

Puede tener una solución de alta disponibilidad para SQL Server en un nivel de base de datos con grupos de disponibilidad Always On (denominados grupos de disponibilidad). También puede crear una solución de alta disponibilidad en un nivel de instancia con instancias de clúster de conmutación por error Always On (instancias del clúster de conmutación por error). Si desea obtener redundancia adicional, puede crear redundancia en ambos niveles creando de grupos de disponibilidad en instancias de clúster de conmutación por error. 

| Technology | Arquitecturas de ejemplo |
| --- | --- |
| **Grupos de disponibilidad** |Todas las réplicas de disponibilidad que se ejecutan en Azure Virtual Machines de la misma región proporcionan alta disponibilidad. Debe configurar una máquina virtual de controlador de dominio, ya que los clústeres de conmutación por error de Windows requieren un dominio de Active Directory.<br/> ![Grupos de disponibilidad](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Para más información, vea [Configuración de grupos de disponibilidad en Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). |
| **Instancias de clúster de conmutación por error** |Las instancias de clúster de conmutación por error (FCI), que requieren almacenamiento compartido, se pueden crear de tres maneras distintas.<br/><br/>1. Un clúster de conmutación por error de dos nodos que se ejecuta en Azure Virtual Machines con almacenamiento conectado con [Espacios de almacenamiento directo de Windows Server 2016 \(S2D\)](virtual-machines-windows-portal-sql-create-failover-cluster.md) para ofrecer una SAN virtual basada en software.<br/><br/>2. Un clúster de conmutación por error de dos nodos que se ejecuta en Azure Virtual Machines con almacenamiento posibilitado por una solución de clústeres de terceros. Si desea obtener un ejemplo específico con SIOS DataKeeper, vea [High availability for a file share using failover clustering and 3rd party software SIOS Datakeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/) (Alta disponibilidad de un recurso compartido de archivos con clúster de conmutación por error y el software de terceros SIOS DataKeeper).<br/><br/>3. Un clúster de conmutación por error de dos nodos que se ejecuta en Azure Virtual Machines con almacenamiento en bloque compartido de destino iSCSI remoto a través de ExpressRoute. Por ejemplo, NetApp Private Storage (NPS) expone un destino iSCSI a través de ExpressRoute con Equinix a las máquinas virtuales de Azure.<br/><br/>Para las soluciones de almacenamiento compartido y de replicación de datos de terceros, debe ponerse en contacto con el proveedor en caso de que surja cualquier problema relacionado con el acceso a datos en la conmutación por error.<br/><br/>Tenga en cuenta que todavía no se admite el uso de FCI basado en el [Almacenamiento de archivos de Azure](https://azure.microsoft.com/services/storage/files/) , porque esta solución no utiliza Almacenamiento premium. Trabajamos para que pronto sea compatible. |

## <a name="azure-only-disaster-recovery-solutions"></a>Exclusiva de Azure: soluciones de recuperación ante desastres
Puede tener una solución de recuperación ante desastres para las bases de datos de SQL Server en Azure con grupos de disponibilidad, creación de reflejo de bases de datos o copias de seguridad y restauración con blobs de almacenamiento.

| Technology | Arquitecturas de ejemplo |
| --- | --- |
| **Grupos de disponibilidad** |Réplicas de disponibilidad que se ejecutan en varios centros de datos en máquinas virtuales de Azure para la recuperación ante desastres. Esta solución entre regiones protege frente a interrupciones en todo el sitio. <br/> ![Grupos de disponibilidad](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>Dentro de una región, todas las réplicas deben estar en el mismo servicio en la nube y la misma red virtual. Dado que cada región tendrá una red virtual distinta, estas soluciones precisan de conectividad entre estas redes. Para obtener más información, consulte [Configuración de una conexión de red virtual a red virtual mediante Azure Portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md). Para obtener instrucciones detalladas, vea [Configuración de un grupo de disponibilidad en máquinas virtuales de Azure en distintas regiones](virtual-machines-windows-portal-sql-availability-group-dr.md).|
| **Creación de reflejo de la base de datos** |Los servidores principal y de reflejo se ejecutan en distintos centros de datos para la recuperación ante desastres. Debe realizar la implementación con certificados de servidor porque un dominio de Active Directory no puede abarcar varios centros de datos.<br/>![Creación de reflejo de la base de datos](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif) |
| **Copia de seguridad y restauración con el servicio Almacenamiento de blobs de Azure** |Las bases de datos de producción de las que se realizó una copia de seguridad directamente en el almacenamiento de blobs en otro centro de datos para la recuperación ante desastres.<br/>![Copia de seguridad y restauración](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Para más información, consulte [Copias de seguridad y restauración para SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-backup-recovery.md). |
| **Replicación y conmutación por error de SQL Server en Azure con Azure Site Recovery** |SQL Server de producción de un centro de datos de Azure replicado directamente en una instancia de Azure Storage de un centro de datos de Azure distinto para la recuperación ante desastres.<br/>![Replicación con Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_standalone_sqlserver-asr.png)<br/>Para obtener más información, consulte [Proteger SQL Server con la recuperación ante desastres de SQL Server y Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |


## <a name="hybrid-it-disaster-recovery-solutions"></a>TI híbrida: soluciones de recuperación ante desastres
Puede tener una solución de recuperación ante desastres para las bases de datos de SQL Server en un entorno de TI híbrida con grupos de disponibilidad, creación de reflejo de la base de datos, trasvase de registros y copias de seguridad y restauración con Azure Blob Storage.

| Technology | Arquitecturas de ejemplo |
| --- | --- |
| **Grupos de disponibilidad** |Algunas réplicas de disponibilidad se ejecutan en máquinas virtuales de Azure y réplicas se ejecutan localmente para la recuperación ante desastres a través de sitios. El sitio de producción puede ser local o encontrarse en un centro de datos de Azure.<br/>![Grupos de disponibilidad](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Dado que todas las réplicas de disponibilidad deben estar en el mismo clúster de conmutación por error, este debe abarcar ambas redes (un clúster de conmutación por error de varias subredes). Esta configuración requiere una conexión VPN entre Azure y la red local.<br/><br/>Para que se produzca una recuperación ante desastres correcta de las bases de datos, también debe instalar un controlador de dominio de réplica en el sitio de recuperación ante desastres.<br/><br/>Es posible usar el Asistente para agregar una réplica en SSMS para agregar una réplica de Azure a un grupo de disponibilidad AlwaysOn existente. Para más información, consulte el tutorial: Ampliación del grupo de disponibilidad AlwaysOn a Azure. |
| **Creación de reflejo de la base de datos** |Un asociado se ejecuta en una máquina virtual de Azure y otro localmente para la recuperación ante desastres a través de sitios con certificados de servidor. Los asociados no necesitan estar en el mismo dominio de Active Directory y no se requiere ninguna conexión VPN.<br/>![Creación de reflejo de la base de datos](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Otro escenario de creación de reflejo de la base de datos implica a un asociado que se ejecuta en una máquina virtual de Azure y los demás que se ejecutan localmente en el mismo dominio de Active Directory para la recuperación ante desastres entre sitios. Se requiere una [conexión VPN entre la red virtual de Azure y la red local](../../../vpn-gateway/vpn-gateway-site-to-site-create.md).<br/><br/>Para que se produzca una recuperación ante desastres correcta de las bases de datos, también debe instalar un controlador de dominio de réplica en el sitio de recuperación ante desastres. |
| **Trasvase de registros** |Un servidor se ejecuta en una máquina virtual de Azure y  otro localmente para la recuperación ante desastres a través de sitios. El trasvase de registros depende del uso compartido de archivos de Windows, de modo que se requiere una conexión VPN entre la red virtual de Azure y la red local.<br/>![Trasvase de registros](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>Para que se produzca una recuperación ante desastres correcta de las bases de datos, también debe instalar un controlador de dominio de réplica en el sitio de recuperación ante desastres. |
| **Copia de seguridad y restauración con el servicio Almacenamiento de blobs de Azure** |Bases de datos de producción locales con copia de seguridad directa en el almacenamiento de blobs de Azure para la recuperación ante desastres.<br/>![Copia de seguridad y restauración](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Para más información, consulte [Copias de seguridad y restauración para SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-backup-recovery.md). |
| **Replicación y conmutación por error de SQL Server en Azure con Azure Site Recovery** |SQL Server de producción local replicado directamente en una instancia de Azure Storage para la recuperación ante desastres.<br/>![Replicación con Azure Site Recovery](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_standalone_sqlserver-asr.png)<br/>Para obtener más información, consulte [Proteger SQL Server con la recuperación ante desastres de SQL Server y Azure Site Recovery](../../../site-recovery/site-recovery-sql.md). |

## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Consideraciones importantes para HADR de SQL Server en Azure
Las máquinas virtuales de Azure, el almacenamiento y la conexión de red tienen características operativas diferentes de las de una infraestructura TI local y no virtualizada. Para implementar correctamente una solución HADR de SQL Server en Azure es necesario conocer estas diferencias y diseñar la solución adaptada a ellas.

### <a name="high-availability-nodes-in-an-availability-set"></a>Nodos de alta disponibilidad en un conjunto de disponibilidad
Los conjuntos de disponibilidad de Azure permiten colocar los nodos de alta disponibilidad en dominios de error y dominios de actualización independientes. Para que las máquinas virtuales de Azure se coloquen en el mismo conjunto de disponibilidad, debe implementarlas en el mismo servicio en la nube. Tenga en cuenta que solo las máquinas virtuales del mismo servicio en la nube puede participar en el mismo conjunto de disponibilidad. Para obtener más información, consulte [Administración de la disponibilidad de las máquinas virtuales](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="failover-cluster-behavior-in-azure-networking"></a>Comportamiento de clúster de conmutación por error en redes de Azure
El servicio DHCP no compatible con RFC en Azure puede hacer que la creación de determinadas configuraciones de clúster de conmutación por error produzcan errores al asignarle una dirección IP duplicada al nombre de red del clúster como, por ejemplo, la misma dirección IP que uno de los nodos de clúster. Se trata de un problema al implementar grupos de disponibilidad que depende de la característica de clúster de conmutación por error de Windows.

Considere un escenario en el que se crea un clúster de dos nodos y se pone en conexión:

1. El clúster se conecta y NODE1 solicita una dirección IP asignada dinámicamente para el nombre de red en clúster.
2. El servicio DHCP no otorga ninguna dirección IP a excepción de la propia de NODE1, ya que el servicio DHCP reconoce que la solicitud procede del propio NODE1.
3. Windows detecta que se asigna una dirección duplicada tanto a NODE1 como al nombre de red en clúster de conmutación por error y el grupo de clústeres predeterminado no puede poner en línea.
4. El grupo de clústeres predeterminado se pasa a NODE2, que trata la dirección IP de NODE1 como la dirección IP del clúster y pone en línea el grupo de clústeres predeterminado.
5. Cuando NODE2 intenta establecer conectividad con NODE1, los paquetes dirigidos a NODE1 nunca abandonan NODE2 porque resuelve la dirección IP de NODE1 en sí mismo. NODE2 no puede establecer conectividad con NODE1, pierde el cuórum y cierra el clúster.
6. Mientras tanto, NODE1 puede enviar paquetes a NODE2 pero NODE2 no puede responder. Node1 pierde el cuórum y cierra el clúster.

Puede evitar esta situación asignando una dirección IP estática no usada, por ejemplo una dirección IP de vínculo local como 169.254.1.1, al nombre de red del clúster a fin de poner dicho nombre de red en línea. Para simplificar este proceso, vea [Configuración del clúster de conmutación por error de Windows en Azure para grupos de disponibilidad](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Para más información, vea [Configuración de grupos de disponibilidad en Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

### <a name="availability-group-listener-support"></a>Compatibilidad del agente de escucha del grupo de disponibilidad
Los agentes de escucha del grupo de disponibilidad son compatibles con máquinas virtuales de Azure que ejecutan Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 y Windows Server 2016. Esta compatibilidad es posible gracias al uso de puntos de conexión de carga equilibrada habilitados en las máquinas virtuales de Azure y que son nodos del grupo de disponibilidad. Debe seguir pasos de configuración especiales para que los agentes de escucha funcionen tanto con las aplicaciones de cliente que se ejecutan en Azure como con las que se ejecutan localmente.

Existen dos opciones principales para configurar el agente de escucha: externa (público) o interna. El agente de escucha externo (público) usa un equilibrador de carga accesible desde Internet y está asociado a una IP virtual pública (VIP) que es accesible a través de Internet. Un agente de escucha interno usa un equilibrador de carga interno y solo admite clientes dentro del mismo servicio Virtual Network. Para cada tipo de equilibrador de carga, debe habilitar Direct Server Return. 

Si el grupo de disponibilidad abarca varias subredes de Azure (como por ejemplo, una implementación que comprenda varias regiones de Azure), la cadena de conexión de cliente debe incluir "**MultisubnetFailover = True**". Esto se traduce en intentos de conexión en paralelo a las réplicas de las diferentes subredes. Para obtener instrucciones sobre cómo configurar un agente de escucha, consulte

* [Configuración de un agente de escucha con ILB para grupos de disponibilidad en Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md).
* [Configuración de un agente de escucha externo para grupos de disponibilidad en Azure](../classic/ps-sql-ext-listener.md).

Puede seguir conectándose a cada réplica de disponibilidad por separado conectándose directamente a la instancia del servicio. Además, puesto que los grupos de disponibilidad son compatibles con las versiones anteriores de los clientes de creación de reflejo de la base de datos, puede conectarse a las réplicas de disponibilidad como asociados de creación de reflejo de la base de datos siempre y cuando las réplicas estén configuradas de forma similar a la creación de reflejo de la base de datos:

* Una réplica principal y una réplica secundaria
* La réplica secundaria está configurada como no legible (la opción **Secundaria legible** está establecida en **No**).

A continuación se muestra una cadena de conexión de cliente de ejemplo correspondiente a esta configuración similar a la creación de reflejo de la base de datos que usa ADO.NET o SQL Server Native Client:

    Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Para obtener más información sobre la conectividad del cliente, consulte:

* [Usar palabras clave de cadena de conexión con SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
* [Conectar clientes a una sesión de creación de reflejo de la base de datos (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [Conexión con el agente de escucha del grupo de disponibilidad en TI híbrida](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
* [Agentes de escucha del grupo de disponibilidad, conectividad de cliente y conmutación por error de una aplicación (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [Uso de cadenas de conexión de creación de reflejo de la base de datos con grupos de disponibilidad](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>Latencia de red en TI híbrida
Es recomendable implementar la solución HADR partiendo de la suposición de que podría haber períodos de tiempo con una alta latencia de red entre la red local y Azure. Al implementar réplicas en Azure, debe usar la confirmación asincrónica en lugar de la confirmación sincrónica para el modo de sincronización. Al implementar servidores de creación de reflejo de la base de datos tanto en local como en Azure, use el modo de alto rendimiento en lugar del modo de alta seguridad.

### <a name="geo-replication-support"></a>Compatibilidad de la replicación geográfica
La replicación geográfica en discos de Azure no admite que el archivo de datos y el archivo de registro de la misma base de datos se almacenen en discos independientes. La GRS replica los cambios en cada disco independiente y asincrónicamente. Este mecanismo garantiza el orden de escritura en un único disco en la copia con replicación geográfica pero no a través de las copias con replicación geográfica de varios discos. Si configura una base de datos para almacenar su archivo de datos y su archivo de registro en discos independientes, los discos recuperados después de un desastre pueden contener una copia más actualizada del archivo de datos que el archivo de registro, lo que interrumpe el registro de escritura previa en SQL Server y las propiedades ACID de las transacciones. Si no tiene la opción de deshabilitar la replicación geográfica en la cuenta de almacenamiento, debe conservar todos los archivos de datos y de registro de una base de datos dada en el mismo disco. Si debe usar más de un disco debido al tamaño de la base de datos, debe implementar una de las soluciones de recuperación de desastres enumeradas anteriormente para garantizar la redundancia de datos.

## <a name="next-steps"></a>Pasos siguientes
Si necesita crear una máquina virtual de Azure con SQL Server, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-windows-portal-sql-server-provision.md).

Para obtener el mejor rendimiento de SQL Server en una máquina virtual de Azure, consulte la guía en [Procedimientos recomendados para SQL Server en Máquinas virtuales de Azure](virtual-machines-windows-sql-performance.md).

Para ver otros temas sobre la ejecución de SQL Server en máquinas virtuales de Azure, consulte [SQL Server en máquinas virtuales de Azure](virtual-machines-windows-sql-server-iaas-overview.md).

### <a name="other-resources"></a>Otros recursos:
* [Instalación de un nuevo bosque de Active Directory en Azure](../../../active-directory/active-directory-new-forest-virtual-machine.md)
* [Creación del clúster de conmutación por error para grupos de disponibilidad en la VM de Azure](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

