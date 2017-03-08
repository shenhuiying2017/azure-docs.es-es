---
title: "Implementación en Azure App Services con Visual Studio 2015 | Microsoft Docs"
description: "En este artículo se describe cómo implementar una aplicación Web, aplicación de API o aplicación móvil para Azure Government mediante Visual Studio 2015 y Azure SDK."
services: azure-government
cloud: gov
documentationcenter: 
author: sdubeymsft
manager: zakramer
ms.assetid: 8f9a3700-b9ee-43b7-b64d-2e6c3b57d4c0
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/03/2016
ms.author: sdubeymsft
translationtype: Human Translation
ms.sourcegitcommit: 1cb57e5156dab976599ddfa9a58f26ca8ef1ee0e
ms.openlocfilehash: 159c891dedb66dfaa238981b395b4bba074c5716
ms.lasthandoff: 02/22/2017


---
# <a name="deploy-to-azure-app-services-using-visual-studio-2015"></a>Implementación en Azure App Services con Visual Studio 2015
En este artículo describe cómo implementar una aplicación de Azure App Services (aplicación de API, aplicación web, aplicación móvil) en Azure Government mediante Visual Studio 2015.

## <a name="prerequisites"></a>Requisitos previos
* Consulte los [Requisitos previos de Visual Studio] (../app-service-api/app-service-api-dotnet-get-started.md#prerequisites) para instalar y configurar Visual Studio 2015 y Azure SDK.
* Siga [estas instrucciones] (documentation-government-manage-subscriptions.md) para configurar Visual Studio para conectarse a la cuenta de Azure Government.

## <a name="open-app-project-in-visual-studio"></a>Apertura del proyecto de aplicación en Visual Studio
* Abra el proyecto o solución existente en Visual Studio, cree un proyecto siguiendo [estas instrucciones] (../app-service-web/web-sites-dotnet-get-started.md#create-a-web-application) o descargue la aplicación de ejemplo siguiendo [estos pasos] (../app-service-api/app-service-api-dotnet-get-started.md#download-the-sample-application).
* Ejecute la aplicación en Visual Studio para asegurarse de que funciona localmente.

## <a name="deploy-to-azure-government"></a>Implementación en Azure Government
* Una vez que **Visual Studio esté configurado para conectarse a la cuenta de Azure Government** (ya se hecho en la sección de requisitos previos), las instrucciones para implementar en App Services son exactamente iguales que para la versión pública de Azure.
* Para implementar la aplicación, siga [estos pasos] (../app-service-api/app-service-api-dotnet-get-started.md#a-idcreateapiappa-create-an-api-app-in-azure-and-deploy-code-to-it).

### <a name="references"></a>Referencias
* [Implementación de una aplicación web creada con ASP.NET en Azure App Service mediante Visual Studio] (../app-service-web/web-sites-dotnet-get-started.md)
* Para que conocer otras formas de implementar, consulte [Documentación de implementación de Azure App Service] (.. /App-Service-Web/Web-Sites-Deploy.MD)
* Para obtener documentación de servicio de aplicación general, vea [App Service: documentación de API Apps] (../app-service-api/index.md)

## <a name="next-steps"></a>Pasos siguientes
Para obtener información complementaria y actualizaciones, suscríbase al [blog de Microsoft Azure Government](https://blogs.msdn.microsoft.com/azuregov/).

