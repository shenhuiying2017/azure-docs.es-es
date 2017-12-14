---
title: "Tutorial de Azure Container Service tutorial: preparación de ACR"
description: "Tutorial de Azure Container Service tutorial: preparación de ACR"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c9c8ad6dfd6df0e99f9e41eaf1da12ebeb2a2da6
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-and-use-azure-container-registry"></a>Implementación y uso de Azure Container Registry

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Azure Container Registry (ACR) es un registro privado basado en Azure para imágenes de contenedor de Docker. Este tutorial, la sección dos de siete, le guía a través de la implementación de una instancia de Azure Container Registry y la inserción de imágenes de contenedor en ella. Los pasos completados incluyen:

> [!div class="checklist"]
> * Implementación de una instancia de Azure Container Registry (ACR)
> * Etiquetado de una imagen de contenedor para ACR
> * Carga de la imagen a ACR

En posteriores tutoriales, esta instancia de ACR se integra con un clúster de Kubernetes en Azure Container Service. 

## <a name="before-you-begin"></a>Antes de empezar

En el [tutorial anterior](./container-service-tutorial-kubernetes-prepare-app.md), se creó una imagen de contenedor para una aplicación sencilla de Azure Voting. Si no ha creado la imagen de la aplicación de Azure Voting, vuelva al [Tutorial 1: creación de imágenes de contenedor](./container-service-tutorial-kubernetes-prepare-app.md).

Para realizar este tutorial es necesario que ejecute la versión 2.0.4 o superior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="deploy-azure-container-registry"></a>Implementación de Azure Container Registry

Para implementar Azure Container Registry, necesita tener antes un grupo de recursos. Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). En este ejemplo, se crea un grupo de recursos denominado `myResourceGroup` en la región `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Cree una instancia de Azure Container Registry con el comando [az acr create](/cli/azure/acr#create). El nombre de una instancia de Container Registry **debe ser único**.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

En el resto de este tutorial, se usa `<acrname>` como un marcador de posición del nombre del registro de contenedor.

## <a name="container-registry-login"></a>Inicio de sesión en Container Registry

Después, use el comando [az acr login](https://docs.microsoft.com/cli/azure/acr#az_acr_login) para iniciar sesión en la instancia de Azure Container Registry. Debe proporcionar el nombre único que se especificó para el registro de contenedor cuando se creó.

```azurecli
az acr login --name <acrName>
```

Al finalizar, el comando devuelve un mensaje que indica que el inicio de sesión se ha realizado correctamente.

## <a name="tag-container-images"></a>Etiquetado de imágenes de contenedor

Para ver una lista de imágenes actual, use el comando [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Salida:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Es preciso etiquetar cada imagen de contenedor con el nombre loginServer del registro. Esta etiqueta se usa para el enrutamiento al insertar imágenes de contenedor en un registro de imágenes.

Para obtener el nombre de loginServer, ejecute el comando siguiente.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Ahora, etiquete la imagen `azure-vote-front` con el loginServer del registro de contenedor. Además, agregue `:redis-v1` al final del nombre de la imagen. Esta etiqueta indica la versión de la imagen.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v1
```

Una vez que estén etiquetadas, ejecute [docker images] (https://docs.docker.com/engine/reference/commandline/images/) para comprobar la operación.

```bash
docker images
```

Salida:

```bash
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   redis-v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Inserción de imágenes en el registro

Inserte la imagen `azure-vote-front` en el registro. 

En el siguiente ejemplo, reemplace el nombre loginServer de ACR por el loginServer de su entorno.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v1
```

Tarda unos minutos en completarse.

## <a name="list-images-in-registry"></a>Lista de imágenes en el registro

Para que se devuelva una lista de las imágenes que se han insertado en la instancia de Azure Container Registry, el usuario debe usar el comando [az acr repository list](/cli/azure/acr/repository#list). Actualice el comando con el nombre de instancia de ACR.

```azurecli
az acr repository list --name <acrName> --output table
```

Salida:

```azurecli
Result
----------------
azure-vote-front
```

Luego, para ver las etiquetas de una imagen concreta, use el comando [az acr repository show-tags](/cli/azure/acr/repository#show-tags).

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Salida:

```azurecli
Result
--------
redis-v1
```

Al finalizar el tutorial, la imagen de contenedor se ha almacenado en una instancia privada de Azure Container Registry. Esta imagen se implementa desde ACR en un clúster de Kubernetes en tutoriales posteriores.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se ha preparado una instancia de Azure Container Registry para su uso en un clúster de ACS Kubernetes. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * Implementación de una instancia de Azure Container Registry
> * Etiquetado de una imagen de contenedor para ACR
> * Carga de la imagen a ACR

Vaya al siguiente tutorial para aprender a implementar un clúster de Kubernetes en Azure.

> [!div class="nextstepaction"]
> [Implementación de un clúster de Kubernetes](./container-service-tutorial-kubernetes-deploy-cluster.md)