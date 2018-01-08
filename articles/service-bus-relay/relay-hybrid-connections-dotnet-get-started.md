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
ms.date: 12/15/2017
ms.author: sethm
ms.openlocfilehash: 3667d7f4a74ac7c43f8f443aed2beacafd909dbd
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="get-started-with-relay-hybrid-connections"></a>Introducción a Conexiones híbridas de Relay
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Este tutorial proporciona una introducción a [Conexiones híbridas de Azure Relay](relay-what-is-it.md#hybrid-connections). Obtenga información acerca de cómo usar Microsoft .NET para crear una aplicación cliente que envíe mensajes a una aplicación de escucha correspondiente. 

## <a name="what-will-be-accomplished"></a>Lo que se logrará
Las conexiones híbridas requieren un componente de cliente y un componente de servidor. En este tutorial, se siguen estos pasos para crear dos aplicaciones de consola:

1. Creación de un espacio de nombres de Relay mediante Azure Portal.
2. Creación de una conexión híbrida en dicho espacio de nombres mediante Azure Portal.
3. Escritura de una aplicación de consola de servidor (de escucha) para recibir mensajes.
4. Escritura de una aplicación de consola de cliente (remitente) para enviar mensajes.

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial, debe cumplir los siguientes requisitos previos:

* [Visual Studio 2015 o posterior](http://www.visualstudio.com). En los ejemplos de este tutorial se usa Visual Studio 2017.
* Una suscripción de Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-by-using-the-azure-portal"></a>1. Creación de un espacio de nombres mediante Azure Portal
Si ya ha creado un espacio de nombres de Relay, vaya a la sección [Creación de una conexión híbrida mediante Azure Portal](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-by-using-the-azure-portal"></a>2. Creación de una conexión híbrida mediante Azure Portal
Si ya ha creado una conexión híbrida, vaya a la sección [Creación de una aplicación de servidor](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Creación de una aplicación de servidor (agente de escucha)
En Visual Studio, escriba una aplicación de consola en C# para escuchar y recibir mensajes de Relay.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Creación de una aplicación de cliente (remitente)
En Visual Studio, escriba una aplicación de consola en C# para enviar mensajes a Relay.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Ejecución de las aplicaciones
1. Ejecute la aplicación de servidor.
2. Ejecute la aplicación de cliente y escriba algún texto.
3. Asegúrese de que la consola de aplicación de servidor muestra el texto que se escribió en la aplicación cliente.

![running-applications](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Enhorabuena, ha creado una aplicación de conexiones híbridas de extremo a extremo.

## <a name="next-steps"></a>pasos siguientes

* [Preguntas más frecuentes acerca de Relay](relay-faq.md)
* [Creación de un espacio de nombres](relay-create-namespace-portal.md)
* [Introducción a Node](relay-hybrid-connections-node-get-started.md)

