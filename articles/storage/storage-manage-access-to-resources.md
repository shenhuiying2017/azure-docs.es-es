---
title: "Administración del acceso de lectura anónimo a contenedores y blobs | Microsoft Docs"
description: "Obtenga información acerca de cómo permitir el acceso anónimo a contenedores y blobs y cómo tener acceso a ellos mediante programación."
services: storage
documentationcenter: 
author: tamram
manager: carmonm
editor: tysonn
ms.assetid: a2cffee6-3224-4f2a-8183-66ca23b2d2d7
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2016
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c43677749a80506221a5992d215eddab48cd69c8


---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Administración del acceso de lectura anónimo a contenedores y blobs
## <a name="overview"></a>Información general
De forma predeterminada, solamente el propietario de la cuenta de almacenamiento puede acceder a los recursos de almacenamiento en esa cuenta. Únicamente en el caso del almacenamiento de blobs, puede establecer permisos de un contenedor para permitir el acceso de lectura anónimo al contenedor y sus blobs, de manera que pueda conceder acceso a esos recursos sin compartir la clave de cuenta.

El acceso anónimo es mejor para escenarios donde desea que ciertos blobs estén siempre disponibles para el acceso de lectura anónimo. Para un control más específico, puede crear una firma de acceso compartido, lo que permite delegar el acceso restringido mediante permisos diferentes y en un intervalo de tiempo especificado. Para obtener más información sobre la creación de firmas de acceso compartido, consulte [Uso de firmas de acceso compartido (SAS)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Concesión de permisos a usuarios anónimos a contenedores y blobs
De forma predeterminada, solamente el dueño de la cuenta de almacenamiento puede obtener acceso a un contenedor y a todos los blobs en su interior. Para dar a los usuarios anónimos permisos de lectura para un contenedor y sus blobs, puede establecer los permisos del contenedor de forma que se permita el acceso público. Los usuarios anónimos pueden leer los blobs que estén en un contenedor con acceso público sin necesidad de tener que autenticar la solicitud.

Los contenedores ofrecen las siguientes opciones para administrar el acceso al contenedor:

* **Acceso de lectura público completo** : los datos del contenedor y blobs se pueden leer mediante una solicitud anónima. Los clientes pueden enumerar los blobs del contenedor a través de una solicitud anónima, pero no pueden enumerar los contenedores que están en la cuenta de almacenamiento.
* **Acceso de lectura público solo para blobs** : los datos de blob dentro de este contenedor pueden leerse a través de una solicitud anónima, pero los datos del contenedor no están disponibles. Los clientes no pueden enumerar los blobs incluidos en el contenedor mediante una solicitud anónima.
* **Sin acceso público de lectura** : solamente el propietario de la cuenta puede leer los datos del contenedor y del blob.

Puede establecer los permisos del contenedor de las maneras siguientes:

* Desde el [Portal de Azure](https://portal.azure.com).
* Mediante programación, usando la biblioteca de cliente de almacenamiento o la API de REST.
* Mediante PowerShell. Para obtener información acerca de cómo establecer los permisos del contenedor desde Azure PowerShell, consulte [Usar Azure PowerShell con Almacenamiento de Azure](storage-powershell-guide-full.md#how-to-manage-azure-blobs).

### <a name="setting-container-permissions-from-the-azure-portal"></a>Configuración de los permisos del contenedor desde el Portal de Azure
Para configurar los permisos del contenedor desde el [Portal de Azure](https://portal.azure.com), siga estos pasos:

1. Desplácese hasta el panel de la cuenta de almacenamiento.
2. Seleccione el nombre del contenedor en la lista. Al hacer clic en el nombre se exponen los blobs en el contenedor elegido.
3. Seleccione **Directiva de acceso** en la barra de herramientas.
4. En el campo **Tipo de acceso** , seleccione el nivel de permisos que desee, como se muestra en la captura de pantalla siguiente.
   
    ![Cuadro de diálogo Editar metadatos del contenedor](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="setting-container-permissions-programmatically-using-net"></a>Configuración de permisos de contenedor mediante programación con .NET
Para configurar permisos para un contenedor mediante la biblioteca de cliente de .NET, primero recupere los permisos del contenedor existente mediante una llamada al método **GetPermissions** . A continuación, establezca la propiedad **PublicAccess** para el objeto **BlobContainerPermissions** devuelto por el método **GetPermissions**. Por último, llame al método **SetPermissions** con los permisos actualizados.

El ejemplo siguiente establece los permisos del contenedor para el acceso de lectura público completo. Para establecer permisos de acceso de lectura público solo para blobs, establezca la propiedad **PublicAccess** en **BlobContainerPublicAccessType.Blob**. Para quitar todos los permisos para los usuarios anónimos, establezca la propiedad en **BlobContainerPublicAccessType.Off**.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Acceso anónimo a contenedores y blobs
Un cliente que tiene acceso a contenedores y blobs de forma anónima puede utilizar constructores que no requieren credenciales. En los ejemplos siguientes se muestran varias maneras diferentes de hacer referencia a recursos del servicio BLOB de forma anónima.

### <a name="create-an-anonymous-client-object"></a>Creación de un objeto de cliente anónimo
Puede crear un nuevo objeto de cliente de servicio para el acceso anónimo proporcionando el punto de conexión del servicio BLOB para la cuenta. Sin embargo, también debe conocer el nombre de un contenedor en esa cuenta que esté disponible para el acceso anónimo.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob service endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Referencia a un contenedor de forma anónima
Si tiene la dirección URL de un contenedor que está disponible de forma anónima, puede utilizarla para hacer referencia al contenedor directamente.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```


### <a name="reference-a-blob-anonymously"></a>Referencia a un blob de forma anónima
Si tiene la dirección URL a un blob que está disponible para el acceso anónimo, puede hacer referencia al blob directamente con esa dirección URL:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Características disponibles para los usuarios anónimos
En la siguiente tabla, se indican las operaciones a las que pueden llamar los usuarios anónimos cuando la ACL de un contenedor se establece para permitir un acceso público.

| Operación REST | Permiso con acceso de lectura público completo | Permiso con acceso de lectura público para solo para los blobs |
| --- | --- | --- |
| List Containers |Solo el propietario |Solo el propietario |
| Create Container |Solo el propietario |Solo el propietario |
| Get Container Properties |Todo |Solo el propietario |
| Get Container Metadata |Todo |Solo el propietario |
| Set Container Metadata |Solo el propietario |Solo el propietario |
| Get Container ACL |Solo el propietario |Solo el propietario |
| Set Container ACL |Solo el propietario |Solo el propietario |
| Delete Container |Solo el propietario |Solo el propietario |
| List Blobs |Todo |Solo el propietario |
| Put Blob |Solo el propietario |Solo el propietario |
| Get Blob |Todo |Todo |
| Get Blob Properties |Todo |Todo |
| Set Blob Properties |Solo el propietario |Solo el propietario |
| Get Blob Metadata |Todo |Todo |
| Set Blob Metadata |Solo el propietario |Solo el propietario |
| Put Block |Solo el propietario |Solo el propietario |
| Get Block List (solo bloques confirmados) |Todo |Todo |
| Get Block List (solo bloques sin confirmar o todos los bloques) |Solo el propietario |Solo el propietario |
| Put Block List |Solo el propietario |Solo el propietario |
| Delete Blob |Solo el propietario |Solo el propietario |
| Copia de blobs |Solo el propietario |Solo el propietario |
| Instantánea de blob |Solo el propietario |Solo el propietario |
| Lease Blob |Solo el propietario |Solo el propietario |
| Put Page |Solo el propietario |Solo el propietario |
| Get Page Ranges |Todo |Todo |
| Append Blob |Solo el propietario |Solo el propietario |

## <a name="see-also"></a>Otras referencias
* [Autenticación para los servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* [Uso de Firmas de acceso compartido (SAS)](storage-dotnet-shared-access-signature-part-1.md)
* [Delegación de acceso con una firma de acceso compartido](https://msdn.microsoft.com/library/azure/ee395415.aspx)




<!--HONumber=Nov16_HO3-->


