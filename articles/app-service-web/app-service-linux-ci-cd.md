---
title: "Implementación continua de Docker Hub con Azure Web App on Linux | Microsoft Docs"
description: "En este artículo se describe cómo configurar una implementación continua en Azure Web App en Linux."
keywords: azure app service, linux, oss
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 68e11f07520bbe8da6812a34fe95708fc9e932ac
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="docker-hub-continuous-deployment-with-azure-web-app-on-linux"></a>Implementación continua de Docker Hub con Azure Web App en Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

En este tutorial, configurará una implementación continua para una imagen de contenedor personalizada desde [Docker Hub](https://hub.docker.com).

## <a name="step-1---log-in-to-azure"></a>Paso 1: Inicio de sesión en Azure

Inicie sesión en Azure Portal: http://portal.azure.com.

## <a name="step-2---enable-docker-hub-continuous-deployment"></a>Paso 2: Habilitación de la implementación continua de Docker Hub

En **[Azure Portal](https://portal.azure.com/)**, haga clic en la opción **App Service** del lado izquierdo de la página.

Haga clic en el nombre de la aplicación para la que desea configurar una implementación continua de Docker Hub.

En la **configuración de la aplicación**, agregue una aplicación llamada `DOCKER_ENABLE_CI` con el valor `true`.

![insert image of app setting](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="step-3---add-a-web-hook-to-docker-hub"></a>Paso 3: Incorporación de un enlace web en Docker Hub

En la página de Docker Hub, haga clic en **Webhooks** y, luego, en **CREAR NUEVO WEBHOOK**.

![insert image of adding webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

Para la URL del Webhook, debe tener el siguiente punto de conexión: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

![insert image of adding webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

Puede obtener `publishingusername` y `publishingpwd` descargando el perfil de publicación de la aplicación web mediante Azure Portal.

![insert image of adding webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

Cuando se actualiza la imagen, la aplicación web se actualizan automáticamente con la nueva imagen.

## <a name="next-steps"></a>Pasos siguientes
* [¿Qué es Web App on Linux de Azure?](./app-service-linux-intro.md)
* [Creación de aplicaciones en Web App on Linux de Azure](./app-service-linux-how-to-create-web-app.md)
* [Uso de la configuración de PM2 para Node.js en Web App on Linux de Azure](app-service-linux-using-nodejs-pm2.md)
* [Uso de .NET Core en Web App on Linux de Azure](app-service-linux-using-dotnetcore.md)
* [Uso de Ruby en Web App on Linux de Azure](app-service-linux-ruby-get-started.md)
* [Uso de una imagen personalizada de Docker para Web App on Linux de Azure](./app-service-linux-using-custom-docker-image.md)
* [Preguntas más frecuentes sobre Web App on Linux de Azure App Service](./app-service-linux-faq.md) 




