---
title: 'Tutorial de Azure Container Instances: implementación de aplicación'
description: 'Tutorial de Azure Container Instances (parte 3 de 3): implementación de aplicación'
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 29d7114f288f7387d0c7cd5c6afe2eaaa7a8c560
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-deploy-a-container-to-azure-container-instances"></a>Tutorial: Implementación de un contenedor en Azure Container Instances

Este es el último tutorial de una serie de tres partes. En las series anteriores, [se creó una imagen de contenedor](container-instances-tutorial-prepare-app.md) y [se insertó en una instancia de Azure Container Registry](container-instances-tutorial-prepare-acr.md). En este artículo se completa la serie con la implementación del contenedor en Azure Container Instances.

En este tutorial, hizo lo siguiente:

> [!div class="checklist"]
> * Implementar el contenedor de Azure Container Registry a Azure Container Instances
> * Visualizar la aplicación en ejecución en el explorador
> * Mostrar los registros del contenedor

## <a name="before-you-begin"></a>Antes de empezar

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>Implementación del contenedor mediante la CLI de Azure

En esta sección, se utiliza la CLI de Azure para implementar la imagen integrada en el [primer tutorial](container-instances-tutorial-prepare-app.md) e insertarla en Azure Container Registry del [segundo tutorial](container-instances-tutorial-prepare-acr.md). Asegúrese de haber completado estos tutoriales antes de continuar.

### <a name="get-registry-credentials"></a>Obtención de las credenciales del registro

Al implementar una imagen que se hospeda en un registro de contenedor privado como el que se creó en el [segundo tutorial](container-instances-tutorial-prepare-acr.md), debe proporcionar las credenciales del registro.

En primer lugar, obtenga el nombre completo del servidor de inicio de sesión del registro de contenedor (reemplace `<acrName>` por el nombre del registro):

```azurecli
az acr show --name <acrName> --query loginServer
```

A continuación, obtenga la contraseña del registro de contenedor:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

### <a name="deploy-container"></a>Implementación de un contenedor

Ahora, utilice comando [az container create][az-container-create] para implementar el contenedor. Reemplace `<acrLoginServer>` y `<acrPassword>` por los valores obtenidos en los dos comandos anteriores. Reemplace `<acrName>` por el nombre de Registro de contenedor.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Al cabo de unos segundos, debe recibir una respuesta inicial de Azure. El valor `--dns-name-label` debe ser único dentro de la región de Azure en la que crea la instancia de contenedor. Modifique el valor del comando anterior si recibe un mensaje de error de **etiqueta de nombre DNS** al ejecutar el comando.

### <a name="verify-deployment-progress"></a>Comprobación del progreso de la implementación

Para ver el estado de la implementación, use [az container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Repita el comando [az container show][az-container-show] hasta que el estado cambie de *Pendiente* a *En ejecución*, que debe tardar menos de un minuto. Cuando el contenedor esté en *En ejecución*, continúe con el paso siguiente.

## <a name="view-the-application-and-container-logs"></a>Visualización de los registros de contenedor y aplicación

Cuando se complete correctamente la implementación, se mostrará el nombre de dominio completo (FQDN) del contenedor con el comando [az container show][az-container-show]:

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

Por ejemplo: 
```console
$ az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
"aci-demo.eastus.azurecontainer.io"
```

Para ver la aplicación en ejecución, vaya al nombre DNS que aparece en su explorador favorito:

![Aplicación Hola mundo en el explorador][aci-app-browser]

También puede ver la salida de registro del contenedor:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Salida de ejemplo:

```bash
$ az container logs --resource-group myResourceGroup --name aci-tutorial-app
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita ninguno de los recursos creados en esta serie de tutoriales, puede ejecutar el comando [az group delete][az-group-delete] para quitar el grupo de recursos y todos los recursos que contiene. Este comando elimina el registro de contenedor que se ha creado, así como el contenedor en ejecución y todos los recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial se completó el proceso de implementar los contenedores en Azure Container Instances. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * Implementación del contenedor desde Azure Container Registry mediante la CLI de Azure
> * Visualización de la aplicación en el explorador
> * Visualización de los registros del contenedor

Ahora que ya tiene los conceptos básicos, va a aprender más acerca de Azure Container Instances, como por ejemplo, cómo funcionan los grupos de contenedores:

> [!div class="nextstepaction"]
> [Grupos de contenedores en Azure Container Instances](container-instances-container-groups.md)

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
