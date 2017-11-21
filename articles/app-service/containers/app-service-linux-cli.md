---
title: "Administración de Web Apps for Containers mediante la CLI de Azure 2.0 | Microsoft Docs"
description: "Administración de Web Apps for Containers mediante la CLI de Azure."
keywords: azure app service, web app, cli, linux, oss
services: app-service
documentationCenter: 
author: ahmedelnably
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: aelnably
ms.openlocfilehash: 54c979313a6ffa43008aa9870332b92d2b2f182a
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Administración de Web Apps for Containers mediante la CLI de Azure

Con los comandos de este artículo, podrá crear y administrar una instancia de Web Apps for Containers mediante la CLI de Azure 2.0.
Puede empezar a usar la nueva versión de la CLI de dos formas:

* [Instalación de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) en su equipo.
* Uso de [Azure Cloud Shell (versión preliminar)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Creación de un plan de App Service de Linux

Para crear un plan de App Service de Linux, puede usar el siguiente comando:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Creación de una aplicación web del contenedor de Docker personalizada

Para crear una aplicación web y configurarla para ejecutar un contenedor de Docker personalizado, puede usar el siguiente comando:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Activación del registro del contenedor de Docker

Para activar el registro del contenedor de Docker, puede usar el comando siguiente:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Cambio del contenedor de Docker personalizado para una aplicación existente de Web Apps for Containers

Para cambiar una aplicación creada anteriormente, de la imagen de Docker actual a una nueva imagen, puede usar el siguiente comando:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Uso de imágenes de Docker de un Registro privado

Puede configurar la aplicación para usar imágenes de un Registro privado. Debe proporcionar la dirección URL de su Registro, nombre de usuario y contraseña. Esto se puede lograr con el siguiente comando:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Habilitación de implementaciones continuas para imágenes de Docker personalizadas

Con el siguiente comando puede habilitar la funcionalidad de CD y obtener la dirección URL de webhook. Esta dirección URL se puede usar para configurar su repositorio de Azure Container Registry o DockerHub.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Creación de una aplicación de Web Apps for Containers mediante uno de nuestros marcos en tiempo de ejecución integrados

Para crear una aplicación de Web Apps for Containers en PHP 5.6, puede usar el siguiente comando.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Cambio de la versión del marco para una aplicación existente de Web App for Containers

Para cambiar una aplicación creada anteriormente, de la versión de .NET Framework actual a Node.js 6.11, puede usar el siguiente comando:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Configuración de implementaciones Git para su aplicación web

Para configurar implementaciones Git para su aplicación, puede usar el siguiente comando:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Pasos siguientes

* [¿Qué es Azure App Service en Linux?](app-service-linux-intro.md)
* [Instalación de la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure Cloud Shell (versión preliminar)](../../cloud-shell/overview.md)
* [Configuración de entornos de ensayo en Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implementación continua con Web App for Containers](app-service-linux-ci-cd.md)
