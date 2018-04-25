---
title: Uso de una identidad de servicio administrada (MSI) de máquina virtual Windows para acceder a Azure Cosmos DB
description: Este tutorial le guía en el proceso para usar una identidad de servicio administrada (MSI) asignada por el sistema en una máquina virtual Windows para acceder a Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: arluca
ms.openlocfilehash: 0e62e11e0f86714b79cf2bc84bbb505b03df1ba6
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
---
# <a name="use-a-windows-vm-msi-to-access-azure-cosmos-db"></a>Uso de una identidad de servicio administrada (MSI) de máquina virtual Windows para acceder a Azure Cosmos DB

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial le muestra cómo crear y utilizar una identidad de servicio administrada (MSI) de máquina virtual Windows para acceder a Cosmos DB. Aprenderá a:

> [!div class="checklist"]
> * Crear una máquina virtual Windows con una identidad de servicio administrada habilitada 
> * Creación de una cuenta de Cosmos DB
> * Conceder acceso mediante identidad de servicio administrada de máquina virtual Windows a las claves de acceso de la cuenta de Cosmos DB
> * Obtener un token de acceso mediante la identidad de servicio administrada de máquina virtual Windows y usarlo para llamar a Azure Resource Manager
> * Obtención de las claves de acceso desde Azure Resource Manager para realizar llamadas a Cosmos DB

## <a name="prerequisites"></a>requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]


## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Creación de una máquina virtual Windows en un nuevo grupo de recursos

En este tutorial, se crea una nueva máquina virtual Windows.  También puede habilitar MSI en una máquina virtual existente.

1. Haga clic en el botón **Crear un recurso** de la esquina superior izquierda de Azure Portal.
2. Seleccione **Compute** y, después, seleccione **Windows Server 2016 Datacenter**. 
3. Escriba la información de la máquina virtual. El **nombre de usuario** y la **contraseña** creados aquí son las credenciales que se usan para iniciar sesión en la máquina virtual.
4. Elija la **suscripción** adecuada de la máquina virtual en la lista desplegable.
5. Para seleccionar un nuevo **grupo de recursos** en el que crear la máquina virtual, elija **Crear nuevo**. Cuando haya terminado, haga clic en **Aceptar**.
6. Seleccione el tamaño de la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro **Supported disk type** (Tipo de disco admitido). En la página de configuración, conserve los valores predeterminados y haga clic en **Aceptar**.

   ![Texto alternativo de imagen](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Habilitación de MSI en la máquina virtual 

Una identidad MSI de máquina virtual le permite obtener tokens de acceso de Azure AD sin tener que incluir las credenciales en el código. En un segundo plano, habilitar MSI en una máquina virtual mediante Azure Portal permite hacer dos cosas: registrar la máquina virtual con Azure AD para crear una identidad administrada y configurar la identidad en la máquina virtual.

1. Seleccione la **máquina virtual** en la que desee habilitar MSI.  
2. En la barra de navegación de la izquierda, haga clic en **Configuración**. 
3. Verá **Managed Service Identity**. Para registrar y habilitar MSI, seleccione **Sí**; si desea deshabilitarla, elija No. 
4. No olvide hacer clic en **Guardar** para guardar la configuración.  
   ![Texto alternativo de imagen](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-cosmos-db-account"></a>Creación de una cuenta de Cosmos DB 

Si aún no tiene una, cree una cuenta de Cosmos DB. También puede omitir este paso y usar una cuenta de Cosmos DB existente. 

1. Haga clic en el botón **+/Crear nuevo servicio** de la esquina superior izquierda de Azure Portal.
2. Haga clic en **Bases de datos** y, a continuación, en **Azure Cosmos DB** para mostrar el panel "Nueva cuenta".
3. Escriba un **identificador** para la cuenta de Cosmos DB, el cual se utilizará más adelante.  
4. **API** se debe establecer en "SQL". El enfoque descrito en este tutorial se puede utilizar con los otros tipos de API disponibles, pero los pasos de este tutorial son para la API de SQL.
5. Asegúrese de que **Suscripción** y **Grupo de recursos** coinciden con los que especificó cuando creó la máquina virtual en el paso anterior.  Seleccione una **Ubicación** en la que Cosmos DB esté disponible.
6. Haga clic en **Create**(Crear).

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Creación de una colección en la cuenta de Cosmos DB

A continuación, agregue una colección de datos en la cuenta de Cosmos DB que podrá consultar en pasos posteriores.

1. Vaya a la cuenta de Cosmos DB recién creada.
2. En la pestaña **Información general**, haga clic en el botón **+/Agregar colección** y aparecerá un panel "Agregar colección".
3. Proporcione para la colección un identificador de base de datos, el identificador de la colección, seleccione una capacidad de almacenamiento, escriba una clave de partición, escriba un valor de rendimiento y, luego, haga clic en **Aceptar**.  Para este tutorial, es suficiente con utilizar "Test" como identificador de la base de datos e identificador de la colección, seleccionar una capacidad de almacenamiento fijo y el rendimiento más bajo (400 RU/s).  

## <a name="grant-windows-vm-msi-access-to-the-cosmos-db-account-access-keys"></a>Conceder acceso mediante identidad de servicio administrada de máquina virtual Windows a las claves de acceso de la cuenta de Cosmos DB

Cosmos DB no admite la autenticación de Azure AD de forma nativa. No obstante, puede usar una MSI para recuperar una clave de acceso de Cosmos DB desde el administrador de recursos y usar la clave para acceder a Cosmos DB. En este paso, se concede acceso mediante MSI a las claves de la cuenta de Cosmos DB.

Para conceder el acceso mediante una identidad MSI a la cuenta de Cosmos DB en Azure Resource Manager con PowerShell, actualice los valores de `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` y `<COSMOS DB ACCOUNT NAME>` para su entorno. Reemplace `<MSI PRINCIPALID>` por la propiedad `principalId` que devuelve el comando `az resource show` en [Retrieve the principalID of the Linux VM's MSI](#retrieve-the-principalID-of-the-linux-VM's-MSI) (Recuperar principalID de la MSI de máquina virtual Linux).  Cosmos DB admite dos niveles de granularidad en las claves de acceso: acceso de lectura y escritura a la cuenta y acceso de solo lectura a la cuenta.  Asigne el rol `DocumentDB Account Contributor` si desea obtener claves de lectura y escritura para la cuenta o bien asigne el rol `Cosmos DB Account Reader Role` si desea obtener claves de solo lectura para la cuenta:

```azurepowershell
$spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
```

## <a name="get-an-access-token-using-the-windows-vms-msi-to-call-azure-resource-manager"></a>Obtener un token de acceso mediante la identidad de servicio administrada de máquina virtual Windows y usarlo para llamar a Azure Resource Manager

En el resto del tutorial, vamos a trabajar desde la máquina virtual que se creó anteriormente. 

En esta parte tendrá que usar los cmdlets de PowerShell de Azure Resource Manager.  Si no lo tiene instalado, [descargue la versión más reciente](https://docs.microsoft.com/powershell/azure/overview) antes de continuar.

También necesitará instalar la versión más reciente de la [CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) en su máquina virtual de Windows.

1. En Azure Portal, vaya a **Máquinas virtuales**, vaya a la máquina virtual Windows y, a continuación, desde la página **Información general**, haga clic en **Conectar** en la parte superior. 
2. Escriba su **nombre de usuario** y **contraseña** que agregó cuando creó la máquina virtual Windows. 
3. Ahora que ha creado una **conexión a Escritorio remoto** con la máquina virtual, abra PowerShell en la sesión remota.
4. Mediante Invoke-WebRequest de PowerShell, realice una solicitud al punto de conexión de MSI local para obtener un token de acceso para Azure Resource Manager.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```

    > [!NOTE]
    > El valor del parámetro "resource" debe coincidir exactamente con el que Azure AD espera. Al usar el id. de recurso de Azure Resource Manager, debe incluir la barra diagonal final en el URI.
    
    A continuación, extraiga el elemento "Content", que se almacena como una cadena con formato de notación de objetos JavaScript (JSON) en el objeto $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Luego, extraiga el token de acceso de la respuesta.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Obtención de las claves de acceso desde Azure Resource Manager para realizar llamadas a Cosmos DB

Ahora, utilice PowerShell para llamar a Resource Manager mediante el token de acceso que se recuperó en la sección anterior, para recuperar la clave de acceso de la cuenta de Cosmos DB. Una vez que tenemos la clave de acceso, podemos realizar consultas en Cosmos DB. Asegúrese de reemplazar los valores de los parámetros `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` y `<COSMOS DB ACCOUNT NAME>` con sus propios valores. Reemplace el valor de `<ACCESS TOKEN>` por el token de acceso que se recuperó anteriormente.  Si quiere recuperar claves de lectura y escritura, use el tipo de operación de claves `listKeys`.  Si quiere recuperar claves de solo lectura, use el tipo de operación de claves `readonlykeys`:

```powershell
Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
La respuesta proporciona la lista de claves.  Por ejemplo, si obtiene las claves de solo lectura:

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
Ahora que tiene la clave de acceso de la cuenta de Cosmos DB, puede pasársela al SDK de Cosmos DB y realizar llamadas para acceder a la cuenta.  Como ejemplo rápido, puede pasar la clave de acceso a la CLI de Azure.  Puede obtener el valor de <COSMOS DB CONNECTION URL> en la pestaña **Información general** de la hoja de la cuenta de Cosmos DB en Azure Portal.  Reemplace <ACCESS KEY> por el valor obtenido anteriormente:

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Este comando de la CLI devuelve detalles acerca de la colección:

```bash
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](overview.md) (Introducción a Managed Service Identity).
