---
title: archivo de inclusión
description: archivo de inclusión
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 04/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 6ed114ea6162c9d4888b6f86998cfb422a3944e8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32198233"
---
## <a name="create-a-service-principal"></a>Creación de una entidad de servicio

Para crear a una entidad de servicio con acceso a su registro de contenedor, puede usar el siguiente script. Actualice la variable `ACR_NAME` con el nombre de su registro de contenedor y, de manera opcional, el valor `--role` del comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para otorgar permisos diferentes. Debe tener la [CLI de Azure](/cli/azure/install-azure-cli) instalada para poder usar este script.

Tras ejecutar el script, tome nota del **identificador** y la **contraseña** de la entidad de servicio. Cuando disponga de las credenciales, podrá configurar las aplicaciones y los servicios para autenticarse en su registro de contenedor como la entidad de servicio.

[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Uso de una entidad de servicio existente

Para conceder al registro acceso a una entidad de servicio existente, debe asignar un nuevo rol a la entidad de servicio. Al igual que con la creación de una nueva entidad de servicio, puede conceder acceso de extracción, inserción y extracción, así como de propietario.

El script siguiente usa el comando [az role assignment create][az-role-assignment-create] para conceder permisos de *extracción* a la entidad de servicio especificada en la variable `SERVICE_PRINCIPAL_ID`. Ajuste el valor `--role` si quiere conceder un nivel de acceso diferente.

[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
