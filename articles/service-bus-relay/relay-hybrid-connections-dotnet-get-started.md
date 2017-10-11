---
title: "Introducción a las conexiones híbridas de Azure Relay en .NET | Microsoft Docs"
description: "Escriba una aplicación de consola en C# para Conexiones híbridas de Azure Relay."
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 07/07/2017
ms.author: sethm
ms.openlocfilehash: 1af23bfd46dd7d3781505473f7c1d86e65ea9bc7
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="get-started-with-relay-hybrid-connections"></a>Introducción a las conexiones híbridas de Relay
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Este tutorial proporciona una introducción a las [conexiones híbridas de Azure Relay](relay-what-is-it.md#hybrid-connections) y muestra la forma cómo usar .NET para crear una aplicación de escucha correspondiente envía mensajes a una aplicación de escucha correspondiente. 

## <a name="what-will-be-accomplished"></a>Lo que se logrará
Dado que las conexiones híbridas requieren un cliente y un componente de servidor, se van a crear dos aplicaciones de consola en este tutorial. Estos son los pasos que se deben seguir:

1. Creación de un espacio de nombres de Relay mediante Azure Portal.
2. Creación de una conexión híbrida en dicho espacio de nombres mediante Azure Portal.
3. Escritura de una aplicación de consola de servidor (de escucha) para recibir mensajes.
4. Escritura de una aplicación de consola de cliente (remitente) para enviar mensajes.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, debe cumplir los siguientes requisitos previos:

1. [Visual Studio 2015 o posterior](http://www.visualstudio.com). En los ejemplos de este tutorial se usa Visual Studio 2017.
2. Una suscripción de Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Creación de un espacio de nombres mediante Azure Portal
Si ya ha creado un espacio de nombres de Relay, vaya a la sección [Creación de una conexión híbrida mediante Azure Portal](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Creación de una conexión híbrida mediante Azure Portal
Si ya ha creado una conexión híbrida, vaya a la sección [Creación de una aplicación de servidor](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Creación de una aplicación de servidor (agente de escucha)
Para escuchar y recibir mensajes desde Relay, escribiremos una aplicación de consola en C# con Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Creación de una aplicación de cliente (remitente)
Para enviar mensajes a Relay, escribiremos una aplicación de consola en C# con Visual Studio.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Ejecución de las aplicaciones
1. Ejecute la aplicación de servidor.
2. Ejecute la aplicación de cliente y escriba algún texto.
3. Asegúrese de que la consola de aplicación de servidor genera el texto que escribió en la aplicación de cliente.

![running-applications](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Enhorabuena, ha creado una aplicación de conexiones híbridas de extremo a extremo.

## <a name="next-steps"></a>Pasos siguientes:
* [Preguntas más frecuentes acerca de Relay](relay-faq.md)
* [Creación de un espacio de nombres](relay-create-namespace-portal.md)
* [Introducción a Node](relay-hybrid-connections-node-get-started.md)

