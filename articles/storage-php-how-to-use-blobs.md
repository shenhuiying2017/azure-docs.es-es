<properties title="How to use blob storage (PHP) - Azure feature guide" pageTitle="How to use blob storage (PHP) | Microsoft Azure" metaKeywords="Azure blob service PHP, Azure blobs PHP" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs. Code samples are written in PHP." documentationCenter="PHP" services="storage" videoId="" scriptId="" solutions="" authors="waltpo" manager="bjsmith" editor="mollybos" />

Uso del servicio BLOB de PHP
============================

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio BLOB de Azure. Los ejemplos están escritos en PHP y utilizan el [SDK de Azure para PHP](http://go.microsoft.com/fwlink/?LinkID=252473). Dichas tareas comunes incluyen **cargar**, **enumerar**, **descargar** y **eliminar** blobs. Para obtener más información acerca de los blobs, consulte la sección [Pasos siguientes](#NextSteps).

Tabla de contenido
------------------

-   [Qué es el almacenamiento de blobs](#what-is)
-   [Conceptos](#concepts)
-   [Creación de una cuenta de almacenamiento de Azure](#CreateAccount)
-   [Creación de una aplicación PHP](#CreateApplication)
-   [Configuración de la aplicación para obtener acceso al servicio BLOB](#ConfigureStorage)
-   [Configuración de una conexión de almacenamiento de Azure](#ConnectionString)
-   [Creación de un contenedor](#CreateContainer)
-   [Carga de un blob en un contenedor](#UploadBlob)
-   [Enumeración de los blobs de un contenedor](#ListBlobs)
-   [Descarga de un blob](#DownloadBlob)
-   [Eliminación de un blob](#DeleteBlob)
-   [Eliminación de un contenedor de blobs](#DeleteContainer)
-   [Pasos siguientes](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Creación de una cuenta de almacenamiento de Azure
-------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Creación de una aplicación PHP
------------------------------

El único requisito a la hora de crear una aplicación PHP para obtener acceso al servicio BLOB de Azure es que el código haga referencia a clases del SDK de Azure para PHP. Puede utilizar cualquier herramienta de desarrollo para crear la aplicación, incluido el Bloc de notas.

En esta guía, utilizará funciones del servicio a las que se puede llamar desde una aplicación PHP localmente o bien mediante código a través de un rol web, rol de trabajo o sitio web de Azure.

Obtención de las bibliotecas de clientes de Azure
-------------------------------------------------

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

Configuración de la aplicación para obtener acceso al servicio BLOB
-------------------------------------------------------------------

Para utilizar las API del servicio BLOB de Azure, necesita:

1.  hacer referencia al archivo autocargador utilizando la instrucción [require\_once](http://php.net/require_once) y
2.  hacer referencia a todas las clases que utilice.

En el siguiente ejemplo se muestra cómo incluir el archivo autocargador y hacer referencia a la clase **ServicesBuilder**.

> [WACOM.NOTE] 
> En este ejemplo (así como en otros ejemplos de este artículo), se asume que ya instaló las bibliotecas de clientes PHP para Azure utilizando el compositor. Si las instaló manualmente o como paquete PEAR, deberá hacer referencia al archivo autocargador `WindowsAzure.php`.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

En los ejemplos que aparecen a continuación, la instrucción `require_once` aparecerá siempre, pero solo se hará referencia a las clases necesarias para la ejecución del ejemplo.

Configuración de una conexión de almacenamiento de Azure
--------------------------------------------------------

Para crear una instancia de un cliente del servicio BLOB de Azure, primero debe disponer de una cadena de conexión válida. El formato de las cadenas de conexión del servicio BLOB es:

Para obtener acceso a un servicio en directo:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Para obtener acceso al emulador de almacenamiento:

    UseDevelopmentStorage=true

Para crear un cliente de cualquier servicio de Azure necesario para utilizar la clase **ServicesBuilder**, puede:

-   pasarle directamente la cadena de conexión, o bien
-   utilizar el **Administrador de configuración de nube (CCM)** (CloudConfigurationManager ) para buscar la cadena de conexión en varios orígenes externos:
    -   De manera predeterminada, admite un origen externo: variables de entorno.
    -   Puede agregar nuevos orígenes ampliando la clase **ConnectionStringSource**.

En los ejemplos descritos aquí, la cadena de conexión se pasará directamente.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

Creación de un contenedor
-------------------------

Los objetos **BlobRestProxy** le permiten crear un contenedor de blobs mediante el método **createContainer**. Al crear un contenedor, puede establecer opciones en él, aunque no es obligatorio. (El ejemplo que aparece a continuación muestra cómo establecer la ACL y los metadatos del contenedor).

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Blob\Models\CreateContainerOptions;
    use WindowsAzure\Blob\Models\PublicAccessType;
    use WindowsAzure\Common\ServiceException;

    // Crear proxy REST de blobs.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    // OPCIONAL: establecer directiva de acceso público y metadatos.
    // Crear objeto de opciones del contenedor.
    $createContainerOptions = new CreateContainerOptions(); 

    // Establecer directiva de acceso público. Los valores posibles son 
    // PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
    // CONTAINER_AND_BLOBS:     
    // habilita el acceso de lectura público completo a los datos del contenedor y los blobs.
    // Los proxys pueden enumerar los blobs incluidos en el contenedor mediante una solicitud 
    // anónima, pero no los contenedores incluidos en la cuenta de almacenamiento.
    //
    // BLOBS_ONLY:
    // habilita el acceso de lectura público a los blobs. Los datos de los blobs de este 
    // contenedor pueden leerse mediante una solicitud anónima, no así los datos del 
    // contenedor. Los proxys no pueden enumerar los blobs incluidos en el contenedor mediante 
    // una solicitud anónima.
    // Si no se especifica este valor en la solicitud, los datos del contenedor son 
    // de acceso privado para el propietario de la cuenta.
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);

    // Establecer los metadatos del contenedor.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    try {
        // Crear el contenedor.
        $blobRestProxy->createContainer("mycontainer", $createContainerOptions);
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Si llama a **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)**, los datos del contenedor y los blobs pasan a ser accesibles mediante solicitudes anónimas. Si llama a **setPublicAccess(PublicAccessType::BLOBS\_ONLY)**, solo los datos de los blobs pasan a ser accesibles mediante solicitudes anónimas. Para obtener más información acerca de las ACL del contenedor, consulte [Set Container ACL (REST API)](http://msdn.microsoft.com/en-us/library/windowsazure/dd179391.aspx).

Para obtener más información acerca de los códigos de error del servicio BLOB, consulte [Blob Service Error Codes](http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx).

Carga de un blob en un contenedor
---------------------------------

Para cargar un archivo en forma de blob, utilice el método **BlobRestProxy-\>createBlockBlob**. Esta operación creará el blob si todavía no existe, o bien lo sobrescribirá si ya existe. En el ejemplo de código que aparece a continuación, se asume que el contenedor ya se creó y se utiliza [fopen](http://www.php.net/fopen) para abrir el archivo como secuencia.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Crear proxy REST de blobs.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    $content = fopen("c:\myfile.txt", "r");
    $blob_name = "myblob";

    try {
        //Cargar blob.
        $blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Observe que en el ejemplo anterior se carga un blob en forma de secuencia. Sin embargo, también es posible cargar un blob en forma de cadena utilizando, por ejemplo, la función [file\_get\_contents](http://php.net/file_get_contents). Para hacerlo, cambie `$content = fopen("c:\myfile.txt", "r");` en el ejemplo anterior por `$content = file_get_contents("c:\myfile.txt");`.

Enumeración de los blobs de un contenedor
-----------------------------------------

Para enumerar los blobs de un contenedor, utilice el método **BlobRestProxy-\>listBlobs** aplicando un bucle **foreach** al resultado. El código siguiente permite obtener en el explorador el nombre y el URI de cada uno de los blobs de un contenedor.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Crear proxy REST de blobs.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Enumerar los blobs.
        $blob_list = $blobRestProxy->listBlobs("mycontainer");
        $blobs = $blob_list->getBlobs();
        
        foreach($blobs as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Descarga de un blob
-------------------

Para descargar un blob, llame al método **BlobRestProxy-\>getBlob** y, a continuación, al método **getContentStream** en el objeto **GetBlobResult** resultante.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Crear proxy REST de blobs.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Obtener blob.
        $blob = $blobRestProxy->getBlob("mycontainer", "myblob");
        fpassthru($blob->getContentStream());
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Observe que en el ejemplo anterior se obtiene un blob en forma de recurso de secuencia (opción predeterminada). Sin embargo, es posible utilizar la función [stream\_get\_contents](http://www.php.net/stream_get_contents) para convertir la secuencia en una cadena.

Eliminación de un blob
----------------------

Para eliminar un blob, pase el nombre del contenedor y del blob a **BlobRestProxy-\>deleteBlob**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Crear proxy REST de blobs.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Eliminar contenedor.
        $blobRestProxy->deleteBlob("mycontainer", "myblob");
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Eliminación de un contenedor de blobs
-------------------------------------

Finalmente, para eliminar un contenedor de blobs, pase el nombre del contenedor a **BlobRestProxy-\>deleteContainer**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Crear proxy REST de blobs.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
        // Eliminar contenedor.
        $blobRestProxy->deleteContainer("mycontainer");
    }
    catch(ServiceException $e){
        // Administrar la excepción a partir de los códigos y mensajes de error.
        // Los códigos y mensajes de error se incluyen aquí: 
        // http://msdn.microsoft.com/en-us/library/windowsazure/dd179439.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Pasos siguientes
----------------

Ahora que está familiarizado con los aspectos básicos del servicio BLOB de Azure, utilice estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx)
-   Obtenga acceso al blog del equipo de almacenamiento de Azure: <http://blogs.msdn.com/b/windowsazurestorage/>
-   Consulte el ejemplo de blob en bloques PHP en <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>.
-   Consulte el ejemplo de blob en páginas PHP en <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>.

