El elemento **TodoItemController** existente se actualiza de manera que el método **PostTodoItem** genera una SAS cuando se inserta un nuevo elemento Todo.

1.  Si todavía no ha creado su cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento](/en-us/manage/services/storage/how-to-create-a-storage-account).

2.  En el Portal de administración, haga clic en **Almacenamiento**, haga clic en la cuenta de almacenamiento y, a continuación, haga clic en **Manage Keys**.

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account.png)

1.  Tome nota de los valores en los campos **Storage Account Name** y **Access Key**.

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png)

2.  En su servicio móvil, haga clic en la pestaña **Configure**, desplácese hacia abajo hasta **App settings** y escriba un par de **Name** y **Value** para cada uno de los siguientes que obtuvo desde la cuenta de almacenamiento y, a continuación, haga clic en **Save**.

    -   `STORAGE_ACCOUNT_NAME`
    -   `STORAGE_ACCOUNT_ACCESS_KEY`

    ![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

    La clave de acceso de la cuenta de almacenamiento se almacena cifrada en la configuración de aplicaciones. Puede tener acceso a esta clave desde cualquier script de servidor en tiempo de ejecución. Para obtener más información, consulte [Configuración de aplicación](http://msdn.microsoft.com/en-us/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7).

3.  En el Explorador de soluciones de Visual Studio, abra el archivo Web.config para el proyecto de servicios móviles y agregue la siguiente nueva configuración de aplicaciones reemplazando los marcadores de posición por el nombre de la cuenta de almacenamiento y la clave de acceso que estableció como configuración de la aplicación en el portal:

         <add key="STORAGE_ACCOUNT_NAME" value="**your_account_name**" />
         <add key="STORAGE_ACCOUNT_ACCESS_KEY" value="**your_access_token_secret**" />

    El servicio móvil usa esta configuración almacenada cuando se ejecuta en el equipo local, lo que le permite realizar una prueba del código antes de publicarlo. Cuando se ejecuta en Azure, el servicio móvil usa en su lugar los valores de la configuración de aplicaciones en el portal e ignora esta configuración de proyecto.

4.  En la carpeta Controladores, abra el archivo TodoItemController.cs y agregue las siguientes directivas **using**:

        using System;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Blob;

5.  Reemplace el método existente **PostTodoItem** por el siguiente código:

        var azure = require('azure');
        var qs = require('querystring');
        var appSettings = require('mobileservice-config').appSettings;
            
        function insert(item, user, request) {
            // Obtener la configuración de la cuenta de almacenamiento de la configuración de aplicaciones. 
            var accountName = appSettings.STORAGE_ACCOUNT_NAME;
            var accountKey = appSettings.STORAGE_ACCOUNT_ACCESS_KEY;
            var host = accountName + '.blob.core.windows.net';
            
            if ((typeof item.containerName !== "undefined") && (
            item.containerName !== null)) {
                // Establezca el nombre del contenedor del almacén de BLOB en el elemento, que debe estar en minúsculas.
                item.containerName = item.containerName.toLowerCase();
            
                // Si todavía no existe, cree el contenedor 
                // con acceso de lectura público para los blobs.        
                var blobService = azure.createBlobService(accountName, accountKey, host);
                blobService.createContainerIfNotExists(item.containerName, {
                    publicAccessLevel: 'blob'
                }, function(error) {
                    if(!error){
            
                        // Proporcione acceso de escritura al contenedor durante los 5 minutos siguientes.        
                        var sharedAccessPolicy = {
                            AccessPolicy: {
                                Permissions: azure.Constants.BlobConstants.SharedAccessPermissions.WRITE,
                                Expiry: new Date(new Date().getTime() + 5 * 60 * 1000)
                            }
                        };
            
                        // Genere la URL de carga con SAS para la nueva imagen.
                        var sasQueryUrl = 
                        blobService.generateSharedAccessSignature(item.containerName, 
                        item.resourceName, sharedAccessPolicy);
            
                        // Configurar la cadena de consulta.
                        item.sasQueryString = qs.stringify(sasQueryUrl.queryString);
            
                        // Configure la ruta completa sobre el nuevo elemento, 
                        // que se utiliza para el enlace de datos en el cliente. 
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

	![](./media/mobile-services-configure-blob-storage/mobile-insert-script-blob.png) 

	Así, se reemplaza la función que se invoca cuando se produce una inserción en la tabla TodoItem con un script nuevo. Este script nuevo genera una SAS nueva para la inserción, válida por 5 minutos, y asigna el valor de la SAS generada a la propiedad `sasQueryString` del elemento devuelto. La propiedad `imageUri` se establece también para la ruta de acceso del recurso del BLOB nuevo a fin de habilitar la visualización de imágenes durante el enlace en la interfaz de usuario de cliente.

    >[WACOM.NOTE] Este código crea una SAS para un BLOB individual. Si necesita cargar varios blobs en un contenedor utilizando el mismo SAS, puede llamar al método <a href="http://go.microsoft.com/fwlink/
    LinkId=390455" target="_blank">generateSharedAccessSignature method</a> con un nombre de recurso de blob vacío, como este: 
    <pre><code>blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);</code></pre>

A continuación, actualizará la aplicación de inicio rápido para agregar la funcionalidad de carga de imágenes usando la SAS generada en la inserción.

