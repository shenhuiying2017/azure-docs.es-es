---
title: Uso de Azure Files con AKS
description: Usar de Azure Disks con AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 04380e5e9e0f1f9b0c88fdba8d21b3e7e8e8a358
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="volumes-with-azure-files"></a>Volúmenes con archivos de Azure

Las aplicaciones que usan contenedores a menudo necesitan acceder a un volumen de datos externo y conservar datos en él. Azure Files puede utilizarse como este almacén de datos externos. En este artículo se detallan el uso de Azure Files como un volumen de Kubernetes Azure Container Service.

Para más información sobre volúmenes de Kubernetes, consulte el artículo sobre [volúmenes de Kubernetes][kubernetes-volumes].

## <a name="create-an-azure-file-share"></a>Creación de un recurso compartido de archivos de Azure

Antes de utilizar un recurso compartido de archivos de Azure como volumen de Kubernetes, es preciso crear una cuenta de Azure Storage y el recurso compartido de archivos. El script siguiente puede utilizarse para completar estas tareas. Anote o actualice los valores de los parámetros, ya que algunos son necesarios al crear el volumen de Kubernetes.

```azurecli-interactive
#!/bin/bash

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

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storgae account key: $STORAGE_KEY
```

## <a name="create-kubernetes-secret"></a>Creación de un secreto de Kubernetes

Kubernetes necesita credenciales para acceder al recurso compartido de archivos. Estas credenciales se almacenan en un [secreto de Kubernetes][kubernetes-secret], al que se hace referencia al crear un pod de Kubernetes.

Use el comando siguiente para crear el secreto. Reemplace `STORAGE_ACCOUNT_NAME` por el nombre de su cuenta de almacenamiento y `STORAGE_ACCOUNT_KEY` por su clave de almacenamiento.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=STORAGE_ACCOUNT_KEY
```

## <a name="mount-file-share-as-volume"></a>Montaje del recurso compartido de archivos como volumen

Monte el recurso compartido de Azure Files en su pod mediante la configuración del volumen en su especificación. Cree un nuevo archivo denominado `azure-files-pod.yaml` con el contenido siguiente. Actualice `aksshare` con el nombre dado al recurso compartido de Azure Files.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
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

Ahora tiene un contenedor en ejecución con el recurso compartido de Azure Files montado en el directorio `/mnt/azure`.  Puede ver el montaje del volumen al inspeccionar el pod a través de `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>Pasos siguientes

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
