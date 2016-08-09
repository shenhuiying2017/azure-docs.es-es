<properties
    pageTitle="Uso del almacenamiento de blobs de Azure desde iOS | Microsoft Azure"
	description="Almacene datos no estructurados en la nube con Almacenamiento de blobs (objetos) de Azure."
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Objective-C"
    ms.topic="article"
    ms.date="07/27/2016"
    ms.author="micurd"/>

# Uso del almacenamiento de blobs desde iOS

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)] <br/> [AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## Información general

En este artículo se muestra cómo realizar algunas tareas comunes con el almacenamiento de blobs de Microsoft Azure. Los ejemplos están escritos en Objective-C y usan la [Biblioteca del cliente de almacenamiento de Azure para iOS](https://github.com/Azure/azure-storage-ios). Entre los escenarios descritos se incluyen **cargar**, **enumerar**, **descargar** y **eliminar** blobs. Para obtener más información acerca de los blobs, consulte la sección [Pasos siguientes](#next-steps). También puede descargar la [aplicación de ejemplo](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) para ver rápidamente el uso del almacenamiento de Azure en una aplicación de iOS.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Importación de la biblioteca de iOS del Almacenamiento de Azure en una aplicación

Puede importar la biblioteca de iOS de Almacenamiento de Azure en la aplicación mediante el [CocoaPod de Almacenamiento de Azure](https://cocoapods.org/pods/AZSClient) o importando el archivo de **marco**.

## CocoaPod

1. Si aún no lo ha hecho, hecho [instale CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) en el equipo abriendo una ventana de terminal y ejecutando el siguiente comando.

        sudo gem install cocoapods

2. A continuación, en el directorio del proyecto (el directorio que contiene el archivo `.xcodeproj`), cree un nuevo archivo llamado `Podfile` (sin extensión de archivo). Agregue lo siguiente y `Podfile` guarde.

        pod 'AZSClient'

3. En la ventana terminal, vaya al directorio del proyecto y ejecute el siguiente comando.

        pod install

4. Si su `.xcodeproj` está abierta en Xcode, ciérrelo. En el directorio del proyecto, abra el archivo de proyecto recién creado que tendrá la `.xcworkspace` extensión. Este es el archivo en el que trabajará a partir de ahora.

## Marco
Para usar la biblioteca de iOS del Almacenamiento de Azure, primero será preciso compilar el archivo de .NET Framework.

1. En primer lugar, descargue o clone el [repositorio azure-storage-ios](https://github.com/azure/azure-storage-ios).

2. Vaya a *azure-storage-ios* -> *Biblioteca* -> *Biblioteca de cliente del Almacenamiento de Azure* y abra `AZSClient.xcodeproj` en Xcode.

3. En la parte superior izquierda de Xcode, cambie el esquema activo de "Azure Storage Client Library" (Biblioteca de cliente del Almacenamiento de Azure) a "Framework" (Marco).

4. Compile el proyecto. Se creará un archivo `AZSClient.framework` en el escritorio.

Después puede importar el archivo de marco en la aplicación realizando el siguiente procedimiento:

1. Cree un proyecto nuevo o abra un proyecto existente en Xcode.

2. Haga clic en el proyecto en el panel de navegación izquierdo y haga clic en la pestaña *General* de la parte superior del editor de proyectos.

3. En la sección *Linked Frameworks and Libraries* (Bibliotecas y marcos vinculados) sección, haga clic en el botón Agregar (+).

4. Haga clic en *Add Other...* (Agregar otro). Navegue hasta él y agregué el archivo `AZSClient.framework` recién creado.

5. En la sección *Linked Frameworks and Libraries* (Bibliotecas y marcos vinculados) sección, vuelva a hacer clic en el botón Agregar (+).

6. En la lista de bibliotecas ya proporcionada, busque `libxml2.2.dylib` y agréguelo al proyecto.

7. Haga clic en la pestaña *Build Settings* (Configuración de compilación) en la parte superior del editor de proyecto.

8. En la sección *Search Paths* (Rutas de acceso de búsqueda), haga doble clic en *Framework Search Paths* (Rutas de acceso de búsqueda de marco) y agregue la ruta de acceso al archivo `AZSClient.framework`.

## La instrucción import
Será preciso que incluya la siguiente instrucción import en el archivo en el desea invocar la API de Almacenamiento de Azure.

    // Include the following import statement to use blob APIs.
    #import <AZSClient/AZSClient.h>

## Configuración de su aplicación para obtener acceso al almacenamiento de blobs

Hay dos maneras para autenticar una aplicación para tener acceso a los servicios de almacenamiento:

- Clave compartida: la clave compartida solo se usa para realizar pruebas.
- Firma de acceso compartido (SAS): SAS para aplicaciones de producción.

### Clave compartida
La autenticación de clave compartida significa que la aplicación usará el nombre y la clave de la cuenta para tener acceso a los servicios de almacenamiento. Con el fin de mostrar rápidamente cómo usar el almacenamiento de blobs desde iOS, en esta introducción se va a usar la autenticación de clave compartida.

> [AZURE.WARNING (Only use Shared Key authentication for testing purposes!) ] El nombre de cuenta y la clave de cuenta, que ofrecen acceso total de lectura y escritura a la cuenta de almacenamiento asociada, se distribuirán a todas las personas que descarguen la aplicación. Esta práctica **no** se recomienda, ya que se corre el riesgo de que los clientes en los que no se confía pongan la clave en peligro.

Si se usa la autenticación de clave compartida, se creará una cadena de conexión. La cadena de conexión consta de:

- Protocolo **DefaultEndpointsProtocol**: puede elegir si usar HTTP o HTTPS. De todos modos, se recomienda encarecidamente el uso de HTTPS.
- **Nombre de cuenta**: es el nombre de la cuenta de almacenamiento.
- **Clave de cuenta**: si está usando el [Portal de Azure](https://portal.azure.com), diríjase a su cuenta de almacenamiento y haga clic en el icono de **Claves** para acceder a esta información. Si usa el [Portal de Azure clásico](https://manage.windowsazure.com), diríjase a su cuenta de almacenamiento en el portal y haga clic en **Administrar claves de acceso**.

Este es el aspecto que tendrá en su aplicación:

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

### Firmas de acceso compartido (SAS)
En el caso de una aplicación de iOS, el método recomendado para autenticar una solicitud por parte de un cliente con el almacenamiento de blobs es mediante el uso de una firma de acceso compartido (SAS). SAS permite conceder a los clientes acceso a un recurso durante un período especificado, con un conjunto de permisos especificado. Como propietario de cuenta de almacenamiento, será preciso que genere una SAS para que los clientes de iOS la consuman. Para generar la SAS, puede escribir un servicio independiente que genere la SAS que se va a distribuir a los clientes. Para las pruebas, puede usar el Explorador de almacenamiento de Microsoft Azure para generar una SAS. Al crear la SAS, se puede especificar el intervalo de tiempo durante el que la SAS es válida y los permisos la SAS concede al cliente.

El siguiente ejemplo muestra cómo usar el Explorador de almacenamiento de Microsoft Azure para generar una SAS.

1. Si no lo ha hecho ya, [instale el Explorador de almacenamiento de Microsoft Azure](http://storageexplorer.com).

2. Conexión a su suscripción

3. Haga clic en la cuenta de almacenamiento y haga clic en la pestaña "Acciones" en la parte inferior izquierda. Haga clic en "Get Shared Access Signature" (Obtener firma de acceso compartido) para generar una "cadena de conexión" para la SAS.

4. Este es un ejemplo de una cadena de conexión de SAS que concede permisos de lectura y escritura en el nivel de servicio, contenedor y objeto para el servicio Blob de la cuenta de almacenamiento.

        SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net

6. En la aplicación de iOS, ahora puede obtener una referencia a la cuenta usando la cadena de conexión de la siguiente manera:

		// Get a reference to your Storage account
    	AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net" error:&accountCreationError];

Como puede ver, cuando se usa una SAS, no expone el nombre y la clave de cuenta en la aplicación de iOS. Para más información sobre SAS, consulte [Firmas de acceso compartido, Parte 1: Descripción del modelo SAS](storage-dotnet-shared-access-signature-part-1.md).

## Operaciones asincrónicas
> [AZURE.NOTE] Todos los métodos que realizan una solicitud en el servicio son operaciones asincrónicas. En los ejemplos de código, encontrará que estos métodos tienen un controlador de finalización. El código de dentro del controlador de finalización se ejecutará **después** de que se haya completado la solicitud. El código posterior al controlador de finalización se ejecutará **mientras** se realiza la solicitud.

## Crear un contenedor
Todos los blobs del Almacenamiento de Azure deben residir en un contenedor. En el siguiente ejemplo, se muestra cómo crear un contenedor denominado *newcontainer* en su cuenta de almacenamiento (siempre y cuando no exista ya). Al elegir el nombre del contenedor, tenga en cuenta las reglas de nomenclatura mencionadas anteriormente.

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

Para confirmar que esto funciona, examine el [Explorador de almacenamiento de Microsoft Azure](http://storageexplorer.com) y compruebe que *newcontainer* está en la lista de contenedores de su cuenta de almacenamiento.

## Establecimiento de los permisos del contenedor
De manera predeterminada, los permisos de un contenedor se configuran para el acceso **Privado**. Sin embargo, los contenedores proporcionan varias opciones diferentes para acceder a ellos:

- **Privado**: el propietario de la cuenta es el único que puede leer los datos del contenedor y de los blobs.

- **Blob**: los datos de los blobs de este contenedor se pueden leer a través de una solicitud anónima, pero los datos del contenedor no están disponibles. Los clientes no pueden enumerar los blobs incluidos en el contenedor mediante una solicitud anónima.

- **Contenedor**: los datos del contenedor y de los blobs se pueden leer mediante una solicitud anónima. Los clientes pueden enumerar los blobs del contenedor a través de una solicitud anónima, pero no pueden enumerar los contenedores que están en la cuenta de almacenamiento.

En el ejemplo siguiente se muestra cómo crear un contenedor con permiso de acceso de **Contenedor** que permita el acceso público, de solo lectura, a todos los usuarios de Internet:

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

## Cargar un blob en un contenedor
Tal como se mencionó en la sección [Conceptos del servicio BLOB](#blob-service-concepts), el almacenamiento de blobs ofrece tres tipos de blob: blob en bloques, blob en anexos y blob en páginas. En este momento, la biblioteca de iOS del Almacenamiento de Azure solo admite blobs en bloques. En la mayoría de los casos, se recomienda usar blobs en bloques.

En el ejemplo siguiente se muestra cómo cargar un blob en bloques de un NSString. Si ya existe un blob con el mismo nombre en el contenedor, se sobrescribirá el contenido de dicho blob.

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

Para confirmar que esto funciona, examine el [Explorador de almacenamiento de Microsoft Azure](http://storageexplorer.com) y compruebe que el contenedor, *containerpublic*, contiene el blob *sampleblob*. En este ejemplo, se usa un contenedor público, por lo que para comprobar que esto ha funcionado puede ir al URI de los blobs:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Además de cargar un blob en bloques de un NSString, hay métodos similares para NSData, NSInputStream o un archivo local.

## Enumerar los blobs de un contenedor
En el ejemplo siguiente se muestra cómo enumerar todos los blobs en un contenedor. Al realizar esta operación, tenga en cuenta los siguientes parámetros:

- **continuationToken**: el token de continuación representa el lugar en el que debe iniciarse la operación de lista. Si no se proporciona ningún token, los blobs se enumerarán desde el principio. Se puede enumerar cualquier número de blobs, desde cero hasta el máximo configurado. Aunque este método devuelve cero como resultado, si `results.continuationToken` no es nulo, puede haber más blobs en el servicio que no se hayan enumerado.
- **prefix**: puede especificar el prefijo que se va a usar para la lista de blobs. Solo se enumerarán los blobs que comiencen por dicho prefijo.
- **useFlatBlobListing**: como se mencionó en la sección [Asignación de nombres y referencia a contenedores y blobs](#naming-and-referencing-containers-and-blobs), aunque el servicio BLOB es un esquema plano de almacenamiento, puede crear una jerarquía virtual asignando a los blobs el nombre de la información de la ruta de acceso. Sin embargo, actualmente no se admiten listas que no sean planas, pero se admitirán pronto. Por el momento, este valor debe ser `YES`
- **blobListingDetails**: puede especificar qué elementos desea incluir al enumerar los blobs
	- `AZSBlobListingDetailsNone`: se enumeran solo los blobs confirmados y no se devuelven los metadatos de los blobs.
	- `AZSBlobListingDetailsSnapshots`: se enumeran los blobs confirmados y las instantáneas de los blobs.
	- `AZSBlobListingDetailsMetadata`: se recuperan los metadatos de los blobs que se devuelven en la lista.
	- `AZSBlobListingDetailsUncommittedBlobs`: se enumeran los blobs confirmados y sin confirmar.
	- `AZSBlobListingDetailsCopy`: se incluyen propiedades de copia en la lista.
	- `AZSBlobListingDetailsAll`: se enumeran todos los blobs confirmados, blobs sin confirmar e instantáneas disponibles y se devuelven todos los metadatos y estados de copia de dichos blobs.
- **maxResults**: el número máximo de resultados que se devuelven para esta operación. Utilice -1 para no establecer un límite.
- **completionHandler**: el bloque de código que se ejecutará con los resultados de la operación de lista.

En este ejemplo, se usa un método auxiliar para llamada de forma recursiva el método de blobs de lista cada vez que se devuelve un token de continuación.

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


## Descarga de un blob

En el ejemplo siguiente se muestra cómo descargar un blob en un objeto NSString.

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

## Eliminar un blob

En el ejemplo siguiente se muestra cómo eliminar un blob.

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

## un contenedor de blobs

En el ejemplo siguiente se muestra cómo eliminar un contenedor.

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

## Pasos siguientes

Ahora que ha aprendido cómo utilizar el Almacenamiento de blobs de iOS, siga estos vínculos para más información sobre la biblioteca de iOS y el servicio de almacenamiento.

- [Biblioteca de cliente de almacenamiento de Azure para iOS](https://github.com/azure/azure-storage-ios)
- [Documentación de referencia de iOS del Almacenamiento de Azure](http://azure.github.io/azure-storage-ios/)
- [API de REST de servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage)

Si tiene alguna pregunta sobre esta biblioteca, puede publicarla en el [foro de MSDN Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) o en [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files). Si quiere sugerir características nuevas para el Almacenamiento de Azure, publíquelas en la página de [comentarios sobre el Almacenamiento de Azure](https://feedback.azure.com/forums/217298-storage/).

<!---HONumber=AcomDC_0727_2016-->