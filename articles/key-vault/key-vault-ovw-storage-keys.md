---
ms.assetid: ''
title: Claves de cuenta de almacenamiento de Azure Key Vault
description: Las claves de cuenta de almacenamiento proporcionan una integración sin problemas entre Azure Key Vault y el acceso basado en claves a la cuenta de Azure Storage.
ms.topic: article
services: key-vault
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: 4f42a47a6d934bf0538efccbcf7f057fd28e2c03
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179595"
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
1. Almacene la cadena de conexión o el token de SAS en la configuración de la aplicación Azure AppService o en otro almacenamiento.
1. Al iniciar la aplicación, capture la cadena de conexión o el token de SAS.
1. Cree [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) para interactuar con el almacenamiento.

```cs
// The Connection string is being fetched from App Service application settings
var connectionStringOrSasToken = CloudConfigurationManager.GetSetting("StorageConnectionString");
var storageAccount = CloudStorageAccount.Parse(connectionStringOrSasToken);
var blobClient = storageAccount.CreateCloudBlobClient();
 ```

### <a name="after-azure-key-vault-storage-keys"></a>A partir de la existencia de las claves de cuenta de almacenamiento de Azure Key Vault

Los desarrolladores crean una instancia de [KeyVaultClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient) y la usan para obtener el token de SAS para su almacenamiento. Después, crean [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) con ese token.

```cs
// Create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();

// Use the blobClientWithSas
...

// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Instrucciones para desarrolladores

- Permita que solo Key Vault administre las claves de ASA. No intente administrarlas usted mismo, ya que interferiría con los procesos de Key Vault.
- No permita que más de un objeto de Key Vault administre las claves de ASA.
- Si tiene que volver a generar manualmente las claves de ASA, se recomienda hacerlo a través de Key Vault.

## <a name="getting-started"></a>Introducción

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Configuración de permisos de control de acceso basado en roles (RBAC)

La identidad de aplicación de Azure Key Vault necesita permisos para *enumerar* y *regenerar* las claves de una cuenta de almacenamiento. Configure estos permisos mediante los siguientes pasos:

```powershell
# Get the resource ID of the Azure Storage Account you want to manage.
# Below, we are fetching a storage account using Azure Resource Manager
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure Key Vault Identity
$servicePrincipal = Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093

# Assign Storage Key Operator role to Azure Key Vault Identity
New-AzureRmRoleAssignment -ObjectId $servicePrincipal.Id -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storage.Id
```

    >[!NOTE]
    > For a classic account type, set the role parameter to *"Classic Storage Account Key Operator Service Role."*

## <a name="working-example"></a>Ejemplo práctico

En el siguiente ejemplo se muestra cómo crear una cuenta de almacenamiento de Azure administrada con Key Vault y las definiciones de Firma de acceso compartido (SAS) asociadas.

### <a name="prerequisite"></a>Requisito previo

Asegúrese de haber leído [Configuración de permisos de control de acceso basado en roles (RBAC)](#setup-for-role-based-access-control-rbac-permissions).

### <a name="setup"></a>Configuración

```powershell
# This is the name of our Key Vault
$keyVaultName = "mykeyVault"

# Fetching all the storage account object, of the ASA we want to manage with KeyVault
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure KeyVault Identity service principal
$servicePrincipalId = $(Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

A continuación, establezca los permisos de **su cuenta** para asegurarse de que puede administrar todos los permisos de almacenamiento en la instancia de Key Vault. En el ejemplo siguiente, la cuenta de Azure es _developer@contoso.com_.

```powershell
# Searching our Azure Active Directory for our account's ObjectId
$userPrincipalId = $(Get-AzureRmADUser -SearchString "developer@contoso.com").Id

# We use the ObjectId we found to setting permissions on the vault
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $userPrincipalId -PermissionsToStorage all
```

### <a name="create-a-key-vault-managed-storage-account"></a>Creación de una cuenta de almacenamiento administrado en Key Vault

Ahora, cree una cuenta de almacenamiento administrado en Azure Key Vault y use una clave de acceso de la cuenta de almacenamiento para crear los tokens de SAS.
- `-ActiveKeyName` usa "key2" para generar los tokens de SAS.
- `-AccountName` se usa para identificar la cuenta de almacenamiento administrado. A continuación, se usa el nombre de la cuenta de almacenamiento con fines de simplificación, pero puede tratarse de cualquier nombre.
- `-DisableAutoRegenerateKey` especifica que no se deben regenerar las claves de la cuenta de almacenamiento.

```powershell
# Adds your storage account to be managed by Key Vault and will use the access key, key2
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storage.StorageAccountName -AccountResourceId $storage.Id -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="key-regeneration"></a>Regeneración de claves

Si desea que Key Vault regenere las claves de acceso al almacenamiento periódicamente, puede establecer un período de regeneración. A continuación, se establece un período de regeneración de tres días. Después de tres días, Key Vault generará "key1" y cambiará la clave activa de "key2" a "key1".

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
$accountName = $storage.StorageAccountName

Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $accountName -AccountResourceId $storage.Id -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Establecer las definiciones de SAS

La cuenta SAS proporciona acceso a Blob service con permisos diferentes.
Establezca las definiciones de SAS en Key Vault para la cuenta de almacenamiento administrada.
- `-AccountName` es el nombre de la cuenta de almacenamiento administrado en Key Vault.
- `-Name` es el identificador del token de SAS en el almacenamiento.
- `-ValidityPeriod` establece la fecha de expiración del token de SAS generado.

```powershell
$validityPeriod = [System.Timespan]::FromDays(1)
$readSasName = "readBlobSas"
$writeSasName = "writeBlobSas"

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName $keyVaultName -AccountName $accountName -Name $readSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName $keyVaultName -AccountName $accountName -Name $writeSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List,Write
```

### <a name="get-sas-tokens"></a>Obtención de tokens de SAS

Obtenga los tokens de SAS correspondientes y realice llamadas al almacenamiento. `-SecretName` se construye con la entrada de los parámetros `AccountName` y `Name` al ejecutar [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition).

```powershell
$readSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$readSasName").SecretValueText
$writeSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$writeSasName").SecretValueText
```

### <a name="create-storage"></a>Crear el almacenamiento

Observe que, al intentar acceder con *$readSasToken*, se produce un error, aunque sí podemos acceder con *$writeSasToken*.

```powershell
$context1 = New-AzureStorageContext -SasToken $readSasToken -StorageAccountName $storage.StorageAccountName
$context2 = New-AzureStorageContext -SasToken $writeSasToken -StorageAccountName $storage.StorageAccountName

Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt" -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt" -Context $context2
```

Se puede obtener acceso al contenido del blob de almacenamiento con el token de SAS que tiene acceso de escritura.

### <a name="relevant-powershell-cmdlets"></a>Cmdlets de PowerShell relevantes

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

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
