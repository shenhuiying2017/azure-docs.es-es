## <a name="configure-your-application-to-access-azure-storage"></a>Configuración de la aplicación para obtener acceso al almacenamiento de Azure
Hay dos maneras para autenticar una aplicación para tener acceso a los servicios de almacenamiento:

* Clave compartida: la clave compartida solo se usa para realizar pruebas.
* Firma de acceso compartido (SAS): SAS para aplicaciones de producción.

### <a name="shared-key"></a>Clave compartida
La autenticación de clave compartida significa que la aplicación usará el nombre y la clave de la cuenta para tener acceso a los servicios de almacenamiento. Con el fin de mostrar rápidamente cómo usar esta biblioteca, emplearemos la autenticación de clave compartida en esta introducción.

> [!WARNING] 
> **Use la autenticación de clave compartida solamente para las pruebas.** El nombre de cuenta y la clave de cuenta, que ofrecen acceso total de lectura y escritura a la cuenta de almacenamiento asociada, se distribuirán a todas las personas que descarguen la aplicación. Esta práctica **no** se recomienda, ya que se corre el riesgo de que los clientes en los que no se confía pongan la clave en peligro.
> 
> 

Si se usa la autenticación de clave compartida, se creará una [cadena de conexión](../articles/storage/common/storage-configure-connection-string.md). La cadena de conexión consta de:  

* Protocolo **DefaultEndpointsProtocol** : puede elegir si usar HTTP o HTTPS. De todos modos, se recomienda encarecidamente el uso de HTTPS.
* **Nombre de cuenta** : es el nombre de la cuenta de almacenamiento.
* **Clave de cuenta**: en [Azure Portal](https://portal.azure.com), navegue a la cuenta de almacenamiento y haga clic en el icono **Claves** para buscar esta información.
* (Opcional) **EndpointSuffix** : se usa para servicios de almacenamiento en regiones con distintos sufijos de punto de conexión, como Azure China o Azure Governance.

Este es un ejemplo de cadena de conexión que usa la autenticación de clave compartida.

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Firmas de acceso compartido (SAS)
Para una aplicación móvil, el método recomendado para la autenticación de una solicitud por un cliente en el servicio Azure Storage es el uso de una firma de acceso compartido (SAS). SAS permite conceder a los clientes acceso a un recurso durante un período especificado, con un conjunto de permisos especificado.
Como propietario de cuenta de almacenamiento, será preciso que genere una SAS para que la consuman los clientes móviles. Para generar la SAS, puede escribir un servicio independiente que genere la SAS que se va a distribuir a los clientes. Para la realización de pruebas puede usar el [Explorador de Microsoft Azure Storage](http://storageexplorer.com) o [Azure Portal](https://portal.azure.com) para generar una SAS. Al crear la SAS, se puede especificar el intervalo de tiempo durante el que la SAS es válida y los permisos la SAS concede al cliente.

El siguiente ejemplo muestra cómo usar el Explorador de almacenamiento de Microsoft Azure para generar una SAS.

1. Si no lo ha hecho ya, [instale el Explorador de Microsoft Azure Storage](http://storageexplorer.com)
2. Conéctese a su suscripción.
3. Haga clic en la cuenta de almacenamiento y haga clic en la pestaña "Acciones" en la parte inferior izquierda. Haga clic en "Get Shared Access Signature" (Obtener firma de acceso compartido) para generar una "cadena de conexión" para la SAS.
4. Este es un ejemplo de una cadena de conexión de SAS que concede permisos de lectura y escritura en el nivel de servicio, contenedor y objeto para el servicio Blob de la cuenta de almacenamiento.
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

Como puede ver, cuando usa una SAS, no expone su clave de cuenta en la aplicación. Para más información sobre SAS y los procedimientos de uso recomendados, consulte [Firmas de acceso compartido, Parte 1: Descripción del modelo SAS](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md).

