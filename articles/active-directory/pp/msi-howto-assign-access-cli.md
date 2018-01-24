---
title: "Asignación a una MSI asignada por el usuario acceso a un recurso de Azure mediante la CLI de Azure"
description: Instrucciones paso a paso para asignar a una identidad de servicio administrada (MSI) asignada por el usuario en un recurso acceso a otro recurso mediante la CLI de Azure.
services: active-directory
documentationcenter: 
author: bryanLa
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 732fc1981bdf95e7548ea0ebe0ca8ece00f483ce
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="assign-a-user-assigned-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Asignación a una identidad de servicio administrada (MSI) asignada por el usuario acceso a un recurso mediante la CLI de Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Una vez haya creado una identidad de servicio administrada asignada por el usuario, puede dar a la MSI acceso a otro recurso, al igual que cualquier entidad de seguridad. En este ejemplo se muestra cómo proporcionar a la MSI asignada por el usuario acceso a una cuenta de Azure Storage mediante la CLI de Azure.

## <a name="prerequisites"></a>requisitos previos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Para ejecutar los ejemplos de script de la CLI de este tutorial, tiene dos opciones:

- Use [Azure Cloud Shell](~/articles/cloud-shell/overview.md) desde Azure Portal o mediante el botón "Pruébelo", situado en la esquina superior derecha de cada bloque de código.
- [Instale la versión más reciente de la CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 o posterior), si prefiere usar una consola de la CLI local. A continuación, inicie sesión en Azure mediante [az login](/cli/azure/#login). Use una cuenta asociada a la suscripción de Azure en la que desearía implementar la MSI asignada por el usuario y la máquina virtual:

   ```azurecli
   az login
   ```

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Uso de RBAC para asignar el acceso de MSI a otro recurso

Para usar una MSI con un recurso de host (por ejemplo, una máquina virtual), para un acceso de inicio de sesión o a los recursos, se debe proporcionar a la MSI, en primer lugar, acceso a los recursos a través de la asignación de roles. Puede hacerlo antes de asociar la MSI al host o después. Para ver todos los pasos sobre cómo crear y asociar a una máquina virtual, consulte [Configuración de una identidad de servicio administrada (MSI) asignada por el usuario para una máquina virtual, mediante la CLI de Azure](msi-qs-configure-cli-windows-vm.md).

En el ejemplo siguiente, se proporciona a una MSI asignada por el usuario acceso a una cuenta de almacenamiento.  

1. Para proporcionar acceso a la MSI, necesita el identificador de cliente (también conocido como identificador de aplicación) de la entidad de servicio de la MSI. El identificador de cliente lo proporciona [az identity create](/cli/azure/identity#az_identity_create), tras el aprovisionamiento de la MSI y su entidad de servicio (se muestra aquí como referencia):

   ```azurecli-interactive
   az identity create -g rgPrivate -n msiServiceApp
   ```

   Una respuesta correcta contiene el identificador de cliente de la entidad de servicio de la MSI asignada por el usuario, en la propiedad `clientId`:

   ```json
   {
        "clientId": "9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp/credentials?tid=733a8f0p-ec41-4e69-8adz-971fc4b533bl&oid=z4baa4fc-fce4-4202-8250-5fb359abblll&aid=9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp",
        "location": "westcentralus",
        "name": "msiServiceApp",
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "resourceGroup": "rgPrivate",
        "tags": {},
        "tenantId": "733a8f0p-ec41-4e69-8adz-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
   }
   ```

2. Una vez que se conoce el identificador de cliente, utilice [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) para asignar a la MSI acceso a otro recurso. Asegúrese de usar el identificador de cliente para el parámetro `--assignee`, así como el identificador de suscripción coincidente y el nombre del grupo de recursos, tal como se devuelve al ejecutar `az identity create`. Aquí se asigna a la MSI acceso de "Lectura" a una cuenta de almacenamiento denominada "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee 9391e5b1-dada-4a8z-834a-43ad44f296bl --role Reader --scope /subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

   Una respuesta correcta será similar al siguiente resultado:

   ```json
   {
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct/providers/Microsoft.Authorization/roleAssignments/f4766864-9493-43c6-91d7-abd131c3c017",
        "name": "f4766864-9493-43c6-91d7-abd131c3c017",
        "properties": {
            "additionalProperties": {
            "createdBy": null,
            "createdOn": "2017-12-21T20:49:37.5590544Z",
            "updatedBy": "pd78b21f-17a4-41az-b7db-9aadec3376bl",
            "updatedOn": "2017-12-21T20:49:37.5590544Z"
            },
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "roleDefinitionId": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "scope": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct"
        },
        "resourceGroup": "rgPrivate",
        "type": "Microsoft.Authorization/roleAssignments"
   }
   ```

## <a name="next-steps"></a>pasos siguientes

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](msi-overview.md) (Introducción a Managed Service Identity).
- Para configurar una MSI asignada por el usuario en una máquina virtual de Azure, consulte [Configuración de una identidad de servicio administrada (MSI) asignada por el usuario para una máquina virtual, mediante la CLI de Azure](msi-qs-configure-cli-windows-vm.md).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.

