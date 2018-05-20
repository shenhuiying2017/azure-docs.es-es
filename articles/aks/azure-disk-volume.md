---
title: Usar de Azure Disks con AKS
description: Usar de Azure Disks con AKS
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b790213e19b9f2aaef74a3f670c89246f54fd6d7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="volumes-with-azure-disks"></a>Volúmenes con discos de Azure

Las aplicaciones que usan contenedores a menudo necesitan acceder a un volumen de datos externo y conservar datos en él. Los discos de Azure se pueden usar como este almacén de datos externos. En este artículo se describe el uso de un disco de Azure como un volumen de Kubernetes en un clúster de Azure Kubernetes Service (AKS).

Para más información sobre volúmenes de Kubernetes, consulte el artículo sobre [volúmenes de Kubernetes][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Creación de un disco de Azure

Antes de montar un disco administrado de Azure como un volumen de Kubernetes, el disco debe existir en el mismo grupo de recursos que los recursos del clúster de AKS. Para encontrar este grupo de recursos, use el comando [az group list][az-group-list].

```azurecli-interactive
az group list --output table
```

Lo que busca es un grupo de recursos con un nombre parecido a `MC_clustername_clustername_locaton`, donde clustername es el nombre del clúster de AKS y location es la región de Azure donde se ha implementado el clúster.

```console
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Use el comando [az disk create][az-disk-create] para crear el disco de Azure.

En este ejemplo, actualice `--resource-group` con el nombre del grupo de recursos y `--name` con un nombre de su elección.

```azurecli-interactive
az disk create \
  --resource-group MC_myAKSCluster_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Cuando haya creado el disco, verá un resultado similar al siguiente. Este valor es el identificador del disco, que se usa al montar el disco en un pod de Kubernetes.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Montaje del disco como un volumen

Monte el disco de Azure en su pod mediante la configuración del volumen en las especificaciones del contenedor.

Cree un nuevo archivo denominado `azure-disk-pod.yaml` con el contenido siguiente. Actualice `diskName` con el nombre del disco recién creado y `diskURI` con el identificador del disco. Además, anote el valor de `mountPath`, que es la ruta de acceso en la que se monta el disco de Azure en el pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Use kubectl para crear el pod.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

Ahora tiene un pod en ejecución con un disco de Azure montado en `/mnt/azure`.

## <a name="next-steps"></a>Pasos siguientes

Aprenda más sobre los volúmenes de Kubernetes con discos de Azure.

> [!div class="nextstepaction"]
> [Complemento de Kubernetes para discos de Azure][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
