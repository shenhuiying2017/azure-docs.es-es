<properties
   pageTitle="Modelos de programación de Service Fabric | Microsoft Azure"
   description="Service Fabric ofrece dos marcos para la creación de servicios: el marco de actores y el marco de servicios. Ofrecen distintas ventajas e inconvenientes en cuanto a simplicidad y control."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/15/2015"
   ms.author="seanmck"/>

# Elección de un marco para su servicio

Azure Service Fabric ofrece dos marcos de alto nivel para la creación de servicios: las API de Reliable Actors y las API de Reliable Services. Aunque ambas se basan en el mismo núcleo de Service Fabric, hacen diferentes compromisos entre la simplicidad y la flexibilidad en términos de simultaneidad, creación de particiones y comunicación. Es útil comprender ambos modelos para que pueda elegir el marco adecuado para un servicio concreto dentro de la aplicación.

## Comparación de las API de Reliable Actors y las API de Reliable Services

|**Cuándo se debe elegir la API de Reliable Actors**|**Cuándo se debe elegir la API de Reliable Services**|
|-----------------------|--------------------------|
|El espacio del problema implica muchas unidades pequeñas e independientes de estado y lógica.|Necesita mantener la lógica en varios componentes.|
|Desea trabajar con objetos uniproceso a la vez que continuar siendo capaz de escalar y mantener la coherencia.|Desea usar colecciones confiables (como .NET Reliable Dictionary y Reliable Queue) para almacenar y administrar el estado.|
|Desea que el marco de trabajo administre la simultaneidad y la granularidad del estado.|Desea controlar la simultaneidad y la granularidad del estado.|
|Desea que la plataforma administre la comunicación por usted.|Desea administrar la comunicación y controlar el esquema de particiones del servicio.|

Tenga en cuenta que es perfectamente razonable usar marcos diferentes para distintos servicios dentro de su aplicación. Por ejemplo, podría tener un servicio con estado que agrega datos generados por varios actores.

## Pasos siguientes

- [Obtener más información sobre las API de Reliable Actors](service-fabric-reliable-actors-introduction.md)
- [Obtener más información acerca de las API de Reliable Services](../Service-Fabric/service-fabric-reliable-services-introduction.md)

<!---HONumber=AcomDC_0114_2016-->