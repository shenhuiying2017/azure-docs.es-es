<properties umbracoNaviHide="0" pageTitle="Storage Account Concepts | Azure" metaKeywords="Azure storage, storage service, service, storage account, account, create storage account, create account" description="Learn about storage account concepts." linkid="manage-windows-how-to-guide-storage-accounts" urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="Storage Account Concepts" services="storage" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# Conceptos de cuenta de almacenamiento

## Opciones de replicación de la cuenta de almacenamiento

[WACOM.INCLUDE [storage-replication-options][]]

## Extremos de la cuenta de almacenamiento

Los *extremos* de una cuenta de almacenamiento representan el nivel más alto del espacio de nombres para el acceso a blobs, tablas, colas o archivos. Los extremos predeterminados para una cuenta de almacenamiento tienen los siguientes formatos:

-   Servicio BLOB: http://*mystorageaccount*.blob.core.windows.net

-   Servicio Tabla: http://*mystorageaccount*.table.core.windows.net

-   Servicio Cola: http://*mystorageaccount*.queue.core.windows.net

-   Servicio de archivos: http://*mystorageaccount*.file.core.windows.net

La dirección URL para el acceso a un objeto en una cuenta de almacenamiento se crea anexando la ubicación del objeto en la cuenta de almacenamiento al extremo. Por ejemplo, una dirección de blob podría tener el siguiente formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

## Seguridad de la cuenta de almacenamiento

Al crear una cuenta de almacenamiento, Azure genera dos claves de acceso de almacenamiento de 512 bits que se usan para autenticación cuando se obtiene acceso a la cuenta de almacenamiento. Al brindar dos claves de acceso de almacenamiento, Azure le permite volver a generar las claves sin interrupción en su servicio de almacenamiento, o bien, tener acceso a ese servicio.

Se recomienda no compartir con nadie las claves de acceso de la cuenta de almacenamiento. Si piensa que su cuenta se ha puesto en peligro, puede volver a generar las claves de acceso desde dentro del portal. Seleccione su cuenta de almacenamiento y elija **Administrar claves de acceso**.

Para permitir el acceso a los recursos de almacenamiento sin proporcionar sus claves de acceso, puede usar una *firma de acceso compartido*. Una firma de acceso compartido proporciona acceso a un recurso de su cuenta durante un intervalo que defina y con los permisos que especifique. Para obtener más información, consulte el [tutorial sobre firmas de acceso compartido][].

## Métrica y registro de la cuenta de almacenamiento

-   **métricas mínimas frente a métricas detalladas** Puede configurar métricas mínimas o detalladas en la configuración de supervisión de la cuenta de almacenamiento. Las *métricas mínimas* recopilan métricas en datos como entrada/salida, disponibilidad, latencia y porcentajes de éxito, que se agregan a los servicios Blob, Tabla y Cola. Las *métricas detalladas* recopilan detalles en el nivel de las operaciones, además de detalles en el nivel de servicio para las mismas métricas. Las métricas detalladas facilitan el análisis preciso de los problemas que se producen durante las operaciones de las aplicaciones. Si desea obtener la lista completa de métricas disponibles, consulte [Storage Analytics Metrics Table Schema][]. Para obtener más información acerca de la supervisión del almacenamiento, consulte [About Storage Analytics Metrics][].

-   **registro** El registro es una característica configurable de las cuentas de almacenamiento que permite registrar solicitudes de lectura y de escritura y eliminar blobs, tablas y colas. Puede configurar el registro en el Portal de administración de Azure, pero no puede ver los registros en el Portal de administración. Los registros están almacenados en la cuenta de almacenamiento, en el contenedor $logs, y se puede tener acceso a ellos desde ahí. Para obtener más información, consulte [Storage Analytics Overview][].

## Grupos de afinidad para colocalizar el almacenamiento de Azure y otros servicios

Un *grupo de afinidad* es un grupo geográfico de los servicios de Azure y máquinas virtuales con la cuenta de almacenamiento de Azure. Un grupo de afinidad puede mejorar el rendimiento del servicio al ubicar cargas de trabajo de equipos en el mismo centro de datos o cerca de la audiencia de usuarios de destino. Además, no se aplicarán costes de facturación en la salida cuando el acceso a los datos de una cuenta de almacenamiento tenga lugar desde otro servicio que forma parte del mismo grupo de afinidad.

  [storage-replication-options]: ../includes/storage-replication-options.md
  [tutorial sobre firmas de acceso compartido]: ../storage-dotnet-shared-access-signature-part-1/
  [Storage Analytics Metrics Table Schema]: http://msdn.microsoft.com/en-us/library/windowsazure/hh343264.aspx
  [About Storage Analytics Metrics]: http://msdn.microsoft.com/en-us/library/windowsazure/hh343258.aspx
  [Storage Analytics Overview]: http://msdn.microsoft.com/en-us/library/windowsazure/hh343268.aspx
