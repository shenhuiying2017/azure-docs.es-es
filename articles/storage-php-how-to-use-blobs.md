<properties title="How to use blob storage (PHP) - Azure feature guide" pageTitle="Uso del almacenamiento de blobs (PHP) | Microsoft Azure" metaKeywords="Azure blob service PHP, Azure blobs PHP" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs. Code samples are written in PHP." documentationCenter="PHP" services="storage" videoId="" scriptId="" solutions="" authors="tomfitz" manager="wpickett" editor="mollybos" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/24/2014" ms.author="tomfitz" />

#Uso del servicio BLOB de PHP

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio BLOB de Azure. Los ejemplos están escritos en PHP y utilizan el [SDK de Azure para PHP] [descargar]. Los escenarios cubiertos incluyen **cargar**, **escuchar**, **descargar** y **eliminar** blobs. Para obtener más información acerca de los blobs, consulte la sección [Pasos siguientes](#NextSteps) .

##Tabla de contenido

* [Qué es el almacenamiento de blobs](#what-is)
* [Conceptos](#concepts)
* [Creación de una cuenta de almacenamiento de Azure](#CreateAccount)
* [Creación de una aplicación PHP](#CreateApplication)
* [Configuración de la aplicación para obtener acceso al servicio BLOB](#ConfigureStorage)
* [Configuración de una conexión de almacenamiento de Azure](#ConnectionString)
* [Recuperación de de un contenedor](#CreateContainer)
* [Recuperación de de un blob en un contenedor](#UploadBlob)
* [Escucha de los blobs de un contenedor](#ListBlobs)
* [Descarga de un blob](#DownloadBlob)
* [Descarga de un blob](#DeleteBlob)
* [Eliminación de un contenedor de blobs](#DeleteContainer)
* [Pasos siguientes](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a id="CreateAccount"></a>Crear una cuenta de almacenamiento de Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a id="CreateApplication"></a>Creación de una aplicación PHP</h2>

El único requisito a la hora de crear una aplicación PHP para obtener acceso al servicio BLOB de Azure es que el código haga referencia a clases del SDK de Azure para PHP. Puede utilizar cualquier herramienta de desarrollo para crear la aplicación, incluido el Bloc de notas.

En esta guía, utilizará funciones del servicio a las que se puede llamar desde una aplicación PHP localmente o bien mediante código a través de un rol web, rol de trabajo o sitio web de Azure.

<h2><a id="GetClientLibrary"></a>Obtención de las bibliotecas de clientes de Azure</h2>

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

<h2><a id="ConfigureStorage"></a>Configuración de la aplicación para obtener acceso al servicio BLOB</h2>

Para utilizar las API del servicio BLOB de Azure, necesita:

1. Hacer referencia al archivo autocargador mediante la instrucción [require_once][require_once] y
2. Hacer referencia a todas las clases que utilice.

En el siguiente ejemplo se muestra cómo incluir el archivo autocargador y hacer referencia a la clase **ServicesBuilder**.

> [WACOM.NOTE]
>  En este ejemplo (así como en otros ejemplos de este artículo), se asume que ya instaló las bibliotecas de clientes PHP para Azure utilizando el compositor. Si las instaló manualmente o como paquete PEAR, deberá hacer referencia al archivo autocargador "WindowsAzure.php".

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


En los ejemplos que aparecen a continuación, la instrucción "require_once" aparecerá siempre, pero solo se hará referencia a las clases necesarias para la ejecución del ejemplo.

<h2><a id="ConnectionString"></a>Configuración de una conexión de almacenamiento de Azure</h2>

Para crear una instancia de un cliente del servicio BLOB de Azure, primero debe disponer de una cadena de conexión válida. El formato de las cadenas de conexión del servicio BLOB es:

Para obtener acceso a un servicio en directo:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Para obtener acceso al emulador de almacenamiento:

	UseDevelopmentStorage=true


Para crear un cliente de cualquier servicio de Azure necesario para utilizar la clase **ServicesBuilder**. puede:

* pasarle directamente la cadena de conexión, o bien
* utilizar el **Administrador de configuración de nube (CCM)** (CloudConfigurationManager) para buscar la cadena de conexión en varios orígenes externos:
	* De manera predeterminada, admite un origen externo: variables de entorno.
	* Puede agregar nuevos orígenes ampliando la clase **ConnectionStringSource**

En los ejemplos descritos aquí, la cadena de conexión se pasará directamente.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

<h2><a id="CreateContainer"></a>Reproducción de Creación de un contenedor</h2>

Un objeto **BlobRestProxy** le permiten crear un contenedor de blobs mediante el método **createContainer**. Al crear un contenedor, puede establecer opciones en él, aunque no es obligatorio. (El ejemplo que aparece a continuación muestra cómo establecer la ACL y los metadatos del contenedor).

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Blob\Models\CreateContainerOptions;
	use WindowsAzure\Blob\Models\PublicAccessType;
	use WindowsAzure\Common\ServiceException;

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
	
	// Set container metadata
	$createContainerOptions->addMetaData("key1", "value1");
	$createContainerOptions->addMetaData("key2", "value2");
	
	try	{
		// Create container.
		$blobRestProxy->createContainer("mycontainer", $createContainerOptions);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Si llama a **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)**, los datos del contenedor y los blobs pasan a ser accesibles mediante solicitudes anónimas. Si llama a **setPublicAccess(PublicAccessType::BLOBS_ONLY)**, solo los datos de los blobs pasan a ser accesibles mediante solicitudes anónimas. Para obtener más información acerca de las ACL del contenedor, consulte [Definir la ACL del contenedor (API de REST)][container-acl].

Para obtener más información acerca de los códigos de error del servicio BLOB, consulte [Códigos de error del servicio BLOB][error-codes].

<h2><a id="UploadBlob"></a>Reproducción de un blob en un contenedor</h2>

Para cargar un archivo en forma de blob, utilice el método **BlobRestProxy->createBlockBlob**. De este modo, se creará el blob si no existe, o bien se sobrescribirá si ya existe. En el ejemplo de código que aparece a continuación, se asume que el contenedor ya se creó y se utiliza [fopen][fopen] para abrir el archivo como secuencia.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

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
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Observe que en el ejemplo anterior se carga un blob en forma de secuencia. Sin embargo, también es posible cargar un blob en forma de cadena utilizando, por ejemplo, la función [file\_get\_contents][file_get_contents]. Para hacerlo, cambie "$content = fopen("c:\myfile.txt", "r");" en el ejemplo anterior por "$content = file_get_contents("c:\myfile.txt");".

<h2><a id="ListBlobs"></a>Reproducción de los blobs de un contenedor</h2>

Para enumerar los blobs de un contenedor, utilice el método **BlobRestProxy->listBlobs** aplicando un bucle **foreach** al resultado. El código siguiente permite obtener en el explorador el nombre y el URI de cada uno de los blobs de un contenedor.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

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
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


<h2><a id="DownloadBlob"></a>Descarga de un blob</h2>

Para descargar un blob, llame al método **BlobRestProxy->getBlob** y, a continuación, al método **getContentStream** en el objeto **GetBlobResult** resultante.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

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
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Observe que en el ejemplo anterior se obtiene un blob en forma de recurso de secuencia (opción predeterminada). Sin embargo, es posible utilizar la función [stream\_get\_contents][stream-get-contents] para convertir la secuencia en una cadena.

<h2><a id="DeleteBlob"></a>Reproducción de un blob</h2>

Para eliminar un blob, pase el nombre del contenedor y del blob a **BlobRestProxy->deleteBlob**. 

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Delete container.
		$blobRestProxy->deleteBlob("mycontainer", "myblob");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="DeleteContainer"></a>Reproducción de un contenedor de blobs</h2>

Finalmente, para eliminar un contenedor de blobs, pase el nombre del contenedor a **BlobRestProxy->deleteContainer**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Delete container.
		$blobRestProxy->deleteContainer("mycontainer");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="NextSteps"></a>Pasos siguientes</h2>

Ahora que está familiarizado con los aspectos básicos del servicio BLOB de Azure, utilice estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

- Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure] []
- Obtenga acceso al blog del equipo de almacenamiento de Azure: <http://blogs.msdn.com/b/windowsazurestorage/>
- Consulte el ejemplo de blob en bloques PHP en <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>.
- Consulte el ejemplo de blob en páginas PHP en <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[Storing and Accessing Data in Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
[container-acl]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents

<!--HONumber=35.1-->
