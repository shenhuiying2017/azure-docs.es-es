---
title: "Asignación de un acceso de MSI a un recurso de Azure mediante CLI de Azure"
description: Instrucciones paso a paso para asignar una identidad de servicio administrada (MSI) en un recurso y acceso a otro recurso, mediante CLI de Azure.
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
ms.date: 09/25/2017
ms.author: daveba
ms.openlocfilehash: ca228a99eec1d376e456f5730aaaf8c569620f43
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Asignación de un acceso de Managed Service Identity (MSI) a un recurso mediante CLI de Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Una vez haya configurado un recurso de Azure con una identidad de servicio administrada, puede dar el acceso de MSI a otro recurso, al igual que cualquier entidad de seguridad. En este ejemplo se muestra cómo otorgar acceso de MSI a una máquina virtual o un conjunto de escalado de máquinas virtuales de Azure a una cuenta de almacenamiento de Azure mediante la CLI de Azure.

## <a name="prerequisites"></a>requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Para ejecutar los ejemplos de script de la CLI, tiene tres opciones:

- Usar [Azure Cloud Shell](../../cloud-shell/overview.md) desde Azure Portal (consulte la sección siguiente).
- Usar Azure Cloud Shell integrado a través del botón "Pruébelo", situado en la esquina superior derecha de cada bloque de código.
- [Instalar la versión más reciente de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 o posterior), si prefiere usar una consola local de CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Uso de RBAC para asignar el acceso de MSI a otro recurso

Después de habilitar MSI en un recurso de Azure, como una [máquina virtual de Azure](qs-configure-cli-windows-vm.md) o un [conjunto de escalado de máquinas virtuales de Azure](qs-configure-cli-windows-vmss.md): 

1. Si usa la CLI de Azure en una consola local, lo primero que debe hacer es iniciar sesión en Azure mediante el [inicio de sesión de az](/cli/azure/reference-index#az_login). Use una cuenta asociada a la suscripción de Azure en la que desearía implementar la máquina virtual o el conjunto de escalado de máquinas virtuales:

   ```azurecli-interactive
   az login
   ```

2. En este ejemplo, vamos a dar a una máquina virtual de Azure acceso a una cuenta de almacenamiento. En primer lugar se utiliza una [az resource list](/cli/azure/resource/#az_resource_list) para obtener la entidad de servicio para la máquina virtual denominada "myVM":

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   En el caso de un conjunto de escalado de máquinas virtuales de Azure, el comando es el mismo excepto aquí, donde obtiene la entidad de servicio para el conjunto de escalado de máquinas virtuales denominado "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Cuando ya tenga el identificador de la entidad de servicio, use [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) para conceder a la máquina virtual o el conjunto de escalado de máquinas virtuales acceso de "lector" a una cuenta de almacenamiento denominada "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>solución de problemas

Si la identidad de servicio administrada para el recurso no aparece en la lista de identidades disponibles, compruebe que la identidad se haya habilitado correctamente. En nuestro caso, podemos volver a la máquina virtual o el conjunto de escalado de máquinas virtuales de Azure en [Azure Portal](https://portal.azure.com) y:

- Buscar en la página "Configuración" y asegurarnos de que MSI habilitado = "Sí".
- Mire la página "Extensiones" y asegúrese de que la extensión MSI se implementó correctamente (la página **Extensiones** no está disponible para un conjunto de escalado de máquinas virtuales de Azure).

Si alguna de las opciones no es correcta, puede que tenga que volver a implementar la identidad de servicio administrada en el recurso nuevo o solucionar el error de implementación.

## <a name="related-content"></a>Contenido relacionado

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](overview.md) (Introducción a Managed Service Identity).
- Para habilitar MSI en una máquina virtual de Azure, consulte [Configure a VM Managed Service Identity (MSI) using Azure CLI](qs-configure-cli-windows-vm.md) (Configuración de Managed Service Identity [MSI] en una máquina virtual de Azure con la CLI de Azure).
- Para habilitar MSI en un conjunto de escalado de máquinas virtuales de Azure, consulte [Configure an Azure Virtual Machine Scale Set Managed Service Identity (MSI) using the Azure portal](qs-configure-portal-windows-vmss.md) (Configuración de Managed Service Identity [MSI] de conjunto de escalado de máquinas virtuales de Azure con Azure Portal).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.

