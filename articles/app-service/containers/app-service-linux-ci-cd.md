---
title: "Implementación continua con Azure Web App for Containers | Microsoft Docs"
description: "Configuración de la implementación continua en Azure Web App for Containers."
keywords: azure app service, linux, oss, acr
services: app-service
documentationcenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc
ms.openlocfilehash: 27a2c95c09197b3439d3fac7c74d253df2b32b1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="continuous-deployment-with-azure-web-app-for-containers"></a>Implementación continua con Azure Web App for Containers

En este tutorial, configurará una implementación continua para una imagen de contenedor personalizada desde los repositorios administrados de [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/) o [Docker Hub](https://hub.docker.com).

## <a name="step-1---sign-in-to-azure"></a>Paso 1: Inicio de sesión en Azure

Inicie sesión en Azure Portal: http://portal.azure.com/.

## <a name="step-2---enable-container-continuous-deployment-feature"></a>Paso 2: Habilitación de la característica de implementación continua del contenedor

Puede habilitar la característica de implementación continua mediante la [CLI de Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli), así como ejecutando el siguiente comando

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
``` 

En **[Azure Portal](https://portal.azure.com/)**, haga clic en la opción **App Service** del lado izquierdo de la página.

Haga clic en el nombre de la aplicación para la que desea configurar una implementación continua de Docker Hub.

En la **configuración de la aplicación**, agregue una aplicación llamada `DOCKER_ENABLE_CI` con el valor `true`.

![insert image of app setting](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---prepare-webhook-url"></a>Paso 3: Preparación de la dirección URL de webhook

Puede obtener la dirección URL de webhook mediante la [CLI de Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli), así como ejecutando el siguiente comando

```azurecli-interactive
az webapp deployment container show-cd-url -n sname1 -g rgname
``` 

Para la URL del Webhook, debe tener el siguiente punto de conexión: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Puede obtener `publishingusername` y `publishingpwd` descargando el perfil de publicación de la aplicación web mediante Azure Portal.

![insert image of adding webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="step-4---add-a-web-hook"></a>Paso 4: Incorporación de webhook

### <a name="azure-container-registry"></a>Azure Container Registry

En la hoja del portal de registro, haga clic en **Webhooks** y cree un nuevo webhook haciendo clic en **Agregar**. En la hoja **Crear webhook**, asigne un nombre a su webhook. Para el URI de webhook, deberá proporcionar la dirección URL obtenida en el **paso 3**.

Asegúrese de que define el ámbito como el repositorio que contiene la imagen de contenedor.

![insertar imagen de webhook](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

Cuando se actualiza la imagen, la aplicación web se actualizan automáticamente con la nueva imagen.

### <a name="docker-hub"></a>Docker Hub

En la página de Docker Hub, haga clic en **Webhooks** y, luego, en **CREAR NUEVO WEBHOOK**.

![insert image of adding webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

Para la dirección URL de webhook, deberá proporcionar la dirección URL que se obtienen de **paso 3**

![insert image of adding webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

Cuando se actualiza la imagen, la aplicación web se actualizan automáticamente con la nueva imagen.

## <a name="next-steps"></a>Pasos siguientes

* [What is Azure Web App for Containers?](./app-service-linux-intro.md) (¿Qué es Azure Web App for Containers?)
* [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/)
* [Uso de .NET Core en Azure Web Apps for Containers](quickstart-dotnetcore.md)
* [Uso de Ruby en Azure Web Apps for Containers](quickstart-ruby.md)
* [Cómo usar una imagen personalizada de Docker para Azure Web Apps for Containers](quickstart-custom-docker-image.md)
* [Preguntas más frecuentes sobre Web App for Containers de Azure App Service ](./app-service-linux-faq.md) 
* [Administración de Web Apps for Containers mediante la CLI de Azure 2.0](./app-service-linux-cli.md)
