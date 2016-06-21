<properties
	pageTitle="Uso de Almacenamiento de blobs (almacenamiento de objetos) en PHP | Microsoft Azure"
	description="Almacene datos no estructurados en la nube con Almacenamiento de blobs (objetos) de Azure."
	documentationCenter="php"
	services="storage"
	authors="allclark"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
    	ms.date="06/01/2016"
	ms.author="allclark;yaqiyang"/>

# Uso del almacenamiento de blobs de PHP

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Información general

Almacenamiento de blobs de Azure es un servicio que almacena datos no estructurados en la nube como objetos o blobs. El Almacenamiento de blobs puede almacenar cualquier tipo de datos binarios o texto, como un documento, un archivo multimedia o un instalador de aplicación. El Almacenamiento de blobs a veces se conoce como "almacenamiento de objetos".

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio BLOB de Azure. Los ejemplos están escritos en PHP y usan el [SDK de Azure para PHP][download]. Entre los escenarios descritos se incluyen **cargar**, **enumerar**, **descargar** y **eliminar** blobs. Para obtener más información acerca de los blobs, consulte la sección [Pasos siguientes](#next-steps).

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creación de una aplicación PHP

El único requisito a la hora de crear una aplicación PHP para obtener acceso al servicio BLOB de Azure es que el código haga referencia a clases del SDK de Azure para PHP dentro del código. Puede utilizar cualquier herramienta de desarrollo para crear la aplicación, incluido el Bloc de notas.

En esta guía, usará funciones del servicio a las que se puede llamar desde una aplicación PHP localmente o bien mediante código a través de un rol web, rol de trabajo o sitio web de Azure.

## Obtención de las bibliotecas de clientes de Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## Configuración de la aplicación para obtener acceso al servicio BLOB

Para usar las API del servicio BLOB de Azure, necesita:

1. Hacer referencia al archivo autocargador mediante la instrucción [require\_once] y
2. Hacer referencia a todas las clases que utilice.

En el siguiente ejemplo se muestra cómo incluir el archivo autocargador y hacer referencia a la clase **ServicesBuilder**.

> [AZURE.NOTE] En este ejemplo (así como en otros ejemplos de este artículo), se asume que ha instalado las bibliotecas de clientes PHP para Azure mediante el compositor. Si las instaló manualmente, deberá hacer referencia al archivo del cargador automático `WindowsAzure.php`.

	require_once 'vendor/autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


En los ejemplos que aparecen a continuación, la instrucción `require_once` aparecerá siempre, pero solo se hará referencia a las clases necesarias para la ejecución del ejemplo.

## Configuración de una conexión de Almacenamiento de Azure

Para crear una instancia de un cliente del servicio BLOB de Azure, primero debe disponer de una cadena de conexión válida. El formato de las cadenas de conexión del servicio BLOB es:

Para obtener acceso a un servicio en directo:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Para obtener acceso al emulador de almacenamiento:

	UseDevelopmentStorage=true


Para crear un cliente de cualquier servicio de Azure, debe usar la clase **ServicesBuilder**. Puede:

* pasarle directamente la cadena de conexión, o bien
* usar **CloudConfigurationManager (CCM)** para buscar la cadena de conexión en varios orígenes externos:
	* de manera predeterminada, admite un origen externo: variables de entorno.
	* para agregar nuevos orígenes, amplíe la clase **ConnectionStringSource**

En los ejemplos descritos aquí, la cadena de conexión se pasará directamente.

	require_once 'vendor/autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## Crear un contenedor

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Un objeto **BlobRestProxy** le permite crear un contenedor de blobs mediante el método **createContainer**. Al crear un contenedor, puede establecer opciones en él, aunque no es obligatorio. (El ejemplo que aparece a continuación muestra cómo establecer la lista de control de acceso o ACL y los metadatos del contenedor).

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use MicrosoftAzure\Storage\Blob\Models\CreateContainerOptions;
	use MicrosoftAzure\Storage\Blob\Models\PublicAccessType;
	use MicrosoftAzure\Storage\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	// OPTIONAL: Set public access policy and metadata.
	// Create container options object.
	$createContainerOptions = new CreateContainerOptions();

	// Set public access policy. Possible values are
	// PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
	// CONTAINER_AND_BLOBS:
	// Specifies full public read access for container and blob data.
    // proxys can enumerate blobs within the container via anonymous
	// request, but cannot enumerate containers within the storage account.
	//
	// BLOBS_ONLY:
	// Specifies public read access for blobs. Blob data within this
    // container can be read via anonymous request, but container data is not
    // available. proxys cannot enumerate blobs within the container via
	// anonymous request.
	// If this value is not specified in the request, container data is
	// private to the account owner.
	$createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

	// Set container metadata.
	$createContainerOptions->addMetaData("key1", "value1");
	$createContainerOptions->addMetaData("key2", "value2");

	try	{
		// Create container.
		$blobRestProxy->createContainer("mycontainer", $createContainerOptions);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Si llama a **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)**, los datos del contenedor y los blobs pasan a ser accesibles mediante solicitudes anónimas. Si llama a **setPublicAccess(PublicAccessType::BLOBS\_ONLY)**, solo los datos de los blobs pasan a ser accesibles mediante solicitudes anónimas. Para obtener más información acerca de las ACL del contenedor, consulte [Set container ACL (API de REST)][container-acl].

Para obtener más información acerca de los códigos de error del servicio BLOB, consulte [Códigos de error del servicio BLOB][error-codes].

## Cargar un blob en un contenedor

Para cargar un archivo como blob, use el método **BlobRestProxy->createBlockBlob**. De este modo, se creará el blob si no existe, o se sobrescribirá si ya existe. En el ejemplo de código que aparece a continuación, se asume que el contenedor ya se creó y se utiliza [fopen][fopen] para abrir el archivo como secuencia.

	require_once 'vendor/autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use MicrosoftAzure\Storage\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	$content = fopen("c:\myfile.txt", "r");
	$blob_name = "myblob";

	try	{
		//Upload blob
		$blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Observe que en el ejemplo anterior se carga un blob en forma de secuencia. Sin embargo, también es posible cargar un blob en forma de cadena utilizando, por ejemplo, la función [file\_get\_contents][file_get_contents]. Para hacer esto en el ejemplo anterior, cambie `$content = fopen("c:\myfile.txt", "r");` a `$content = file_get_contents("c:\myfile.txt");`.

## Enumerar los blobs de un contenedor

Para enumerar los blobs de un contenedor, utilice el método **BlobRestProxy->listBlobs** aplicando un bucle **foreach** al resultado. El código siguiente permite obtener en el explorador el nombre y el URI de cada uno de los blobs de un contenedor.

	require_once 'vendor/autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use MicrosoftAzure\Storage\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	try	{
		// List blobs.
		$blob_list = $blobRestProxy->listBlobs("mycontainer");
		$blobs = $blob_list->getBlobs();

		foreach($blobs as $blob)
		{
			echo $blob->getName().": ".$blob->getUrl()."<br />";
		}
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


## Descarga de un blob

Para descargar un blob, llame al método **BlobRestProxy->getBlob** y, a continuación, al método **getContentStream** en el objeto **GetBlobResult** resultante.

	require_once 'vendor/autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use MicrosoftAzure\Storage\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	try	{
		// Get blob.
		$blob = $blobRestProxy->getBlob("mycontainer", "myblob");
		fpassthru($blob->getContentStream());
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Observe que en el ejemplo anterior se obtiene un blob en forma de recurso de secuencia (opción predeterminada). Sin embargo, es posible utilizar la función [stream\_get\_contents][stream-get-contents] para convertir la secuencia devuelta en una cadena.

## Eliminar un blob

Para eliminar un blob, pase el nombre del contenedor y del blob a **BlobRestProxy->deleteBlob**.

	require_once 'vendor/autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use MicrosoftAzure\Storage\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	try	{
		// Delete blob.
		$blobRestProxy->deleteBlob("mycontainer", "myblob");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## un contenedor de blobs

Finalmente, para eliminar un contenedor de blobs, pase el nombre del contenedor a **BlobRestProxy->deleteContainer**.

	require_once 'vendor/autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use MicrosoftAzure\Storage\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	try	{
		// Delete container.
		$blobRestProxy->deleteContainer("mycontainer");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del servicio BLOB de Azure, use estos vínculos para obtener más información acerca de tareas de almacenamiento más complejas.

- Visite el [Blog del equipo de Almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/).
- Consulte el [ejemplo de blob en bloques PHP](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php).
- Consulte el [ejemplo de blob en páginas PHP](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php).
- [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md)
 
Para obtener más información, consulte también el [Centro para desarrolladores de PHP](/develop/php/).


[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require\_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents

<!---HONumber=AcomDC_0608_2016-->