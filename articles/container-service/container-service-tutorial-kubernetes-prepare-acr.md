---
title: "Tutorial de Azure Container Service tutorial: preparación de ACR | Microsoft Docs"
description: "Tutorial de Azure Container Service tutorial: preparación de ACR"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenedores, microservicios, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: adc00cb52d5805e152ca742f4cd6b9fab3ed3788
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017

---

# <a name="deploy-and-use-azure-container-registry"></a>Implementación y uso de Azure Container Registry

Azure Container Registry (ACR) es un registro privado basado en Azure para imágenes de contenedor de Docker. Este tutorial le guía a través de la implementación de una instancia de Azure Container Registry y la inserción de imágenes en ella. Los pasos completados incluyen:

> [!div class="checklist"]
> * Implementación de una instancia de Azure Container Registry
> * Etiquetado de imágenes de contenedor para ACR
> * Carga de imágenes en ACR

En posteriores tutoriales, esta instancia de ACR se integra con un clúster de Kubernetes en Azure Container Service, para la ejecución segura de imágenes de contenedor. 

## <a name="before-you-begin"></a>Antes de empezar

En el [tutorial anterior](./container-service-tutorial-kubernetes-prepare-app.md), se crearon imágenes de contenedor para una aplicación sencilla de Azure Voting. En este tutorial, estas imágenes se han insertado en Azure Container Registry. Si no ha creado las imágenes de la aplicación de Azure Voting, vuelva al [paso 1 del Tutorial: creación de imágenes de contenedor](./container-service-tutorial-kubernetes-prepare-app.md). Por otra parte, los pasos descritos aquí funcionan con cualquier imagen de contenedor.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tutorial es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="deploy-azure-container-registry"></a>Implementación de Azure Container Registry

Para implementar Azure Container Registry, necesita tener antes un grupo de recursos. Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). En este ejemplo se crea un grupo de recursos denominado *myResourceGroup* en la región *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Cree una instancia de Azure Container Registry con el comando [az acr create](/cli/azure/acr#create). El nombre de una instancia de Container Registry **debe ser único**. En el siguiente ejemplo, actualice el nombre con algunos caracteres aleatorios.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic --admin-enabled true
```

## <a name="get-acr-information"></a>Obtención de información de ACR 

Una vez creada la instancia de ACR, son necesarios el nombre, el nombre del servidor de inicio de sesión y la contraseña de autenticación. El código siguiente devuelve cada uno de estos valores. Apunte cada valor, se hace referencia a ellos a lo largo de este tutorial.  

Nombre y servidor de inicio de sesión de ACR:

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrName:name,acrLoginServer:loginServer}" --output table
```

Contraseña de ACR: se actualiza con el nombre de ACR.

```azurecli-interactive
az acr credential show --name <acrName> --query passwords[0].value -o tsv
```

## <a name="container-registry-login"></a>Inicio de sesión en Container Registry

Debe iniciar sesión en la instancia de ACR antes de insertar imágenes en ella. Use el comando [docker login](https://docs.docker.com/engine/reference/commandline/login/) para completar la operación. Al ejecutar el inicio de sesión de docker, tiene que proporcionar el nombre del servidor de inicio de sesión de ACR y las credenciales de ACR.

```bash
docker login --username=<acrName> --password=<acrPassword> <acrLoginServer>
```

Al finalizar, el comando devuelve un mensaje que indica que el inicio de sesión se ha realizado correctamente.

## <a name="tag-container-images"></a>Etiquetado de imágenes de contenedor

Es preciso etiquetar cada imagen de contenedor con el nombre `loginServer` del registro. Esta etiqueta se usa para el enrutamiento al insertar imágenes de contenedor en un registro de imágenes.

Para ver una lista de imágenes actual, use el comando `docker images`.

```bash
docker images
```

Salida:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              c13c4f50ede1        39 seconds ago       716 MB
azure-vote-back              latest              33fe5afc1885        About a minute ago   407 MB
mysql                        latest              e799c7f9ae9c        4 weeks ago          407 MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        8 months ago         694 MB
```

Etiquete la imagen *azure-vote-front* con el loginServer del registro de contenedor. Además, agregue `:v1` al final del nombre de la imagen. Esta etiqueta indica el número de versión de la imagen.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Repita el comando con la imagen *azure-vote-back*.

```bash
docker tag azure-vote-back <acrLoginServer>/azure-vote-back:v1
```

Una vez etiquetada, ejecute `docker images` para comprobar la operación.

```bash
docker images
```

Salida:

```bash
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-back                                      latest              a9dace4e1a17        7 minutes ago       407 MB
mycontainerregistry082.azurecr.io/azure-vote-back    v1                  a9dace4e1a17        7 minutes ago       407 MB
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1                  eaf2b9c57e5e        8 minutes ago       716 MB
mysql                                                latest              e799c7f9ae9c        6 weeks ago         407 MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-acr"></a>Inserción de imágenes en ACR

Inserte la imagen *azure-vote-front* en el registro. 

En el siguiente ejemplo, reemplace el nombre loginServer de ACR por el loginServer de su entorno. Tarda unos minutos en completarse.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

Haga lo mismo con la imagen *azure-vote-back*.

```bash
docker push <acrLoginServer>/azure-vote-back:v1
```

## <a name="list-images-in-acr"></a>Lista de imágenes de ACR 

Para que se devuelva una lista de las imágenes que se han insertado en la instancia de Azure Container Registry, el usuario debe usar el comando [az acr repository list](/cli/azure/acr/repository#list). Actualice el comando con el nombre de instancia de ACR.

```azurecli-interactive
az acr repository list --name <acrName> --username <acrName> --password <acrPassword> --output table
```

Salida:

```azurecli
Result
----------------
azure-vote-back
azure-vote-front
```

Luego, para ver las etiquetas de una imagen concreta, use el comando [az acr repository show-tags](/cli/azure/acr/repository#show-tags).

```azurecli-interactive
az acr repository show-tags --name <acrName> --username <acrName> --password <acrPassword> --repository azure-vote-front --output table
```

Salida:

```azurecli
Result
--------
v1
```

Al finalizar el tutorial, las dos imágenes de contenedor se han almacenado en una instancia privada de Azure Container Registry. Estas imágenes se implementan desde ACR en un clúster de Kubernetes en posteriores tutoriales.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se ha preparado una instancia de Azure Container Registry para su uso en un clúster de ACS Kubernetes. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * Implementación de una instancia de Azure Container Registry
> * Etiquetado de imágenes de contenedor para ACR
> * Carga de imágenes en ACR

Vaya al siguiente tutorial para aprender a implementar un clúster de Kubernetes en Azure.

> [!div class="nextstepaction"]
> [Implementación de un clúster de Kubernetes](./container-service-tutorial-kubernetes-deploy-cluster.md)
