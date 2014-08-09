<properties umbracoNaviHide="0" pageTitle="Storage Account Concepts | Azure" metaKeywords="Azure storage, storage service, service, storage account, account, create storage account, create account" description="Learn about storage account concepts." linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Storage Account Concepts" authors="" />

Conceptos de cuenta de almacenamiento
=====================================

-   **almacenamiento con redundancia geográfica (GRS)** La redundancia geográfica proporciona el nivel más alto de durabilidad del almacenamiento al replicar directamente los datos a una ubicación secundaria dentro de la misma región. Esto permite la conmutación por error en caso de que se produzca un error importante en la ubicación principal. La ubicación secundaria se encuentra a cientos de kilómetros de la ubicación principal. El GRS se implementa a través de una característica que se llama *replicación geográfica*, que está activada de manera predeterminada para una cuenta de almacenamiento, pero que se puede desactivar si no desea usarla (por ejemplo, si las directivas de la empresa impiden su uso). Para obtener más información, consulte [Introducing Geo-replication for Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx).

-   **almacenamiento con redundancia local (LRS)** El almacenamiento con redundancia local proporciona almacenamiento altamente disponible y duradero dentro de una ubicación única. Para el almacenamiento con redundancia local, los datos de la cuenta se replican tres veces dentro del mismo centro de datos. Todo el almacenamiento en Azure tiene redundancia local. Para que sea más duradero, puede activar la replicación geográfica. El almacenamiento con redundancia local se ofrece con un descuento. Para obtener más información, consulte [Precios de Azure de un vistazo](http://www.windowsazure.com/es-es/pricing/details/).

-   **grupo de afinidad** Un *grupo de afinidad* es una agrupación geográfica de sus implementaciones de servicios en la nube y cuentas de almacenamiento dentro de Azure. Un grupo de afinidad puede mejorar el rendimiento del servicio al ubicar cargas de trabajo de equipos en el mismo centro de datos o cerca de la audiencia de usuarios de destino. Además, no se aplicarán costes de facturación en la salida.

-   **extremos de cuenta de almacenamiento** Los *extremos* de una cuenta de almacenamiento representan el nivel más alto del espacio de nombres para obtener acceso a blobs, tablas o colas. Los extremos predeterminados para una cuenta de almacenamiento tienen los siguientes formatos:

    -   Servicio BLOB: http://*mystorageaccount*.blob.core.windows.net

    -   Servicio Tabla: http://*mystorageaccount*.table.core.windows.net

    -   Servicio Cola: http://*mystorageaccount*.queue.core.windows.net

-   **URL de cuenta de almacenamiento** La dirección URL para obtener acceso a un objeto en una cuenta de almacenamiento se crea anexando la ubicación del objeto en la cuenta de almacenamiento al extremo. Por ejemplo, una dirección de blob podría tener el siguiente formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

-   **claves de acceso de almacenamiento** Cuando se crea una cuenta de almacenamiento, Azure genera dos claves de acceso de almacenamiento de 512 bits, las que se utilizan para autenticación cuando se tiene acceso a la cuenta de almacenamiento. Al brindar dos claves de acceso de almacenamiento, Azure le permite volver a generar las claves sin interrupción en su servicio de almacenamiento, o bien, tener acceso a ese servicio.

-   **métricas mínimas frente a métricas detalladas** Puede configurar métricas mínimas o detalladas en la configuración de supervisión de la cuenta de almacenamiento. Las *métricas mínimas* recopilan métricas en datos como entrada/salida, disponibilidad, latencia y porcentajes de éxito, que se agregan a los servicios Blob, Tabla y Cola. Las *métricas detalladas* recopilan detalles en el nivel de las operaciones, además de detalles en el nivel de servicio para las mismas métricas. Las métricas detalladas facilitan el análisis preciso de los problemas que se producen durante las operaciones de las aplicaciones. Si desea obtener la lista completa de métricas disponibles, consulte [Storage Analytics Metrics Table Schema](http://msdn.microsoft.com/es-es/library/windowsazure/hh343264.aspx). Para obtener más información acerca de la supervisión del almacenamiento, consulte [About Storage Analytics Metrics](http://msdn.microsoft.com/es-es/library/windowsazure/hh343258.aspx).

-   **registro** El registro es una característica configurable de las cuentas de almacenamiento que permite registrar solicitudes de lectura y de escritura y eliminar blobs, tablas y colas. Puede configurar el registro en el Portal de administración de Azure, pero no puede ver los registros en el Portal de administración. Los registros están almacenados en la cuenta de almacenamiento, en el contenedor \$logs, y se puede tener acceso a ellos desde ahí. Para obtener más información, consulte [Storage Analytics Overview](http://msdn.microsoft.com/es-es/library/windowsazure/hh343268.aspx).


