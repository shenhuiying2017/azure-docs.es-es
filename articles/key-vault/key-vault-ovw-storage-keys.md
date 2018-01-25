---
ms.assetid: 
title: Claves de cuenta de almacenamiento de Azure Key Vault
description: "Las claves de cuenta de almacenamiento proporcionan una integración sin problemas entre Azure Key Vault y el acceso basado en claves a la cuenta de Azure Storage."
ms.topic: article
services: key-vault
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: 6ebac5fc90e259b19e0a4103a732754384232a44
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="azure-key-vault-storage-account-keys"></a>Claves de cuenta de almacenamiento de Azure Key Vault

Antes de que existieran las claves de cuenta de almacenamiento de Azure Key Vault, los desarrolladores tenían que administrar sus propias claves de cuenta de Azure Storage (ASA) y rotarlas manualmente o mediante la automatización externa. Ahora las claves de cuenta de almacenamiento de Key Vault se implementan como [secretos de Key Vault](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) para autenticar con una cuenta de Azure Storage. 

La característica de clave de cuenta de almacenamiento de Azure (ASA) administra la rotación secreta de forma automática. También elimina la necesidad de tener contacto directo con una clave de ASA, ya que ofrece como método las Firmas de acceso compartido (SAS). 

Para más información general sobre las cuentas de Azure Storage, vea [Acerca de las cuentas de Azure Storage](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Interfaces admitidas

Encontrará una lista completa y vínculos a nuestras interfaces de programación y scripting en la [Guía del desarrollador de Key Vault](key-vault-developers-guide.md#coding-with-key-vault).


## <a name="what-key-vault-manages"></a>¿Qué administra Key Vault?

Cuando se usan las claves de la cuenta de almacenamiento administrada, Key Vault realiza varias funciones de administración interna en su nombre.

- Azure Key Vault administra las claves de una cuenta de Azure Storage (ASA).
    - Internamente, Azure Key Vault puede enumerar (sincronizar) las claves con una cuenta de Azure Storage.  
    - Azure Key Vault vuelve a generar (rotar) las claves periódicamente. 
    - Los valores de clave nunca se devuelven como respuesta al autor de la llamada. 
    - Azure Key Vault administra las claves de las cuentas de almacenamiento y de las cuentas de almacenamiento clásicas. 
- Azure Key Vault le permite a usted, como propietario del almacén/objeto, crear definiciones de SAS (SAS de cuenta o de servicio).
    - El valor de SAS, creado mediante la definición de SAS, se devuelve como un secreto a través de la ruta de acceso de URI de REST. Para más información, vea [Azure Key Vault storage account operations (Operaciones de cuenta de almacenamiento de Azure Key Vault)](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations).

## <a name="naming-guidance"></a>Instrucciones de nomenclatura

- Los nombres de cuentas de almacenamiento deben tener entre 3 y 24 caracteres, y solo pueden contener números y letras minúsculas.  
- Los nombres de las definiciones de SAS deben tener una longitud de entre 1 y 102 caracteres y contener solo los caracteres 0-9, a-z, A-z.

## <a name="developer-experience"></a>Experiencia para el desarrollador

### <a name="before-azure-key-vault-storage-keys"></a>Antes de la existencia de las claves de cuenta de almacenamiento de Azure Key Vault 

Anteriormente, para obtener acceso a Azure Storage, los desarrolladores debían realizar los procedimientos siguientes con una clave de cuenta de almacenamiento. 
 
```powershell
//create an Azure Storage Account using a connection string containing an account name and a storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>A partir de la existencia de las claves de cuenta de almacenamiento de Azure Key Vault 

```powershell
//Make sure to set storage permissions appropriately on your key vault
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourVault' -ObjectId yourObjectId -PermissionsToStorage all

//Get secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  

-AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get a SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If your SAS token is about to expire, Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Instrucciones para desarrolladores

- Permita que solo Key Vault administre las claves de ASA. No intente administrarlas usted mismo, ya que interferiría con los procesos de Key Vault. 
- No permita que más de un objeto de Key Vault administre las claves de ASA. 
- Si tiene que volver a generar manualmente las claves de ASA, se recomienda hacerlo a través de Key Vault. 

## <a name="getting-started"></a>Introducción

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Configuración de permisos de control de acceso basado en roles (RBAC)

La identidad de aplicación de Azure Key Vault necesita permisos para *enumerar* y *regenerar* las claves de una cuenta de almacenamiento. Configure estos permisos mediante los siguientes pasos:

- Obtenga ObjectId de la identidad de Azure Key Vault: 

    `Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093`

- Asigne el rol Operador de claves de almacenamiento a la identidad de Azure Key Vault: 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > En el caso de un tipo de cuenta clásica, establezca el parámetro del rol en *"Rol de servicio de operador de claves de cuentas de almacenamiento clásicas"*.

## <a name="working-example"></a>Ejemplo práctico

En el siguiente ejemplo se muestra cómo crear una cuenta de almacenamiento de Azure administrada con Key Vault y las definiciones de Firma de acceso compartido (SAS) asociadas.

### <a name="assumptions"></a>Supuestos

Se proporcionan las siguientes instrucciones para este ejemplo práctico.

- El recurso de almacenamiento se encuentra aquí: */subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1*

- El nombre del almacén de claves es: *yourtest1*

### <a name="get-a-service-principal"></a>Obtener una entidad de servicio

```powershell
$yourKeyVaultServicePrincipalId = (Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

El resultado del comando anterior incluirá su entidad de servicio, que llamaremos *yourKeyVaultServicePrincipalId*. 

### <a name="set-permissions"></a>Establecer permisos

Asegúrese de que tiene los permisos de almacenamiento establecidos en *todo*. Puede obtener youruserPrincipalId y establecer permisos en el almacén mediante los siguientes comandos.

```powershell
$youruserPrincipalId = (Get-AzureRmADUser -SearchString "your user principal name").Id
```
Ahora busque su nombre y obtenga el ObjectId relacionado, que se usará al establecer permisos en el almacén.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourtest1' -ObjectId $youruserPrincipalId -PermissionsToStorage all
```

### <a name="allow-access"></a>Permitir acceso

El servicio Key Vault debe poder tener acceso a las cuentas de almacenamiento para que se pueda crear una cuenta de almacenamiento administrada y las definiciones de SAS.

```powershell
New-AzureRmRoleAssignment -ObjectId $yourKeyVaultServicePrincipalId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '/subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1'
```

### <a name="create-storage-account"></a>Crear cuenta de almacenamiento

Ahora, cree una cuenta de almacenamiento administrada y dos definiciones de SAS. La cuenta SAS proporciona acceso a Blob service con permisos diferentes.

```powershell
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="regeneration"></a>Regeneración

Establezca el período de regeneración con los siguientes comandos.

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Establecer las definiciones de SAS

Establezca las definiciones de SAS en Key Vault para la cuenta de almacenamiento administrada.

```powershell
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourtest1  -AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName yourtest1  -AccountName msak01 -Name blobsas2 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List,Write
```

### <a name="get-token"></a>Obtener el token

Obtenga los tokens de SAS correspondientes y realice llamadas al almacenamiento.

```powershell
$sasToken1 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas1).SecretValueText
$sasToken2 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas2).SecretValueText
```

### <a name="create-storage"></a>Crear el almacenamiento

Observe que, al intentar obtener acceso con *$sastoken1*, se produce un error, pero sí que se puede obtener acceso con *$sastoken2*. 

```powershell
$context1 = New-AzureStorageContext -SasToken $sasToken1 -StorageAccountName yourtest1
$context2 = New-AzureStorageContext -SasToken $sasToken2 -StorageAccountName yourtest1
Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt"  -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt"  -Context $context2
```

### <a name="example-summary"></a>Resumen del ejemplo

Se puede obtener acceso al contenido del blob de almacenamiento con el token de SAS que tiene acceso de escritura.

### <a name="relevant-powershell-cmdlets"></a>Cmdlets de PowerShell relevantes

- [Get-AzureKeyVaultManagedStorageAccount ](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount?view=azurermps-4.3.1)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)

## <a name="storage-account-onboarding"></a>Incorporación de cuentas de almacenamiento 

Ejemplo: como propietario de un objeto de Key Vault, agregue un objeto de cuenta de almacenamiento a Azure Key Vault para incorporar una cuenta de almacenamiento.

Durante la incorporación, Key Vault debe comprobar que la identidad de la cuenta que se está incorporando tiene permisos para *mostrar* y *volver a generar* las claves de almacenamiento. Para comprobar estos permisos, Key Vault obtiene un token OBO (del inglés On Behalf Of [en nombre de]) desde el servicio de autenticación, estable la audiencia en Azure Resource Manager y realiza una llamada de clave de *lista* al servicio Azure Storage. Si hay un error en la llamada de *lista*, se produce un error en la creación del objeto de Key Vault con un código de estado HTTP de *Prohibido*. Las claves que se muestran de esta manera se almacenan en caché con el almacenamiento de la entidad de Key Vault. 

Key Vault debe comprobar que la identidad tiene permisos para *volver a generar* antes de encargarse de volver a generar las claves. Para comprobar que la identidad (a través del token OBO) y la identidad propia de Key Vault tienen estos permisos:

- Key Vault enumera los permisos RBAC en el recurso de la cuenta de almacenamiento.
- Key Vault valida la respuesta a través de la coincidencia de expresión regular de acciones y no acciones. 

En [Key Vault - Managed Storage Account Keys Samples ](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167) (Key Vault: ejemplos de administración de claves de cuentas de Azure Storage) encontrará algunos ejemplos auxiliares.

Si la identidad no dispone de permisos para *volver a generar* o si la identidad propia de Key Vault no tiene permiso para *mostrar* o *volver a generar*, se produce un error en la solicitud de incorporación, que devuelve el código de error y el mensaje correspondientes. 

El token OBO solo funcionará cuando se usen aplicaciones cliente nativas propias de PowerShell o la CLI.

## <a name="other-applications"></a>Otras aplicaciones

- Los tokens de SAS, construidos mediante claves de cuenta de almacenamiento de Key Vault, proporcionan acceso todavía más controlado a una cuenta de Azure Storage. Consulte [Uso de firmas de acceso compartido](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) para más información.

## <a name="see-also"></a>Otras referencias

- [Información acerca de claves, secretos y certificados](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog del equipo de Key Vault](https://blogs.technet.microsoft.com/kv/)
