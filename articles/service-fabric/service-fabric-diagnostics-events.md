---
title: Eventos de Azure Service Fabric | Microsoft Docs
description: Obtenga información acerca de los eventos de Service Fabric que le proporcionarán ayuda para supervisar el clúster de Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: b9372c806eab1b0ca69ba078d972b076c8a7d6f6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212728"
---
# <a name="service-fabric-events"></a>Eventos de Service Fabric 

La plataforma Service Fabric escribe varios eventos estructurados para actividades operativas clave que se producen dentro del clúster. Estas van desde las actualizaciones del clúster hasta las decisiones de ubicación de réplicas. Cada evento que Service Fabric expone se asigna a una de las siguientes entidades en el clúster:
* Clúster
* Application
* Servicio
* Partition
* Réplica 
* Contenedor

Para ver una lista completa de los eventos que expone la plataforma, consulte: [eventos de la lista de Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Estos son algunos ejemplos de escenarios importantes en los que puede ver los eventos para el clúster. 
1. Eventos de ciclo de vida del nodo: a medida que los nodos ascienden, descienden, se activan o desactivan, los eventos que se expondrán le mostrarán qué ha ocurrido, y le ayudarán a identificar si hay algún problema con la propia máquina o si existe una API que se llamó a través de SF para modificar el estado de un nodo.
1. Actualización del clúster: cuando el clúster se actualiza (la versión o la configuración de SF cambiará), puede comprobar cómo se inicia la actualización, avanza por cada uno de los UD y se completa (o revierte). 
1. Actualizaciones de aplicaciones: de forma similar a las actualizaciones de clúster, hay un conjunto completo de eventos a medida que avanza la actualización. Estos eventos pueden ser útiles para entender el momento en que se programa una actualización, el estado actual de la misma y la secuencia general de eventos. Esto es útil para ver qué actualizaciones se han realizado correctamente.
1. Implementar o eliminar una aplicación o servicio: hay eventos para cada aplicación, servicio y contenedor que se vaya a crear o eliminar.
1. Movimientos de partición (reconfiguración): cada vez que se lleva a cabo una reconfiguración (un cambio en el conjunto de réplicas) en una partición con estado, se registra un evento. Esto es útil si desea saber cada cuánto cambia el conjunto de réplicas de la partición, o si quiere realizar un seguimiento para saber en cualquier momento qué nodo estaba ejecutando la réplica principal.
1. Eventos de caos: al usar el servicio [Chaos](service-fabric-controlled-chaos.md) de Service Fabric, verá los eventos cada vez que se inicie o detenga el servicio, o cuando inserte un error en el sistema.
1. Eventos de estado: Service Fabric expone eventos de estado cada vez que se crea un informe de mantenimiento de advertencia o error, si una entidad vuelve a un estado correcto o si un informe de mantenimiento expira. Estos eventos son muy útiles para realizar el seguimiento de las estadísticas de mantenimiento históricas de una entidad. 

## <a name="how-to-access-events"></a>Cómo obtener acceso a eventos

Hay varias maneras mediante las cuales se puede obtener acceso a eventos de Service Fabric:
* a través del canal operativo. Los eventos se pueden recopilar a través de la extensión de Azure Diagnostics y enviarse a una tabla de Storage para su consumo o ingesta en una herramienta como Log Analytics de OMS. Cuando se habilitan los diagnósticos en un clúster, el agente de Azure Diagnostics se implementa en el clúster y se configura de forma predeterminada para leer los registros del canal operativo. Obtenga más información sobre la configuración del [agente de Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) para modificar la configuración de diagnósticos del clúster para seleccionar más registros o contadores de rendimiento. 
* a través de las API de Rest del servicio EventStore que le permiten consultar el clúster directamente o a través de la biblioteca cliente de Service Fabric. Consulte [Query EventStore APIs for cluster events](service-fabric-diagnostics-eventstore-query.md) (Consultar las API del servicio EventStore para obtener eventos de clúster).

## <a name="next-steps"></a>Pasos siguientes
* Obtener más información acerca de cómo supervisar el clúster: [Supervisión del clúster y la plataforma](service-fabric-diagnostics-event-generation-infra.md).
* Obtener más información sobre el servicio EventStore: - [EventStore service overview](service-fabric-diagnostics-eventstore.md) (Información general sobre el servicio EventStore).
