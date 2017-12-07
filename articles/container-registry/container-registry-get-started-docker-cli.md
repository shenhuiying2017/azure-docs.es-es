---
title: "Inserción de una imagen de Docker en el registro privado de Azure"
description: "Inserción y extracción de imágenes de Docker en un registro de contenedor privado de Azure mediante la CLI de Docker"
services: container-registry
author: stevelas
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 11/29/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 21d1abfbb49eaeae654a600d35ab350b96a12fd3
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Inserción de la primera imagen en un registro de contenedor privado de Docker mediante la CLI de Docker

Un registro de contenedor de Azure almacena y administra imágenes privadas de contenedor de [Docker](http://hub.docker.com), de una forma similar a la que [Docker Hub](https://hub.docker.com/) almacena imágenes públicas. Puede usar la [interfaz de la línea de comandos de Docker](https://docs.docker.com/engine/reference/commandline/cli/) (CLI de Docker) para, entre otras, realizar las siguientes operaciones en el registro de contenedor: [iniciar sesión](https://docs.docker.com/engine/reference/commandline/login/), [insertar](https://docs.docker.com/engine/reference/commandline/push/), [extraer](https://docs.docker.com/engine/reference/commandline/pull/).

En los pasos siguientes, se descarga una [imagen de Nginx](https://store.docker.com/images/nginx) oficial desde el registro público de Docker Hub, se le asigna una etiqueta para el registro de contenedor de Azure privado, se le inserta en el registro y luego se extrae del registro.

## <a name="prerequisites"></a>Requisitos previos

* **Registro de contenedor de Azure**: cree un registro de contenedor en la suscripción de Azure. Por ejemplo, use [Azure Portal](container-registry-get-started-portal.md) o la [CLI de Azure 2.0](container-registry-get-started-azure-cli.md).
* **CLI de docker**: para configurar el equipo local como un host de Docker y tener acceso a los comandos de la CLI de Docker, instale [Docker](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Inicio de sesión en un registro

Hay [varias maneras de autenticar](container-registry-authentication.md) en el registro de contenedor privado. Es el método recomendado cuando se trabaja en una línea de comandos con el comando de la CLI de Azure [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login). Por ejemplo, para iniciar sesión en un registro denominado *myregistry*:

```azurecli
az acr login --name myregistry
```

También puede iniciar sesión con [docker login](https://docs.docker.com/engine/reference/commandline/login/). En el ejemplo siguiente se pasa el identificador y la contraseña de una [entidad de servicio](../active-directory/active-directory-application-objects.md) de Azure Active Directory. Por ejemplo, puede que haya [asignado una entidad de servicio](container-registry-authentication.md#service-principal) al registro para ver un escenario de automatización.

```Bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Ambos comandos devuelven `Login Succeeded` una vez completados. Si usa `docker login`, puede ver una advertencia de seguridad que recomienda el uso del parámetro `--password-stdin`. Cuando su uso esté fuera del ámbito de este artículo, recomendamos seguir este procedimiento recomendado. Para más información, vea la referencia del comando [docker login](https://docs.docker.com/engine/reference/commandline/login/).

> [!TIP]
> Especifique siempre el nombre completo del registro (en minúsculas) cuando se usa `docker login` y al etiquetar imágenes para insertar en el registro. En los ejemplos de este artículo, el nombre completo es *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Extracción de la imagen de Nginx oficial

En primer lugar, extraiga la imagen pública de Nginx al equipo local.

```Bash
docker pull nginx
```

## <a name="run-the-container-locally"></a>Ejecute el contenedor localmente

Ejecute el siguiente comando [docker run](https://docs.docker.com/engine/reference/run/) para iniciar una instancia local del contenedor de Nginx de forma interactiva (`-it`) en el puerto 8080. El argumento `--rm` especifica que el contenedor debe quitarse cuando lo detenga.

```Bash
docker run -it --rm -p 8080:80 nginx
```

Vaya a [http://localhost: 8080](http://localhost:8080) para ver la página web predeterminada que proporciona Nginx en el contenedor en ejecución. Debería ver una página similar a la siguiente:

![Nginx en un equipo local](./media/container-registry-get-started-docker-cli/nginx.png)

Dado que inició el contenedor de forma interactiva con `-it`, puede ver la salida del servidor Nginx en la línea de comandos después de navegar a él en el explorador.

Para detener y quitar el contenedor, presione `Control`+`C`.

## <a name="create-an-alias-of-the-image"></a>Creación de un alias de la imagen

Utilice [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) para crear un alias de la imagen, con la ruta de acceso completa al registro. Este ejemplo especifica el espacio de nombres `samples` para evitar el desorden en la raíz del registro.

```Bash
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Para más información sobre cómo etiquetar con espacios de nombres, vea la sección [Espacios de nombres del repositorio](container-registry-best-practices.md#repository-namespaces) de [Procedimientos recomendados para Azure Container Registry](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Inserción de la imagen en el registro

Ahora que ha etiquetado la imagen con la ruta de acceso completa para el registro privado, puede insertarla en el registro con [docker push](https://docs.docker.com/engine/reference/commandline/push/):

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Extracción de la imagen del registro

Use el comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para extraer la imagen del registro:

```Bash
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Inicie el contenedor de Nginx

Use el comando [docker run](https://docs.docker.com/engine/reference/run/) para ejecutar la imagen que ha extraído del registro:

```Bash
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Vaya a [http://localhost:8080](http://localhost:8080) para ver el contenedor en ejecución.

Para detener y quitar el contenedor, presione `Control`+`C`.

## <a name="remove-the-image-optional"></a>Retirada de la imagen (opcional)

Si ya no necesita la imagen Nginx, puede eliminarla localmente con el comando [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).

```Bash
docker rmi myregistry.azurecr.io/samples/nginx
```

Para quitar imágenes del registro de contenedor de Azure, puede usar el comando de la CLI de Azure [az acr repository delete](/cli/azure/acr/repository#az_acr_repository_delete). Por ejemplo, el siguiente comando elimina el manifiesto al que se hace referencia mediante una etiqueta, todos los datos de la capa asociados y todas las demás etiquetas que hacen referencia al manifiesto.

```azurecli
az acr repository delete --name myregistry --repository samples/nginx --tag latest --manifest
```

## <a name="next-steps"></a>Pasos siguientes

¡Ahora que conoce los fundamentos, ya está listo para empezar a usar el registro! Por ejemplo, implemente imágenes de contenedor del registro en un clúster de [Azure Container Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md).
