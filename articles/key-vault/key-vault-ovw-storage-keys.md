---
ms.assetid: 
title: Claves de cuenta de almacenamiento de Azure Key Vault
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 06/8/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: cc00433604adefec86ac43ded9bc5f09038b6a1d
ms.contentlocale: es-es
ms.lasthandoff: 07/04/2017

---
# <a name="azure-key-vault-storage-account-keys"></a>Claves de cuenta de almacenamiento de Azure Key Vault

Antes de que existieran las claves de cuenta de almacenamiento de Azure Key Vault, los desarrolladores tenían que administrar sus propias claves de cuenta de Azure Storage (ASA) y rotarlas manualmente o mediante la automatización externa. Ahora, las claves de cuenta de almacenamiento de Azure Key Vault se implementan como [secretos de Key Vault](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) para autenticar con una cuenta de Azure Storage. 

La característica de clave de ASA de Key Vault agrega valor al administrar automáticamente la rotación de secreto. También elimina la necesidad de tener contacto directo con una clave de cuenta de Azure Storage, ya que ofrece como método las firmas de acceso compartido (SAS). 

Para obtener más información general sobre las cuentas de Azure Storage, vea [Acerca de las cuentas de almacenamiento de Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

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
    - El valor de SAS, creado mediante la definición de SAS, se devuelve como un secreto a través de la ruta de acceso de URI de REST.

### <a name="naming-guidance"></a>Instrucciones de nomenclatura

Los nombres de cuentas de almacenamiento deben tener entre 3 y 24 caracteres, y solo pueden contener números y letras minúsculas.  
 
Los nombres de las definiciones de SAS deben tener una longitud de entre 1 y 102 caracteres y contener solo los caracteres 0-9, a-z, A-z.

## <a name="developer-experience"></a>Experiencia para el desarrollador 

### <a name="before-azure-key-vault-storage-keys"></a>Antes de la existencia de las claves de cuenta de almacenamiento de Azure Key Vault 

Anteriormente, para obtener acceso a Azure Storage, los desarrolladores debían realizar los procedimientos siguientes con una clave de cuenta de almacenamiento. 
 
 ```
//create storage account using connection string containing account name 
// and the storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>A partir de la existencia de las claves de cuenta de almacenamiento de Azure Key Vault 

```
//Use PowerShell command to get Secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  
-AccountName msak01 -Name blobsas1 -Protocol HttpsOrHttp -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get SAS token from Key Vault //....

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If SAS token is about to expire then Get sasToken again from Key Vault 
//.... 
 
// and update the accountSasCredential.UpdateSASToken(sasToken); 
 ```
 
 ### <a name="developer-best-practices"></a>Procedimientos recomendados para desarrolladores 

- Permita que solo Key Vault administre las claves de ASA. No intente administrarlas usted mismo, ya que la administración manual interfiere con los procesos de Key Vault. 
- No permita que más de un objeto de almacén de claves administre las claves de ASA. 
- Si tiene que volver a generar manualmente las claves de ASA, le recomendamos que lo haga a través de Key Vault. 

## <a name="getting-started"></a>Introducción

### <a name="setup-for-role-based-access-control-permissions"></a>Configuración de los permisos de control de acceso basado en rol

Key Vault necesita permisos para mostrar y volver a generar las claves de una cuenta de almacenamiento. Configúrelo mediante los pasos siguientes:

1. Obtenga el valor de ObjectId de Key Vault con este comando: `Get-AzureRmADServicePrincipal -SearchString "AzureKeyVault"`.  
 
2. Asigne el rol de "Operador de claves de almacenamiento" a la identidad de Azure Key Vault: `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`. 

>[!NOTE]
> Para una cuenta clásica, establezca el parámetro del rol en *"Rol de servicio de operador de claves de cuentas almacenamiento clásicas"*. 

### <a name="storage-account-onboarding"></a>Incorporación de cuentas de almacenamiento 

Ejemplo de incorporación: el propietario de un objeto de Key Vault agrega un objeto de cuenta de almacenamiento en AzKV para incorporar una cuenta de almacenamiento.

Durante la incorporación, Key Vault debe comprobar que la identidad de la cuenta que se está incorporando tiene acceso para *enumerar* y *volver a generar* las claves de almacenamiento. Key Vault obtiene un token OBO de EvoSTS con público como Azure Resource Manager y realiza una llamada para enumerar claves a RP de almacenamiento. Si se produce un error en la llamada de enumeración, se produce un error en la creación del objeto de Key Vault con el código de estado HTTP *Prohibido*. Las claves que se muestran de esta manera se almacenan en caché con el almacenamiento de la entidad de Key Vault. 

Key Vault debe comprobar que la identidad tiene permisos para *volver a generar* antes de encargarse de volver a generar las claves. Para comprobar que la identidad (a través del token OBO) y la identidad propia de Key Vault tienen estos permisos:

- Key Vault enumera los permisos RBAC en el recurso de la cuenta de almacenamiento.
- Key Vault valida la respuesta a través de la coincidencia de expresión regular de acciones y no acciones. 

Algunos ejemplos auxiliares: 

- Ejemplo de [VipSwapper](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceMan agerUtil.cs) 
- Ejemplo de [hasPermission](https://msazure.visualstudio.com/One/_search?type=Code&lp=searchproject&text=hasPermissions&result=DefaultCollection%2FOne%2FAzureUXPortalFx%2FGBdev%2F%2Fsrc%2FSDK%2FFramework.Client%2FTypeScript%2FFxHubs%2FPermissions.ts &filters=ProjectFilters%7BOne%7DRepositoryFilters%7BAzureUX-PortalFx%7D&_a=search) 

Si la identidad (a través del token OBO) no dispone de permiso para *volver a generar* o si la identidad propia de Key Vault no tiene permiso para *enumerar* o *volver a generar*, se produce un error en la solicitud de incorporación, que devuelve el código de error y el mensaje correspondientes. 

El token OBO solo funcionará cuando se usen aplicaciones cliente nativas propias de PowerShell o la CLI.

## <a name="other-applications"></a>Otras aplicaciones

- Los tokens de SAS, construidos mediante claves de cuenta de almacenamiento de Key Vault, proporcionan acceso todavía más controlado a una cuenta de Azure Storage. Para obtener más información, vea [Uso de firmas de acceso compartido](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## <a name="see-also"></a>Otras referencias

- [Información acerca de claves, secretos y certificados](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog del equipo de Key Vault](https://blogs.technet.microsoft.com/kv/)

