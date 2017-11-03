---
title: "Supervisión del rendimiento de Azure Service Fabric | Microsoft Docs"
description: "Obtenga información sobre los contadores de rendimiento para la supervisión y el diagnóstico de clústeres de Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: b19a2db85b2e1cc4c5f79f6b0dee97965f40ef88
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
---
# <a name="performance-metrics"></a>Métricas de rendimiento

Se deben recopilar métricas para comprender el rendimiento de un clúster, así como de las aplicaciones se ejecutan en él. Para los clústeres de Service Fabric, recomendamos recopilar los siguientes contadores de rendimiento.

## <a name="nodes"></a>Nodos

Para las máquinas del clúster, considere la posibilidad de recopilar los siguientes contadores de rendimiento para comprender la carga de cada equipo y tomar las decisiones apropiadas sobre el escalado del clúster.

| Categoría de contador | Nombre del contador |
| --- | --- |
| Disco físico (por disco) | Prom. Longitud de la cola de lectura de disco |
| Disco físico (por disco) | Prom. Longitud de la cola de escritura de disco |
| Disco físico (por disco) | Prom. Segundos de disco/lecturas |
| Disco físico (por disco) | Prom. Segundos de disco/escrituras |
| Disco físico (por disco) | Lecturas de disco/s  |
| Disco físico (por disco) | Bytes de lectura de disco/s  |
| Disco físico (por disco) | Escrituras en disco/s |
| Disco físico (por disco) |  Bytes de escritura en disco/s |
| Memoria | MB disponibles |
| Archivo de paginación | % de uso |
| Procesador (total) | % de tiempo de procesador |
| Proceso (por servicio) | % de tiempo de procesador |
| Proceso (por servicio) | Id. de proceso |
| Proceso (por servicio) | Bytes privados |
| Proceso (por servicio) | Número de subprocesos |
| Proceso (por servicio) | Bytes virtuales |
| Proceso (por servicio) | Espacio de trabajo |
| Proceso (por servicio) | Espacio de trabajo privado |
| Interfaz de red (todas las instancias) | Longitud de la cola de salida |
| Interfaz de red (todas las instancias) | Paquetes de salida descartados |
| Interfaz de red (todas las instancias) | Paquetes recibidos descartados |
| Interfaz de red (todas las instancias) | Paquetes de salida con errores |
| Interfaz de red (todas las instancias) | Paquetes recibidos con errores |

## <a name="net-applications-and-services"></a>Aplicaciones y servicios .NET

Recopile los contadores siguientes si va a implementar servicios de .NET en el clúster. 

| Categoría de contador | Nombre del contador |
| --- | --- |
| Memoria CLR de .NET (por servicio) | Identificador del proceso |
| Memoria CLR de .NET (por servicio) | Número total de bytes confirmados |
| Memoria CLR de .NET (por servicio) | Número total de bytes reservados |
| Memoria CLR de .NET (por servicio) | Número de bytes en todos los montones |
| Memoria CLR de .NET (por servicio) | Número de colecciones de gen. 0 |
| Memoria CLR de .NET (por servicio) | Número de colecciones de gen. 1 |
| Memoria CLR de .NET (por servicio) | Número de colecciones de gen. 2 |
| Memoria CLR de .NET (por servicio) | % de tiempo del GC |

### <a name="service-fabrics-custom-performance-counters"></a>Contadores de rendimiento personalizados de Service Fabric

Service Fabric genera una cantidad considerable de contadores de rendimiento personalizados. Si tiene instalado el SDK, puede ver la lista completa en el equipo Windows, en la aplicación Monitor de rendimiento (Inicio > Monitor de rendimiento). 

En las aplicaciones que está implementando en el clúster, si usa Reliable Actors, agregue los contadores de las categorías `Service Fabric Actor` y `Service Fabric Actor Method` (consulte [Diagnóstico de Reliable Actors de Service Fabric](service-fabric-reliable-actors-diagnostics.md)).

Si usa Reliable Services, del mismo modo tenemos las categorías de contadores `Service Fabric Service` y `Service Fabric Service Method` desde las cuales se deben recopilar los contadores. 

Si usa Reliable Collections, se recomienda agregar `Avg. Transaction ms/Commit` desde `Service Fabric Transactional Replicator` para recopilar la latencia promedio de confirmación por cada métrica de transacción.


## <a name="next-steps"></a>Pasos siguientes

* Aprenda más sobre la [generación de eventos en el nivel de plataforma ](service-fabric-diagnostics-event-generation-infra.md) en Service Fabric.
* Recopile las métricas de rendimiento mediante [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md)
