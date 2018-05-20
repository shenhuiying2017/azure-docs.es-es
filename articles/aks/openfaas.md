---
title: Uso de OpenFaaS con Azure Kubernetes Service (AKS)
description: Implementación y uso de OpenFaaS con Azure Kubernetes Service (AKS)
services: container-service
author: justindavies
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/05/2018
ms.author: juda
ms.custom: mvc
ms.openlocfilehash: 778fa5ddcdf8006d28c092746e4ac17a497baa5f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="using-openfaas-on-aks"></a>Uso de OpenFaaS en AKS

[OpenFaaS] [open-faas] es una plataforma para la creación de funciones sin servidor sobre contenedores. Como proyecto de Open Source, ha sido adoptado a gran escala dentro de la comunidad. En este documento se detalla la instalación y uso de OpenFaas en un clúster de Azure Kubernetes Service (AKS).

## <a name="prerequisites"></a>requisitos previos

Para completar los pasos de este artículo, necesita lo siguiente.

* Conocimientos básicos sobre Kubernetes.
* Un clúster de Azure Kubernetes Service (AKS) y las credenciales de AKS configuradas en el sistema de desarrollo.
* CLI de Azure instalada en el sistema de desarrollo.
* Herramientas de línea de comandos GIT instaladas en el sistema.

## <a name="get-openfaas"></a>Obtención de OpenFaaS

Clone el repositorio de proyecto de OpenFaaS para su sistema de desarrollo.

```azurecli-interactive
git clone https://github.com/openfaas/faas-netes
```

Cambie al directorio del repositorio clonado.

```azurecli-interactive
cd faas-netes
```

## <a name="deploy-openfaas"></a>Implementación de OpenFaaS

Como recomendación, OpenFaaS y sus funciones deberían almacenarse en su propio espacio de nombres de Kubernetes.

Cree un espacio de nombres para el sistema OpenFaaS.

```azurecli-interactive
kubectl create namespace openfaas
```

Cree un segundo espacio de nombres para las funciones de OpenFaaS.

```azurecli-interactive
kubectl create namespace openfaas-fn
```

En el repositorio clonado se incluye un gráfico de Helm para OpenFaaS. Utilice este gráfico para implementar OpenFaaS en el clúster de AKS.

```azurecli-interactive
helm install --namespace openfaas -n openfaas \
  --set functionNamespace=openfaas-fn, \
  --set serviceType=LoadBalancer, \
  --set rbac=false chart/openfaas/
```

Salida:

```
NAME:   openfaas
LAST DEPLOYED: Wed Feb 28 08:26:11 2018
NAMESPACE: openfaas
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                 DATA  AGE
prometheus-config    2     20s
alertmanager-config  1     20s

{snip}

NOTES:
To verify that openfaas has started, run:

  kubectl --namespace=openfaas get deployments -l "release=openfaas, app=openfaas"
```

Se crea una dirección IP pública para tener acceso a la puerta de enlace de OpenFaaS. Para recuperar esta dirección IP, use el comando [kubectl get service][kubectl-get]. La asignación de la dirección IP al servicio puede tardar hasta un minuto.

```console
kubectl get service -l component=gateway --namespace openfaas
```

Salida.

```console
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)          AGE
gateway            ClusterIP      10.0.156.194   <none>         8080/TCP         7m
gateway-external   LoadBalancer   10.0.28.18     52.186.64.52   8080:30800/TCP   7m
```

Para probar el sistema OpenFaaS, vaya a la dirección IP externa en el puerto 8080, `http://52.186.64.52:8080` en este ejemplo.

![UI de OpenFaaS](media/container-service-serverless/openfaas.png)

Por último, instale la CLI de OpenFaaS. En este ejemplo se usaba brew, consulte la [documentación de la CLI de OpenFaaS][open-faas-cli] para ver más opciones.

```console
brew install faas-cli
```

## <a name="create-first-function"></a>Creación de la primera función

Ahora que OpenFaaS está en funcionamiento, cree una función mediante el portal de OpenFaas.

Haga clic en **Deploy New Function** (Implementar nueva función) y busque **Figlet**. Seleccione la función Figlet y haga clic en **Deploy** (Implementar).

![Figlet](media/container-service-serverless/figlet.png)

Use curl para invocar la función. Reemplace la dirección IP en el ejemplo siguiente con la de la puerta de enlace de OpenFaas.

```azurecli-interactive
curl -X POST http://52.186.64.52:8080/function/figlet -d "Hello Azure"
```

Salida:

```console
 _   _      _ _            _
| | | | ___| | | ___      / \    _____   _ _ __ ___
| |_| |/ _ \ | |/ _ \    / _ \  |_  / | | | '__/ _ \
|  _  |  __/ | | (_) |  / ___ \  / /| |_| | | |  __/
|_| |_|\___|_|_|\___/  /_/   \_\/___|\__,_|_|  \___|

```

## <a name="create-second-function"></a>Creación de la segunda función

Ahora cree la segunda función. Este ejemplo se implementará con la CLI de OpenFaaS e incluye una imagen de contenedor personalizada y la recuperación de datos de una instancia de Cosmos DB. Antes de crear la función, hay que configurar varios elementos.

En primer lugar, hay que crear un grupo de recursos para Cosmos DB.

```azurecli-interactive
az group create --name serverless-backing --location eastus
```

Implemente una instancia de CosmosDB de tipo `MongoDB`. La instancia necesita un nombre único, actualice `openfaas-cosmos` con un nombre único para su entorno.

```azurecli-interactive
az cosmosdb create --resource-group serverless-backing --name openfaas-cosmos --kind MongoDB
```

Obtenga la cadena de conexión de Cosmos DB y almacénela en una variable.

Actualice el valor del argumento `--resource-group` con el nombre de su grupo de recursos y el argumento `--name` con el de su instancia de Cosmos DB.

```azurecli-interactive
COSMOS=$(az cosmosdb list-connection-strings \
  --resource-group serverless-backing \
  --name openfaas-cosmos \
  --query connectionStrings[0].connectionString \
  --output tsv)
```

Ahora rellene la instancia de Cosmos DB con datos de prueba. Cree un archivo denominado `plans.json` y cópielo en el siguiente código json.

```json
{
    "name" : "two_person",
    "friendlyName" : "Two Person Plan",
    "portionSize" : "1-2 Person",
    "mealsPerWeek" : "3 Unique meals per week",
    "price" : 72,
    "description" : "Our basic plan, delivering 3 meals per week, which will feed 1-2 people.",
    "__v" : 0
}
```

Use la herramienta *mongoimport* para cargar la instancia de CosmosDB con datos.

Si es necesario, instale las herramientas de MongoDB. En el ejemplo siguiente se instalan estas herramientas con brew. Consulte la [documentación de MongoDB][ install-mongo] para ver otras opciones.

```azurecli-interactive
brew install mongodb
```

Cargue los datos en la base de datos.

```azurecli-interactive
mongoimport --uri=$COSMOS -c plans < plans.json
```

Salida:

```console
2018-02-19T14:42:14.313+0000    connected to: localhost
2018-02-19T14:42:14.918+0000    imported 1 document
```

Ejecute el siguiente comando para crear la función. Actualice el valor del argumento `-g` con la dirección de la puerta de enlace de OpenFaaS.

```azurecli-interctive
faas-cli deploy -g http://52.186.64.52:8080 --image=shanepeckham/openfaascosmos --name=cosmos-query --env=NODE_ENV=$COSMOS
```

Una vez implementado, debería ver el punto de conexión de OpenFaaS recién creado para la función.

```console
Deployed. 202 Accepted.
URL: http://52.186.64.52:8080/function/cosmos-query
```

Pruebe la función con curl. Actualice la dirección IP con la dirección de puerta de enlace de OpenFaaS.

```console
curl -s http://52.186.64.52:8080/function/cosmos-query
```

Salida:

```json
[{"ID":"","Name":"two_person","FriendlyName":"","PortionSize":"","MealsPerWeek":"","Price":72,"Description":"Our basic plan, delivering 3 meals per week, which will feed 1-2 people."}]
```

También puede probar la función dentro de la UI de OpenFaaS.

![texto alternativo](media/container-service-serverless/OpenFaaSUI.png)

## <a name="next-steps"></a>Pasos siguientes

La implementación predeterminada de OpenFaas debe estar bloqueada tanto para la puerta de enlace de OpenFaaS como para sus funciones. [La entrada del blog de Alex Ellis](https://blog.alexellis.io/lock-down-openfaas/) contiene más detalles sobre las opciones de configuración segura.

<!-- LINKS - external -->
[install-mongo]: https://docs.mongodb.com/manual/installation/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[open-faas]: https://www.openfaas.com/
[open-faas-cli]: https://github.com/openfaas/faas-cli