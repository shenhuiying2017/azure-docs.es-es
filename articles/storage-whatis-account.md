<properties linkid="manage-services-what-is-a-storage-account" urlDisplayName="What is a Storage Account" pageTitle="What is a storage account? | Microsoft Azure" metaKeywords="" description="Learn about the different types of storage accounts available in Azure, and get definitions for key storage terms." metaCanonical="" services="storage" documentationCenter="" title="What is a Storage Account?" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"/>

# ¿Qué es una cuenta de almacenamiento?

El Almacenamiento de Azure incluye tres servicios: almacenamiento de blobs, almacenamiento de tablas y almacenamiento de colas. Cada cuenta de almacenamiento incluye estos servicios. Una cuenta de almacenamiento proporciona el espacio de nombres único para trabajar con blobs, colas y tablas.

Para obtener más información sobre los límites de las cuentas de almacenamiento, consulte [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure][].

Toda la información acerca de su cuenta de almacenamiento, incluida la información de cuándo se creó, se encuentra disponible en el Portal de administración, en la página **Panel** para el **Almacenamiento**.

Los costes del almacenamiento se basan en cuatro factores: capacidad de almacenamiento, esquema de replicación, transacciones de almacenamiento y salida de los datos. La capacidad de almacenamiento se refiere a cuánto de la asignación correspondiente a cuentas de almacenamiento utiliza para almacenar datos. El coste de simplemente almacenar los datos está determinado por la cantidad de datos que almacena y la manera en que se replican. Las transacciones se refieren a todas las operaciones de lectura y escritura en Almacenamiento de Azure. La salida de los datos se refiere a los datos transferidos fuera de una región de Azure. Cuando una aplicación que no está en ejecución en la misma región tiene acceso a los datos en su cuenta de almacenamiento, se le cobra por la salida de los datos, independientemente de si la aplicación es un servicio en la nube u otro tipo de aplicación. (En el caso de los servicios de Azure, puede llevar a cabo pasos para agrupar sus datos y servicios en los mismos centros de datos para reducir o eliminar los cargos por concepto de salida de los datos).

La página [Detalles de precios de Storage][] proporciona información detallada sobre los precios de la capacidad, replicación y transacciones de almacenamiento. La página [Detalles de precios de Transferencias de datos][] proporciona información detallada sobre los precios para la salida de datos.

## Conceptos de cuenta de almacenamiento

### Opciones de replicación de la cuenta de almacenamiento

[WACOM.INCLUDE [storage-replication-options][]]

### Extremos de la cuenta de almacenamiento

Los *extremos* de una cuenta de almacenamiento representan el nivel más alto del espacio de nombres para el acceso a blobs, tablas, colas o archivos. Los extremos predeterminados para una cuenta de almacenamiento tienen los siguientes formatos:

-   Servicio BLOB: <http://*mystorageaccount>\*.blob.core.windows.net

-   Servicio Tabla: <http://*mystorageaccount>\*.table.core.windows.net

-   Servicio Cola: <http://*mystorageaccount>\*.queue.core.windows.net

-   Servicio de archivos: <http://*mystorageaccount>\*.file.core.windows.net

La dirección URL para el acceso a un objeto en una cuenta de almacenamiento se crea anexando la ubicación del objeto en la cuenta de almacenamiento al extremo. Por ejemplo, una dirección de blob podría tener el siguiente formato: <http://*mystorageaccount>*.blob.core.windows.net/*mycontainer*/*myblob\*.

### Seguridad de la cuenta de almacenamiento

Al crear una cuenta de almacenamiento, Azure genera dos claves de acceso de almacenamiento de 512 bits que se usan para autenticación cuando se obtiene acceso a la cuenta de almacenamiento. Al brindar dos claves de acceso de almacenamiento, Azure le permite volver a generar las claves sin interrupción en su servicio de almacenamiento, o bien, tener acceso a ese servicio.

Se recomienda no compartir con nadie las claves de acceso de la cuenta de almacenamiento. Si piensa que su cuenta se ha puesto en peligro, puede volver a generar las claves de acceso desde dentro del portal. Seleccione su cuenta de almacenamiento y elija **Administrar claves de acceso**.

Para permitir el acceso a los recursos de almacenamiento sin proporcionar sus claves de acceso, puede usar una *firma de acceso compartido*. Una firma de acceso compartido proporciona acceso a un recurso de su cuenta durante un intervalo que defina y con los permisos que especifique. Para obtener más información, consulte el [tutorial sobre firmas de acceso compartido][].

### Métrica y registro de la cuenta de almacenamiento

-   **métricas mínimas frente a métricas detalladas** Puede configurar métricas mínimas o detalladas en la configuración de supervisión de la cuenta de almacenamiento. Las *métricas mínimas* recopilan métricas en datos como entrada/salida, disponibilidad, latencia y porcentajes de éxito, que se agregan a los servicios Blob, Tabla y Cola. Las *métricas detalladas* recopilan detalles en el nivel de las operaciones, además de detalles en el nivel de servicio para las mismas métricas. Las métricas detalladas facilitan el análisis preciso de los problemas que se producen durante las operaciones de las aplicaciones. Si desea obtener la lista completa de métricas disponibles, consulte [Storage Analytics Metrics Table Schema][]. Para obtener más información acerca de la supervisión del almacenamiento, consulte [About Storage Analytics Metrics][].

-   **registro** El registro es una característica configurable de las cuentas de almacenamiento que permite registrar solicitudes de lectura y de escritura y eliminar blobs, tablas y colas. Puede configurar el registro en el Portal de administración de Azure, pero no puede ver los registros en el Portal de administración. Los registros están almacenados en la cuenta de almacenamiento, en el contenedor $logs, y se puede tener acceso a ellos desde ahí. Para obtener más información, consulte [Storage Analytics Overview][].

### Grupos de afinidad para colocalizar el almacenamiento de Azure y otros servicios

Un *grupo de afinidad* es un grupo geográfico de los servicios de Azure y máquinas virtuales con la cuenta de almacenamiento de Azure. Un grupo de afinidad puede mejorar el rendimiento del servicio al ubicar cargas de trabajo de equipos en el mismo centro de datos o cerca de la audiencia de usuarios de destino. Además, no se aplicarán costes de facturación en la salida cuando el acceso a los datos de una cuenta de almacenamiento tenga lugar desde otro servicio que forma parte del mismo grupo de afinidad.

  [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure]: http://msdn.microsoft.com/en-us/library/dn249410.aspx
  [Detalles de precios de Storage]: http://www.windowsazure.com/en-us/pricing/details/#storage
  [Detalles de precios de Transferencias de datos]: http://www.windowsazure.com/en-us/pricing/details/data-transfers/
  [storage-replication-options]: ../includes/storage-replication-options.md
  [tutorial sobre firmas de acceso compartido]: ../storage-dotnet-shared-access-signature-part-1/
  [Storage Analytics Metrics Table Schema]: http://msdn.microsoft.com/en-us/library/windowsazure/hh343264.aspx
  [About Storage Analytics Metrics]: http://msdn.microsoft.com/en-us/library/windowsazure/hh343258.aspx
  [Storage Analytics Overview]: http://msdn.microsoft.com/en-us/library/windowsazure/hh343268.aspx
