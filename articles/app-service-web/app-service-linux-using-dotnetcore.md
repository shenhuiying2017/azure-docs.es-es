---
title: Uso de .NET Core en Azure App Service Web Apps en Linux | Microsoft Docs
description: Uso de .NET Core en Azure App Service Web Apps en Linux.
keywords: "azure app service, aplic. web, dotnet, núcleo, linux, oss"
services: app-service
documentationCenter: 
authors: aelnably
manager: wpickett
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 317d6980d304cc503cc43358c4b91459d4abd1ba
ms.openlocfilehash: a54b7413c2eb6ae67b7424a8966d0cd82cd1ce6a


---

# <a name="using-net-core-in-web-apps-on-linux"></a>Uso de .NET Core en Web Apps en Linux #

Con la última actualización de nuestro back-end, se introdujo la compatibilidad con .NET Core v.1.0. Al establecer la configuración de la aplicación web de Linux, puede cambiar la pila de la aplicación.


## <a name="using-xplat-cli"></a>Uso de la CLI multiplataforma ##

Mediante la CLI multiplataforma de Azure más reciente, puede usar el comando **azure webapp config set** para cambiar la pila de la aplicación. A continuación se muestra un ejemplo de ello:

        azure webapp config set --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup --netframeworkversion v1.0 --appcommandline aspnetcore.dll

Tenga en cuenta que el archivo **aspnetcore.dll** es la dll de la aplicación. Puede utilizar el nombre que desee en la aplicación.

Así se cargará la imagen de .NET Core y se iniciará la aplicación web. Para comprobar que la configuración se ha realizado correctamente, use **azure webapp config show**. A continuación se muestra un ejemplo de ello:

        azure webapp config show --name ContosoAppServicePlan --resource-group ContosoLinuxAzureResourceGroup

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a App Service en Linux](./app-service-linux-intro.md) 
* [¿Qué es App Service en Linux?](app-service-linux-intro.md)
* [Creación de aplicaciones web en App Service en Linux](./app-service-linux-how-to-create-a-web-app.md)
* [CLI multiplataforma de Azure Web App](app-service-web-app-azure-resource-manager-xplat-cli.md)



<!--HONumber=Nov16_HO3-->


