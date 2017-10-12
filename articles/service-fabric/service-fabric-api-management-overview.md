---
title: "Información general de Azure Service Fabric con API Management | Microsoft Docs"
description: "Este artículo es una introducción al uso de Azure API Management como puerta de enlace para las aplicaciones de Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: a3eedacac5efb53f82e46a56285713dece56ffe8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Información general de Service Fabric con Azure API Management

Las aplicaciones en la nube normalmente necesitan una puerta de enlace front-end para proporcionar un único punto de entrada para usuarios, dispositivos u otras aplicaciones. En Service Fabric, una puerta de enlace puede ser cualquier servicio sin estado como una [aplicación ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) u otro servicio designado para la entrada de tráfico, como [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/) o [Azure API Management](https://docs.microsoft.com/azure/api-management/).

Este artículo es una introducción al uso de Azure API Management como puerta de enlace para las aplicaciones de Service Fabric. API Management se integra directamente con Service Fabric, lo que le permite publicar API con un amplio conjunto de reglas de enrutamiento para los servicios back-end de Service Fabric. 

## <a name="architecture"></a>Arquitectura
Una arquitectura de Service Fabric común usa una aplicación web de una página que realiza llamadas HTTP a servicios back-end que exponen API HTTP. La [aplicación de ejemplo de inicio de Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) presenta una muestra de esta arquitectura.

En este escenario, un servicio web sin estado actúa como puerta de enlace en la aplicación de Service Fabric. Este enfoque precisa que escriba un servicio web que admita solicitudes HTTP proxy para servicios back-end, como se muestra en el siguiente diagrama:

![Información general de la topología de Service Fabric con Azure API Management][sf-web-app-stateless-gateway]

A medida que aumenta la complejidad de las aplicaciones, también lo hacen las puertas de enlace que deben presentar una API delante de innumerables servicios back-end. Azure API Management se ha diseñado para controlar API complejas con reglas de enrutamiento, control de acceso, limitación de velocidad, supervisión, registro de eventos y almacenamiento en caché de respuestas con el mínimo esfuerzo por su parte. Azure API Management admite la selección de réplicas, la resolución de la partición y la detección de servicios de Service Fabric para enrutar de forma inteligente las solicitudes directamente a los servicios back-end en Service Fabric para que no tenga que escribir su propia puerta de enlace de API sin estado. 

En este escenario, la interfaz de usuario web se sigue ofreciendo a través de un servicio web, mientras que las llamadas a la API HTTP se administran y se enrutan a través de Azure API Management, tal como se muestra en el siguiente diagrama:

![Información general de la topología de Service Fabric con Azure API Management][sf-apim-web-app]

## <a name="application-scenarios"></a>Escenarios de aplicación

Los servicios de Service Fabric pueden ser con o sin estado, y pueden tener particiones con uno de los tres esquemas: singleton, intervalo de Int-64 y con nombre. La resolución del punto de conexión de servicio requiere identificar una partición específica de una instancia de servicio específica. Al resolver un punto de conexión de un servicio, deben especificarse el nombre de instancia de servicio (por ejemplo, `fabric:/myapp/myservice`) y la partición específica del servicio, salvo en el caso de la partición singleton.

Azure API Management puede usarse con cualquier combinación de servicios con o sin estado y cualquier esquema de partición.

## <a name="send-traffic-to-a-stateless-service"></a>Envío de tráfico a un servicio sin estado

En el caso más simple, el tráfico se reenvía a una instancia de servicio sin estado. Para lograr esto, una operación de API Management contiene una directiva de procesamiento de entrada con un back-end de Service Fabric que se asigna a una instancia de servicio sin estado específica en el back-end de Service Fabric. Las solicitudes destinadas a ese servicio se envían a una réplica aleatoria de la instancia de servicio sin estado.

#### <a name="example"></a>Ejemplo
En el siguiente escenario, una aplicación de Service Fabric contiene un servicio sin estado denominado `fabric:/app/fooservice`, que expone una API HTTP interna. El nombre de instancia de servicio es conocido y puede estar codificado de forma rígida directamente en la directiva de procesamiento de entrada de API Management. 

![Información general de la topología de Service Fabric con Azure API Management][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Envío de tráfico a un servicio con estado

De forma similar a lo que ocurría en el escenario del servicio sin estado, el tráfico se puede reenviar a una instancia de servicio con estado. En este caso, una operación de API Management contiene una directiva de procesamiento de entrada con un back-end de Service Fabric que asigna una solicitud a una partición específica de una instancia de servicio *con estado* específica. La partición para asignar cada solicitud se calcula a través de un método lambda con algunas entradas de la solicitud HTTP entrante, como un valor de la ruta de acceso de la dirección URL. La directiva puede configurarse para enviar solicitudes solo a la réplica principal o a una réplica aleatoria para operaciones de lectura.

#### <a name="example"></a>Ejemplo

En el siguiente escenario, una aplicación de Service Fabric contiene un servicio con estado con partición denominado `fabric:/app/userservice`, que expone una API HTTP interna. El nombre de instancia de servicio es conocido y puede estar codificado de forma rígida directamente en la directiva de procesamiento de entrada de API Management.  

Se realiza la partición del servicio mediante el esquema de partición Int-64 con dos particiones y un intervalo de claves que abarca de `Int64.MinValue` a `Int64.MaxValue`. La directiva de back-end calcula una clave de partición dentro de ese intervalo convirtiendo el valor `id` proporcionado en la ruta de acceso de solicitud de la dirección URL en un entero de 64 bits, aunque se puede utilizar aquí cualquier algoritmo para calcular la clave de partición. 

![Información general de la topología de Service Fabric con Azure API Management][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Envío de tráfico a varios servicios sin estado

En escenarios más avanzados, puede definir una operación de API Management que asigne solicitudes a más de una instancia de servicio. En este caso, cada operación contiene una directiva que asigna solicitudes a una instancia de servicio determinada basándose en valores de la solicitud HTTP de entrada, como la cadena de consulta o la ruta de acceso de la dirección URL y, en el caso de servicios con estado, una partición dentro de la instancia de servicio. 

Para lograr esto, una operación de API Management contiene una directiva de procesamiento de entrada con un back-end de Service Fabric que asigna una instancia de servicio sin estado en el back-end de Service Fabric basándose en los valores recuperados de la solicitud HTTP entrante. Las solicitudes para la instancia de servicio se envían a una réplica aleatoria de la instancia de servicio.

#### <a name="example"></a>Ejemplo

En este ejemplo, se crea una nueva instancia de servicio sin estado para cada usuario de una aplicación con un nombre generado de forma dinámica con la siguiente fórmula:
 
 - `fabric:/app/users/<username>`

 Cada servicio tiene un nombre único, pero los nombres no se conocen por adelantado porque los servicios se crean en respuesta a la entrada del administrador o usuario y, por lo tanto, no se pueden codificar de forma rígida en las directivas APIM o las reglas de enrutamiento. En su lugar, se genera el nombre del servicio que se va a enviar una solicitud en la definición de la directiva de back-end del valor `name` proporcionado en la ruta de acceso de solicitud de la dirección URL. Por ejemplo:

  - Se enruta una solicitud para `/api/users/foo` a la instancia de servicio `fabric:/app/users/foo`
  - Se enruta una solicitud para `/api/users/bar` a la instancia de servicio `fabric:/app/users/bar`

![Información general de la topología de Service Fabric con Azure API Management][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Envío de tráfico a varios servicios con estado

De forma similar al ejemplo de servicio sin estado, una operación de API Management puede asignar solicitudes a más de una instancia de servicio **con estado**, en cuyo caso también podría tener que realizar una resolución de partición para cada instancia de servicio con estado.

Para lograr esto, una operación de API Management contiene una directiva de procesamiento de entrada con un back-end de Service Fabric que se asigna a una instancia de servicio con estado en el back-end de Service Fabric basándose en los valores recuperados de la solicitud HTTP entrante. Además de asignar una solicitud a la instancia de servicio específica, la solicitud también se puede asignar a una partición específica dentro de la instancia de servicio y, opcionalmente, a la réplica principal o a una réplica secundaria aleatoria dentro de la partición.

#### <a name="example"></a>Ejemplo

En este ejemplo, se crea una nueva instancia de servicio con estado para cada usuario de la aplicación con un nombre generado de forma dinámica con la siguiente fórmula:
 
 - `fabric:/app/users/<username>`

 Cada servicio tiene un nombre único, pero los nombres no se conocen por adelantado porque los servicios se crean en respuesta a la entrada del administrador o usuario y, por lo tanto, no se pueden codificar de forma rígida en las directivas APIM o las reglas de enrutamiento. En su lugar, se genera el nombre del servicio que se va a enviar una solicitud en la definición de la directiva de back-end del valor `name` proporcionado en la ruta de acceso de solicitud de la dirección URL. Por ejemplo:

  - Se enruta una solicitud para `/api/users/foo` a la instancia de servicio `fabric:/app/users/foo`
  - Se enruta una solicitud para `/api/users/bar` a la instancia de servicio `fabric:/app/users/bar`

Se realiza también la partición de cada instancia de servicio mediante el esquema de partición Int-64 con dos particiones y un intervalo de claves que abarca de `Int64.MinValue` a `Int64.MaxValue`. La directiva de back-end calcula una clave de partición dentro de ese intervalo convirtiendo el valor `id` proporcionado en la ruta de acceso de solicitud de la dirección URL en un entero de 64 bits, aunque se puede utilizar aquí cualquier algoritmo para calcular la clave de partición. 

![Información general de la topología de Service Fabric con Azure API Management][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Pasos siguientes

Siga la [Guía de inicio rápido](service-fabric-api-management-quick-start.md) para configurar el primer clúster de Service Fabric con API Management y solicitudes de flujo mediante API Management a los servicios.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png