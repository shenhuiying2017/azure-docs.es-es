---
title: Uso de Azure Files con AKS
description: Usar de Azure Disks con AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/04/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4873b98c8ba4f1e574be20baebef3b6860341529
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="persistent-volumes-with-azure-files---dynamic-provisioning"></a>Aprovisionamiento dinámico de volúmenes persistentes con archivos de Azure

Un volumen persistente representa un fragmento de almacenamiento aprovisionado para usarlo en un clúster de Kubernetes. Un volumen persistente puede usarse en uno o varios pods y puede aprovisionarse de forma dinámica o estática. En este documento se explica el aprovisionamiento dinámico de un recurso compartido de archivos de Azure como un volumen persistente de Kubernetes en un clúster de AKS. 

Para más información sobre volúmenes persistentes de Kubernetes, vea el artículo sobre [volúmenes persistentes de Kubernetes][kubernetes-volumes].

## <a name="prerequisites"></a>Requisitos previos

Al realizar el aprovisionamiento dinámico de un recurso compartido de archivos de Azure como un volumen de Kubernetes, se puede usar cualquier cuenta de almacenamiento, siempre que esté incluida en el mismo grupo de recursos que el clúster de AKS. Si es necesario, cree una cuenta de almacenamiento en el mismo grupo de recursos que el clúster de AKS. 

Para identificar el grupo de recursos apropiado, use el comando [az group list][az-group-list].

```azurecli-interactive
az group list --output table
```

En el resultado del ejemplo siguiente se muestran los grupos de recursos, ambos asociados con un clúster de AKS. El grupo de recursos con un nombre como *MC_myAKSCluster_myAKSCluster_eastus* contiene los recursos de clúster de AKS y es donde se debe crear la cuenta de almacenamiento. 

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Una vez identificado el grupo de recursos, cree la cuenta de almacenamiento con el comando [az storage account create][az-storage-account-create].

```azurecli-interactive
az storage account create --resource-group  MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Creación de la clase de almacenamiento

Una clase de almacenamiento se utiliza para definir cómo se configura un volumen persistente creado de forma dinámica. Los elementos como la cuenta de almacenamiento de Azure, la SKU y la región se definen en el objeto de clase de almacenamiento. Para más información sobre las clases de almacenamiento de Kubernetes, vea las [clases de almacenamiento de Kubernetes][kubernetes-storage-classes].

En el ejemplo siguiente se especifica que se puede usar cualquier cuenta de almacenamiento del tipo de SKU `Standard_LRS` en la región `eastus` al solicitar almacenamiento. 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  skuName: Standard_LRS
```

Para usar una cuenta de almacenamiento específica, se puede usar el parámetro `storageAccount`.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: azure_storage_account_name
```

## <a name="create-persistent-volume-claim"></a>Creación de una notificación de volumen persistente

Una notificación de volumen persistente usa el objeto de clase de almacenamiento para aprovisionar un fragmento de almacenamiento de forma dinámica. Al usar una instancia de Azure Files, se crea un recurso compartido de archivos de Azure en la cuenta de almacenamiento seleccionada o especificada en el objeto de clase de almacenamiento.

>  [!NOTE]
>   Asegúrese de que una cuenta de almacenamiento adecuada se ha creado previamente en el mismo grupo de recursos en que se encuentran los recursos de clúster de AKS. Este grupo de recursos tiene un nombre como *MC_myAKSCluster_myAKSCluster_eastus*. La notificación de volumen persistente generará un error de aprovisionamiento del recurso compartido de archivos de Azure si no se encuentra disponible ninguna cuenta de almacenamiento. 

El siguiente manifiesto puede utilizarse para crear una notificación de volumen persistente con un tamaño de `5GB` y con acceso `ReadWriteOnce`. Para más información sobre los modos de acceso PVC, vea [Access Modes][access-modes] (Modos de acceso).

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

## <a name="using-the-persistent-volume"></a>Uso del volumen persistente

Una vez creada la notificación de volumen persistente y tras el aprovisionamiento correcto del almacenamiento, se puede crear un pod con acceso al volumen. El siguiente manifiesto crea un pod que utiliza la notificación de volumen persistente `azurefile` para montar el recurso compartido de archivos de Azure en la ruta de acceso `/var/www/html`. 

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
      - mountPath: "/var/www/html"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

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

## <a name="next-steps"></a>pasos siguientes

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
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create