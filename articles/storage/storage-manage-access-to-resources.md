<properties 
	pageTitle="Administración del acceso a los recursos de almacenamiento de Azure" 
	description="Obtenga información sobre las diferentes maneras que existen para administrar el acceso a los recursos de almacenamiento de Azure." 
	services="storage" 
	documentationCenter="" 
	authors="micurd,tamram" 
	manager="jahogg" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="micurd"/>

# Administración del acceso a los recursos de almacenamiento de Azure

## Información general

De forma predeterminada, solamente el dueño de la cuenta de almacenamiento puede obtener acceso a blobs, tablas y colas en esa cuenta. Si su servicio o aplicación debe hacer que estos recursos estén disponibles para otros clientes sin compartir la clave de acceso, tiene las opciones siguientes para permitir el acceso:

- Puede establecer los permisos de un contenedor para permitir el acceso de lectura anónimo al contenedor y a sus blobs. Esto no se permite para tablas o colas.

- Puede exponer un recurso a través de una firma de acceso compartido, que permite delegar acceso restringido a un recurso del contenedor, blob, tabla o cola especificando el intervalo para el que los recursos estarán disponibles y los permisos que un cliente tendrá en él.

- Puede utilizar una directiva de acceso almacenada para administrar firmas de acceso compartido para un contenedor o sus blobs, para una cola o para una tabla. La directiva de acceso ofrece una medida adicional de control sobre las firmas de acceso compartido y también proporciona medios directos para revocarlas.

## Restringir acceso a contenedores y blobs

De forma predeterminada, solamente el dueño de la cuenta de almacenamiento puede obtener acceso a un contenedor y a todos los blobs en su interior. Si desea proporcionar a los usuarios anónimos permisos de lectura para un contenedor y sus blobs, puede establecer los permisos del contenedor de forma que se permita el acceso al público. Los usuarios anónimos pueden leer los blobs que estén en un contenedor con acceso público sin necesidad de tener que autenticar la solicitud.

Los contenedores ofrecen las siguientes opciones para administrar el acceso al contenedor:

- Acceso de lectura público completo: los datos del contenedor y blobs se pueden leer mediante una solicitud anónima. Los clientes pueden enumerar los blobs del contenedor a través de una solicitud anónima, pero no pueden enumerar los contenedores que están en la cuenta de almacenamiento.

- Acceso de lectura público solo para blobs: Los datos de blob dentro de este contenedor pueden leerse a través de una solicitud anónima, pero los datos del contenedor no están disponibles. Los clientes no pueden enumerar los blobs incluidos en el contenedor mediante una solicitud anónima.

- Sin acceso público de lectura: solamente el propietario de la cuenta puede leer los datos del contenedor y del blob.

>[AZURE.NOTE] Si el servicio requiere que lleve un control de los recursos de blob más minucioso o bien, si desea conceder permisos para operaciones a parte de las operaciones de lectura, puede utilizar la firma de acceso compartido para que otros usuarios puedan tener acceso a un recurso determinado. 

### Características disponibles para los usuarios anónimos
En la siguiente tabla, se indican las operaciones a las que pueden llamar los usuarios anónimos cuando la ACL de un contenedor se establece para permitir un acceso público.

| REST Operation                                         | Permiso con acceso de lectura público completo | Permiso con acceso de lectura público solo para blobs |
|--------------------------------------------------------|-----------------------------------------|---------------------------------------------------|
| List Containers                                        | Solo propietario                              | Solo propietario                                        |
| Create Container                                       | Solo propietario                              | Solo propietario                                        |
| Get Container Properties                               | Todos                                     | Solo propietario                                        |
| Get Container Metadata                                 | Todos                                     | Solo propietario                                        |
| Set Container Metadata                                 | Solo propietario                              | Solo propietario                                        |
| Get Container ACL                                      | Solo propietario                              | Solo propietario                                        |
| Set Container ACL                                      | Solo propietario                              | Solo propietario                                        |
| Delete Container                                       | Solo propietario                              | Solo propietario                                        |
| List Blobs                                             | Todos                                     | Solo propietario                                        |
| Put Blob                                               | Solo propietario                              | Solo propietario                                        |
| Get Blob                                               | Todos                                     | Todos                                               |
| Get Blob Properties                                    | Todos                                     | Todos                                               |
| Set Blob Properties                                    | Solo propietario                              | Solo propietario                                        |
| Get Blob Metadata                                      | Todos                                     | Todos                                               |
| Set Blob Metadata                                      | Solo propietario                              | Solo propietario                                        |
| Put Block                                              | Solo propietario                              | Solo propietario                                        |
| Get Block List (solo bloques asignados)                 | Todos                                     | Todos                                               |
| Get Block List (solo bloques no asignados o todos los bloques) | Solo propietario                              | Solo propietario                                        |
| Put Block List                                         | Solo propietario                              | Solo propietario                                        |
| Delete Blob                                            | Solo propietario                              | Solo propietario                                        |
| Copy Blob                                              | Solo propietario                              | Solo propietario                                        |
| Snapshot Blob                                          | Solo propietario                              | Solo propietario                                        |
| Lease Blob                                             | Solo propietario                              | Solo propietario                                        |
| Put Page                                               | Solo propietario                              | Solo propietario                                        |
| Get Page Ranges                                        | Todos                                     | Todos                                                  |

## Crear y usar una firma de acceso compartido
Una firma de acceso compartido es un URI que concede derechos de acceso restringidos a los contenedores, los blobs, las colas y las tablas durante un intervalo de tiempo concreto. Cuando se facilita a un cliente una firma de acceso compartido, este podrá obtener acceso a los recursos de la cuenta de almacenamiento sin necesidad compartir la clave de cuenta con él.

>[AZURE.NOTE] Para obtener información general conceptual detallada, así como un tutorial acerca de las firmas de acceso compartido, consulte [Firmas de acceso compartido](storage-dotnet-shared-access-signature-part-1.md).

Entre las operaciones admitidas con firmas de acceso compartido, se incluyen:

- Leer y escribir contenido de blobs en bloques o en páginas, listas de bloques, propiedades y metadatos

- Eliminar, conceder y crear una instantánea de un blob

- Enumerar los blobs de un contenedor

- Agregar, quitar, actualizar y eliminar mensajes de la cola (en la biblioteca cliente de almacenamiento 2.0 o versiones posteriores)

- Obtener metadatos de la cola, lo cual incluye el conteo de mensajes (en la biblioteca cliente de almacenamiento 2.0 y versiones posteriores)

- Realizar solicitudes, agregar, actualizar, eliminar y operaciones UPSERT en entidades de tabla (en la biblioteca cliente de almacenamiento 2.0 o versiones posteriores)

Los parámetros de consulta del URI de firma de acceso compartido incorporan toda la información necesaria para conceder acceso controlado un recurso de almacenamiento. Los parámetros de consulta del URI especifican el intervalo de tiempo durante el que es válida la firma de acceso compartido, los permisos que concede, el recurso que debe estar disponible y la firma que los servicios de almacenamiento deben utilizar para autenticar la solicitud.

Además, el URI de firma de acceso compartido puede hacer referencia a una directiva de acceso almacenada que proporciona un nivel de control adicional sobre un conjunto de firmas, incluida la capacidad de modificar o revocar el acceso al recurso en caso necesario. 

Para obtener información sobre el formato de URI de una firma de acceso compartido, consulte [Delegación del acceso con una firma de acceso compartido](https://msdn.microsoft.com/library/ee395415.aspx).

### Uso seguro de las firmas de acceso compartido
Una firma de acceso compartido concede acceso al recurso especificado por los permisos concedidos a la dirección URI. Debe utilizar siempre HTTPS para crear un URI de firma de acceso compartido. Si utiliza HTTP con firmas de acceso compartido, podría hacer que la cuenta de almacenamiento se haga vulnerable a usos malintencionados.

Si una firma de acceso compartido concede accesos que no están pensados para el público en general, entonces deberá crearla con el mínimo de permisos. Es más, una firma de acceso compartido debe distribuirse a los clientes a través de una conexión segura, ha de asociarse a una directiva de acceso almacenada para la revocación y se debe especificar un período de vida lo más corto posible para la firma.

>[AZURE.NOTE] Los URI de firma de acceso compartido están asociados a la clave de la cuenta que se utiliza para crear la firma y a la directiva de acceso almacenada correspondiente (en su caso). Si no se especifica una directiva de acceso almacenada, la única forma de revocar una firma de acceso compartido es cambiar la clave de la cuenta. 

### Crear una firma de acceso compartido
El ejemplo de código siguiente crea una directiva de acceso en un contenedor y luego genera una firma de acceso compartido para el contenedor. Esta firma de acceso compartido se podrá asignar a los clientes:

    // La cadena de conexión de la cuenta de almacenamiento.  Modifique para su cuenta.
    string storageConnectionString =
       "DefaultEndpointsProtocol=https;" +
       "AccountName=myaccount;" +
       "AccountKey=<account-key>";
    
    // Como alternativa, puede recuperar información de la cuenta de almacenamiento desde un archivo app.config. 
    // Esta es una forma de almacenar y recuperar una cadena de conexión si 
    // escribe una aplicación que se ejecutará de manera local, en lugar de ejecutarse en Microsoft Azure.
    
    // string storageConnectionString = ConfigurationManager.AppSettings["StorageAccountConnectionString"];
    
    // Cree la cuenta de almacenamiento con la cadena de conexión.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);
       
    // Cree el objeto de cliente blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Obtenga una referencia al contenedor para el que se creará una firma de acceso compartido.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    container.CreateIfNotExists();
    
    // Cree permisos de contenedor de blobs, que constan de una directiva de acceso compartido 
    // y una configuración de acceso público. 
    BlobContainerPermissions blobPermissions = new BlobContainerPermissions();
    
    // La directiva de acceso compartido proporciona 
    // acceso de lectura/escritura al contenedor durante 10 horas.
    blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
    {
       // Para asegurarse de que SAS sea válido de inmediato, no defina hora de inicio.
       // De este modo, puede evitar errores provocados por pequeñas diferencias horarias.
       SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
       Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
    });
    
    // La configuración de acceso público especifica de manera explícita que 
    // el contenedor es privado, por lo tanto, no es posible tener acceso a él de manera anónima.
    blobPermissions.PublicAccess = BlobContainerPublicAccessType.Off;
    
    // Establezca la directiva de permiso en el contenedor.
    container.SetPermissions(blobPermissions);
    
    // Obtenga la firma de acceso compartido para compartir con los usuarios.
    string sasToken =
       container.GetSharedAccessSignature(new SharedAccessBlobPolicy(), "mypolicy");

### Usar una firma de acceso compartido
Un cliente que recibe una firma de acceso compartido puede usarla desde su código para generar un objeto de tipo [StorageCredentials](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.auth.storagecredentials.aspx). Esas credenciales se pueden usar entonces para generar un objeto [CloudStorageAccount](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.cloudstorageaccount.aspx) o un objeto [CloudBlobClient](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx) para trabajar con el recurso, tal como se muestra en este ejemplo:

    Uri blobUri = new Uri("https://myaccount.blob.core.windows.net/mycontainer/myblob.txt");
    
    // Cree credenciales con el token de SAS. El token de SAS se creó en un ejemplo anterior.
    StorageCredentials credentials = new StorageCredentials(sasToken);
    
    // Cree un blob nuevo.
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, credentials);
    
    // Cargue el blob. 
    // El blob se creará si todavía no existe. 
    // Si el blob existe, se sobrescribirá su contenido existente.
    using (var fileStream = System.IO.File.OpenRead(@"c:\Test\myblob.txt"))
    {
    blob.UploadFromStream(fileStream);
    }

## Usar una directiva de acceso almacenada
Una directiva de acceso almacenada ofrece un nivel adicional de control sobre las firmas de acceso compartidas en el servidor. Establecer una directiva de acceso almacenada para agrupar firmas de acceso compartido y proporcionar restricciones adicionales para firmas sujetas a la directiva. Puede utilizar una directiva de acceso almacenada para cambiar la hora de inicio, la hora de expiración o los permisos para una firma o bien para revocarla después de haberse emitido.

Una directiva de acceso almacenada ofrece un control mayor sobre las firmas de acceso compartido que se han emitido. En lugar de especificar la duración y los permisos de la firma en la dirección URL, puede especificar estos parámetros en la directiva de acceso almacenada que se ha almacenado en el blob, contenedor, cola o tabla que se están compartiendo. Para cambiar estos parámetros para una o varias firmas, puede modificar la directiva de acceso almacenada, en lugar de volver a emitir las firmas. También puede revocar rápidamente la firma, para lo cual, debe modificar la directiva de acceso almacenada.

Por ejemplo, supongamos que ha emitido una firma de acceso compartido asociada a una directiva de acceso almacenada. Si ha especificado la hora de expiración de la directiva de acceso almacenada, puede modificar la directiva de acceso para ampliar la duración de la firma, sin tener que volver a emitir una firma nueva.

Según los procedimientos recomendados, es mejor especificar una directiva de acceso almacenada para cualquier recurso con signo para el que esté emitiendo una firma de acceso compartido, ya que la directiva almacenada se puede utilizar para modificar o revocar la firma una vez se ha emitido. Si no especifica una directiva almacenada, se recomienda que limite la duración de la firma para minimizar los riegos para los recursos de la cuenta de almacenamiento. 

### Asociar una firma de acceso compartido a una directiva de acceso almacenada
Una directiva de acceso almacenada incluye un nombre de hasta 64 caracteres que es único en el contenedor, cola o tabla. Para asociar una firma de acceso compartido a la directiva de acceso almacenada, se especifica este identificador al crear la firma de acceso compartida. En la URI de la firma de acceso compartido, el campo  *signedidentifier* especifica el identificador para la directiva de acceso almacenada.

Un contenedor, una cola o una tabla pueden incluir hasta 5 directivas de acceso almacenadas. Cada directiva puede usar cualquier número de firmas de acceso compartido.

>[AZURE.NOTE] Establecer una directiva de acceso almacenada en un contenedor, cola o tabla puede tardar hasta 30 segundos en aplicarse. Durante este intervalo, una firma de acceso compartido que se asocia a la directiva de acceso almacenada producirá un error con el código de estado 403 (Prohibido), hasta que la directiva de acceso se active.

### Especificar los parámetros de la directiva de acceso para una firma de acceso compartido
La directiva de acceso almacenada puede especificar los siguientes parámetros de la directiva de acceso para las firmas a las que está asociada:

- Hora de inicio

- Hora de expiración

- Permisos

En función de cómo desee controlar el acceso al recurso de almacenamiento, puede especificar todos estos parámetros en la directiva de acceso almacenada y omitirlos desde la dirección URL para la firma de acceso compartido. De esta forma, podrá modificar el comportamiento de la firma asociada en cualquier momento, así como revocarla. O puede especificar uno o más de los parámetros de la directiva de acceso en la directiva de acceso almacenada y el resto en la dirección URL. Por último, puede especificar todos los parámetros en la dirección URL. En este caso, puede utilizar la directiva de acceso almacenada para revocar la firma, pero no para modificar su comportamiento.

Es preciso que la firma de acceso compartido y la directiva de acceso almacenada, en su conjunto, incluyan todos los campos necesarios para autenticar la firma. Si falta alguno de los campos necesarios, la solicitud producirá un error con código de estado 403 (Prohibido). Asimismo, si un campo se especifica en la dirección URL de la firma de acceso compartido y en la directiva de acceso almacenada, la solicitud producirá un error con el código de estado 403 (Solicitud incorrecta). Consulte Crear y usar una firma de acceso compartido para obtener más información sobre los campos que componen la firma.

### Modificar o revocar una directiva de acceso almacenada

Para revocar el acceso a las firmas de acceso compartido que utilizan la misma directiva de acceso almacenada, quite la directiva almacenada de los recursos de almacenamiento; para ello, sobrescriba la lista de directivas almacenadas con una lista nueva que no contenga el nombre de la directiva. Para cambiar la configuración de acceso de una directiva de acceso almacenada, sobrescriba la lista de directivas almacenadas con una lista nueva que contenga una directiva con el mismo nombre, pero con nuevos datos para el control de acceso.


<!--HONumber=52--> 