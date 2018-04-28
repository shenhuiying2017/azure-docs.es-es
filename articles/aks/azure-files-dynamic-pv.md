---
title: Uso de Azure Files con AKS
description: Usar de Azure Disks con AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ab118cd43f1e3e57627d940072e50405cd85ca58
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="persistent-volumes-with-azure-files"></a>Volúmenes persistentes con archivos de Azure

Un volumen persistente representa un fragmento de almacenamiento aprovisionado para usarlo en un clúster de Kubernetes. Un volumen persistente puede usarse en uno o varios pods y puede aprovisionarse de forma dinámica o estática. En este documento se explica el aprovisionamiento dinámico de un recurso compartido de archivos de Azure como un volumen persistente de Kubernetes en un clúster de AKS.

Para más información sobre volúmenes persistentes de Kubernetes, vea el artículo sobre [volúmenes persistentes de Kubernetes][kubernetes-volumes].

## <a name="create-storage-account"></a>Crear cuenta de almacenamiento

Al realizar el aprovisionamiento dinámico de un recurso compartido de archivos de Azure como un volumen de Kubernetes, se puede usar cualquier cuenta de almacenamiento, siempre que esté incluida en el mismo grupo de recursos que el clúster de AKS. Si es necesario, cree una cuenta de almacenamiento en el mismo grupo de recursos que el clúster de AKS.

Para identificar el grupo de recursos apropiado, use el comando [az group list][az-group-list].

```azurecli-interactive
az group list --output table
```

Lo que busca es un grupo de recursos con un nombre parecido a `MC_clustername_clustername_locaton`, donde clustername es el nombre del clúster de AKS y location es la región de Azure donde se ha implementado el clúster.

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Puede utilizar el comando [az storage account create][az-storage-account-create] para crear una cuenta de almacenamiento.

En este ejemplo, actualice `--resource-group` con el nombre del grupo de recursos y `--name` con un nombre de su elección.

```azurecli-interactive
az storage account create --resource-group MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Creación de la clase de almacenamiento

Una clase de almacenamiento se utiliza para definir cómo se crea un recurso compartido de archivos de Azure. En la clase puede especificarse una cuenta de almacenamiento específica. Si no se especifica una cuenta de almacenamiento, debe especificarse `skuName` y `location`, y se evalúan en busca de alguna coincidencia todas las cuentas de almacenamiento en el grupo de recursos asociado.

Para más información sobre las clases de almacenamiento de Kubernetes para los archivos Azure, vea las [clases de almacenamiento de Kubernetes][kubernetes-storage-classes].

Cree un archivo denominado `azure-file-sc.yaml` y cópielo en el siguiente código manifiesto. Actualice `storageAccount` con el nombre de la cuenta de almacenamiento de destino.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: mystorageaccount
```

Cree la clase de almacenamiento con el comando [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Creación de una notificación de volumen persistente

Una notificación de volumen persistente (PVC) usa el objeto de clase de almacenamiento para aprovisionar de forma dinámica un recurso compartido de archivos de Azure.

El siguiente manifiesto puede utilizarse para crear una notificación de volumen persistente con un tamaño de `5GB` y con acceso `ReadWriteOnce`.

Cree un archivo denominado `azure-file-pvc.yaml` y cópielo en el siguiente código manifiesto. Asegúrese de que `storageClassName` coincide con la clase de almacenamiento creada en el último paso.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Cree la notificación del volumen persistente con el comando [kubectl create] [kubectl-create].

```azurecli-interactive
kubectl create -f azure-file-pvc.yaml
```

Una vez completado, se creará el recurso compartido de archivos. También se crea un secreto Kubernetes que contiene las credenciales y la información de conexión.

## <a name="using-the-persistent-volume"></a>Uso del volumen persistente

El siguiente manifiesto crea un pod que utiliza la notificación de volumen persistente `azurefile` para montar el recurso compartido de archivos de Azure en la ruta de acceso `/mnt/azure`.

Cree un archivo denominado `azure-pvc-files.yaml` y cópielo en el siguiente código manifiesto. Asegúrese de que `claimName` coincide con la clase PVC creada en el último paso.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

Cree el pod con el comando [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-pvc-files.yaml
```

Ahora tiene un pod en ejecución con el disco de Azure montado en el directorio `/mnt/azure`. Esta configuración puede verse al examinar el pod mediante `kubectl describe pod mypod`.

## <a name="mount-options"></a>Opciones de montaje

Los valores predeterminados fileMode y dirMode varían entre las distintas versiones de Kubernetes, tal y como se describe en la tabla siguiente.

| version | value |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v1.8.6 o posterior | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 o posterior | 0755 |

Si se usa un clúster con la versión 1.8.5 o posterior, las opciones de montaje pueden especificarse en el objeto de clase de almacenamiento. En el ejemplo siguiente se establece `0777`.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

Si se usa un clúster con las versiones 1.8.0 a 1.8.4, se puede especificar un contexto de seguridad con el valor `runAsUser` definido en `0`. Para más información sobre el contexto de seguridad de pod, vea [Configure a Security Context][kubernetes-security-context] (Configuración de un contexto de seguridad).

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre los volúmenes persistentes de Kubernetes con Azure Files.

> [!div class="nextstepaction"]
> [Complemento de Kubernetes para Azure Files][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
