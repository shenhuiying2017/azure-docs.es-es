---
title: "Creación de una aplicación administrada de Azure con la CLI de Azure | Microsoft Docs"
description: "Se explica cómo crear una aplicación administrada de Azure diseñada para los miembros de su organización."
services: azure-resource-manager
author: tfitzmac
manager: timlt
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: 35059603096279f7d58da1c1b40dd2ab3f1b5c38
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="create-and-deploy-an-azure-managed-application-with-azure-cli"></a>Creación e implementación de una aplicación administrada de Azure con la CLI de Azure

En este artículo se proporciona una introducción al uso de aplicaciones administradas. Agregará una definición de aplicación administrada a un catálogo interno para los usuarios de su organización. A continuación, implementará la aplicación administrada en la suscripción. Para simplificar la introducción, ya se han creado los archivos para la aplicación administrada. Un archivo define la infraestructura de la solución. Un segundo archivo define la interfaz de usuario para la implementación de la aplicación administrada mediante el portal. Esos archivos están disponibles a través de GitHub. Aprenderá a crear esos archivos en el tutorial [Creación de la aplicación de catálogo de servicio](publish-service-catalog-app.md).

Cuando haya terminado, tendrá tres grupos de recursos que contienen diferentes partes de la aplicación administrada.

| Grupos de recursos | Contains | Descripción |
| -------------- | -------- | ----------- |
| appDefinitionGroup | La definición de la aplicación administrada. | El publicador crea este grupo de recursos y la definición de la aplicación administrada. Cualquier persona con acceso a la definición de la aplicación administrada puede implementarla. |
| applicationGroup | La instancia de la aplicación administrada. | El consumidor crea este grupo de recursos y la instancia de la aplicación administrada. El consumidor puede actualizar la aplicación administrada mediante esta instancia. |
| infrastructureGroup | Los recursos de la aplicación administrada. | Este grupo de recursos se crea automáticamente cuando se crea la aplicación administrada. El publicador tiene acceso a este grupo de recursos para administrar la aplicación. El consumidor tiene acceso limitado al grupo de recursos. |

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>Creación de un grupo de recursos para la definición

La definición de la aplicación administrada existe en un grupo de recursos. El grupo de recursos de Azure es una colección lógica en la que se implementan y administran los recursos de Azure.

Para crear un grupo de recursos, ejecute el comando siguiente:

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>Creación de la definición de aplicación administrada

Al definir la aplicación administrada, seleccionará un usuario, un grupo o una aplicación que administra los recursos en nombre del consumidor. Esta identidad tiene permisos en el grupo de recursos administrado, según el rol asignado. Si es necesario, creará un grupo de usuarios de Azure Active Directory para administrar los recursos. Sin embargo, en este artículo, usará su propia identidad.

Para obtener el identificador de objeto de su identidad, proporcione su nombre principal de usuario en el siguiente comando:

```azurecli-interactive
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
```

A continuación, necesitará el identificador de definición de rol del rol RBAC integrado al que desea conceder acceso al usuario. El comando siguiente muestra cómo obtener el identificador de definición de rol para el rol Propietario:

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

Ahora, cree el recurso de la definición de aplicación administrada. La aplicación administrada contiene solo una cuenta de almacenamiento.

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://raw.githubusercontent.com/Azure/azure-managedapp-samples/master/samples/201-managed-storage-account/managedstorage.zip"
```

Cuando finalice el comando, tendrá una definición de aplicación administrada en el grupo de recursos. 

Algunos de los parámetros usados en el ejemplo anterior son:

* **resource-group**: el nombre del grupo de recursos donde se creó la definición de aplicación administrada.
* **lock-level**: el tipo de bloqueo aplicado al grupo de recursos administrado. Impide que el cliente realice operaciones no deseadas en este grupo de recursos. Actualmente, ReadOnly es el único nivel de bloqueo admitido. Cuando se especifica ReadOnly, el cliente solo puede leer los recursos presentes en el grupo de recursos administrado.
* **authorizations**: describe el identificador de entidad de seguridad y el identificador de definición de rol que se usan para conceder el permiso al grupo de recursos administrado. Se especifica en el formato `<principalId>:<roleDefinitionId>`. También se pueden especificar varios valores para esta propiedad. Si se necesitan varios valores, se deben especificar de esta forma `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Los valores van separados por un espacio.
* **package-file-uri**: la ubicación de un paquete comprimido que contiene los archivos necesarios. El paquete contiene, como mínimo, los archivos **mainTemplate.json** y **createUiDefinition.json**. **mainTemplate.json** define los recursos de Azure que se aprovisionan como parte de la aplicación administrada. La plantilla no difiere de una plantilla habitual de Resource Manager. **createUiDefinition.json** genera la interfaz de usuario para los usuarios que crean la aplicación administrada mediante el portal.

## <a name="create-resource-group-for-managed-application"></a>Creación de un grupo de recursos para la aplicación administrada

Ahora, está listo para implementar la aplicación administrada. 

Para contener la aplicación administrada implementada, se necesita un grupo de recursos. Use **westcentralus** para la ubicación.

```azurecli-interactive
az group create --name applicationGroup --location westcentralus
```

## <a name="deploy-the-managed-application"></a>Implementación de la aplicación administrada

Implementará la aplicación con los siguientes comandos:

```azurecli-interactive
appid=$(az managedapp definition show --name ManagedStorage --resource-group appDefinitionGroup --query id --output tsv)
subid=$(az account show --query id --output tsv)
managedGroupId=/subscriptions/$subid/resourceGroups/infrastructureGroup

az managedapp create \
  --name storageApp \
  --location "westcentralus" \
  --kind "Servicecatalog" \
  --resource-group applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id $managedGroupId \
  --parameters "{\"storageAccountNamePrefix\": {\"value\": \"storage\"}, \"storageAccountType\": {\"value\": \"Standard_LRS\"}}"
```

Algunos de los parámetros usados en el ejemplo anterior son:

* **managedapp-definition-id**: el identificador de la definición que creó anteriormente en este artículo.
* **managed-rg-id**: el identificador del grupo de recursos de los recursos asociados a la aplicación administrada. El comando crea este grupo de recursos. **No debe existir antes de ejecutar el comando**. El publicador administra este grupo de recursos. 
* **resource-group**: grupo de recursos donde se crea el recurso de la aplicación administrada.
* **parameters**: los parámetros que son necesarios para los recursos asociados a la aplicación administrada.

Después de que la implementación ha finalizado correctamente, verá que la aplicación administrada se crea en applicationGroup. El recurso storageAccount se crea en infrastructureGroup.

## <a name="next-steps"></a>Pasos siguientes

* Para una introducción a las aplicaciones administradas, consulte [Introducción a las aplicaciones administradas](overview.md).
* Para ver ejemplos de los archivos, consulte los [ejemplos de aplicaciones administradas](https://github.com/Azure/azure-managedapp-samples/tree/master/samples).
* Para aprender a crear un archivo de definición de interfaz de usuario para una aplicación administrada, consulte [Introducción a CreateUiDefinition](create-uidefinition-overview.md).
