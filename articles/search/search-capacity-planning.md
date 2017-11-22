---
title: Planeamiento de la capacidad para Azure Search | Microsoft Docs
description: "Ajuste los recursos de proceso de réplica y partición en Azure Search, donde el precio de cada recurso se basa en unidades de búsqueda facturables."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 1dc16afe-56f9-439d-8874-1733ae1a2b74
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: 47dcd5366ef8ba3d4598e6d418b11997c61bddea
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="scale-resource-levels-for-query-and-indexing-workloads-in-azure-search"></a>Escalado de niveles de recursos para cargas de trabajo de indexación y consulta en Azure Search
Después de [elegir un plan de tarifa](search-sku-tier.md) y [aprovisionar un servicio de búsqueda](search-create-service-portal.md), el siguiente paso es aumentar opcionalmente el número de réplicas o particiones utilizadas por el servicio. Cada nivel ofrece un número fijo de unidades de facturación. En este artículo se explica cómo asignar las unidades para lograr una configuración óptima que equilibra los requisitos para la ejecución de consulta, indexación y almacenamiento.

La configuración de recursos está disponible cuando se configura un servicio en un [nivel Básico](http://aka.ms/azuresearchbasic) o uno de los [niveles Estándar](search-limits-quotas-capacity.md). Para los servicios en estos niveles, la capacidad se adquiere en incrementos de *unidades de búsqueda* (SU), en las que cada partición y réplica cuentan como una SU. 

Uso de menos SU da lugar a una factura proporcionalmente menor. Mientras el servicio esté configurando, le seguiremos cobrando. Si va a estar un tiempo sin utilizar un servicio, la única forma de evitar que le cobremos será eliminando el servicio y creándolo de nuevo cuando lo necesite más adelante.

> [!Note]
> La eliminación de un servicio supone la eliminación de todo su contenido. Azure Search no dispone de ningún recurso para realizar copias de seguridad de datos de búsqueda persistentes ni para restaurarlos. Para volver a implementar un índice existente en un nuevo servicio, debe ejecutar el programa usado para crearlo y cargarlo originalmente. 

## <a name="terminology-partitions-and-replicas"></a>Terminología: particiones y réplicas
Las particiones y réplicas son los principales recursos que respaldan a un servicio de búsqueda.

| Recurso | Definición |
|----------|------------|
|*Particiones* | Proporciona almacenamiento de índices y E/S para realizar operaciones de lectura y escritura (por ejemplo, volver a generar o actualizar un índice).|
|*Réplicas* | Instancias del servicio de búsqueda, que se utilizan principalmente para equilibrar la carga de las operaciones de consulta. Cada réplica siempre hospeda una copia de un índice. Si hay 12 réplicas, tendrá 12 copias de todos los índices cargados en el servicio.|

> [!NOTE]
> No existe ninguna manera de manipular o administrar directamente los índices que se ejecutan en una réplica. Una copia de cada índice de cada réplica forma parte de la arquitectura del servicio.
>

## <a name="how-to-allocate-partitions-and-replicas"></a>Asignación de particiones y réplicas
En Azure Search, un servicio se asigna inicialmente a un nivel mínimo de recursos que consta de una partición y una réplica. En los niveles donde se admita, puede ajustar de forma incremental los recursos informáticos aumentando las particiones si necesita más almacenamiento y E/S, o bien agregue réplicas para mejorar el rendimiento y aumentar los volúmenes de las consultas. Un único servicio debe tener recursos suficientes para controlar todas las cargas de trabajo (indexación y consultas). No se pueden subdividir las cargas de trabajo entre varios servicios.

Para aumentar o cambiar la asignación de réplicas y particiones, se recomienda usar Azure Portal. El portal aplica límites a las combinaciones permitidas que se mantengan por debajo de los límites máximos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y seleccione su servicio de búsqueda.
2. En **Configuración**, abra la hoja **Escala** y utilice los controles deslizantes para aumentar o disminuir el número de particiones y réplicas.

Una alternativa al portal es usar la [API de REST de administración](https://docs.microsoft.com/rest/api/searchmanagement/services) si necesita un script o un enfoque de aprovisionamiento basado en código.

Como norma general, las aplicaciones de búsqueda necesitan más réplicas que particiones, sobre todo cuando las operaciones de servicio están orientadas a las cargas de trabajo de consulta. En la siguiente sección sobre [alta disponibilidad](#HA) se explica el motivo.

> [!NOTE]
> Una vez que se aprovisiona un servicio, no se puede actualizar en contexto a una SKU superior. Debe crear un servicio de búsqueda en el nivel nuevo y volver a cargar los índices. Consulte [Creación de un servicio Azure Search mediante Azure Portal](search-create-service-portal.md) para obtener ayuda con el proceso de aprovisionamiento de servicios.
>
>

<a id="HA"></a>

## <a name="high-availability"></a>Alta disponibilidad
Dado que es sencillo y relativamente rápido escalar verticalmente, generalmente se recomienda que comience con una partición y una o dos réplicas, y que después escale verticalmente conforme se crean volúmenes de consulta. Las cargas de trabajo de consulta se ejecutan principalmente en réplicas. Es probable que necesite más réplicas si requiere más rendimiento o alta disponibilidad.

Las recomendaciones generales para alta disponibilidad son:

* Dos réplicas para alta disponibilidad de cargas de trabajo de solo lectura (consultas)
* Tres o más réplicas para lograr una alta disponibilidad en las cargas de trabajo de lectura y escritura (se agregan, actualizan o eliminan consultas e indexación como documentos individuales).

Los Acuerdos de Nivel de Servicio (SLA) de Azure Search están destinados a las operaciones de consulta y actualizaciones de índices que constan de procesos de incorporación, actualización o eliminación de documentos.

El nivel básico alcanza el límite en una partición y tres réplicas. Si desea la flexibilidad de responder inmediatamente a las fluctuaciones en la demanda para el rendimiento de indexación y consulta, considere uno de los niveles Estándar.

### <a name="index-availability-during-a-rebuild"></a>Disponibilidad de los índices durante un proceso de regeneración

La alta disponibilidad para Azure Search se refiere a las consultas y actualizaciones de índices que no requieren volver a generar un índice. Si elimina un campo, cambia un tipo de datos o el nombre de un campo, debe volver a generar el índice. Para volver a crear el índice, debe eliminar el índice, volver a crearlo y cargar de nuevo los datos.

> [!NOTE]
> Puede agregar nuevos campos a un índice de Azure Search sin volver a generar el índice. El valor del nuevo campo será Null en todos los documentos que estén en el índice.

Para mantener la disponibilidad del índice durante una regeneración, debe contar con una segunda copia del índice con un nombre distinto en el mismo servicio, o bien una copia del índice con el mismo nombre en un servicio diferente. Luego, tendrá que proporcionar la lógica de conmutación por error o redireccionamiento en el código.

## <a name="disaster-recovery"></a>Recuperación ante desastres
En la actualidad no hay ningún mecanismo integrado para la recuperación ante desastres. La adición de particiones o réplicas sería la estrategia equivocada para cumplir los objetivos de recuperación ante desastres. El enfoque más común es agregar redundancia en el nivel de servicio mediante la configuración de un segundo servicio de búsqueda en otra región. Al igual que con la disponibilidad durante la regeneración de índices, la lógica de conmutación por error o redireccionamiento debe proporcionarse en el código.

## <a name="increase-query-performance-with-replicas"></a>Aumento del rendimiento de las consultas con réplicas
La latencia de consultas es un indicador de que se necesitan más réplicas. Por lo general, el primer paso para mejorar el rendimiento de las consultas consiste en agregar más réplicas. Conforme agrega réplicas, se ponen en línea copias adicionales del índice para admitir mayores cargas de trabajo de consultas y equilibrar la carga de las solicitudes por las diversas réplicas.

No ofrecemos estimaciones finales sobre consultas por segundo (QPS); el rendimiento de las consultas depende de la complejidad de la consulta y las cargas de trabajo competitivas. Si bien la adición de réplicas genera claramente un mejor rendimiento, el resultado final no será estrictamente lineal: la adición de tres réplicas no garantiza el triple rendimiento.

Para obtener una guía para la estimación de las QPS para las cargas de trabajo, consulte [Consideraciones sobre el rendimiento y la optimización de Azure Search](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Aumento del rendimiento de la indexación con particiones
Las aplicaciones de búsqueda que requieren una actualización de datos casi en tiempo real necesitan una proporción mayor de particiones que de réplicas. Al agregarse particiones, se distribuyen las operaciones de lectura y escritura entre un número mayor de recursos de proceso. Además, se cuenta con más espacio en disco para almacenar documentos e índices adicionales.

Las consultas en índices de mayor tamaño tardan más tiempo en realizarse. Por lo tanto, es posible que con cada aumento incremental de las particiones sea necesario también un aumento menor, pero proporcional, de las réplicas. La complejidad y el volumen de las consultas afectarán a la rapidez con que se ejecuta la consulta.

## <a name="basic-tier-partition-and-replica-combinations"></a>Nivel básico: combinaciones de particiones y réplicas
Un servicio del nivel Básico puede tener exactamente 1 partición y hasta 3 réplicas para un límite máximo de 3 SU. El único recurso que puede ajustarse son las réplicas. Se necesita un mínimo de 2 réplicas para lograr una alta disponibilidad en las consultas.

<a id="chart"></a>

## <a name="standard-tiers-partition-and-replica-combinations"></a>Niveles Estándar: combinaciones de particiones y réplicas
Esta tabla muestra las unidades de búsqueda necesarias para admitir combinaciones de réplicas y particiones, con el límite de 36 unidades de búsqueda (SU), para todos los niveles estándar.

|   | **1 partición** | **2 particiones** | **3 particiones** | **4 particiones** | **6 particiones** | **12 particiones** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 réplica** |1 unidad de búsqueda |2 unidades de búsqueda |3 unidades de búsqueda |4 unidades de búsqueda |6 unidades de búsqueda |12 unidades de búsqueda |
| **2 réplicas** |2 unidades de búsqueda |4 unidades de búsqueda |6 unidades de búsqueda |8 unidades de búsqueda |12 unidades de búsqueda |24 unidades de búsqueda |
| **3 réplicas** |3 unidades de búsqueda |6 unidades de búsqueda |9 unidades de búsqueda |12 unidades de búsqueda |18 unidades de búsqueda |36 unidades de búsqueda |
| **4 réplicas** |4 unidades de búsqueda |8 unidades de búsqueda |12 unidades de búsqueda |16 unidades de búsqueda |24 unidades de búsqueda |N/D |
| **5 réplicas** |5 unidades de búsqueda |10 unidades de búsqueda |15 unidades de búsqueda |20 unidades de búsqueda |30 unidades de búsqueda |N/D |
| **6 réplicas** |6 unidades de búsqueda |12 unidades de búsqueda |18 unidades de búsqueda |24 unidades de búsqueda |36 unidades de búsqueda |N/D |
| **12 réplicas** |12 unidades de búsqueda |24 unidades de búsqueda |36 unidades de búsqueda |N/D |N/D |N/D |

En el sitio web de Azure se explican con detalle la capacidad, los precios y las unidades de búsqueda. Para obtener más información, consulte [Detalles de precios](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> El número de réplicas y particiones se dividirse equitativamente en 12 (en concreto, 1, 2, 3, 4, 6, 12). Esto se debe a que Azure Search divide previamente cada índice en 12 particiones para que se pueda repartir en porciones iguales entre todas las particiones. Por ejemplo, si su servicio tiene tres particiones y crea un nuevo índice, cada partición contendrá 4 particiones del índice. La manera en que Azure Search particiona un índice es un detalle de implementación, sujeto a cambios en la futura versión. Aunque el número es 12 hoy, no debe esperar que ese número se siempre 12 en el futuro.
>
>

## <a name="billing-formula-for-replica-and-partition-resources"></a>Fórmula de facturación para los recursos de réplica y partición
La fórmula para calcular cuántas SU se usan para combinaciones concretas es el producto de réplicas y particiones, o (R X P = SU). Por ejemplo, 3 réplicas multiplicadas por 3 particiones se facturan como 9 SU.

El nivel determina el costo por SU, con una tasa de facturación por unidad más baja para Basic que para Estándar. Las tarifas de cada nivel pueden consultarse en [Buscar Precios](https://azure.microsoft.com/pricing/details/search/).
