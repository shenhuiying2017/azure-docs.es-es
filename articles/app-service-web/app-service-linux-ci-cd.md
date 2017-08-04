---
title: "Implementación continua con Aplicación web de Azure en Linux | Microsoft Docs"
description: "En este artículo se describe cómo configurar una implementación continua en Azure Web App en Linux."
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
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 6b6c173c6c4bb3f670c54208c80e6d966a1f396e
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="continuous-deployment-with-azure-web-app-on-linux"></a>Implementación continua con Aplicación web de Azure en Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

En este tutorial, configurará una implementación continua para una imagen de contenedor personalizada desde los repositorios administrados de [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/) o [Docker Hub](https://hub.docker.com).

## <a name="step-1---sign-in-to-azure"></a>Paso 1: Inicio de sesión en Azure

Inicie sesión en Azure Portal: http://portal.azure.com/.

## <a name="step-2---enable-docker-hub-continuous-deployment"></a>Paso 2: Habilitación de la implementación continua de Docker Hub

En **[Azure Portal](https://portal.azure.com/)**, haga clic en la opción **App Service** del lado izquierdo de la página.

Haga clic en el nombre de la aplicación para la que desea configurar una implementación continua de Docker Hub.

En la **configuración de la aplicación**, agregue una aplicación llamada `DOCKER_ENABLE_CI` con el valor `true`.

![insert image of app setting](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---prepare-webhook-url"></a>Paso 3: Preparación de la dirección URL de webhook

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
* [¿Qué es Web App on Linux de Azure?](./app-service-linux-intro.md)
* [Creación de aplicaciones en Web App on Linux de Azure](./app-service-linux-how-to-create-web-app.md)
* [Azure Container Registry](https://azure.microsoft.com/en-us/services/container-registry/)
* [Uso de la configuración de PM2 para Node.js en Web App on Linux de Azure](app-service-linux-using-nodejs-pm2.md)
* [Uso de .NET Core en Web App on Linux de Azure](app-service-linux-using-dotnetcore.md)
* [Uso de Ruby en Web App on Linux de Azure](app-service-linux-ruby-get-started.md)
* [Uso de una imagen personalizada de Docker para Web App on Linux de Azure](./app-service-linux-using-custom-docker-image.md)
* [Preguntas más frecuentes sobre Web App on Linux de Azure App Service](./app-service-linux-faq.md) 




