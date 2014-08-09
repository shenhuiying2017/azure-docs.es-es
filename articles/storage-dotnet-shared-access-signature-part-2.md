<properties linkid="manage-services-storage-net-shared-access-signature-part-2" urlDisplayName="" pageTitle="Create and use a SAS with the Blob Service | Microsoft Azure" metaKeywords="Azure blob, shared access signatures, stored access policy" description="Explore generating and using shared access signatures with the Blob service" metaCanonical="" services="storage" documentationCenter="" title="Part 2: Create and Use a SAS with the Blob Service" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

Firmas de acceso compartido, parte 2: Creación y uso de una firma de acceso compartido con el servicio BLOB
===========================================================================================================

En la [parte 1](../storage-dotnet-shared-access-signature-part-1/) de este tutorial se analizaron las firmas de acceso compartido (SAS) y se explicaron los procedimientos recomendados para su uso. En la parte 2 se muestra cómo generar este tipo de firmas para, a continuación, usarlas con el servicio BLOB de Azure. Los ejemplos están escritos en C\# y usan la biblioteca del cliente de almacenamiento de Azure para .NET. Entre los escenarios descritos se incluyen los siguientes aspectos del uso de firmas de acceso compartido:

-   Generación de una firma de acceso compartido en un contenedor
-   Generación de una firma de acceso compartido en un blob
-   Creación de una directiva de acceso almacenada para administrar las firmas en los recursos de un contenedor
-   Comprobación de las firmas de acceso compartido de una aplicación cliente

Acerca de este tutorial
=======================

En este tutorial, nos centraremos en la creación de firmas de acceso compartido para contenedores y blobs mediante la creación de dos aplicaciones de consola. La primera aplicación de consola genera firmas de acceso compartido en un contenedor y en un blob. Dicha aplicación conoce las claves de la cuenta de almacenamiento. La segunda aplicación de consola, que actuará como aplicación cliente, obtiene acceso a los recursos del contenedor y del blob mediante las firmas de acceso compartido creadas con la primera aplicación. Esta aplicación solo usa las firmas mencionadas para autenticar su acceso a los recursos del contenedor y del blob, pero no conoce las claves de la cuenta.

Parte 1: Creación de una aplicación de consola para generar firmas de acceso compartido
=======================================================================================

En primer lugar, asegúrese de tener instalada la biblioteca del cliente de almacenamiento de Azure para .NET (versión 2.0). Puede instalar el [paquete de NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "paquete de NuGet") (en inglés) que contenga los ensamblados más recientes para la biblioteca del cliente. Este es el método recomendado para asegurarse de contar con las últimas revisiones. También puede descargar dicha biblioteca como parte de la última versión del [SDK de Azure para .NET](http://www.windowsazure.com/es-es/downloads/).

En Visual Studio, cree una aplicación de consola de Windows y denomínela **GenerateSharedAccessSignatures**. Agregue referencias a los archivos **Microsoft.WindowsAzure.Configuration.dll** y **Microsoft.WindowsAzure.Storage.dll** de una de las siguientes formas:

-   Si desea instalar el paquete de NuGet, instale en primer lugar la [extensión Administrador de paquetes NuGet para Visual Studio](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) (en inglés). En Visual Studio, seleccione **Proyecto | Manage NuGet Packages**, busque en línea **Almacenamiento de Azure** y siga las instrucciones de instalación.
-   También puede buscar los ensamblados en la instalación del SDK de Azure y agregar referencias a estos.

En la parte superior del archivo Program.cs, agregue las siguientes instrucciones **using**:

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Edite el archivo app.config para que contenga un valor de configuración con una cadena de conexión que apunte a la cuenta de almacenamiento. El archivo app.config deberá tener un aspecto similar a este:

    <configuration>
      <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings> 
    </configuration>

Generación de un URI de firma de acceso compartido para un contenedor
---------------------------------------------------------------------

Para comenzar, se agregará un método para generar una firma de acceso compartido en un nuevo contenedor. En este caso, la firma no está asociada a una directiva de acceso almacenada, por lo que incluye en el URI la información que indica su tiempo de expiración y los permisos que concede.

En primer lugar, agregue código al método **Main()** para autenticar el acceso a la cuenta de almacenamiento y crear un contenedor:

    static void Main(string[] args)
    {
        //Analizar la cadena de conexión y devolver una referencia a la cuenta de almacenamiento.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
        
        //Crear el objeto de cliente blob.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        //Obtener una referencia a un contenedor para usarla en el código de ejemplo y crearla en caso de que no exista.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();
        
        //Insertar llamadas a los métodos creados a continuación...
        
        //Requerir al usuario que introduzca información antes de cerrar la ventana de la consola.
        Console.ReadLine();
    }

A continuación, agregue un nuevo método que genere la firma de acceso compartido para el contenedor y que devuelva el URI de la firma:

    static string GetContainerSasUri(CloudBlobContainer container)
    {
        //Establecer el tiempo de expiración y los permisos del contenedor.
        //En este caso no se especifica la hora de inicio, por lo que la firma de acceso compartido entra en vigor de forma inmediata.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;
        
        //Generar la firma de acceso compartido en el contenedor y establecer las restricciones directamente en la firma.
        string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
        
        //Devolver la cadena del URI para el contenedor, incluido el token de la firma de acceso compartido.
        return container.Uri + sasContainerToken;
    }

Agregue las líneas siguientes en la parte inferior del método **Main()**, antes de la llamada a **Console.ReadLine()**, para llamar a **GetContainerSasUri()** y escribir el URI de la firma en la ventana de la consola:

    //Generar un URI de firma de acceso compartido para el contenedor, sin una directiva de acceso almacenada.
    Console.WriteLine("URI de la firma de acceso compartido del contenedor: " + GetContainerSasUri(container));
    Console.WriteLine();

Compile y ejecute para generar el URI de la firma de acceso compartido para el nuevo contenedor, que será similar al siguiente:

https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

Una vez que se ejecute el código, la firma de acceso compartido creada en el contenedor será válida durante las cuatro horas siguientes. Dicha firma concede permiso al cliente para enumerar los blobs en el contenedor y escribir un nuevo blob en este.

Generación de un URI de firma de acceso compartido para un blob
---------------------------------------------------------------

A continuación, vamos a escribir código similar al anterior para crear un nuevo blob en el contenedor y generar una firma de acceso compartido para este. Dicha firma no está asociada a una directiva de acceso almacenada, por lo que incluye la hora de inicio, el tiempo de expiración y la información de permisos en el URI.

Agregue un nuevo método para crear un blob y escriba texto en este. A continuación, se genera una firma de acceso compartido y se devuelve el URI de la firma:

    static string GetBlobSasUri(CloudBlobContainer container)
    {
        //Obtener una referencia a un blob en el contenedor.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");
        
        //Cargar texto en el blob. Si el blob no existe, se creará. 
        //En caso de que exista, su contenido se sobrescribirá.
        string blobContent = "Este blob será accesible para los clientes a través de una firma de acceso compartido.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }
        
        //Establecer el tiempo de expiración y los permisos para el blob.
        //En este caso, la hora de inicio se especifica como iniciada hace unos minutos, para reducir la desviación del reloj.
        //La firma de acceso compartido será válida de forma inmediata.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
        
        //Generar la firma de acceso compartido en el blob y establecer las restricciones directamente en la firma.
        string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);
        
        //Devolver la cadena del URI para el contenedor, incluido el token de la firma de acceso compartido.
        return blob.Uri + sasBlobToken;
    }

En la parte inferior del método **Main()**, agregue las líneas siguientes para llamar a **GetBlobSasUri()**, antes de la llamada a **Console.ReadLine()**, y escriba el URI de la firma de acceso compartido en la ventana de la consola:

    //Generar un URI de firma de acceso compartido para un blob en el contenedor, sin una directiva de acceso almacenada.
    Console.WriteLine("URI de la firma de acceso compartido del blob: " + GetBlobSasUri(container));
    Console.WriteLine();

Compile y ejecute para generar el URI de la firma de acceso compartido para el nuevo blob, que será similar al siguiente:

https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

Creación de una directiva de acceso almacenada en el contenedor
---------------------------------------------------------------

Ahora vamos a crear una directiva de acceso almacenada en el contenedor que definirá las restricciones de las firmas de acceso compartido asociadas.

En los ejemplos anteriores, hemos especificado la hora de inicio (de forma implícita o explícita), el tiempo de expiración y los permisos del URI de la firma de acceso compartido. En los siguientes, especificaremos estos datos en la directiva de acceso almacenada y no en la firma. Al hacerlo, podemos cambiar estas restricciones sin necesidad de volver a emitir la firma de acceso compartido.

Tenga en cuenta que es posible incluir una o varias restricciones en la firma de acceso compartida y, el resto, en la directiva de acceso almacenada. Sin embargo, la hora de inicio, el tiempo de expiración y los permisos solo pueden especificarse en una de ambas ubicaciones; por ejemplo, no se pueden especificar permisos en la firma de acceso compartido y también en la directiva de acceso almacenada.

Agregue un nuevo método que cree una directiva de acceso almacenada y devuelva el nombre de esta:

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container, string policyName)
    {
        //Crear una directiva de acceso almacenada y definir sus restricciones.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };
        
        //Obtener los permisos existentes del contenedor.
        BlobContainerPermissions permissions = new BlobContainerPermissions();
        
        //Agregar la nueva directiva a los permisos del contenedor.
        permissions.SharedAccessPolicies.Clear();
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
    }

En la parte inferior del método **Main()**, antes de la llamada a **Console.ReadLine()**, agregue las siguientes líneas para llamar al método **CreateSharedAccessPolicy()**:

    //Crear una directiva de acceso en el contenedor que, opcionalmente, pueda usarse para proporcionar restricciones para 
    //las firmas de acceso compartido en el contenedor y el blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

Generación de un URI de firma de acceso compartido en el contenedor que usa una directiva de acceso
---------------------------------------------------------------------------------------------------

A continuación, vamos a crear otra firma de acceso compartido en el contenedor generado anteriormente, si bien esta vez la asociaremos a la directiva de acceso creada en el ejemplo anterior.

Agregue un nuevo método para generar otra firma de acceso compartido en el contenedor:

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Generar la firma de acceso compartido en el contenedor. En este caso, todas las restricciones de dicha 
        //firma se especifican en la directiva de acceso almacenada.
        string sasContainerToken = container.GetSharedAccessSignature(null, policyName);
        
        //Devolver la cadena del URI para el contenedor, incluido el token de la firma de acceso compartido.
        return container.Uri + sasContainerToken;
    }

En la parte inferior del método **Main()**, antes de la llamada a **Console.ReadLine()**, agregue las siguientes líneas para llamar al método **GetContainerSasUriWithPolicy**:

    //Generar un URI de firma de acceso compartido para el contenedor, con una directiva de acceso almacenada para establecer restricciones en dicha firma.
    Console.WriteLine("URI de firma de acceso compartido del contenedor que usa la directiva de acceso almacenada: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

Generación de un URI de firma de acceso compartido en el blob que usa una directiva de acceso
---------------------------------------------------------------------------------------------

Por último, agregaremos un método similar para crear otro blob y generar una firma de acceso compartido que se asocia a una directiva de acceso.

Agregue un nuevo método para crear un blob y generar una firma de acceso compartido:

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Obtener una referencia a un blob en el contenedor.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");
        
        //Cargar texto en el blob. Si el blob no existe, se creará. 
        //En caso de que exista, su contenido se sobrescribirá.
        string blobContent = "Este blob será accesible para los clientes a través de una firma de acceso compartido. " + 
        "Una directiva de acceso almacenada define las restricciones para la firma.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }
        
        //Generar la firma de acceso compartido en el blob.
        string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);
        
        //Devolver la cadena del URI para el contenedor, incluido el token de la firma de acceso compartido.
        return blob.Uri + sasBlobToken;
    }

En la parte inferior del método **Main()**, antes de la llamada a **Console.ReadLine()**, agregue las siguientes líneas para llamar al método **GetBlobSasUriWithPolicy**:

    //Generar un URI de firma de acceso compartido para un blob en el contenedor, con una directiva de acceso almacenada para establecer restricciones en dicha firma.
    Console.WriteLine("URI de firma de acceso compartido del blob que usa la directiva de acceso almacenada: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

Ahora, el método **Main()** debe tener este aspecto en su conjunto. Ejecútelo para escribir los URI de firma de acceso compartido en la ventana de la consola y, a continuación, cópielos y péguelos en un archivo de texto para usarlos en la segunda parte de este tutorial.

    static void Main(string[] args)
    {
        //Analizar la cadena de conexión y devolver una referencia a la cuenta de almacenamiento.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
        
        //Crear el objeto de cliente blob.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
        //Obtener una referencia a un contenedor para usarla en el código de ejemplo y crearla en caso de que no exista.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();
        
        //Generar un URI de firma de acceso compartido para el contenedor, sin una directiva de acceso almacenada.
        Console.WriteLine("URI de la firma de acceso compartido del contenedor: " + GetContainerSasUri(container));
        Console.WriteLine();
        
        //Generar un URI de firma de acceso compartido para un blob en el contenedor, sin una directiva de acceso almacenada.
        Console.WriteLine("URI de la firma de acceso compartido del blob: " + GetBlobSasUri(container));
        Console.WriteLine();
        
        //Crear una directiva de acceso en el contenedor que, opcionalmente, pueda usarse para proporcionar restricciones para 
        //las firmas de acceso compartido en el contenedor y el blob.
        string sharedAccessPolicyName = "tutorialpolicy";
        CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
        
        //Generar un URI de firma de acceso compartido para el contenedor, con una directiva de acceso almacenada para establecer restricciones en dicha firma.
        Console.WriteLine("URI de firma de acceso compartido del contenedor que usa la directiva de acceso almacenada: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();
        
        //Generar un URI de firma de acceso compartido para un blob en el contenedor, con una directiva de acceso almacenada para establecer restricciones en dicha firma.
        Console.WriteLine("URI de firma de acceso compartido del blob que usa la directiva de acceso almacenada: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();
        
        Console.ReadLine();
    }

Al ejecutar la aplicación de consola GenerateSharedAccessSignatures, el resultado que se mostrará en la ventana de la consola será similar al siguiente. Estas son las firmas de acceso compartido que se usarán en la segunda parte de este tutorial.

![sas-console-output-1](./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG)

Parte 2: Creación de una aplicación de consola para probar las firmas de acceso compartido
==========================================================================================

Para probar las firmas de acceso compartido creadas en los ejemplos anteriores, crearemos una segunda aplicación de consola que use dichas firmas para realizar operaciones en el contenedor y en un blob.

Tenga en cuenta que, si han transcurrido más de cuatro horas desde que se completó la primera parte del tutorial, las firmas generadas con un tiempo de expiración de cuatro horas ya no serán válidas. Asimismo, las firmas asociadas a la directiva de acceso almacenada expiran transcurridas 10 horas. Si uno de estos intervalos ya ha transcurrido (o ambos), deberá ejecutar el código de la primera aplicación de consola a fin de generar nuevas firmas de acceso compartido y usarlas en la segunda parte del tutorial.

En Visual Studio, cree una nueva aplicación de consola de Windows y denomínela **ConsumeSharedAccessSignatures**. Agregue referencias a **Microsoft.WindowsAzure.Configuration.dll** y **Microsoft.WindowsAzure.Storage.dll**, como ya ha hecho anteriormente.

En la parte superior del archivo Program.cs, agregue las siguientes instrucciones **using**:

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Agregue las siguientes constantes al cuerpo del método **Main()** y actualice sus valores a las firmas de acceso compartido generadas en la primera parte del tutorial.

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }

Incorporación de un método para probar las operaciones del contenedor con una firma de acceso compartido
--------------------------------------------------------------------------------------------------------

A continuación, vamos a agregar un método para probar algunas operaciones representativas del contenedor con una firma de acceso compartido en este último. Tenga en cuenta que dicha firma se usa para devolver una referencia al contenedor y el acceso a este se autentica en función de la firma sola.

Agregue el método siguiente a Program.cs:

    static void UseContainerSAS(string sas)
    {
        //Intentar realizar operaciones del contenedor con la firma de acceso compartido proporcionada.

        //Devolver una referencia al contenedor con el URI de la firma de acceso compartido.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Crear una lista para almacenar los URI del blob devueltos por una operación de enumeración en el contenedor.
        List<Uri> blobUris = new List<Uri>();

        try
        {
            //Operación de escritura: escribir un nuevo blob en el contenedor. 
            CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
            string blobContent = "Este blob se creó con una firma de acceso compartido que concede permisos de escritura en el contenedor. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Operación de escritura correcta para la firma de acceso compartido" + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Operación de escritura incorrecta para la firma de acceso compartido" + sas);
            Console.WriteLine("Información adicional del error: " + e.Message);
            Console.WriteLine();
        }

        try
        {
            //Operación de enumeración: enumerar los blobs del contenedor, incluido el que se acaba de agregar.
            foreach (ICloudBlob blobListing in container.ListBlobs())
            {
                blobUris.Add(blobListing.Uri);
            }
            Console.WriteLine("Operación de enumeración correcta para la firma de acceso compartido" + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Operación de enumeración incorrecta para la firma de acceso compartido" + sas);
            Console.WriteLine("Información adicional del error: " + e.Message);
            Console.WriteLine();
        }

        try
        {
            //Operación de lectura: obtener una referencia a uno de los blobs del contenedor y leerla. 
            CloudBlockBlob blob = container.GetBlockBlobReference(blobUris[0].ToString());
            MemoryStream msRead = new MemoryStream();
            msRead.Position = 0;
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                Console.WriteLine(msRead.Length);
            }
            Console.WriteLine("Operación de lectura correcta para la firma de acceso compartido" + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Operación de lectura incorrecta para la firma de acceso compartido" + sas);
            Console.WriteLine("Información adicional del error: " + e.Message);
            Console.WriteLine();
        }
        Console.WriteLine();

        try
        {
            //Operación de eliminación: eliminar un blob en el contenedor.
            CloudBlockBlob blob = container.GetBlockBlobReference(blobUris[0].ToString());
            blob.Delete();
            Console.WriteLine("Operación de eliminación correcta para la firma de acceso compartido" + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Operación de eliminación incorrecta para la firma de acceso compartido" + sas);
            Console.WriteLine("Información adicional del error: " + e.Message);
            Console.WriteLine();
        }        
    }

Actualice el método **Main()** para que llame a **UseContainerSAS()** con las dos firmas de acceso compartido creadas en el contenedor:

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Llamar a los métodos de prueba con las firmas de acceso compartido creadas en el contenedor, con y sin la directiva de acceso.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy); 
        
        //Llamar a los métodos de prueba con las firmas de acceso compartido creadas en el blob, con y sin la directiva de acceso.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
    }

Incorporación de un método para probar operaciones de blob con una firma de acceso compartido
---------------------------------------------------------------------------------------------

Por último, vamos a agregar un método para probar algunas operaciones representativas del blob usando una firma de acceso compartido en este último. En este caso se usa el constructor **CloudBlockBlob(String)** y se pasa la firma de acceso compartido para devolver una referencia al blob. No se requiere ningún otro tipo de autenticación, esta se basa exclusivamente en la firma.

Agregue el método siguiente a Program.cs:

    static void UseBlobSAS(string sas)
    {
        //Intentar realizar operaciones del blob con la firma de acceso compartido proporcionada.

        //Devolver una referencia al blob con el URI de la firma de acceso compartido.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        try
        {
            //Operación de escritura: escribir un nuevo blob en el contenedor. 
            string blobContent = "Este blob se creó con una firma de acceso compartido que concede permisos de escritura en el blob. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Operación de escritura correcta para la firma de acceso compartido" + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Operación de escritura incorrecta para la firma de acceso compartido" + sas);
            Console.WriteLine("Información adicional del error: " + e.Message);
            Console.WriteLine();
        }

        try
        {
            //Operación de lectura: leer el contenido del blob.
            MemoryStream msRead = new MemoryStream();
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                msRead.Position = 0;
                using (StreamReader reader = new StreamReader(msRead, true))
                {
                    string line;
                    while ((line = reader.ReadLine()) != null)
                    {
                        Console.WriteLine(line);
                    }
                }
            }
            Console.WriteLine("Operación de lectura correcta para la firma de acceso compartido" + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Operación de lectura incorrecta para la firma de acceso compartido" + sas);
            Console.WriteLine("Información adicional del error: " + e.Message);
            Console.WriteLine();
        }

        try
        {
            //Operación de eliminación: eliminar el blob.
            blob.Delete();
            Console.WriteLine("Operación de eliminación correcta para la firma de acceso compartido" + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Operación de eliminación incorrecta para la firma de acceso compartido" + sas);
            Console.WriteLine("Información adicional del error: " + e.Message);
            Console.WriteLine();
        }        
    }

Actualice el método **Main()** para que llame a **UseBlobSAS()** con las dos firmas de acceso compartido creadas en el blob:

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Llamar a los métodos de prueba con las firmas de acceso compartido creadas en el contenedor, con y sin la directiva de acceso.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy); 
        
        //Llamar a los métodos de prueba con las firmas de acceso compartido creadas en el blob, con y sin la directiva de acceso.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
    }

Ejecute la aplicación de consola y observe el resultado para ver qué operaciones se permiten para cada firma. El resultado de la ventana de la consola será similar al siguiente:

![sas-console-output-2](./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG)

Pasos siguientes
================

[Firmas de acceso compartido, parte 1: Descripción del modelo de firmas de acceso compartido](../storage-dotnet-shared-access-signature-part-1/)

[Administración del acceso a los recursos de almacenamiento de Azure](http://msdn.microsoft.com/es-es/library/windowsazure/ee393343.aspx)

[Delegación de acceso con una firma de acceso compartido (API de REST)](http://msdn.microsoft.com/es-es/library/windowsazure/ee395415.aspx)

[Introducción a las firmas de acceso compartido de tabla y cola](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

