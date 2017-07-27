---
title: "Creación, administración o eliminación de una cuenta de almacenamiento en Azure Portal | Microsoft Docs"
description: "Cree una nueva cuenta de almacenamiento, administre las claves de acceso de dicha cuenta o elimínela en Azure Portal. Obtenga información acerca de las cuentas de almacenamiento estándar y premium."
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 87c37da0-6cc6-4d88-a330-ef2896a1531d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
f1_keywords:
- sql13.swb.windowsazurestorage.connect.f1
ms.date: 01/23/2017
ms.author: robinsh
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 16d9fa8a7b7593f222976897bddf615c28109540
ms.contentlocale: es-es
ms.lasthandoff: 06/20/2017

---
# <a name="about-azure-storage-accounts"></a>Acerca de las cuentas de almacenamiento de Azure
[!INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Información general
Una cuenta de almacenamiento de Azure proporciona un espacio de nombres único para almacenar y tener acceso a los objetos de datos de Almacenamiento de Azure. Todos los objetos de una cuenta de almacenamiento se facturan juntos como un grupo. De forma predeterminada, los datos de su cuenta están disponibles solo para usted, el propietario de la cuenta.

[!INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

## <a name="storage-account-billing"></a>Facturación de la cuenta de almacenamiento
[!INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

> [!NOTE]
> Al crear una máquina virtual de Azure, se crea automáticamente una cuenta de almacenamiento en la ubicación de implementación si todavía no tiene una cuenta de almacenamiento en esa ubicación. Por tanto, no es necesario aplicar los pasos descritos a continuación para crear una cuenta de almacenamiento para los discos de máquinas virtuales. El nombre de la cuenta de almacenamiento se basará en el nombre de la máquina virtual. Consulte la [documentación de máquinas virtuales de Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) para obtener más detalles.
> 
> 

## <a name="storage-account-endpoints"></a>Extremos de la cuenta de almacenamiento
Cada objeto que se almacena en el Almacenamiento de Azure tiene una dirección URL única. El nombre de la cuenta de almacenamiento forma el subdominio de esa dirección. La combinación de nombre de subdominio y dominio, específica de cada servicio, forma un *punto de conexión* para la cuenta de almacenamiento.

Por ejemplo, si la cuenta de almacenamiento se llama *mystorageaccount*, los extremos predeterminados para la cuenta de almacenamiento son:

* Blob service: http://*mystorageaccount*.blob.core.windows.net
* Table service: http://*mystorageaccount*.table.core.windows.net
* Queue service: http://*mystorageaccount*.queue.core.windows.net
* Servicio de archivos: http://*mystorageaccount*.file.core.windows.net

> [!NOTE]
> Una cuenta de Almacenamiento de blobs solo expone el punto de conexión del BLOB.
> 
> 

La dirección URL para el acceso a un objeto en una cuenta de almacenamiento se crea anexando la ubicación del objeto en la cuenta de almacenamiento al extremo. Por ejemplo, una dirección de blob podría tener este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

También puede configurar un nombre de dominio personalizado para usarlo con la cuenta de almacenamiento. En el caso de las cuentas de almacenamiento clásicas, consulte [Configurar un nombre de dominio personalizado para el punto de conexión de Almacenamiento de blobs](storage-custom-domain-name.md) para conocer más detalles. En el caso de las cuentas de almacenamiento de Resource Manager, esta funcionalidad aún no se ha agregado al [Portal de Azure](https://portal.azure.com) , pero se puede configurar con PowerShell. Para más información, consulte el cmdlet [Set-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx) .  

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En el menú central, seleccione **Nuevo** -> **Almacenamiento** -> **Cuenta de almacenamiento**.
3. Escriba un nombre para la cuenta de almacenamiento. Consulte [Puntos de conexión de cuenta de almacenamiento](#storage-account-endpoints) para más información sobre la forma en que se usará el nombre de la cuenta de almacenamiento para dirigir los objetos en Almacenamiento de Azure.
   
   > [!NOTE]
   > Los nombres de cuentas de almacenamiento deben tener entre 3 y 24 caracteres, y solo pueden contener números y letras minúsculas.
   > 
   > El nombre de la cuenta de almacenamiento debe ser único dentro de Azure. El Portal de Azure indicará si el nombre de la cuenta de almacenamiento que seleccione ya está en uso.
   > 
   > 
4. Especifique el modelo de implementación que se va a usar: **Resource Manager** o **Clásico**. **Administrador de recursos** es el modelo de implementación recomendado. Para obtener más información, vea [Descripción de la implementación del Administrador de recursos y la implementación clásica](../azure-resource-manager/resource-manager-deployment-model.md).
   
   > [!NOTE]
   > Las cuentas de Almacenamiento de blobs solo pueden crearse mediante el modelo de implementación de Resource Manager.
   > 
   > 
5. Seleccione el tipo de cuenta de almacenamiento: **Uso general** o **Blob Storage**. **Uso general** es el tipo predeterminado.
   
    Si se ha seleccionado **Uso general**, especifique el nivel de rendimiento: **Estándar** o **Premium**. El valor predeterminado es **Estándar**. Para más información sobre las cuentas de almacenamiento estándar y Premium Storage, consulte [Introducción a Microsoft Azure Storage](storage-introduction.md) y [Premium Storage: almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](storage-premium-storage.md).
   
    Si se ha seleccionado **Blob Storage**, especifique el nivel de acceso: **Frecuente** o **Esporádico**. El valor predeterminado es **Frecuente**. Consulte [Almacenamiento de blobs de Azure: niveles de acceso Esporádico y Frecuente](storage-blob-storage-tiers.md) para más información.
6. Seleccione la opción de replicación de la cuenta de almacenamiento: **LRS**, **GRS**, **RA-GRS** o **ZRS**. El valor predeterminado es **RA-GRS**. Para obtener más información sobre las opciones de replicación del Almacenamiento de Azure, consulte [Replicación de Almacenamiento de Azure](storage-redundancy.md).
7. Seleccione la suscripción en la que desea crear la nueva cuenta de almacenamiento.
8. Especifique un nuevo grupo de recursos o seleccione un grupo de recursos existente. Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
9. Seleccione la ubicación geográfica para la cuenta de almacenamiento. Consulte [Regiones de Azure](https://azure.microsoft.com/regions/#services) para más información acerca de qué servicios están disponibles en cada región.
10. Haga clic en **Crear** para crear la cuenta de almacenamiento.

## <a name="manage-your-storage-account"></a>Administración de la cuenta de almacenamiento
### <a name="change-your-account-configuration"></a>Cambio de la configuración de cuenta
Después de crear la cuenta de almacenamiento, puede modificar su configuración, por ejemplo, cambiar la opción de replicación usada para la cuenta o cambiar el nivel de acceso de una cuenta de Almacenamiento de blobs. En [Azure Portal](https://portal.azure.com), navegue hasta su cuenta de almacenamiento y haga clic en **Configuración** en **CONFIGURACIÓN** para ver o cambiar la configuración de la cuenta.

> [!NOTE]
> Según el nivel de rendimiento que eligió al crear la cuenta de almacenamiento, es posible que algunas opciones de replicación no estén disponibles.
> 
> 

Si se cambia la opción de replicación, cambiarán los precios. Consulte [Precios de Almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/) para más información.

Para las cuentas de Almacenamiento de blobs, cambiar el nivel de acceso puede generar cargos por el cambio, además de cambios en el precio. Consulte [Cuentas de Almacenamiento de blobs: precios y facturación](storage-blob-storage-tiers.md#pricing-and-billing) para más información.

### <a name="manage-your-storage-access-keys"></a>Administración de las claves de acceso de almacenamiento
Al crear una cuenta de almacenamiento, Azure genera dos claves de acceso de almacenamiento de 512 bits que se usan para autenticación cuando se obtiene acceso a la cuenta de almacenamiento. Al brindar dos claves de acceso de almacenamiento, Azure le permite volver a generar las claves sin interrupción en su servicio de almacenamiento, o bien, tener acceso a ese servicio.

> [!NOTE]
> Se recomienda no compartir con nadie las claves de acceso de almacenamiento. Para permitir el acceso a los recursos de almacenamiento sin proporcionar sus claves de acceso, puede usar una *firma de acceso compartido*. Una firma de acceso compartido proporciona acceso a un recurso de su cuenta durante un intervalo que defina y con los permisos que especifique. Consulte [Uso de firmas de acceso compartido (SAS)](storage-dotnet-shared-access-signature-part-1.md) para más información.
> 
> 
<a id="view-and-copy-storage-access-keys"/></a>
#### <a name="view-and-copy-storage-access-keys"></a>Visualización y copia de las claves de acceso de almacenamiento
En [Azure Portal](https://portal.azure.com), vaya a su cuenta de almacenamiento, haga clic en **All settings** (Toda la configuración) y luego en **Claves de acceso** para ver, copiar y volver a generar las claves de acceso de la cuenta. La hoja de **claves de acceso** también incluye cadenas de conexión configuradas previamente que usan claves principales y secundarias que puede copiar para usarlas en las aplicaciones.

#### <a name="regenerate-storage-access-keys"></a>Nueva generación de las claves de acceso de almacenamiento
Recomendamos que cambie las claves de acceso de su cuenta de almacenamiento periódicamente para ayudar a mantener seguras las conexiones de almacenamiento. Se asignan dos claves de acceso para que pueda mantener las conexiones con la cuenta de almacenamiento usando una clave de acceso mientras genera de nuevo la otra clave de acceso.

> [!WARNING]
> La regeneración de las claves de acceso puede afectar a servicios de Azure, así como a sus propias aplicaciones que dependen de la cuenta de almacenamiento. Todos los clientes que usan la clave de acceso para acceder a la cuenta de almacenamiento deben estar actualizados para usar la nueva clave.
> 
> 

**Servicios multimedia** : si tiene servicios multimedia que dependen de su cuenta de almacenamiento, debe volver a sincronizar las claves de acceso con los servicios multimedia después de regenerar las claves.

**Aplicaciones** : si tiene aplicaciones web o servicios en la nube que usan la cuenta de almacenamiento, perderá las conexiones si regenera las claves, a menos que las convierta.

**Exploradores de almacenamiento** : si usa alguna [aplicación exploradora de almacenamiento](storage-explorers.md), es probable que tenga que actualizar la clave de almacenamiento que usan dichas aplicaciones.

Este es el proceso de rotación de las claves de acceso de almacenamiento:

1. Actualice las cadenas de conexión en el código de su aplicación para hacer referencia a la clave de acceso secundaria de la cuenta de almacenamiento.
2. Vuelva a generar la clave de acceso primaria para su cuenta de almacenamiento. En la hoja **Claves de acceso**, haga clic en **Regenerar clave1** y luego haga clic en **Sí** para confirmar que desea generar una nueva clave.
3. Actualice las cadenas de conexión en su código para hacer referencia a la nueva clave de acceso primaria.
4. Vuelva a generar la clave de acceso secundaria de la misma forma.

## <a name="delete-a-storage-account"></a>Eliminar una cuenta de almacenamiento
Para quitar una cuenta de almacenamiento que ha dejado de usarse, navegue a la cuenta de almacenamiento en el [Portal de Azure](https://portal.azure.com)y haga clic en **Eliminar**. Si se elimina la cuenta de almacenamiento, se elimina toda la cuenta, incluidos todos los datos de la cuenta.

> [!WARNING]
> No es posible restaurar una cuenta de almacenamiento eliminada ni recuperar el contenido que contenía antes de la eliminación. Asegúrese de hacer una copia de seguridad de cualquier contenido que desee guardar antes de eliminar la cuenta. Esto también es verdad para los recursos de la cuenta: cuando se elimina un blob, tabla, cola o archivo, este se eliminará definitivamente.
> 
> 

Para eliminar una cuenta de almacenamiento que está asociada a una máquina virtual de Azure, primero debe asegurarse de que se han eliminado los discos de la máquina virtual. Si no elimina primero los discos de las máquinas virtuales, cuando intente eliminar la cuenta de almacenamiento, verá un mensaje de error similar al siguiente:

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

Si la cuenta de almacenamiento utiliza el modelo de implementación clásico, puede quitar el disco de máquina virtual, para lo que debe seguir estos pasos en el [Portal de Azure](https://manage.windowsazure.com):

1. Navegue al [Portal de Azure clásico](https://manage.windowsazure.com).
2. Vaya a la pestaña de máquinas virtuales.
3. Haga clic en la pestaña Discos.
4. Seleccione el disco de datos y luego haga clic en Eliminar disco.
5. Para eliminar imágenes de discos, vaya a la pestaña Imágenes y elimine todas las imágenes almacenadas en la cuenta.

Para más información, consulte la [documentación sobre máquinas virtuales de Azure](http://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="next-steps"></a>Pasos siguientes
* El [Explorador de Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente y gratuita de Microsoft que permite trabajar visualmente con los datos de Azure Storage en Windows, macOS y Linux.
* [Almacenamiento de blobs de Azure: niveles de acceso Esporádico y Frecuente](storage-blob-storage-tiers.md)
* [Replicación de almacenamiento de Azure](storage-redundancy.md)
* [Configuración de las cadenas de conexión de Almacenamiento de Azure](storage-configure-connection-string.md)
* [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md)
* Visite el [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/).


