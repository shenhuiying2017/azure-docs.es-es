---
title: 'Ejemplos de la CLI de Azure: Azure SignalR Service | Microsoft Docs'
description: 'Ejemplos de la CLI de Azure: Azure SignalR Service'
services: signalr
documentationcenter: signalr
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 6e76ee89160c84ff0f1033590d14c288f023f201
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33779861"
---
# <a name="azure-cli-samples"></a>Ejemplos de la CLI de Azure

En la tabla siguiente se incluyen vínculos a scripts de Bash para Azure SignalR Service creados con la CLI de Azure.

| | |
|-|-|
|**Creación**||
| [Creación de una instancia nueva de SignalR Service y un nuevo grupo de recursos](scripts/signalr-cli-create-service.md) | Crea un nuevo recurso de Azure SignalR Service en un nuevo grupo de recursos con un nombre aleatorio.  |
|**Integración**||
| [Creación de una instancia nueva de SignalR Service y una aplicación web configurada para usar SignalR](scripts/signalr-cli-create-with-app-service.md) | Crea un nuevo recurso de Azure SignalR Service en un nuevo grupo de recursos con un nombre aleatorio. También agrega una nueva aplicación web y un nuevo plan de App Service para hospedar una aplicación web ASP.NET Core que usa la instancia de SignalR Service. La aplicación web se configura con una configuración de aplicación para conectar con el nuevo recurso de SignalR Service. |
| [Creación de una instancia nueva de SignalR Service y una aplicación web configurada para usar SignalR y GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Crea un nuevo recurso de Azure SignalR Service en un nuevo grupo de recursos con un nombre aleatorio. También agrega una nueva aplicación web de Azure y un nuevo plan de hospedaje para hospedar una aplicación web ASP.NET Core que usa la instancia de SignalR Service. La aplicación web está configurada con los valores de la aplicación para la cadena de conexión al nuevo recurso de SignalR Service y los secretos de cliente para admitir la [autenticación de GitHub](https://developer.github.com/v3/guides/basics-of-authentication/), como se muestra en el [tutorial de autenticación](signalr-authenticate-oauth.md). La aplicación web también está configurada para usar un origen de implementación de repositorio de Git local. |
| | |
