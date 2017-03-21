---
title: "Creación de su primera aplicación web de Java en Azure en cinco minutos | Microsoft Docs"
description: "Aprenda lo fácil que es ejecutar aplicaciones web en App Service mediante la implementación de una aplicación de ejemplo."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: e48e03e86a325b8f39809a49cdd19820dfa78bdc
ms.lasthandoff: 03/10/2017


---
# <a name="create-your-first-java-web-app-in-azure-in-five-minutes"></a>Creación de su primera aplicación web de Java en Azure en cinco minutos
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)]

Este tutorial de inicio rápido le ayuda a implementar su primera aplicación web de Java en [Azure App Service](../app-service/app-service-value-prop-what-is.md) en tan solo unos minutos.

Antes de empezar, asegúrese de que [la CLI de Azure esté instalada](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) en la máquina.

## <a name="create-a-java-web-app-in-azure"></a>Creación de una aplicación web de Java en Azure
2. Inicie sesión en Azure, para lo que debe ejecutar `az login` y seguir las instrucciones de la pantalla.
   
    ```azurecli
    az login
    ```
   
3. Cree un [grupo de recursos](../azure-resource-manager/resource-group-overview.md). Aquí es donde se colocan todos los recursos de Azure que quiere administrar juntos, como la aplicación web y su back-end de SQL Database.

    ```azurecli
    az group create --location "West Europe" --name myResourceGroup
    ```

    Para ver los posibles valores que se pueden usar para `---location`, utilice el comando `az appservice list-locations` de la CLI de Azure.

3. Cree un [plan de App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) de nivel "Gratis". 

    ```azurecli
    az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
    ```

4. Cree una nueva aplicación web con un nombre único en `<app_name>`.

    ```azurecli
    az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
    ```

4. Implemente una aplicación de Java de ejemplo de GitHub.

    ```azurecli
    az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
    --repo-url "https://github.com/azure-appservice-samples/JavaCoffeeShopTemplate.git" --branch master --manual-integration 
    ```


5. Para ver la aplicación en ejecución en Azure, ejecute este comando:

    ```azurecli
    az appservice web browse --name <app_name> --resource-group myResourceGroup
    ```

Enhorabuena, la primera aplicación web de Java se está ejecutando en directo en Azure App Service.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Pasos siguientes

Explore los [scripts de la CLI de aplicaciones web](app-service-cli-samples.md) previamente creados.

