---
title: Qué es Azure SignalR | Microsoft Docs
description: Introducción a Azure SignalR Service.
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/17/2018
ms.author: wesmc
ms.openlocfilehash: bc144fb1d7db9251871e7e181b012417a32de7e6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/08/2018
ms.locfileid: "33868116"
---
# <a name="what-is-azure-signalr-service"></a>Qué es Azure SignalR Service

El servicio Microsoft Azure SignalR se encuentra actualmente en [versión preliminar pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure SignalR Service es un servicio de Azure basado en [ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction). ASP.NET Core SignalR es una [biblioteca de código abierto](https://github.com/aspnet/signalr) que simplifica la incorporación de funcionalidades web en tiempo real a las aplicaciones a través de HTTP. Esta funcionalidad en tiempo real permite que el servidor web inserte las actualizaciones de contenido a los clientes conectados. Como resultado, los clientes se actualizan sin necesidad de sondear el servidor ni de enviar nuevas solicitudes HTTP para las actualizaciones.

En este artículo se proporciona una introducción a Azure SignalR Service. Si desea empezar, comience con la [guía de inicio rápido de ASP.NET Core](signalr-quickstart-dotnet-core.md).

## <a name="what-is-signalr-service-used-for"></a>Para qué se usa SignalR Service 

Hay muchos tipos de aplicación que requieren actualizaciones de contenido en tiempo real. Los siguientes tipos de aplicación de ejemplo son buenos candidatos para usar Azure SignalR Service:

* Aplicaciones que requieren actualizaciones desde el servidor con mucha frecuencia. Algunos ejemplos son juegos, redes sociales, aplicaciones de votación, subastas, mapas y GPS.
* Paneles y aplicaciones de supervisión. Los ejemplos incluyen paneles empresariales, actualizaciones de venta instantáneas o alertas de viaje.
* Aplicaciones de colaboración. Las aplicaciones de pizarra y el software de reuniones de equipo son ejemplos de aplicaciones de colaboración.
* Aplicaciones que requieren notificaciones. Redes sociales, correo electrónico, chat, juegos, alertas de viaje y muchas otras aplicaciones utilizan notificaciones.

Internamente, SignalR es una abstracción de una serie de técnicas que se usa para compilar aplicaciones web en tiempo real. [WebSockets](https://wikipedia.org/wiki/WebSocket) es el transporte óptimo, pero otras técnicas, como [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) y Long Polling, se usan cuando otras opciones no están disponibles. SignalR detecta e inicializa automáticamente el transporte adecuado en función de las características admitidas en el servidor y el cliente.

## <a name="developing-signalr-apps"></a>Desarrollo de aplicaciones SignalR

Actualmente, hay dos versiones de SignalR que se puede usar con las aplicaciones web: SignalR para ASP.NET y ASP.NET SignalR Core, que es la versión más reciente. Azure SignalR Service es un servicio administrado de Azure basado en ASP.NET Core SignalR. 

ASP.NET Core SignalR es una reescritura de la versión anterior. Como resultado, ASP.NET Core SignalR no es compatible con la versión anterior de SignalR. Las API y los comportamientos son diferentes. El SDK de ASP.NET Core SignalR es .NET Standard, por lo que puede usarse con .NET Framework. Sin embargo, debe utilizar las nuevas API en lugar de las antiguas. Si usa SignalR y desea pasarse a ASP.NET Core SignalR, o Azure SignalR Service, debe cambiar el código para gestionar las diferencias en las API.

Con Azure SignalR Service, el componente de servidor de ASP.NET Core SignalR se hospeda en Azure. Sin embargo, dado que la tecnología se basa en ASP.NET Core, tiene la capacidad de ejecutar la aplicación web real en varias plataformas (Windows, Linux y MacOS), a la vez que la hospeda en [Azure App Service](../app-service/app-service-web-overview.md), [IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache), [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index). También puede utilizar un hospedaje autosuficiente en su propio proceso.

Si los objetivos de la aplicación incluyen: compatibilidad con la funcionalidad más reciente para actualizar los clientes web con las actualizaciones de contenido en tiempo real, ejecución en varias plataformas (Azure, Windows, Linux y MacOS), y hospedaje en entornos diferentes, la mejor opción sería aprovechar Azure SignalR Service.


## <a name="why-not-deploy-signalr-myself"></a>Por qué no implementar SignalR usted mismo

Sigue siendo un enfoque válido implementar su propia aplicación web de Azure que admita ASP.NET Core SignalR como componente de back-end en la aplicación web general.

Una de las principales razones para usar Azure SignalR Service es su sencillez. Con Azure SignalR Service, no es necesario encargarse de problemas como el rendimiento, la escalabilidad y la disponibilidad. Estos problemas quedarán bajo su control con un contrato de nivel de servicio del 99,9 %.

Además, WebSockets suele ser la técnica de preferencia para admitir las actualizaciones de contenido en tiempo real. Sin embargo, el equilibrio de carga de un gran número de conexiones persistentes de WebSocket se convierte en un problema complicado de resolver cuando se escala. Las soluciones comunes hacen uso del equilibrio de carga de DNS, equilibradores de carga de hardware y equilibrio de carga de software. Azure SignalR Service controla este problema automáticamente.

Otra razón puede ser que en realidad no deba cumplir con ningún requisito para hospedar una aplicación web. La lógica de la aplicación web puede aprovechar la [informática sin servidor](https://azure.microsoft.com/overview/serverless-computing/). Por ejemplo, quizás el código solo se hospede y ejecuta a petición con desencadenadores de [Azure Functions](https://docs.microsoft.com/azure/azure-functions/). Este escenario puede ser engañoso, porque el código solo se ejecuta a petición y no mantiene conexiones prolongadas con los clientes. Azure SignalR Service puede encargarse de esta situación, ya que el servicio ya administra las conexiones por usted.

## <a name="how-does-it-scale"></a>Cómo se escala

Es común escalar SignalR con SQL Server, Azure Service Bus o Redis Cache. Azure SignalR Service controla el enfoque de escalado por usted. El rendimiento y el costo es comparable a estos métodos, sin la complejidad de trabajar con estos otros servicios. Lo único que debe hacer es actualizar el número de unidades del servicio. Cada unidad de servicio admite hasta 1000 conexiones de cliente.

## <a name="next-steps"></a>Pasos siguientes
* [Quickstart: Create a chat room with Azure SignalR](signalr-quickstart-dotnet-core.md) (Guía de inicio rápido: Creación de un salón de chat con Azure SignalR)  
  

