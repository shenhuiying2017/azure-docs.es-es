<properties linkid="manage-services-what-is-a-storage-account" urlDisplayName="What is a Storage Account" pageTitle="What is a storage account? | Microsoft Azure" metaKeywords="" description="Learn about the different types of storage accounts available in Azure, and get definitions for key storage terms." metaCanonical="" services="storage" documentationCenter="" title="What is a Storage Account?" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

¿Qué es una cuenta de almacenamiento?
=====================================

El Almacenamiento de Azure incluye tres servicios: almacenamiento de blobs, almacenamiento de tablas y almacenamiento de colas. Cada cuenta de almacenamiento incluye estos servicios. Una cuenta de almacenamiento proporciona el espacio de nombres único para trabajar con blobs, colas y tablas.

Una cuenta de almacenamiento puede contener hasta 200 TB de datos de blob, cola y tabla si se creó el 8 de junio de 2012 o en una fecha posterior. En el caso de cuentas de almacenamiento creadas antes de esa fecha, la capacidad total es de 100 TB. Puede crear hasta 20 cuentas de almacenamiento con nombre único bajo una única suscripción. Para obtener más información acerca de las cuentas de almacenamiento, consulte [Azure Storage Scalability and Performance Targets](http://msdn.microsoft.com/es-es/library/dn249410.aspx).

Toda la información acerca de su cuenta de almacenamiento, incluida la información de cuándo se creó, se encuentra disponible en el Portal de administración, en la página **Panel** para el **Almacenamiento**.

Los costes del almacenamiento se basan en cuatro factores: capacidad de almacenamiento, esquema de replicación, transacciones de almacenamiento y salida de los datos. La capacidad de almacenamiento se refiere a cuánto de la asignación correspondiente a cuentas de almacenamiento utiliza para almacenar datos. El coste de simplemente almacenar los datos está determinado por la cantidad de datos que almacena y la manera en que se replican. Las transacciones se refieren a todas las operaciones de lectura y escritura en Almacenamiento de Azure. La salida de los datos se refiere a los datos transferidos fuera de una región de Azure. Cuando una aplicación que no está en ejecución en la misma región tiene acceso a los datos en su cuenta de almacenamiento, se le cobra por la salida de los datos, independientemente de si la aplicación es un servicio en la nube u otro tipo de aplicación. (En el caso de los servicios de Azure, puede llevar a cabo pasos para agrupar sus datos y servicios en los mismos centros de datos para reducir o eliminar los cargos por concepto de salida de los datos).

La página [Detalles de precios de Storage](http://www.windowsazure.com/es-es/pricing/details/#storage) proporciona información detallada sobre los precios de la capacidad, replicación y transacciones de almacenamiento. La página [Detalles de precios de Transferencias de datos](http://www.windowsazure.com/es-es/pricing/details/data-transfers/) proporciona información detallada sobre los precios para la salida de datos.

Conceptos
---------

-   **almacenamiento con redundancia geográfica (GRS)** La redundancia geográfica proporciona el nivel más alto de durabilidad del almacenamiento al replicar directamente los datos a una ubicación secundaria dentro de la misma región. Esto permite la conmutación por error en caso de que se produzca un error importante en la ubicación principal. La ubicación secundaria se encuentra a cientos de kilómetros de la ubicación principal. El GRS se implementa a través de una característica que se llama *replicación geográfica*, que está activada de manera predeterminada para una cuenta de almacenamiento, pero que se puede desactivar si no desea usarla (por ejemplo, si las directivas de la empresa impiden su uso). Para obtener más información, consulte [Introducing Geo-Replication for Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx).

-   **almacenamiento con redundancia local (LRS)** El almacenamiento con redundancia local proporciona almacenamiento altamente disponible y duradero dentro de una ubicación única. Para el almacenamiento con redundancia local, los datos de la cuenta se replican tres veces dentro del mismo centro de datos. Todo el almacenamiento en Azure tiene redundancia local. Para que sea más duradero, puede activar la replicación geográfica. El almacenamiento con redundancia local se ofrece con un descuento. Para obtener más información sobre los precios, consulte la [Información sobre el precio](http://www.windowsazure.com/es-es/pricing/details/#storage).

-   **grupo de afinidad** Un *grupo de afinidad* es una agrupación geográfica de sus implementaciones de servicios en la nube y cuentas de almacenamiento dentro de Azure. Un grupo de afinidad puede mejorar el rendimiento del servicio al ubicar cargas de trabajo de equipos en el mismo centro de datos o cerca de la audiencia de usuarios de destino. Además, no se generará un cobro por la salida de los datos cuando un servicio en ejecución en el mismo grupo de afinidad tenga acceso a los datos en su cuenta de almacenamiento.

-   **extremos de cuenta de almacenamiento** Los *extremos* de una cuenta de almacenamiento representan el nivel más alto del espacio de nombres para obtener acceso a blobs, tablas o colas. Los extremos predeterminados para una cuenta de almacenamiento tienen los siguientes formatos:

    -   Servicio BLOB: http://*mystorageaccount*.blob.core.windows.net

    -   Servicio Tabla: http://*mystorageaccount*.table.core.windows.net

    -   Servicio Cola: http://*mystorageaccount*.queue.core.windows.net

-   **URL de cuenta de almacenamiento** La dirección URL para obtener acceso a un objeto en una cuenta de almacenamiento se forma anexando la ubicación del objeto en la cuenta de almacenamiento al extremo. Por ejemplo, una dirección de blob podría tener el siguiente formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

-   **claves de acceso de almacenamiento** Cuando se crea una cuenta de almacenamiento, Azure genera dos claves de acceso de almacenamiento de 512 bits, las que se utilizan para autenticación cuando se tiene acceso a la cuenta de almacenamiento. Al brindar dos claves de acceso de almacenamiento, Azure le permite volver a generar las claves sin interrupción en su servicio de almacenamiento, o bien, tener acceso a ese servicio.

-   **métricas mínimas frente a métricas detalladas** Puede configurar métricas mínimas o detalladas en la configuración de supervisión de la cuenta de almacenamiento. Las *métricas mínimas* recopilan métricas en datos como entrada/salida, disponibilidad, latencia y porcentajes de éxito, que se agregan a los servicios Blob, Tabla y Cola. Las *métricas detalladas* recopilan detalles en el nivel de las operaciones, además de detalles en el nivel de servicio para las mismas métricas. Las métricas detalladas facilitan el análisis preciso de los problemas que se producen durante las operaciones de las aplicaciones. Si desea obtener la lista completa de métricas disponibles, consulte [Storage Analytics Metrics Table Schema](http://msdn.microsoft.com/es-es/library/windowsazure/hh343264.aspx). Para obtener más información acerca de la supervisión del almacenamiento, consulte [About Storage Analytics Metrics](http://msdn.microsoft.com/es-es/library/windowsazure/hh343258.aspx).

-   **registro** El registro es una característica configurable de las cuentas de almacenamiento que permite registrar solicitudes de lectura y de escritura y eliminar blobs, tablas y colas. Puede configurar el registro en el Portal de administración de Azure, pero no puede ver los registros en el Portal de administración. Los registros están almacenados en la cuenta de almacenamiento, en el contenedor $logs, y se puede tener acceso a ellos desde ahí. Para obtener más información, consulte [Storage Analytics Overview](http://msdn.microsoft.com/es-es/library/windowsazure/hh343268.aspx).


