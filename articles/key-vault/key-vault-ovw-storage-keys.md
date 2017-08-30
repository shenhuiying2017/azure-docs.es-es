---
ms.assetid: 
title: Claves de cuenta de almacenamiento de Azure Key Vault
description: "Las claves de cuenta de almacenamiento proporcionan una integración sin problemas entre Azure Key Vault y el acceso basado en claves a la cuenta de Azure Storage."
ms.topic: article
services: key-vault
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 07/25/2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 3148088c88236c64e089fd25c98eb8ac7cdcbfea
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---
# <a name="azure-key-vault-storage-account-keys"></a>Claves de cuenta de almacenamiento de Azure Key Vault

Antes de que existieran las claves de cuenta de almacenamiento de Azure Key Vault, los desarrolladores tenían que administrar sus propias claves de cuenta de Azure Storage (ASA) y rotarlas manualmente o mediante la automatización externa. Ahora las claves de cuenta de almacenamiento de Key Vault se implementan como [secretos de Key Vault](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) para autenticar con una cuenta de Azure Storage. 

La característica de clave de ASA administra la rotación de secretos automáticamente y elimina la necesidad de tener contacto directo con una clave de ASA, ya que ofrece como método las firmas de acceso compartido (SAS). 

Para más información general sobre las cuentas de Azure Storage, vea [Acerca de las cuentas de Azure Storage](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Interfaces admitidas

La característica de claves de la cuenta de Azure Storage está disponible inicialmente a través de las interfaces de REST, .NET/C# y PowerShell. Para obtener más información, vea [Documentación de Key Vault](https://docs.microsoft.com/azure/key-vault/).


## <a name="storage-account-keys-behavior"></a>Comportamiento de las claves de cuenta de almacenamiento

### <a name="what-key-vault-manages"></a>¿Qué administra Key Vault?

Cuando usa las claves de la cuenta de almacenamiento, Key Vault realiza varias funciones de administración interna en su nombre.

1. Azure Key Vault administra las claves de una cuenta de Azure Storage (ASA). 
    - Internamente, Azure Key Vault puede enumerar (sincronizar) las claves con una cuenta de Azure Storage.  
    - Azure Key Vault vuelve a generar (rotar) las claves periódicamente. 
    - Los valores de clave nunca se devuelven como respuesta al autor de la llamada. 
    - Azure Key Vault administra las claves de las cuentas de almacenamiento y de las cuentas de almacenamiento clásicas. 
2. Azure Key Vault le permite a usted, como propietario del almacén/objeto, crear definiciones de SAS (SAS de cuenta o de servicio). 
    - El valor de SAS, creado mediante la definición de SAS, se devuelve como un secreto a través de la ruta de acceso de URI de REST. Para más información, vea [Azure Key Vault storage account operations (Operaciones de cuenta de almacenamiento de Azure Key Vault)](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations).

### <a name="naming-guidance"></a>Instrucciones de nomenclatura

Los nombres de cuentas de almacenamiento deben tener entre 3 y 24 caracteres, y solo pueden contener números y letras minúsculas.  
 
Los nombres de las definiciones de SAS deben tener una longitud de entre 1 y 102 caracteres y contener solo los caracteres 0-9, a-z, A-z.

## <a name="developer-experience"></a>Experiencia para el desarrollador 

### <a name="before-azure-key-vault-storage-keys"></a>Antes de la existencia de las claves de cuenta de almacenamiento de Azure Key Vault 

Anteriormente, para obtener acceso a Azure Storage, los desarrolladores debían realizar los procedimientos siguientes con una clave de cuenta de almacenamiento. 
 
 ```
//create storage account using connection string containing account name 
// and the storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>A partir de la existencia de las claves de cuenta de almacenamiento de Azure Key Vault 

```
//Please make sure to set storage permissions appropriately on your key vault
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourVault' -ObjectId yourObjectId -PermissionsToStorage all

//Use PowerShell command to get Secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  
-AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If SAS token is about to expire then Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);

  ```
 
 ### <a name="developer-best-practices"></a>Procedimientos recomendados para desarrolladores 

- Permita que solo Key Vault administre las claves de ASA. No intente administrarlas usted mismo, ya que interferiría con los procesos de Key Vault. 
- No permita que más de un objeto de Key Vault administre las claves de ASA. 
- Si tiene que volver a generar manualmente las claves de ASA, se recomienda hacerlo a través de Key Vault. 

## <a name="getting-started"></a>Introducción

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Configuración de permisos de control de acceso basado en roles (RBAC)

Key Vault necesita permisos para *mostrar* y *volver a generar* las claves de una cuenta de almacenamiento. Configure estos permisos mediante los siguientes pasos:

- Obtenga ObjectId de Key Vault: 

    `Get-AzureRmADServicePrincipal -SearchString "AzureKeyVault"`

- Asigne el rol Operador de claves de almacenamiento a la identidad de Azure Key Vault: 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > Para un tipo de cuenta clásica, establezca el parámetro del rol en *"Rol de servicio de operador de claves de cuentas de almacenamiento clásicas"*.

### <a name="storage-account-onboarding"></a>Incorporación de cuentas de almacenamiento 

Ejemplo: como propietario de un objeto de Key Vault, agregue un objeto de cuenta de almacenamiento a Azure Key Vault para incorporar una cuenta de almacenamiento.

Durante la incorporación, Key Vault debe comprobar que la identidad de la cuenta que se está incorporando tiene permisos para *mostrar* y *volver a generar* las claves de almacenamiento. Para comprobar estos permisos, Key Vault obtiene un token OBO (del inglés On Behalf Of [en nombre de]) desde el servicio de autenticación, estable la audiencia en Azure Resource Manager y realiza una llamada de clave de *lista* al servicio Azure Storage. Si hay un error en la llamada de *lista*, se produce un error en la creación del objeto de Key Vault con un código de estado HTTP de *Prohibido*. Las claves que se muestran de esta manera se almacenan en caché con el almacenamiento de la entidad de Key Vault. 

Key Vault debe comprobar que la identidad tiene permisos para *volver a generar* antes de encargarse de volver a generar las claves. Para comprobar que la identidad (a través del token OBO) y la identidad propia de Key Vault tienen estos permisos:

- Key Vault enumera los permisos RBAC en el recurso de la cuenta de almacenamiento.
- Key Vault valida la respuesta a través de la coincidencia de expresión regular de acciones y no acciones. 

En [Key Vault - Managed Storage Account Keys Samples ](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167) (Key Vault: ejemplos de administración de claves de cuentas de Azure Storage) encontrará algunos ejemplos auxiliares.

Si la identidad no dispone de permisos para *volver a generar* o si la identidad propia de Key Vault no tiene permiso para *mostrar* o *volver a generar*, se produce un error en la solicitud de incorporación, que devuelve el código de error y el mensaje correspondientes. 

El token OBO solo funcionará cuando se usen aplicaciones cliente nativas propias de PowerShell o la CLI.

## <a name="other-applications"></a>Otras aplicaciones

- Los tokens de SAS, construidos mediante claves de cuenta de almacenamiento de Key Vault, proporcionan acceso todavía más controlado a una cuenta de Azure Storage. Para obtener más información, vea [Uso de firmas de acceso compartido](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## <a name="see-also"></a>Otras referencias

- [Información acerca de claves, secretos y certificados](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog del equipo de Key Vault](https://blogs.technet.microsoft.com/kv/)

