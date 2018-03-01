---
title: "Uso de una identidad de servicio administrada (MSI) asignada por el usuario en una máquina virtual Linux para acceder a Azure Cosmos DB"
description: "Este tutorial le guía en el proceso para usar una identidad de servicio administrada (MSI) asignada por el usuario en una máquina virtual Linux para acceder a Azure Cosmos DB."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2018
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2c0c3597999e80af86f079385653d94ddfcab245
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-cosmos-db"></a>Uso de una identidad de servicio administrada (MSI) asignada por el usuario en una máquina virtual Linux para acceder a Azure Cosmos DB 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

En este tutorial se muestra cómo crear y utilizar una identidad de servicio administrada (MSI) asignada por el usuario en una máquina virtual Linux y, a continuación, usar esa identidad para acceder a Azure Cosmos DB. Aprenderá a:

> [!div class="checklist"]
> * Crear una identidad de servicio administrada (MSI) asignada por el usuario de la máquina virtual
> * Asignar la MSI asignada por el usuario a una máquina virtual Linux
> * Conceder el acceso MSI a una instancia de Azure Cosmos DB
> * Obtener un token de acceso mediante la identidad de servicio administrada asignada por el usuario y usarlo para acceder a Azure Cosmos DB

## <a name="prerequisites"></a>requisitos previos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Para ejecutar los ejemplos de script de la CLI de este tutorial, tiene dos opciones:

- Use [Azure Cloud Shell](~/articles/cloud-shell/overview.md) desde Azure Portal o mediante el botón **Pruébelo**, situado en la esquina superior derecha de cada bloque de código.
- [Instale la versión más reciente de la CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 o posterior), si prefiere usar una consola de la CLI local.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Crear una máquina virtual Linux en un nuevo grupo de recursos

En este tutorial, vamos a crear una nueva máquina virtual Linux. También puede habilitar MSI en una máquina virtual existente.

1. Haga clic en **+ Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Proceso** y, después, seleccione la máquina virtual **Ubuntu Server 16.04 LTS**.
3. Escriba la información de la máquina virtual. En **Tipo de autenticación**, seleccione **Clave pública SSH** o **Contraseña**. Las credenciales creadas le permiten iniciar sesión en la máquina virtual.

    ![Máquina virtual Linux con MSI](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Elija una **Suscripción** para la máquina virtual en la lista desplegable.
5. En **Grupo de recursos**, elija **Crear nuevo** y escriba el nombre del grupo de recursos de la máquina virtual. Cuando haya terminado, haga clic en **Aceptar**.
6. Seleccione el tamaño de la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro de tipo de disco admitido. En la hoja **Configuración**, conserve los valores predeterminados y haga clic en **Aceptar**.

## <a name="create-a-user-assigned-msi"></a>Creación de una MSI asignada por el usuario

1. Si usa la consola de la CLI (en lugar de una sesión de Azure Cloud Shell), primero inicie sesión en Azure. Use una cuenta asociada a la suscripción de Azure en la que desee crear la nueva MSI:

    ```azurecli
    az login
    ```

2. Cree una MSI asignada por el usuario mediante el cmdlet [az identity create](/cli/azure/identity#az_identity_create). El parámetro `-g` especifica el grupo de recursos donde se creó la MSI, mientras que el parámetro `-n` especifica su nombre. Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>` y `<MSI NAME>` por sus propios valores:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    La respuesta contiene detalles de la MSI asignada por el usuario creada, similares a los del ejemplo siguiente (tome nota de los valores de `clientId` e `id` de la MSI, ya que se usan en pasos posteriores):

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Asignación de la MSI asignada por el usuario a la máquina virtual Linux

A diferencia de una MSI asignada por el sistema, los clientes pueden usar una MSI asignada por el usuario en varios recursos de Azure. En este tutorial, la asignará a una sola máquina virtual. También puede asignarla a más de una máquina virtual.

Asigne la MSI asignada por el usuario a la máquina virtual Linux con el cmdlet [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). Asegúrese de reemplazar los valores de los parámetros `<RESOURCE GROUP>` y `<VM NAME>` por sus propios valores. Use la propiedad `id` devuelta en el paso anterior para el valor del parámetro `--identities`:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-cosmos-db-account"></a>Creación de una cuenta de Cosmos DB 

Si aún no tiene una, cree una cuenta de Cosmos DB. También puede omitir este paso y usar una cuenta de Cosmos DB existente, si lo prefiere. 

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

## <a name="grant-your-user-assigned-msi-access-to-the-cosmos-db-account-access-keys"></a>Concesión de acceso a la MSI asignada por el usuario a las claves de acceso de la cuenta de Cosmos DB

Cosmos DB no admite la autenticación de Azure AD de forma nativa.  No obstante, puede usar una MSI para recuperar una clave de acceso de Cosmos DB desde el administrador de recursos y usar la clave para acceder a Cosmos DB.  En este paso, se concede acceso a la MSI asignada por el usuario a las claves de la cuenta de Cosmos DB.

En primer lugar, conceda acceso a la identidad MSI a la cuenta de Cosmos DB en Azure Resource Manager mediante la CLI de Azure. Actualice los valores de `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` y `<COSMOS DB ACCOUNT NAME>` según corresponda en su entorno. Reemplace `<MSI PRINCIPALID>` por la propiedad `principalId` devuelta por el comando `az identity create` en [Creación de una identidad MSI asignada por el usuario](#create-a-user-assigned-msi).  Cosmos DB admite dos niveles de granularidad en las claves de acceso: acceso de lectura y escritura a la cuenta y acceso de solo lectura a la cuenta.  Asigne el rol `DocumentDB Account Contributor` si desea obtener claves de lectura y escritura para la cuenta o bien asigne el rol `Cosmos DB Account Reader Role` si desea obtener claves de solo lectura para la cuenta:

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

La respuesta incluye los detalles de la asignación de roles que se ha creado:

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msi-and-use-it-to-call-azure-resource-manager"></a>Obtención de un token de acceso mediante la MSI asignada por el usuario para llamar a Azure Resource Manager

En el resto del tutorial, vamos a trabajar desde la máquina virtual que se creó anteriormente.

Para completar estos pasos, necesitará un cliente SSH. Si usa Windows, puede usar el cliente SSH en el [Subsistema de Windows para Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Si necesita ayuda para configurar las claves del cliente de SSH, consulte [Uso de SSH con Windows en Azure](../../virtual-machines/linux/ssh-from-windows.md) o [Creación y uso de un par de claves SSH pública y privada para máquinas virtuales Linux en Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. En Azure Portal, vaya a **Máquinas virtuales**, vaya a la máquina virtual Linux y, a continuación, desde la página **Información general**, haga clic en **Conectar** en la parte superior. Copie la cadena para conectarse a la máquina virtual. 
2. Conéctese a la máquina virtual mediante un cliente SSH.  
3. A continuación, se le pedirá que escriba la **contraseña** que agregó al crear la **máquina virtual Linux**. Debe haber iniciado sesión correctamente.  
4. Utilice CURL para obtener un token de acceso para Azure Resource Manager.  

    La solicitud y la respuesta de CURL para el token de acceso están a continuación.  Reemplace <CLIENT ID> por el valor de clientId de la identidad MSI asignada por el usuario:
    
    ```bash
    curl 'http://localhost:50342/oauth2/token?resource=https://management.azure.com/&client_id=<CLIENT ID>' -H "Metadata:true"
    ```
    
    > [!NOTE]
    > En la solicitud anterior, el valor del parámetro "resource" tiene que coincidir exactamente con el que Azure AD espera. Al usar el id. de recurso de Azure Resource Manager, debe incluir la barra diagonal final en el URI.
    > En la siguiente respuesta, el elemento access_token se ha abreviado.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Obtención de las claves de acceso desde Azure Resource Manager para realizar llamadas a Cosmos DB  

Ahora, utilice CURL para llamar al administrador de recursos mediante el token de acceso que se recuperó en la sección anterior, para recuperar la clave de acceso de la cuenta de Cosmos DB. Una vez que tenemos la clave de acceso, podemos realizar consultas en Cosmos DB. Asegúrese de reemplazar los valores de los parámetros `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` y `<COSMOS DB ACCOUNT NAME>` con sus propios valores. Reemplace el valor de `<ACCESS TOKEN>` por el token de acceso que se recuperó anteriormente.  Si quiere recuperar claves de lectura y escritura, use el tipo de operación de claves `listKeys`.  Si quiere recuperar claves de solo lectura, use el tipo de operación de claves `readonlykeys`:

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> El texto de la dirección URL anterior distingue mayúsculas de minúsculas, por lo que tiene que fijarse en si usa mayúsculas o minúsculas para los grupos de recursos para reflejarlo adecuadamente según corresponda. Además, es importante saber que se trata de una solicitud POST, no una solicitud GET, y asegurarse de que pasa un valor para capturar un límite de longitud con -d que puede ser NULL.  

La respuesta de CURL proporciona la lista de claves.  Por ejemplo, si obtiene las claves de solo lectura:  

```bash 
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

## <a name="next-steps"></a>pasos siguientes

- Para más información sobre MSI, consulte [Identidad de servicio administrada (MSI) para recursos de Azure](msi-overview.md).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.