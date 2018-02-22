---
title: "Uso de una dirección IP estática con el equilibrador de carga de Azure Container Service (AKS)"
description: "Use una dirección IP estática con el equilibrador de carga de Azure Container Service (AKS)."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 945132dd5f7e51f05ceda89a9cb16315aabbda8a
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="use-a-static-ip-address-with-the-azure-container-service-aks-load-balancer"></a>Uso de una dirección IP estática con el equilibrador de carga de Azure Container Service (AKS)

En algunos casos, como cuando se vuelve a crear el equilibrador de carga de Azure Container Service (AKS) o se vuelven a crear servicios de Kubernetes con el tipo LoadBalancer, puede cambiar la dirección IP pública del servicio de Kubernetes. En este documento se describe la configuración de una dirección IP estática para los servicios de Kubernetes.

## <a name="create-static-ip-address"></a>Creación de una dirección IP estática

Cree una dirección IP estática pública para el servicio de Kubernetes. La dirección IP debe crearse en el grupo de recursos que se creó automáticamente durante la implementación del clúster. Para obtener información acerca de los diferentes grupos de recursos de AKS y sobre cómo identificar el grupo de recursos creado de forma automática, consulte las [preguntas más frecuentes sobre AKS][aks-faq-resource-group].

Use el comando [az network public ip create][az-network-public-ip-create] para crear una dirección IP.

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGRoup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

Anote la dirección IP.

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    "ipConfiguration": null,
    "ipTags": [],
    "location": "eastus",
    "name": "myAKSPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Static",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "56ec8760-a3b8-4aeb-a89d-42e68d2cbc8c",
    "sku": {
      "name": "Basic"
    },
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses",
    "zones": null
  }
````

 Si es necesario, se puede recuperar la dirección mediante el comando [az network public-ip list][az-network-public-ip-list].

```console
$ az network public-ip list --resource-group MC_myResourceGRoup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-service-with-ip-address"></a>Creación del servicio con dirección IP

Una vez que se ha proporcionado la dirección IP estática, puede crearse un servicio de Kubernetes con la propiedad `loadBalancerIP` y un valor de la dirección IP estática.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="troubleshooting"></a>solución de problemas

Si la dirección IP estática no se ha creado o se ha creado en un grupo de recursos incorrecto, se produce un error en la creación del servicio. Para solucionar los problemas, devuelva los eventos de creación del servicio con el comando [kubectl describe][kubectl-describe].

```console
$ kubectl describe service azure-vote-front

Name:                     azure-vote-front
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-vote-front
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-vote-front: user supplied IP Address 40.121.183.52 was not found
```

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/ 

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list