---
title: "Administración de recursos con la CLI de Azure | Microsoft Docs"
description: "Utilice la interfaz de línea de comandos (CLI) de Azure para administrar recursos y grupos de Azure"
editor: 
manager: timlt
documentationcenter: 
author: tfitzmac
services: azure-resource-manager
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: c68f2a8b6e18dc2d51d8bbb5cd05bc037dc2fadb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Uso de la CLI de Azure para administrar los recursos y grupos de recursos de Azure

En este artículo obtendrá información sobre cómo administrar las soluciones con la CLI de Azure y Azure Resource Manager. Si no está familiarizado con Resource Manager, consulte [Información general de Resource Manager](resource-group-overview.md). Este artículo se centra en las tareas de administración. Podrá:

1. Crear un grupo de recursos
2. Adición de un recurso al grupo de recursos
3. Adición de una etiqueta al recurso
4. Consulta de recursos según nombres y valores de etiqueta
5. Aplicación y eliminación de un bloqueo en el recurso
6. Eliminación de un grupo de recursos

En este artículo no se muestra cómo implementar una plantilla de Resource Manager en la suscripción. Para consultar esa información, vea [Implementación de recursos con plantillas de Resource Manager y la CLI de Azure](resource-group-template-deploy-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para instalar y usar la CLI de Azure localmente, vea [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli).

## <a name="set-subscription"></a>Definir la suscripción

Si tiene más de una suscripción, puede cambiar a otra diferente. En primer lugar, vamos a ver todas las suscripciones de su cuenta.

```azurecli-interactive
az account list
```

Se devuelve una lista de las suscripciones habilitadas y deshabilitadas.

```json
[
  {
    "cloudName": "AzureCloud",
    "id": "<guid>",
    "isDefault": true,
    "name": "Example Subscription One",
    "registeredProviders": [],
    "state": "Enabled",
    "tenantId": "<guid>",
    "user": {
      "name": "example@contoso.org",
      "type": "user"
    }
  },
  ...
]
```

Fíjese en que una suscripción está marcada como valor predeterminado. Esta suscripción es el contexto actual para las operaciones. Para cambiar a otra suscripción, indique el nombre de esa suscripción con el comando **az account set**.

```azurecli-interactive
az account set -s "Example Subscription Two"
```

Para mostrar el contexto de la suscripción actual, use **az account show** sin ningún parámetro:

```azurecli-interactive
az account show
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Antes de implementar los recursos en la suscripción, debe crear un grupo de recursos que contendrá los recursos.

Para crear un grupo de recursos, use el comando **az group create**. El comando usa el parámetro **name** para especificar un nombre para el grupo de recursos y el parámetro **location** para definir su ubicación.

```azurecli-interactive
az group create --name TestRG1 --location "South Central US"
```

La salida tiene el siguiente formato:

```json
{
  "id": "/subscriptions/<subscription-id>/resourceGroups/TestRG1",
  "location": "southcentralus",
  "managedBy": null,
  "name": "TestRG1",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

Si más tarde tiene que recuperar el grupo de recursos, use el siguiente comando:

```azurecli-interactive
az group show --name TestRG1
```

Para obtener una lista de todos los grupos de recursos de la suscripción, use lo siguiente:

```azurecli-interactive
az group list
```

## <a name="add-resources-to-a-resource-group"></a>Adición de recursos a un grupo de recursos

Para agregar un recurso al grupo de recursos, puede usar el comando **az resource create** o un comando específico del tipo de recurso que esté creando (como **az storage account create**). Quizás le resulte más fácil usar un comando que sea específico de un tipo de recurso, ya que incluye parámetros de las propiedades que son necesarias en el nuevo recurso. Para usar **az resource create**, debe conocer todas las propiedades que se deben establecer sin que se soliciten.

Pero el hecho de agregar un recurso mediante un script puede provocar confusión en el futuro, porque el nuevo recurso no existe en una plantilla de Resource Manager. Las plantillas le permiten implementar la solución de forma confiable y repetida.

Con el siguiente comando se crea una cuenta de almacenamiento. En lugar de usar el nombre que aparece en el ejemplo, proporcione un nombre único para la cuenta de almacenamiento. El nombre debe tener entre 3 y 24 caracteres y usar solo números y letras minúsculas. Si usa el nombre que aparece en el ejemplo, recibirá un error porque ese nombre ya está en uso.

```azurecli-interactive
az storage account create -n myuniquestorage -g TestRG1 -l westus --sku Standard_LRS
```

Si posteriormente necesita recuperar este recurso, use el siguiente comando:

```azurecli-interactive
az storage account show --name myuniquestorage --resource-group TestRG1
```

## <a name="add-a-tag"></a>Agregar una etiqueta

Las etiquetas permiten organizar los recursos de acuerdo con diferentes propiedades. Por ejemplo, puede tener varios recursos en distintos grupos de recursos que pertenecen al mismo departamento. Puede aplicar una etiqueta de departamento y un valor a esos recursos para marcarlos como pertenecientes a la misma categoría. O bien, puede marcar si un recurso se usa en un entorno de producción o de prueba. En este artículo, aplicará etiquetas a un solo recurso; sin embargo, es probable que en su entorno tenga más sentido aplicar etiquetas a todos los recursos.

El siguiente comando incluye dos etiquetas en la cuenta de almacenamiento:

```azurecli-interactive
az resource tag --tags Dept=IT Environment=Test -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

Las etiquetas se actualizan como un único objeto. Para agregar una etiqueta a un recurso que ya incluye etiquetas, primero recupere las etiquetas existentes. Agregue la nueva etiqueta al objeto que contiene las etiquetas existentes y vuelva a aplicar todas las etiquetas al recurso.

```azurecli-interactive
jsonrtag=$(az resource show -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

## <a name="search-for-resources"></a>Búsqueda de recursos

Use el comando **az resource list** para recuperar recursos de diferentes condiciones de búsqueda.

* Para obtener un recurso por su nombre, proporcione el parámetro **name**:

  ```azurecli-interactive
  az resource list -n myuniquestorage
  ```

* Para obtener todos los recursos de un grupo de recursos, proporcione el parámetro **resource-group**:

  ```azurecli-interactive
  az resource list --resource-group TestRG1
  ```

* Para obtener todos los recursos con un nombre de etiqueta y un valor, proporcione el parámetro **tag**:

  ```azurecli-interactive
  az resource list --tag Dept=IT
  ```

* Para obtener todos los recursos con un tipo de recurso en particular, proporcione el parámetro **resource-type**:

  ```azurecli-interactive
  az resource list --resource-type "Microsoft.Storage/storageAccounts"
  ```

## <a name="get-resource-id"></a>Obtener el identificador de recurso

Muchos comandos toman un identificador de recurso como un parámetro. Para obtener el identificador para un recurso y almacenarlo en una variable, use:

```azurecli-interactive
webappID=$(az resource show -g exampleGroup -n exampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
```

## <a name="lock-a-resource"></a>Bloqueo de un recurso

Cuando deba asegurarse de que un recurso crítico no se elimine o modifique por accidente, aplique un bloqueo al recurso. Puede especificar **CanNotDelete** o **ReadOnly**.

Para crear o eliminar bloqueos de administración, debe tener acceso a las acciones `Microsoft.Authorization/*` o `Microsoft.Authorization/locks/*`. Entre los roles integrados, solamente se conceden esas acciones al propietario y al administrador de acceso de usuarios.

Para aplicar un bloqueo, use el siguiente comando:

```azurecli-interactive
az lock create --lock-type CanNotDelete --resource-name myuniquestorage --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --name storagelock
```

El recurso bloqueado en el ejemplo anterior no se puede eliminar hasta que no se quite el bloqueo. Para quitar un bloqueo, use:

```azurecli-interactive
az lock delete --name storagelock --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --resource-name myuniquestorage
```

Para más información sobre el establecimiento de bloqueos, consulte [Bloqueo de recursos con Azure Resource Manager](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Eliminación de recursos o grupos de recursos
Puede quitar un recurso o un grupo de recursos. Al quitar un grupo de recursos, también se quitan todos los recursos que contiene.

* Para eliminar un recurso del grupo de recursos, use el comando delete en el tipo de recurso que esté eliminando. Este comando elimina el recurso, pero no el grupo de recursos.

  ```azurecli-interactive
  az storage account delete -n myuniquestorage -g TestRG1
  ```

* Para eliminar un grupo de recursos y todos sus recursos, use el comando **az group delete**.

  ```azurecli-interactive
  az group delete -n TestRG1
  ```

En ambos comandos, se le pide que confirme que quiere quitar el recurso o el grupo de recursos.

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre la creación de plantillas de Resource Manager, consulte [Authoring Azure Resource Manager templates](resource-group-authoring-templates.md) (Creación de plantillas de Azure Resource Manager).
* Para obtener información sobre cómo implementar plantillas, consulte [Implementación de una aplicación con la plantilla de Azure Resource Manager](resource-group-template-deploy-cli.md).
* Puede mover recursos existentes a un nuevo grupo de recursos. Para consultar ejemplos, vea [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](resource-group-move-resources.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).