---
title: Creación de un equilibrador de carga interno de Azure Kubernetes Service (AKS)
description: Uso de un equilibrador de carga interno con Azure Kubernetes Service (AKS).
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 3/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a1a5d6d455086ee34767e92b277936840717bcd6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33933780"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Uso de un equilibrador de carga interno con Azure Kubernetes Service (AKS)

El equilibrio de carga interno permite que un servicio de Kubernetes sea accesible para las aplicaciones que se ejecutan en la misma red virtual que el clúster de Kubernetes. En este documento se detalla la creación de un equilibrador de carga interno con Azure Kubernetes Service (AKS).

## <a name="create-internal-load-balancer"></a>Creación del equilibrador de carga interno

Para crear un equilibrador de carga interno, genere un manifiesto de servicio con el tipo de servicio `LoadBalancer` y la anotación `azure-load-balancer-internal`, como se muestra en el ejemplo siguiente.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Una vez implementado, se crea un equilibrador de carga de Azure y se pone a disposición en la misma red virtual que el clúster de AKS.

![Imagen del equilibrador de carga interno de AKS](media/internal-lb/internal-lb.png)

Al recuperar los detalles del servicio, la dirección IP de la columna `EXTERNAL-IP` es la dirección IP del equilibrador de carga interno.

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>Especificación de una dirección IP

Si quiere usar una dirección IP específica con el equilibrador de carga interno, agregue la propiedad `loadBalancerIP` a la especificación del equilibrador de carga. La dirección IP especificada debe encontrarse en la misma subred que el clúster de AKS y no se debe haber asignado ya a un recurso.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Al recuperar los detalles del servicio, la dirección IP de `EXTERNAL-IP` debe reflejar la dirección IP especificada.

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="delete-the-load-balancer"></a>Eliminación del equilibrador de carga

Cuando se han eliminado todos los servicios con el equilibrador de carga interno, también se elimina el propio equilibrador de carga.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre los servicios de Kubernetes en la [documentación de servicios de Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/