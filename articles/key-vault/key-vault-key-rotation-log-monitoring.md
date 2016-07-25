<properties
	pageTitle="Configuración del Almacén de claves con rotación y auditoría integrales | Microsoft Azure"
	description="Utilice este tutorial para establecer la configuración con rotación de claves y supervisar los registros del almacén de claves"
	services="key-vault, automation,app-service\logic"
	documentationCenter=""
	authors="swgriffith"
	manager=""
	tags=""/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/05/2016"
	ms.author="jodehavi;stgriffi"/>
#Configuración del Almacén de claves con rotación de claves y auditoría integrales

##Introducción

Una vez que haya creado el Almacén de claves de Azure, podrá utilizarlo para guardar las claves y los secretos. Ya no será necesario que las claves y secretos se guarden en las aplicaciones, sino que las aplicaciones solicitarán las claves al almacén cuando lo necesiten. De este modo, puede actualizar las claves y los secretos sin que esto afecte al rendimiento de la aplicación, lo que brinda un amplio abanico de posibilidades en lo que respecta a la administración de las claves y los secretos.

En este artículo, se explica paso a paso un ejemplo sobre el uso del Almacén de claves de Azure para guardar un secreto. En este caso, se trata de una clave de la cuenta de Almacenamiento de Azure a la que accede una aplicación. El artículo también incluye una demostración de la implementación de una rotación programada de dicha clave. Por último, se explica paso a paso cómo supervisar los registros de auditoría del almacén de claves y cómo generar alertas cuando se realizan solicitudes inesperadas.

> [AZURE.NOTA] En este tutorial, no se explica en detalle la configuración inicial del Almacén de claves de Azure. Para más información sobre este tema, consulte [Introducción al Almacén de claves de Azure](key-vault-get-started.md). O bien, para obtener instrucciones de la interfaz de la línea de comandos entre plataformas, consulte [este tutorial equivalente](key-vault-manage-with-cli.md).

##Configuración del Almacén de claves

Para que una aplicación pueda recuperar un secreto del Almacén de claves de Azure, primero debe crear el secreto y guardarlo en el almacén. Esta operación puede hacerse fácilmente a través de PowerShell, tal y como se indica a continuación.

Inicie una sesión de PowerShell de Azure e inicie sesión en su cuenta de Azure con el siguiente comando:

```powershell
Login-AzureRmAccount
```

En la ventana emergente del explorador, escriba el nombre de usuario y la contraseña de su cuenta de Azure. Azure PowerShell obtendrá todas las suscripciones que están asociadas a esta cuenta y, de forma predeterminada, usará la primera.

Si tiene varias suscripciones, es posible que deba especificar la que se usó para crear el Almacén de claves de Azure. Escriba lo siguiente para ver las suscripciones de su cuenta:

```powershell
Get-AzureRmSubscription
```

A continuación, para especificar la suscripción asociada al Almacén de claves que registrará, escriba:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID> 
```

Dado que en este artículo se explica cómo se guarda una clave de cuenta de almacenamiento como un secreto, deberá obtener dicha clave.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Una vez recuperado el secreto (en este caso, la clave de cuenta de almacenamiento), deberá convertir la clave en una cadena segura y crear un secreto con ese valor en el almacén de claves.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
A continuación, tendrá que obtener el URI del secreto que acaba de crear. Este URI se utilizará en un paso posterior, cuando llame al Almacén de claves para recuperar el secreto. Ejecute el siguiente comando de PowerShell y anote el valor de ‘Id’, que es el URI del secreto.

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

##Configuración de la aplicación

Ahora que el secreto está guardado, tiene que recuperarlo y usarlo en el código. Para hacerlo, deberá seguir algunos pasos: el más importante es registrar la aplicación con Azure Active Directory y proporcionar al Almacén de claves de Azure la información de la aplicación para que pueda permitir que se realicen solicitudes desde dicha aplicación.

> [AZURE.NOTA] La aplicación debe crearse en el mismo inquilino de Azure Active Directory que el del Almacén de claves.

En primer lugar, abra la pestaña Aplicaciones de Azure Active Directory.

![Abra Aplicaciones en Azure AD](./media/keyvault-keyrotation/AzureAD_Header.png)

Elija 'Agregar' para agregar una nueva aplicación a Azure AD.

![Elija Agregar aplicación](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Deje el tipo de aplicación como ‘Aplicación web y/o API web’ y asigne un nombre a la aplicación.

![Asigne un nombre a la aplicación](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Especifique un valor en ‘URL de inicio de sesión’ y en ‘URI de id. de aplicación’. En esta demostración, puede especificar los valores que desee y cambiarlos más adelante.

![Especifique los URI necesarios](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

Cuando la aplicación se agregue a Azure AD, accederá automáticamente a la página de la aplicación. Una vez aquí, deberá hacer clic en la pestaña ‘Configurar’ para buscar y copiar el valor de ‘Id. de cliente’. Anote este valor, ya que lo necesitará posteriormente.

A continuación, tiene que generar una clave para que la aplicación pueda interactuar con Azure AD. Puede crear esta clave en la sección ‘Claves’ de la pestaña ‘Configuración’. No olvide anotar esta nueva clave de la aplicación de Azure AD, ya que la necesitará más adelante.

![Claves de aplicación de Azure AD](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Antes de definir en el Almacén de claves las llamadas que se realizarán desde la aplicación, deberá proporcionar al Almacén de claves los datos de la aplicación y sus permisos. El comando siguiente toma el nombre del almacén y el identificador de cliente de la aplicación de Azure AD y concede a la aplicación acceso ‘Get’ al almacén de claves.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Llegados a este punto, ya tiene todo listo para empezar a crear las llamadas a la aplicación. En primer lugar, deberá instalar en la aplicación los paquetes NuGet necesarios para poder interactuar con el Almacén de claves de Azure y Azure Active Directory. En la Consola del Administrador de paquetes de Visual Studio, escriba los siguientes comandos. Tenga en cuenta que, cuando se redactó este artículo, la versión más reciente del paquete de Active Directory era la 3.10.305231913; por tanto, deberá confirmar que se trata de la última versión y actualizar en caso necesario.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

En el código de la aplicación, cree una clase que contenga el método de autenticación de Active Directory. En este ejemplo, la clase se llama ‘Utils’. Por tanto, tendrá que agregar la siguiente instrucción using.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

A continuación, agregue el método siguiente para recuperar el token JWT de Azure AD. Es posible que, para facilitar el mantenimiento, quiera trasladar los valores de cadena codificados de forma rígida a la configuración del sitio web o la aplicación.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Por último, puede agregar el código necesario para llamar al Almacén de claves y recuperar el valor del secreto. Lo primero que debe hacer es agregar la siguiente instrucción using.

```csharp
using Microsoft.Azure.KeyVault;
```

A continuación, agregará las llamadas de método para invocar al Almacén de claves y recuperar el secreto. En este método, proporcionará el URI del secreto que guardó en un paso anterior. Observe el uso del método GetToken en la clase Utils que creó anteriormente.
    
```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Cuando ejecute la aplicación, debería estar autenticado en Azure Active Directory y recuperar el valor del secreto del Almacén de claves de Azure.

##Rotación de claves con Automatización de Azure

Existen varias opciones para implementar una estrategia de rotación de los valores que se guardan como secretos del Almacén de claves de Azure. Los secretos pueden rotarse mediante un proceso manual, mediante programación a través de llamadas a API o mediante un script de automatización. En este artículo, utilizaremos Azure PowerShell y Automatización de Azure para cambiar una clave de acceso de la cuenta de Almacenamiento de Azure y después actualizaremos un secreto del almacén de claves con esa nueva clave.

Para que Automatización de Azure pueda establecer los valores del secreto en el almacén de claves, deberá obtener el identificador de cliente de la conexión 'AzureRunAsConnection', que se creó al establecer la instancia de Automatización de Azure. Para obtener este identificador, puede seleccionar 'Recursos' en la instancia de Automatización de Azure. A continuación, seleccione 'Conexiones' y el nombre principal de servicio 'AzureRunAsConnection'. Conviene que anote el 'Identificador de la aplicación'.

![Identificador de cliente de Automatización de Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Es posible que, mientras se encuentra en la ventana Recursos, desee elegir los módulos. En ’Módulo’, podrá seleccionar la 'galería', así como buscar e 'importar' las versiones actualizadas de cada uno de los siguientes módulos.

	Azure
	Azure.Storage	
	AzureRM.Profile
	AzureRM.KeyVault
	AzureRM.Automation
	AzureRM.Storage
	
> [AZURE.NOTA] Cuando se redactó este artículo, los módulos que aparecen arriba eran los únicos que debían actualizarse para el script que se muestra más abajo. Si se produce algún error en el trabajo de automatización, deberá confirmar que ha importado todos los módulos necesarios y sus dependencias.

Una vez que haya recuperado el identificador de aplicación de la conexión de Automatización de Azure, deberá notificar al Almacén de claves de Azure que la aplicación tiene acceso para actualizar los secretos del almacén. Para ello, puede utilizar el siguiente comando de PowerShell.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

A continuación, seleccione el recurso 'Runbooks' en la instancia de Automatización de Azure y después 'Agregar un Runbook'. Seleccione 'Creación rápida'. Asigne un nombre al Runbook y seleccione 'PowerShell' como tipo del Runbook. Si lo desea, puede agregar una descripción. Por último, haga clic en 'Crear'.

![Crear Runbook](./media/keyvault-keyrotation/Create_Runbook.png)

En el panel del editor del nuevo Runbook, pegue el siguiente script de PowerShell.

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -StorageAccountName $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

Si desea probar el script, puede seleccionar 'Panel de prueba'. Una vez que el script se ejecute sin errores, puede seleccionar la opción 'Publicar' y aplicar después una programación para el Runbook desde el panel de configuración.

##Canalización de auditoría del Almacén de claves

Cuando configure un Almacén de claves de Azure, puede habilitar la auditoría para que se recopilen registros de las solicitudes de acceso a dicho almacén. Estos registros se guardan en una cuenta de Almacenamiento de Azure designada y pueden extraerse, supervisarse y analizarse. A continuación, se muestra paso a paso un escenario que el que se usan registros de auditoría de Funciones de Azure, Azure Logic Apps y el Almacén de claves para crear una canalización que envía un correo electrónico cuando una aplicación cuyo identificador coincide con el de la aplicación web recupera los secretos del almacén.

En primer lugar, tendrá que habilitar los registros en el Almacén de claves. Para ello, puede utilizar los siguientes comandos de PowerShell (consulte [aquí](key-vault-logging.md) una explicación detallada):

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>' 
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Una vez habilitados, comenzarán a recopilarse registros de auditoría en la cuenta de almacenamiento designada. Estos registros incluirán eventos acerca de qué usuario, cómo y cuándo ha obtenido acceso a los almacenes de claves.

> [AZURE.NOTA] Tras la operación del almacén de claves, el tiempo máximo para acceder a la información de registro es de 10 minutos. En la mayoría de los casos, será más rápido que esto.

El siguiente paso consiste en la [creación de una cola del Bus de servicio de Azure](../service-bus/service-bus-dotnet-get-started-with-queues.md). La cola estará en la misma ubicación en la que se insertan los registros de auditoría del almacén de claves. Una vez en la cola, la aplicación lógica los seleccionará y trabajará con ellos. El proceso para crear un Bus de servicio es relativamente sencillo. A continuación se indican los principales pasos:

1. Cree un espacio de nombres de Bus de servicio. Si ya tiene uno que quiera usar, puede saltar al paso 2.
2. Busque el Bus de servicio en el portal y seleccione el espacio de nombres en el que quiere crear la cola.
3. Seleccione Nuevo, Bus de servicio -> Cola y especifique los datos necesarios.
4. Recopile la información de la conexión del Bus de servicio. Para ello, elija el espacio de nombres y haga clic en _Información de conexión_. Va a necesitar esta información en el apartado siguiente.

A continuación, debe [crear una función de Azure](../azure-functions/functions-create-first-azure-function.md) para sondear los registros del Almacén de claves incluidos en la cuenta de almacenamiento y recopilar nuevos eventos. Esta función se desencadenará en función de una programación.

Cree una función de Azure. Para ello, elija Nuevo -> Function APP en el portal. Durante el proceso de creación, puede usar un plan de hospedaje existente o crear uno nuevo. También puede utilizar un plan de hospedaje dinámico. Encontrará más información sobre las opciones de hospedaje de funciones [aquí](../azure-functions/functions-scale.md).

Una vez creada la función de Azure, acceda a ella y, en la pantalla de inicio, elija una función de temporizador y C#. A continuación, haga clic en **Crear**.

![Hoja de inicio de Funciones de Azure](./media/keyvault-keyrotation/Azure_Functions_Start.png)

En la pestaña _Desarrollar_, sustituya el código de run.csx por el siguiente:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging; 
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log) 
{ 
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //required to order by time as they may not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob) 
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```
> [AZURE.NOTA] No olvide reemplazar las variables del código anterior para que apunten a la cuenta de almacenamiento en la que se escriben los registros del Almacén de claves, al Bus de servicio que creó anteriormente y a la ruta de acceso específica de los registros de almacenamiento del almacén de claves.

La función selecciona el archivo de registro más reciente de la cuenta de almacenamiento en la que se escriben los registros del Almacén de claves, recopila los últimos eventos de ese archivo y los inserta en una cola del Bus de servicio. Como un único archivo puede tener varios eventos (por ejemplo, los eventos registrados durante toda una hora), crearemos un archivo _sync.txt_. La función también consultará este archivo para determinar la marca de tiempo del último evento seleccionado. De este modo, nos aseguramos de que no insertamos el mismo evento varias veces. Este archivo _sync.txt_ solo contiene la marca de tiempo del último evento encontrado. Cuando los registros se han cargado, deben ordenarse con arreglo a la marca de tiempo para garantizar que el orden es el correcto.

En esta función, vamos a hacer referencia a un par de bibliotecas adicionales que no están disponibles de forma predeterminada en Funciones de Azure. Para incluir estas bibliotecas, necesitamos que Funciones de Azure las extraiga mediante NuGet. Elija la opción _Ver archivos_

![Opción Ver archivos](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

y agregue un nuevo archivo denominado _project.json_ con el contenido siguiente:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```
Cuando haga clic en _Guardar_, este archivo desencadenará Funciones de Azure para que descargue los binarios necesarios.

Cambie a la pestaña **Integrar** y asigne al parámetro del temporizador un nombre descriptivo para usarlo en la función. En el código anterior, el nombre esperado para el temporizador es _myTimer_. Especifique una [expresión CRON](../app-service-web/web-sites-create-web-jobs.md#CreateScheduledCRON) del modo siguiente: 0 * * * * * para el temporizador, lo que hará que la función se ejecute una vez por minuto.

En la misma pestaña **Integrar**, agregue una entrada que sea de tipo _Almacenamiento de blobs de Azure_. Esta entrada apuntará al archivo _sync.txt_, que contiene la marca de tiempo del último evento que consultó la función. El parámetro name se encargará de que esté disponible en la función. En el código anterior, la entrada de Almacenamiento de blobs de Azure espera que el parámetro name sea _inputBlob_. Elija la cuenta de almacenamiento en la que se incluirá el archivo _sync.txt_ (puede ser la misma u otra distinta) y, en el campo de ruta de acceso, especifique la ruta de acceso en la que se encuentra el archivo utilizando el formato {nombre-contenedor}/path/to/sync.txt.

Agregue una salida que sea de tipo _Almacenamiento de blobs de Azure_. Esta salida también apuntará al archivo _sync.txt_ que acaba de definir en la entrada. La función utilizará esta salida para escribir la marca de tiempo del último evento que consultó. En el código anterior, el nombre esperado para esta parámetro es _outputBlob_.

Llegados a este punto, la función ya está lista. No olvide volver a la pestaña **Desarrollar** y _guardar_ el código. Compruebe en la ventana de salida si hay algún error de compilación y, de ser así, corrija los errores como corresponda. Si el código se compila correctamente, debería ejecutarse, comprobar cada minuto los registros del Almacén de claves e insertar nuevos eventos en la cola del Bus de servicio definida. Cada vez que la función se desencadena, debería aparecer la información de los registros en la ventana del registro.

###Aplicación lógica de Azure

Ahora, tendremos que crear una aplicación lógica de Azure que seleccione los eventos que la función inserta en la cola del Bus de servicio, analice el contenido y envíe un correo electrónico si se cumple una determinada condición.

[Cree una aplicación lógica ](../app-service-logic/app-service-logic-create-a-logic-app.md) en Nuevo -> Aplicación lógica.

Una vez creada, acceda a ella y elija _editar_. En el editor de la aplicación lógica, elija la API administrada de la _cola de Bus de servicio_ y especifique las credenciales del Bus de servicio para conectarla a la cola.

![Bus de servicio de la aplicación lógica de Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

A continuación, elija _Agregar una condición_. En la condición, cambie al _editor avanzado_ y escriba lo siguiente (no olvide cambiar APP\_ID por el valor real de APP\_ID de la aplicación web):

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

En esencia, esta expresión devolverá **false** si la propiedad **appid** del evento entrante (que es el cuerpo del mensaje del Bus de servicio) no es el **appid** de la aplicación.

Ahora, cree una acción en la opción _If no, do nothing..._ (Si no, no hacer nada).

![Elegir una acción en la aplicación lógica de Azure](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

En la acción, elija _Office 365 - send email_ (Office 365 - enviar correo electrónico). Rellene los campos para crear el correo electrónico que se enviará cuando la condición definida devuelva false. Si no tiene Office 365, puede buscar otras alternativas.

En este momento, tiene una canalización integral que, una vez por minuto, busca nuevos registros de auditoría del Almacén de claves. Todos los registros que encuentre los insertará en una cola del Bus de servicio. La aplicación lógica se desencadenará en cuanto un nuevo mensaje llegue a la cola y, si el identificador de aplicación del evento no coincide con el identificador de la aplicación de llamada, enviará un correo electrónico.

<!---HONumber=AcomDC_0713_2016-->