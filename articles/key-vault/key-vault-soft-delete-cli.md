---
ms.assetid: 
title: "Azure Key Vault: Uso de la eliminación temporal con la CLI"
description: "Ejemplos de casos de uso de eliminación temporal con fragmentos de código de la CLI"
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: alleonar
ms.openlocfilehash: a9b80cae69c4e5852341385b98fcccc86d7959e9
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Uso de la eliminación temporal de Key Vault con la CLI

La característica de eliminación temporal de Azure Key Vault permite recuperar almacenes y objetos de almacenes eliminados. En concreto, la eliminación temporal trata los siguientes escenarios:

- Compatibilidad con la eliminación recuperable de una instancia de Key Vault
- Compatibilidad con la eliminación recuperable de objetos de Key Vault: claves, secretos y certificados

## <a name="prerequisites"></a>requisitos previos

- CLI de Azure 2.0: si no tiene esta configuración para su entorno, consulte [Administración de Key Vault mediante CLI 2.0](key-vault-manage-with-cli2.md).

Para obtener información de referencia específica sobre Key Vault para la CLI, consulte la [referencia de Key Vault para la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/keyvault).

## <a name="required-permissions"></a>Permisos necesarios

Las operaciones de Key Vault se administran por separado mediante los permisos de control de acceso basado en roles (RBAC) como se indica a continuación:

| Operación | DESCRIPCIÓN | Permiso del usuario |
|:--|:--|:--|
|Enumerar|Enumera los almacenes de claves eliminados.|Microsoft.KeyVault/deletedVaults/read|
|Recuperar|Restaura un almacén de claves eliminado.|Microsoft.KeyVault/vaults/write|
|Purgar|Elimina permanentemente un almacén de claves eliminado y todo su contenido.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Para más información sobre los permisos y el control de acceso, consulte [Protección de un almacén de claves](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Habilitar la eliminación temporal

Para poder recuperar un almacén de claves eliminado o los objetos almacenados en un almacén de claves, primero debe habilitar la eliminación temporal para ese almacén de claves.

### <a name="existing-key-vault"></a>Almacén de claves existente

Para un almacén de claves existente denominado ContosoVault, habilite la eliminación temporal como se indica a continuación. 

>[!NOTE]
>Actualmente debe usar la manipulación de recursos de Azure Resource Manager para escribir directamente la propiedad *enableSoftDelete* en el recurso de Key Vault.

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Almacén de claves nuevo

La habilitación de la eliminación temporal para un nuevo almacén de claves se realiza en el momento de la creación agregando el indicador de habilitación de eliminación temporal al comando create.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Comprobación de la habilitación de la eliminación temporal

Para comprobar que un almacén de claves tiene habilitada la eliminación temporal, ejecute el comando *show* y busque el atributo "Soft Delete Enabled?" y su valor, true o false.

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Eliminación de un almacén de claves protegido por eliminación temporal

El comando para eliminar (o quitar) un almacén de claves sigue siendo el mismo, pero su comportamiento cambia dependiendo de si ha habilitado la eliminación temporal o no.

```azurecli
az keyvault delete --name ContosoVault
```

> [!IMPORTANT]
>Si ejecuta el comando anterior para un almacén de claves que no tiene habilitada la eliminación temporal, se eliminará permanentemente este almacén de claves y todo su contenido sin ninguna opción de recuperación.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Uso de la eliminación temporal para proteger los almacenes de claves

Con la eliminación temporal habilitada:

- Cuando se elimina un almacén de claves, se quita de su grupo de recursos y se coloca en un espacio de nombres reservado que solo está asociado a la ubicación donde se creó. 
- Los objetos de un almacén de claves eliminado como, por ejemplo, las claves, los secretos y los certificados, son inaccesibles y permanecen así mientras que el almacén de claves que los contiene está en el estado eliminado. 
- El nombre DNS de un almacén de claves en estado eliminado se reserva por lo que no se podrá crear ningún nuevo almacén de claves con ese mismo nombre.  

Puede ver los almacenes de claves en estado eliminado asociados a su suscripción con el comando siguiente:

```azurecli
az keyvault list-deleted
```

El *identificador de recurso* en la salida hace referencia al identificador de recurso original de este almacén. Puesto que este almacén de claves está ahora en estado eliminado, no existe ningún recurso con ese identificador de recurso. El campo *Identificador* se puede utilizar para identificar el recurso durante la recuperación o purga. El campo *Scheduled Purge Date* (Fecha de purga programada) indica si el almacén se eliminará permanentemente (se purgará) si no se realiza ninguna acción en ese almacén eliminado. El período de retención predeterminado que se utiliza para calcular la *fecha de purga programada* es de 90 días.

## <a name="recovering-a-key-vault"></a>Recuperación de un almacén de claves

Para recuperar un almacén de claves, debe especificar el nombre del almacén de claves, el grupo de recursos y la ubicación. Anote la ubicación y el grupo de recursos del almacén de claves eliminado ya que los necesitará para un proceso de recuperación del almacén de claves.

```azurecli
az keyvault recover --location westus --name ContosoVault
```

Cuando se recupera un almacén de claves, el resultado es un nuevo recurso con el identificador de recurso original del almacén de claves. Si se ha quitado el grupo de recursos donde existía el almacén de claves, se deberá crear un nuevo grupo de recursos con el mismo nombre antes de poder recuperar el almacén de claves.

## <a name="key-vault-objects-and-soft-delete"></a>Objetos de Key Vault y eliminación temporal

Si tiene una clave, "ContosoFirstKey", en un almacén de claves denominado "ContosoVault" con eliminación temporal habilitada, este es el procedimiento para eliminarla.

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Con el almacén de claves habilitado para la eliminación temporal, una clave eliminada seguirá apareciendo como eliminada excepto si enumera o recupera explícitamente las claves eliminadas. La mayoría de las operaciones que se realicen en una clave en el estado eliminado producirán error, excepto las operaciones de enumeración, recuperación o purga. 

Por ejemplo, para solicitar la enumeración de las claves eliminadas de un almacén de claves, use el comando siguiente:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Estado de transición 

Cuando se elimina una clave de un almacén de claves con eliminación temporal habilitada, puede tardar unos segundos en completarse la transición. Durante este estado de transición, puede parecer que la clave no está ni en el estado activo ni en el estado eliminado. Este comando permite enumerar todas las claves eliminadas en el almacén de claves denominado "ContosoVault".

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="using-soft-delete-with-key-vault-objects"></a>Uso de la eliminación temporal con objetos de almacén de claves

Al igual que con los almacenes de claves, una clave, secreto o certificado eliminados permanecerán en este estado durante 90 días a menos que los recupere o los purgue. 

#### <a name="keys"></a>simétricas

Para recuperar una clave eliminada:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Para eliminar permanentemente una clave:

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

>[!NOTE]
>Si purga una clave se eliminará permanentemente, lo que significa que no se podrá recuperar de nuevo.

Las acciones de **recuperación** y **purga** tienen sus propios permisos asociados en una directiva de acceso del almacén de claves. Para que un usuario o una entidad de servicio puedan ejecutar una acción de **recuperación** o **purga** deben tener el permiso correspondiente para ese objeto (clave o secreto) en la directiva de acceso del almacén de claves. De forma predeterminada, el permiso de **purga** no se agrega a la directiva de acceso del almacén de claves cuando se usa el acceso directo "all" para conceder todos los permisos a un usuario. Debe conceder el permiso de **purga** de forma explícita. Por ejemplo, el siguiente comando concede el permiso user@contoso.com para realizar varias operaciones en las claves de *ContosoVault* incluidas las operaciones de **purga**.

#### <a name="set-a-key-vault-access-policy"></a>Establecimiento de una directiva de acceso del almacén de claves

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Si tiene un almacén de claves existente para el que se acaba de habilitar la eliminación temporal, puede que no tenga los permisos de **recuperación** y **purga**.

#### <a name="secrets"></a>Secretos

Al igual que con las claves, se trabaja con los secretos de un almacén de claves con sus propios comandos. A continuación, se muestran los comandos para eliminar, enumerar, recuperar y purgar secretos.

- Elimine un secreto llamado SQLPassword: 
```azurecli
az keyvault secret delete --vault-name ContosoVault -name SQLPassword
```

- Enumere todos los secretos eliminados de un almacén de claves: 
```azurecli
az keyvault secret list-deleted --vault-name ContosoVault
```

- Recupere un secreto en el estado eliminado: 
```azurecli
az keyvault secret recover --name SQLPassword --vault-name ContosoVault
```

- Purgue un secreto en el estado eliminado: 
```azurecli
az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
```

>[!NOTE]
>Si purga un secreto se eliminará permanentemente, lo que significa que no se podrá recuperar de nuevo.

## <a name="purging-and-key-vaults"></a>Purga y almacenes de claves

### <a name="key-vault-objects"></a>Objetos de almacén de claves

Si purga una clave, un secreto o un certificado se eliminarán permanentemente, lo que significa que no se podrán recuperar de nuevo. No obstante, el almacén de claves que contenía el objeto eliminado permanecerá intacto al igual que el resto de objetos del almacén. 

### <a name="key-vaults-as-containers"></a>Almacenes de claves como contenedores
Cuando se purga un almacén de claves, todo su contenido, incluidas las claves, los secretos y los certificados, se eliminan permanentemente. Para purgar un almacén de claves, use el comando `az keyvault purge`. Puede encontrar la ubicación de los almacenes de claves eliminados de la suscripción mediante el comando `az keyvault list-deleted`.

```azurecli
az keyvault purge --location westus --name ContosoVault
```

>[!NOTE]
>Si purga un almacén de claves se eliminará permanentemente, lo que significa que no se podrá recuperar de nuevo.

### <a name="purge-permissions-required"></a>Se requieren permisos de purga
- Para purgar un almacén de claves eliminado, de forma que el almacén y todo su contenido se quite de forma permanente, el usuario necesita el permiso de RBAC para realizar una operación *Microsoft.KeyVault/locations/deletedVaults/purge/action*. 
- Para que se muestre el almacén de claves eliminado, el usuario necesita el permiso de RBAC para realizar una operación *Microsoft.KeyVault/deletedVaults/read*. 
- Solo un administrador de suscripción tiene estos permisos. 

### <a name="scheduled-purge"></a>Purga programada

La enumeración de los objetos de almacén de claves eliminados muestra cuándo se han programado para que Key Vault efectúe la purga. El campo *Scheduled Purge Date* (Fecha de purga programada) indica cuándo se eliminará permanentemente un objeto de almacén de claves si no se realiza ninguna acción en ese almacén. De forma predeterminada, el período de retención para un objeto de almacén de claves eliminado es de 90 días.

>[!NOTE]
>Un objeto de almacén purgado, desencadenado por el valor de su campo de *fecha de purga programada*, se eliminará permanentemente. No se podrá volver a recuperar.

## <a name="other-resources"></a>Otros recursos:

- Para obtener una información general sobre la nueva característica de eliminación temporal, consulte [la información general sobre la eliminación temporal de Azure Key Vault](key-vault-ovw-soft-delete.md).
- Para obtener una descripción general del uso de Azure Key Vault, consulte [Introducción a Azure Key Vault](key-vault-get-started.md).

