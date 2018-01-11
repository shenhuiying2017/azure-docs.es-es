---
title: 'Alta disponibilidad: servicio Azure SQL Database | Microsoft Docs'
description: "Obtenga información acerca de las funcionalidades y características de alta disponibilidad de Azure SQL Database"
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
ms.assetid: 
ms.service: sql-database
ms.custom: 
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 12/13/2017
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: c0a140c959f14c2e8ceaddad5d323f0900be5d2f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="high-availability-and-azure-sql-database"></a>Alta disponibilidad y Azure SQL Database
Desde el comienzo de la oferta de PaaS de Azure SQL Database, Microsoft se comprometió a integrar la alta disponibilidad (HA) en el servicio para que los clientes no tuvieran que administrar, agregar lógicas especiales o tomar decisiones acerca de la alta disponibilidad. Microsoft ofrece a sus clientes un SLA y mantiene un control total sobre la configuración y el manejo del sistema de alta disponibilidad. El SLA de alta disponibilidad se aplica a una base de datos SQL en una región y no proporciona protección en los casos de que se produzca un error total en la región debido a factores externos que estén fuera de nuestro control (por ejemplo, desastres naturales, guerras, terrorismo, disturbios, acción gubernamental o un error de red o dispositivos externo a nuestros centros de datos, incluyendo el sitio del cliente o entre este sitio y nuestro centro de datos).

Para simplificar el problema de espacio de la alta disponibilidad, Microsoft usa los siguientes supuestos:
1.  Los errores de hardware y software son inevitables.
2.  El personal de operaciones comete errores que dar lugar a problemas.
3.  Las operaciones de mantenimiento planeadas pueden provocar interrupciones. 

Aunque dichos eventos individuales son poco frecuentes, si incluimos la nube, estos se pueden producir semanal o diariamente. 

## <a name="fault-tolerant-sql-databases"></a>Bases de datos SQL con tolerancia a errores
Los clientes están especialmente interesados en la resistencia de sus propias bases de datos y les interesa menos la resistencia del servicio SQL Database en general. Un tiempo de actividad del 99,99 % en un servicio no tiene sentido si "mi base de datos" forma parte del 0,01 % de las bases de datos que están inactivas. Cada base de datos debe poder tolerar errores y la mitigación de estos nunca debe derivarse en la pérdida de una transacción confirmada. 

En cuanto a los datos, SQL Database utiliza el almacenamiento local (LS) basado en discos y discos duros virtuales adjuntados directamente y en almacenamiento remoto (RS) basado en los blobs en páginas de Azure Premium Storage. 
- El almacenamiento local se utiliza en las bases de datos y grupos Premium que están diseñados para aplicaciones de OLTP con grandes requisitos de IOPS. 
- El almacenamiento remoto se utiliza en niveles de servicio básicos y estándar diseñados para bases de datos pequeñas, inactivas o grandes que necesiten un almacenamiento y una capacidad de cálculo para escalar de forma independiente. Usan un blob en páginas individual para archivos de registro y bases de datos y tienen mecanismos de conmutación por error y replicación de almacenamiento integrados.

En ambos casos, la replicación, la detección de errores y los mecanismos de conmutación por error de SQL Database están totalmente automatizados y operan sin intervención humana. Esta arquitectura está diseñada para garantizar que nunca se pierdan datos confirmados y que la durabilidad de esos datos tenga prioridad sobre todo lo demás.

Ventajas principales:
- Los clientes obtienen todas las ventajas de las bases de datos replicadas sin tener que configurar o mantener hardware, software, sistemas operativos o entornos de virtualización complicados.
- El sistema mantiene las propiedades ACID completas de las bases de datos relacionales.
- Las conmutaciones por error están totalmente automatizados para que no pierdan datos confirmados.
- El servicio se encarga de administrar de forma dinámica el enrutamiento de conexiones a la réplica principal sin necesidad de lógicas de aplicación.
- El alto nivel de redundancia automatizada se proporciona sin cargo adicional.

> [!NOTE]
> Es posible que la arquitectura de alta disponibilidad descrita cambie sin previo aviso. 

## <a name="data-redundancy"></a>Redundancia de datos

La solución de alta disponibilidad en SQL Database se basa en la tecnología [AlwaysON](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) de SQL Server para que puedan funcionar las bases de datos LS y RS con diferencias mínimas. En la configuración de LS, AlwaysON se usa para la persistencia, mientras que en RS se usa para la disponibilidad (RTO bajo). 

## <a name="local-storage"></a>Almacenamiento local

En el caso de LS, el servicio de administración (MS) es el encargado de poner en línea cada base de datos dentro del anillo de control. Una réplica principal y al menos dos réplicas secundarias (conjunto de cuórum) se ubican en el anillo del inquilino que abarca tres subsistemas físicos independientes dentro del mismo centro de datos. Todas las lecturas y escrituras se envían por la puerta de enlace (GW) a la réplica principal y las escrituras se replican asincrónicamente en las réplicas secundarias. SQL Database emplea un esquema de confirmación basado en cuórum donde los datos se escriben en la réplica principal y en una de las secundarias antes de que la transacción se considere confirmada.

El sistema de conmutación por error de [Service Fabric](/azure/service-fabric/service-fabric-overview.md) recompila de forma automática réplicas a medida que se van produciendo errores en los nodos y mantiene la pertenencia al conjunto del cuórum a medida que los nodos entran y salen del sistema. El mantenimiento planeado se coordina con sumo cuidado para evitar que el conjunto de cuórum caiga por debajo de un recuento de réplica mínimo (normalmente 2). Este modelo funciona bien en bases de datos Premium, pero requiere redundancia de ambos componentes de proceso y almacenamiento y tiene un costo mayor.

## <a name="remote-storage"></a>Almacenamiento remoto

En las configuraciones de almacenamiento remoto (niveles Basic y Standard) se guarda exactamente una copia de la base de datos en el almacenamiento de blobs remoto y permite aprovechar las funcionalidades de los sistemas de almacenamiento para poder detectar la degradación de bits, la redundancia y la durabilidad. 

La arquitectura de alta disponibilidad se muestra en el diagrama siguiente:
 
![arquitectura de alta disponibilidad](./media/sql-database-high-availability/high-availability-architecture.png)

## <a name="failure-detection--recovery"></a>Detección de errores y recuperación 
Un sistema distribuido a gran escala necesita tener un sistema de detección de errores que sea altamente fiable y que pueda detectar errores de forma precisa, rápidamente y lo más cerca posible del cliente. En SQL Database, este sistema se basa en Azure Service Fabric. 

Con la réplica principal, se hace evidente inmediatamente si esta ha fallado y cuándo lo ha hecho; debido a ello, el trabajo no puede continuar porque todas las lecturas y escrituras tienen lugar primero en la réplica principal. Este proceso para convertir una réplica secundaria en principal tiene un objetivo de tiempo de recuperación (RTO) de 30 segundos y un objetivo de punto de recuperación (RPO) de 0. Para mitigar el impacto del RTO de 30 segundos, lo mejor es intentar reconectarse varias veces con un tiempo de espera menor error para los intentos fallidos de conexión.

Cuando se produce un error en una réplica secundaria, la base de datos baja hasta un conjunto de cuórum mínimo y sin reservas. Service Fabric inicia el proceso de reconfiguración de forma similar al proceso que sigue al error de la réplica principal, por lo que después de una breve espera para determinar si el error es permanente, se crea otra réplica secundaria. En los casos de estado de fuera de servicio temporal como, por ejemplo, un error del sistema operativo o una actualización, no se genera inmediatamente la nueva réplica para permitir que el nodo con error se reinicie. 

En cuanto a las configuraciones de almacenamiento remoto,SQL Database usa la funcionalidad AlwaysON en bases de datos de conmutación por error durante las actualizaciones. Para ello, una nueva instancia de SQL se deriva de antemano como parte de los eventos planeados de la actualización y se adjunta para recuperar el archivo de base de datos del almacenamiento remoto. En caso de que se bloquee el proceso o sucedan otros eventos no planeados, Windows Fabric se encarga de administrar la disponibilidad de las instancias y, como último paso de recuperación, adjunta el archivo de base de datos remota.

## <a name="conclusion"></a>Conclusión
Azure SQL DB está totalmente integrada con la plataforma Azure y es altamente dependiente de Service Fabric para la detección de errores y la recuperación, y de Azure Storage Blobs para la protección de datos. Al mismo tiempo, Azure SQL Database usa la tecnología de AlwaysOn del producto de cuadro de SQL Server para la replicación y la conmutación por error. La combinación de estas tecnologías permite que las aplicaciones obtengan todos los beneficios de un modelo de almacenamiento mixto y puedan admitir los SLA más exigentes. 

## <a name="next-steps"></a>Pasos siguientes

- Obtener más información sobre [Service Fabric](/azure/service-fabric/service-fabric-overview.md).
- Obtener más información acerca de [Azure Traffic Manager](/traffic-manager/traffic-manager-overview.md). 
