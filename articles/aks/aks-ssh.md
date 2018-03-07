---
title: "Conexión SSH con los nodos de un clúster de Azure Container Service (AKS)"
description: "Creación de una conexión SSH con los nodos de un clúster de Azure Container Service (AKS)"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/28/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 00affc3d1c02c477826261aeac6e092934037e81
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>Conexión SSH con los nodos de un clúster de Azure Container Service (AKS)

En ocasiones, necesitará tener acceso a un nodo de Azure Container Service (AKS) para el mantenimiento, la recopilación de registros u otras operaciones de solución de problemas. Los nodos de Azure Container Service (AKS) no están expuestos a Internet. Utilice los pasos indicados en este documento para crear una conexión SSH con un nodo de AKS.

## <a name="configure-ssh-access"></a>Configuración del acceso SSH

 Para conectarse mediante SSH con un nodo específico, se crea un pod con acceso `hostNetwork`. También se crea un servicio para el acceso del pod. Esta configuración tiene privilegios y debe eliminarse después de su uso.

Cree un archivo llamado `aks-ssh.yaml` y copie este manifiesto. Actualice el nombre del nodo con el nombre del nodo de AKS de destino.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-ssh
spec:
  selector:
    app: aks-ssh
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 22
    targetPort: 22
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aks-ssh
  labels:
    app: aks-ssh
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-ssh
  template:
    metadata:
      labels:
        app: aks-ssh
    spec:
      containers:
      - name: alpine
        image: alpine:latest
        ports:
        - containerPort: 22
        command: ["/bin/sh", "-c", "--"]
        args: ["while true; do sleep 30; done;"]
      hostNetwork: true
      nodeName: aks-nodepool1-42032720-0
```

Ejecute el manifiesto para crear el servicio y el pod.

```azurecli-interactive
$ kubectl apply -f aks-ssh.yaml
```

Obtenga la dirección IP externa del servicio expuesto. La configuración de la dirección IP puede tardar un minuto en finalizar. 

```azurecli-interactive
$ kubectl get service

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
kubernetes         ClusterIP      10.0.0.1      <none>          443/TCP        1d
aks-ssh            LoadBalancer   10.0.51.173   13.92.154.191   22:31898/TCP   17m
```

Cree la conexión SSH. 

El nombre de usuario predeterminado para un clúster de AKS es `azureuser`. Si esta cuenta se cambió en el momento de la creación del clúster, sustituya el nombre del usuario administrador adecuado. 

Si la clave no está en `~/ssh/id_rsa`, proporcione la ubicación correcta mediante el argumento `ssh -i`.

```azurecli-interactive
$ ssh azureuser@13.92.154.191

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

Cuando haya finalizado, elimine el pod y el servicio de acceso SSH.

```azurecli-interactive
kubectl delete -f aks-ssh.yaml
```