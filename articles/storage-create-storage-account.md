<properties urlDisplayName="How to create" pageTitle="Creación de una cuenta de almacenamiento | Azure" metaKeywords="" description="Learn how to create a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" solutions="" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />


<h1><a id="createstorageaccount"></a>Creación de una cuenta de almacenamiento</h1>

Para almacenar archivos y datos en los servicios Blob, Tabla, Cola y Archivo en Azure, debe crear una cuenta de almacenamiento en la región geográfica en la que desea almacenar los datos. Este tema describe la forma de crear una cuenta de almacenamiento en el Portal de administración de Azure.

Para obtener más información acerca de la capacidad y el rendimiento de la cuenta de almacenamiento, consulte [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure](http://msdn.microsoft.com/es-es/library/dn249410.aspx).

> [WACOM.NOTE] Para una máquina virtual de Azure, se crea automáticamente una cuenta de almacenamiento en la ubicación de implementación si todavía no tiene una cuenta de almacenamiento en esa ubicación. El nombre de la cuenta de almacenamiento se basará en el nombre de la máquina virtual.

##Tabla de contenido##

* [Direccionamiento del una cuenta de almacenamiento](#create)
* [Pasos siguientes](#next)

<h2><a id="create"></a>Direccionamiento del una cuenta de almacenamiento</h2>

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).

2. Haga clic en **Crear nuevo**, en **Almacenamiento** y, a continuación, en **Creación rápida**.

	![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3. En **URL**, escriba el nombre de subdominio que vaya usar en la URL para la cuenta de almacenamiento. Para tener acceso a un objeto en almacenamiento, anexará la ubicación del objeto al extremo. Por ejemplo, la dirección URL para obtener acceso a un blob podría ser http://*myaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

4. En **Región/Grupo de afinidad**, seleccione una región o un grupo de afinidad para el almacenamiento.  Seleccione un grupo de afinidad en lugar de una región si desea que sus servicios de almacenamiento estén en el mismo centro de datos con otros servicios de Azure que utilice. Con esto se puede mejorar el rendimiento y no se generan cargos por concepto de salida.

	> [WACOM.NOTE]  Para crear un grupo de afinidad, abra el área <b>Configuración</b> del Portal de administración, haga clic en <b>Grupos de afinidad</b> y, a continuación, haga clic en el botón <b>Agregar un grupo de afinidad</b> o <b>Agregar</b>. Además puede crear y administrar los grupos de afinidad usando la API de administración de servicios de Azure. Consulte <a href="http://msdn.microsoft.com/es-es/library/windowsazure/ee460798.aspx">Operaciones en grupos de afinidad</a> para obtener más información.

5. Si tiene más de una suscripción a Azure, aparecerá el campo **Suscripción**. En **Suscripción**, escriba la suscripción a Azure con la cual desea usar la cuenta de almacenamiento. Puede crear hasta cinco cuentas de almacenamiento para una suscripción.

6. En **Replicación**, seleccione el nivel de replicación que desea para su cuenta de almacenamiento.

	[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

6. Haga clic en **Crear cuenta de almacenamiento**.

	Su cuenta de almacenamiento puede tardar unos minutos en crearse. Para revisar el estado, puede supervisar las notificaciones en la parte inferior del portal. Una vez que se ha creado la cuenta de almacenamiento, la nueva cuenta de almacenamiento tiene un estado **Online** y está lista para usarse. 

	![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)

<h2><a id="next"></a>Pasos siguientes</h2>

- Para obtener más información acerca del Almacenamiento de Azure, consulte la documentación sobre Almacenamiento de Azure en [azure.com](http://azure.microsoft.com/es-es/documentation/services/storage/) y en [MSDN](http://msdn.microsoft.com/es-es/library/gg433040.aspx). 

- Visite el [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/).

