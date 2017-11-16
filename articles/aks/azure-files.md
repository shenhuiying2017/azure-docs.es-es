---
title: Uso de Azure Files con AKS | Microsoft Docs
description: Usar de Azure Disks con AKS
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 11457e6556e6400d8f58f71c71ab1e790bcef8f1
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2017
---
# <a name="using-azure-files-with-kubernetes"></a>Uso de Azure Files con Kubernetes

Las aplicaciones basadas en contenedor suelen necesitar acceder y conservar los datos en un volumen de datos externos. Azure Files puede utilizarse como este almacén de datos externos. En este artículo se detallan el uso de Azure Files como un volumen de Kubernetes Azure Container Service.

Para más información sobre volúmenes de Kubernetes, consulte el artículo sobre [volúmenes de Kubernetes][kubernetes-volumes].

## <a name="creating-a-file-share"></a>Creación de un recurso compartido de archivos

Se puede usar un recurso compartido de Azure Files existente con Azure Container Service. Si tiene que crear uno, use el siguiente conjunto de comandos.

Cree un grupo de recursos para el recurso compartido de Azure Files con el comando [az group create][az-group-create]. El grupo de recursos de la cuenta de almacenamiento y el clúster de Kubernetes deben estar en la misma región.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Puede utilizar el comando [az storage account create][az-storage-create] para crear una cuenta de Azure Storage. El nombre de la cuenta de almacenamiento debe ser único. Actualice el valor del argumento `--name` con un valor único.

```azurecli-interactive
az storage account create --name mystorageaccount --resource-group myResourceGroup --sku Standard_LRS
```

Utilice el comando [az storage account keys list][az-storage-key-list] para devolver la clave de almacenamiento. Actualice el valor del argumento `--account-name` con el nombre de cuenta de almacenamiento único.

Tome nota de uno de los valores de clave, pues se utilizará en los pasos siguientes.

```azurecli-interactive
az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup --output table
```

Utilice el comando [az storage share create][az-storage-share-create] para crear el recurso compartido de Azure Files. Actualice el valor de `--account-key` con el valor recopilado en el último paso.

```azurecli-interactive
az storage share create --name myfileshare --account-name mystorageaccount --account-key <key>
```

## <a name="create-kubernetes-secret"></a>Creación de un secreto de Kubernetes

Kubernetes necesita credenciales para acceder al recurso compartido de archivos. En lugar de almacenar el nombre de la cuenta de Azure Storage y la clave con cada pod, se almacena una vez en un [secreto de Kubernetes][kubernetes-secret], al que se referirán todos los volúmenes de Azure Files. 

Los valores de un manifiesto de secreto de Kubernetes deben estar codificados en base64. Utilice los siguientes comandos para devolver valores codificados.

En primer lugar, codifique el nombre de la cuenta de almacenamiento. Reemplace `storage-account` por el nombre de la cuenta de Azure Storage.

```azurecli-interactive
echo -n <storage-account> | base64
```

A continuación, se necesita la clave de acceso de la cuenta de almacenamiento. Ejecute el comando siguiente para devolver la clave codificada. Reemplace `storage-key` por la clave recopilada en un paso anterior.

```azurecli-interactive
echo -n <storage-key> | base64
```

Cree un archivo denominado `azure-secret.yml` y cópielo en el siguiente código YAML. Actualice los valores `azurestorageaccountname` y `azurestorageaccountkey` con los valores codificados en base64 recuperados en el último paso.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: azure-secret
type: Opaque
data:
  azurestorageaccountname: <base64_encoded_storage_account_name>
  azurestorageaccountkey: <base64_encoded_storage_account_key>
```

Utilice el comando [kubectl apply][kubectl-apply] para crear el secreto.

```azurecli-interactive
kubectl apply -f azure-secret.yml
```

## <a name="mount-file-share-as-volume"></a>Montaje del recurso compartido de archivos como volumen

Puede montar el recurso compartido de Azure Files en su pod mediante la configuración del volumen en su especificación. Cree un nuevo archivo denominado `azure-files-pod.yml` con el contenido siguiente. Actualice `share-name` con el nombre dado al recurso compartido de Azure Files.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: kubernetes/pause
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: <share-name>
      readOnly: false
```

Use kubectl para crear un pod.

```azurecli-interactive
kubectl apply -f azure-files-pod.yml
```

Ahora tiene un contenedor en ejecución con el recurso compartido de Azure Files montado en el directorio `/mnt/azure`. Puede ver el montaje del volumen al inspeccionar el pod a través de `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre los volúmenes de Kubernetes mediante Azure Files.

> [!div class="nextstepaction"]
> [Complemento de Kubernetes para Azure Files](https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md)

<!-- LINKS -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[kubectl-apply]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#apply
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[az-group-create]: /cli/azure/group#az_group_create