<properties linkid="dev-java-how-to-on-premise-application-with-blob-storage" urlDisplayName="Image Gallery w/ Storage" pageTitle="On-premises application with blob storage (Java) | Microsoft Azure" metaKeywords="Azure blob storage, Azure blob Java, Azure blob example, Azure blob tutorial" description="Learn how to create a console application that uploads an image to Azure, and then displays the image in your browser. Code samples in Java." metaCanonical="" services="storage" documentationCenter="Java" title="On-Premises Application with Blob Storage" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />

Aplicación local con almacenamiento de blobs
============================================

El siguiente ejemplo muestra cómo se puede utilizar el almacenamiento de Azure para almacenar las imágenes en Azure. El siguiente código se destina a una aplicación de consola que carga una imagen en Azure y, a continuación, crea un archivo HTML que muestra la imagen en su explorador.

Tabla de contenido
------------------

-   [Requisitos previos](#bkmk_prerequisites)
-   [Uso del almacenamiento de blobs de Azure para cargar un archivo](#bkmk_uploadfile)
-   [Eliminación de un contenedor](#bkmk_deletecontainer)

Requisitos previos
------------------

1.  Un kit para desarrolladores de Java (JDK) v1.6 o posterior instalado.
2.  El SDK de Azure instalado.
3.  El archivo JAR de las Bibliotecas de Azure para Java (y cualquier JAR de dependencia correspondiente) instalado y en la ruta de acceso de compilación utilizada por el compilador de Java. Para obtener información acerca de la instalación de bibliotecas de Azure para Java, consulte [Descarga del SDK de Azure para Java](http://www.windowsazure.com/es-es/develop/java/).
4.  Una cuenta de almacenamiento configurada en Azure. El nombre y clave de la cuenta para la cuenta de almacenamiento será utilizada por el código a continuación. Consulte [Creación de una cuenta de almacenamiento](http://www.windowsazure.com/es-es/manage/services/storage/how-to-create-a-storage-account/) para obtener información acerca de la creación de una cuenta de almacenamiento, y [Administración de cuentas de almacenamiento](http://www.windowsazure.com/es-es/manage/services/storage/how-to-manage-a-storage-account/) para obtener información acerca del la recuperación de la clave de la cuenta.
5.  Un archivo de imagen local creado con nombre y almacenado en la ruta de acceso c:\\myimages\\image1.jpg. También puede modificar el constructor **FileInputStream** en el ejemplo para utilizar una ruta de acceso de imagen y un nombre de archivo diferentes.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

Uso del almacenamiento de blobs de Azure para cargar un archivo
---------------------------------------------------------------

Se presenta a continuación un procedimiento paso a paso; si desea omitir estos pasos, más adelante se presenta todo el código en este tema.

Comience el código mediante la inclusión de importaciones para las clases de almacenamiento central de Azure, las clases de clientes de blob de Azure, las clases de Java IO y la clase **URISyntaxException**:

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.azure.services.blob.client.*;
    import java.io.*;
    import java.net.URISyntaxException;

Declare una clase llamada **StorageSample** e incluya el corchete de apertura 
**{**.

    public class StorageSample {

Dentro de la clase **StorageSample**, declare una variable de cadena que contendrá el protocolo de extremo predeterminado, el nombre de la cuenta de almacenamiento y su clave de acceso al almacenamiento, según lo especificado en su cuenta de almacenamiento de Azure. Reemplace los valores de marcador de posición **your\_account\_name** y **your\_account\_key** por su propio nombre de cuenta y clave de cuenta, respectivamente.

    public static final String storageConnectionString = 
           "DefaultEndpointsProtocol=http;" + 
               "AccountName=your_account_name;" + 
               "AccountKey=your_account_name"; 

Agregue su declaración de **main**, incluya un bloque **try** e incluya los corchetes de apertura necesarios (**{**).

    public static void main(String[] args) 
    {
        try
        {

Declare variables del siguiente tipo (las descripciones reflejan el uso que se les da en este ejemplo):

-   **CloudStorageAccount**: Se utiliza para inicializar el objeto de cuenta con el nombre y la clave de la cuenta de almacenamiento de Azure y para crear el objeto de cliente blob.
-   **CloudBlobClient**: Se utiliza para tener acceso al servicio de blob.
-   **CloudBlobContainer**: Se utiliza para crear un contenedor de blobs, enumerar los blobs en el contenedor y eliminar el contenedor.
-   **CloudBlockBlob**: Se utiliza para cargar un archivo de imagen local al contenedor.

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

Asigne un valor a la variable **account**.

    account = CloudStorageAccount.parse(storageConnectionString);

Asigne un valor a la variable **serviceclient**.

    serviceClient = account.createCloudBlobClient();

Asigne un valor a la variable **container**. Obtendremos una referencia al contenedor llamado **gettingstarted**.

    // El nombre del contenedor debe estar en minúsculas.
    container = serviceClient.getContainerReference("gettingstarted");

Cree el contenedor. Este método creará el contenedor si no existe (y devolverá **true**). Si el contenedor existe, devolverá **false**. Una alternativa a **createIfNotExist** es el método **create** (que devolverá un error si el contenedor ya existe).

    container.createIfNotExist();

Configure el acceso anónimo para el contenedor.

    // Configurar acceso anónimo en el contenedor.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Obtenga una referencia al blob en bloque, el cual representará al blob en el almacenamiento de Azure.

    blob = container.getBlockBlobReference("image1.jpg");

Utilice el constructor **File** para obtener una referencia al archivo que se creó de manera local que va a cargar. (Asegúrese de haber creado el archivo antes de ejecutar el código).

    File fileReference = new File ("c:\myimages\image1.jpg");

Cargue el archivo local a través de una llamada al método **CloudBlockBlob.upload**. El primer parámetro del método **CloudBlockBlob.upload** es un objeto **FileInputStream** que representa el archivo local que se cargará en el almacenamiento de Azure. El segundo parámetro es el tamaño, en bytes, del archivo.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Llame a una función auxiliar denominada **MakeHTMLPage** para hacer una página HTML básica que contiene un elemento **&lt;image\>** con la fuente establecida en el blob que se encuentra ahora en la cuenta de almacenamiento de Azure. (El código de **MakeHTMLPage** se describirá más adelante en este tema).

    MakeHTMLPage(container);

Imprima un mensaje de estado y la información sobre la página HTML creada.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Cierre el bloque **try** mediante la inserción de un corchete de cierre: **}**

Controle las siguientes excepciones:

-   **FileNotFoundException**: Se puede mostrar mediante los constructores **FileInputStream** o **FileOutputStream**.
-   **StorageException**: Se puede mostrar mediante la biblioteca de almacenamiento del cliente de Azure.
-   **URISyntaxException**: Se puede mostrar mediante el método **ListBlobItem.getUri**.
-   **Exception**: Control de una excepción genérica.

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Cierre **main** mediante la inserción de un corchete de cierre: **}**

Cree un método llamado **MakeHTMLPage** para crear una página HTML básica. Este método tiene un parámetro de tipo **CloudBlobContainer**, el cual se usará para iterar a través de la lista de blobs cargados. Este método mostrará excepciones de tipo **FileNotFoundException**, que se pueden mostrar mediante el constructor **FileOutputStream** y **URISyntaxException**, que se pueden mostrar a través del método **ListBlobItem.getUri**. Incluya el corchete de apertura **{**.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

Cree un archivo local llamado **index.html**.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

Escriba en el archivo local y agregue los elementos **&lt;html\>**, **&lt;header\>** y **&lt;body\>**.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Itere a través de la lista de blobs cargados. Para cada blob, en la página HTML cree un elemento **&lt;img\>** que envió su atributo **src** a la URI del blob, tal como existe en su cuenta de almacenamiento de Azure. Aunque ha agregado una sola imagen en este ejemplo, si agregara más, este código iteraría todas ellas.

Para simplificar, en este ejemplo se asume que cada blob cargado es una imagen. Si ha actualizado blobs aparte de imágenes o blobs de páginas en lugar de blobs en bloques, ajuste el código según sea necesario.

    // Enumerar los blobs cargados.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // Enumerar cada blob como un elemento <img> en el cuerpo de HTML.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Cierre el elemento **&lt;body\>** y el elemento **&lt;html\>**.

    stream.println("</body>");
    stream.println("</html>");

Cierre el archivo local.

    stream.close();

Cierre **MakeHTMLPage** con la inserción de un corchete de cierre: **}**

Cierre **StorageSample** mediante la inserción de corchete de cierre: **}**

El siguiente es el código completo de este ejemplo. Recuerde que debe modificar los valores de marcador de posición **your\_account\_name** y **your\_account\_key** para usar el nombre y la clave de la cuenta, respectivamente.

    import com.microsoft.azure.services.core.storage.*;
    import com.microsoft.azure.services.blob.client.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Crear una imagen, c:\myimages\image1.jpg, antes de ejecutar este ejemplo.
    // También puede cambiar el valor que usó el constructor FileInputStream 
    // para utilizar una ruta de acceso de imagen y un archivo diferentes que ya ha creado.
    public class StorageSample {

        public static final String storageConnectionString = 
                "DefaultEndpointsProtocol=http;" + 
                   "AccountName=your_account_name;" + 
                   "AccountKey=your_account_name"; 

        public static void main(String[] args) 
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;
                
                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // El nombre del contenedor debe estar en minúsculas.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExist();
                
                // Configurar acceso anónimo en el contenedor.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Cargar un archivo de imagen.
                blob = container.getBlockBlobReference("image1.jpg");
                File fileReference = new File ("c:\myimages\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // En este punto se carga la imagen.
                // A continuación, cree una página HTML que muestre todas las imágenes cargadas.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            }
            catch (FileNotFoundException fileNotFoundException)
            {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (URISyntaxException uriSyntaxException)
            {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Crear una página HTML que se pueda usar para mostrar las imágenes cargadas.
        // En este ejemplo se asume que todos los blobs son para imágenes.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Crear los elementos de apertura <html>, <header> y <body>.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerar los blobs cargados.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // Enumerar cada blob como un elemento <img> en el cuerpo de HTML.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Completar el elemento <html> y cerrar el archivo.
            stream.println("</html>");
            stream.close();
        }
    }

Además de subir el archivo de imagen local al almacenamiento de Azure, el código de ejemplo crea un archivo local namedindex.html, que se puede abrir en el explorador para ver la imagen cargada.

Debido a que el código contiene el nombre y la clave de la cuenta, asegúrese de que su código fuente sea seguro.

Eliminación de un contenedor
----------------------------

Debido a que se cobrará por el almacenamiento, es posible que desee eliminar el contenedor **gettingstarted** después de que haya terminado de experimentar con este ejemplo. Para eliminar un contenedor, use el método **CloudBlobContainer.delete**:

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Para llamar al método **CloudBlobContainer.delete**, el proceso de inicialización de los objetos **CloudStorageAccount**, **ClodBlobClient**, **CloudBlobContainer** es el mismo que se muestra para el método **createIfNotExist**. El siguiente es un ejemplo completo que elimina el contenedor llamado **gettingstarted**.

    import com.microsoft.azure.services.core.storage.*;
    import com.microsoft.azure.services.blob.client.*;

    public class DeleteContainer {

        public static final String storageConnectionString = 
                "DefaultEndpointsProtocol=http;" + 
                   "AccountName=your_account_name;" + 
                   "AccountKey=your_account_key"; 

        public static void main(String[] args) 
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                
                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // El nombre del contenedor debe estar en minúsculas.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();
                
                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

Para ver información general de otras clases y métodos de almacenamiento de blobs, consulte [Uso del servicio de almacenamiento de blobs desde Java](http://www.windowsazure.com/es-es/develop/java/how-to-guides/blob-storage/).

