---
title: "Guía de inicio rápido: clúster de Azure Kubernetes para Windows"
description: "Aprenda rápidamente a crear un clúster de Kubernetes para contenedores de Windows en Azure Container Service con la CLI de Azure."
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 07/18/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: ed6e4ec438cc445645d55514c2bd51596d566af8
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="deploy-kubernetes-cluster-for-windows-containers"></a>Implementación de un clúster de Kubernetes para los contenedores de Windows

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

La CLI de Azure se usa para crear y administrar recursos de Azure desde la línea de comandos o en scripts. Esta guía ofrece información detallada sobre cómo implementar un clúster de [Kubernetes](https://kubernetes.io/docs/home/) en [Azure Container Service](../container-service-intro.md) usando la CLI de Azure. Una vez que se implemente el clúster, se conecta a él con la herramienta de línea de comandos `kubectl` de Kubernetes y se implementa el primer contenedor de Windows.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para esta guía de inicio rápido es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

> [!NOTE]
> La compatibilidad para contenedores de Windows en Kubernetes en Azure Container Service está en versión preliminar. 
>

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un grupo lógico en el que se implementan y se administran los recursos de Azure. 

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Creación de un clúster de Kubernetes
Cree un clúster de Kubernetes en Azure Container Service con el comando [az acs create](/cli/azure/acs#create). 

En el ejemplo siguiente se crea un clúster denominado *myK8sCluster* con un nodo maestro de Linux y dos nodos de agente de Windows. Este ejemplo crea claves SSH necesarias para conectar con el maestro de Linux. En este ejemplo se utiliza *azureuser* como nombre de usuario administrativo y *myPassword12* como contraseña en los nodos de Windows. Actualice estos valores a un valor apropiado para su entorno. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username azureuser \
    --admin-password myPassword12
```

Después de varios minutos, el comando se completa y muestra la información sobre la implementación.

## <a name="install-kubectl"></a>Instalación de kubectl

Para conectarse al clúster de Kubernetes desde el equipo cliente, use [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), el cliente de la línea de comandos de Kubernetes. 

Si usa Azure CloudShell, `kubectl` ya está instalado. Si desea instalarlo de forma local, puede usar el comando [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

El siguiente ejemplo de CLI de Azure instala `kubectl` en el sistema. En Windows, ejecute este comando como administrador.

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

## <a name="deploy-a-windows-iis-container"></a>Implementación de un contenedor de Windows IIS

Puede ejecutar un contenedor de Docker dentro de un *pod* de Kubernetes que contiene uno o varios contenedores. 

En este ejemplo básico se usa un archivo JSON para especificar un contenedor de Microsoft Internet Information Server (IIS) y luego se crea el pod mediante el comando `kubctl apply`. 

Cree un archivo local denominado `iis.json` y copie el texto a continuación. Este archivo indica a Kubernetes que ejecute IIS en Windows Server 2016 Nano Server, usando una imagen de contenedor pública de [Docker Hub](https://hub.docker.com/r/microsoft/iis/). El contenedor usa el puerto 80, pero inicialmente solo es accesible dentro de la red de clústeres.

 ```JSON
 {
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "iis",
    "labels": {
      "name": "iis"
    }
  },
  "spec": {
    "containers": [
      {
        "name": "iis",
        "image": "microsoft/iis:nanoserver",
        "ports": [
          {
          "containerPort": 80
          }
        ]
      }
    ],
    "nodeSelector": {
     "beta.kubernetes.io/os": "windows"
     }
   }
 }
 ```

Para iniciar el pod, escriba:
  
```azurecli-interactive
kubectl apply -f iis.json
```  

Para realizar el seguimiento de la implementación, escriba:
  
```azurecli-interactive
kubectl get pods
```

Mientras se implementa el pod, el estado es `ContainerCreating`. Puede tardar unos minutos hasta que el contenedor pase al estado `Running`.

```azurecli-interactive
NAME     READY        STATUS        RESTARTS    AGE
iis      1/1          Running       0           32s
```

## <a name="view-the-iis-welcome-page"></a>Página principal de IIS

Para exponer el pod a todo el mundo con una dirección IP pública, escriba el siguiente comando:

```azurecli-interactive
kubectl expose pods iis --port=80 --type=LoadBalancer
```

Con este comando, Kubernetes crea un servicio y una regla del equilibrador de carga de Azure con una dirección IP pública para el servicio. 

Ejecute el siguiente comando para ver el estado del servicio.

```azurecli-interactive
kubectl get svc
```

La dirección IP aparece inicialmente como `pending`. Después de unos minutos, se establece la dirección IP externa del pod `iis`:
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
iis          10.0.111.25    13.64.158.233   80/TCP         22m
```

Puede usar un explorador web de su elección para ver la página de bienvenida predeterminada de IIS en la dirección IP externa:

![Imagen de exploración hasta IIS](./media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  


## <a name="delete-cluster"></a>Eliminación de clúster
Cuando un clúster ya no se necesite, puede usar el comando [az group delete](/cli/azure/group#delete) para quitar el grupo de recursos, el servicio de contenedor y todos los recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha implementado un clúster de Kubernetes, conectado con `kubectl` y un pod con un contenedor IIS. Para más información sobre Azure Container Service, continúe con el tutorial de Kubernetes.

> [!div class="nextstepaction"]
> [Administración de un clúster de ACS Kubernetes](container-service-tutorial-kubernetes-prepare-app.md)
