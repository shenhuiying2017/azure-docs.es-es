---
title: "Límites de servicio en Azure Search | Microsoft Docs"
description: "Límites de servicio usados en la planeación de la capacidad y los límites máximos de solicitudes y respuestas de Azure Search."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 857a8606-c1bf-48f1-8758-8032bbe220ad
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/07/2017
ms.author: heidist
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: d41a02b837d2cd0c478abdcf3068a5ccf7ed1b6f
ms.contentlocale: es-es
ms.lasthandoff: 06/08/2017


---
# <a name="service-limits-in-azure-search"></a>Límites de servicio en la Búsqueda de Azure
Los límites máximos del almacenamiento, las cargas de trabajo y las cantidades de índices, documentos y otros objetos dependen de si [aprovisiona Azure Search](search-create-service-portal.md) conforme a un plan de tarifa **Gratis**, **Básico** o **Estándar**.

* **Gratis** es un servicio multiinquilino compartido incluido en su suscripción de Azure. Es una opción sin costo adicional para los suscriptores existentes que permite experimentar con el servicio antes de registrarse para obtener recursos dedicados.
* El plan **Básico** proporciona recursos de proceso dedicados para cargas de trabajo de producción a escala más pequeña.
* **Estándar** se ejecuta en máquinas dedicadas, con más almacenamiento y capacidad de procesamiento en cada nivel. Standard incluye cuatro niveles: S1, S2, S3 y S3 High Density (S3 HD).

> [!NOTE]
> Un servicio se aprovisiona en un nivel específico. Si necesita saltar niveles para obtener más capacidad, debe aprovisionar un nuevo servicio (no hay ninguna actualización en contexto). Para más información, vea [Selección de SKU o plan de tarifa](search-sku-tier.md). Para información sobre cómo ajustar la capacidad dentro de un servicio que ya ha aprovisionado, vea [Escalado de niveles de recursos para cargas de trabajo de indexación y consulta en Azure Search](search-capacity-planning.md).
>

## <a name="per-subscription-limits"></a>Límites por suscripción
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="per-service-limits"></a>Por límites de servicio
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

## <a name="per-index-limits"></a>Por límites de índice
Hay una correspondencia uno a uno entre los límites de los índices y los límites de los indexadores. Dado un límite de 200 índices, el límite máximo de indexadores también es 200 para el mismo servicio.

| Recurso | Gratuito | Básica | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Índice: campos máximos por índice |1000 |100 <sup>1</sup> |1000 |1000 |1000 |1000 |
| Índice: perfiles de puntuación máximos por índice |100 |100 |100 |100 |100 |100 |
| Índice: funciones máximas por perfil |8 |8 |8 |8 |8 |8 |
| Indexadores: carga máxima de indexación por invocación |10 000 documentos |Limitado solamente por el número máximo de documentos |Limitado solamente por el número máximo de documentos |Limitado solamente por el número máximo de documentos |Limitado solamente por el número máximo de documentos |N/D <sup>2</sup> |
| Indexadores: tiempo de ejecución máximo |3 minutos |24 horas |24 horas |24 horas |24 horas |N/D <sup>2</sup> |
| Indexador de blobs: tamaño máximo de blob, MB |16 |16 |128 |256 |256 |N/D <sup>2</sup> |
| Indexador de blobs: número máximo de caracteres del contenido extraído de un blob |32 000 |64 000 |4 millones |4 millones |4 millones |N/D <sup>2</sup> |

<sup>1</sup> El nivel básico es el único SKU con un límite inferior de 100 campos por índice.

<sup>2</sup> S3 HD actualmente no admite indexadores. Póngase en contacto con el soporte técnico de Azure si necesita urgentemente esta funcionalidad.

## <a name="document-size-limits"></a>Límites de tamaño de documento
| Recurso | Gratuito | Básica | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Tamaño de documento individual por API de índice |<16 MB |<16 MB |<16 MB |<16 MB |<16 MB |<16 MB |

Hace referencia al tamaño máximo del documento cuando se llama a una API de índice. El tamaño del documento es de hecho un límite en el tamaño del cuerpo de la solicitud de la API de índice. Dado que se puede pasar un lote de varios documentos a la API de índice de una vez, el límite de tamaño depende de cuántos documentos estén en el lote. Para un lote con un único documento, el tamaño máximo del documento es de 16 MB de JSON.

Para mantener el tamaño del documento reducido, no olvide excluir los datos no consultables de la solicitud. Las imágenes y otros datos binarios no son consultables directamente y no se deben almacenar en el índice. Para integrar los datos no consultables en los resultados de búsqueda, defina un campo que admita búsqueda que almacene una referencia de dirección URL al recurso.

## <a name="workload-limits-queries-per-second"></a>Límites de carga de trabajo (consultas por segundo)
| Recurso | Gratuito | Básica | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| QPS |N/D  |~ 3 por réplica |~ 15 por réplica |~ 60 por réplica |>60 por réplica |>60 por réplica |

Las consultas por segundo (QPS) son una aproximación basada en heurística, mediante cargas de trabajo de clientes reales y simuladas para obtener valores estimados. El rendimiento QPS exacto varía dependiendo de los datos y de la naturaleza de la consulta.

Aunque se proporcionan cálculos aproximados, una tasa real es difícil de determinar, en especial en el servicio compartido gratuito donde el rendimiento se basa en el ancho de banda disponible y en la competición por los recursos del sistema. En el nivel Gratis, los recursos de proceso y almacenamiento que respaldan el servicio compartido se comparten entre varios suscriptores, de modo que el número de QPS de su solución siempre variará según cuántas otras cargas de trabajo se estén ejecutando al mismo tiempo.

En el nivel Estándar, puede calcular el número de QPS con más precisión porque tiene control sobre más parámetros. Consulte la sección de procedimientos recomendados en [Administración del servicio de búsqueda en Microsoft Azure](search-manage.md) para obtener instrucciones sobre cómo calcular el valor de QPS para las cargas de trabajo.

## <a name="api-request-limits"></a>Límites de solicitud de API
* Máximo de 16 MB por solicitud <sup>1</sup>
* Longitud máxima de dirección URL de 8 KB
* Máximo de 1000 documentos por lote del índice de cargas de índices, combinaciones o eliminaciones
* Máximo de 32 campos en cláusula $orderby
* El tamaño máximo del término de búsqueda es de 32 766 bytes (32 KB menos 2 bytes) de texto con codificación UTF-8

<sup>1</sup> En Azure Search, el cuerpo de una solicitud está sujeto a un límite superior de 16 MB, que impone un límite práctico en el contenido de campos individuales o colecciones que no esté restringido de algún modo por límites teóricos (consulte [Supported data types](https://msdn.microsoft.com/library/azure/dn798938.aspx) (Tipos de datos admitidos) para más información sobre composición de campos y restricciones).

## <a name="api-response-limits"></a>Límites de respuesta de API
* Máximo de 1000 documentos devueltos por página de resultados de búsqueda
* Máximo de 100 sugerencias devueltas por solicitud de Sugerir API

## <a name="api-key-limits"></a>Límites de clave de API
Las claves de API se usan para la autenticación del servicio. Hay dos tipos. Las claves de administración se especifican en el encabezado de solicitud y conceden acceso completo de lectura y escritura al servicio. Las claves de consulta son de solo lectura, se especifican en la dirección URL y normalmente se distribuyen a las aplicaciones cliente.

* Máximo de 2 claves de administración por servicio
* Máximo de 50 claves de consultas por servicio

