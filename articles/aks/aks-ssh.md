---
title: Conexión SSH con los nodos de un clúster de Azure Kubernetes Service (AKS)
description: Creación de una conexión SSH con los nodos de un clúster de Azure Kubernetes Service (AKS)
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c2b77e558db0e323370c24b87a75357235677f7e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="ssh-into-azure-kubernetes-service-aks-cluster-nodes"></a>Conexión SSH con los nodos de un clúster de Azure Kubernetes Service (AKS)

En ocasiones, necesitará acceder a un nodo de Azure Kubernetes Service (AKS) para el mantenimiento, la recopilación de registros u otras operaciones de solución de problemas. Los nodos de Azure Kubernetes Service (AKS) no están expuestos a Internet. Utilice los pasos indicados en este documento para crear una conexión SSH con un nodo de AKS.

## <a name="get-aks-node-address"></a>Obtención de la dirección del nodo de AKS

Obtenga la dirección IP del nodo de un clúster de AKS mediante el comando `az vm list-ip-addresses`. Reemplace el nombre del grupo de recursos por el nombre de su grupo de recursos de AKS.

```console
$ az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table

VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-42032720-0  10.240.0.6
aks-nodepool1-42032720-1  10.240.0.5
aks-nodepool1-42032720-2  10.240.0.4
```

## <a name="create-ssh-connection"></a>Creación de la conexión SSH

Ejecute la imagen de contenedor `debian` y asocie a ella una sesión de terminal. El contenedor se puede usar luego para crear una sesión de SSH con cualquier nodo del clúster de AKS.

```console
kubectl run -it --rm aks-ssh --image=debian
```

Instale un cliente de SSH en el contenedor.

```console
apt-get update && apt-get install openssh-client -y
```

Abra un segundo terminal y enumere todos los pods para obtener el nombre del grupo recién creado.

```console
$ kubectl get pods

NAME                       READY     STATUS    RESTARTS   AGE
aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
```

Copie la clave SSH en el pod y reemplace el nombre del pod por el valor adecuado.

```console
kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
```

Actualice el archivo `id_rsa` para que sea de solo lectura para el usuario.

```console
chmod 0600 id_rsa
```

Ahora, cree una conexión SSH al nodo de AKS. El nombre de usuario predeterminado para un clúster de AKS es `azureuser`. Si esta cuenta se cambió en el momento de la creación del clúster, sustituya el nombre del usuario administrador adecuado.

```console
$ ssh -i id_rsa azureuser@10.240.0.6

Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

48 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Tue Feb 27 20:10:38 2018 from 167.220.99.8
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@aks-nodepool1-42032720-0:~$
```

## <a name="remove-ssh-access"></a>Eliminación del acceso SSH

Cuando haya finalizado, cierre la sesión de SSH y, a continuación, la sesión del contenedor interactiva. Esta acción elimina el pod que se usa para el acceso de SSH desde el clúster de AKS.