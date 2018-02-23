---
title: "Creación de un clúster de Azure Container Service (AKS)"
description: "Creación de un clúster de AKS con la CLI o Azure Portal."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 3e884bc16680d74801911547045deb48246afccd
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="create-an-azure-container-service-aks-cluster"></a>Creación de un clúster de Azure Container Service (AKS)

Un clúster de Azure Container Service (AKS) se puede crear con la CLI de Azure o Azure Portal.

## <a name="azure-cli"></a>Azure CLI

Use el comando [az aks create][az-aks-create] para crear el clúster de AKS.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

Las siguientes opciones están disponibles con el comando `az aks create`.

| Argumento | DESCRIPCIÓN | Obligatorio |
|---|---|:---:|
| `--name``-n` | Nombre de recurso para el clúster administrado. | Sí |
| `--resource-group``-g` | Nombre del grupo de recursos de Azure Container Service (AKS). | Sí |
| `--admin-username``-u` | Nombre de usuario para Linux Virtual Machines.  Valor predeterminado: azureuser. | no |
| ` --client-secret` | Secreto asociado a la entidad de servicio. | no |
| `--dns-name-prefix``-p` | Prefijo DNS para la dirección ip pública de clústeres. | no |
| `--generate-ssh-keys` | Generar archivos de clave pública y privada SSH si faltan. | no |
| `--kubernetes-version``-k` | La versión de Kubernetes que se va a usar para crear el clúster, como “1.7.9” o “1.8.2”.  Valor predeterminado: 1.7.7. | no |
| `--no-wait` | No espere hasta que finalice la operación de ejecución prolongada. | no |
| `--node-count``-c` | Número predeterminado de nodos para los grupos de nodos.  Valor predeterminado: 3. | no |
| `--node-osdisk-size` | Tamaño de osDisk en GB de la máquina virtual del grupo de nodos. | no |
| `--node-vm-size``-s` | Tamaño de la máquina virtual.  Valor predeterminado: Standard_D1_v2. | no |
| `--service-principal` | Entidad de servicio utilizada para la autenticación de clúster. | no |
| `--ssh-key-value` | Valor de archivo de clave o ruta de acceso al archivo de clave SSH.  Valor predeterminado: ~/.ssh/id_rsa.pub. | no |
| `--tags` | Etiquetas separadas por espacios con el formato “clave [= valor]”. Use “” para borrar las etiquetas existentes. | no |

## <a name="azure-portal"></a>Azure Portal

Para instrucciones sobre cómo implementar un clúster de AKS con Azure Portal, consulte el [inicio rápido de Azure Portal][aks-portal-quickstart] de Azure Container Service (AKS). 

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md