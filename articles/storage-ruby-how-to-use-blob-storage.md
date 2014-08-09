<properties linkid="dev-ruby-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Ruby) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Ruby" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Ruby." metaCanonical="" services="storage" documentationCenter="Ruby" title="How to Use the Blob Service from Ruby" authors="guayan" solutions="" manager="" editor="" />

Uso del servicio BLOB desde Ruby
================================

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio BLOB de Azure. Los ejemplos están escritos usando la API Ruby. Dichas tareas comunes incluyen **cargar, enumerar, descargar** y **eliminar** blobs. Para obtener más información acerca de los blobs, consulte la sección [Pasos siguientes](#next-steps).

Tabla de contenido
------------------

-   [¿Qué es el servicio BLOB?](#what-is)
-   [Conceptos](#concepts)
-   [Creación de una cuenta de almacenamiento de Azure](#CreateAccount)
-   [Creación de una aplicación de Ruby](#CreateRubyApp)
-   [Configuración de su aplicación para obtener acceso al almacenamiento](#ConfigAccessStorage)
-   [Configuración de una conexión de almacenamiento de Azure](#SetupStorageConnection)
-   [Creación de un contenedor](#CreateContainer)
-   [Carga de un blob en un contenedor](#UploadBlob)
-   [Enumeración de los blobs de un contenedor](#ListBlobs)
-   [Descarga de blobs](#DownloadBlobs)
-   [Eliminación de un blob](#DeleteBlob)
-   [Pasos siguientes](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Creación de una cuenta de almacenamiento de Azure
-------------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Creación de una aplicación de Ruby
----------------------------------

Cree una aplicación de Ruby. Para obtener instrucciones, consulte [Creación de una aplicación de Ruby en Azure](/es-es/develop/ruby/tutorials/web-app-with-linux-vm/).

Configuración de su aplicación para obtener acceso al almacenamiento
--------------------------------------------------------------------

Para usar el almacenamiento de Azure tendrá que descargar y usar el paquete Ruby azure, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### Uso de RubyGems para obtener el paquete

1.  Utilice una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).

2.  Escriba "gem install azure" en la ventana de comandos para instalar la gema y las dependencias.

### Importación del paquete

Con el editor de texto que prefiera, agregue lo siguiente al principio del archivo de Ruby en el que pretenda utilizar el almacenamiento:

    require "azure"

Configuración de una conexión de almacenamiento de Azure
--------------------------------------------------------

El módulo azure leerá las variables de entorno **AZURE\_STORAGE\_ACCOUNT** y **AZURE\_STORAGE\_ACCESS\_KEY** para obtener la información necesaria para conectarse a la cuenta de almacenamiento de Azure. Si no se establecen estas variables de entorno, debe especificar la información de la cuenta antes de usar **Azure::BlobService** con el siguiente código:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

Para obtener estos valores:

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).
2.  Vaya a la cuenta de almacenamiento que desea utilizar.
3.  Haga clic en **MANAGE KEYS** en la parte inferior del panel de navegación.
4.  En el cuadro de diálogo emergente, verá el nombre de cuenta de almacenamiento, la clave de acceso principal y la clave de acceso secundaria. Para la clave de acceso, puede elegir la principal o la secundaria.

Creación de un contenedor
-------------------------

El objeto **Azure::BlobService** le permite trabajar con contenedores y blobs. Para crear un contenedor, utilice el método **create\_container()**.

En el siguiente ejemplo se crea un contenedor o se imprime el error, si hay alguno.

    azure_blob_service = Azure::BlobService.new
    begin
      container = azure_blob_service.create_container("test-container")
    rescue
      puts $!
    end

Si desea hacer públicos los archivos del contenedor, debe configurar los permisos del contenedor.

Simplemente puede modificar la llamada **create\_container()** para pasar la opción **:public\_access\_level**:

    container = azure_blob_service.create_container("test-container", 
      :public_access_level => "<public access level>")

Algunos valores válidos para la opción **:public\_access\_level** son:

-   **blob:** Habilita el acceso de lectura público completo a los datos del contenedor y los blobs. Los clientes pueden enumerar los blobs del contenedor a través de una solicitud anónima, pero no pueden enumerar los contenedores que están en la cuenta de almacenamiento.

-   **container:** Habilita el acceso de lectura público a los blobs. Los datos de blob dentro de este contenedor pueden leerse a través de una solicitud anónima, pero los datos del contenedor no están disponibles. Los clientes no pueden enumerar los blobs incluidos en el contenedor mediante una solicitud anónima.

También puede modificar el nivel de acceso público de un contenedor usando el método **set\_container\_acl()** para especificarlo.

El ejemplo siguiente cambia el nivel de acceso público a **container**:

    azure_blob_service.set_container_acl('test-container', "container")

Carga de un blob en un contenedor
---------------------------------

Para cargar contenido a un blob, utilice el método **create\_block\_blob()** para crear el blob, use un archivo o una cadena como el contenido del blob.

El siguiente código cargará el archivo **test.png** como un blob nuevo llamado "image-blob" en el contenedor.

    content = File.open("test.png", "rb") { |file| file.read }
    blob = azure_blob_service.create_block_blob(container.name,
      "image-blob", content)
    puts blob.name

Enumeración de los blobs de un contenedor
-----------------------------------------

Para enumerar los contenedores, use el método **list\_containers()**. Para enumerar los blobs de un contenedor, use el método **list\_blobs()**.

Esta acción obtiene como resultado las URL de todos los blobs en todos los contenedores para la cuenta.

    containers = azure_blob_service.list_containers()
    containers.each do |container|
      blobs = azure_blob_service.list_blobs(container.name)
      blobs.each do |blob|
        puts blob.name
      end
    end

Descarga de blobs
-----------------

Para descargar blobs, utilice el método **get\_blob()** para recuperar el contenido.

El ejemplo siguiente muestra cómo utilizar **get\_blob()** para descargar el contenido de "image-blob" y escribirlo en un archivo local.

    blob, content = azure_blob_service.get_blob(container.name,"image-blob")
    File.open("download.png","wb") {|f| f.write(content)}

Eliminación de un blob
----------------------

Finalmente, para eliminar un blob, use el método **delete\_blob()**. En el siguiente ejemplo se muestra cómo eliminar un blob.

    azure_blob_service.delete_blob(container.name, "image-blob")

Pasos siguientes
----------------

Ahora que está familiarizado con los aspectos básicos del almacenamiento de blobs, siga estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure](http://msdn.microsoft.com/es-es/library/windowsazure/gg433040.aspx)
-   Visite el [blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/) (en inglés).
-   Visite el repositorio del [SDK de Azure para Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) (en inglés) en GitHub.

