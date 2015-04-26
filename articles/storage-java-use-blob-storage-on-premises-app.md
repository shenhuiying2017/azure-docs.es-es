<properties 
	pageTitle="Aplicación local con almacenamiento de blobs (Java) | Microsoft Azure" 
	description="Aprenda a crear una aplicación de consola que carga una imagen en Azure y, a continuación, muestra la imagen en el explorador. Ejemplos de código en Java." 
	services="storage" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="robmcm"/>

# Aplicación local con almacenamiento de blobs

El siguiente ejemplo muestra cómo se puede utilizar el almacenamiento de Azure para almacenar las imágenes en Azure. El siguiente código se destina a una aplicación de consola que carga una imagen en Azure y, a continuación, crea un
archivo HTML que muestra la imagen en el explorador.

## Tabla de contenido

-   [Requisitos previos][]
-   [Para usar el almacenamiento de blobs de Azure para cargar un archivo][]
-   [Para eliminar un contenedor][]

## <a name="bkmk_prerequisites"> </a>Requisitos previos

1.  Un kit para desarrolladores de Java (JDK) v1.6 o posterior instalado.
2.  El SDK de Azure instalado.
3.  El archivo JAR de las Bibliotecas de Azure para Java (y cualquier JAR de dependencia correspondiente) instalado y en la ruta de acceso de compilación utilizada por el compilador de Java. Para obtener información acerca de la instalación de bibliotecas de Azure para Java, consulte [Descarga del SDK de Azure para Java].
4.  Una cuenta de almacenamiento configurada en Azure. El nombre y clave de la cuenta para la cuenta de almacenamiento será utilizada por el código a continuación. Consulte [Creación de una cuenta de almacenamiento] para obtener información acerca de la creación de una cuenta de almacenamiento y [Administración de cuentas de almacenamiento] para obtener información acerca del la recuperación de la clave de la cuenta.
5.  Un archivo de imagen local creado con nombre y almacenado en la ruta de acceso c:\\myimages\\image1.jpg. También puede modificar el constructor **FileInputStream** en el ejemplo para utilizar una ruta de acceso de imagen y un nombre de archivo diferentes.

[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <a name="bkmk_uploadfilePara usar el almacenamiento de blobs de Azure para cargar un archivo"></a>

Se presenta a continuación un procedimiento paso a paso; si desea omitir estos pasos, más adelante se presenta todo el código en este tema.

Comience el código mediante la inclusión de importaciones para las clases de almacenamiento central de Azure, las clases de clientes de blob de Azure, las clases de Java IO y la clase **URISyntaxException**:

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;
    import java.io.*;
    import java.net.URISyntaxException;

Declare una clase llamada **StorageSample** e incluya el corchete de apertura
**{**.

    public class StorageSample {

Dentro de la clase **StorageSample**, declare una variable de cadena que contendrá el protocolo de extremo predeterminado, el nombre de la cuenta de almacenamiento y su clave de acceso al almacenamiento, según lo especificado en su cuenta de almacenamiento de Azure. Reemplace los valores de marcador de posición **your\_account\_name** y
**your\_account\_key** con su propio nombre y clave de cuenta, respectivamente.

    public static final String storageConnectionString = 
           "DefaultEndpointsProtocol=http;" + 
               "AccountName=your_account_name;" + 
               "AccountKey=your_account_name"; 

Agregue su declaración de **main**, incluya un bloque **try** e incluya los corchetes de apertura necesarios (*{***).

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

Asigne un valor a la variable **serviceClient**.

    serviceClient = account.createCloudBlobClient();

Asigne un valor a la variable **container**. Obtendremos una referencia al contenedor llamado **gettingstarted**.

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

Cree el contenedor. Este método creará el contenedor si no existe (y devolverá **true**). Si el contenedor existe, devolverá
**false**. Una alternativa a **createIfNotExist** es el método **create** (que devolverá un error si el contenedor ya existe).

    container.createIfNotExist();

Configure el acceso anónimo para el contenedor.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
     containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

Obtenga una referencia al blob en bloque, que representará al blob en el
almacenamiento de Azure.

    blob = container.getBlockBlobReference("image1.jpg");

Use el constructor **File** para obtener una referencia al archivo que se creó de manera local que va a cargar. (Asegúrese de haber creado el archivo antes de ejecutar el código).

    File fileReference = new File ("c:\\myimages\\image1.jpg");

Cargue el archivo local a través de una llamada al método **CloudBlockBlob.upload**. El primer parámetro para el método **CloudBlockBlob.upload** es un objeto
**FileInputStream** que representa el archivo local que se cargarán en el almacenamiento de Azure. El segundo parámetro es el tamaño, en bytes, del archivo.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

Llame a una función auxiliar denominada **MakeHTMLPage** para crear una página HTML básica que contenga un elemento **&lt;image&gt;** con la fuente definida en el blob que se encuentra ahora en la cuenta de almacenamiento de Azure. (El código de
**MakeHTMLPage** se describirá más adelante en este tema).

    MakeHTMLPage(container);

Imprima un mensaje de estado y la información sobre la página HTML creada.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

Cierre el bloque **try** mediante la inserción de un corchete de cierre: **}**

Controle las siguientes excepciones:

-   **FileNotFoundException**: Se puede mostrar mediante los constructores **FileInputStream** o **FileOutputStream**.
-   **StorageException**: Se puede mostrar mediante la biblioteca de almacenamiento del cliente de Azure.
-   **URISyntaxException**: Se puede mostrar mediante el método **ListBlobItem.getUri** .
-   **Excepción**: Control de una excepción genérica.

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

Escriba en el archivo local y agregue los elementos **&lt;html&gt;**, **&lt;header&gt;** y
**&lt;body&gt;**.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

Itere a través de la lista de blobs cargados. Para cada blob, en la página HTML, cree un elemento **&lt;img&gt;** que envíe su atributo **src** al URI del blob, tal como existe en la cuenta de almacenamiento de Azure.
Aunque ha agregado una sola imagen en este ejemplo, si agregara más, este código iteraría todas ellas.

Para simplificar, en este ejemplo se asume que cada blob cargado es una imagen. Si ha actualizado blobs aparte de imágenes o blobs de páginas en lugar de blobs en bloques, ajuste el código según sea necesario.

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

Cierre los elementos **&lt;body&gt;** y **&lt;html&gt;**.

    stream.println("</body>");
    stream.println("</html>");

Cierre el archivo local.

    stream.close();

Cierre **MakeHTMLPage** con la inserción de un corchete de cierre: **}**

Cierre **StorageSample** mediante la inserción de un corchete de cierre: **}**

El siguiente es el código completo de este ejemplo. Recuerde que debe modificar los valores de marcador de posición **your\_account\_name** y
**your\_account\_key** para usar su nombre y clave de cuenta, respectivamente.

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor 
    // to use a different image path and file that you have already created.
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
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExist();
                
                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");
                File fileReference = new File ("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
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

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

Además de subir el archivo de imagen local al almacenamiento de Azure, el código de ejemplo crea un archivo local namedindex.html, que se puede abrir en el explorador para ver la imagen cargada.

Debido a que el código contiene el nombre y la clave de la cuenta, asegúrese de que su código fuente sea seguro.

## <a name="bkmk_deletecontainerPara eliminar un contenedor"></a>

Dado que el almacenamiento se cobra, puede que desee eliminar el contenedor
**gettingstarted** al acabar de experimentar con el ejemplo. Para eliminar un contenedor, use el método **CloudBlobContainer.delete**:

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

Para llamar al método **CloudBlobContainer.delete**, el proceso de inicialización de los objetos **CloudStorageAccount**, **ClodBlobClient**,
**CloudBlobContainer** es el mismo que se muestra para el método
**createIfNotExist**. A continuación se expone un ejemplo completo que elimina el contenedor llamado **gettingstarted**.

    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;

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
                // Container name must be lower case.
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

Para obtener información general sobre otras clases y métodos de almacenamiento de blobs, consulte [Uso del servicio de almacenamiento de blobs desde Java].

  [Requisitos previos]: #bkmk_prerequisites
  [Para usar el almacenamiento de blobs de Azure para cargar un archivo]: #bkmk_uploadfile
  [Para eliminar un contenedor]: #bkmk_deletecontainer
  [Descarga del SDK de Azure para Java]: http://azure.microsoft.com/develop/java/
  [Creación de una cuenta de almacenamiento]: http://azure.microsoft.com/manage/services/storage/how-to-create-a-storage-account/
  [Administración de cuentas de almacenamiento]: http://azure.microsoft.com/manage/services/storage/how-to-manage-a-storage-account/
  [Uso del servicio de almacenamiento de blobs desde Java]: http://azure.microsoft.com/develop/java/how-to-guides/blob-storage/

<!--HONumber=42-->
