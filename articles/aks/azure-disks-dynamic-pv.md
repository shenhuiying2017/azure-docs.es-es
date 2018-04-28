---
title: Uso de discos de Azure con AKS
description: Usar de Azure Disks con AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: a6bc79d0556299634a78c5232bbab4e20810172c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="persistent-volumes-with-azure-disks"></a>Volúmenes persistentes con discos de Azure

Un volumen persistente representa un fragmento de almacenamiento aprovisionado para su uso con pods de Kubernetes. Un volumen persistente puede usarse en uno o varios pods y puede aprovisionarse de forma dinámica o estática. Para más información sobre volúmenes persistentes de Kubernetes, vea el artículo sobre [volúmenes persistentes de Kubernetes][kubernetes-volumes].

En este documento se detalla el uso de los volúmenes persistentes con discos de Azure en un clúster de Azure Container Service (AKS).

> [!NOTE]
> Solo se puede montar un disco de Azure con el tipo de modo de acceso ReadWriteOnce, que lo pone a disposición de un único nodo de AKS. Si necesita compartir un volumen persistente en varios nodos, considere la posibilidad de usar [Azure Files][azure-files-pvc].

## <a name="built-in-storage-classes"></a>Clases de almacenamiento integradas

Una clase de almacenamiento se usa para definir cómo se crea dinámicamente una unidad de almacenamiento con un volumen persistente. Para más información sobre las clases de almacenamiento de Kubernetes, vea las [clases de almacenamiento de Kubernetes][kubernetes-storage-classes].

Cada clúster de AKS incluye dos clases de almacenamiento creadas previamente y configuradas para funcionar con discos de Azure. La clase de almacenamiento `default` aprovisiona un disco de Azure estándar. La clase de almacenamiento `managed-premium` aprovisiona un disco de Azure premium. Si los nodos de AKS del clúster usan almacenamiento premium, seleccione la clase `managed-premium`.

Use el comando [kubectl get sc][kubectl-get] para ver las clases de almacenamiento que se crearon previamente.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

## <a name="create-persistent-volume-claim"></a>Creación de una notificación de volumen persistente

Una notificación de volumen persistente (PVC) se usa para aprovisionar automáticamente el almacenamiento en función de una clase de almacenamiento. En ese caso, una PVC puede usar una de las clases de almacenamiento que crearon previamente para crear un disco administrado de Azure estándar o premium.

Cree un archivo denominado `azure-premimum.yaml` y cópielo en el siguiente manifiesto.

Tenga en cuenta que la clase de almacenamiento `managed-premium` se especifica en la anotación y que la notificación solicita un disco con `5GB` de tamaño con acceso de `ReadWriteOnce`.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: managed-premium
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

Cree la notificación del volumen persistente con el comando [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-premimum.yaml
```

## <a name="using-the-persistent-volume"></a>Uso del volumen persistente

Una vez que se haya creado la notificación de volumen persistente y tras el aprovisionamiento correcto del almacenamiento, se puede crear un pod con acceso al disco. El siguiente manifiesto crea un pod que utiliza la notificación de volumen persistente `azure-managed-disk` para montar el disco de Azure en la ruta de acceso `/mnt/azure`.

Cree un archivo denominado `azure-pvc-disk.yaml` y cópielo en el siguiente código manifiesto.

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
        claimName: azure-managed-disk
```

Cree el pod con el comando [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-pvc-disk.yaml
```

Ahora tiene un pod en ejecución con el disco de Azure montado en el directorio `/mnt/azure`. Esta configuración puede verse al examinar el pod mediante `kubectl describe pod mypod`.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre los volúmenes persistentes de Kubernetes con discos de Azure.

> [!div class="nextstepaction"]
> [Complemento de Kubernetes para discos de Azure][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md