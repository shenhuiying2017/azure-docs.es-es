---
title: "Tutorial de Azure Container Service: escalado de una aplicación | Microsoft Docs"
description: "Tutorial de Azure Container Service: escalado de una aplicación"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Kubernetes, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2017
ms.author: danlep
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: e72e33e4c8511ab3daa0b9ccbefb5fde887bc0f9
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017

---

# <a name="scale-kubernetes-pods-and-kubernetes-infrastructure"></a>Escalado de pods de Kubernetes e infraestructura de Kubernetes

Si ha ido siguiendo los tutoriales, tiene un clúster de Kubernetes en funcionamiento en Azure Container Service y ha implementado la aplicación de Azure Voting. 

En este tutorial, se escalan horizontalmente los pods en la aplicación y se prueba el escalado automático de pods. También se aprende a escalar el número de nodos de agente para cambiar la capacidad del clúster para hospedar cargas de trabajo. Las tareas completadas incluyen:

> [!div class="checklist"]
> * Escalado manual de pods de Kubernetes
> * Configuración de pods de Autoscale que se ejecuten en el front-end de la aplicación
> * Escalado de los nodos de agente de Azure para Kubernetes

## <a name="before-you-begin"></a>Antes de empezar

En anteriores tutoriales, se empaquetó una aplicación en imágenes de contenedor, se cargaron estas imágenes en Azure Container Registry y se creó un clúster de Kubernetes. La aplicación se ejecutó después en el clúster de Kubernetes. Si no ha realizado estos pasos y quiere continuar, vuelva al [paso 1 del tutorial: creación de imágenes de contenedor](./container-service-tutorial-kubernetes-prepare-app.md). 

Como mínimo, este tutorial requiere un clúster de Kubernetes con una aplicación en ejecución.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="manually-scale-pods"></a>Escalado manual de pods

En el tutorial anterior se implementó el front-end y el back-end de Azure Vote, cada uno en un solo pod. Para comprobarlo, ejecute el comando [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```bash
kubectl get pods
```

La salida es similar a la siguiente:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Cambie manualmente el número de pods en la implementación de `azure-vote-front` mediante el comando [kubectl scale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#scale). En este ejemplo, el número se aumenta a 4:

```bash
kubectl scale --replicas=5 deployment/azure-vote-front
```

Ejecute [kubectl get pods](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) para comprobar que Kubernetes crea los pods. Tras aproximadamente un minuto, se ejecutan los pods adicionales:

```bash
kubectl get pods
```

Salida:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          33m
azure-vote-front-848767080-1kt72   1/1       Running   0          1m
azure-vote-front-848767080-2b62d   1/1       Running   0          1m
azure-vote-front-848767080-78rf0   1/1       Running   0          1m
azure-vote-front-848767080-tf34m   1/1       Running   0          33m
```

## <a name="autoscale-pods"></a>Escalado automático de pods

Kubernetes admite el [escalado horizontal automático de pods](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) para ajustar el número de pods en una implementación en función del uso de la CPU o de otras métricas. 

Para usar el escalador automático, los pods deben tener solicitudes de la CPU y límites definidos. En la implementación de `azure-vote-front`, cada contenedor solicita 0,25 de la CPU, con un límite de CPU de 0,5. La configuración tiene el siguiente aspecto:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

En el ejemplo siguiente se usa el comando [kubectl autoscale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#autoscale) para escalar automáticamente el número de pods en la implementación de `azure-vote-front`. En este contexto, si el uso de la CPU supera el 50 %, el escalador automático aumenta el número de pods a un máximo de 10.


```bash
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Para ver el estado del escalador automático, ejecute el siguiente comando.

```bash
kubectl get hpa
```

Salida:

```bash
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Después de unos minutos con carga mínima en la aplicación de Azure Vote, el número de réplicas del pod se reduce automáticamente a 3.

## <a name="scale-the-agents"></a>Escalado de los agentes

Si en el tutorial anterior ha creado el clúster de Kubernetes mediante comandos predeterminados, este tiene tres nodos de agente. Puede ajustar el número de pods manualmente si tiene previsto usar más o menos cargas de trabajo en el clúster. Use el comando [az acs scale](/cli/azure/acs#scale) y especifique el número de agentes con el parámetro `--new-agent-count`.

En el ejemplo siguiente, el número de nodos de agente se aumenta a 4 en el clúster de Kubernetes denominado *myK8sCluster*. El comando tarda unos minutos en completarse.

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

La salida del comando muestra el número de nodos de agente en el valor de `agentPoolProfiles:count`:

```azurecli
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se han usado distintas características de escalado en el clúster de Kubernetes. Las tareas tratadas incluyen:

> [!div class="checklist"]
> * Escalado manual de pods de Kubernetes
> * Configuración de pods de Autoscale que se ejecuten en el front-end de la aplicación
> * Escalado de los nodos de agente de Azure para Kubernetes

Vaya al siguiente tutorial para aprender a actualizar la aplicación en Kubernetes.

> [!div class="nextstepaction"]
> [Actualización de una aplicación en Kubernetes](./container-service-tutorial-kubernetes-app-update.md)


