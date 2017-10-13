---
title: "Tutorial de Azure Container Instances: preparación de Azure Container Registry | Microsoft Docs"
description: "Tutorial de Azure Container Instances: preparación de Azure Container Registry"
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenedores, microservicios, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 7ac85bffb9593923808c77f2240e6f0e841e74cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-and-use-azure-container-registry"></a>Implementación y uso de Azure Container Registry

Esta es la segunda parte de un tutorial de tres partes. En el [paso anterior](./container-instances-tutorial-prepare-app.md), se creó una imagen de contenedor para una aplicación web simple escrita en [Node.js](http://nodejs.org). En este tutorial, esta imagen se insertó en una instancia de Azure Container Registry. Si no ha creado la imagen de contenedor, vuelva a [Tutorial 1: Creación de una imagen de contenedor](./container-instances-tutorial-prepare-app.md). 

Azure Container Registry es un registro privado basado en Azure para imágenes de contenedor de Docker. Este tutorial le guía a través de la implementación de una instancia de Azure Container Registry y la inserción de una imagen de contenedor en ella. Los pasos completados incluyen:

> [!div class="checklist"]
> * Implementación de una instancia de Azure Container Registry
> * Etiquetado de una imagen de contenedor para Azure Container Registry
> * Carga de una imagen a Azure Container Registry

En los tutoriales subsiguientes, se implementa el contenedor del registro privado a Azure Container Instances.

## <a name="before-you-begin"></a>Antes de empezar

Para realizar este tutorial es necesario que ejecute la versión 2.0.12 o superior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="deploy-azure-container-registry"></a>Implementación de Azure Container Registry

Para implementar Azure Container Registry, necesita tener antes un grupo de recursos. Un grupo de recursos de Azure es una colección lógica en la que se implementan y se administran los recursos de Azure.

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). En este ejemplo se crea un grupo de recursos denominado *myResourceGroup* en la región *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Cree una instancia de Azure Container Registry con el comando [az acr create](/cli/azure/acr#create). El nombre de una instancia de Container Registry **debe ser único**. En el ejemplo siguiente, usamos el nombre *mycontainerregistry082*.

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

En el resto de este tutorial, usamos `<acrname>` como un marcador de posición para el nombre del registro de contenedor que eligió.

## <a name="container-registry-login"></a>Inicio de sesión en Container Registry

Debe iniciar sesión en la instancia de ACR antes de insertar imágenes en ella. Use el comando [az acr login](https://docs.microsoft.com/en-us/cli/azure/acr#az_acr_login) para completar la operación. Debe proporcionar el nombre único que se especificó para el registro de contenedor cuando se creó.

```azurecli
az acr login --name <acrName>
```

Al finalizar, el comando devuelve un mensaje que indica que el inicio de sesión se ha realizado correctamente.

## <a name="tag-container-image"></a>Etiquetado de la imagen de contenedor

Para implementar una imagen de contenedor desde un registro privado, la imagen se debe etiquetar con el nombre `loginServer` del registro.

Para ver una lista de imágenes actual, use el comando `docker images`.

```bash
docker images
```

Salida:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

Para obtener el nombre de loginServer, ejecute el comando siguiente.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Etiquete la imagen *aci-tutorial-app* con el loginServer del registro de contenedor. Además, agregue `:v1` al final del nombre de la imagen. Esta etiqueta indica el número de versión de la imagen.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Una vez etiquetada, ejecute `docker images` para comprobar la operación.

```bash
docker images
```

Salida:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Inserción de imágenes en Azure Container Registry

Inserte la imagen *aci-tutorial-app* en el registro.

En el siguiente ejemplo, reemplace el nombre loginServer del registro de contenedor por el loginServer de su entorno.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

## <a name="list-images-in-azure-container-registry"></a>Lista de imágenes en Azure Container Registry

Para que se devuelva una lista de las imágenes que se han insertado en la instancia de Azure Container Registry, el usuario debe usar el comando [az acr repository list](/cli/azure/acr/repository#list). Actualice el comando con el nombre del registro de contenedor.

```azurecli
az acr repository list --name <acrName> --output table
```

Salida:

```azurecli
Result
----------------
aci-tutorial-app
```

Luego, para ver las etiquetas de una imagen concreta, use el comando [az acr repository show-tags](/cli/azure/acr/repository#show-tags).

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Salida:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se preparó una instancia de Azure Container Registry para usarla con Azure Container Instances y se insertó la imagen de contenedor. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * Implementación de una instancia de Azure Container Registry
> * Etiquetado de una imagen de contenedor para Azure Container Registry
> * Carga de una imagen a Azure Container Registry

Vaya al tutorial siguiente para obtener información sobre cómo implementar el contenedor en Azure con Azure Container Instances.

> [!div class="nextstepaction"]
> [Implementación de contenedores en Azure Container Instances](./container-instances-tutorial-deploy-app.md)
