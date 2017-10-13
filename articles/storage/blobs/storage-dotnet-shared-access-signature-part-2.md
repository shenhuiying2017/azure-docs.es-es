---
title: "Creación y uso de una firma de acceso compartido (SAS) con Azure Blob Storage | Microsoft Docs"
description: "En este tutorial se muestra cómo crear firmas de acceso compartido para su uso con Blob Storage y cómo consumirlas en las aplicaciones cliente."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 491e0b3c-76d4-4149-9a80-bbbd683b1f3e
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: 9dde12acde748c48b56f9f96ee772fca49954358
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Firmas de acceso compartido, Parte 2: Creación y uso de una SAS con Almacenamiento de blobs

[Parte 1](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) de este tutorial se analizaron las firmas de acceso compartido (SAS) y se explicaron los procedimientos recomendados para su uso. En la parte 2 se muestra cómo generar este tipo de firmas para luego usarlas con Almacenamiento de blobs. Los ejemplos están escritos en C# y usan la biblioteca del cliente de almacenamiento de Azure para .NET. En los ejemplos de este tutorial:

* Se generará una firma de acceso compartido en un contenedor.
* Se generará una firma de acceso compartido en un blob.
* Se creará una directiva de acceso almacenada para administrar las firmas en los recursos de un contenedor.
* Se probarán las firmas de acceso compartido en una aplicación cliente.

## <a name="about-this-tutorial"></a>Acerca de este tutorial
En este tutorial, crearemos dos aplicaciones de consola para demostrar la creación y el uso de firmas de acceso compartido para contenedores y blobs:

**Aplicación 1**: la aplicación de administración. Genera una firma de acceso compartido para un contenedor y un blob. Incluye la clave de acceso de la cuenta de almacenamiento en el código fuente.

**Aplicación 2**: la aplicación cliente. Accede a los recursos del contenedor y del blob mediante las firmas de acceso compartido creadas con la primera aplicación. Usa únicamente firmas de acceso compartido para acceder a los recursos del blob y del contenedor; *no* incluye la clave de acceso de la cuenta de almacenamiento.

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>Parte 1: Creación de una aplicación de consola para generar firmas de acceso compartido
En primer lugar, asegúrese de tener instalada la biblioteca del cliente de almacenamiento de Azure para .NET. Puede instalar el [paquete de NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "NuGet package") que contiene los ensamblados más actualizados para la biblioteca de cliente. Este es el método recomendado para garantizar que cuenta con las revisiones más recientes. También puede descargar dicha biblioteca como parte de la última versión de [Azure SDK para .NET](https://azure.microsoft.com/downloads/).

En Visual Studio, cree una aplicación de consola de Windows y denomínela **GenerateSharedAccessSignatures**. Agregue referencias a [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) y [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) mediante uno de los siguientes enfoques:

* Use el [Administrador de paquetes NuGet](https://docs.nuget.org/consume/installing-nuget) de Visual Studio. Seleccione **Proyecto** > **Administrar paquetes NuGet**, busque en línea cada paquete (Microsoft.WindowsAzure.ConfigurationManager y WindowsAzure.Storage) e instálelo.
* Como alternativa, puede buscar estos ensamblados en su instalación del SDK de Azure y agregarles referencias:
  * Microsoft.WindowsAzure.Configuration.dll
  * Microsoft.WindowsAzure.Storage.dll

En la parte superior del archivo Program.cs, agregue las siguientes directivas **using**:

```csharp
using System.IO;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

Edite el archivo app.config para que contenga un valor de configuración con una cadena de conexión que apunte a la cuenta de almacenamiento. El archivo app.config deberá tener un aspecto similar a este:

```xml
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
  </appSettings>
</configuration>
```

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Generación de un URI de firma de acceso compartido para un contenedor
Para comenzar, se agregará un método para generar una firma de acceso compartido en un nuevo contenedor. En este caso, la firma no está asociada a una directiva de acceso almacenada, por lo que incluye en el URI la información que indica su tiempo de expiración y los permisos que concede.

En primer lugar, agregue código al método **Main()** para autenticar el acceso a la cuenta de almacenamiento y crear un contenedor:

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Insert calls to the methods created below here...

    //Require user input before closing the console window.
    Console.ReadLine();
}
```

A continuación, agregue un método que genere la firma de acceso compartido para el contenedor y que devuelva el URI de la firma:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
    //Set the expiry time and permissions for the container.
    //In this case no start time is specified, so the shared access signature becomes valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

Agregue las líneas siguientes en la parte inferior del método **Main()**, antes de la llamada a **Console.ReadLine()**, para llamar a **GetContainerSasUri()** y escribir el URI de la firma en la ventana de la consola:

```csharp
//Generate a SAS URI for the container, without a stored access policy.
Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
Console.WriteLine();
```

Compile y ejecute para generar el URI de la firma de acceso compartido para el nuevo contenedor, que será similar al siguiente:

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D
```

Una vez que se ejecute el código, la firma de acceso compartido creada en el contenedor será válida durante las 24 horas siguientes. Dicha firma concede permiso al cliente para enumerar los blobs en el contenedor y para escribir nuevos blobs en él.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Generación de un URI de firma de acceso compartido para un blob
A continuación, escribiremos código similar al anterior para crear un nuevo blob en el contenedor y generar una firma de acceso compartido para él. Dicha firma no está asociada a una directiva de acceso almacenada, por lo que incluye la hora de inicio, el tiempo de expiración y la información de permisos en el URI.

Agregue un nuevo método para crear un blob y escribir texto en él, y luego generar una firma de acceso compartido y devolver el URI de la firma:

```csharp
static string GetBlobSasUri(CloudBlobContainer container)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature (SAS).";
    blob.UploadText(blobContent);

    //Set the expiry time and permissions for the blob.
    //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
    //The shared access signature will be valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

En la parte inferior del método **Main()**, agregue las líneas siguientes para llamar a **GetBlobSasUri()**, antes de la llamada a **Console.ReadLine()**, y escriba el URI de la firma de acceso compartido en la ventana de la consola:

```csharp
//Generate a SAS URI for a blob within the container, without a stored access policy.
Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
Console.WriteLine();
```

Compile y ejecute para generar el URI de la firma de acceso compartido para el nuevo blob, que será similar al siguiente:

```
https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D
```

### <a name="create-a-stored-access-policy-on-the-container"></a>Creación de una directiva de acceso almacenada en el contenedor
Ahora vamos a crear una directiva de acceso almacenada en el contenedor que definirá las restricciones de las firmas de acceso compartido asociadas.

En los ejemplos anteriores, hemos especificado la hora de inicio (de forma implícita o explícita), el tiempo de expiración y los permisos del URI de la firma de acceso compartido. En los ejemplos siguientes, especificaremos esta información en la directiva de acceso almacenada, no en la firma de acceso compartido. Al hacerlo, podemos cambiar estas restricciones sin necesidad de volver a emitir la firma de acceso compartido.

Es posible tener una o varias restricciones en la firma de acceso compartido y, el resto, en la directiva de acceso almacenada. Sin embargo, solo puede especificar la hora de inicio, la hora de expiración y los permisos en uno de los dos lugares. Por ejemplo, no puede especificar permisos en la firma de acceso compartido y también en la directiva de acceso almacenada.

Al agregar una directiva de acceso almacenada a un contenedor, debe obtener los permisos existentes del contenedor, agregar la nueva directiva de acceso y luego establecer los permisos del contenedor.

Agregue un nuevo método que cree una directiva de acceso almacenada en un contenedor y devuelva el nombre de esta:

```csharp
static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
    string policyName)
{
    //Get the container's existing permissions.
    BlobContainerPermissions permissions = container.GetPermissions();

    //Create a new shared access policy and define its constraints.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    //Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    container.SetPermissions(permissions);
}
```

En la parte inferior del método **Main()**, antes de la llamada a **Console.ReadLine()**, agregue las siguientes líneas para borrar primero cualquier directiva de acceso existente y llame luego al método **CreateSharedAccessPolicy()**:

```csharp
//Clear any existing access policies on container.
BlobContainerPermissions perms = container.GetPermissions();
perms.SharedAccessPolicies.Clear();
container.SetPermissions(perms);

//Create a new access policy on the container, which may be optionally used to provide constraints for
//shared access signatures on the container and the blob.
string sharedAccessPolicyName = "tutorialpolicy";
CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
```

Al borrar las directivas de acceso en un contenedor, primero se obtienen los permisos existentes del contenedor, luego se borran los permisos y después se establecen de nuevo.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Generación de un URI de firma de acceso compartido en el contenedor que usa una directiva de acceso
A continuación, crearemos otra firma de acceso compartido en el contenedor que creamos anteriormente, pero esta vez la asociaremos a la directiva de acceso almacenada creada en el ejemplo anterior.

Agregue un nuevo método para generar otra firma de acceso compartido en el contenedor:

```csharp
static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Generate the shared access signature on the container. In this case, all of the constraints for the
    //shared access signature are specified on the stored access policy.
    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

En la parte inferior del método **Main()**, antes de la llamada a **Console.ReadLine()**, agregue las siguientes líneas para llamar al método **GetContainerSasUriWithPolicy**:

```csharp
//Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Generación de un URI de firma de acceso compartido en el blob que usa una directiva de acceso
Por último, agregaremos un método similar para crear otro blob y generar una firma de acceso compartido que se asocia a una directiva de acceso almacenada.

Agregue un nuevo método para crear un blob y generar una firma de acceso compartido:

```csharp
static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature. " +
    "A stored access policy defines the constraints for the signature.";
    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    ms.Position = 0;
    using (ms)
    {
        blob.UploadFromStream(ms);
    }

    //Generate the shared access signature on the blob.
    string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

En la parte inferior del método **Main()**, antes de la llamada a **Console.ReadLine()**, agregue las siguientes líneas para llamar al método **GetBlobSasUriWithPolicy**:

```csharp
//Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

Ahora, el método **Main()** debe tener este aspecto en su conjunto. Ejecútelo para escribir los URI de firma de acceso compartido en la ventana de la consola y, a continuación, cópielos y péguelos en un archivo de texto para usarlos en la segunda parte de este tutorial.

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    Console.ReadLine();
}
```

Al ejecutar la aplicación de consola GenerateSharedAccessSignatures, verá una salida similar a la siguiente. Estas son las firmas de acceso compartido que usará en la segunda parte de este tutorial.

```
Container SAS URI: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=pFlEZD%2F6sJTNLxD%2FQ26Hh85j%2FzYPxZav6mP1KJwnvJE%3D&se=2017-05-16T16%3A16%3A47Z&sp=wl

Blob SAS URI: https://storagesample.blob.core.windows.net/sascontainer/sasblob.txt?sv=2016-05-31&sr=b&sig=%2FiBWAZbXESzCMvRcm7JwJBK0gT0BtPSWEq4pRwmlBRI%3D&st=2017-05-15T16%3A11%3A48Z&se=2017-05-16T16%3A16%3A48Z&sp=rw

Container SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&si=tutorialpolicy&sig=aMb6rKDvvpfiGVsZI2rCmyUra6ZPpq%2BZ%2FLyTgAeec%2Bk%3D

Blob SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer/sasblobpolicy.txt?sv=2016-05-31&sr=b&si=tutorialpolicy&sig=%2FkTWkT23SS45%2FoF4bK2mqXkN%2BPKs%2FyHuzkfQ4GFoZVU%3D
```

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>Parte 2: Creación de una aplicación de consola para probar las firmas de acceso compartido
Para probar las firmas de acceso compartido creadas en los ejemplos anteriores, crearemos una segunda aplicación de consola que use dichas firmas para realizar operaciones en el contenedor y en un blob.

> [!NOTE]
> Si han transcurrido más de 24 horas desde que se completó la primera parte del tutorial, las firmas generadas ya no serán válidas. En este caso, deberá ejecutar el código de la primera aplicación de consola a fin de generar nuevas firmas de acceso compartido y usarlas en la segunda parte del tutorial.
>

En Visual Studio, cree una nueva aplicación de consola de Windows y denomínela **ConsumeSharedAccessSignatures**. Agregue referencias a [Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) y [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/), igual que hizo anteriormente.

En la parte superior del archivo Program.cs, agregue las siguientes directivas **using**:

```csharp
using System.IO;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

En el cuerpo del método **Main()**, agregue las siguientes constantes de cadena y cambie sus valores por las firmas de acceso compartido generadas en la parte 1 del tutorial.

```csharp
static void Main(string[] args)
{
    const string containerSAS = "<your container SAS>";
    const string blobSAS = "<your blob SAS>";
    const string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    const string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
}
```

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Adición de un método para probar las operaciones del contenedor con una firma de acceso compartido
A continuación, agregaremos un método para probar algunas operaciones del contenedor con una firma de acceso compartido en este último. Dicha firma se usa para devolver una referencia al contenedor y el acceso a este se autentica en función de la firma sola.

Agregue el método siguiente a Program.cs:

```csharp
static void UseContainerSAS(string sas)
{
    //Try performing container operations with the SAS provided.

    //Return a reference to the container using the SAS URI.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

    //Create a list to store blob URIs returned by a listing operation on the container.
    List<ICloudBlob> blobList = new List<ICloudBlob>();

    //Write operation: write a new blob to the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
        string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        blob.UploadText(blobContent);

        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //List operation: List the blobs in the container.
    try
    {
        foreach (ICloudBlob blob in container.ListBlobs())
        {
            blobList.Add(blob);
        }
        Console.WriteLine("List operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("List operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Get a reference to one of the blobs in the container and read it.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        MemoryStream msRead = new MemoryStream();
        msRead.Position = 0;
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            Console.WriteLine(msRead.Length);
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    Console.WriteLine();

    //Delete operation: Delete a blob in the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Actualice el método **Main()** para que llame a **UseContainerSAS()** con las dos firmas de acceso compartido creadas en el contenedor:

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    Console.ReadLine();
}
```

### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Adición de un método para probar operaciones de blob con una firma de acceso compartido
Por último, agregaremos un método para probar algunas operaciones del blob mediante una firma de acceso compartido en este último. En este caso se usa el constructor **CloudBlockBlob(String)** y se pasa la firma de acceso compartido para devolver una referencia al blob. No se requiere ningún otro tipo de autenticación, esta se basa exclusivamente en la firma.

Agregue el método siguiente a Program.cs:

```csharp
static void UseBlobSAS(string sas)
{
    //Try performing blob operations using the SAS provided.

    //Return a reference to the blob using the SAS URI.
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

    //Write operation: Write a new blob to the container.
    try
    {
        string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
        MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        msWrite.Position = 0;
        using (msWrite)
        {
            blob.UploadFromStream(msWrite);
        }
        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Read the contents of the blob.
    try
    {
        MemoryStream msRead = new MemoryStream();
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            msRead.Position = 0;
            using (StreamReader reader = new StreamReader(msRead, true))
            {
                string line;
                while ((line = reader.ReadLine()) != null)
                {
                    Console.WriteLine(line);
                }
            }
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Delete operation: Delete the blob.
    try
    {
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

Actualice el método **Main()** para que llame a **UseBlobSAS()** con las dos firmas de acceso compartido creadas en el blob:

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
    UseBlobSAS(blobSAS);
    UseBlobSAS(blobSASWithAccessPolicy);

    Console.ReadLine();
}
```

Ejecute la aplicación de consola y observe el resultado para ver qué operaciones se permiten para cada firma. El resultado de la ventana de la consola será similar al siguiente:

```
Write operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

List operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

Read operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

Delete operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

...
```

## <a name="next-steps"></a>Pasos siguientes

* [Firmas de acceso compartido, Parte 1: Descripción del modelo de firmas de acceso compartido](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Administración del acceso de lectura anónimo a contenedores y blobs](storage-manage-access-to-resources.md)
* [Delegación de acceso con una firma de acceso compartido (API de REST)](http://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Introducción a las firmas de acceso compartido de tabla y cola](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
