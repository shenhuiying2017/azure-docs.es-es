---
title: "Introducción a las conexiones híbridas de Relay | Microsoft Docs"
description: "Cómo escribir una aplicación de consola en Node para conexiones híbridas"
services: service-bus
documentationcenter: node
author: jtaubensee
manager: timlt
editor: 
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 10/28/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0f59faed2edb75e32ea62fa7f1d7013da5f387d8


---
# <a name="get-started-with-relay-hybrid-connections"></a>Introducción a las conexiones híbridas de Relay
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>Lo que se logrará
Dado que las conexiones híbridas requieren un cliente y un componente de servidor, crearemos dos aplicaciones de consola en este tutorial. Estos son los pasos que se deben seguir:

1. Creación de un espacio de nombres de Relay mediante Azure Portal.
2. Creación de una conexión híbrida mediante Azure Portal.
3. Escritura de una aplicación de consola de servidor para recibir mensajes.
4. Escritura de una aplicación de consola de cliente para enviar mensajes.

## <a name="prerequisites"></a>Requisitos previos
1. [Node.js](https://nodejs.org/en/) (En este ejemplo se usa Node 7.0).
2. Una suscripción de Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Creación de un espacio de nombres mediante el Portal de Azure
Si ya tiene creado un espacio de nombres de Relay, vaya a la sección [Creación de una conexión híbrida mediante Azure Portal](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Creación de una conexión híbrida mediante Azure Portal
Si ya tiene una conexión híbrida creada, vaya a la sección [Creación de una aplicación de servidor](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Creación de una aplicación de servidor (agente de escucha)
Para escuchar y recibir mensajes desde Relay, escribiremos una aplicación de consola en Node.js.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Creación de una aplicación de cliente (remitente)
Para enviar mensajes a Relay, escribiremos una aplicación de consola en Node.js.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Ejecución de las aplicaciones
1. Ejecute la aplicación de servidor.
2. Ejecute la aplicación de cliente y escriba algún texto.
3. Asegúrese de que la consola de aplicación de servidor genera el texto que escribió en la aplicación de cliente.

![running-applications](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Enhorabuena, ha creado una aplicación de conexiones híbridas de extremo a extremo.

## <a name="next-steps"></a>Pasos siguientes:
* [Preguntas más frecuentes acerca de Relay](relay-faq.md)
* [Creación de un espacio de nombres](relay-create-namespace-portal.md)
* [Introducción a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introducción a Node](relay-hybrid-connections-node-get-started.md)




<!--HONumber=Nov16_HO2-->


