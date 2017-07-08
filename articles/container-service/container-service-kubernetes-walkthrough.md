---
title: "Guía de inicio rápido: clúster de Azure Kubernetes para Linux | Microsoft Docs"
description: "Aprenda rápidamente a crear un clúster de Kubernetes para contenedores de Linux en Azure Container Service con la CLI de Azure."
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 25043f6bf5e5ab3def8563bd2c096b79706bfec1
ms.contentlocale: es-es
ms.lasthandoff: 06/20/2017

---

# <a name="deploy-kubernetes-cluster-for-linux-containers"></a>Implementación de un clúster de Kubernetes para los contenedores de Linux

La CLI de Azure se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. Esta guía ofrece información detallada sobre cómo implementar un clúster de [Kubernetes](https://kubernetes.io/docs/home/) en [Azure Container Service](container-service-intro.md) usando la CLI de Azure. Una vez que se implemente el clúster, se conecta a él con la herramienta de línea de comandos `kubectl` de Kubernetes y se implementa el primer contenedor de Linux.

Para realizar este tutorial es necesaria la versión 2.0.4 o superior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure. 

Inicie sesión en la suscripción de Azure con el comando [az login](/cli/azure/#login) y siga las instrucciones de la pantalla.

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un grupo lógico en el que se implementan y se administran los recursos de Azure. 

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Creación de un clúster de Kubernetes
Cree un clúster de Kubernetes en Azure Container Service con el comando [az acs create](/cli/azure/acs#create). 

En el ejemplo siguiente se crea un clúster denominado *myK8sCluster* con un nodo maestro de Linux y dos nodos de agente de Linux. Este ejemplo crea claves SSH si aún no existen en las ubicaciones predeterminadas. Para utilizar un conjunto específico de claves, utilice la opción `--ssh-key-value`. Actualice el nombre del clúster a un valor apropiado para su entorno. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --generate-ssh-keys 
```

Después de varios minutos, el comando se completa y muestra la información sobre la implementación.

## <a name="install-kubectl"></a>Instalación de kubectl

Para conectarse al clúster de Kubernetes desde el equipo cliente, use [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), el cliente de la línea de comandos de Kubernetes. 

Si usa Azure CloudShell, `kubectl` ya está instalado. Si desea instalarlo de forma local, puede usar el comando [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

El siguiente ejemplo de CLI de Azure instala `kubectl` en el sistema. Si está ejecutando la CLI de Azure en Mac OS o Linux, tendrá que ejecutar el comando con `sudo`.

```azurecli-interactive 
az acs kubernetes install-cli 
```

## <a name="connect-with-kubectl"></a>Conexión con kubectl

Para configurar `kubectl` para conectarse al clúster de Kubernetes, ejecute el comando [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). En el ejemplo siguiente se descarga la configuración del clúster para el clúster de Kubernetes.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Para comprobar la conexión al clúster desde el equipo, ejecute:

```azurecli-interactive
kubectl get nodes
```

`kubectl` muestra los nodos maestro y de agente.

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```


## <a name="deploy-an-nginx-container"></a>Implementación de un contenedor NGINX

Puede ejecutar un contenedor de Docker dentro de un *pod* de Kubernetes que contiene uno o varios contenedores. 

Este comando inicia el contenedor de NGINX de Docker en un pod de Kubernetes en uno de los nodos. En este caso, el contenedor ejecuta el servidor de web NGINX extraído de una imagen en [Docker Hub](https://hub.docker.com/_/nginx/).

```azurecli-interactive
kubectl run nginx --image nginx
```
Para ver si el contenedor se está ejecutando, ejecute:

```azurecli-interactive
kubectl get pods
```

## <a name="view-the-nginx-welcome-page"></a>Visualización de la página de bienvenida de NGINX
Para exponer el servidor NGINX a todo el mundo con una dirección IP pública, escriba el siguiente comando:

```azurecli-interactive
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Con este comando, Kubernetes crea un servicio y una [regla del equilibrador de carga de Azure](container-service-kubernetes-load-balancing.md) con una dirección IP pública para el servicio. 

Ejecute el siguiente comando para ver el estado del servicio.

```azurecli-interactive
kubectl get svc
```

La dirección IP aparece inicialmente como `pending`. Después de unos minutos, se establece la dirección IP externa del servicio:
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
nginx        10.0.111.25    52.179.3.96     80/TCP         22m
```

Puede usar un explorador web de su elección para ver la página de bienvenida predeterminada de NGINX en la dirección IP externa:

![Imagen de desplazamiento a Nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


## <a name="delete-cluster"></a>Eliminación de clúster
Cuando un clúster ya no se necesite, puede usar el comando [az group delete](/cli/azure/group#delete) para quitar el grupo de recursos, el servicio de contenedor y todos los recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha implementado un clúster de Kubernetes, conectado con `kubectl` y un pod con un contenedor NGINX. Para más información sobre Azure Container Service, continúe con el tutorial de clúster de Kubernetes.

> [!div class="nextstepaction"]
> [Administración de un clúster de ACS Kubernetes](./container-service-tutorial-kubernetes-prepare-app.md)

