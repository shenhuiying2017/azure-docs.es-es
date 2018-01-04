---
title: "Ejecución de una imagen personalizada de Docker Hub en Azure Web Apps for Containers | Microsoft Docs"
description: "Cómo usar una imagen personalizada de Docker para Azure Web App for Containers."
keywords: "azure app service, aplicación web, linux, docker, contenedor"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/02/2017
ms.author: cephalin;wesmc
ms.custom: mvc
ms.openlocfilehash: a95a8435e4ecef201ad0f6d9ecda68e94f06ea80
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="run-a-custom-docker-hub-image-in-azure-web-app-for-containers"></a>Ejecución de una imagen personalizada de Docker Hub en Azure Web Apps for Containers

App Service proporciona pilas de aplicaciones predefinidas en Linux con compatibilidad para versiones específicas, como PHP 7.0 y Node.js 4.5. También puede usar una imagen personalizada de Docker para ejecutar la aplicación web en una pila de aplicaciones aún sin definir en Azure. En esta guía de inicio rápido se muestra cómo crear una aplicación web e implementar en ella [una imagen Nginx de Docker oficial](https://hub.docker.com/r/_/nginx/). Creará la aplicación web con la [CLI de Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

![Aplicación de ejemplo que se ejecuta en Azure](media/quickstart-custom-docker-image/hello-world-in-browser.png)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-container"></a>Creación de una instancia de Web App for Containers

Cree una [aplicación web](../app-service-web-overview.md) en el plan de App Service `myAppServicePlan` con el comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). No olvide reemplazar `<app name>` por un nombre de aplicación único.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name nginx
```

En el comando anterior, `--deployment-container-image-name` apunta a la imagen pública de Docker Hub [https://hub.docker.com/r/_/nginx/](https://hub.docker.com/r/_/nginx/).

Cuando se haya creado la aplicación web, la CLI de Azure mostrará información similar a la del ejemplo siguiente:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="browse-to-the-app"></a>Navegación hasta la aplicación

Use el explorador web para ir a la dirección URL siguiente.

```bash
http://<app_name>.azurewebsites.net
```

![Aplicación de ejemplo que se ejecuta en Azure](media/quickstart-custom-docker-image/hello-world-in-browser.png)

**¡Enhorabuena!** Ha implementado una imagen personalizada de Docker en Web Apps for Containers.

## <a name="next-steps"></a>pasos siguientes

> [!div class="nextstepaction"]
> [Usar una imagen de Docker personalizada](tutorial-custom-docker-image.md)
