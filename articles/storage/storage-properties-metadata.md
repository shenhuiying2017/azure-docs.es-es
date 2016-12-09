---
title: "Establecimiento y recuperación de propiedades y metadatos para los objetos contenidos en Azure Storage | Microsoft Docs"
description: "Almacenamiento de metadatos personalizados en los objetos de Almacenamiento de Azure y establecimiento y recuperación de propiedades del sistema."
services: storage
documentationcenter: 
author: tamram
manager: carmonm
editor: tysonn
ms.assetid: 036f9006-273e-400b-844b-3329045e9e1f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6e89921509bb273d6d97f829d4867eded20c82bc


---
# <a name="set-and-retrieve-properties-and-metadata"></a>Establecer y recuperar propiedades y metadatos
## <a name="overview"></a>Información general
Objetos en las propiedades de compatibilidad de sistema de Almacenamiento de Azure y metadatos definidos por el usuario, además de los datos que contienen:

* **Propiedades del sistema.**  En cada recurso de almacenamiento existen propiedades del sistema. Algunas se pueden leer o establecer, mientras que otras son de solo lectura. En segundo plano, algunas propiedades del sistema corresponden a ciertos encabezados HTTP estándar. La biblioteca de cliente de almacenamiento de Azure las mantiene automáticamente.  
* **Metadatos definidos por el usuario.**  Los metadatos definidos por el usuario son los metadatos que se especifican en un recurso determinado, en forma de par nombre-valor. Puede usar metadatos para almacenar valores adicionales con un recurso de almacenamiento; estos valores son únicamente para sus propios fines y no afectan a la manera en que se comporta el recurso.  

El proceso de recuperación de los valores de propiedad y metadatos de un recurso de almacenamiento consta de dos pasos. Para poder leer estos valores, tiene que capturarlos explícitamente llamando el método **FetchAttributes** .

> [!IMPORTANT]
> Los valores de propiedad y los metadatos para un recurso de almacenamiento no se rellenan, a menos que se llame a uno de los métodos **FetchAttributes** . 
> 
> 

## <a name="setting-and-retrieving-properties"></a>Establecer y recuperar propiedades
Para recuperar valores de propiedad, llame al método **FetchAttributes** en el blob o contenedor para rellenar las propiedades y, a continuación, lea los valores.

Para establecer propiedades en un objeto, especifique el valor de propiedad y, a continuación, llame al método **SetProperties** .

En el ejemplo de código siguiente se crea un contenedor y se escriben algunos de sus valores de propiedad en una ventana de la consola:

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container. 
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
container.FetchAttributes();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>Establecer y recuperar metadatos
Puede especificar metadatos como uno o más pares nombre-valor en un recurso de blob o contenedor. Para establecer los metadatos, agregue pares nombre-valor a la colección **Metadata** del recurso y, a continuación, llame al método **SetMetadata** para guardar los valores en el servicio.

> [!NOTE]
> El nombre de los metadatos debe cumplir las convenciones de nomenclatura para los identificadores de C#.
> 
> 

El ejemplo de código siguiente establece los metadatos en un contenedor. Un valor se establece mediante el método **Agregar** de la colección. El otro valor se establece mediante la sintaxis implícita de clave/valor. Ambos son válidos.

```csharp
public static void AddContainerMetadata(CloudBlobContainer container)
{
    //Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    //Set the container's metadata.
    container.SetMetadata();
}
```

Para recuperar metadatos, llame al método **FetchAttributes** en el blob o contenedor para rellenar la colección **Metadata** y, a continuación, lea los valores tal como se muestra en el ejemplo siguiente.

```csharp
public static void ListContainerMetadata(CloudBlobContainer container)
{
    //Fetch container attributes in order to populate the container's properties and metadata.
    container.FetchAttributes();

    //Enumerate the container's metadata.
    Console.WriteLine("Container metadata:");
    foreach (var metadataItem in container.Metadata)
    {
        Console.WriteLine("\tKey: {0}", metadataItem.Key);
        Console.WriteLine("\tValue: {0}", metadataItem.Value);
    }
}
```

## <a name="see-also"></a>Otras referencias
* [Documentación de referencia de la biblioteca cliente de Almacenamiento de Azure para .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
* [Paquete de la biblioteca cliente de Almacenamiento de Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) 




<!--HONumber=Nov16_HO3-->


