---
title: Desarrollo con C++ para Azure Files | Microsoft Docs
description: Aprenda a desarrollar aplicaciones y servicios C++ que usen Azure Files para almacenar datos de archivos.
services: storage
documentationcenter: .net
author: renashahmsft
manager: aungoo
editor: tysonn
ms.assetid: a1e8c99e-47a6-43a9-9541-c9262eb00b38
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: renashahmsft
ms.openlocfilehash: d2f55b5ca6348ba8e190c65ec9a72c6f730d869e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="develop-for-azure-files-with-c"></a>Desarrollo con C++ para Azure Files
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>Acerca de este tutorial

En este tutorial, aprenderá a realizar operaciones básicas en Azure Files. Mediante ejemplos escritos en C++, aprenderá a crear recursos compartidos y directorios, y a cargar, enumerar y eliminar archivos. Si es la primera vez que usa Azure Files, le resultará útil repasar los conceptos de las secciones siguientes para comprender los ejemplos.


* Crear y eliminar recursos compartidos de Azure File
* Crear y eliminar directorios
* Enumerar los archivos y directorios de un recurso compartido de Azure File
* Cargar, descargar y eliminar un archivo
* Establecer la cuota (tamaño máximo) de un recurso compartido de Azure File
* Crear una firma de acceso compartido (clave SAS) para un archivo que utiliza una directiva de acceso compartido definida en el recurso compartido.

> [!Note]  
> Dado que se puede tener acceso a Azure Files a través de SMB, es posible escribir aplicaciones sencillas que tengan acceso al recurso compartido de Azure Files mediante las clases y funciones estándar de E/S de C++. En este artículo se describe cómo escribir aplicaciones que utilizan el SDK de C++ de Azure Storage, que usa la [API de REST de archivos](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) para comunicarse con Azure Files.

## <a name="create-a-c-application"></a>Creación de una aplicación de C++
Para compilar los ejemplos, debe instalar la biblioteca de cliente de Azure Storage 2.4.0 para C++. También deberá haber creado una cuenta de almacenamiento de Azure.

Para instalar el cliente de Azure Storage 2.4.0 para C++, puede usar uno de los métodos siguientes:

* **Linux:** siga las instrucciones indicadas en la página [Léame de la biblioteca de cliente de almacenamiento de Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .
* **Windows:**: en Visual Studio, haga clic en **Herramientas&gt;Administrador de paquetes de NuGet&gt;Consola del Administrador de paquetes**. Escriba el siguiente comando en la [Consola del Administrador de paquetes de NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) y presione **ENTRAR**.
  
```
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-files"></a>Configuración de la aplicación para usar Azure Files
Agregue las siguientes instrucciones include a la parte superior del archivo de origen de C++ en el que desea manipular Azure Files:

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configuración de una cadena de conexión de Almacenamiento de Azure
Para usar el almacenamiento de archivos, necesita conectarse con la cuenta de almacenamiento de Azure. El primer paso sería configurar una cadena de conexión que se usará para establecer la conexión con su cuenta de almacenamiento. Definamos una variable estática para hacerlo.

```cpp
// Define the connection-string with your values.
const utility::string_t 
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Conexión a una cuenta de almacenamiento de Azure
Puede usar la clase **cloud_storage_account** para representar la información de la cuenta de almacenamiento. Para recuperar la información de la cuenta de almacenamiento a partir de la cadena de conexión de almacenamiento, puede usar el método **parse** .

```cpp
// Retrieve storage account from connection string.    
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Creación de un recurso compartido de Azure File
Todos los archivos y directorios de un recurso compartido de Azure Files denominado **Share**. La cuenta de almacenamiento puede tener tantos recursos compartidos como los que permita la capacidad de su cuenta. Para acceder a un recurso compartido y su contenido, es preciso usar un cliente de Azure Files.

```cpp
// Create the Azure Files client.
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();
```

Se usa el cliente de Azure Files para obtener una referencia a un recurso compartido.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

Para crear el recurso compartido, use el método **create_if_not_exists** del objeto **cloud_file_share**.

```cpp
if (share.create_if_not_exists()) {    
    std::wcout << U("New share created") << std::endl;    
}
```

En este punto, **share** contiene una referencia a un recurso compartido denominado **my-sample-share**.

## <a name="delete-an-azure-file-share"></a>Eliminación de un recurso compartido de Azure File
Para eliminar un recurso compartido, realice una llamada al método **delete_if_exists** en un objeto cloud_file_share. A continuación se facilita código de ejemplo que lo hace.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>Creación de directorios
Para organizar el almacenamiento, coloque los archivos en los subdirectorios, en lugar de mantenerlos todos en el directorio raíz. Azure Files permite crear tantos directorios como permita su cuenta. El código siguiente creará un directorio llamado **Mi directorio de ejemplo** bajo el directorio raíz, así como un subdirectorio denominado **mi subdirectorio de ejemplo**.

```cpp
// Retrieve a reference to a directory
azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Return value is true if the share did not exist and was successfully created.
directory.create_if_not_exists();

// Create a subdirectory.
azure::storage::cloud_file_directory subdirectory = 
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
subdirectory.create_if_not_exists();
```

## <a name="delete-a-directory"></a>Eliminación de un directorio
Eliminar un directorio es una tarea sencilla, aunque se debe tener en cuenta que no se puede eliminar un directorio que contenga archivos u otros directorios.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// Get a reference to the directory.
azure::storage::cloud_file_directory directory = 
  share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Get a reference to the subdirectory you want to delete.
azure::storage::cloud_file_directory sub_directory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));

// Delete the subdirectory and the sample directory.
sub_directory.delete_directory_if_exists();

directory.delete_directory_if_exists();
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar los archivos y directorios de un recurso compartido de Azure File
Es fácil obtener una lista de archivos y directorios dentro de un recurso compartido mediante la llamada a **list_files_and_directories** en una referencia de **cloud_file_directory**. Para acceder al conjunto completo de propiedades y métodos de un elemento **list_file_and_directory_item** devuelto, debe llamar al método **list_file_and_directory_item.as_file** para obtener un objeto **cloud_file**, o al método **list_file_and_directory_item.as_directory** para obtener un objeto **cloud_file_directory**.

El código siguiente muestra cómo recuperar y generar el URI de cada elemento en el directorio raíz del recurso compartido.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = 
  share.get_root_directory_reference();

// Output URI of each item.
azure::storage::list_file_and_diretory_result_iterator end_of_results;

for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
{
    if(it->is_directory())
    {
        ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
    else if (it->is_file())
    {
        ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
    }        
}
```

## <a name="upload-a-file"></a>Cargar un archivo
Un recurso compartido de Azure File contiene como mínimo un directorio raíz donde pueden residir los archivos. En esta sección, aprenderá cómo cargar un archivo del almacenamiento local en el directorio raíz de un recurso compartido.

El primer paso para cargar un archivo es obtener una referencia al directorio donde debe residir. Para ello, llame al método **get_root_directory_reference** del objeto de recurso compartido.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

Ahora que tiene una referencia al directorio raíz del recurso compartido, puede cargar en él un archivo. En este ejemplo se carga desde un archivo, desde texto y desde una secuencia.

```cpp
// Upload a file from a stream.
concurrency::streams::istream input_stream = 
  concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

azure::storage::cloud_file file1 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
file1.upload_from_stream(input_stream);

// Upload some files from text.
azure::storage::cloud_file file2 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
file2.upload_text(_XPLATSTR("more text"));

// Upload a file from a file.
azure::storage::cloud_file file4 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
file4.upload_from_file(_XPLATSTR("DataFile.txt"));    
```

## <a name="download-a-file"></a>Descarga de un archivo
Para descargar archivos, primero recupere una referencia de archivo y luego llame al método **download_to_stream** para transferir el contenido del archivo a un objeto de secuencia que luego puede guardar en un archivo local. Como alternativa, puede usar el método **download_to_file** para descargar el contenido de un archivo en un archivo local. También puede usar el método **download_text** para descargar el contenido de un archivo como una cadena de texto.

En el ejemplo siguiente se usan los métodos **download_to_stream** y **download_text** para mostrar la descarga de los archivos que se crearon en las secciones anteriores.

```cpp
// Download as text
azure::storage::cloud_file text_file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
utility::string_t text = text_file.download_text();
ucout << "File Text: " << text << std::endl;

// Download as a stream.
azure::storage::cloud_file stream_file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
stream_file.download_to_stream(output_stream);
std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();
outfile.write((char *)&data[0], buffer.size());
outfile.close();
```

## <a name="delete-a-file"></a>Eliminación de un archivo
Otra operación habitual en Azure Files es la eliminación de archivos. El código siguiente elimina un archivo denominado my-sample-file-3 almacenado en el directorio raíz.

```cpp
// Get a reference to the root directory for the share.    
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

azure::storage::cloud_file_directory root_dir = 
  share.get_root_directory_reference();

azure::storage::cloud_file file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

file.delete_file_if_exists();
```

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Establecer la cuota (tamaño máximo) de un recurso compartido de Azure File
Puede establecer la cuota (o el tamaño máximo) para un recurso compartido de archivos, en gigabytes. También puede comprobar cuántos datos se almacenan actualmente en el recurso compartido.

Al establecer la cuota para un recurso compartido, puede limitar el tamaño total de los archivos almacenados en el recurso compartido. Si el tamaño total de archivos del recurso compartido supera la cuota establecida en el recurso compartido, los clientes no podrán aumentar el tamaño de los archivos existentes ni crear nuevos archivos, a menos que estén vacíos.

En el ejemplo siguiente se muestra cómo comprobar el uso actual de un recurso compartido y cómo establecer la cuota para el recurso compartido.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client.
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();

// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
if (share.exists())
{
    std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();

    //This line sets the quota to 2560GB
    share.resize(2560);

    std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();

}
```

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generar una firma de acceso compartido para un archivo o recurso compartido de archivos
Puede generar una firma de acceso compartido (SAS) para un recurso compartido de archivos o para un archivo individual. También puede crear una directiva de acceso compartido en un recurso compartido de archivos para administrar firmas de acceso compartido. Se recomienda la creación de una directiva de acceso compartido, ya que ofrece un medio de revocar la SAS si esta se encuentra en peligro.

En el ejemplo siguiente se crea una directiva de acceso compartido en un recurso compartido y luego se usa esa directiva para ofrecer las restricciones para una SAS en un archivo del recurso compartido.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client and get a reference to the share
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();

azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

if (share.exists())
{
    // Create and assign a policy
    utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

    azure::storage::file_shared_access_policy sharedPolicy = 
      azure::storage::file_shared_access_policy();

    //set permissions to expire in 90 minutes
    sharedPolicy.set_expiry(utility::datetime::utc_now() + 
       utility::datetime::from_minutes(90));

    //give read and write permissions
    sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

    //set permissions for the share
    azure::storage::file_share_permissions permissions;    

    //retrieve the current list of shared access policies
    azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;

    //add the new shared policy
    policies.insert(std::make_pair(policy_name, sharedPolicy));

    //save the updated policy list
    permissions.set_policies(policies);
    share.upload_permissions(permissions);

    //Retrieve the root directory and file references
    azure::storage::cloud_file_directory root_dir = 
        share.get_root_directory_reference();
    azure::storage::cloud_file file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

    // Generate a SAS for a file in the share 
    //  and associate this access policy with it.        
    utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);

    // Create a new CloudFile object from the SAS, and write some text to the file.        
    azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
    utility::string_t text = _XPLATSTR("My sample content");        
    file_with_sas.upload_text(text);        

    //Download and print URL with SAS.
    utility::string_t downloaded_text = file_with_sas.download_text();        
    ucout << downloaded_text << std::endl;        
    ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;

}
```
## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre Almacenamiento de Azure, consulte los siguientes recursos:

* [Biblioteca de cliente de almacenamiento para C++](https://github.com/Azure/azure-storage-cpp)
* [Ejemplos del servicio Azure Storage File en C++] (https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Explorador de almacenamiento de Azure](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Documentación de Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)