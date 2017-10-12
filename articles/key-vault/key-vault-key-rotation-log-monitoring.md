---
title: "Configuración de Azure Key Vault con la auditoría y la rotación de claves de un extremo a otro | Microsoft Docs"
description: "Utilice este tutorial para establecer la configuración con rotación de claves y supervisar los registros del almacén de claves."
services: key-vault
documentationcenter: 
author: swgriffith
manager: mbaldwin
tags: 
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: jodehavi;stgriffi
ms.openlocfilehash: f98ba1e2da6924476392948a4d18c807d68e39e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-azure-key-vault-with-end-to-end-key-rotation-and-auditing"></a>Configuración de Azure Key Vault con la auditoría y la rotación de claves de un extremo a otro
## <a name="introduction"></a>Introducción
Una vez que haya creado el almacén de claves, podrá utilizarlo para guardar las claves y los secretos. Ya no será necesario que las claves y secretos se guarden en las aplicaciones, sino que las aplicaciones solicitarán las claves al almacén cuando lo necesiten. De este modo, puede actualizar las claves y los secretos sin que esto afecte al rendimiento de la aplicación, lo que brinda un amplio abanico de posibilidades en lo que respecta a la administración de las claves y los secretos.

En este artículo, se explica paso a paso un ejemplo sobre el uso de Azure Key Vault para guardar un secreto. En este caso, se trata de una clave de la cuenta de Azure Storage a la que accede una aplicación. El artículo también incluye una demostración de la implementación de una rotación programada de dicha clave. Por último, se explica paso a paso cómo supervisar los registros de auditoría del almacén de claves y cómo generar alertas cuando se realizan solicitudes inesperadas.

> [!NOTE]
> En este tutorial no se explica en detalle la configuración inicial del almacén de claves. Para obtener información, consulte [Introducción al Almacén de claves de Azure](key-vault-get-started.md). Para obtener instrucciones acerca de la interfaz de la línea de comandos para todas las plataformas, consulte [Administración del almacén de claves mediante CLI](key-vault-manage-with-cli2.md).
>
>

## <a name="set-up-key-vault"></a>Configuración del Almacén de claves
Para que una aplicación pueda recuperar un secreto de Key Vault, primero debe crear el secreto y guardarlo en el almacén. Para ello, abra una sesión de Azure PowerShell e inicie sesión en su cuenta de Azure con el siguiente comando:

```powershell
Login-AzureRmAccount
```

En la ventana emergente del explorador, escriba el nombre de usuario y la contraseña de su cuenta de Azure. PowerShell obtendrá todas las suscripciones asociadas a esta cuenta. PowerShell utiliza la primera de forma predeterminada.

Si tiene varias suscripciones, es posible que deba especificar la que se usó para crear el almacén de claves. Escriba lo siguiente para ver las suscripciones de su cuenta:

```powershell
Get-AzureRmSubscription
```

Para especificar la suscripción asociada al almacén de claves que registrará, escriba:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID>
```

Dado que en este artículo se explica cómo se guarda una clave de cuenta de almacenamiento como un secreto, debe obtener dicha clave.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Una vez recuperado el secreto (en este caso, la clave de la cuenta de almacenamiento), debe convertir la clave en una cadena segura y crear un secreto con ese valor en el almacén de claves.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
A continuación, obtenga el identificador URI para el secreto que creó. Este se utilizará en un paso posterior, cuando llame al almacén de claves para recuperar el secreto. Ejecute el siguiente comando de PowerShell y anote el valor de identificación, que es el URI del secreto:

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Configuración de la aplicación
Ahora que tiene un secreto almacenado, puede utilizar el código para recuperarlo y utilizarlo. Existen varios pasos obligatorios para conseguirlo. El primero y más importante consiste en registrar la aplicación con Azure Active Directory y proporcionar a Key Vault la información de la aplicación para que pueda permitir que se realicen solicitudes desde dicha aplicación.

> [!NOTE]
> La aplicación debe crearse en el mismo inquilino de Azure Active Directory que el del almacén de claves.
>
>

Abra la pestaña Aplicaciones de Azure Active Directory.

![Abra las aplicaciones de Azure Active Directory](./media/keyvault-keyrotation/AzureAD_Header.png)

Elija **AGREGAR** para agregar una aplicación a Azure Active Directory.

![Elija AGREGAR](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Deje el tipo de aplicación como **APLICACIÓN WEB Y/O API WEB** y asigne un nombre a la aplicación.

![Asigne un nombre a la aplicación](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Especifique un valor en **URL DE INICIO DE SESIÓN** y en **URI DE ID DE APLICACIÓN**. En esta demostración, puede especificar los valores que desee y cambiarlos más adelante si es necesario.

![Especifique los identificadores URI necesarios](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

Una vez que la aplicación se ha agregado a Azure Active Directory, accederá automáticamente a la página de la aplicación. Una vez aquí, deberá hacer clic en la pestaña **Configurar** para buscar y copiar el valor de **ID de cliente**. Anote este valor, ya que lo necesitará posteriormente.

A continuación, genere una clave para su aplicación para que pueda interactuar con Azure Active Directory. Puede crear esta clave en la sección **Claves** de la pestaña **Configuración**. No olvide anotar esta nueva clave de la aplicación de Azure Active Directory, ya que la necesitará más adelante.

![Claves de aplicaciones de Azure Active Directory](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Antes de definir en el almacén de claves las llamadas que se realizarán desde la aplicación, debe proporcionar al almacén de claves los datos de la aplicación y sus permisos. El comando siguiente toma el nombre del almacén y el identificador de cliente de la aplicación de Azure Active Directory y concede a la aplicación acceso **Get** al almacén de claves.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Llegados a este punto, ya tiene todo listo para empezar a crear las llamadas a la aplicación. En primer lugar, deberá instalar en la aplicación los paquetes NuGet necesarios para poder interactuar con Azure Key Vault y Azure Active Directory. En la Consola del Administrador de paquetes de Visual Studio, escriba los siguientes comandos. Cuando se redactó este artículo, la versión más reciente del paquete de Azure Active Directory era la 3.10.305231913; por tanto, debe confirmar que se trata de la última versión y actualizar en caso necesario.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

En el código de la aplicación, cree una clase que contenga el método de autenticación de Azure Active Directory. En este ejemplo, la clase se llama **Utils**. Agregue la siguiente instrucción using:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

A continuación, agregue el método siguiente para recuperar el token JWT de Azure Active Directory. Es posible que, para facilitar el mantenimiento, quiera trasladar los valores de cadena codificados de forma rígida a la configuración del sitio web o la aplicación.

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

Agregue el código necesario para llamar a Key Vault y recuperar el valor del secreto. Lo primero que debe hacer es agregar la siguiente instrucción using:

```csharp
using Microsoft.Azure.KeyVault;
```

Agregue las llamadas de método para invocar a Key Vault y recuperar el secreto. En este método, proporcione el URI del secreto que guardó en un paso anterior. Observe el uso del método **GetToken** en la clase **Utils** que creó anteriormente.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Cuando ejecute la aplicación, debería estar autenticado en Azure Active Directory y recuperar el valor del secreto de Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Rotación de claves mediante Azure Automation
Existen varias opciones para implementar una estrategia de rotación de los valores que se guardan como secretos del Almacén de claves de Azure. Los secretos pueden rotarse mediante un proceso manual, mediante programación a través de llamadas a API o mediante un script de automatización. En este artículo, utilizaremos Azure PowerShell y Azure Automation para cambiar una clave de acceso de la cuenta de Azure Storage. A continuación, podrá actualizar un secreto del almacén de claves con esa nueva clave.

Para que Azure Automation pueda establecer los valores del secreto en el almacén de claves, deberá obtener el identificador de cliente de la conexión AzureRunAsConnection que se creó al establecer la instancia de Azure Automation. Para encontrar este identificador, seleccione **Recursos** en la instancia de Azure Automation. A continuación, seleccione **Conexiones** y el nombre principal de servicio **AzureRunAsConnection**. Anote el **Identificador de la aplicación**.

![Identificador de cliente de Azure Automation](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

En **Recursos**, elija **Módulos**. En **Módulos**, seleccione **Galería** y, a continuación, busque e **importe** las versiones actualizadas de cada uno de los siguientes módulos:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage


> [!NOTE]
> Cuando se redactó este artículo, los módulos que aparecen arriba eran los únicos que debían actualizarse para el script que se muestra a continuación. Si se produce algún error en el trabajo de automatización, confirme que ha importado todos los módulos necesarios y sus dependencias.
>
>

Una vez que haya recuperado el identificador de aplicación de la conexión de Azure Automation, debe notificar al almacén de claves que la aplicación tiene acceso para actualizar los secretos del almacén. Para ello, puede utilizar el siguiente comando de PowerShell:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

A continuación, seleccione **Runbooks** en la instancia de Azure Automation y, después, seleccione **Agregar un runbook**. Seleccione **Creación rápida**. Asigne un nombre al runbook y seleccione **PowerShell** como tipo del runbook. Tiene la opción de agregar una descripción. Por último, haga clic en **Crear**.

![Creación de runbook](./media/keyvault-keyrotation/Create_Runbook.png)

En el panel del editor del nuevo runbook, pegue el siguiente script de PowerShell:

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
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

Si desea probar el script, seleccione **Panel de prueba**. Una vez que el script se ejecuta sin errores, puede seleccionar **Publicar** y aplicar después una programación para el runbook desde el panel de configuración.

## <a name="key-vault-auditing-pipeline"></a>Canalización de auditorías de Key Vault
Cuando configure un almacén de claves, puede habilitar la auditoría para que se recopilen registros de las solicitudes de acceso a dicho almacén. Estos registros se guardan en una cuenta de Azure Storage designada y pueden extraerse, supervisarse y analizarse. El siguiente escenario usa instancias de Azure Functions, Azure Logic Apps y Key Vault para crear una canalización que envía un correo electrónico cuando una aplicación cuyo identificador coincide con el de la aplicación web recupera los secretos del almacén.

En primer lugar, debe habilitar los registros en el almacén de claves. Para ello, puede utilizar los siguientes comandos de PowerShell (consulte una explicación detallada en [key-vault-logging](key-vault-logging.md)):

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>'
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Una vez habilitado, comenzarán a recopilarse registros de auditoría en la cuenta de almacenamiento designada. Estos registros incluirán eventos acerca de qué usuario, cómo y cuándo ha obtenido acceso a los almacenes de claves.

> [!NOTE]
> Puede acceder a la información de registro 10 minutos después de la operación del almacén de claves. Normalmente, tardará menos.
>
>

El siguiente paso consiste en la [creación de una cola de Azure Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). En ella se insertan los registros de auditoría del almacén de claves. Cuando los mensajes de registro de auditoría se encuentran en la cola, la aplicación lógica los recopila y actúa sobre ellos. Realice los siguientes pasos para crear una instancia de Service Bus:

1. Cree un espacio de nombres de Service Bus. Si ya tiene uno que quiera usar, puede saltar al paso 2.
2. Busque la instancia de Service Bus en Azure Portal y seleccione el espacio de nombres en el que quiere crear la cola.
3. Seleccione **Nuevo**, **Service Bus -> Cola** y especifique los datos necesarios.
4. Seleccione la información de la conexión de Service Bus. Para ello, elija el espacio de nombres y haga clic en **Información de conexión**. Va a necesitar esta información en la sección siguiente.

A continuación, debe [crear una función de Azure](../azure-functions/functions-create-first-azure-function.md) para sondear los registros del almacén de claves incluidos en la cuenta de almacenamiento y recopilar nuevos eventos. Esta función se desencadenará en función de una programación.

Cree una función de Azure. Para ello, elija **Nuevo -> Function App** en Azure Portal. Durante el proceso de creación, puede usar un plan de hospedaje existente o crear uno nuevo. También puede utilizar un plan de hospedaje dinámico. Encontrará más información sobre las opciones de hospedaje de funciones en [Escalado de Azure Functions](../azure-functions/functions-scale.md).

Una vez creada la función de Azure, acceda a ella y elija una función de temporizador y C\#. A continuación, haga clic en **Crear esta función**.

![Hoja de inicio de Azure Functions](./media/keyvault-keyrotation/Azure_Functions_Start.png)

En la pestaña **Desarrollar**, sustituya el código de run.csx por el siguiente:

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


> [!NOTE]
> No olvide reemplazar las variables del código anterior para que apunten a la cuenta de almacenamiento en la que se escriben los registros de Key Vault, al Service Bus que creó anteriormente y a la ruta de acceso específica de los registros de almacenamiento del almacén de claves.
>
>

La función selecciona el archivo de registro más reciente de la cuenta de almacenamiento en la que se escriben los registros del almacén de claves, recopila los últimos eventos de ese archivo y los inserta en una cola de Service Bus. Como un único archivo puede tener varios eventos, debe crear un archivo sync.txt. La función también consultará este archivo para determinar la marca de tiempo del último evento seleccionado. De este modo, se asegura de que no se inserta el mismo evento varias veces. Este archivo sync.txt contiene la marca de tiempo del último evento encontrado. Cuando los registros se han cargado, deben ordenarse con arreglo a la marca de tiempo para garantizar que el orden es el correcto.

En esta función, vamos a hacer referencia a un par de bibliotecas adicionales que no están disponibles de forma predeterminada en Funciones de Azure. Para incluir estas bibliotecas, necesitamos que Azure Functions las extraiga mediante NuGet. Elija la opción **Ver archivos**.

![Opción Ver archivos](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Y agregue un archivo denominado project.json con el contenido siguiente:

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
Cuando haga clic en **Guardar**, Azure Functions descargará los binarios necesarios.

Cambie a la pestaña **Integrar** y asigne al parámetro del temporizador un nombre descriptivo para usarlo en la función. En el código anterior, el nombre esperado para el temporizador es *myTimer*. Especifique una [expresión CRON](../app-service/web-sites-create-web-jobs.md#CreateScheduledCRON) del modo siguiente: 0 \* \* \* \* \* para el temporizador que hará que la función se ejecute una vez por minuto.

En la misma pestaña **Integrar**, agregue una entrada que sea del tipo **Azure Blob Storage**. Esta entrada apuntará al archivo sync.txt , que contiene la marca de tiempo del último evento que consultó la función. El parámetro name se encargará de que esté disponible en la función. En el código anterior, la entrada de Azure Blob Storage espera que el parámetro name sea *inputBlob*. Elija la cuenta de almacenamiento donde residirá el archivo sync.txt (puede ser la misma o una diferente). En el campo ruta de acceso, proporcione la ruta donde se encuentra el archivo en el formato {nombre-contenedor}/path/to/sync.txt.

Agregue una salida que sea de tipo *Azure Blob Storage*. Esta salida también apuntará al archivo sync.txt que definió en la entrada. La función utilizará esta salida para escribir la marca de tiempo del último evento que consultó. En el código anterior, el nombre esperado para esta parámetro es *outputBlob*.

Llegados a este punto, la función ya está lista. No olvide volver a la pestaña **Desarrollar** y guardar el código. Compruebe en la ventana de salida si hay algún error de compilación y, de ser así, corrija los errores como corresponda. Si el código se compila correctamente, debería ya comprobar cada minuto los registros del almacén de claves e insertar los nuevos eventos en la cola de Service Bus definida. Cada vez que la función se desencadena, debería aparecer la información de los registros en la ventana del registro.

### <a name="azure-logic-app"></a>Aplicación lógica de Azure
Ahora, debe crear una aplicación lógica de Azure que seleccione los eventos que la función inserta en la cola de Service Bus, analice el contenido y envíe un correo electrónico si se cumple una determinada condición.

[Cree una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md) en **Nuevo -&gt; Aplicación lógica**.

Una vez creada, acceda a ella y elija **editar**. En el editor de la aplicación lógica, elija la **cola de Service Bus** y especifique las credenciales de Service Bus para conectarla a la cola.

![Bus de servicio de la aplicación lógica de Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

A continuación, elija **Agregar una condición**. En la condición, cambie al editor avanzado y escriba el código siguiente (no olvide cambiar APP_ID por el valor real de APP_ID de la aplicación web):

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

En esencia, esta expresión devolverá **false** si la propiedad *appid* del evento entrante (que es el cuerpo del mensaje de Service Bus) no es el *appid* de la aplicación.

Ahora, cree una acción en la opción **If no, do nothing** (Si no, no hacer nada).

![Elegir una acción en la aplicación lógica de Azure](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

En la acción, elija **Office 365 - send email**(Office 365 - enviar correo electrónico). Rellene los campos para crear el correo electrónico que se enviará cuando la condición definida devuelva **false**. Si no tiene Office 365, puede buscar otras alternativas para conseguir los mismos resultados.

En este momento, tiene una canalización integral que, una vez por minuto, busca nuevos registros de auditoría del almacén de claves. Inserta los nuevos registros que encuentra en una cola de Service Bus. La aplicación lógica se activa cuando un nuevo mensaje llega a la cola. Si el *appid* dentro del evento no coincide con el identificador de la aplicación de la aplicación que llama, envía un correo electrónico.
