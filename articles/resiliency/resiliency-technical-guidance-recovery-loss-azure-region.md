<properties
   pageTitle="Guía técnica sobre resistencia en la recuperación ante la pérdida de una región de Azure | Microsoft Azure"
   description="Artículo para entender y diseñar aplicaciones resistentes, con alta disponibilidad y con tolerancia a errores y para planear la recuperación ante desastres."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="patw;jroth;aglick"/>

#Guía técnica sobre resistencia en Azure: recuperación ante una interrupción del servicio en toda la región de Azure

Azure se divide física y lógicamente en unidades denominadas regiones. Una región consta de uno o varios centros de datos muy cercanos. En el momento de redactar este artículo, Azure tiene veinticuatro regiones en todo el mundo. En raras ocasiones es posible que no se pueda acceder a las instalaciones de toda una región debido, por ejemplo, a errores en la red, o que estas se pierdan por completo por causa, por ejemplo, de desastres naturales. Esta sección explica las funcionalidades de Azure para crear aplicaciones que se distribuyen entre regiones. Las regiones están diseñadas para minimizar la posibilidad de que un error en una región pueda afectar a otras.

##Servicios en la nube

###Administración de recursos
La distribución de instancias de proceso entre las regiones se consigue mediante la creación de un servicio en la nube independiente en cada región de destino y la publicación del paquete de implementación para cada servicio en la nube. Sin embargo, tenga en cuenta que la distribución del tráfico entre los servicios en la nube de distintas regiones debe implementarlo el desarrollador de la aplicación o puede implementarse mediante un servicio de administración de tráfico.

Determinar el número de instancias de rol de reserva que debe implementar de antemano para la recuperación ante desastres es un aspecto importante del planeamiento de la capacidad. La existencia de una implementación secundaria a escala completa garantiza que habrá capacidad disponible cuando sea necesario. Sin embargo, esto duplica realmente el costo. Un patrón común consiste en tener una pequeña implementación secundaria lo suficientemente grande como para ejecutar los servicios esenciales. Se recomienda crear al menos una pequeña implementación secundaria para tener capacidad reservada y para probar la configuración del entorno secundario.

>[AZURE.NOTE]La cuota de suscripción no es una garantía de capacidad. La cuota es simplemente un límite de crédito. Para garantizar la capacidad debe definirse el número necesario de roles en el modelo de servicio y se deben implementar estos roles.

###Equilibrio de carga
Para equilibrar la carga de tráfico entre regiones se requiere el uso de una solución de administración de tráfico. Azure proporciona el [Administrador de tráfico de Azure](https://azure.microsoft.com/services/traffic-manager/). También puede utilizar los servicios de terceros que proporcionan funcionalidades de administración de tráfico similares.

###Estrategias
Existen muchas estrategias alternativas para implementar el proceso distribuido entre regiones. Estas se deben adaptar a los requisitos empresariales específicos y a las circunstancias de la aplicación. En un nivel alto, los métodos pueden dividirse en 3 categorías:

  * __Volver a implementar en caso de desastre__: en este enfoque, la aplicación se vuelve a implementar desde cero en el momento del desastre. Esto es adecuado para aplicaciones que no son esenciales y que no requieren un tiempo de recuperación garantizado.

  * __Espera semiactiva (activa/pasiva)__: un servicio hospedado secundario se crea en una región alternativa y se implementan los roles para garantizar una capacidad mínima; sin embargo, los roles no reciben el tráfico de producción. Este enfoque es útil para las aplicaciones que no se han diseñado para distribuir el tráfico entre las regiones.

  * __Reserva activa (activa/activa)__: la aplicación está diseñada para recibir la carga de producción en varias regiones. Los servicios en la nube de cada región pueden configurarse para una capacidad mayor de la necesaria para fines de recuperación ante desastres. Como alternativa, se podrían escalar horizontalmente los servicios en la nube según sea necesario en el momento de un desastre y una conmutación por error. Este enfoque requiere una inversión sustancial en el diseño de la aplicación, pero tiene ventajas importantes como un tiempo bajo de recuperación garantizado, la prueba continua de todas las ubicaciones de recuperación y un uso eficiente de la capacidad.

Un análisis completo del diseño distribuido está fuera del ámbito de este documento. Para más información, consulte [Consideraciones sobre la alta disponibilidad y la recuperación ante desastres para las aplicaciones de Azure](https://aka.ms/drtechguide)

##Máquinas virtuales
La recuperación de máquinas virtuales (VMs) de infraestructura como servicio (IaaS) es similar en muchos aspectos a la recuperación de procesos de plataforma como servicio (PaaS), sin embargo, hay diferencias importantes debido al hecho de que una máquina virtual de IaaS consta de la máquina virtual y el disco de esta.

  * __Use Copia de seguridad de Azure para crear copias de seguridad entre regiones coherentes con la aplicación__: [Copia de seguridad de Azure](https://azure.microsoft.com/services/backup/) permite a los clientes crear copias de seguridad coherentes con la aplicación en varios discos de máquinas virtuales y admite la replicación de tales copias entre regiones. Esto se logra al elegir replicar geográficamente el almacén de copia de seguridad en el momento de la creación. Tenga en cuenta que la replicación del almacén de copia de seguridad se debe configurar en el momento de la creación, ya que no se podrá establecer más adelante. Si se pierde una región, Microsoft dará las copias de seguridad disponibles a los clientes. Los clientes podrán restaurar a cualquiera de sus puntos de restauración configurados.

  * __Separe el disco de datos del disco del sistema operativo__: una consideración importante para las máquinas virtuales de IaaS es que no se puede cambiar el disco del sistema operativo sin volver a crear la máquina virtual. Esto no supone ningún problema si su estrategia de recuperación es volver a implementar después de los desastres. Sin embargo, podría ser un problema si utiliza el método de espera semiactiva para capacidad reservada. Para implementar esto correctamente debe tener el disco de sistema operativo correcto implementado en las ubicaciones principales y secundarias y se deben almacenar los datos de la aplicación en una unidad independiente. Si es posible, utilice una configuración estándar de sistema operativo que se pueda proporcionar en ambas ubicaciones. Después de una conmutación por error, debe adjuntar la unidad de datos a las máquinas virtuales de IaaS existentes en el controlador de dominio secundario. Use AzCopy para copiar instantáneas de los discos de datos en un sitio remoto.

  * __Posibles problemas de coherencia después de una conmutación por error geográfica de varios discos de máquinas virtuales__: los discos de máquinas virtuales se implementan como blobs de Almacenamiento de Azure y tienen la misma característica de replicación geográfica (consulte a continuación). A menos que utilice [Copia de seguridad de Azure](https://azure.microsoft.com/services/backup/) no hay garantías de coherencia entre los discos, porque la replicación geográfica es asincrónica y se replica de forma independiente. Se garantiza que los discos de máquinas virtuales individuales se encontrarán en un estado de coherencia frente a bloqueos después de una conmutación por error geográfica, pero no la coherencia entre varios discos. Esto podría producir problemas en algunos casos (por ejemplo, en los casos de seccionamiento de discos).

##Almacenamiento

###Recuperación mediante almacenamiento con redundancia geográfica de blobs, tablas, colas y almacenamiento de discos de máquinas virtuales
En Azure, todos los blobs, tablas, colas y discos de máquinas virtuales se replican geográficamente de forma predeterminada. Esto se conoce como almacenamiento con redundancia geográfica (GRS). El almacenamiento con redundancia geográfica replica los datos de almacenamiento en un centro de datos emparejado a cientos de kilómetros de distancia dentro de una región geográfica concreta. El almacenamiento con redundancia geográfica está diseñado para proporcionar durabilidad adicional si se produce un desastre en el centro de datos principal. Microsoft controla cuándo se produce la conmutación por error y esta se limita a casos de desastres importantes en los que la ubicación principal original se considera irrecuperable en un período de tiempo razonable. En algunos escenarios este puede ser de varios días. Normalmente, los datos se replican en unos minutos, aunque el intervalo de sincronización aún no lo cubre el Acuerdo de Nivel de Servicio.

En el caso de una conmutación por error geográfica no habrá ningún cambio en cómo se accede a la cuenta (la dirección URL y la clave de cuenta no cambiarán), sin embargo, la cuenta de almacenamiento estará en una región distinta después de la conmutación por error, lo que podría afectar a las aplicaciones que necesitan tener afinidad regional con la cuenta de almacenamiento. Incluso para aquellos servicios y aplicaciones que no requieren una cuenta de almacenamiento en el mismo centro de datos, los gastos por ancho de banda y latencia entre centros de datos pueden ser un motivo convincente para mover el tráfico temporalmente a la región de conmutación por error. Esto podría tenerse en cuenta a la hora de crear una estrategia general de recuperación ante desastres.

Además de la conmutación por error automática proporcionada por GRS, Azure ha introducido un servicio que le proporciona acceso de lectura a la copia de los datos en la ubicación de almacenamiento secundaria. Este se denomina Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS).

Para más información sobre GRS y RA-GRS, consulte [Replicación de Almacenamiento de Azure](../storage/storage-redundancy.md).

###Asignaciones de regiones de replicación geográfica:
Es importante saber dónde se replican geográficamente los datos para saber dónde implementar las otras instancias de los datos que requieren afinidad regional con el almacenamiento. La siguiente tabla muestra los emparejamientos de la ubicación principal y la secundaria:

[AZURE.INCLUDE [paired-region-list](../../includes/paired-region-list.md)]

###Precios de la replicación geográfica:
La replicación geográfica está incluida en el precio actual de Almacenamiento de Azure. Se denomina almacenamiento con redundancia geográfica. Si no desea que los datos se repliquen geográficamente puede deshabilitar la replicación geográfica para su cuenta. Esto se denomina almacenamiento con redundancia local y se cobra a un precio reducido con respecto al almacenamiento de replicación geográfica.

###Determinación de si se ha producido una conmutación por error geográfica
Si se produce una conmutación por error geográfica, se publicará en el [panel de estado del servicio de Azure](https://azure.microsoft.com/status/), sin embargo, las aplicaciones pueden implementar medios automatizados para detectar esto mediante la supervisión de la región geográfica de la cuenta de almacenamiento. Esto se puede utilizar para desencadenar otras operaciones de recuperación como la activación de recursos de proceso en la región geográfica a la que se movió el almacenamiento. Esto se puede consultar desde Service Management API mediante [Get Storage Account Properties](https://msdn.microsoft.com/library/ee460802.aspx) (Obtención de propiedades de la cuenta de almacenamiento). Las propiedades pertinentes son:

    <GeoPrimaryRegion>primary-region</GeoPrimaryRegion>
    <StatusOfPrimary>[Available|Unavailable]</StatusOfPrimary>
    <LastGeoFailoverTime>DateTime</LastGeoFailoverTime>
    <GeoSecondaryRegion>secondary-region</GeoSecondaryRegion>
    <StatusOfSecondary>[Available|Unavailable]</StatusOfSecondary>

###Discos de máquinas virtuales y conmutación por error geográfica
Como se describe en la sección de discos de máquinas virtuales, no hay garantías de coherencia de los datos entre discos de máquinas virtuales después de una conmutación por error. Para garantizar la corrección de las copias de seguridad, se debe utilizar un producto como Data Protection Manager, para realizar una copia de seguridad y restaurar los datos de la aplicación.

##Base de datos

###Base de datos SQL
Base de datos SQL de Azure proporciona dos tipos de recuperación, la restauración geográfica y la replicación geográfica activa.

####Restauración geográfica
La [restauración geográfica](../sql-database/sql-database-geo-restore.md) también está disponible en las bases de datos Básico, Estándar y Premium. Esta restauración proporciona la opción de recuperación predeterminada cuando la base de datos tampoco está disponible debido a una incidencia en la región en la que se hospeda la base de datos. Al igual que la restauración a un momento dado, la restauración geográfica se basa en copias de seguridad de la base de datos en el almacenamiento de Azure con redundancia geográfica. Esta opción permite restaurar la base de datos desde la copia de seguridad de replicación geográfica. Por lo tanto es resistente a las interrupciones de almacenamiento que tienen lugar en la región principal. Vea la sección [Recuperación tras una interrupción](../sql-database/sql-database-disaster-recovery.md) para obtener más información sobre cómo usar la restauración geográfica.

####Replicación geográfica activa
La [Replicación geográfica activa](../sql-database/sql-database-geo-replication-overview.md) está disponible para todos los niveles de base de datos. Está diseñada para aplicaciones que tienen unos requisitos de recuperación más exigentes que los que puede ofrecer la restauración geográfica. Con la replicación geográfica activa, puede crear hasta cuatro bases de datos secundarias legibles en servidores situados en regiones diferentes. Puede iniciar la conmutación por error a cualquiera de las bases de datos secundarias. Además, la replicación geográfica activa puede utilizarse para los escenarios de actualización o reubicación de la aplicación, así como para el equilibrio de cargas de trabajo de solo lectura. Consulte [Diseño para la continuidad del negocio](../sql-database/sql-database-business-continuity-design.md) para obtener información detallada sobre cómo [configurar la replicación geográfica](../sql-database/sql-database-geo-replication-portal.md) y realizar la [conmutación por error a la base de datos secundaria](../sql-database/sql-database-geo-replication-failover-portal.md). Vea la sección [Actualización de la aplicación sin tiempo de inactividad](../sql-database/sql-database-business-continuity-application-upgrade.md) para obtener más información sobre cómo implementar la actualización de la aplicación sin tiempo de inactividad.

###SQL Server en máquinas virtuales
Hay varias opciones disponibles de recuperación y alta disponibilidad para SQL Server 2012 (y versiones posteriores) que se ejecuta en Máquinas virtuales de Azure. Para una información más detallada, consulte [Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

##Otros servicios de la plataforma de Azure
Al intentar ejecutar el servicio en la nube en varias regiones de Azure, debe tener en cuenta las implicaciones de cada una de las dependencias. En las secciones siguientes, la guía específica del servicio supone que debe usar el mismo servicio de Azure en un centro de datos de Azure alternativo. Esto implica tareas de configuración y de replicación de datos.

>[AZURE.NOTE]En algunos casos, estos pasos pueden ayudar a mitigar una interrupción específica del servicio en lugar de un evento de centro de datos completo. Desde la perspectiva de la aplicación, una interrupción específica del servicio puede ser igual de restrictiva y requerir la migración temporal del servicio en una región de Azure alternativa.

###Bus de servicio
Bus de servicio de Azure usa un espacio de nombres único que no abarca varias regiones de Azure. El primer requisito es configurar los espacios de nombres necesarios del bus de servicio en la región alternativa. Sin embargo, también hay consideraciones sobre la durabilidad de los mensajes en cola. Existen varias estrategias para replicar mensajes entre regiones de Azure. Para obtener detalles sobre estas estrategias de replicación y otras estrategias de recuperación ante desastres, consulte [Procedimientos recomendados para aislar aplicaciones ante desastres e interrupciones de Bus de servicio](../service-bus/service-bus-outages-disasters.md). Para otras consideraciones sobre disponibilidad, consulte [Bus de servicio (disponibilidad)](./resiliency-technical-guidance-recovery-local-failures.md#service-bus).

###Aplicaciones web
Para migrar una aplicación web de Azure a una región secundaria de Azure, debe tener una copia de seguridad del sitio web disponible para su publicación. Si la interrupción no afecta al centro de datos de Azure completo, es posible utilizar una FTP para descargar una copia de seguridad reciente del contenido del sitio. A continuación, cree una nueva aplicación web en la región alternativa, a menos que ya lo haya hecho previamente para reservar capacidad. Publique el sitio en la nueva región y realice los cambios de configuración necesarios. Estos cambios podrían incluir cadenas de conexión de base de datos u otra configuración específica de la región. Si es necesario, agregue el certificado SSL del sitio y cambie el registro DNS CNAME para que el nombre de dominio personalizado señale a la dirección URL de la aplicación web de Azure reimplementada.

###Servicios móviles
En la región de Azure secundaria, cree un servicio móvil de copia de seguridad para su aplicación. Restaure también la Base de datos SQL de Azure en la región alternativa. A continuación, utilice las herramientas de la línea de comandos de Azure para mover el servicio móvil a la región alternativa. Después, configure el servicio móvil para usar la base de datos restaurada. Para más información sobre este proceso, consulte [Recuperación del servicio móvil en caso de un desastre](../mobile-services/mobile-services-disaster-recovery.md). Para otras consideraciones sobre disponibilidad, consulte [Servicios móviles (disponibilidad)](./resiliency-technical-guidance-recovery-local-failures.md#mobile-services).

###HDInsight
Los datos asociados a HDInsight se almacenan de forma predeterminada en el Almacenamiento de blobs de Azure. HDInsight requiere que un clúster de Hadoop que esté procesando trabajos de MapReduce esté ubicado en la misma región que la cuenta de almacenamiento que contiene los datos analizados. Si utiliza la característica de replicación geográfica disponible en el Almacenamiento de Azure, podrá acceder a datos en la región secundaria en la que estos se replicaron si por algún motivo la región principal ya no está disponible. Puede crear un nuevo clúster de Hadoop en la región en la que se han replicado los datos y seguir procesándolo. Para otras consideraciones sobre disponibilidad, consulte [HDInsight (disponibilidad)](./resiliency-technical-guidance-recovery-local-failures.md#hdinsight).

###SQL Reporting
En la actualidad, la recuperación de la pérdida de una región de Azure requiere varias instancias de SQL Reporting en diferentes regiones de Azure. Estas instancias de SQL Reporting deben tener acceso a los mismos datos, y estos deben tener su propio plan de recuperación en caso de desastre. También puede mantener copias de seguridad externas del archivo RDL para cada informe.

###Servicios multimedia
Los Servicios multimedia de Azure tienen un enfoque de recuperación diferente para la codificación y el streaming. Normalmente, el streaming es más importante durante un apagón regional. Para prepararse para esto, debe tener una cuenta de Servicios multimedia en dos regiones distintas de Azure. El contenido codificado debe estar ubicado en ambas regiones. Durante un error, puede redirigir el tráfico de streaming a la región alternativa. La codificación puede realizarse en cualquier región de Azure. Si la codificación está sujeta a limitación temporal como, por ejemplo, durante el procesamiento de eventos en directo, debe estar preparado para enviar trabajos a un centro de datos alternativo durante los errores.

###Red virtual
Los archivos de configuración proporcionan la manera más rápida de configurar una red virtual en otra región de Azure. Después de configurar la red virtual en la región principal de Azure, [exporte la configuración de red virtual](../virtual-network/virtual-networks-create-vnet-classic-portal.md) para la red actual a un archivo de configuración de red. En el caso de una interrupción en la región principal, [restaure la red virtual](../virtual-network/virtual-networks-create-vnet-classic-portal.md) a partir del archivo de configuración almacenado. Después, configure otros servicios en la nube, máquinas virtuales o configuración entre locales para que funcionen con la nueva red virtual.

##Listas de comprobación para la recuperación ante desastres.

##Lista de comprobación de los Servicios en la nube
  1. Revise la sección [Servicios en la nube](#cloud-services) de este documento.
  2. Cree una estrategia de recuperación ante desastres entre regiones.
  3. Comprenda las ventajas e inconvenientes de reservar capacidad en las regiones alternativas.
  4. Use herramientas de enrutamiento de tráfico, como el Administrador de tráfico de Azure.

##Lista de comprobación de máquinas virtuales
  1. Revise la sección [Máquinas virtuales](#virtual-machines) de este documento.
  2. Utilice [Copia de seguridad de Azure](https://azure.microsoft.com/services/backup/) para crear copias de seguridad de la aplicación coherentes entre regiones.

##Lista de comprobación de almacenamiento
  1. Revise la sección [Almacenamiento](#storage) de este documento.
  2. No deshabilite la replicación geográfica de los recursos de almacenamiento.
  3. Conozca la región alternativa para la replicación geográfica en caso de conmutación por error.
  4. Cree estrategias de copia de seguridad personalizadas para las estrategias de conmutación por error controladas por el usuario.

##Lista de comprobación de Base de datos SQL
  1. Revise la sección [Base de datos SQL](#sql-database) de este documento.
  2. Utilice la [restauración geográfica](../sql-database/sql-database-geo-restore.md) o la [replicación geográfica](../sql-database/sql-database-geo-replication-overview.md) según corresponda

##Lista de comprobación de SQL Server en Máquinas virtuales
  1. Revise la sección [SQL Server en Máquinas virtuales](#sql-server-on-virtual-machines) de este documento.
  2. Use grupos de disponibilidad AlwaysOn entre regiones o la creación de reflejo de base de datos.
  3. También puede utilizar copia de seguridad y restauración en el almacenamiento de blobs.

##Lista de comprobación del Bus de servicio
  1. Revise la sección [Bus de servicio](#service-bus) de este documento.
  2. Configure un espacio de nombres del Bus de servicio en una región alternativa.
  3. Considere las estrategias de replicación personalizadas para los mensajes entre regiones

##Lista de comprobación de aplicaciones web
  1. Revise la sección [Aplicaciones web](#web-apps) de este documento.
  2. Disponga de copias de seguridad del sitio web fuera de la región principal.
  3. Si la interrupción es parcial, intente recuperar el sitio actual con FTP.
  4. Planee la implementación del sitio web en un sitio web nuevo o ya existente en una región alternativa.
  5. Planee cambios de configuración para los registros de la aplicación y los de DNS CNAME.

##Lista de comprobación de los Servicios móviles
  1. Revise la sección [Servicios móviles](#mobile-services) de este documento.
  2. Cree un servicio móvil de copia de seguridad en una región alternativa.
  3. Administre las copias de seguridad de la Base de datos SQL de Azure asociada para restaurar durante la conmutación por error.
  4. Use las herramientas de la línea de comandos de Azure para mover el servicio móvil.

##Lista de comprobación de HDInsight
  1. Revise la sección [HDInsight](#hdinsight) de este documento.
  2. Cree un nuevo clúster de Hadoop en la región con los datos replicados.

##Lista de comprobación de SQL Reporting
  1. Revise la sección [SQL Reporting](#sql-reporting) de este documento.
  2. Disponga de una instancia alternativa de SQL Reporting en una región diferente.
  3. Mantenga un plan independiente para replicar el destino en esa región.

##Lista de comprobación de los Servicios multimedia
  1. Revise la sección [Servicios multimedia](#media-services) de este documento.
  2. Cree una cuenta de Servicios multimedia en una región alternativa.
  3. Codifique el mismo contenido en ambas regiones para que admitan la conmutación por error del streaming.
  4. Envíe los trabajos de codificación a una región alternativa en caso de una interrupción.

##Lista de comprobación de Red virtual
  1. Revise la sección [Red virtual](#virtual-network) de este documento.
  2. Use la configuración exportada de la red virtual para volver a crearla en otra región.
 
##Pasos siguientes
Este artículo forma parte de una serie que se centra en la [Guía técnica sobre resistencia en Azure](./resiliency-technical-guidance.md). El siguiente artículo de esta serie se centra en la [recuperación desde un centro de datos local a Azure](./resiliency-technical-guidance-recovery-on-premises-azure.md).

<!---HONumber=AcomDC_0525_2016-->