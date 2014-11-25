Se registra un nuevo script de inserción que genera un SAS cuando se inserta un nuevo "Todo item".

1.  Si todavía no ha creado su cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento][Creación de una cuenta de almacenamiento].

2.  En el Portal de administración, haga clic en **Almacenamiento**, haga clic en la cuenta de almacenamiento y, a continuación, haga clic en **Manage Keys**.

    ![][0]

3.  Tome nota de los valores en los campos **Storage Account Name** y **Access Key**.

    ![][1]

4.  En su servicio móvil, haga clic en la pestaña **Configure**, desplácese hacia abajo hasta **App settings** y escriba un par de **Name** y **Value** para cada uno de los siguientes que obtuvo desde la cuenta de almacenamiento y, a continuación, haga clic en **Save**.

    -   `STORAGE_ACCOUNT_NAME`
    -   `STORAGE_ACCOUNT_ACCESS_KEY`

    ![][2]

    La clave de acceso de la cuenta de almacenamiento se almacena cifrada en la configuración de aplicaciones. Puede tener acceso a esta clave desde cualquier script de servidor en tiempo de ejecución. Para obtener más información, consulte [Configuración de aplicación][Configuración de aplicación].

5.  Haga clic en la pestaña **Data** y, a continuación, haga clic en la tabla **TodoItem**.

    ![][3]

6.  En **todoitem**, haga clic en la pestaña **Script** y seleccione **Insert**, reemplace la función de inserción por el código siguiente y, a continuación, haga clic en **Save**:

        var azure = require('azure');
        var qs = require('querystring');
        var appSettings = require('mobileservice-config').appSettings;

        function insert(item, user, request) {
            // Get storage account settings from app settings. 
            var accountName = appSettings.STORAGE_ACCOUNT_NAME;
            var accountKey = appSettings.STORAGE_ACCOUNT_ACCESS_KEY;
            var host = accountName + '.blob.core.windows.net';

            if ((typeof item.containerName !== "undefined") && (
            item.containerName !== null)) {
                // Set the BLOB store container name on the item, which must be lowercase.
                item.containerName = item.containerName.toLowerCase();

                // If it does not already exist, create the container 
                // with public read access for blobs.        
                var blobService = azure.createBlobService(accountName, accountKey, host);
                blobService.createContainerIfNotExists(item.containerName, {
                    publicAccessLevel: 'blob'
                }, function(error) {
                    if (!error) {

                        // Provide write access to the container for the next 5 mins.        
                        var sharedAccessPolicy = {
                            AccessPolicy: {
                                Permissions: azure.Constants.BlobConstants.SharedAccessPermissions.WRITE,
                                Expiry: new Date(new Date().getTime() + 5 * 60 * 1000)
                            }
                        };

                        // Generate the upload URL with SAS for the new image.
                        var sasQueryUrl = 
                        blobService.generateSharedAccessSignature(item.containerName, 
                        item.resourceName, sharedAccessPolicy);

                        // Set the query string.
                        item.sasQueryString = qs.stringify(sasQueryUrl.queryString);

                        // Set the full path on the new new item, 
                        // which is used for data binding on the client. 
                        item.imageUri = sasQueryUrl.baseUrl + sasQueryUrl.path;

                    } else {
                        console.error(error);
                    }
                    request.execute();
                });
            } else {
                request.execute();
            }
        }

    ![][4]

    Así, se reemplaza la función que se invoca cuando se produce una inserción en la tabla TodoItem con un script nuevo. Este script nuevo genera una SAS nueva para la inserción, válida por 5 minutos, y asigna el valor de la SAS generada a la propiedad `sasQueryString` del elemento devuelto. La propiedad `imageUri` se establece también para la ruta de acceso del recurso del BLOB nuevo a fin de habilitar la visualización de imágenes durante el enlace en la interfaz de usuario de cliente.

    > [WACOM.NOTE] Este código crea una SAS para un BLOB individual. Si necesita cargar varios blobs en un contenedor utilizando la misma SAS, puede llamar al [método generateSharedAccessSignature][método generateSharedAccessSignature] con un nombre de recurso de blob vacío, como este:
    >
    >     blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);
    >
    > </p>

A continuación, actualizará la aplicación de inicio rápido para agregar la funcionalidad de carga de imágenes usando la SAS generada en la inserción.





  [Creación de una cuenta de almacenamiento]: /es-es/manage/services/storage/how-to-create-a-storage-account
  [0]: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-account.png
  [1]: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png
  [2]: ./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png
  [Configuración de aplicación]: http://msdn.microsoft.com/es-es/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
  [3]: ./media/mobile-services-configure-blob-storage/mobile-portal-data-tables.png
  [4]: ./media/mobile-services-configure-blob-storage/mobile-insert-script-blob.png
  [método generateSharedAccessSignature]: http://go.microsoft.com/fwlink/?LinkId=390455
