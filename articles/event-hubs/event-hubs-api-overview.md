---
title: "Introducción a la API de Azure Event Hubs | Microsoft Docs"
description: "Información general sobre las API de Azure Event Hubs disponibles"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 40cd76e1aacb68d6051cae4a3c90a8970f5449f0
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="available-event-hubs-apis"></a>API disponibles de Event Hubs

## <a name="runtime-apis"></a>API de tiempo de ejecución

A continuación, se muestra una descripción de todos los clientes del tiempo de ejecución de Azure Event Hubs disponibles. Aunque algunas de estas bibliotecas también incluyen la funcionalidad de administración limitada, también hay [determinadas bibliotecas](#management-apis) dedicadas a las operaciones de administración. El enfoque central de estas bibliotecas es enviar y recibir mensajes de un centro de eventos.

Consulte la [información adicional](#additional-information) para más detalles sobre el estado actual de cada biblioteca del entorno de ejecución.

| Lenguaje/plataforma | Paquete del cliente | Paquete EventProcessorHost | Repositorio |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | N/D |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Nodo | [NPM](https://www.npmjs.com/package/azure-event-hubs) | N/D | [GitHub](https://github.com/Azure/azure-event-hubs-node) |
| C | N/D | N/D | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Información adicional

#### <a name="net"></a>.NET
El ecosistema de .NET tiene varios entornos de ejecución y, por tanto, hay varias bibliotecas de .NET para Event Hubs. La biblioteca de .NET Standard se puede ejecutar mediante .NET Core o .NET Framework, mientras que la biblioteca de .NET Framework solo puede ejecutarse en un entorno de .NET Framework. Para ampliar la información sobre las versiones de .NET Framework, consulte [Versiones de marcos de trabajo](https://docs.microsoft.com/dotnet/articles/standard/frameworks#framework-versions).

#### <a name="node"></a>Nodo

La biblioteca de Node.js está en versión preliminar y los empleados de Microsoft y colaboradores externos la mantienen como un proyecto secundario. Todas las contribuciones, incluido código fuente, son bienvenidas y se revisarán.

## <a name="management-apis"></a>API de administración

A continuación se proporciona una lista de todas las bibliotecas específicas de administración disponibles actualmente. Ninguna de estas bibliotecas contiene operaciones de entornos de ejecución y son exclusivamente para administrar las entidades de Event Hubs.

| Lenguaje/plataforma | Paquete de administración | Repositorio |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Información general de Event Hubs](event-hubs-what-is-event-hubs.md)
* [Creación de un centro de eventos](event-hubs-create.md)
* [Preguntas más frecuentes sobre Event Hubs](event-hubs-faq.md)