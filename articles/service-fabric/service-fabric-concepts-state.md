---
title: "Definición y administración del estado en microservicios de Azure | Microsoft Docs"
description: "Cómo definir y administrar el estado de servicio en Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 103fd6c3d536bc11f4e39444043a332a1d8f6c01
ms.contentlocale: es-es
ms.lasthandoff: 08/19/2017

---
# <a name="service-state"></a>Estado de servicio
**Estado de servicio** hace referencia a los datos en memoria o en disco que el servicio necesita para funcionar. Incluye, por ejemplo, las variables de miembro y las estructuras de datos que el servicio lee y escribe para trabajar. Dependiendo del diseño del servicio, también puede incluir archivos u otros recursos que se almacenan en discos. Por ejemplo, los archivos que usaría una base de datos para almacenar los registros de transacciones y de datos.

Como servicio de ejemplo, consideremos una calculadora. Un servicio de calculadora básica toma dos números y devuelve la suma. Realizar este cálculo no implica ninguna variable de miembro ni otros datos.

Ahora considere la misma calculadora, pero con un método adicional para almacenar y devolver la última suma que había calculado. Este servicio ahora tiene estado. Tener estado significa que contiene algún estado en el que escribe al calcular una nueva suma y del que lee al pedirle que devuelva la última suma calculada.

En Azure Service Fabric, el primer servicio se denomina servicio sin estado. El segundo servicio se denomina servicio con estado.

## <a name="storing-service-state"></a>Almacenamiento del estado de servicio
El estado se puede externalizar o colocar con el código que está manipulando el estado. La externalización de estado normalmente se realiza mediante una base de datos u otro almacén de datos externos que se ejecutan en distintas máquinas de la red o fuera del proceso de la misma máquina. En nuestro ejemplo de la calculadora, el almacén de datos podría ser una base de datos SQL o una instancia de Azure Table Storage. Todas las solicitudes para calcular la suma realizan una actualización en estos datos y solicitan al servicio que devuelva el resultado como el valor actual capturado del almacén. 

El estado también se puede ubicar simultáneamente con el código que lo manipula. Los servicios con estado de Service Fabric se suelen crear mediante este modelo. Service Fabric proporciona la infraestructura que garantiza que este estado está altamente disponible, es coherente y duradero, y que los servicios que se crean de esta forma se escalan fácilmente.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre los conceptos de Service Fabric, consulte los siguientes artículos:

* [Disponibilidad de los servicios de Service Fabric](service-fabric-availability-services.md)
* [Escalabilidad de servicios de Service Fabric](service-fabric-concepts-scalability.md)
* [Creación de particiones de los servicios de Service Fabric](service-fabric-concepts-partitioning.md)
* [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)

