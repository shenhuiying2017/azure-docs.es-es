---
title: "Integración con los servicios administrados de Azure mediante Open Service Broker for Azure (OSBA)"
description: "Integración con los servicios administrados de Azure mediante Open Service Broker for Azure (OSBA)"
services: container-service
author: sozercan
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 12/05/2017
ms.author: seozerca
ms.openlocfilehash: 339e600f18613e8cf4e5529c759ad33076d48654
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="integrate-with-azure-managed-services-using-open-service-broker-for-azure-osba"></a>Integración con los servicios administrados de Azure mediante Open Service Broker for Azure (OSBA)

Junto con el [catálogo de servicios de Kubernetes][kubernetes-service-catalog], Open Service Broker para Azure (OSBA) permite a los desarrolladores usar servicios administrados de Azure en Kubernetes. Esta guía se centra en la implementación del catálogo de servicios de Kubernetes, Open Service Broker for Azure (OSBA) y las aplicaciones que utilizan servicios administrados de Azure mediante Kubernetes.

## <a name="prerequisites"></a>Requisitos previos
* Una suscripción de Azure

* CLI de Azure 2.0: puede [instalarla localmente][azure-cli-install] o usarla en [Azure Cloud Shell][azure-cloud-shell].

* CLI 2.7+ de Helm: puede [instalarla localmente][helm-cli-install] o usarla en [Azure Cloud Shell][azure-cloud-shell].

* Permisos para crear una entidad de servicio con el rol de Colaborador en la suscripción de Azure

* Un clúster de Azure Container Service (AKS) existente. Si necesita un clúster de AKS, siga la guía de inicio rápido [Creación de un clúster de AKS][create-aks-cluster].

## <a name="install-service-catalog"></a>Instalación del catálogo de servicios

El primer paso consiste en instalar el catálogo de servicios en el clúster de Kubernetes mediante un gráfico de Helm. Actualice la instalación de Tiller (servidor de Helm) en el clúster con:

```azurecli-interactive
helm init --upgrade
```

Ahora, agregue el gráfico del catálogo de servicios al repositorio de Helm:

```azurecli-interactive
helm repo add svc-cat https://svc-catalog-charts.storage.googleapis.com
```

Por último, instale el catálogo de servicios con el gráfico de Helm:

```azurecli-interactive
helm install svc-cat/catalog --name catalog --namespace catalog --set rbacEnable=false
```

Una vez se ha ejecutado el gráfico de Helm, compruebe que `servicecatalog` aparece en la salida del comando siguiente:

```azurecli-interactive
kubectl get apiservice
```

Por ejemplo, debería ver una salida similar a la del siguiente ejemplo (aquí se muestra truncado):

```
NAME                                 AGE
v1.                                  10m
v1.authentication.k8s.io             10m
...
v1beta1.servicecatalog.k8s.io        34s
v1beta1.storage.k8s.io               10
```

## <a name="install-open-service-broker-for-azure"></a>Instalación de Open Service Broker para Azure

El paso siguiente consiste en instalar [Open Service Broker para Azure][open-service-broker-azure], que incluye el catálogo para los servicios administrados de Azure. Algunos ejemplos de servicios de Azure disponibles son Azure Database for PostgreSQL, Azure Redis Cache, Azure Database for MySQL, Azure Cosmos DB, Azure SQL Database y otros.

Puede empezar agregando Open Service Broker para el repositorio de Azure Helm:

```azurecli-interactive
helm repo add azure https://kubernetescharts.blob.core.windows.net/azure
```

Cree una [entidad de servicio][create-service-principal] con el siguiente comando de la CLI de Azure:

```azurecli-interactive
az ad sp create-for-rbac
```

La salida debe ser similar a la siguiente: Tome nota de los valores `appId`, `password` y `tenant`, que se utilizan en el paso siguiente.

```JSON
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

Establezca las siguientes variables de entorno con los valores anteriores:

```azurecli-interactive
AZURE_CLIENT_ID=<appId>
AZURE_CLIENT_SECRET=<password>
AZURE_TENANT_ID=<tenant>
```

Ahora obtenga el identificador de la suscripción a Azure:

```azurecli-interactive
az account show --query id --output tsv
```

De nuevo, establezca la siguiente variable de entorno con el valor anterior:

```azurecli-interactive
AZURE_SUBSCRIPTION_ID=[your Azure subscription ID from above]
```

Ahora que ha rellenado estas variables de entorno, ejecute el siguiente comando para instalar Open Service Broker para Azure mediante el gráfico de Helm:

```azurecli-interactive
helm install azure/open-service-broker-azure --name osba --namespace osba \
    --set azure.subscriptionId=$AZURE_SUBSCRIPTION_ID \
    --set azure.tenantId=$AZURE_TENANT_ID \
    --set azure.clientId=$AZURE_CLIENT_ID \
    --set azure.clientSecret=$AZURE_CLIENT_SECRET
```

Una vez completada la implementación de OSBA, instale la [CLI del catálogo de servicios][service-catalog-cli], una interfaz de línea de comandos fácil de usar para realizar consultas de Service Broker, clases de servicio, planes de servicio y mucho más.

Ejecute los comandos siguientes para instalar la CLI binaria del catálogo de servicios:

```azurecli-interactive
curl -sLO https://servicecatalogcli.blob.core.windows.net/cli/latest/$(uname -s)/$(uname -m)/svcat
chmod +x ./svcat
```

Ahora, se enumeran los Service Brokers instalados:

```azurecli-interactive
./svcat get brokers
```

Debería ver una salida similar a la siguiente:

```
  NAME                               URL                                STATUS
+------+--------------------------------------------------------------+--------+
  osba   http://osba-open-service-broker-azure.osba.svc.cluster.local   Ready
```

A continuación, se enumeran las clases de servicio disponibles. Las clases de servicio mostradas son los servicios administrados de Azure disponibles que se pueden aprovisionar mediante Open Service Broker para Azure.

```azurecli-interactive
./svcat get classes
```

Por último, se enumeran todos los planes de servicio disponibles. Los planes de servicio son los niveles de servicio para los servicios administrados de Azure. Por ejemplo, para Azure Database for MySQL, los planes abarcan de `basic50` para el nivel básico con 50 unidades de transacción de base de datos (DTU), a `standard800` para el nivel estándar con 800 DTU.

```azurecli-interactive
./svcat get plans
```

## <a name="install-wordpress-from-helm-chart-using-azure-database-for-mysql"></a>Instalación de WordPress desde el gráfico de Helm mediante Azure Database for MySQL

En este paso, se utilizará Helm para instalar un gráfico de Helm actualizado para WordPress. El gráfico proporciona una instancia externa de Azure Database for MySQL que WordPress puede usar. Este proceso puede tardar unos minutos.

```azurecli-interactive
helm install azure/wordpress --name wordpress --namespace wordpress --set resources.requests.cpu=0
```

Para comprobar que la instalación dispone de los recursos adecuados, enumere las instancias de servicio instaladas y los enlaces:

```azurecli-interactive
./svcat get instances -n wordpress
./svcat get bindings -n wordpress
```

Enumere los secretos instalados:

```azurecli-interactive
kubectl get secrets -n wordpress -o yaml
```

## <a name="next-steps"></a>Pasos siguientes

Al seguir este artículo, implementó el catálogo de servicios en un clúster de Azure Container Service (AKS). Usó Open Service Broker for Azure para implementar una instalación de WordPress que use servicios administrados de Azure, en este caso, Azure Database for MySQL.

Consulte el repositorio de [Azure/helm-charts][helm-charts] para acceder a otros gráficos de Helm basados en OSBA. Si está interesado en crear sus propios gráficos que funcionen con OSBA, consulte [Creación de un nuevo gráfico][helm-create-new-chart].

<!-- LINKS - external -->
[helm-charts]: https://github.com/Azure/helm-charts
[helm-cli-install]: kubernetes-helm.md#install-helm-cli
[helm-create-new-chart]: https://github.com/Azure/helm-charts#creating-a-new-chart
[kubernetes-service-catalog]: https://github.com/kubernetes-incubator/service-catalog
[open-service-broker-azure]: https://github.com/Azure/open-service-broker-azure
[service-catalog-cli]: https://github.com/Azure/service-catalog-cli

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[create-aks-cluster]: ./kubernetes-walkthrough.md
[create-service-principal]: ./kubernetes-service-principal.md
