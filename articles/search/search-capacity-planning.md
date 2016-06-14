<properties
	pageTitle="Escalado de niveles de recursos para cargas de trabajo de indexación y consulta en Búsqueda de Azure | Microsoft Azure"
	description="El planeamiento de la capacidad en Búsqueda de Azure se basa en combinaciones de recursos informáticos de partición y réplica, en las que el precio de cada recurso se indica en unidades de búsqueda facturables."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="paulettm"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="05/25/2016"
	ms.author="heidist"/>

# Escalado de niveles de recursos para cargas de trabajo de indexación y consulta en Búsqueda de Azure

En Búsqueda de Azure, puede ajustar de forma incremental la capacidad de recursos informáticos específicos aumentando las particiones si necesita más almacenamiento y E/S, o las réplicas para mejorar el rendimiento de las consultas y la indexación.

La escalabilidad está disponible cuando se aprovisiona un servicio en un [nivel básico](http://aka.ms/azuresearchbasic) o uno de los [niveles estándar](search-limits-quotas-capacity.md).

Para todos los niveles facturables, la capacidad se adquiere en incrementos de *unidades de búsqueda* (SU), en las que cada partición y réplica cuentan como una SU.

- El nivel básico proporciona hasta 3 SU por servicio.
- El nivel estándar proporciona hasta 36 SU por servicio.

Debe elegir una combinación de particiones y réplicas que permanezca por debajo del límite del nivel. Si usa el portal para escalar verticalmente, se aplicarán límites a las combinaciones permitidas.

Como norma general, las aplicaciones de búsqueda necesitan más réplicas que particiones. En la siguiente sección, [Recursos para lograr alta disponibilidad](#HA), se explica por qué.

<a id="HA"></a>
## Recursos para lograr alta disponibilidad

Dado que es sencillo y relativamente rápido escalar verticalmente, generalmente se recomienda que comience con una partición y una o dos réplicas, y que después escale verticalmente conforme se crean volúmenes de consulta. Para muchas implementaciones, una partición proporciona suficiente almacenamiento y E/S (en 15 millones de documentos por partición).

Sin embargo, las cargas de trabajo de consulta se ejecutan principalmente en réplicas. Podría requerir réplicas adicionales si necesita más rendimiento o alta disponibilidad.

Las recomendaciones generales para alta disponibilidad son:

- 2 réplicas para alta disponibilidad de cargas de trabajo de solo lectura (consultas)
- 3 o más réplicas para alta disponibilidad de cargas de trabajo de lectura-escritura (consultas e indización)

## Recuperación ante desastres

En la actualidad no hay ningún mecanismo integrado para la recuperación ante desastres. La adición de particiones o réplicas sería la estrategia equivocada para cumplir los objetivos de recuperación ante desastres. El enfoque más común es agregar redundancia en el nivel de servicio mediante el aprovisionamiento de un segundo servicio de búsqueda en otra región.

> [AZURE.NOTE] Recuerde que la escalabilidad y los acuerdos de nivel de servicio son características del servicio básico y el estándar. El servicio gratuito se ofrece en un nivel de recurso fijo, con las réplicas y las particiones compartidas por varios suscriptores. Si comenzó con el servicio gratuito y ahora quiere actualizar, deberá crear un servicio Búsqueda de Azure en el nivel básico o estándar y luego volver a cargar los índices y los datos en este nuevo servicio. Consulte [Creación de un servicio Búsqueda de Azure en el Portal](search-create-service-portal.md) para obtener instrucciones sobre el aprovisionamiento del servicio.

## Terminología: particiones y réplicas

Las **particiones** ofrecen almacenamiento y E/S. Un único servicio de búsqueda puede tener un máximo de 12 particiones. Cada partición se incluye con un límite máximo de 15 millones de documentos o 25 GB de almacenamiento, lo que ocurra primero. Si agrega particiones, su servicio de búsqueda puede cargar más documentos. Por ejemplo, un servicio con una partición única que almacena inicialmente hasta 25 GB de datos puede almacenar 50 GB cuando se agrega una segunda partición al servicio.

Las **réplicas** son copias del motor de búsqueda. Un servicio de búsqueda único puede tener un máximo de 12 réplicas. Necesita al menos 2 réplicas para disponibilidad (consultas) de lectura y al menos 3 réplicas para disponibilidad de lectura y escritura (consultas, indización).

## Aumento del rendimiento de las consultas con réplicas

La latencia de consultas es un indicador de que se pueden necesitar réplicas adicionales. Por lo general, el primer paso para mejorar el rendimiento de las consultas es agregar más réplicas.

Se ejecuta una copia de cada índice en cada réplica. Conforme agrega réplicas, se ponen en línea copias adicionales del índice para admitir mayores cargas de trabajo de consultas y para equilibrar la carga de las solicitudes por las diversas réplicas. Si tiene varios índices, digamos 6, y 3 réplicas, cada réplica tendrá una copia de todos los 6 índices.

Tenga en cuenta que no ofrecemos estimaciones finales sobre consultas por segundo (QPS); el rendimiento de las consultas depende de la complejidad de la consulta y las cargas de trabajo competitivas. De media, una réplica puede dar servicio a unas 15 QPS, pero el rendimiento será un algo mayor o menor en función de la complejidad de la consulta (las consultas por facetas son más complejas) y la latencia de red. Además, es importante reconocer que, aunque la adición de réplicas agregará definitivamente escala y rendimiento, el resultado final no será estrictamente lineal: la adición de 3 réplicas no garantiza el triple rendimiento.

Para información sobre QPS, incluidos los métodos para estimar el valor de QPS para las cargas de trabajo, consulte [Administración del servicio de búsqueda en Microsoft Azure](search-manage.md).

## Aumento del rendimiento de la indexación con particiones

Las aplicaciones de búsqueda que requieren una actualización de datos casi en tiempo real necesitan una proporción mayor de particiones que de réplicas. Al agregarse particiones, se distribuyen las operaciones de lectura y escritura entre un número mayor de recursos de proceso. Además, se cuenta con más espacio en disco para almacenar documentos e índices adicionales.

Las consultas en índices de mayor tamaño tardan más tiempo en realizarse. Por lo tanto, es posible que con cada aumento incremental de las particiones sea necesario también un aumento menor, pero proporcional, de las réplicas. Como se indicó antes, la complejidad y el volumen de las consultas afectarán a la rapidez con que se ejecuta la consulta.

## Nivel básico: combinaciones de particiones y réplicas

Un servicio básico puede tener 1 partición y hasta 3 réplicas, para un límite máximo de 3 SU.

<a id="chart"></a>
## Nivel Estándar: combinaciones de particiones y réplicas

Esta tabla muestra las unidades de búsqueda necesarias para admitir combinaciones de réplicas y particiones, con el límite de 36 unidades de búsqueda (SU).

- |- |- |- |- |- |- |
---|----|---|---|---|---|---|
**12 réplicas**|12 unidades de búsqueda|24 unidades de búsqueda|36 unidades de búsqueda|N/D|N/D|N/D|
**6 réplicas**|6 unidades de búsqueda|12 unidades de búsqueda|18 unidades de búsqueda|24 unidades de búsqueda|36 unidades de búsqueda|N/D|
**5 réplicas**|5 unidades de búsqueda|10 unidades de búsqueda|15 unidades de búsqueda|20 unidades de búsqueda|30 unidades de búsqueda|N/D|
**4 réplicas**|4 unidades de búsqueda|8 SU<|12 unidades de búsqueda|16 unidades de búsqueda|24 unidades de búsqueda|N/|
**3 réplicas**|3 unidades de búsqueda|6 unidades de búsqueda|9 unidades de búsqueda|12 unidades de búsqueda|18 unidades de búsqueda|36 unidades de búsqueda|
**2 réplicas**|2 unidades de búsqueda|4 unidades de búsqueda|6 unidades de búsqueda|8 unidades de búsqueda|12 unidades de búsqueda|24 unidades de búsqueda|
**1 réplica**|1 unidad de búsqueda|2 unidades de búsqueda|3 unidades de búsqueda|4 unidades de búsqueda|6 unidades de búsqueda|12 unidades de búsqueda|
N/D|**1 partición**|**2 particiones**|**3 particiones**<|**4 particiones**|**6 particiones**|**12 particiones**|


En el sitio web de Azure se explican con detalle la capacidad, los precios y las unidades de búsqueda. Para más información, consulte [Detalles de precios](https://azure.microsoft.com/pricing/details/search/).

> [AZURE.NOTE] El número de réplicas y particiones debe dividirse en 12 uniformemente (de manera específica, 1, 2, 3, 4, 6, 12). Esto se debe a que la Búsqueda de Azure divide previamente cada índice en 12 particiones para que se pueda repartir entre las particiones. Por ejemplo, si su servicio tiene tres particiones y crea un nuevo índice, cada partición contendrá 4 particiones del índice. La manera en que la Búsqueda de Azure particiona un índice es un detalle de implementación, sujeto a cambios en la futura versión. Aunque el número es 12 hoy, no debe esperar que ese número se siempre 12 en el futuro.

## Cálculo de las unidades de búsqueda para combinaciones de recursos específicas: R X P = SU

La fórmula para calcular cuántas SU se necesitan es réplicas multiplicadas por particiones. Por ejemplo, 3 réplicas multiplicadas por 3 particiones se factura como 9 unidades de búsqueda.

Ambos niveles comienzan con una réplica y una partición, que cuentan como una unidad de búsqueda (SU). Este es el único caso en que una réplica y una partición se cuentan como una unidad de búsqueda. Cada recurso adicional, ya sea una réplica o una partición, se cuenta como una SU independiente.

El nivel determina el costo por SU. El costo por SU es menor para el nivel básico que para el nivel Estándar. Las tarifas de cada nivel se encuentran en [Precios de Búsqueda](https://azure.microsoft.com/pricing/details/search/).

<!---HONumber=AcomDC_0601_2016-->