---
title: Uso de .NET Core en Azure App Service Web Apps en Linux | Microsoft Docs
description: Uso de .NET Core en Azure App Service Web Apps en Linux.
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
ms.date: 02/16/2017
ms.author: aelnably;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 769de52a85d1d5078b2ba583e94cabd22b0fde65
ms.lasthandoff: 02/17/2017


---

# <a name="using-net-core-in-web-apps-on-linux"></a>Uso de .NET Core en Web Apps en Linux #

Con la última actualización de nuestro back-end, se introdujo la compatibilidad con .NET Core v.1.0. Al establecer la configuración de la aplicación web de Linux, puede cambiar la pila de la aplicación.


## <a name="using-xplat-cli"></a>Uso de la CLI multiplataforma ##

Mediante la CLI multiplataforma de Azure más reciente, puede usar el comando **azure webapp config set** para cambiar la pila de la aplicación. Aquí tiene un ejemplo:

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

El archivo **aspnetcore.dll** es el dll de la aplicación. Puede utilizar el nombre que desee en la aplicación.

Esto carga la imagen de .NET Core e inicia su aplicación web. Para comprobar que la configuración se ha realizado correctamente, use **azure webapp config show**. Aquí tiene un ejemplo:

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>Pasos siguientes
* [¿Qué es App Service en Linux?](app-service-linux-intro.md)
* [Creación de aplicaciones web en App Service en Linux](./app-service-linux-how-to-create-a-web-app.md)
* [CLI multiplataforma de Azure Web App](app-service-web-app-azure-resource-manager-xplat-cli.md)
* [Preguntas más frecuentes sobre Azure App Service Web Apps en Linux](app-service-linux-faq.md)
