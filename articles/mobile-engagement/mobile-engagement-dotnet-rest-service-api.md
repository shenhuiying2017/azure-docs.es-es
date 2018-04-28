---
title: Uso de la API de REST para acceder a las API del servicio Azure Mobile Engagement
description: Describe cómo usar las API de REST de Mobile Engagement para acceder a las API del servicio Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: ''
ms.assetid: e8df4897-55ee-45df-b41e-ff187e3d9d12
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: ea7fe6a58d8173c410573e3aa978cb1975ab5da7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="using-rest-to-access-azure-mobile-engagement-service-apis"></a>Uso de REST para acceder a las API del servicio Azure Mobile Engagement
> [!IMPORTANT]
> Azure Mobile Engagement se retira el 31 de marzo de 2018. Esta página se eliminará poco después.
> 

Azure Mobile Engagement proporciona la [API de REST Azure Mobile Engagement](https://msdn.microsoft.com/library/azure/mt683754.aspx) para administrar dispositivos, campañas de cobertura e inserción, etc.

> [!NOTE]
> El servicio Azure Mobile Engagement se retirará en marzo de 2018 y actualmente solo está disponible para los clientes existentes. Para más información, consulte [Mobile Engagement](https://azure.microsoft.com/services/mobile-engagement/).

Si no desea usar las API de REST directamente, también proporcionamos un [archivo de Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json) que puede utilizar con las herramientas para generar los SDK para su idioma preferido. Se recomienda usar la herramienta [AutoRest](https://github.com/Azure/AutoRest) para generar el SDK a partir de nuestro archivo de Swagger. De forma similar, hemos creado un SDK para .NET que permite interactuar con estas API con un contenedor de C# sin tener que realizar la negociación de token de autenticación y la actualización usted mismo. Vea el [ejemplo del SDK de .NET de la API del servicio](mobile-engagement-dotnet-sdk-service-api.md) para obtener información sobre cómo usar el SDK de .NET de la API.
