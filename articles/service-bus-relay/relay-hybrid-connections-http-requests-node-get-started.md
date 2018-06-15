---
title: Introducción a las solicitudes HTTP de Conexiones híbridas de Azure Relay en Node | Microsoft Docs
description: Escriba una aplicación de consola en Node.js para solicitudes HTTP de Conexiones híbridas de Azure Relay en Node.
services: service-bus-relay
documentationcenter: node
author: clemensv
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 05/02/2018
ms.author: clemensv
ms.openlocfilehash: 2bc923650425c76562161dd6f44f3a5722b5cefe
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896632"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Introducción a las solicitudes HTTP de Conexiones híbridas de Relay en Node

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Este tutorial proporciona una introducción a las solicitudes HTTP de [Conexiones híbridas de Azure Relay](relay-what-is-it.md#hybrid-connections) y muestra cómo usar Node.js para crear una aplicación cliente que envíe mensajes a una aplicación de escucha correspondiente.

## <a name="what-will-be-accomplished"></a>Lo que se logrará

Dado que Conexiones híbridas requiere un cliente y un componente de servidor, en este tutorial se crearán dos aplicaciones de consola. Estos son los pasos que se deben seguir:

1. Creación de un espacio de nombres de Relay mediante Azure Portal.
2. Creación de una conexión híbrida mediante Azure Portal.
3. Escritura de una aplicación de consola de servidor para recibir mensajes.
4. Escritura de una aplicación de consola de cliente para enviar mensajes.

## <a name="prerequisites"></a>requisitos previos

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

Para escuchar y recibir mensajes desde Relay, escriba una aplicación de consola en Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-http-requests-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Creación de una aplicación de cliente (remitente)

Para enviar mensajes a Relay, puede usar cualquier cliente HTTP o escribir una aplicación de consola en Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-http-requests-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Ejecución de las aplicaciones

1. Ejecute la aplicación de servidor: en un símbolo del sistema de Node.js escriba `node listener.js`.
2. Ejecute la aplicación de cliente: en un símbolo del sistema de Node.js escriba `node sender.js` y escriba texto.
3. Asegúrese de que la consola de aplicación de servidor genera el texto que escribió en la aplicación de cliente.

Ya ha creado una aplicación de Conexiones híbridas de un extremo a otro.

## <a name="next-steps"></a>Pasos siguientes

* [Preguntas más frecuentes acerca de Relay](relay-faq.md)
* [Creación de un espacio de nombres](relay-create-namespace-portal.md)
* [Introducción a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introducción a Node](relay-hybrid-connections-node-get-started.md)

