---
title: "Aplicación local con Blob Storage (Java) | Microsoft Docs"
description: "Aprenda a crear una aplicación de consola que carga una imagen en Azure y, a continuación, muestra la imagen en el explorador. Ejemplos de código en Java."
services: storage
documentationcenter: java
author: mmacy
manager: carmonm
editor: tysonn
ms.assetid: ccc9a7d7-6fe4-467b-b7fd-a73f17539e3f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/17/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 27d088291d93ce936a9b2465a13a47e62270fb84


---
# <a name="on-premises-application-with-blob-storage"></a>Aplicación local con almacenamiento en blobs
## <a name="overview"></a>Información general
El siguiente ejemplo muestra cómo se puede utilizar el almacenamiento de Azure para almacenar las imágenes en Azure. El código en este artículo se destina a una aplicación de consola que carga una imagen en Azure y, a continuación, crea un archivo HTML que muestra la imagen en su explorador.

## <a name="prerequisites"></a>Requisitos previos
* Un kit para desarrolladores de Java (JDK) v 1.6 o posteriores instalado.
* El SDK de Azure instalado.
* El archivo JAR de las Bibliotecas de Azure para Java (y cualquier JAR de dependencia correspondiente) instalado y en la ruta de acceso de compilación utilizada por el compilador de Java. Para obtener información acerca de la instalación de bibliotecas de Azure para Java, consulte [Descarga del SDK de Azure para Java](../java-download-azure-sdk.md).
* Una cuenta de almacenamiento configurada en Azure. El código en este artículo usará el nombre y la clave de cuenta para la cuenta de almacenamiento. Consulte [Crear una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account) para más información sobre la creación de una cuenta de almacenamiento y [Visualización y copia de las claves de acceso de almacenamiento](storage-create-storage-account.md#view-and-copy-storage-access-keys) para más información acerca de la recuperación de la clave de cuenta.
* Ha creado un archivo de imagen local con el nombre almacenado en la ruta de acceso c:\\myimages\\image1.jpg. También puede modificar el constructor   **FileInputStream** en el ejemplo para usar una ruta de acceso de imagen y un nombre de archivo diferentes.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="to-use-azure-blob-storage-to-upload-a-file"></a>Para usar el almacenamiento de blobs de Azure para cargar un archivo
A continuación se presenta un procedimiento paso a paso. Si desea omitir pasos, el código completo se presenta más adelante en este artículo.

Comience el código mediante la inclusión de importaciones para las clases de almacenamiento central de Azure, las clases de clientes de blob de Azure, las clases de Java IO y la clase **URISyntaxException** :

```java
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;
import java.io.*;
import java.net.URISyntaxException;
```

Declare una clase llamada **StorageSample** e incluya el corchete de apertura, **{**.

```java
public class StorageSample {
```

Dentro de la clase **StorageSample** , declare una variable de cadena que contendrá el protocolo de extremo predeterminado, el nombre de la cuenta de almacenamiento y la clave de acceso de almacenamiento, según lo especificado en su cuenta de Almacenamiento de Azure. Reemplace los valores de marcador de posición **your\_account\_name** y **your\_account\_key** por los suyos propios.

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_account_name;" +
    "AccountKey=your_account_name";
```

Agregue su declaración de **main**, incluya un bloque **try** e incluya los corchetes de apertura necesarios, **{**.

```java
    public static void main(String[] args)
    {
        try
        {
```

Declare variables del siguiente tipo (las descripciones reflejan el uso que se les da en este ejemplo):

* **CloudStorageAccount**: se utiliza para inicializar el objeto de cuenta con el nombre y la clave de la cuenta de Almacenamiento de Azure y para crear el objeto de cliente blob.
* **CloudBlobClient**: se utiliza para tener acceso al servicio BLOB.
* **CloudBlobContainer**: se utiliza para crear un contenedor de blobs, enumerar los blobs en el contenedor y eliminar el contenedor.
* **CloudBlockBlob**: se utiliza para cargar un archivo de imagen local al contenedor.

<!-- -->

```java
    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;
```

Asigne un valor a la variable **account** .

```java
account = CloudStorageAccount.parse(storageConnectionString);
```

Asigne un valor a la variable **serviceClient** .

```java
serviceClient = account.createCloudBlobClient();
```

Asigne un valor a la variable **container** . Obtendremos una referencia a un contenedor llamado **gettingstarted**.

```java
// Container name must be lower case.
container = serviceClient.getContainerReference("gettingstarted");
```

Cree el contenedor. Este método creará el contenedor si no existe (y devolverá **true**). Si el contenedor existe, devolverá **false**. Una alternativa a **createIfNotExist** es el método **create** (que devolverá un error si el contenedor ya existe).

```java
container.createIfNotExists();
```

Configure el acceso anónimo para el contenedor.

```java
// Set anonymous access on the container.
BlobContainerPermissions containerPermissions;
containerPermissions = new BlobContainerPermissions();
containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
container.uploadPermissions(containerPermissions);
```

Obtenga una referencia al blob en bloque, el cual representará al blob en el almacenamiento de Azure.

```java
blob = container.getBlockBlobReference("image1.jpg");
```

Use el constructor **File** para obtener una referencia al archivo que se creó de manera local que va a cargar. Asegúrese de haber creado el archivo antes de ejecutar el código.

```java
File fileReference = new File ("c:\\myimages\\image1.jpg");
```

Cargue el archivo local a través de una llamada al método **CloudBlockBlob.upload** . El primer parámetro del método **CloudBlockBlob.upload** es un objeto **FileInputStream** que representa el archivo local que se cargará en Azure Storage. El segundo parámetro es el tamaño, en bytes, del archivo.

```java
blob.upload(new FileInputStream(fileReference), fileReference.length());
```

Llame a una función auxiliar denominada **MakeHTMLPage** para crear una página HTML básica que contenga un elemento **&lt;image&gt;** con la fuente definida en el blob que se encuentra ahora en la cuenta de Azure Storage. El código de **MakeHTMLPage** se describirá más adelante en este artículo.

```java
MakeHTMLPage(container);
```

Imprima un mensaje de estado y la información sobre la página HTML creada.

```java
System.out.println("Processing complete.");
System.out.println("Open index.html to see the images stored in your storage account.");
```

Cierre el bloque **try** mediante la inserción de un corchete de cierre: **}**

Controle las siguientes excepciones:

* **FileNotFoundException**: se puede mostrar mediante los constructores **FileInputStream** o **FileOutputStream**.
* **StorageException**: se puede mostrar mediante la biblioteca de almacenamiento del cliente de Azure.
* **URISyntaxException**: se puede mostrar mediante el método **ListBlobItem.getUri**.
* **Exception**: control de una excepción genérica.

<!-- -->

```java
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
```

Cierre **main** mediante la inserción de un corchete de cierre: **}**.

Cree un método llamado **MakeHTMLPage** para crear una página HTML básica. Este método tiene un parámetro de tipo **CloudBlobContainer**, que se usará para iterar a través de la lista de blobs cargados. Este método mostrará excepciones de tipo **FileNotFoundException**, que se pueden mostrar mediante el constructor **FileOutputStream** y **URISyntaxException**, que se pueden mostrar a través del método **ListBlobItem.getUri**. Incluya el corchete de apertura, **{**.

```java
public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
{
```

Cree un archivo local llamado **index.html**.

```java
PrintStream stream;
stream = new PrintStream(new FileOutputStream("index.html"));
```

Escriba en el archivo local y agregue los elementos **html&lt;&gt;**, **&lt;header&gt;** y **&lt;body&gt;**.

```java
stream.println("<html>");
stream.println("<header/>");
stream.println("<body>");
```

Itere a través de la lista de blobs cargados. Para cada blob, en la página HTML cree un elemento **&lt;img&gt;** que envió su atributo **src** al URI del blob, tal como existe en su cuenta de Azure Storage.
Aunque ha agregado una sola imagen en este ejemplo, si agregara más, este código iteraría todas ellas.

Para simplificar, en este ejemplo se asume que cada blob cargado es una imagen. Si ha actualizado blobs aparte de imágenes o blobs de páginas en lugar de blobs en bloques, ajuste el código según sea necesario.

```java
// Enumerate the uploaded blobs.
for (ListBlobItem blobItem : container.listBlobs()) {
// List each blob as an <img> element in the HTML body.
stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
}
```

Cierre el elemento **&lt;body&gt;** y el elemento **&lt;html&gt;**.

```java
stream.println("</body>");
stream.println("</html>");
```

Cierre el archivo local.

```java
stream.close();
```

Cierre **MakeHTMLPage** mediante la inserción de un corchete de cierre: **}**.

Cierre **StorageSample** mediante la inserción de un corchete de cierre: **}**.

El siguiente es el código completo de este ejemplo. Recuerde modificar los valores de marcador de posición **your\_account\_name** and **your\_account\_key** para usar los suyos propios.

```java
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;
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

    public static void main(String[] args) {
        try {
            CloudStorageAccount account;
            CloudBlobClient serviceClient;
            CloudBlobContainer container;
            CloudBlockBlob blob;

            account = CloudStorageAccount.parse(storageConnectionString);
            serviceClient = account.createCloudBlobClient();
            // Container name must be lower case.
            container = serviceClient.getContainerReference("gettingstarted");
            container.createIfNotExists();

            // Set anonymous access on the container.
            BlobContainerPermissions containerPermissions;
            containerPermissions = new BlobContainerPermissions();
            containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
            container.uploadPermissions(containerPermissions);

            // Upload an image file.
            blob = container.getBlockBlobReference("image1.jpg");

            File fileReference = new File("c:\\myimages\\image1.jpg");
            blob.upload(new FileInputStream(fileReference), fileReference.length());

            // At this point the image is uploaded.
            // Next, create an HTML page that lists all of the uploaded images.
            MakeHTMLPage(container);

            System.out.println("Processing complete.");
            System.out.println("Open index.html to see the images stored in your storage account.");

        } catch (FileNotFoundException fileNotFoundException) {
            System.out.print("FileNotFoundException encountered: ");
            System.out.println(fileNotFoundException.getMessage());
            System.exit(-1);
        } catch (StorageException storageException) {
            System.out.print("StorageException encountered: ");
            System.out.println(storageException.getMessage());
            System.exit(-1);
        } catch (URISyntaxException uriSyntaxException) {
            System.out.print("URISyntaxException encountered: ");
            System.out.println(uriSyntaxException.getMessage());
            System.exit(-1);
        } catch (Exception e) {
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
```

Además de subir el archivo de imagen local al almacenamiento de Azure, el código de ejemplo crea un archivo local namedindex.html, que se puede abrir en el explorador para ver la imagen cargada.

Debido a que el código contiene el nombre y la clave de la cuenta, asegúrese de que su código fuente sea seguro.

## <a name="to-delete-a-container"></a>Para eliminar un contenedor
Debido a que se cobrará por el almacenamiento, es posible que desee eliminar el contenedor **gettingstarted** después de que haya terminado de experimentar con este ejemplo. Para eliminar un contenedor, use el método **CloudBlobContainer.delete** :

```java
container = serviceClient.getContainerReference("gettingstarted");
container.delete();
```

Para llamar al método **CloudBlobContainer.delete**, el proceso de inicialización de los objetos **CloudStorageAccount**, **ClodBlobClient** y **CloudBlobContainer** es el mismo que se muestra para el método **createIfNotExist**. A continuación se expone un ejemplo completo que elimina el contenedor llamado **gettingstarted**.

```java
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;

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
```

Para ver información general de otras clases y métodos de almacenamiento de blobs, consulte [Uso del almacenamiento de blobs desde Java](storage-java-how-to-use-blob-storage.md).

## <a name="next-steps"></a>Pasos siguientes
Siga estos vínculos para obtener más información acerca de las tareas de almacenamiento más complejas.

* [SDK de almacenamiento de Azure para Java](https://github.com/azure/azure-storage-java)
* [Referencia del SDK de cliente de almacenamiento de Azure](http://dl.windowsazure.com/storage/javadoc/)
* [API de REST de servicios de Azure Storage](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/)




<!--HONumber=Nov16_HO3-->


