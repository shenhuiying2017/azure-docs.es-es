---
title: Introducción a Azure Cosmos DB | Microsoft Docs
description: Información acerca de Azure Cosmos DB. Esta base de datos de varios modelos y distribución global se ha creado con latencia baja, escalabilidad elástica y alta disponibilidad.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/08/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 3c7fc83e379bcd1ad084d20555d9a82697cb5069
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="welcome-to-azure-cosmos-db"></a>Bienvenido a Azure Cosmos DB

Azure Cosmos DB es la base de datos multimodelo de distribución global de Microsoft. Con tan solo un clic, Azure Cosmos DB permite escalar de forma elástica e individual el rendimiento y el almacenamiento en cualquiera de las regiones geográficas de Azure. Ofrece garantía de rendimiento, latencia, disponibilidad y coherencia con [acuerdos de nivel de servicio](https://aka.ms/acdbsla) (SLA)  integrales, algo que no ofrece ningún otro servicio de base de datos. También puede [probar gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sin ninguna suscripción de Azure, de forma gratuita y sin compromiso.

> [!div class="nextstepaction"]
> [Probar gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB es el servicio de base de datos de distribución global de Microsoft con escalado elástico horizontal, baja latencia garantizada, cinco modelos de coherencia y los SLA más completos garantizados.](./media/introduction/azure-cosmos-db.png)

## <a name="key-capabilities"></a>Principales capacidades
Al igual que un servicio de base de datos de varios modelos y distribuido globalmente, Azure Cosmos DB facilita la compilación de aplicaciones escalables y de gran capacidad de respuesta a escala global:

* **Distribución global inmediata**
    * También puede [distribuir los datos](distribute-data-globally.md) a cualquier número de [regiones de Azure](https://azure.microsoft.com/regions/), con el [clic de un botón](tutorial-global-distribution-sql-api.md). Esto le permite colocar los datos en donde están los usuarios, con lo que asegura de esta manera la latencia más baja posible para sus clientes. 
    * Con las API de hospedaje múltiple de Azure Cosmos DB, la aplicación siempre sabe dónde está la región más cercana y envía solicitudes al centro de datos más cercano. Y todo esto es posible sin realizar ningún cambio en la configuración. Usted establece la región de escritura y tantas regiones de lectura como desee y el resto se administra sin intervención suya.
    * A medida que agrega y quita regiones a su base de datos de Azure Cosmos DB, no es necesario volver a implementar la aplicación y sigue con alta disponibilidad gracias a la funcionalidad de la API de hospedaje múltiple.

* **Varios modelos de datos y API populares para acceder a los datos y consultarlos**
    * El modelo de datos basado en la secuencia de registro de átomos (ARS) de Azure Cosmos DB se integra de forma nativa y es compatible con varios modelos de datos, entre ellos documentos, grafos, pares de clave-valor, tablas y modelos de tablas de familia de columnas.
    * Se admiten API para los siguientes modelos de datos con SDK disponibles en varios idiomas:
        * [SQL API](sql-api-introduction.md): un motor de base de datos JSON sin esquema con funcionalidades de consulta SQL avanzadas.
        * [API de MongoDB](mongodb-introduction.md): *MongoDB como servicio* masivamente escalable con la tecnología de la plataforma Azure Cosmos DB. Es compatible con las bibliotecas, los controladores, las herramientas y las aplicaciones existentes de MongoDB.
        * [Cassandra API](cassandra-introduction.md): Cassandra como servicio distribuido globalmente con tecnología de la plataforma Azure Cosmos DB. Es compatible con las bibliotecas, los controladores, las herramientas y las aplicaciones existentes de [Apache Cassandra](https://cassandra.apache.org/).
        * [Gremlin API](graph-introduction.md): servicio de base de datos de grafos de escalabilidad horizontal y completamente administrado que facilita la compilación y ejecución de aplicaciones que funcionan con conjuntos de datos altamente conectados compatibles con las API Open Graph (según la [especificación de Apache TinkerPop](http://tinkerpop.apache.org/) y Apache Gremlin).
        * [Table API](table-introduction.md): servicio de base de datos de par clave-valor compilado para proporcionar funcionalidades premium (por ejemplo, indexación automática, baja latencia garantizada y distribución global) a aplicaciones de almacenamiento existentes de Azure Table sin realizar ningún cambio en las aplicaciones.
        * Próximamente aparecerán nuevos modelos de datos y API adicionales.

* **Escalado elástico e independiente del rendimiento y del almacenamiento bajo demanda en todo el mundo**
    * Escale fácilmente el rendimiento de la base de datos por [segundos](request-units.md) y cámbielo cuando quiera. 
    * Escale el tamaño del almacenamiento de manera [transparente y automática](partition-data.md) para controlar los requisitos de tamaño ahora y siempre.

* **Creación de aplicaciones muy adaptables y de misión crítica**
    * Azure Cosmos DB garantiza una baja latencia global en el percentil 99 para sus clientes. 
    * Para un elemento típico de 1 KB, Cosmos DB garantiza una latencia de lecturas completas de menos de 10 ms y escrituras indexadas de menos de 15 ms en el percentil 99, en la misma región de Azure. Las latencias medianas son mucho menores (menos de 5 ms).

* **Disponibilidad "siempre activa" garantizada**
    * Contrato de nivel de servicio con disponibilidad del 99,99 % para todas las cuentas de bases de datos de una sola región y disponibilidad de lectura del 99,999 % para todas las cuentas de base de datos de varias regiones.
    * Impleméntela en cualquier número de [regiones de Azure](https://azure.microsoft.com/regions) para obtener una mayor disponibilidad y un mejor rendimiento.
    * Establezca de forma dinámica las prioridades para las regiones y [simule un error](regional-failover.md) de una o más regiones sin pérdida de datos garantizada para probar la disponibilidad completa en toda la aplicación (más allá de la base de datos). 

* **Escritura de aplicaciones de distribución global de la forma correcta**
    * Cinco [modelos de coherencia](consistency-levels.md) bien definidos, prácticos e intuitivos ofrecen un espectro de coherencia fuerte similar a SQL hasta la coherencia final relajada similar a NoSQL, y en todo lo intermedio. 
  
* **Garantía de devolución del dinero**
    * [Contratos de nivel de servicio](https://aka.ms/acdbsla) (SLA) líderes del sector, con soporte financiero y completos para la disponibilidad, la latencia, el rendimiento y la coherencia de los datos críticos. 

* **Sin administración de esquema o índice de la base de datos**
    * Itere rápidamente el esquema de su aplicación sin preocuparse de la administración del índice o del esquema de la base de datos.
    * El motor de la base de datos de Azure Cosmos DB es totalmente independiente del esquema: indexa automáticamente todos los datos que se introducen sin necesidad de esquemas ni índices y las consultas son ultrarrápidas. 

* **Costo de propiedad reducido**
    * De cinco a diez veces [más rentable](https://aka.ms/cosmos-db-tco-paper) que una solución no administrada o una solución NoSQL local.
    * Tres veces más económico que AWS DynamoDB o Google Spanner.

## <a name="capability-comparison"></a>Comparación de funcionalidades

Azure Cosmos DB proporciona las mejores funcionalidades de las bases de datos relacionales tradicionales y no relacionales.

| Capacidades | Bases de datos relacionales   | Bases de datos no relacionales (NoSQL) |    Azure Cosmos DB |
| --- | --- | --- | --- |
| Distribución global | Sin  | Sin  | Sí, distribución inmediata en más de 30 regiones, con las API de hospedaje múltiple|
| Escalado horizontal | Sin  | Sí | Sí, puede escalar de manera independiente el almacenamiento y el rendimiento | 
| Garantías de latencia | Sin  | Sí | Sí, 99 % de lecturas en <10 ms y escrituras de <15 ms. | 
| Alta disponibilidad | Sin  | Sí | Sí, Azure Cosmos DB siempre está activo, ofrece compensaciones PACELC bien definidas y proporciona opciones de conmutación por error automática y manual.|
| Modelo de datos + API | Relacional + SQL | Varios modelos + API de OSS | Varios modelos + SQL + API de OSS (más próximamente) |
| SLA | Sí | Sin  | Sí, SLA integrales para la latencia, el rendimiento, la coherencia y la disponibilidad |

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Soluciones que se benefician de Azure Cosmos DB

Cualquier [aplicación web, móvil, de juegos y de IoT](use-cases.md) que deba controlar grandes cantidades de datos, lecturas y escrituras en una escala [global](distribute-data-globally.md) con tiempos de respuesta casi en tiempo real para una variedad de datos se beneficiará de la alta disponibilidad, el elevado rendimiento, la latencia baja y la coherencia ajustable [garantizados](https://azure.microsoft.com/support/legal/sla/cosmos-db/) de Azure Cosmos DB. Conozca cómo se puede aplicar Azure CosmosDB a los sectores de [IoT y telemática](use-cases.md#iot-and-telematics), [comercio minorista y marketing](use-cases.md#retail-and-marketing), [juegos](use-cases.md#gaming) y [aplicaciones web y móviles](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Pasos siguientes
Comience a usar Azure Cosmos DB con una de nuestras guías rápidas:

* [Introducción a SQL API de Azure Cosmos DB](create-sql-api-dotnet.md)
* [Introducción a la API de MongoDB de Azure Cosmos DB](create-mongodb-nodejs.md)
* [Introducción a la API Cassandra de Azure Cosmos DB](create-cassandra-dotnet.md)
* [Introducción a Gremlin API de Azure Cosmos DB](create-graph-dotnet.md)
* [Introducción a Table API de Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Pruebe gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
