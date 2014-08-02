<properties linkid="manage-services-how-to-create-a-storage-account" urlDisplayName="How to create" pageTitle="How to create a storage account | Azure" metaKeywords="" description="Learn how to create a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" authors="tysonn" solutions="" manager="paulettm" editor="cgronlun" />

Creación de una cuenta de almacenamiento
========================================

Para almacenar archivos y datos en los servicios Blob, Tabla y Cola en Azure, debe crear una cuenta de almacenamiento en la región geográfica en la que desea almacenar los datos. Una cuenta de almacenamiento puede contener hasta 200 TB de datos y puede crear hasta 20 cuentas de almacenamiento para cada suscripción a Azure. Consulte [Azure Storage Scalability and Performance Targets](http://msdn.microsoft.com/en-us/library/dn249410.aspx) para obtener más información.

Este tema describe la forma de crear una cuenta de almacenamiento en el Portal de administración de Azure.

**Nota:**

Para una máquina virtual de Azure, se crea automáticamente una cuenta de almacenamiento en la ubicación de implementación si todavía no tiene una cuenta de almacenamiento en esa ubicación. El nombre de la cuenta de almacenamiento se basará en el nombre de la máquina virtual.

Tabla de contenido
------------------

-   [Creación de una cuenta de almacenamiento](#create)
-   [Pasos siguientes](#next)

Creación de una cuenta de almacenamiento
----------------------------------------

1.  Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).

2.  Haga clic en **Create New**, en **Almacenamiento** y, a continuación, en **Quick Create**.

    ![Nueva cuenta de almacenamiento](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3.  En **URL**, escriba el nombre de subdominio que vaya usar en la URL para la cuenta de almacenamiento. Para tener acceso a un objeto en almacenamiento, anexará la ubicación del objeto al extremo. Por ejemplo, la dirección URL para obtener acceso a un blob podría ser http://*myaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

4.  En **Region/Affinity Group**, seleccione una región o un grupo de afinidad para el almacenamiento. Seleccione un grupo de afinidad en lugar de una región si desea que sus servicios de almacenamiento estén en el mismo centro de datos con otros servicios de Azure que utilice. Con esto se puede mejorar el rendimiento y no se generan cargos por concepto de salida.

    > [WACOM.NOTE] 
      > Para crear un grupo de afinidad, abra el área **Networks** del Portal de administración, haga clic en **Grupos de afinidad** y, a continuación, en **Crear un nuevo grupo de afinidad** o **Create**. Puede usar grupos de afinidad que haya creado en el anterior Portal de administración. Para abrir el otro portal, haga clic en **Preview** en la barra de títulos y, a continuación, en **Take me to the previous portal**. (Para volver a este portal, haga clic en **View the Preview Portal** en la parte inferior del portal). Además puede crear y administrar los grupos de afinidad usando la API de administración de servicios de Azure. Consulte [Operaciones en grupos de afinidad](http://msdn.microsoft.com/en-us/library/windowsazure/ee460798.aspx) para obtener más información.

5.  Si tiene más de una suscripción a Azure, aparecerá el campo **Suscripción**. En **Suscripción**, escriba la suscripción a Azure con la cual desea usar la cuenta de almacenamiento. Puede crear hasta cinco cuentas de almacenamiento para una suscripción.

6.  En **Replication**, seleccione el nivel de replicación que desea para su cuenta de almacenamiento.

    De manera predeterminada, la replicación está definida en **Geo-Redundant**. Con replicación con redundancia geográfica, la cuenta de almacenamiento y todos los datos que contiene realiza conmutación por error en una ubicación secundaria ante la eventualidad de un desastre importante en la ubicación principal. Azure asigna una ubicación secundaria en la misma región, que no se puede modificar. Después de una conmutación por error, la ubicación secundaria se convierte en la ubicación principal para la cuenta de almacenamiento y los datos se replican en una ubicación secundaria nueva.

    Si desea poder leer datos desde la ubicación secundaria, puede seleccionar la replicación con **Read-Access Geo-Redundant**. Esta opción proporciona replicación con redundancia geográfica y permite el acceso de solo lectura a los datos replicados en la ubicación secundaria. La replicación con redundancia geográfica de acceso de lectura le permite tener acceso a los datos desde la ubicación principal o la secundaria, ante la eventualidad de que alguna de las ubicaciones deje de estar disponible.

    Una tercera opción de replicación, **Read Access Geo-Redundant**, está actualmente en vista previa. Esta opción permite acceso de solo lectura a los datos replicados en la ubicación secundaria. La replicación con redundancia geográfica de acceso de lectura le permite tener acceso a los datos desde la ubicación principal o la secundaria, ante la eventualidad de que alguna de las ubicaciones deje de estar disponible.

    > [WACOM.NOTE]
    >  Para utilizar la replicación de redundancia geográfica de acceso de lectura mientras está en vista previa, debe solicitar manualmente que esa característica se habilite para su suscripción. Visite la página [Azure Preview Features](https://account.windowsazure.com/PreviewFeatures) para solicitar replicación de redundancia geográfica de acceso de lectura para su suscripción. Para obtener más detalles acerca de la replicación con redundancia geográfica de acceso de lectura, consulte el [blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/04/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) (en inglés). Si la replicación con redundancia geográfica de acceso de lectura no está habilitada como una característica de vista previa en su suscripción, la opción para seleccionarla para su cuenta de almacenamiento estará deshabilitada.

    Para obtener información de precios para la replicación de cuentas de almacenamiento, consulte [Detalles de precios de Storage](http://www.windowsazure.com/en-us/pricing/details/storage/).

7.  Haga clic en **Create Storage Account**.

    Su cuenta de almacenamiento puede tardar unos minutos en crearse. Para revisar el estado, puede supervisar las notificaciones en la parte inferior del portal. Una vez que se ha creado la cuenta de almacenamiento, la nueva cuenta de almacenamiento tiene un estado **Online** y está lista para usarse.

    ![Página de almacenamiento](./media/storage-create-storage-account/Storage_StoragePage.png)

Pasos siguientes
----------------

-   Para obtener más información acerca de los servicios de almacenamiento de Azure, consulte [Descripción del almacenamiento de nube](http://www.windowsazure.com/en-us/develop/net/fundamentals/cloud-storage/) y [Blobs, colas y tablas](http://msdn.microsoft.com/en-us/library/gg433040.aspx).

-   Visite el [blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/) (en inglés).

-   Configure sus aplicaciones para utilizar los servicios de Blobs, Tablas y Colas de Azure. El [Centro para desarrolladores de Azure](http://www.windowsazure.com/en-us/develop/overview/) proporciona guías de procedimientos para utilizar los servicios de almacenamiento Blob, Tabla y Cola con sus aplicaciones.NET, Node.js, Java y PHP. Para obtener instrucciones específicas para un lenguaje de programación, consulte las guías de procedimientos para ese lenguaje.


