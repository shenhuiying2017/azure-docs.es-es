---
title: Cifrado del servicio Storage de Azure mediante claves administradas por el cliente en Azure Key Vault | Microsoft Docs
description: Use la característica Cifrado del servicio Storage de Azure para cifrar Azure Blob Storage en el servicio cuando almacene los datos y descifrarlo cuando recupere dichos datos mediante claves administradas por el cliente.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/07/2018
ms.author: lakasa
ms.openlocfilehash: 04688f943ac9eba27ca193aa2054c69b6a94547d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Cifrado del servicio Storage mediante claves administradas por el cliente en Azure Key Vault

Microsoft Azure está comprometido a ayudarle a asegurar y proteger sus datos con el fin de satisfacer los compromisos de cumplimiento y seguridad de su organización. Una manera en que Azure Storage protege los datos es mediante Cifrado del servicio Storage (SSE), que cifra los datos al escribirlos en el almacenamiento y los descifra al recuperarlos. El cifrado y descifrado son automáticos, transparentes y usan [cifrado AES](https://wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uno de los cifrados de bloques más seguros disponibles.

Puede usar claves de cifrado administradas por Microsoft con SSE o puede utilizar sus propias claves de cifrado. En este artículo se describe cómo usar sus propias claves de cifrado. Para más información sobre el uso de claves administradas por Microsoft, o sobre SSE en general, consulte [Storage Service Encryption para datos en reposo](storage-service-encryption.md).

SSE para Blob y File Storage está integrado con Azure Key Vault, de manera que pueda usar un almacén de claves para administrar las claves de cifrado. Puede crear sus propias claves de cifrado y almacenarlas en un almacén de claves, o bien puede usar las API de Azure Key Vault para generar las claves de cifrado. Con Azure Key Vault, puede administrar y controlar las claves y auditar su uso.

¿Por qué crear sus propias claves? Las claves personalizadas proporcionan mayor flexibilidad, de manera que puede crear, girar, deshabilitar y definir controles de acceso. También le permiten auditar las claves de cifrado que se usan para proteger los datos.

## <a name="get-started-with-customer-managed-keys"></a>Introducción a las claves administradas por el cliente

Para usar las claves administradas por el cliente con SSE, puede crear un almacén de claves y una clave nuevos, o bien puede usar un almacén de claves y una clave existentes. La cuenta de almacenamiento y el almacén de claves deben estar en la misma región, pero pueden estar en distintas suscripciones. 

### <a name="step-1-create-a-storage-account"></a>Paso 1: Creación de una cuenta de almacenamiento

En primer lugar, cree una cuenta de almacenamiento si todavía no tiene una. Para más información, consulte [Crear una cuenta de almacenamiento](storage-quickstart-create-account.md).

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>Paso 2: Habilitación de SSE para Blob y File Storage

Para habilitar SSE con claves administradas por el cliente, también se deben habilitar dos características de protección de claves, Eliminación temporal y No purgar. Estos valores garantizan que las claves no se puedan eliminar de manera accidental ni intencional. El período de retención máximo de las claves se establece en 90 días, lo que protege a los usuarios frente a actores malintencionados o ataques de ransomware.

Si desea habilitar las claves administradas por el cliente para SSE mediante programación, puede usar la [API de REST de proveedor de recursos de Azure Storage](https://docs.microsoft.com/rest/api/storagerp), la [biblioteca de cliente de proveedor de recursos de almacenamiento para .NET](https://docs.microsoft.com/dotnet/api), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) o la [CLI de Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli).

Para usar las claves administradas por el cliente con SSE, debe asignar una identidad de cuenta de almacenamiento a la cuenta de almacenamiento. Para establecer la identidad, ejecuta el comando de PowerShell siguiente:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName \$resourceGroup -Name \$accountName -AssignIdentity
```

Para habilitar las características Eliminación temporal y No purgar, ejecute los comandos de PowerShell siguientes:

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enableSoftDelete -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties

($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enablePurgeProtection -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties
```

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>Paso 3: Habilitación del cifrado con claves administradas por el cliente

De manera predeterminada, SSE usa claves administradas por Microsoft. Puede habilitar SSE con claves administradas por el cliente para la cuenta de almacenamiento mediante [Azure Portal](https://portal.azure.com/). En la hoja **Configuración** de la cuenta de almacenamiento, haga clic en **Cifrado**. Seleccione la opción **Use su propia clave**, tal como se muestra en la imagen siguiente.

![Captura de pantalla del Portal que muestra la opción de la característica Cifrado](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>Paso 4: Selección de la clave

Puede especificar la clave como un identificador URI, o bien selecciónela en un almacén de claves.

#### <a name="specify-a-key-as-a-uri"></a>Especificación de una clave como URI

Para especificar la clave a partir de un URI, siga estos pasos:

1. Elija la opción **Enter key URI** (Escribir el URI de la clave).  
2. En el campo **Key URI** (URI de la clave), especifique el URI.

    ![Captura de pantalla del portal que muestra el cifrado con la opción de especificación del UIR de la clave](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)


#### <a name="specify-a-key-from-a-key-vault"></a>Especificación de una clave a partir de un almacén de claves 

Para especificar la clave a partir de un almacén de claves, siga estos pasos:

1. Elija la opción **Select from Key Vault** (Seleccionar desde almacén de claves).  
2. Elija el almacén de claves que contiene la clave que desea usar.
3. Elija la clave en el almacén de claves.

    ![Captura de pantalla del portal que muestra la opción de cifrado con su propia clave](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Si la cuenta de almacenamiento no tiene acceso al almacén de claves, puede ejecutar el comando de Azure PowerShell que aparece en la imagen siguiente para conceder acceso.

![Captura de pantalla de portal que muestra el acceso denegado para el almacén de claves](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

También puede conceder acceso a través de Azure Portal yendo a Azure Key Vault en Azure Portal y concediendo acceso a la cuenta de almacenamiento.


Puede asociar la clave anterior con una cuenta de almacenamiento existente mediante los siguientes comandos de PowerShell:
```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount"
$keyVault = Get-AzureRmKeyVault -VaultName "mykeyvault"
$key = Get-AzureKeyVaultKey -VaultName $keyVault.VaultName -Name "keytoencrypt"
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVault.VaultName -ObjectId $storageAccount.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
Set-AzureRmStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName -AccountName $storageAccount.StorageAccountName -EnableEncryptionService "Blob" -KeyvaultEncryption -KeyName $key.Name -KeyVersion $key.Version -KeyVaultUri $keyVault.VaultUri
```


### <a name="step-5-copy-data-to-storage-account"></a>Paso 5: Copia de los datos en una cuenta de almacenamiento

Para transferir datos a la nueva cuenta de almacenamiento de modo que estén cifrados. Para obtener más información, consulte [Preguntas frecuentes sobre el Cifrado del servicio Storage](storage-service-encryption.md#faq-for-storage-service-encryption).

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>Paso 6: Consulta del estado de los datos cifrados

Consulte el estado de los datos cifrados.

## <a name="faq-for-sse-with-customer-managed-keys"></a>Preguntas más frecuentes sobre SSE con claves administradas por el cliente

**P: Uso Premium Storage. ¿Puedo usar claves administradas por el cliente con SSE?**

R: Sí, SSE con claves administradas por Microsoft y claves administradas el cliente es compatible tanto con almacenamiento estándar como con almacenamiento premium.

**P: ¿Puedo crear cuentas de almacenamiento con SSE con claves administradas por el cliente habilitado a través de Azure PowerShell y la CLI de Azure?**

R: Sí.

**P: ¿Cuánto aumenta el costo de Azure Storage si uso las claves administradas por el cliente con SSE?**

R: Hay un costo asociado para usar Azure Key Vault. Para más información, visite [Precios de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). No hay ningún costo adicional por SSE, que está habilitado para todas las cuentas de almacenamiento.

**P: ¿Puedo revocar el acceso a las claves de cifrado?**

R: Sí, puede revocar el acceso en cualquier momento. Hay varias maneras para revocar el acceso a las claves. Para más información, consulte [PowerShell de Azure Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) y la [CLI de Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). La revocación del acceso bloqueará eficazmente el acceso a todos los blobs de la cuenta de almacenamiento, ya que Azure Storage no podrá acceder a la clave de cifrado de la cuenta.

**P: ¿puedo crear una cuenta de almacenamiento y un clave en una región diferente?**

R: No, la cuenta de almacenamiento, Azure Key Vault y la clave deben estar en la misma región.

**P: ¿Puedo habilitar claves administradas por el cliente para SSE durante la creación de la cuenta de almacenamiento?**

R.: No. Cuando crea la cuenta de almacenamiento, solo las claves administradas por Microsoft están disponibles para SSE. Para usar claves administradas por el cliente, debe actualizar las propiedades de la cuenta de almacenamiento. Puede usar REST o una de las bibliotecas de cliente de almacenamiento para actualizar mediante programación la cuenta de almacenamiento, o actualice las propiedades de la cuenta de almacenamiento mediante Azure Portal después de crear la cuenta.

**P: ¿Puedo deshabilitar el cifrado mientras uso claves administradas por el cliente con SSE?**

R: No, no puede deshabilitar el cifrado. El cifrado está habilitado de manera predeterminada para todos los servicios: Blob, File, Table y Queue Storage. De manera opcional, puede pasar de usar las claves administradas por Microsoft a usar las claves administradas por el cliente y viceversa.

**P: ¿SSE está habilitado de manera predeterminada cuando creo una cuenta de almacenamiento?**

R: SSE está habilitado de manera predeterminada para todas las cuentas de almacenamiento y todos los servicios: Blob, File, Table y Queue Storage.

**P: No puedo habilitar SSE mediante las claves administradas por el cliente en mi cuenta de almacenamiento.**

R: ¿Es una cuenta de almacenamiento de Azure Resource Manager? Las cuentas de almacenamiento clásicas no son compatibles con las claves administradas por el cliente. SSE con claves administradas por el cliente solo se pueden habilitar en las cuentas de almacenamiento de Resource Manager.

**P: ¿Qué son las características Eliminación temporal y No purgar? ¿Tengo que habilitarlas para usar SSE con claves administradas por el cliente?**

R: Las características Eliminación temporal y No purgar se deben habilitar para usar SSE con claves administradas por el cliente. Estos valores garantizan que la clave no se elimine de manera accidental ni intencional. El período de retención máximo de las claves se establece en 90 días, lo que protege a los usuarios frente a actores malintencionados y ataques de ransomware. Esta configuración no se puede deshabilitar.

**P: ¿Solo se permite en determinadas regiones a SSE con claves administradas por el cliente?**

R: SSE con claves administradas por el cliente está disponible en todas las regiones para Blob y File Storage.

**P: ¿Cómo me puedo comunicar con alguna persona si tengo problemas o si deseo enviar comentarios?**

R: Envíe un mensaje de correo electrónico a [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) si tiene algún problema relacionado con el cifrado del servicio Storage.

## <a name="next-steps"></a>Pasos siguientes

-   Para más información sobre el conjunto completo de funcionalidades de seguridad que ayudan a los desarrolladores a crear aplicaciones seguras, consulte la [Guía de seguridad de Azure Storage](storage-security-guide.md).

-   Para información general sobre Azure Key Vault, consulte [¿Qué es Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)

-   Para una introducción sobre Azure Key Vault, consulte [Introducción al Almacén de claves de Azure](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).
