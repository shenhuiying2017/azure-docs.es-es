
<properties 
  pageTitle="Establecimiento y recuperación de propiedades y metadatos para los objetos contenidos en Almacenamiento de Azure | Microsoft Azure" 
  description="Almacenamiento de metadatos personalizados en los objetos de Almacenamiento de Azure y establecimiento y recuperación de propiedades del sistema." 
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="adinah" 
  editor=""/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="09/03/2015" 
  ms.author="tamram"/>


# Establecer y recuperar propiedades y metadatos #

## Información general

Objetos en las propiedades de compatibilidad de sistema de Almacenamiento de Azure y metadatos definidos por el usuario, además de los datos que contienen:

*   **Propiedades del sistema.** En cada recurso de almacenamiento existen propiedades del sistema. Algunas se pueden leer o establecer, mientras que otras son de solo lectura. En segundo plano, algunas propiedades del sistema corresponden a ciertos encabezados HTTP estándar. La biblioteca de cliente de almacenamiento de Azure las mantiene automáticamente.  

*   **Metadatos definidos por el usuario.** Los metadatos definidos por el usuario son los metadatos que se especifican en un recurso determinado, en forma de par nombre-valor. Puede usar metadatos para almacenar valores adicionales con un recurso de almacenamiento; estos valores son únicamente para sus propios fines y no afectan a la manera en que se comporta el recurso.

## Establecer y recuperar propiedades

El proceso de recuperación de los valores de propiedad y metadatos de un recurso de almacenamiento consta de dos pasos. Para poder leer estos valores, tiene que capturarlos explícitamente llamando el método **FetchAttributes** o **FetchAttributesAsync**.

> [AZURE.IMPORTANT]Los valores de propiedad y los metadatos para un recurso de almacenamiento no se rellenan, a menos que se llame a uno de los métodos **FetchAttributes**.

Para establecer propiedades en un blob, especifique el valor de propiedad y, a continuación, llame al método **SetProperties** o **SetPropertiesAsync**.

En el ejemplo de código siguiente se crea un contenedor y se escriben los valores de propiedad en una ventana de la consola:

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
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

## Establecer y recuperar metadatos

Puede especificar metadatos como uno o más pares nombre-valor en un recurso de blob o contenedor. Para establecer los metadatos, agregue pares nombre-valor a la colección **Metadata** del recurso y, a continuación, llame al método **SetMetadata** para guardar los valores en el servicio.

> [AZURE.NOTE]\: El nombre de los metadatos debe cumplir las convenciones de nomenclatura para los identificadores de C#.
 
Para recuperar metadatos, llame al método **FetchAttributes** en el blob o contenedor para rellenar la colección **Metadata** y, a continuación, lea los valores.

En el ejemplo de código siguiente se crea un nuevo contenedor, se establecen sus metadatos y se vuelven a leer los valores de los metadatos:

         
	// Account name and key.  Modify for your account.
	<span style="color:Blue;">string accountName = <span style="color:#A31515;">"myaccount";
	<span style="color:Blue;">string accountKey = <span style="color:#A31515;">"SzlFqgzqhfkj594cFoveYqCuvo8v9EESAnOLcTBeBIo31p16rJJRZx/5vU/oY3ZsK/VdFNaVpm6G8YSD2K48Nw==";

	// Get a reference to the storage account and client with authentication credentials.
	StorageCredentials credentials = <span style="color:Blue;">new StorageCredentials(accountName, accountKey);
	CloudStorageAccount storageAccount = <span style="color:Blue;">new CloudStorageAccount(credentials, <span style="color:Blue;">true);
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve a reference to a container. 
	CloudBlobContainer container = blobClient.GetContainerReference(<span style="color:#A31515;">"mycontainer");

	// Create the container if it does not already exist.
	container.CreateIfNotExists();

	// Set metadata for the container.
	container.Metadata[<span style="color:#A31515;">"category"] = <span style="color:#A31515;">"images";
	container.Metadata[<span style="color:#A31515;">"owner"] = <span style="color:#A31515;">"azure";
	container.SetMetadata();

	// Get container metadata.
	container.FetchAttributes();
	<span style="color:Blue;">foreach (<span style="color:Blue;">string key <span style="color:Blue;">in container.Metadata.Keys)
	{
	   Console.WriteLine(<span style="color:#A31515;">"Metadata key: " + key);
	   Console.WriteLine(<span style="color:#A31515;">"Metadata value: " + container.Metadata[key]);
	}

	//Clean up.
	container.Delete();

## Otras referencias  

- [Referencia de la biblioteca de cliente de almacenamiento de Azure](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [Introducción al almacenamiento de blobs para .NET](storage-dotnet-how-to-use-blobs.md)  
 

<!---HONumber=Oct15_HO3-->