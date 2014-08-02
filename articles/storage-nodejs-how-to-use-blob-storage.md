<properties linkid="dev-nodejs-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Node.js) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Node.js" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Blob Service from Node.js" authors="" solutions="" manager="" editor="" />

Uso del servicio BLOB desde Node.js
===================================

Esta guía muestra cómo realizar algunas tareas comunes a través del servicio BLOB de Azure. Los ejemplos están escritos usando la API Node.js. Dichas tareas comunes incluyen **cargar**, **enumerar**, **descargar** y **eliminar** blobs. Para obtener más información acerca de los blobs, consulte la sección [Pasos siguientes](#next-steps).

Tabla de contenido
------------------

-   [żQué es el servicio BLOB?](#what-is)
-   [Conceptos](#concepts)
-   [Creación de una cuenta de almacenamiento de Azure](#create-account)
-   [Creación de una aplicación Node.js](#create-app)
-   [Configuración de su aplicación para obtener acceso al almacenamiento](#configure-access)
-   [Configuración de una cadena de conexión de almacenamiento de Azure](#setup-connection-string)
-   [Creación de un contenedor](#create-container)
-   [Carga de un blob en un contenedor](#upload-blob)
-   [Enumeración de los blobs de un contenedor](#list-blob)
-   [Descarga de blobs](#download-blobs)
-   [Eliminación de un blob](#delete-blobs)
-   [Pasos siguientes](#next-steps)

żQué es el servicio BLOB?
-------------------------

El servicio BLOB de Azure sirve para almacenar grandes cantidades de datos sin estructurar a los que se puede obtener acceso desde cualquier lugar del mundo por medio de HTTP o HTTPS. Un solo blob puede tener un tamańo de cientos de gigabytes y una sola cuenta de almacenamiento puede contener hasta 100 TB de blobs. Entre los usos habituales de los blobs se incluyen:

-   Servicio de imágenes o documentos directamente a un explorador
-   Almacenamiento de archivos para acceso distribuido
-   Streaming de audio y vídeo
-   Realización de copia de seguridad segura y recuperación ante desastres
-   Almacenamiento de datos para el análisis en local o en un servicio hospedado de Azure

Puede usar los blobs para exponer los datos públicamente para todo el mundo o de forma privada para el almacenamiento interno de las aplicaciones.

Conceptos
---------

El servicio BLOB contiene los componentes siguientes:

![Blob1](./media/storage-nodejs-how-to-use-blob-storage/blob1.jpg)

-   **Formato de dirección URL:** Es posible dirigir los blobs con el siguiente formato de dirección URL:

        http://storageaccount.blob.core.windows.net/container/blob  

    Las siguientes direcciones URL desvían uno de los blobs del diagrama:

        http://sally.blob.core.windows.net/movies/MOV1.AVI

-   **Cuenta de almacenamiento:** Todo el acceso a Almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. Se trata del nivel superior del espacio de nombres para el acceso a los blobs. Una cuenta puede contener una cantidad ilimitada de contenedores, siempre que su tamańo total no supere los 100 TB.

-   **Contenedor:** Un contenedor ofrece una agrupación de un conjunto de blobs. Todos los blobs deben residir en un contenedor. Además, una cuenta puede disponer de un número ilimitado de contenedores y un contenedor puede almacenar un número ilimitado de blobs.

-   **Blob:** Un archivo de cualquier tipo y tamańo. Hay dos tipos de blobs: en bloques y en páginas. La mayoría de los archivos son blobs en bloques. Un blob en bloques único puede tener un tamańo de hasta 200 GB. En este tutorial se usan blobs en bloques. Los blobs en páginas, que son otro tipo de blobs, pueden tener un tamańo de hasta 1 TB y son más eficaces cuando los intervalos de bytes de un archivo se modifican con frecuencia.

Creación de una cuenta de almacenamiento de Azure
-------------------------------------------------

Necesita una cuenta de almacenamiento de Azure para usar operaciones de almacenamiento. Siga estos pasos para crear una cuenta de almacenamiento. También puede crear una cuenta de almacenamiento [usando la API de REST](http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx).

1.  Inicie sesión en el [Portal de administración de Azure](http://manage.windowsazure.com).

2.  En la parte inferior del panel de navegación, haga clic en **+NEW**.

    ![+new](./media/storage-nodejs-how-to-use-blob-storage/plus-new.png)

3.  Haga clic en **Cuenta de almacenamiento** y, a continuación, en **Quick Create**.

    ![Cuadro de diálogo de creación rápida](./media/storage-nodejs-how-to-use-blob-storage/quick-storage.png)

4.  En URL, escriba el nombre de subdominio que vaya usar en el URI para la cuenta de almacenamiento. Esta entrada puede contener de 3 a 24 letras minúsculas y números. Este valor se convierte en el nombre del host dentro del URI que se ha usado para direccionar los recursos Blob, Cola o Tabla de la suscripción.

5.  Seleccione un grupo de región/afinidad en el que ubicar el almacenamiento. Si va a usar el almacenamiento desde la aplicación de Azure, seleccione la misma región en la que implementará la aplicación.

6.  Haga clic en **Create Storage Account**.

Creación de una aplicación Node.js
----------------------------------

Cree una aplicación Node.js vacía. Para obtener instrucciones acerca de cómo crear una aplicación Node.js, consulte [Creación e implementación de una aplicación Node.js en un sitio web de Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/), [Servicio en la nube Node.js]({localLink:2221} "Aplicación web Node.js") (usando Windows PowerShell) o [Sitio web con WebMatrix](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/).

Configuración de su aplicación para obtener acceso al almacenamiento
--------------------------------------------------------------------

Para usar el almacenamiento de Azure tendrá que descargar y usar el paquete Node.js azure, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### Uso del Administrador de paquetes para Node (NPM) para obtener el paquete

1.  Utilice una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix) y vaya a la carpeta donde ha creado la aplicación de ejemplo.

2.  Escriba **npm install azure** en la ventana de comandos. Esto debería devolver la salida siguiente:

        azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Puede ejecutar manualmente el comando **ls** para comprobar si se ha creado la carpeta **node\_modules**. Dentro de dicha carpeta, busque el paquete **azure**, que contiene las bibliotecas necesarias para el acceso al almacenamiento.

### Importación del paquete

Con el Bloc de notas u otro editor de texto, agregue lo siguiente en la parte superior del archivo **server.js** de la aplicación en la que pretenda usar el almacenamiento:

    var azure = require('azure');

Configuración de una conexión de almacenamiento de Azure
--------------------------------------------------------

El módulo azure leerá las variables de entorno AZURE\_STORAGE\_ACCOUNT y AZURE\_STORAGE\_ACCESS\_KEY para obtener la información necesaria para conectarse a su cuenta de almacenamiento de Azure. Si no se configuran estas variables de entorno, debe especificar la información de la cuenta llamando a **createBlobService**.

Para ver un ejemplo de cómo configurar las variables de entorno en un archivo de configuración para un servicio de nube de Azure, consulte [Servicio de nube de Node.js con almacenamiento](/en-us/develop/nodejs/tutorials/web-app-with-storage/).

Para ver un ejemplo de cómo configurar las variables de entorno del Portal de administración para un sitio web de Azure, consulte [Aplicación web de Node.js con almacenamiento](/en-us/develop/nodejs/tutorials/web-site-with-storage/).

Creación de un contenedor
-------------------------

El objeto **BlobService** le permite trabajar con contenedores y blobs. El código siguiente crea un objeto **BlobService**. Agregue lo siguiente cerca de la parte superior de **server.js**:

    var blobService = azure.createBlobService();

Todos los blobs residen en un contenedor. La llamada a **createContainerIfNotExists** en el objeto **BlobService** devolverá el contendor especificado, si existe, o creará uno nuevo con el nombre especificado, si es que todavía no existe. De forma predeterminada el contenedor nuevo es privado y requiere el uso de la clave de acceso para descargar los blobs de él.

    blobService.createContainerIfNotExists(containerName, function(error){
        if(!error){
            // El contenedor existe y es privado.
        }
    });

Si desea que los archivos del contenedor sean públicos para que se pueda tener acceso a ellos sin necesidad de una clave de acceso, puede configurar el nivel de acceso del contenedor en **blob** o **container**. Configurar el nivel de acceso en **blob** permite el acceso anónimo de lectura al contenido del blob y los metadatos del contenedor, pero no al listado de todos los blobs de un contenedor determinado. Configurar el nivel de acceso en **container** permite el acceso de lectura anónimo al contenido del blob y los metadatos, incluidos los del contenedor. El ejemplo siguiente demuestra cómo configurar el nivel de acceso en **blob**:

    blobService.createContainerIfNotExists(containerName
        , {publicAccessLevel : 'blob'}
        , function(error){
            if(!error){
                // El contenedor existe y es público.
            }
        });

También puede modificar el nivel de acceso de un contenedor usando **setContainerAcl** para especificarlo. El ejemplo siguiente cambia el nivel de acceso a container:

    blobService.setContainerAcl(containerName
        , 'container'
        , function(error){
            if(!error){
                // El nivel de acceso del contenedor se ha configurado en 'container'.
            }
        });

### Filtros

Las operaciones de filtrado opcionales se aplican a las operaciones realizadas usando **BlobService**. Las operaciones de filtrado pueden incluir el registro y el reintento automático, entre otros. Los filtros son objetos que implementan un método con la firma siguiente:

     function handle (requestOptions, next)

Después de realizar el preprocesamiento en las opciones de solicitud, el método tiene que llamar a "next" pasando una devolución de llamada con la firma siguiente:

     function (returnObject, finalCallback, next)

En esta devolución de llamada y después de procesar returnObject (la respuesta de la solicitud al servidor), la devolución de llamada tiene que invocar a next, si existe, para continuar procesando otros filtros, o bien simplemente invocar a finalCallback para finalizar la invocación del servicio.

Se incluyen dos filtros que implementan la lógica de reintento con el SDK de Azure para Node.js: **ExponentialRetryPolicyFilter** y **LinearRetryPolicyFilter**. Lo siguiente crea un objeto **BlobService** que usa **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var blobService = azure.createBlobService().withFilter(retryOperations);

Carga de un blob en un contenedor
---------------------------------

Para cargar datos en un blob, use los métodos **createBlockBlobFromFile**, **createBlockBlobFromStream** o **createBlockBlobFromText**. **createBlockBlobFromFile** carga el contenido de un archivo, **createBlockBlobFromStream** carga el contenido de una secuencia y **createBlockBlobFromText** carga el valor del texto especificado.

Lo siguiente carga el contenido del archivo **test1.txt** en el blob 'test1'.

    blobService.createBlockBlobFromFile(containerName
        , 'test1'
        , 'test1.txt'
        , function(error){
            if(!error){
                // El archivo se ha cargado.
            }
        });

Enumeración de los blobs de un contenedor
-----------------------------------------

Para incorporar los blobs a un contenedor, use el método **listBlobs** con un bucle **for** para mostrar el nombre de cada blob del contenedor. El código siguiente ofrece el **nombre** de todos los blobs de un contenedor a la consola.

    blobService.listBlobs(containerName, function(error, blobs){
        if(!error){
            for(var index in blobs){
                console.log(blobs[index].name);
            }
        }
    });

Descarga de blobs
-----------------

Para descargar datos de un blob, use **getBlobToFile**, **getBlobToStream** o **getBlobToText**. El ejemplo siguiente demuestra cómo usar **getBlobToStream** para descargar el contenido del blob **test1** y almacenarlo en el archivo **output.txt** usando una secuencia:

    var fs=require('fs');
    blobService.getBlobToStream(containerName
        , 'test1'
        , fs.createWriteStream('output.txt')
        , function(error){
            if(!error){
                // El blob se ha escrito en la secuencia.
            }
        });

Eliminación de un blob
----------------------

Finalmente, para eliminar un blob, llame a **deleteBlob**. El ejemplo siguiente elimina el blob llamado 'blob1'.

    blobService.deleteBlob(containerName
        , 'blob1'
        , function(error){
            if(!error){
                // El blob se ha eliminado.
            }
        });

Pasos siguientes
----------------

Ahora que está familiarizado con los aspectos básicos del almacenamiento de blobs, siga estos vínculos para obtener más información acerca de cómo realizar tareas de almacenamiento más complejas.

-   Consulte la referencia de MSDN: [Almacenamiento de datos y acceso a los mismos en Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx).
-   Visite el [blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/) (en inglés).
-   Visite el repositorio del [SDK de Azure para Node.js](https://github.com/WindowsAzure/azure-sdk-for-node) (en inglés) en GitHub.

