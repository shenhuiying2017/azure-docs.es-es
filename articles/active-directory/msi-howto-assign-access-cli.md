---
title: "Asignación de un acceso de MSI a un recurso de Azure mediante CLI de Azure"
description: Instrucciones paso a paso para asignar una identidad de servicio administrada (MSI) en un recurso y acceso a otro recurso, mediante CLI de Azure.
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2017
ms.author: bryanla
ms.openlocfilehash: e77915c1d982ccf6262ffcbc09dc91dfd986dac5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Asignación de un acceso de Managed Service Identity (MSI) a un recurso mediante CLI de Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Una vez haya configurado un recurso de Azure con una identidad de servicio administrada, puede dar el acceso de MSI a otro recurso, al igual que cualquier entidad de seguridad. En este ejemplo se muestra cómo otorgar acceso de MSI a una máquina virtual de Azure para una cuenta de almacenamiento de Azure, mediante CLI de Azure.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Para ejecutar los ejemplos de script de la CLI, tiene tres opciones:

- Usar [Azure Cloud Shell](../cloud-shell/overview.md) desde Azure Portal (consulte la sección siguiente).
- Usar Azure Cloud Shell integrado a través del botón "Pruébelo", situado en la esquina superior derecha de cada bloque de código.
- [Instalar la versión más reciente de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 o posterior), si prefiere usar una consola local de CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Uso de RBAC para asignar el acceso de MSI a otro recurso

Después de habilitar MSI en un recurso de Azure, [como una máquina virtual de Azure](msi-qs-configure-cli-windows-vm.md): 

1. Si usa la CLI de Azure en una consola local, lo primero que debe hacer es iniciar sesión en Azure mediante el [inicio de sesión de az](/cli/azure/#login). Use una cuenta asociada a la suscripción de Azure en la que desearía implementar la máquina virtual:

   ```azurecli-interactive
   az login
   ```

2. En este ejemplo, vamos a dar a una máquina virtual de Azure acceso a una cuenta de almacenamiento. En primer lugar, se usa [az resource list](/cli/azure/resource/#list) para obtener la entidad de servicio para la máquina virtual denominada "myVM", que se creó cuando se habilitó MSI en la máquina virtual:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```

3. Cuando ya se tenga el identificador de la entidad de servicio, se usa [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) para conceder a la máquina virtual acceso de "lector" a una cuenta de almacenamiento denominada "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>Solución de problemas

Si la identidad de servicio administrada para el recurso no aparece en la lista de identidades disponibles, compruebe que la identidad se haya habilitado correctamente. En nuestro caso, podemos volver a la máquina virtual de Azure en [Azure Portal](https://portal.azure.com) y:

- Buscar en la página "Configuración" y asegurarnos de que MSI habilitado = "Sí".
- Buscar en la página "Extensiones" y asegurarnos de que la extensión MSI se ha implementado correctamente.

Si alguna de las opciones no es correcta, puede que tenga que volver a implementar la identidad de servicio administrada en el recurso nuevo o solucionar el error de implementación.

## <a name="related-content"></a>Contenido relacionado

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](msi-overview.md) (Introducción a Managed Service Identity).
- Para habilitar MSI en una máquina virtual de Azure, consulte [Configuración de Managed Service Identity (MSI) de una máquina virtual de Azure con CLI de Azure](msi-qs-configure-cli-windows-vm.md).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.

