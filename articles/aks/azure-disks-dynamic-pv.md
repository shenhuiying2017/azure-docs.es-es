---
title: Uso de discos de Azure con AKS
description: Usar de Azure Disks con AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/25/2018
ms.author: nepeters
ms.openlocfilehash: e1f5b68d5d39dd846ebec525d1e83a6c0ef4971a
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2018
---
# <a name="persistent-volumes-with-azure-disks---dynamic-provisioning"></a>Volúmenes persistentes con discos de Azure: aprovisionamiento dinámico

Un volumen persistente representa un fragmento de almacenamiento aprovisionado para usarlo en un clúster de Kubernetes. Un volumen persistente puede usarse en uno o varios pods y puede aprovisionarse de forma dinámica o estática. En este documento se explica el aprovisionamiento dinámico de un disco de Azure como un volumen persistente de Kubernetes en un clúster de AKS. 

Para más información sobre volúmenes persistentes de Kubernetes, vea el artículo sobre [volúmenes persistentes de Kubernetes][kubernetes-volumes].

## <a name="built-in-storage-classes"></a>Clases de almacenamiento integradas

Una clase de almacenamiento se utiliza para definir cómo se configura un volumen persistente creado de forma dinámica. Para más información sobre las clases de almacenamiento de Kubernetes, vea las [clases de almacenamiento de Kubernetes][kubernetes-storage-classes].

Cada clúster de AKS incluye dos clases de almacenamiento creadas previamente y configuradas para funcionar con discos de Azure. Use el comando `kubectl get storageclass` para verlos.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

Si estas clases de almacenamiento se ajustan sus necesidades, no es necesario crear una.

## <a name="create-storage-class"></a>Creación de la clase de almacenamiento

Si desea crear una clase de almacenamiento configurada para discos de Azure, puede hacerlo mediante el siguiente manifiesto de ejemplo. 

El valor `storageaccounttype` de `Standard_LRS` indica que se ha creado un disco estándar. Este valor puede cambiarse a `Premium_LRS` para crear un [disco Premium][premium-storage]. Para usar discos Premium, la máquina virtual del nodo de AKS debe tener un tamaño compatible con los discos Premium. Vea [este documento][premium-storage] para obtener una lista de tamaños compatibles.

```yaml
apiVersion: storage.k8s.io/v1beta1
kind: StorageClass
metadata:
  name: azure-managed-disk
provisioner: kubernetes.io/azure-disk
parameters:
  kind: Managed
  storageaccounttype: Standard_LRS
```

## <a name="create-persistent-volume-claim"></a>Creación de una notificación de volumen persistente

Una notificación de volumen persistente usa un objeto de clase de almacenamiento para aprovisionar un fragmento de almacenamiento de forma dinámica. Al utilizar un disco de Azure, este se crea en el mismo grupo de recursos que los recursos de AKS.

Este manifiesto de ejemplo crea una notificación de volumen persistente utilizando la clase de almacenamiento `azure-managed-disk` para crear un disco con un tamaño de `5GB` y con acceso `ReadWriteOnce`. Para más información sobre los modos de acceso PVC, vea [Access Modes][access-modes] (Modos de acceso).

> [!NOTE]
> Solo se puede montar un disco de Azure con el tipo de modo de acceso ReadWriteOnce, que lo pone a disposición de un único nodo de AKS. Si necesita compartir un volumen persistente en varios nodos, considere la posibilidad de usar [Azure Files][azure-files-pvc]. 

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

## <a name="using-the-persistent-volume"></a>Uso del volumen persistente

Una vez que se haya creado la notificación de volumen persistente y tras el aprovisionamiento correcto del almacenamiento, se puede crear un pod con acceso al disco. El siguiente manifiesto crea un pod que utiliza la notificación de volumen persistente `azure-managed-disk` para montar el disco de Azure en la ruta de acceso `/var/www/html`. 

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
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>pasos siguientes

Obtenga más información sobre los volúmenes persistentes de Kubernetes con discos de Azure.

> [!div class="nextstepaction"]
> [Complemento de Kubernetes para discos de Azure][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md