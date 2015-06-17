<properties 
    pageTitle="Cómo usar el almacenamiento de blobs (C++) | Microsoft Azure" 
    description="Obtenga información sobre cómo usar el servicio de almacenamiento de blobs de Azure. Los ejemplos están escritos en C++." 
    services="storage" 
    documentationCenter=".net" 
    authors="tamram" 
    manager="adinah" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/06/2015" 
    ms.author="tamram"/>

# Cómo usar el almacenamiento de blobs de C++  

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Información general
Esta guía demuestra cómo realizar algunas tareas comunes a través del servicio de almacenamiento de blobs de Azure. Los ejemplos están escritos en C++ y usan la [biblioteca de cliente de almacenamiento de Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/v0.5.0-preview/README.md). Entre los escenarios descritos se incluyen **cargar**, **enumerar**, **descargar** y **eliminar** blobs.

>[AZURE.NOTE]Esta guía se destina a la biblioteca de cliente de almacenamiento de Azure para C++, versión 0.5.0 y posteriores. La versión recomendada es la biblioteca de cliente de almacenamiento 0.5.0, que se encuentra disponible a través de [NuGet](http://www.nuget.org/packages/wastorage) o [GitHub](https://github.com/).

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creación de una aplicación de C++
En esta guía, usará las características de almacenamiento que se pueden ejecutar en una aplicación C++.

Para ello, deberá instalar la biblioteca de cliente de almacenamiento de Azure para C++ y crear una cuenta de almacenamiento de Azure en su suscripción de Azure.

Para instalar la biblioteca de cliente de almacenamiento de Azure para C++, puede usar los métodos siguientes:

-	**Linux:** siga las instrucciones indicadas en la página [Léame de la biblioteca de cliente de almacenamiento de Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).  
-	**Windows:** en Visual Studio, haga clic en **Herramientas > Administrador de paquetes de NuGet > Consola del Administrador de paquetes**. Escriba el siguiente comando en la [Consola del Administrador de paquetes de NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) y presione **ENTRAR**.  

		Install-Package wastorage -Pre

## Configuración de su aplicación para obtener acceso al almacenamiento de blobs  
Agregue las siguientes instrucciones include en la parte superior del archivo C++ en el que desea usar las API de almacenamiento de Azure para obtener acceso a los blobs:

	#include "was/storage_account.h"
	#include "was/blob.h"

## Configuración de una cadena de conexión de almacenamiento de Azure
Un cliente de almacenamiento de Azure usa una cadena de conexión de almacenamiento para almacenar extremos y credenciales con el fin de obtener acceso a los servicios de administración de datos. Al ejecutarse en una aplicación cliente, debe proporcionar la cadena de conexión de almacenamiento en el siguiente formato, usando el nombre de su cuenta de almacenamiento y la clave de acceso de almacenamiento de la cuenta de almacenamiento que se muestra en el Portal de administración para los valores *AccountName* y *AccountKey*. Para obtener información sobre las cuentas de almacenamiento y las claves de acceso, consulte [Acerca de las cuentas de almacenamiento de Azure](storage-create-storage-account.md). En este ejemplo se muestra cómo puede declarar un campo estático para mantener la cadena de conexión:

	// Define the connection-string with your values.
	const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Para probar la aplicación en el equipo local de Windows, puede usar el [emulador de almacenamiento](https://msdn.microsoft.com/library/azure/hh403989.aspx) de Microsoft Azure que se instala con el [SDK de Azure](http://azure.microsoft.com/downloads/). El emulador de almacenamiento es una utilidad que simula los servicios Blob, Cola y Tabla disponibles en Azure en el equipo de desarrollo local. En el ejemplo siguiente se muestra cómo puede declarar un campo estático para mantener la cadena de conexión en el emulador de almacenamiento local:

	// Define the connection-string with Azure Storage Emulator.
	const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Para iniciar el emulador de almacenamiento de Azure, seleccione el botón **Inicio** o presione la tecla **Windows**. Comience a escribir **Emulador de almacenamiento de Azure** y seleccione **Emulador de almacenamiento de Microsoft Azure** en la lista de aplicaciones.

En los ejemplos siguientes se supone que usó uno de estos dos métodos para obtener la cadena de conexión de almacenamiento.

## Recuperación de la cadena de conexión
Puede usar la clase **cloud_storage_account** para representar la información de la cuenta de almacenamiento. Para recuperar la información de la cuenta de almacenamiento a partir de la cadena de conexión de almacenamiento, puede usar el método **parse**.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

A continuación, obtenga una referencia a una clase **cloud_blob_client**, ya que le permite recuperar objetos que representan contenedores y blobs almacenados en el servicio de almacenamiento de blobs. El código siguiente crea un objeto **cloud_blob_client** usando el objeto de cuenta de almacenamiento recuperado anteriormente:

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  

## Cómo crear un contenedor
Cada blob del almacenamiento de Azure debe residir en un contenedor. En este ejemplo se muestra cómo crear un contenedor si todavía no existe:

	try 
	{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
	}
	catch (const std::exception& e)
	{
	std::wcout << U("Error: ") << e.what() << std::endl;
	}  
De manera predeterminada, el nuevo contenedor es privado y debe especificar su clave de acceso de almacenamiento para descargar blobs de él. Si desea poner los archivos (blobs) del contenedor a disposición de todo el mundo, puede convertir el contenedor en público usando el código siguiente:

	// Make the blob container publicly accessible.
	azure::storage::blob_container_permissions permissions;
	permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
	container.upload_permissions(permissions);  

Cualquier usuario de Internet puede ver los blobs de los contenedores públicos, pero solo es posible modificarlos o eliminarlos si se dispone de la clave de acceso apropiada.

## Cómo cargar un blob en un contenedor
El almacenamiento de blobs de Azure admite blobs en bloques y en páginas. En la mayoría de los casos, se recomienda usar blobs en bloques.

Para cargar un archivo en un blob en bloques, obtenga una referencia de contenedor y úsela para obtener una referencia de blob en bloques. Una vez que disponga de la referencia de blob, puede cargar cualquier flujo de datos en ella llamando al método **upload_from_stream**. De este modo, se creará el blob si no existía anteriormente, o bien se sobrescribirá si ya existía. En el siguiente ejemplo se muestra cómo cargar un blob en un contenedor creado anteriormente.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Create or overwrite the "my-blob-1" blob with contents from a local file.
	concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
	blockBlob.upload_from_stream(input_stream);
	input_stream.close().wait();

	// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
	// Retrieve a reference to a blob named "my-blob-2".
	azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
	blob2.upload_text(U("more text"));

	// Retrieve a reference to a blob named "my-blob-3".
	azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
	blob3.upload_text(U("other text"));  

Como alternativa, puede usar el método **upload_from_file** para cargar un archivo en un blob en bloques.

## Cómo enumerar los blobs en un contenedor
Para enumerar los blobs de un contenedor, primero obtenga una referencia de contenedor. A continuación, puede usar el método **list_blobs_segmented** del contenedor para recuperar los blobs y los directorios que contiene. Para obtener acceso a las numerosas propiedades y métodos de un **blob_result_segment** devuelto, debe llamar a la propiedad **blob_result_segment.blobs** para obtener un objeto **cloud_blob**, o bien a la propiedad **blob_result_segment.directories** para obtener un objeto cloud_blob_directory. El código siguiente muestra cómo recuperar y consultar el URI de cada elemento del contenedor**my-sample-container**:

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Loop over items within the container and output the length and URI.
	azure::storage::continuation_token token;
	do
	{
	 azure::storage::blob_result_segment result = container.list_blobs_segmented(token); std::vector<azure::storage::cloud_blob> blobs = result.blobs();
	
	for (std::vector<azure::storage::cloud_blob>::const_iterator it = blobs.cbegin(); it != blobs.cend(); ++it) { std::wcout << U("Blob: ") << it->uri().primary_uri().to_string() << std::endl; }
	
	std::vector<azure::storage::cloud_blob_directory> directories = result.directories();
	
	for (std::vector<azure::storage::cloud_blob_directory>::const_iterator it = directories.cbegin(); it != directories.cend(); ++it) { std::wcout << U("Directory: ") << it->uri().primary_uri().to_string() << std::endl; } token = result.continuation_token(); } while (!token.empty());


## Cómo descargar blobs
Para descargar blobs, primero recupere una referencia de blob y, a continuación, llame al método **download_to_stream**. En el siguiente ejemplo, se usa el método **download_to_stream** para transferir el contenido del blob a un objeto de flujo que, a continuación, puede guardar en un archivo local.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Save blob contents to a file.
	concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
	concurrency::streams::ostream output_stream(buffer);
	blockBlob.download_to_stream(output_stream);

	std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
	std::vector<unsigned char>& data = buffer.collection();
		
	outfile.write((char *)&data[0], buffer.size());
	outfile.close();  

Como alternativa, puede usar el método **download_to_file** para descargar el contenido de un blob en un archivo. También puede usar el método **download_text** para descargar el contenido de un blob en forma de cadena de texto.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-2".
	azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

	// Download the contents of a blog as a text string.
	utility::string_t text = text_blob.download_text();

## Cómo eliminar blobs
Para eliminar un blob, primero obtenga una referencia de blob y, a continuación, llame al método **delete_blob** en ella.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Delete the blob.
	blockBlob.delete_blob();

## Pasos siguientes
Ahora que está familiarizado con los aspectos básicos del almacenamiento de blobs, siga estos vínculos para obtener más información sobre Almacenamiento de Azure.

-	[Cómo usar el almacenamiento de colas de C++](storage-c-plus-plus-how-to-use-queues.md)
-	[Cómo usar el almacenamiento de tablas de C++](storage-c-plus-plus-how-to-use-tables.md)
-	[Biblioteca de cliente de almacenamiento para C++](https://msdn.microsoft.com/library/azure/gg433040.aspx) 
-	[Referencia MSDN de Almacenamiento de Azure](https://msdn.microsoft.com/library/azure/gg433040.aspx)
-	[Documentación de Almacenamiento de Azure](http://azure.microsoft.com/documentation/services/storage/)





<!--HONumber=52-->
 