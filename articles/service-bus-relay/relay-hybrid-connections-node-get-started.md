---
title: "Introducción a las conexiones híbridas de Azure Relay en un nodo | Microsoft Docs"
description: "Escriba una aplicación de consola en Node.js para Conexiones híbridas de Azure Relay."
services: service-bus-relay
documentationcenter: node
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 07/07/2017
ms.author: sethm
ms.openlocfilehash: c3bfc45969f250059988129f532edd12dfe3dcfe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-relay-hybrid-connections"></a>Introducción a Conexiones híbridas de Relay

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Este tutorial proporciona una introducción a [Conexiones híbridas de Azure Relay](relay-what-is-it.md#hybrid-connections) y muestra la forma cómo usar Node.js para crear una aplicación de escucha correspondiente envía mensajes a una aplicación de escucha correspondiente. 

## <a name="what-will-be-accomplished"></a>Lo que se logrará

Dado que Conexiones híbridas requiere un cliente y un componente de servidor, en este tutorial crearemos dos aplicaciones de consola. Estos son los pasos que se deben seguir:

1. Creación de un espacio de nombres de Relay mediante Azure Portal.
2. Creación de una conexión híbrida mediante Azure Portal.
3. Escritura de una aplicación de consola de servidor para recibir mensajes.
4. Escritura de una aplicación de consola de cliente para enviar mensajes.

## <a name="prerequisites"></a>Requisitos previos

1. [Node.js](https://nodejs.org/en/).
2. Una suscripción de Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Creación de un espacio de nombres mediante Azure Portal

Si ya tiene creado un espacio de nombres de Relay, vaya a la sección [Creación de una conexión híbrida mediante Azure Portal](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Creación de una conexión híbrida mediante Azure Portal

Si ya tiene una conexión híbrida creada, vaya a la sección [Creación de una aplicación de servidor (agente de escucha)](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Creación de una aplicación de servidor (agente de escucha)

Para escuchar y recibir mensajes desde Relay, escribiremos una aplicación de consola en Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Creación de una aplicación de cliente (remitente)

Para enviar mensajes a Relay, escribiremos una aplicación de consola en Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Ejecución de las aplicaciones

1. Ejecute la aplicación de servidor: en un símbolo del sistema de Node.js escriba `node listener.js`.
2. Ejecute la aplicación de cliente: en un símbolo del sistema de Node.js escriba `node sender.js` y escriba texto.
3. Asegúrese de que la consola de aplicación de servidor genera el texto que escribió en la aplicación de cliente.

![running-applications](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Ya ha creado una aplicación de Conexiones híbridas de un extremo a otro.

## <a name="next-steps"></a>Pasos siguientes:

* [Preguntas más frecuentes acerca de Relay](relay-faq.md)
* [Creación de un espacio de nombres](relay-create-namespace-portal.md)
* [Introducción a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introducción a Node](relay-hybrid-connections-node-get-started.md)

