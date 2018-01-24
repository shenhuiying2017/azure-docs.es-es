---
title: "Tutorial de Azure Container Instances: preparación de Azure Container Registry"
description: "Tutorial de Azure Container Instances, parte 2 de 3: preparación de Azure Container Registry"
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c0aad1f9bbaac9a456b34f75633faba92f57f498
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Implementación y uso de Azure Container Registry

Esta es la segunda parte de un tutorial de tres partes. En el [paso anterior](container-instances-tutorial-prepare-app.md), se creó una imagen de contenedor para una aplicación web simple escrita en [Node.js][nodejs]. En este tutorial, la imagen se inserta en una instancia de Azure Container Registry. Si no ha creado la imagen de contenedor, vuelva a [Tutorial 1: Creación de una imagen de contenedor](container-instances-tutorial-prepare-app.md).

Azure Container Registry es un registro privado basado en Azure para imágenes de contenedor de Docker. Este tutorial le guía a través de la implementación de una instancia de Azure Container Registry y la inserción de una imagen de contenedor en ella.

En este artículo, la segunda parte de la serie, podrá:

> [!div class="checklist"]
> * Implementar una instancia de Azure Container Registry
> * Etiquetar una imagen de contenedor para Azure Container Registry
> * Cargar la imagen en el registro

En el artículo siguiente, el último tutorial de la serie, implementa el contenedor del registro privado en Azure Container Instances.

## <a name="before-you-begin"></a>Antes de empezar

Para realizar este tutorial es necesario que ejecute la versión 2.0.23 o posterior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Install Azure CLI 2.0][azure-cli-install] (Instalación de la CLI de Azure 2.0).

Para completar este tutorial, se necesita un entorno de desarrollo de Docker instalado localmente. Docker proporciona paquetes que permiten configurar Docker fácilmente en cualquier sistema [Mac][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

Azure Cloud Shell no incluye los componentes de Docker necesarios para completar cada paso de este tutorial. Tiene que instalar el entorno de desarrollo de la CLI de Azure y Docker en el equipo local para completar este tutorial.

## <a name="deploy-azure-container-registry"></a>Implementación de Azure Container Registry

Para implementar Azure Container Registry, necesita tener antes un grupo de recursos. Un grupo de recursos de Azure es una colección lógica en la que se implementan y se administran los recursos de Azure.

Cree un grupo de recursos con el comando [az group create][az-group-create]. En este ejemplo se crea un grupo de recursos denominado *myResourceGroup* en la región *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Cree una instancia de Azure Container Registry con el comando [az acr create][az-acr-create]. El nombre de registro de contenedor **debe ser único** dentro de Azure y contener entre 5 y 50 caracteres alfanuméricos. Reemplace `<acrName>` por un nombre único para el registro:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Por ejemplo, para crear un Azure Container Registry denominado *mycontainerregistry082*:

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

En el resto de este tutorial, usamos `<acrName>` como un marcador de posición para el nombre del registro de contenedor que eligió.

## <a name="container-registry-login"></a>Inicio de sesión en Container Registry

Debe iniciar sesión en la instancia de Azure Container Registry antes de insertar imágenes en ella. Use el comando [az acr login][az-acr-login] para completar la operación. Debe especificar el nombre único que dio al registro de contenedor cuando lo creó.

```azurecli
az acr login --name <acrName>
```

El comando devolverá un mensaje `Login Succeeded` una vez completado.

## <a name="tag-container-image"></a>Etiquetado de la imagen de contenedor

Para implementar una imagen de contenedor desde un registro privado, debe etiquetar la imagen con el nombre `loginServer` del registro.

Para ver una lista de las imágenes actuales, use el comando [docker images][docker-images].

```bash
docker images
```

Salida:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

Para obtener el nombre de loginServer, ejecute el comando [az acr show][az-acr-show]. Reemplace `<acrName>` por el nombre de Registro de contenedor.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Salida de ejemplo:

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

Etiquete la imagen *aci-tutorial-app* con el loginServer del registro de contenedor. Además, agregue `:v1` al final del nombre de la imagen. Esta etiqueta indica el número de versión de la imagen. Reemplace `<acrLoginServer>` por el resultado del comando [az acr show][az-acr-show] que acaba de ejecutar.

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

Inserte la imagen *aci-tutorial-app* en el registro con el comando [docker push][docker-push]. Reemplace `<acrLoginServer>` por el nombre del servidor de inicio de sesión completo obtenido en el paso anterior.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

La operación `push` debería tardar de unos segundos a unos minutos según la conexión a Internet, y el resultado ser similar al siguiente:

```bash
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Lista de imágenes en Azure Container Registry

Para que se devuelva una lista de las imágenes que se han insertado en la instancia de Azure Container Registry, el usuario debe usar el comando [az acr repository list][az-acr-repository-list]. Actualice el comando con el nombre del registro de contenedor.

```azurecli
az acr repository list --name <acrName> --output table
```

Salida:

```azurecli
Result
----------------
aci-tutorial-app
```

Luego, para ver las etiquetas de una imagen concreta, use el comando [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Salida:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha preparado una instancia de Azure Container Registry para usarla con Azure Container Instances y ha insertado una imagen de contenedor en el registro. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * Implementación de una instancia de Azure Container Registry
> * Etiquetado de una imagen de contenedor para Azure Container Registry
> * Carga de una imagen en Azure Container Registry

Vaya al tutorial siguiente para obtener información sobre cómo implementar el contenedor en Azure con Azure Container Instances.

> [!div class="nextstepaction"]
> [Implementación de contenedores en Azure Container Instances](./container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
