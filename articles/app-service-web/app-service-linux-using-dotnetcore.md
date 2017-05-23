---
title: Uso de .NET Core en Web App on Linux de Azure App Service | Microsoft Docs
description: Uso de .NET Core en Web App on Linux de Azure App Service
keywords: "azure app service, aplic. web, dotnet, núcleo, linux, oss"
services: app-service
documentationCenter: 
authors: aelnably
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: aelnably;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 3608ddf86c3d8010b577e6f745dcd5cef016acd9
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---

# <a name="using-net-core-in-azure-web-app-on-linux"></a>Uso de .NET Core en Web App on Linux de Azure #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


Con la última actualización de nuestro back-end, se introdujo la compatibilidad con .NET Core v.1.0. Al establecer la configuración de la aplicación web de Linux, puede cambiar la pila de la aplicación.


## <a name="using-the-azure-cli"></a>Uso de la CLI de Azure ##

En la [interfaz de la línea de comandos (CLI) de Azure más reciente](https://docs.microsoft.com/cli/azure/install-azure-cli), puede usar el comando **azure webapp config set** para cambiar la pila de aplicaciones. Este es un ejemplo:

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

El archivo **aspnetcore.dll** es el dll de la aplicación. Puede utilizar el nombre que desee en la aplicación.

Esto carga la imagen de .NET Core e inicia su aplicación web. Para comprobar que la configuración se ha realizado correctamente, use **azure webapp config show**. Aquí tiene un ejemplo:

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>Pasos siguientes
* [¿Qué es Web App on Linux de Azure?](app-service-linux-intro.md)
* [Creación de aplicaciones web en Web App on Linux de Azure](./app-service-linux-how-to-create-web-app.md)
* [CLI multiplataforma de Azure Web App](app-service-web-app-azure-resource-manager-xplat-cli.md)
* [Preguntas más frecuentes sobre Web App on Linux de Azure App Service](app-service-linux-faq.md)
