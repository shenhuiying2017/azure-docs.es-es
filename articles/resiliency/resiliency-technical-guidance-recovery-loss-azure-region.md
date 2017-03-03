---
title: "Guía técnica sobre resistencia en la recuperación ante la pérdida de una región de Azure | Microsoft Docs"
description: "Información y diseño de aplicaciones resistentes, con alta disponibilidad y con tolerancia a errores, y planeamiento de la recuperación ante desastres."
services: 
documentationcenter: na
author: adamglick
manager: saladki
editor: 
ms.assetid: f2f750aa-9305-487e-8c3f-1f8fbc06dc47
ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick
translationtype: Human Translation
ms.sourcegitcommit: 65fe1b29b55b58a160c8c1a3baed117530040c71
ms.openlocfilehash: 4d98ff207e08184fffd6dbfa93a018c1e208fa95
ms.lasthandoff: 02/17/2017


---
# <a name="azure-resiliency-technical-guidance-recovery-from-a-region-wide-service-disruption"></a>Guía técnica sobre resistencia en Azure: recuperación ante una interrupción del servicio en toda la región
Azure se divide física y lógicamente en unidades denominadas regiones. Cada región consta de uno o varios centros de datos muy cercanos entre si. Para consultar la lista actual de regiones, vea la [página Regiones de Azure](https://azure.microsoft.com/regions/).

En raras ocasiones es posible que no se pueda acceder a las instalaciones de toda una región debido, por ejemplo, a errores en la red. También existe la posibilidad de que las instalaciones se pierdan por completo, por ejemplo a causa de un desastre natural. En este artículo se explican las funcionalidades de Azure para crear aplicaciones que se distribuyen entre diferentes regiones. Este tipo de distribución ayuda a minimizar la posibilidad de que un error en una región pueda afectar a otras.

## <a name="cloud-services"></a>Servicios en la nube
### <a name="resource-management"></a>Administración de recursos
Las instancias de proceso se pueden distribuir entre las regiones creando un servicio en la nube independiente en cada región de destino y publicando luego el paquete de implementación en cada servicio en la nube. Sin embargo, la distribución del tráfico entre los servicios en la nube de distintas regiones debe implementarla el desarrollador de la aplicación, o bien con un servicio de administración de tráfico.

Determinar el número de instancias de rol de reserva que debe implementar de antemano para la recuperación ante desastres es un aspecto importante del planeamiento de la capacidad. La existencia de una implementación secundaria a escala completa garantiza que habrá capacidad disponible cuando sea necesario. Sin embargo, esto duplica el costo. Un patrón común consiste en tener una pequeña implementación secundaria que sea lo suficientemente grande como para ejecutar los servicios críticos. Esta pequeña implementación secundaria es recomendable tanto para tener capacidad reservada como para probar la configuración del entorno secundario.

> [!NOTE]
> La cuota de suscripción no es una garantía de capacidad. La cuota es simplemente un límite de crédito. Para garantizar la capacidad se tiene que definir el número necesario de roles en el modelo de servicio y dichos roles se tienen que implementar.
>
>

### <a name="load-balancing"></a>Equilibrio de carga
Para equilibrar la carga de tráfico entre regiones es necesaria una solución de administración de tráfico. Azure proporciona el [Administrador de tráfico de Azure](https://azure.microsoft.com/services/traffic-manager/). También puede utilizar los servicios de terceros que proporcionan funcionalidades de administración de tráfico similares.

### <a name="strategies"></a>Estrategias
Existen muchas estrategias alternativas para implementar el proceso distribuido entre regiones. Estas se deben adaptar a los requisitos empresariales específicos y a las circunstancias de la aplicación. En un nivel alto, los enfoques pueden dividirse en las siguientes categorías:

* **Volver a implementar en caso de desastre**. En este enfoque, la aplicación se vuelve a implementar desde cero en el momento del desastre. Esta reimplementación es adecuada para aplicaciones que no son críticas y que no requieren un tiempo de recuperación garantizado.
* **Reserva semiactiva (activa/pasiva)**. Se crea un servicio hospedado secundario en una región alternativa y se implementan roles para garantizar una capacidad mínima. Sin embargo, los roles no reciben tráfico de producción. Este enfoque es útil para las aplicaciones que no se han diseñado para distribuir el tráfico entre las regiones.
* **Reserva activa (activa/activa)**. La aplicación está diseñada para recibir la carga de producción en varias regiones. Los servicios en la nube de cada región pueden configurarse para una capacidad mayor de la necesaria para fines de recuperación ante desastres. Como alternativa, se podrían escalar horizontalmente los servicios en la nube según sea necesario en el momento de un desastre y una conmutación por error. Este enfoque requiere una inversión sustancial en el diseño de la aplicación, pero tiene ventajas significativas. Estas incluyen un tiempo de recuperación bajo y garantizado, la comprobación continua de todas las ubicaciones de recuperación y un uso eficiente de la capacidad.

Un análisis completo del diseño distribuido está fuera del ámbito de este artículo. Para más información, consulte [Recuperación ante desastres y alta disponibilidad para aplicaciones creadas en Microsoft Azure](https://aka.ms/drtechguide).

## <a name="virtual-machines"></a>Máquinas virtuales
La recuperación de máquinas virtuales (VMs) de infraestructura como servicio (IaaS) es similar en muchos aspectos a la recuperación de procesos de plataforma como servicio (PaaS). Sin embargo, hay diferencias importantes, ya que una máquina virtual de IaaS consta de la máquina virtual y del disco de esta.

* **Use Copia de seguridad de Azure para crear copias de seguridad entre regiones que sean coherentes con la aplicación**.
  [Copia de seguridad de Azure](https://azure.microsoft.com/services/backup/) permite a los clientes crear copias de seguridad coherentes con la aplicación en varios discos de máquinas virtuales y admite la replicación de tales copias entre regiones. Para realizar esta tarea, elija replicar geográficamente el almacén de copia de seguridad en el momento de la creación. La replicación del almacén de copia de seguridad se tiene que configurar en el momento de la creación. Más adelante no será posible establecerla. Si se pierde alguna región, Microsoft pone las copias de seguridad a disposición de los clientes. Los clientes pueden restaurarlas en cualquiera de los puntos de restauración configurados.
* **Separe el disco de datos del disco del sistema operativo**. Una consideración importante para las máquinas virtuales de IaaS es que no se puede cambiar el disco del sistema operativo sin volver a crear la máquina virtual. Este proceso no es un problema si la estrategia de recuperación es volver a realizar una implementación después de un desastre. Sin embargo, esto puede ser un problema si se utiliza el método de espera semiactiva para la capacidad reservada. Para realizar la implementación correctamente es preciso implementar el disco correcto del sistema operativo en las ubicaciones principal y secundaria, mientras que los datos de la aplicación se deben almacenar en una unidad independiente. Si es posible, utilice una configuración estándar de sistema operativo que se pueda proporcionar en ambas ubicaciones. Después de una conmutación por error, tiene que adjuntar la unidad de datos a las máquinas virtuales de IaaS existentes en el controlador de dominio secundario. Use AzCopy para copiar instantáneas de todos y cada uno de los discos de datos en un sitio remoto.
* **Tenga en cuenta los posibles problemas de coherencia después de una conmutación por error geográfica de varios discos de máquina virtual**. Cada disco de máquina virtual se implementa como una instancia de Azure Blob Storage y tiene la misma característica de replicación geográfica. A menos que utilice [Copia de seguridad de Azure](https://azure.microsoft.com/services/backup/) no hay garantía de coherencia entre los discos, porque la replicación geográfica es asincrónica y se replica de forma independiente. Se garantiza que los discos de máquinas virtuales individuales se encontrarán en un estado de coherencia frente a bloqueos después de una conmutación por error geográfica, pero no se garantiza la coherencia entre los diferentes discos. Esto puede provocar problemas en algunos casos (por ejemplo, en el caso de seccionamiento del disco).

## <a name="storage"></a>Almacenamiento
### <a name="recovery-by-using-geo-redundant-storage-of-blob-table-queue-and-vm-disk-storage"></a>Recuperación mediante el uso de almacenamiento con redundancia geográfica de blobs, tablas, colas y almacenamiento de discos de máquinas virtuales
En Azure, todos los blobs, tablas, colas y discos de máquinas virtuales se replican geográficamente de forma predeterminada. Esto se conoce como almacenamiento con redundancia geográfica. El almacenamiento con redundancia geográfica replica los datos de almacenamiento en un centro de datos emparejado que está a cientos de kilómetros dentro de una región geográfica concreta. El almacenamiento con redundancia geográfica está diseñado para proporcionar mayor durabilidad si se produce un desastre en un centro de datos principal. Microsoft controla cuándo se produce la conmutación por error y esta se limita a casos de desastres importantes en los que la ubicación principal original se considera irrecuperable en un período de tiempo razonable. En algunos escenarios este periodo puede ser de varios días. Normalmente, los datos se replican en unos minutos, aunque el acuerdo de nivel de servicio aún no cubre el intervalo de sincronización.

En caso de una conmutación por error geográfica, no cambiará la forma en la que se accede a la cuenta (la dirección URL y la clave de la cuenta no cambiarán). La cuenta de almacenamiento, sin embargo, estará en una región distinta después de la conmutación por error. Esto puede afectar a las aplicaciones que requieren afinidad regional con su cuenta de almacenamiento. Hasta para los servicios y aplicaciones que no requieran una cuenta de almacenamiento en el mismo centro de datos, los gastos de ancho de banda y latencia entre centros de datos pueden ser razones de peso para mover el tráfico temporalmente a la región de conmutación por error. Esto puede tenerse en cuenta a la hora de crear una estrategia general de recuperación ante desastres.

Además de la conmutación por error automática que proporciona el almacenamiento con redundancia geográfica, Azure ha introducido un servicio que proporciona acceso de lectura a la copia de los datos en la ubicación de almacenamiento secundaria. Este se denomina almacenamiento con redundancia geográfica con acceso de lectura.

Para más información acerca del almacenamiento con redundancia geográfica y del almacenamiento con redundancia geográfica con acceso de lectura, consulte [Replicación de Azure Storage](../storage/storage-redundancy.md).

### <a name="geo-replication-region-mappings"></a>Asignaciones de regiones de replicación geográfica
Es importante saber dónde se replican geográficamente los datos para saber dónde implementar las otras instancias de los datos que requieren afinidad regional con el almacenamiento. La siguiente tabla muestra los emparejamientos de las ubicaciones principal y secundaria.

[!INCLUDE [paired-region-list](../../includes/paired-region-list.md)]

### <a name="geo-replication-pricing"></a>Precios de la replicación geográfica
La replicación geográfica se incluye en los precios actuales de Azure Storage como almacenamiento con redundancia geográfica. Si no desea que los datos se repliquen geográficamente, puede deshabilitar la replicación geográfica de su cuenta. Esto se denomina almacenamiento con redundancia local y se cobra a un precio reducido, en comparación con el almacenamiento con redundancia geográfica.

### <a name="determine-if-a-geo-failover-occurred"></a>Determinación si se ha producido una conmutación por error geográfica
Si se produce una conmutación por error geográfica, el error se publicará en el [panel de estado de Azure](https://azure.microsoft.com/status/). Sin embargo, las aplicaciones pueden implementar una forma automática de detectar el error mediante la supervisión de la región geográfica de su cuenta de almacenamiento. Esta detección automática se puede utilizar para desencadenar otras operaciones de recuperación como la activación de recursos de proceso en la región geográfica a la que se movió el almacenamiento. Puede realizar una consulta para este evento mediante Service Management API, para lo que debe usar [Get Storage Account Properties](https://msdn.microsoft.com/library/ee460802.aspx). Las propiedades pertinentes son:

    <GeoPrimaryRegion>primary-region</GeoPrimaryRegion>
    <StatusOfPrimary>[Available|Unavailable]</StatusOfPrimary>
    <LastGeoFailoverTime>DateTime</LastGeoFailoverTime>
    <GeoSecondaryRegion>secondary-region</GeoSecondaryRegion>
    <StatusOfSecondary>[Available|Unavailable]</StatusOfSecondary>

### <a name="vm-disks-and-geo-failover"></a>Discos de máquinas virtuales y conmutación por error geográfica
Como se ha descrito en la sección "Máquinas virtuales" de este mismo artículo, después de una conmutación por error no se puede garantizarla coherencia de datos entre los discos de máquina virtual. Para garantizar la exactitud de las copias de seguridad, se debe utilizar un producto como System Center Data Protection Manager para realizar una copia de seguridad y restaurar los datos de la aplicación.

## <a name="database"></a>Base de datos
### <a name="azure-sql-database"></a>Base de datos SQL de Azure
Base de datos SQL de Azure proporciona dos tipos de recuperación, la restauración geográfica y la replicación geográfica activa.

#### <a name="geo-restore"></a>Restauración geográfica
La funcionalidad de [restauración geográfica](../sql-database/sql-database-recovery-using-backups.md#geo-restore) también está disponible en las bases de datos básicas, estándares y premium. Esta restauración proporciona la opción de recuperación predeterminada cuando la base de datos no está disponible debido a una incidencia en la región en la que se hospeda la base de datos. Al igual que la restauración a un momento dado, la restauración geográfica se basa en copias de seguridad de las bases de datos de Azure Storage con redundancia geográfica. Realiza la restauración a partir de la copia de seguridad de replicación geográfica. Por lo tanto es resistente a las interrupciones de almacenamiento en la región principal. Para más información, consulte [Restauración de una base de datos SQL de Azure o una conmutación por error en una secundaria](../sql-database/sql-database-disaster-recovery.md).

#### <a name="active-geo-replication"></a>Replicación geográfica activa
[Replicación geográfica activa](../sql-database/sql-database-geo-replication-overview.md) está disponible para todos los niveles de base de datos. Está diseñada para aplicaciones que tienen unos requisitos de recuperación más exigentes que los que puede ofrecer la restauración geográfica. Mediante la replicación geográfica activa, se pueden crear hasta cuatro bases de datos secundarias legibles en servidores situados en regiones diferentes. Puede iniciar la conmutación por error a cualquiera de las bases de datos secundarias. Además, la replicación geográfica activa puede utilizarse para los escenarios de actualización o reubicación de la aplicación, así como para el equilibrio de cargas de trabajo de solo lectura. Para más información, consulte el artículo sobre cómo [Configuración de replicación geográfica para Azure SQL Database con Azure Portal](../sql-database/sql-database-geo-replication-portal.md) e [Inicio de una conmutación por error planeada o no planeada para Azure SQL Database con Azure Portal](../sql-database/sql-database-geo-replication-failover-portal.md). Para obtener información sobre el diseño e implementación de aplicaciones y la actualización de aplicaciones sin que se produzca un tiempo de inactividad, Consulte [Diseño de aplicaciones para la recuperación ante desastres en la nube mediante replicación geográfica activa en SQL Database](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md) y [Administración de actualizaciones graduales de aplicaciones en la nube mediante la replicación geográfica activa de SQL Database](../sql-database/sql-database-manage-application-rolling-upgrade.md).

### <a name="sql-server-on-azure-virtual-machines"></a>SQL Server en Máquinas virtuales de Azure
Hay varias opciones disponibles para la recuperación y alta disponibilidad para SQL Server 2012 (y las versiones posteriores) que se ejecutan en la implementación de Azure Virtual Machines. Para más información, consulte [Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md).

## <a name="other-azure-platform-services"></a>Otros servicios de la plataforma de Azure
Al intentar ejecutar el servicio en la nube en varias regiones de Azure, debe tener en cuenta las implicaciones de cada una de las dependencias. En las secciones siguientes, la guía específica del servicio supone que debe usar el mismo servicio de Azure en un centro de datos de Azure alternativo. Esto implica tareas de configuración y de replicación de datos.

> [!NOTE]
> En algunos casos, estos pasos pueden ayudar a mitigar una interrupción específica del servicio en lugar de un evento de centro de datos completo. Desde la perspectiva de la aplicación, una interrupción específica del servicio puede ser igual de restrictiva y requerir la migración temporal del servicio a otra región de Azure.
>
>

### <a name="azure-service-bus"></a>Azure Service Bus
Azure Service Bus usa un espacio de nombres único que no abarca varias regiones de Azure. Por tanto, el primer requisito es configurar los espacios de nombres necesarios de Service Bus en la región alternativa. Sin embargo, también hay consideraciones sobre la durabilidad de los mensajes en cola. Existen varias estrategias para replicar mensajes entre regiones de Azure. Si desea información detallada sobre estas estrategias de replicación y otras estrategias de recuperación ante desastres, consulte [Procedimientos recomendados para aislar aplicaciones ante desastres e interrupciones de Service Bus](../service-bus-messaging/service-bus-outages-disasters.md). Para otras consideraciones sobre disponibilidad, consulte [Bus de servicio (disponibilidad)](resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

### <a name="azure-app-service"></a>Servicio de aplicaciones de Azure
Para migrar una aplicación de Azure App Service, como Web Apps o Mobile Apps, a una región de Azure secundaria, debe tener una copia de seguridad del sitio web disponible para su publicación. Si la interrupción no afecta al centro de datos de Azure completo, es posible utilizar una FTP para descargar una copia de seguridad reciente del contenido del sitio. A continuación, cree una nueva aplicación web en la región alternativa, a menos que ya lo haya hecho previamente para reservar capacidad. Publique el sitio en la nueva región y realice los cambios de configuración necesarios. Estos cambios pueden incluir cadenas de conexión de base de datos u otros valores específicos de la región. Si es necesario, agregue el certificado SSL del sitio y cambie el registro DNS CNAME, con el fin de que el nombre de dominio personalizado señale a la dirección URL de Web Apps reimplementada.

### <a name="azure-hdinsight"></a>HDInsight de Azure
De manera predeterminada los datos asociados a Azure HDInsight se almacenan en Azure Blob Storage. HDInsight requiere que un clúster de Hadoop que esté procesando trabajos de MapReduce esté ubicado en la misma región que la cuenta de almacenamiento que contiene los datos analizados. Si utiliza la característica de replicación geográfica disponible en Azure Storage y la región principal no está disponible, puede acceder a los datos de la región secundaria en la que estos se replicaron. Puede crear un clúster de Hadoop en la región en la que se han replicado los datos y seguir procesándolo. Para otras consideraciones sobre disponibilidad, consulte [HDInsight (disponibilidad)](resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

### <a name="sql-reporting"></a>SQL Reporting
En la actualidad, la recuperación de la pérdida de una región de Azure requiere varias instancias de SQL Reporting en diferentes regiones de Azure. Estas instancias de SQL Reporting deben tener acceso a los mismos datos, y estos deben tener su propio plan de recuperación si se produce un desastre. También puede mantener copias de seguridad externas del archivo RDL para cada informe.

### <a name="azure-media-services"></a>Servicios multimedia de Azure
Los Servicios multimedia de Azure tienen un enfoque de recuperación diferente para la codificación y el streaming. Normalmente, el streaming es más importante durante un apagón regional. Para prepararse, es preciso tener una cuenta de Media Services en dos regiones distintas de Azure. El contenido codificado debe estar ubicado en ambas regiones. Durante un error, puede redirigir el tráfico de streaming a la región alternativa. La codificación puede realizarse en cualquier región de Azure. Si la codificación está sujeta a limitación temporal como, por ejemplo, durante el procesamiento de eventos en directo, debe estar preparado para enviar trabajos a un centro de datos alternativo durante los errores.

### <a name="virtual-network"></a>Red virtual
Los archivos de configuración proporcionan la manera más rápida de configurar una red virtual en otra región de Azure. Después de configurar la red virtual en la región principal de Azure, [exporte la configuración de red virtual](../virtual-network/virtual-networks-create-vnet-classic-portal.md) para la red actual a un archivo de configuración de red. Si se produce una interrupción en la región principal, [restaure la red virtual](../virtual-network/virtual-networks-create-vnet-classic-portal.md) a partir del archivo de configuración almacenado. Después, configure otros servicios en la nube, máquinas virtuales o configuración entre locales para que funcionen con la nueva red virtual.

## <a name="checklists-for-disaster-recovery"></a>Listas de comprobación para la recuperación ante desastres.
## <a name="cloud-services-checklist"></a>Lista de comprobación de Cloud services
1. Revise la sección "Cloud services" de este documento.
2. Cree una estrategia de recuperación ante desastres entre regiones.
3. Comprenda las ventajas e inconvenientes de reservar capacidad en regiones alternativas.
4. Use herramientas de enrutamiento de tráfico, como el Administrador de tráfico de Azure.

## <a name="virtual-machines-checklist"></a>Lista de comprobación de Virtual Machines
1. Revise la sección "Virtual Machines" de este artículo.
2. Utilice [Copia de seguridad de Azure](https://azure.microsoft.com/services/backup/) para crear copias de seguridad de la aplicación coherentes entre regiones.

## <a name="storage-checklist"></a>Lista de comprobación de almacenamiento
1. Revise la sección "Storage" de este artículo.
2. No deshabilite la replicación geográfica de los recursos de almacenamiento.
3. Conozca la región alternativa para la replicación geográfica si se produce una conmutación por error.
4. Cree estrategias de copia de seguridad personalizadas para las estrategias de conmutación por error controladas por el usuario.

## <a name="azure-sql-database-checklist"></a>Lista de comprobación de Azure SQL Database
1. Revise la sección "Azure SQL Database" de este artículo.
2. Utilice la [restauración geográfica](../sql-database/sql-database-recovery-using-backups.md#geo-restore) o la [replicación geográfica activa](../sql-database/sql-database-geo-replication-overview.md), según corresponda.

## <a name="sql-server-on-azure-virtual-machines-checklist"></a>Lista de comprobación de SQL Server en Azure Virtual Machines
1. Revise la sección "SQL Server en Azure Virtual Machines" de este artículo.
2. Use grupos de disponibilidad AlwaysOn entre regiones o creación de reflejo de base de datos.
3. También puede utilizar la copia de seguridad y restauración en Blob Storage.

## <a name="service-bus-checklist"></a>Lista de comprobación del Bus de servicio
1. Revise la sección "Azure Service Bus" de este artículo.
2. Configure un espacio de nombres del Bus de servicio en una región alternativa.
3. Considere las estrategias de replicación personalizadas para los mensajes entre regiones.

## <a name="app-service-checklist"></a>Lista de comprobación de App Service
1. Revise la sección "Azure App Service" de este artículo.
2. Mantenga copias de seguridad del sitio fuera de la región primaria.
3. Si la interrupción es parcial, intente recuperar el sitio actual con FTP.
4. Planee la implementación del sitio en un sitio web nuevo o ya existente en una región alternativa.
5. Planee los cambios en la configuración de los registros de la aplicación y los de DNS CNAME.

## <a name="hdinsight-checklist"></a>Lista de comprobación de HDInsight
1. Revise la sección "Azure HDInsight" de este artículo.
2. Cree un clúster de Hadoop en la región con los datos replicados.

## <a name="sql-reporting-checklist"></a>Lista de comprobación de SQL Reporting
1. Revise la sección "SQL Reporting" de este artículo.
2. Disponga de una instancia alternativa de SQL Reporting en una región diferente.
3. Mantenga un plan independiente para replicar el destino en esa región.

## <a name="media-services-checklist"></a>Lista de comprobación de Servicios multimedia
1. Revise la sección "Azure Media Services" de este artículo.
2. Cree una cuenta de Servicios multimedia en una región alternativa.
3. Codifique el mismo contenido en ambas regiones para que admitan la conmutación por error del streaming.
4. Si se produce una interrupción del servicio, envíe los trabajos de codificación a otra región.

## <a name="virtual-network-checklist"></a>Lista de comprobación de Virtual Network
1. Revise la sección "Virtual Network" de este artículo.
2. Use la configuración exportada de la red virtual para volver a crearla en otra región.

## <a name="next-steps"></a>Pasos siguientes
Este artículo forma parte de una serie que se centra en la [Guía técnica sobre resistencia en Azure](resiliency-technical-guidance.md). El siguiente artículo de esta serie se centra en la [recuperación desde un centro de datos local a Azure](resiliency-technical-guidance-recovery-on-premises-azure.md).

