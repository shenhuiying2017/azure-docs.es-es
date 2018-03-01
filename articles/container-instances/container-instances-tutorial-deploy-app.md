---
title: "Tutorial de Azure Container Instances: implementación de aplicación"
description: "Tutorial de Azure Container Instances (parte 3 de 3): implementación de aplicación"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 99bd03bf4c3ca2d7b1ced51ebfe8be669f271c1c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="deploy-a-container-to-azure-container-instances"></a>Implementación de un contenedor en Azure Container Instances

Este es el último tutorial de una serie de tres partes. En las series anteriores, [se creó una imagen de contenedor](container-instances-tutorial-prepare-app.md) y [se insertó en una instancia de Azure Container Registry](container-instances-tutorial-prepare-acr.md). En este artículo se completa la serie de tutoriales con la implementación del contenedor en Azure Container Instances.

En este tutorial, hizo lo siguiente:

> [!div class="checklist"]
> * Implementar el contenedor desde Azure Container Registry mediante la CLI de Azure
> * Ver la aplicación en el explorador
> * Ver los registros del contenedor

## <a name="before-you-begin"></a>Antes de empezar

Para realizar este tutorial es necesario que ejecute la versión 2.0.27 o posterior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Install Azure CLI 2.0][azure-cli-install] (Instalación de la CLI de Azure 2.0).

Para completar este tutorial, se necesita un entorno de desarrollo de Docker instalado localmente. Docker proporciona paquetes que permiten configurar Docker fácilmente en cualquier sistema [Mac][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

Azure Cloud Shell no incluye los componentes de Docker necesarios para completar cada paso de este tutorial. Tiene que instalar el entorno de desarrollo de la CLI de Azure y Docker en el equipo local para completar este tutorial.

## <a name="deploy-the-container-using-the-azure-cli"></a>Implementación del contenedor mediante la CLI de Azure

La CLI de Azure permite la implementación de un contenedor en Azure Container Instances en solo un comando. Como la imagen de contenedor se hospeda en la instancia privada de Azure Container Registry, debe incluir las credenciales que se necesitan para acceder a ella. Obtenga las credenciales con los siguientes comandos de la CLI de Azure.

Servidor de inicio de sesión en el registro de contenedor (actualice con su nombre de registro):

```azurecli
az acr show --name <acrName> --query loginServer
```

Contraseña del registro de contenedor:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Para implementar una imagen de contenedor desde el registro de contenedor con una solicitud de recurso de 1 núcleo de CPU y 1 GB de memoria, ejecute el comando siguiente. Reemplace `<acrLoginServer>` y `<acrPassword>` por los valores obtenidos en los dos comandos anteriores. Reemplace `<acrName>` por el nombre de Registro de contenedor.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

En pocos segundos, debería recibir una respuesta inicial de Azure Resource Manager. El valor `--dns-name-label` debe ser único dentro de la región de Azure en la que crea la instancia de contenedor. Actualice el valor del ejemplo anterior si recibe un mensaje de error de **etiqueta de nombre DNS** al ejecutar el comando.

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

Salida de ejemplo: `"aci-demo.eastus.azurecontainer.io"`

Para ver la aplicación en ejecución, vaya al nombre DNS que aparece en su explorador favorito:

![Aplicación Hola mundo en el explorador][aci-app-browser]

También puede ver la salida de registro del contenedor:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Salida:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita ninguno de los recursos creados en esta serie de tutoriales, puede ejecutar el comando [az group delete][az-group-delete] para quitar el grupo de recursos y todos los recursos que contiene. Este comando elimina el registro de contenedor que se ha creado, así como el contenedor en ejecución y todos los recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>pasos siguientes

En este tutorial se completó el proceso de implementar los contenedores en Azure Container Instances. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * Implementación del contenedor desde Azure Container Registry mediante la CLI de Azure
> * Visualización de la aplicación en el explorador
> * Visualización de los registros del contenedor

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
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
