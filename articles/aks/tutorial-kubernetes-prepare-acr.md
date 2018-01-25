---
title: "Tutorial de Kubernetes en Azure: preparación de ACR"
description: "Tutorial de AKS: preparación de ACR"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 11/11/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b50d3b091848776feb33c042c2cddfcf2a598fc9
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Implementación y uso de Azure Container Registry

Azure Container Registry (ACR) es un registro privado basado en Azure para imágenes de contenedor de Docker. Este tutorial, la segunda parte de ocho, le guía a través de la implementación de una instancia de Azure Container Registry y la inserción de imágenes de contenedor en ella. Los pasos completados incluyen:

> [!div class="checklist"]
> * Implementación de una instancia de Azure Container Registry (ACR)
> * Etiquetado de una imagen de contenedor para ACR
> * Carga de la imagen a ACR

En posteriores tutoriales, esta instancia de ACR se integra con un clúster de Kubernetes en AKS.

## <a name="before-you-begin"></a>Antes de empezar

En el [tutorial anterior][aks-tutorial-prepare-app], se creó una imagen de contenedor para una aplicación sencilla de Azure Voting. Si no ha creado la imagen de la aplicación de Azure Voting, vuelva al [Tutorial 1: Creación de imágenes de contenedor][aks-tutorial-prepare-app].

Para realizar este tutorial es necesario que ejecute la versión 2.0.21 o superior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure][azure-cli-install].

## <a name="deploy-azure-container-registry"></a>Implementación de Azure Container Registry

Para implementar Azure Container Registry, necesita tener antes un grupo de recursos. Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

Cree un grupo de recursos con el comando [az group create][az-group-create]. En este ejemplo, se crea un grupo de recursos denominado `myResourceGroup` en la región `eastus`.

```azurecli
az group create --name myResourceGroup --location eastus
```

Cree una instancia de Azure Container Registry con el comando [az acr create][az-acr-create]. El nombre del registro debe ser único dentro de Azure y contener entre 5 y 50 caracteres alfanuméricos.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

En el resto de este tutorial, se usa `<acrName>` como un marcador de posición del nombre del registro de contenedor.

## <a name="container-registry-login"></a>Inicio de sesión en Container Registry

Después, use el comando [az acr login][az-acr-login] para iniciar sesión en la instancia de ACR. Debe proporcionar el nombre único que se especificó para el registro de contenedor cuando se creó.

```azurecli
az acr login --name <acrName>
```

Al finalizar, el comando devuelve un mensaje que indica que el inicio de sesión se ha realizado correctamente.

## <a name="tag-container-images"></a>Etiquetado de imágenes de contenedor

Para ver una lista de las imágenes actuales, use el comando [docker images][docker-images].

```console
docker images
```

Salida:

```
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

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v1
```

Una vez etiquetada, ejecute [docker images][docker-images] para comprobar la operación.

```console
docker images
```

Salida:

```
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   redis-v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Inserción de imágenes en el registro

Inserte la imagen `azure-vote-front` en el registro.

En el siguiente ejemplo, reemplace el nombre loginServer de ACR por el loginServer de su entorno.

```console
docker push <acrLoginServer>/azure-vote-front:redis-v1
```

Tarda unos minutos en completarse.

## <a name="list-images-in-registry"></a>Lista de imágenes en el registro

Para que se devuelva una lista de las imágenes que se han insertado en la instancia de Azure Container Registry, el usuario debe usar el comando [az acr repository list][az-acr-repository-list]. Actualice el comando con el nombre de instancia de ACR.

```azurecli
az acr repository list --name <acrName> --output table
```

Salida:

```azurecli
Result
----------------
azure-vote-front
```

Luego, para ver las etiquetas de una imagen concreta, use el comando [az acr repository show-tags][az-acr-repository-show-tags].

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

## <a name="next-steps"></a>pasos siguientes

En este tutorial, se ha preparado una instancia de Azure Container Registry para su uso en un clúster de AKS. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * Implementación de una instancia de Azure Container Registry
> * Etiquetado de una imagen de contenedor para ACR
> * Carga de la imagen a ACR

Vaya al siguiente tutorial para aprender a implementar un clúster de Kubernetes en Azure.

> [!div class="nextstepaction"]
> [Implementación de un clúster de Kubernetes][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#create
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#show-tags
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md