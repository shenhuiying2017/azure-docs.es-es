---
title: "Tutorial de Azure Container Instances: implementación de aplicación"
description: "Tutorial de Azure Container Instances: implementación de aplicación"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 5222c43fe381c93527ec6c8b927d723f75dd58f9
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2017
---
# <a name="deploy-a-container-to-azure-container-instances"></a>Implementación de un contenedor en Azure Container Instances

Esta es la tercera parte de un tutorial de tres. En las secciones anteriores, [se creó una imagen de contenedor](container-instances-tutorial-prepare-app.md) y [se insertó en una instancia de Azure Container Registry](container-instances-tutorial-prepare-acr.md). En esta sección se completa el tutorial con la implementación del contenedor en Azure Container Instances. Los pasos completados incluyen:

> [!div class="checklist"]
> * Implementación del contenedor desde Azure Container Registry mediante la CLI de Azure
> * Visualización de la aplicación en el explorador
> * Visualización de los registros del contenedor

## <a name="before-you-begin"></a>Antes de empezar

Para realizar este tutorial es necesario que ejecute la versión 2.0.21 o superior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli).

Para completar este tutorial, se necesita un entorno de desarrollo de Docker. Docker proporciona paquetes que permiten configurar Docker fácilmente en cualquier sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) o [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell no incluye los componentes de Docker necesarios para completar cada paso de este tutorial. Por lo tanto, le recomendamos que realice una instalación local del entorno de desarrollo de la CLI de Azure y Docker.

## <a name="deploy-the-container-using-the-azure-cli"></a>Implementación del contenedor mediante la CLI de Azure

La CLI de Azure permite la implementación de un contenedor en Azure Container Instances en solo un comando. Como la imagen de contenedor se hospeda en la instancia privada de Azure Container Registry, debe incluir las credenciales que se necesitan para acceder a ella. En caso de ser necesario, puede consultarlas tal como se indica a continuación.

Servidor de inicio de sesión en el registro de contenedor (actualice con su nombre de registro):

```azurecli
az acr show --name <acrName> --query loginServer
```

Contraseña del registro de contenedor:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Para implementar una imagen de contenedor desde el registro de contenedor con una solicitud de recurso de 1 núcleo de CPU y 1 GB de memoria, ejecute el comando siguiente. Reemplace `<acrLoginServer>` y `<acrPassword>` por los valores obtenidos en los dos comandos anteriores.

```azurecli
az container create --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public --ports 80 -g myResourceGroup
```

En pocos segundos, debería recibir una respuesta inicial de Azure Resource Manager. Para ver el estado de la implementación, use [az container show](/cli/azure/container#az_container_show):

```azurecli
az container show --name aci-tutorial-app --resource-group myResourceGroup --query instanceView.state
```

Repita el comando `az container show` hasta que el estado cambie de *Pendiente* a *En ejecución*, que debe tardar menos de un minuto. Cuando el contenedor esté en *En ejecución*, continúe con el paso siguiente.

## <a name="view-the-application-and-container-logs"></a>Visualización de los registros de contenedor y aplicación

Cuando se complete correctamente la implementación, se mostrará la dirección IP pública del contenedor con el comando [az container show](/cli/azure/container#az_container_show):

```bash
az container show --name aci-tutorial-app --resource-group myResourceGroup --query ipAddress.ip
```

Salida de ejemplo: `"13.88.176.27"`

Para ver la aplicación en ejecución, vaya a la dirección IP pública en su explorador favorito.

![Aplicación Hola mundo en el explorador][aci-app-browser]

También puede ver la salida de registro del contenedor:

```azurecli
az container logs --name aci-tutorial-app -g myResourceGroup
```

Salida:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ya no necesita ninguno de los recursos creados en esta serie de tutoriales, puede ejecutar el comando [az group delete](/cli/azure/group#delete) para quitar el grupo de recursos y todos los recursos que contiene. Este comando elimina el registro de contenedor que se ha creado, así como el contenedor en ejecución y todos los recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial se completó el proceso de implementar los contenedores en Azure Container Instances. Se han completado los siguientes pasos:

> [!div class="checklist"]
> * Implementación del contenedor desde Azure Container Registry mediante la CLI de Azure
> * Visualización de la aplicación en el explorador
> * Visualización de los registros del contenedor

<!-- LINKS -->
[prepare-app]: ./container-instances-tutorial-prepare-app.md

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
