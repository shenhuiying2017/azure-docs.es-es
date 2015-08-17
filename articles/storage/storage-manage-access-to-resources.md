<properties 
	pageTitle="Administración del acceso a los recursos de Almacenamiento de Azure | Microsoft Azure" 
	description="Aprenda a administrar cómo acceden los usuarios a los recursos de Almacenamiento de Azure." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="jdial" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="micurd;tamram"/>

# Administración del acceso a los recursos de almacenamiento de Azure

## Información general

De forma predeterminada, solamente el propietario de la cuenta de almacenamiento puede acceder a los recursos de almacenamiento en esa cuenta. Si su servicio o aplicación debe hacer que estos recursos estén disponibles para otros clientes sin compartir la clave de acceso, tiene las opciones siguientes para permitir el acceso:

- Puede establecer los permisos de un contenedor para permitir el acceso de lectura anónimo al contenedor y a sus blobs. El acceso de lectura anónimo solo está disponible para los contenedores y blobs. 

- Puede exponer un recurso a través de una firma de acceso compartido, que permite delegar acceso restringido a un contenedor, blob, tabla, cola, recurso compartido de archivos o archivo, especificando el intervalo para el que los recursos estarán disponibles y los permisos que un cliente tendrá en él.

- Puede usar una directiva de acceso almacenada para administrar firmas de acceso compartido para un contenedor o sus blobs, para una cola, para una tabla, para un recurso compartido de archivo o sus archivos. La directiva de acceso ofrece una medida adicional de control sobre las firmas de acceso compartido y también proporciona medios directos para revocarlas.

## Restringir acceso a contenedores y blobs

De forma predeterminada, solamente el dueño de la cuenta de almacenamiento puede obtener acceso a un contenedor y a todos los blobs en su interior. Para dar a los usuarios anónimos permisos de lectura para un contenedor y sus blobs, puede establecer los permisos del contenedor de forma que se permita el acceso público. Los usuarios anónimos pueden leer los blobs que estén en un contenedor con acceso público sin necesidad de tener que autenticar la solicitud.

Los contenedores ofrecen las siguientes opciones para administrar el acceso al contenedor:

- **Acceso de lectura público completo**: los datos del contenedor y blobs se pueden leer mediante una solicitud anónima. Los clientes pueden enumerar los blobs del contenedor a través de una solicitud anónima, pero no pueden enumerar los contenedores que están en la cuenta de almacenamiento.

- **Acceso de lectura público solo para blobs**: los datos de blob dentro de este contenedor pueden leerse a través de una solicitud anónima, pero los datos del contenedor no están disponibles. Los clientes no pueden enumerar los blobs incluidos en el contenedor mediante una solicitud anónima.

- **Sin acceso público de lectura**: solamente el propietario de la cuenta puede leer los datos del contenedor y del blob.

>[AZURE.NOTE]Si el servicio requiere que lleve un control de los recursos de blob más minucioso o bien, si desea conceder permisos para operaciones a parte de las operaciones de lectura, puede utilizar la firma de acceso compartido para que otros usuarios puedan tener acceso a un recurso determinado.

### Características disponibles para los usuarios anónimos
En la siguiente tabla, se indican las operaciones a las que pueden llamar los usuarios anónimos cuando la ACL de un contenedor se establece para permitir un acceso público.

| Operación REST | Permiso con acceso de lectura público completo | Permiso con acceso de lectura público para solo para los blobs |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| List Containers | Solo el propietario | Solo el propietario |
| Create Container | Solo el propietario | Solo el propietario |
| Get Container Properties | Todo | Solo el propietario |
| Get Container Metadata | Todo | Solo el propietario |
| Set Container Metadata | Solo el propietario | Solo el propietario |
| Get Container ACL | Solo el propietario | Solo el propietario |
| Set Container ACL | Solo el propietario | Solo el propietario |
| Delete Container | Solo el propietario | Solo el propietario |
| List Blobs | Todo | Solo el propietario |
| Put Blob | Solo el propietario | Solo el propietario |
| Get Blob | Todo | Todo |
| Get Blob Properties | Todo | Todo |
| Set Blob Properties | Solo el propietario | Solo el propietario |
| Get Blob Metadata | Todo | Todo |
| Set Blob Metadata | Solo el propietario | Solo el propietario |
| Put Block | Solo el propietario | Solo el propietario |
| Get Block List (solo bloques confirmados) | Todo | Todo |
| Get Block List (solo bloques sin confirmar o todos los bloques) | Solo el propietario | Solo el propietario |
| Put Block List | Solo el propietario | Solo el propietario |
| Delete Blob | Solo el propietario | Solo el propietario |
| Copia de blobs | Solo el propietario | Solo el propietario |
| Instantánea de blob | Solo el propietario | Solo el propietario |
| Lease Blob | Solo el propietario | Solo el propietario |
| Put Page | Solo el propietario | Solo el propietario |
| Get Page Ranges | Todo | Todo |

## Crear y usar una firma de acceso compartido
Una firma de acceso compartido (SAS) es un URI que concede derechos de acceso restringidos a un recurso de almacenamiento durante un intervalo de tiempo especificado. Puede crear una SAP en estos recursos de almacenamiento:

- Contenedores y blobs
- Colas
- Tablas
- Archivos y recursos compartidos de archivo 

Cuando se facilita a un cliente una firma de acceso compartido, este podrá obtener acceso a los recursos de la cuenta de almacenamiento sin necesidad compartir la clave de cuenta con él.

>[AZURE.NOTE]Para obtener información general conceptual detallada, así como un tutorial acerca de las firmas de acceso compartido, consulte [Firmas de acceso compartido](storage-dotnet-shared-access-signature-part-1.md).

Los parámetros de consulta del URI de firma de acceso compartido incorporan toda la información necesaria para conceder acceso controlado un recurso de almacenamiento. Los parámetros de consulta de una SAS incluyen el intervalo de tiempo durante el que es válida la firma de acceso compartido, los permisos que concede, el recurso que debe estar disponible, la versión que se usa para ejecutar la solicitud y la firma que los servicios de almacenamiento deben usar para autenticar la solicitud.

Además, el URI de firma de acceso compartido puede hacer referencia a una directiva de acceso almacenada que proporciona un nivel de control adicional sobre un conjunto de firmas, incluida la capacidad de modificar o revocar el acceso al recurso en caso necesario.

Para obtener información sobre el formato de URI de una firma de acceso compartido, consulte [Delegación del acceso con una firma de acceso compartido](https://msdn.microsoft.com/library/ee395415.aspx).

### Uso seguro de las firmas de acceso compartido
Una firma de acceso compartido concede acceso al recurso especificado por los permisos concedidos a la dirección URI. Debe utilizar siempre HTTPS para crear un URI de firma de acceso compartido. Si utiliza HTTP con firmas de acceso compartido, podría hacer que la cuenta de almacenamiento se haga vulnerable a usos malintencionados.

Si una firma de acceso compartido concede accesos que no están pensados para el público en general, entonces deberá crearla con el mínimo de permisos. Es más, una firma de acceso compartido debe distribuirse a los clientes a través de una conexión segura, ha de asociarse a una directiva de acceso almacenada para la revocación y se debe especificar un período de vida lo más corto posible para la firma.

>[AZURE.NOTE]Los URI de firma de acceso compartido están asociados a la clave de la cuenta que se utiliza para crear la firma y a la directiva de acceso almacenada correspondiente (en su caso). Si no se especifica una directiva de acceso almacenada, la única forma de revocar una firma de acceso compartido es cambiar la clave de la cuenta.

### Crear una firma de acceso compartido
El ejemplo de código siguiente crea una directiva de acceso en un contenedor y luego genera una firma de acceso compartido para el contenedor. Esta firma de acceso compartido se podrá asignar a los clientes:

    // The connection string for the storage account.  Modify for your account.
    string storageConnectionString =
       "DefaultEndpointsProtocol=https;" +
       "AccountName=myaccount;" +
       "AccountKey=<account-key>";
    
    // As an alternative, you can retrieve storage account information from an app.config file. 
    // This is one way to store and retrieve a connection string if you are 
    // writing an application that will run locally, rather than in Microsoft Azure.
    
    // string storageConnectionString = ConfigurationManager.AppSettings["StorageAccountConnectionString"];
    
    // Create the storage account with the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);
       
    // Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Get a reference to the container for which shared access signature will be created.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    container.CreateIfNotExists();
    
    // Create blob container permissions, consisting of a shared access policy 
    // and a public access setting. 
    BlobContainerPermissions blobPermissions = new BlobContainerPermissions();
    
    // The shared access policy provides 
    // read/write access to the container for 10 hours.
    blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
    {
       // To ensure SAS is valid immediately, don’t set start time.
       // This way, you can avoid failures caused by small clock differences.
       SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
       Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
    });
    
    // The public access setting explicitly specifies that 
    // the container is private, so that it can't be accessed anonymously.
    blobPermissions.PublicAccess = BlobContainerPublicAccessType.Off;
    
    // Set the permission policy on the container.
    container.SetPermissions(blobPermissions);
    
    // Get the shared access signature to share with users.
    string sasToken =
       container.GetSharedAccessSignature(new SharedAccessBlobPolicy(), "mypolicy");

### Usar una firma de acceso compartido
Un cliente que recibe una firma de acceso compartido puede usarla desde su código para generar un objeto de tipo [StorageCredentials](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.auth.storagecredentials.aspx). Esas credenciales se pueden usar entonces para generar un objeto [CloudStorageAccount](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.cloudstorageaccount.aspx) o un objeto [CloudBlobClient](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx) para trabajar con el recurso, tal y como se muestra en este ejemplo:

    Uri blobUri = new Uri("https://myaccount.blob.core.windows.net/mycontainer/myblob.txt");
    
    // Create credentials with the SAS token. The SAS token was created in previous example.
    StorageCredentials credentials = new StorageCredentials(sasToken);
    
    // Create a new blob.
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, credentials);
    
    // Upload the blob. 
    // If the blob does not yet exist, it will be created. 
    // If the blob does exist, its existing content will be overwritten.
    using (var fileStream = System.IO.File.OpenRead(@"c:\Test\myblob.txt"))
    {
    blob.UploadFromStream(fileStream);
    }

## Usar una directiva de acceso almacenada
Una directiva de acceso almacenada ofrece un nivel adicional de control sobre las firmas de acceso compartidas en el servidor. Establecer una directiva de acceso almacenada para agrupar firmas de acceso compartido y proporcionar restricciones adicionales para firmas sujetas a la directiva. Puede utilizar una directiva de acceso almacenada para cambiar la hora de inicio, la hora de expiración o los permisos para una firma o bien para revocarla después de haberse emitido.

Una directiva de acceso almacenada ofrece un control mayor sobre las firmas de acceso compartido que se han emitido. En lugar de especificar la duración y los permisos de la firma en la dirección URL, puede especificar estos parámetros en una directiva de acceso almacenada que se guarda en el contenedor, recurso compartido de archivo, cola o tabla que contiene el recurso que se está compartiendo. Para cambiar estos parámetros para una o varias firmas, puede modificar la directiva de acceso almacenada, en lugar de volver a emitir las firmas. También puede revocar rápidamente la firma, para lo cual, debe modificar la directiva de acceso almacenada.

Por ejemplo, supongamos que ha emitido una firma de acceso compartido asociada a una directiva de acceso almacenada. Si ha especificado la hora de expiración de la directiva de acceso almacenada, puede modificar la directiva de acceso para ampliar la duración de la firma, sin tener que volver a emitir una firma nueva.

Según los procedimientos recomendados, es mejor especificar una directiva de acceso almacenada para cualquier recurso con signo para el que esté emitiendo una firma de acceso compartido, ya que la directiva almacenada se puede utilizar para modificar o revocar la firma una vez se ha emitido. Si no especifica una directiva almacenada, se recomienda que limite la duración de la firma para minimizar los riegos para los recursos de la cuenta de almacenamiento.

### Asociar una firma de acceso compartido a una directiva de acceso almacenada
Una directiva de acceso almacenada incluye un nombre de hasta 64 caracteres que es único en el contenedor, recurso compartido de archivo, cola o tabla. Para asociar una firma de acceso compartido a la directiva de acceso almacenada, se especifica este identificador al crear la firma de acceso compartida. En el URI de la firma de acceso compartido, el campo *signedidentifier* especifica el identificador de la directiva de acceso almacenada.

Un contenedor, un recurso compartido de archivo, una cola o una tabla pueden incluir hasta 5 directivas de acceso almacenadas. Cada directiva puede usar cualquier número de firmas de acceso compartido.

>[AZURE.NOTE]Establecer una directiva de acceso almacenada en un contenedor, recurso compartido de archivo, cola o tabla puede tardar hasta 30 segundos en aplicarse. Durante este intervalo, una firma de acceso compartido que se asocia a la directiva de acceso almacenada producirá un error con el código de estado 403 (Prohibido), hasta que la directiva de acceso se active.

### Especificar los parámetros de la directiva de acceso para una firma de acceso compartido
La directiva de acceso almacenada puede especificar los siguientes parámetros de la directiva de acceso para las firmas a las que está asociada:

- Hora de inicio

- Hora de expiración

- Permisos

En función de cómo desee controlar el acceso al recurso de almacenamiento, puede especificar todos estos parámetros en la directiva de acceso almacenada y omitirlos desde la dirección URL para la firma de acceso compartido. De esta forma, podrá modificar el comportamiento de la firma asociada en cualquier momento, así como revocarla. O puede especificar uno o más de los parámetros de la directiva de acceso en la directiva de acceso almacenada y el resto en la dirección URL. Por último, puede especificar todos los parámetros en la dirección URL. En este caso, puede utilizar la directiva de acceso almacenada para revocar la firma, pero no para modificar su comportamiento.

Es preciso que la firma de acceso compartido y la directiva de acceso almacenada, en su conjunto, incluyan todos los campos necesarios para autenticar la firma. Si falta alguno de los campos necesarios, la solicitud producirá un error con código de estado 403 (Prohibido). Asimismo, si un campo se especifica en la dirección URL de la firma de acceso compartido y en la directiva de acceso almacenada, la solicitud producirá un error con el código de estado 403 (Solicitud incorrecta). Consulte Crear y usar una firma de acceso compartido para obtener más información sobre los campos que componen la firma.

### Modificar o revocar una directiva de acceso almacenada

Para revocar el acceso a las firmas de acceso compartido que utilizan la misma directiva de acceso almacenada, quite la directiva almacenada de los recursos de almacenamiento; para ello, sobrescriba la lista de directivas almacenadas con una lista nueva que no contenga el nombre de la directiva. Para cambiar la configuración de acceso de una directiva de acceso almacenada, sobrescriba la lista de directivas almacenadas con una lista nueva que contenga una directiva con el mismo nombre, pero con nuevos datos para el control de acceso.

## Otras referencias

- [Autenticación para los servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)
- [Firmas de acceso compartido: Descripción del modelo de firmas de acceso compartido](storage-dotnet-shared-access-signature-part-1.md)
- [Delegación de acceso con una firma de acceso compartido](https://msdn.microsoft.com/library/azure/ee395415.aspx) 

<!---HONumber=August15_HO6-->