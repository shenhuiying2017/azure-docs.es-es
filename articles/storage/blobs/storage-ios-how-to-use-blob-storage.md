---
title: Uso de Azure Blob Storage en iOS | Microsoft Docs
description: Almacene datos no estructurados en la nube con Almacenamiento de blobs (objetos) de Azure.
services: storage
documentationcenter: ios
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: df188021-86fc-4d31-a810-1b0e7bcd814b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: objective-c
ms.topic: article
ms.date: 05/11/2017
ms.author: michaelhauss
ms.openlocfilehash: f238804e6031fcf3f194695a06bf5b88733a27b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-blob-storage-from-ios"></a>Uso del almacenamiento de blobs desde iOS
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Información general
En este artículo se muestra cómo realizar algunas tareas comunes con el almacenamiento de blobs de Microsoft Azure. Los ejemplos están escritos en Objective-C y usan la [Biblioteca del cliente de almacenamiento de Azure para iOS](https://github.com/Azure/azure-storage-ios). Entre los escenarios descritos se incluyen **cargar**, **enumerar**, **descargar**, y **eliminar** blobs. Para obtener más información acerca de los blobs, consulte la sección [Pasos siguientes](#next-steps) . También puede descargar la [aplicación de ejemplo](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) para ver rápidamente el uso del almacenamiento de Azure en una aplicación de iOS.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importación de la biblioteca de iOS del Almacenamiento de Azure en una aplicación
Puede importar la biblioteca de iOS de Almacenamiento de Azure en la aplicación mediante el [CocoaPod de Almacenamiento de Azure](https://cocoapods.org/pods/AZSClient) o importando el archivo de **marco** . CocoaPod es la manera recomendada ya que facilita la integración de la biblioteca; sin embargo, la importación desde el archivo de plataforma es un método menos intrusivo para su proyecto existente.

Para usar esta biblioteca, necesita lo siguiente:
- iOS 8+
- Xcode 7+

## <a name="cocoapod"></a>CocoaPod
1. Si aún no lo ha hecho, hecho [instale CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) en el equipo abriendo una ventana de terminal y ejecutando el siguiente comando.
    
    ```shell   
    sudo gem install cocoapods
    ```

2. Después, en el directorio del proyecto (el directorio que contiene el archivo .xcodeproj), cree un archivo llamado "_Podfile_" (sin extensión de archivo). Agregue lo siguiente a _Podfile_ y guarde.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. En la ventana terminal, vaya al directorio del proyecto y ejecute el siguiente comando.

    ```shell    
    pod install
    ```

4. Si el archivo .xcodeproj está abierto en Xcode, ciérrelo. En el directorio del proyecto, abra el archivo de proyecto recién creado que tendrá la extensión .xcworkspace. Este es el archivo en el que trabajará a partir de ahora.

## <a name="framework"></a>marco
La otra forma de usar la biblioteca es generar la plataforma manualmente:

1. En primer lugar, descargue o clone el [repositorio azure-storage-ios](https://github.com/azure/azure-storage-ios).
2. Vaya a *azure-storage-ios* -> *Lib* -> *Biblioteca de cliente de Azure Storage* y abra `AZSClient.xcodeproj` en Xcode.
3. En la parte superior izquierda de Xcode, cambie el esquema activo de "Azure Storage Client Library" (Biblioteca de cliente del Almacenamiento de Azure) a "Framework" (Marco).
4. Compile el proyecto. Se crea un archivo `AZSClient.framework` en el escritorio.

Después puede importar el archivo de marco en la aplicación realizando el siguiente procedimiento:

1. Cree un proyecto nuevo o abra un proyecto existente en Xcode.
2. Arrastre y coloque `AZSClient.framework` en su navegador de proyecto de Xcode.
3. Seleccione *Copy items if needed* (Copiar elementos si es necesario) y haga clic en *Finish* (Finalizar).
4. Haga clic en el proyecto en el panel de navegación izquierdo y haga clic en la pestaña *General* de la parte superior del editor de proyectos.
5. En la sección *Linked Frameworks and Libraries* (Bibliotecas y marcos vinculados) sección, haga clic en el botón Agregar (+).
6. En la lista de bibliotecas ya proporcionada, busque `libxml2.2.tbd` y agréguelo al proyecto.

## <a name="import-the-library"></a>Importación de la biblioteca 
```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Si usa Swift, deberá crear un encabezado puente e importar <AZSClient/AZSClient.h> aquí:

1. Cree un archivo de encabezado `Bridging-Header.h` y agregue la instrucción "import" anterior.
2. Vaya a la pestaña *Configuración de compilación* y busque *Objective-C Bridging Header*.
3. Haga doble clic en el campo de *Objective-C Bridging Header* y agregue la ruta de acceso al archivo de encabezado: `ProjectName/Bridging-Header.h`
4. Compile el proyecto (⌘ + B) para comprobar que Xcode seleccionó el encabezado puente.
5. Comience a usar la biblioteca directamente en cualquier archivo Swift, no es necesario para las instrucciones "import".

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Operaciones asincrónicas
> [!NOTE]
> Todos los métodos que realizan una solicitud en el servicio son operaciones asincrónicas. En los ejemplos de código, verá que estos métodos tienen un controlador de finalización. El código de dentro del controlador de finalización se ejecutará **después** de que se haya completado la solicitud. El código posterior al controlador de finalización se ejecutará **mientras** se realiza la solicitud.
> 
> 

## <a name="create-a-container"></a>Crear un contenedor
Todos los blobs del Almacenamiento de Azure deben residir en un contenedor. En el siguiente ejemplo, se muestra cómo crear un contenedor denominado *newcontainer*en su cuenta de almacenamiento (siempre y cuando no exista ya). Al elegir el nombre del contenedor, tenga en cuenta las reglas de nomenclatura mencionadas anteriormente.

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

Para confirmar que esto funciona, examine el [Explorador de Microsoft Azure Storage](http://storageexplorer.com) y compruebe que *newcontainer* está en la lista de contenedores de su cuenta de Storage.

## <a name="set-container-permissions"></a>Establecimiento de los permisos del contenedor
De manera predeterminada, los permisos de un contenedor se configuran para el acceso **Privado** . Sin embargo, los contenedores proporcionan varias opciones diferentes para acceder a ellos:

* **Privado**: el propietario de la cuenta es el único que puede leer los datos del contenedor y de los blobs.
* **Blob**: los datos de los blobs de este contenedor se pueden leer a través de una solicitud anónima, pero los datos del contenedor no están disponibles. Los clientes no pueden enumerar los blobs incluidos en el contenedor mediante una solicitud anónima.
* **Contenedor**: los datos del contenedor y de los blobs se pueden leer mediante una solicitud anónima. Los clientes pueden enumerar los blobs del contenedor a través de una solicitud anónima, pero no pueden enumerar los contenedores que están en la cuenta de almacenamiento.

En el ejemplo siguiente se muestra cómo crear un contenedor con permiso de acceso de **Contenedor** que permita el acceso público, de solo lectura, a todos los usuarios de Internet:

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>Cargar un blob en un contenedor
Tal como se mencionó en la sección [Conceptos del servicio BLOB](#blob-service-concepts) , el almacenamiento de blobs ofrece tres tipos de blob: blob en bloques, blob en anexos y blob en páginas. La biblioteca de iOS de Azure Storage admite los tres tipos de blobs. En la mayoría de los casos, se recomienda usar blobs en bloques.

En el ejemplo siguiente se muestra cómo cargar un blob en bloques de un NSString. Si ya existe un blob con el mismo nombre en el contenedor, se sobrescribirá el contenido de dicho blob.

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

Para confirmar que esto funciona, examine el [Explorador de Microsoft Azure Storage](http://storageexplorer.com) y compruebe que el contenedor, *containerpublic*, contiene el blob *sampleblob*. En este ejemplo, se usa un contenedor público, por lo que para comprobar que esta aplicación ha funcionado puede ir al URI de los blobs:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Además de cargar un blob en bloques desde NSString, hay métodos similares para NSData, NSInputStream o un archivo local.

## <a name="list-the-blobs-in-a-container"></a>Enumerar los blobs de un contenedor
En el ejemplo siguiente se muestra cómo enumerar todos los blobs en un contenedor. Al realizar esta operación, tenga en cuenta los siguientes parámetros:     

* **continuationToken** : el token de continuación representa el lugar en el que debe iniciarse la operación de lista. Si no se proporciona ningún token, los blobs se enumerarán desde el principio. Se puede enumerar cualquier número de blobs, desde cero hasta el máximo configurado. Aunque este método devuelve cero como resultado, si `results.continuationToken` no es nulo, puede haber más blobs en el servicio que no se hayan enumerado.
* **prefix** : puede especificar el prefijo que se va a usar para la lista de blobs. Solo se enumerarán los blobs que comiencen por dicho prefijo.
* **useFlatBlobListing** : como se mencionó en la sección [Asignación de nombres y referencia a contenedores y blobs](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) , aunque el servicio BLOB es un esquema plano de almacenamiento, puede crear una jerarquía virtual asignando a los blobs el nombre de la información de la ruta de acceso. Sin embargo, actualmente no se admiten listas que no sean planas. Esta característica estará disponible próximamente. Por el momento, este valor debe ser **SÍ**.
* **blobListingDetails** : puede especificar qué elementos desea incluir al enumerar los blobs
  * _AZSBlobListingDetailsNone_: solo muestra los blobs confirmados y no devuelve los metadatos de blob.
  * _AZSBlobListingDetailsSnapshots_: muestra los blobs confirmados y las instantáneas de blob.
  * _AZSBlobListingDetailsMetadata_: recupera los metadatos de cada blob que se devuelve.
  * _AZSBlobListingDetailsUncommittedBlobs_: muestra los blobs confirmados y sin confirmar.
  * _AZSBlobListingDetailsCopy_: incluye las propiedades de copia en la lista.
  * _AZSBlobListingDetailsAll_: muestra todos los blobs confirmados, blobs sin confirmar e instantáneas disponibles. Además, se devuelven todos los metadatos y estados de copia de dichos blobs.
* **maxResults** : el número máximo de resultados que se devuelven para esta operación. Utilice -1 para no establecer un límite.
* **completionHandler** : el bloque de código que se ejecutará con los resultados de la operación de lista.

En este ejemplo, se usa un método auxiliar para llamada de forma recursiva el método de blobs de lista cada vez que se devuelve un token de continuación.

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>Descarga de un blob
En el ejemplo siguiente se muestra cómo descargar un blob en un objeto NSString.

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>Eliminar un blob
En el ejemplo siguiente se muestra cómo eliminar un blob.

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>un contenedor de blobs
En el ejemplo siguiente se muestra cómo eliminar un contenedor.

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido cómo utilizar el Almacenamiento de blobs de iOS, siga estos vínculos para más información sobre la biblioteca de iOS y el servicio de almacenamiento.

* [Biblioteca de cliente de almacenamiento de Azure para iOS](https://github.com/azure/azure-storage-ios)
* [Documentación de referencia de iOS del Almacenamiento de Azure](http://azure.github.io/azure-storage-ios/)
* [API de REST de servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage)

Si tiene alguna pregunta sobre esta biblioteca, puede publicarla en el [foro de MSDN Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) o en [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Si quiere sugerir características nuevas para Azure Storage, publíquelas en la página de [comentarios sobre Azure Storage](https://feedback.azure.com/forums/217298-storage/).

