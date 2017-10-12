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
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: bb528c424bc71f0309439e741c30e16d0d13c7d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="scale-kubernetes-pods-and-kubernetes-infrastructure"></a>Escalado de pods de Kubernetes e infraestructura de Kubernetes

Si ha ido siguiendo los tutoriales, tiene un clúster de Kubernetes en funcionamiento en Azure Container Service y ha implementado la aplicación Azure Voting. 

En este tutorial, la parte cinco de siete, se escalan horizontalmente los pods en la aplicación y se prueba el escalado automático de pods. También se aprende a escalar el número de nodos de agente de VM de Azure para cambiar la capacidad del clúster de hospedar cargas de trabajo. Las tareas completadas incluyen:

> [!div class="checklist"]
> * Escalado manual de pods de Kubernetes
> * Configuración de pods de Autoscale que se ejecuten en el front-end de la aplicación
> * Escalado de los nodos de agente de Azure para Kubernetes

En tutoriales posteriores se actualiza la aplicación Azure Vote y se configura Operations Management Suite para supervisar el clúster de Kubernetes.

## <a name="before-you-begin"></a>Antes de empezar

En tutoriales anteriores se ha empaquetado una aplicación en una imagen de contenedor, se ha cargado esta imagen en Azure Container Registry y se ha creado un clúster de Kubernetes. La aplicación se ejecutó después en el clúster de Kubernetes. 

Si no ha realizado estos pasos y quiere continuar, vuelva al [paso 1 del tutorial: creación de imágenes de contenedor](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="manually-scale-pods"></a>Escalado manual de pods

Hasta el momento se han implementado el front-end de Azure Vote y la instancia de Redis, cada uno con una única réplica. Para comprobarlo, ejecute el comando [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```azurecli-interactive
kubectl get pods
```

Salida:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Cambie manualmente el número de pods en la implementación de `azure-vote-front` mediante el comando [kubectl scale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#scale). En este ejemplo el número se aumenta a cinco.

```azurecli-interactive
kubectl scale --replicas=5 deployment/azure-vote-front
```

Ejecute [kubectl get pods](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) para comprobar que Kubernetes crea los pods. Tras aproximadamente un minuto, se ejecutan los pods adicionales:

```azurecli-interactive
kubectl get pods
```

Salida:

```bash
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Escalado automático de pods

Kubernetes admite el [escalado horizontal automático de pods](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) para ajustar el número de pods en una implementación en función del uso de la CPU o de otras métricas de selección. 

Para usar el escalador automático, los pods deben tener solicitudes de la CPU y límites definidos. En la implementación de `azure-vote-front`, el contenedor del front-end solicita 0,25 CPU, con un límite de 0,5 CPU. La configuración tiene el siguiente aspecto:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

En el ejemplo siguiente se usa el comando [kubectl autoscale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#autoscale) para escalar automáticamente el número de pods en la implementación de `azure-vote-front`. En este contexto, si el uso de la CPU supera el 50 %, el escalador automático aumenta el número de pods a un máximo de 10.


```azurecli-interactive
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Para ver el estado del escalador automático, ejecute el siguiente comando.

```azurecli-interactive
kubectl get hpa
```

Salida:

```bash
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Después de unos minutos con carga mínima en la aplicación Azure Vote, el número de réplicas del pod se reduce automáticamente a tres.

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

