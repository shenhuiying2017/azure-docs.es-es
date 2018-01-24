---
title: Uso de Azure Files con AKS
description: Usar de Azure Disks con AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/17/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b6267dd2bc1b29229b2e8016e2429ed88b7bf676
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="using-azure-files-with-kubernetes"></a>Uso de Azure Files con Kubernetes

Las aplicaciones que usan contenedores a menudo necesitan acceder a un volumen de datos externo y conservar datos en él. Azure Files puede utilizarse como este almacén de datos externos. En este artículo se detallan el uso de Azure Files como un volumen de Kubernetes Azure Container Service.

Para más información sobre volúmenes de Kubernetes, consulte el artículo sobre [volúmenes de Kubernetes][kubernetes-volumes].

## <a name="create-an-azure-file-share"></a>Creación de un recurso compartido de archivos de Azure

Antes de utilizar un recurso compartido de archivos de Azure como volumen de Kubernetes, es preciso crear una cuenta de Azure Storage y el recurso compartido de archivos. El script siguiente puede utilizarse para completar estas tareas. Anote o actualice los valores de los parámetros, ya que algunos son necesarios al crear el volumen de Kubernetes.

```azurecli-interactive
# Change these four parameters
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create the Resource Group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create the storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)
```

## <a name="create-kubernetes-secret"></a>Creación de un secreto de Kubernetes

Kubernetes necesita credenciales para acceder al recurso compartido de archivos. Estas credenciales se almacenan en un [secreto de Kubernetes][kubernetes-secret], al que se hace referencia al crear un pod de Kubernetes.

Al crear un secreto de Kubernetes, sus valores deben tener codificación en base64.

En primer lugar, codifique el nombre de la cuenta de almacenamiento. Si fuera necesario, reemplace `$AKS_PERS_STORAGE_ACCOUNT_NAME` por el nombre de la cuenta de Azure Storage.

```azurecli-interactive
echo -n $AKS_PERS_STORAGE_ACCOUNT_NAME | base64
```

A continuación, codifique la clave de la cuenta de Storage. Si fuera necesario, reemplace `$STORAGE_KEY` por el nombre de la clave de la cuenta de Azure Storage.

```azurecli-interactive
echo -n $STORAGE_KEY | base64
```

Cree un archivo denominado `azure-secret.yaml` y cópielo en el siguiente código YAML. Actualice los valores `azurestorageaccountname` y `azurestorageaccountkey` con los valores codificados en base64 recuperados en el último paso.

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

Utilice el comando [kubectl create][kubectl-create] para crear el secreto.

```azurecli-interactive
kubectl create -f azure-secret.yaml
```

## <a name="mount-file-share-as-volume"></a>Montaje del recurso compartido de archivos como volumen

Puede montar el recurso compartido de Azure Files en su pod mediante la configuración del volumen en su especificación. Cree un nuevo archivo denominado `azure-files-pod.yaml` con el contenido siguiente. Actualice `aksshare` con el nombre dado al recurso compartido de Azure Files.

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
      shareName: aksshare
      readOnly: false
```

Use kubectl para crear un pod.

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

Ahora tiene un contenedor en ejecución con el recurso compartido de Azure Files montado en el directorio `/mnt/azure`. Puede ver el montaje del volumen al inspeccionar el pod a través de `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>pasos siguientes

Más información sobre los volúmenes de Kubernetes mediante Azure Files.

> [!div class="nextstepaction"]
> [Complemento de Kubernetes para Azure Files][kubernetes-files]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
