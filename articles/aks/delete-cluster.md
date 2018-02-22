---
title: "Eliminación de un clúster de Azure Container Service (AKS)"
description: "Eliminación de un clúster de Azure Container Service (AKS) con la CLI o Azure Portal."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 78056288f45616eda427f8e708efc679f8a5202c
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="delete-an-azure-container-service-aks-cluster"></a>Eliminación de un clúster de Azure Container Service (AKS)

Al eliminar un clúster de Azure Container Service (AKS), el grupo de recursos en el que se implementó el clúster se mantiene, pero se eliminan todos los recursos relacionados con AKS. En este documento se muestra cómo eliminar un clúster de AKS mediante la Interfaz de la línea de comandos de Azure y Azure Portal. 

Además del clúster, también se puede eliminar el grupo de recursos en el que se implementó, lo cual también elimina el clúster de AKS.

## <a name="azure-cli"></a>Azure CLI

Use el comando [az aks delete][az-aks-delete] para eliminar el clúster de AKS.

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

Las siguientes opciones están disponibles con el comando `az aks delete`.

| Argumento | DESCRIPCIÓN | Obligatorio |
|---|---|:---:|
| `--name``-n` | Nombre de recurso para el clúster administrado. | Sí |
| `--resource-group``-g` | Nombre del grupo de recursos de Azure Container Service (AKS). | Sí |
| `--no-wait` | No espere hasta que finalice la operación de ejecución prolongada. | no |
| `--yes``-y` | No solicita confirmación. | no |

## <a name="azure-portal"></a>Azure Portal

Sin salir de Azure Portal, busque el grupo de recursos que contiene el recurso de Azure Container Service (AKS), seleccione el recurso y haga clic en **Eliminar**. Se le pide que confirme la operación de eliminación.

![Portal para eliminar el clúster de AKS](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az_aks_delete