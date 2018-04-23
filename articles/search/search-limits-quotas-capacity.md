---
title: Límites de servicio en Azure Search | Microsoft Docs
description: Límites de servicio usados en la planeación de la capacidad y los límites máximos de solicitudes y respuestas de Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: heidist
ms.openlocfilehash: 59b6efd8459f675bc44891d7b04251260fdd38e9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
---
# <a name="service-limits-in-azure-search"></a>Límites de servicio en Azure Search
Los límites máximos del almacenamiento, las cargas de trabajo y las cantidades de índices, documentos y otros objetos dependen de si [aprovisiona Azure Search](search-create-service-portal.md) conforme a un plan de tarifa **Gratis**, **Básico** o **Estándar**.

+ **Gratis** es un servicio multiinquilino compartido incluido en su suscripción de Azure.

+ El plan **Básico** proporciona recursos de proceso dedicados para cargas de trabajo de producción a escala más pequeña.

+ **Estándar** se ejecuta en máquinas dedicadas, con más almacenamiento y capacidad de procesamiento en cada nivel. Estándar incluye cuatro niveles: S1, S2, S3 y S3 HD.

  S3 High Density (S3 HD) está diseñado para cargas de trabajo específicas: [multiinquilino](search-modeling-multitenant-saas-applications.md) y grandes cantidades de índices pequeños (un millón de documentos por índice, tres mil índices por servicio). Este nivel no proporciona la [característica de indexador](search-indexer-overview.md). En S3 HD, la ingesta de datos debe aprovechar el enfoque de inserción, mediante llamadas API para insertar los datos del origen en el índice. 

> [!NOTE]
> Un servicio se aprovisiona en un nivel específico. Saltar niveles para obtener capacidad implica el aprovisionamiento de un nuevo servicio (no hay ninguna actualización local). Para más información, vea [Selección de SKU o plan de tarifa](search-sku-tier.md). Para información sobre cómo ajustar la capacidad dentro de un servicio que ya ha aprovisionado, vea [Escalado de niveles de recursos para cargas de trabajo de indexación y consulta en Azure Search](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Límites de suscripción
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Límites de Storage
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Límites de índice

| Recurso | Gratuito | Básico&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD |
| -------- | ---- | ------------------- | --- | --- | --- | --- |
| Índices máximos |3 |5 o 15 |50 |200 |200 |1000 GB por partición o 3000 por servicio |
| Campos máximos por índice |1000 |100 |1000 |1000 |1000 |1000 |
| Perfiles de puntuación máximos por índice |100 |100 |100 |100 |100 |100 |
| Funciones máximas por perfil |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Los servicios básicos creados después de finales de 2017 han aumentado su límite en 15 índices, orígenes de datos e indexadores. Los servicios creados anteriormente tienen 5. El nivel básico es la única SKU con un límite inferior de 100 campos por índice.

## <a name="document-limits"></a>Límites de documento 

En la mayoría de las regiones, los planes de tarifa de Azure Search (Básico, S1, S2, S3, S3 HD) presentan un recuento ilimitado de documentos para todos los servicios creados después de noviembre/diciembre de 2017. En esta sección se identifican las regiones donde se aplican límites y cómo determinar si su servicio resulta afectado. 

Para determinar si su servicio tiene límites de documentos, compruebe el icono de uso de la página de información general de su servicio. Los recuentos de documentos son ilimitados o están sujetos a un límite basado en el nivel.

  ![Icono de Uso](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-and-services-having-document-limits"></a>Las regiones y servicios con límites de documentos

Los servicios que tienen límites se crearon antes de finales de 2017, o se ejecutan en centros de datos mediante clústeres de capacidad inferior para hospedar servicios de Azure Search. Los centros de datos afectados se encuentran en las siguientes regiones:

+ Australia Oriental
+ Asia oriental
+ India Central
+ Oeste de Japón
+ Centro occidental de EE.UU.

A los servicios sujetos a límites de documento se les aplican los límites máximos siguientes:

|  Gratuito | Básica | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10.000 |1 millón |15 millones por partición, 180 millones por servicio |60 millones por partición, 720 millones por servicio |120 millones por partición, 1400 millones por servicio |1 millón por índice, 200 millones por partición |

> [!Note] 
> Para los servicios de alta densidad S3 creados después de 2017, se ha quitado el límite de 200 millones de documentos por partición, pero el de 1 millón de documentos por índice se conserva.


### <a name="document-size-limits-per-api-call"></a>Límites de tamaño de documento por llamada API

El tamaño máximo del documento cuando se llama a una API de índice es de aproximadamente 16 megabytes.

El tamaño del documento es de hecho un límite en el tamaño del cuerpo de la solicitud de la API de índice. Dado que se puede pasar un lote de varios documentos a la API de índice de una vez, el límite de tamaño depende en realidad de cuántos documentos estén en el lote. Para un lote con un único documento, el tamaño máximo del documento es de 16 MB de JSON.

Para mantener el tamaño del documento reducido, no olvide excluir los datos no consultables de la solicitud. Las imágenes y otros datos binarios no son consultables directamente y no se deben almacenar en el índice. Para integrar los datos no consultables en los resultados de búsqueda, defina un campo que admita búsqueda que almacene una referencia de dirección URL al recurso.

## <a name="indexer-limits"></a>Límites de indexador

Los servicios básicos creados después de finales de 2017 han aumentado su límite en 15 índices, orígenes de datos e indexadores.

| Recurso | Gratis&nbsp;<sup>1</sup> | Básico&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|
| -------- | ----------------- | ----------------- | --- | --- | --- | --- |
| Indexadores máximos |3 |5 o 15|50 |200 |200 |N/D |
| Orígenes de datos máximos |3 |5 o 15 |50 |200 |200 |N/D |
| Carga máxima de indexación por invocación |10 000 documentos |Limitado solamente por el número máximo de documentos |Limitado solamente por el número máximo de documentos |Limitado solamente por el número máximo de documentos |Limitado solamente por el número máximo de documentos |N/D |
| Tiempo de ejecución máximo | 1-3 minutos |24 horas |24 horas |24 horas |24 horas |N/D  |
| Indexador de blobs: tamaño máximo de blob, MB |16 |16 |128 |256 |256 |N/D  |
| Indexador de blobs: número máximo de caracteres del contenido extraído de un blob |32 000 |64 000 |4 millones |4 millones |4 millones |N/D |

<sup>1</sup> Los servicios gratuitos tienen un tiempo de ejecución máximo del indexador de 3 minutos para servicios de blob y de 1 minuto para todos los demás orígenes de datos.

<sup>2</sup> Los servicios básicos creados después de finales de 2017 han aumentado su límite en 15 índices, orígenes de datos e indexadores. Los servicios creados anteriormente tienen 5.

<sup>3</sup> Los servicios S3 HD no incluyen compatibilidad con indexador.

## <a name="queries-per-second-qps"></a>Consultas por segundo (QPS)

Cada cliente debe desarrollar las estimaciones de QPS de manera independiente. El tamaño y la complejidad del índice, el tamaño y la complejidad de la consulta, y la cantidad de tráfico son los determinantes principales de las consultas por segundo. No hay manera de ofrecer estimaciones significativas cuando estos factores son desconocidos.

Las estimaciones son más predecibles cuando se calculan en los servicios que se ejecutan en recursos dedicados (niveles Básico y Estándar). Puede calcular el número de QPS con más precisión porque tiene control sobre más parámetros. Para obtener instrucciones sobre cómo encargarse del cálculo, consulte [Consideraciones sobre el rendimiento y la optimización de Azure Search](search-performance-optimization.md).

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
