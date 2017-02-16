---
title: "Introducción al modelo de programación de Service Fabric | Microsoft Docs"
description: "Service Fabric ofrece dos marcos para la creación de servicios: el marco de actores y el marco de servicios. Ofrecen distintas ventajas e inconvenientes en simplicidad y control."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: vturecek
ms.assetid: 974b2614-014e-4587-a947-28fcef28b382
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/05/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: d7aa8568dd6fdd806d8ad70e408f108c722ec1ce
ms.openlocfilehash: b56d75d49e0b95025dd1a0bae532f677958eac8d


---
# <a name="service-fabric-programming-model-overview"></a>Información general del modelo de programación de Service Fabric
Service Fabric ofrece varias maneras de escribir y administrar los servicios. Los servicios pueden elegir usar las API de Service Fabric para aprovechar al máximo las características y los marcos de aplicaciones de la plataforma, o los servicios pueden ser simplemente un programa ejecutable compilado escrito en cualquier lenguaje y hospedado simplemente en un clúster de Service Fabric.

## <a name="guest-executable"></a>Archivo ejecutable de invitado
Un archivo ejecutable de invitado es un archivo ejecutable arbitrario, escrito en cualquier lenguaje, por lo que puede tomar las aplicaciones existentes y hospedarlas en un clúster de Service Fabric. Un archivo ejecutable de invitado puede empaquetarse en una aplicación y hospedarse junto a otros servicios. Service Fabric controla la orquestación y la administración simple de la ejecución del archivo ejecutable asegurándose de que permanecen activas y se ejecutan según la descripción del servicio. Sin embargo, dado que los archivos ejecutables de invitado no se integran directamente con las API de Service Fabric, no se benefician del conjunto completo de características que ofrece la plataforma, como el estado personalizado y la carga de informes, el punto de conexión de servicio y el proceso con estado.

Introducción a los archivos ejecutables de invitado mediante la implementación de la primera [aplicación ejecutable de invitado](service-fabric-deploy-existing-app.md).

## <a name="reliable-services"></a>Reliable Services
Reliable Services es un marco de trabajo ligero para escribir servicios que se integran con la plataforma Service Fabric y que se benefician de todo el conjunto de características de la plataforma. Reliable Services proporciona un conjunto mínimo de API que permiten que el tiempo de ejecución de Service Fabric administre el ciclo de vida de los servicios y que permiten a los servicios interactuar con el tiempo de ejecución. El marco de aplicación es mínimo, lo que proporciona un control total sobre las opciones de diseño e implementación, y puede usarse para hospedar cualquier otro marco de aplicación, como ASP.NET MVC o Web API.

Reliable Services puede no tener estado, de forma similar a la mayoría de las plataformas de servicios, como servidores web o roles de trabajo de Servicios en la nube de Azure, en el que cada instancia del servicio se crea de la misma y se conserva el estado en una solución externa, como Almacenamiento de tablas de Azure y Base de datos de Azure.

Reliable Services también pueden tener estado, exclusivo de Service Fabric, donde se conserva el estado directamente en el propio servicio mediante Colecciones confiables. El estado cuenta con una alta disponibilidad mediante la replicación y se distribuye a través de particiones, todo administrado automáticamente por Service Fabric.

[Aprenda más sobre Reliable Services](service-fabric-reliable-services-introduction.md) o comience por [escribir su primer Reliable Service](service-fabric-reliable-services-quick-start.md).

## <a name="reliable-actors"></a>Reliable Actors
Basado en Reliable Services, el marco de Reliable Actor es un marco de aplicación que implementa el diseño de Virtual Actor, basado en el patrón de diseño del actor. El marco de Reliable Actor usa unidades independientes del proceso y el estado con la ejecución de subproceso único denominadas actores. El marco de Reliable Actor proporciona comunicaciones integradas para actores y las configuraciones de escalado horizontal y persistencia de estado establecidas previamente.

Como Reliable Actors es en sí mismo un marco de aplicación basado en Reliable Services, está totalmente integrado con la plataforma Service Fabric y se beneficia del conjunto completo de características que ofrece la plataforma.

## <a name="next-steps"></a>Pasos siguientes
[Más información acerca de Reliable Actors](service-fabric-reliable-actors-introduction.md) o comience por [escribir su primer servicio de Reliable Actor](service-fabric-reliable-actors-get-started.md)
[Más información sobre cómo contener sus servicios en Windows o Linux](service-fabric-deploy-container.md)




<!--HONumber=Jan17_HO2-->


