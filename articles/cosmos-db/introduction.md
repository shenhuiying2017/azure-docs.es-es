---
title: "Introducción a Azure Cosmos DB | Microsoft Docs"
description: "Información acerca de Azure Cosmos DB. Esta base de datos de varios modelos y distribución global se ha creado con latencia baja, escalabilidad elástica y alta disponibilidad."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/14/2017
ms.author: mimig
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: c9d04ae0bc11b99f893e5f003f136fbfe0dfccc9
ms.contentlocale: es-es
ms.lasthandoff: 08/11/2017

---

# <a name="welcome-to-azure-cosmos-db"></a>Bienvenido a Azure Cosmos DB

Azure Cosmos DB es la base de datos multimodelo de distribución global de Microsoft. Con tan solo un clic, Azure Cosmos DB permite escalar de forma elástica e individual el rendimiento y el almacenamiento en cualquiera de las regiones geográficas de Azure. Ofrece garantía de rendimiento, latencia, disponibilidad y coherencia con [acuerdos de nivel de servicio](https://aka.ms/acdbsla) (SLA)  integrales, algo que no ofrece ningún otro servicio de base de datos.

![Azure Cosmos DB es el servicio de base de datos de distribución global de Microsoft con escalado elástico horizontal, baja latencia garantizada, cinco modelos de coherencia y SLA integrales garantizados.](./media/introduction/azure-cosmos-db.png)

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Soluciones que se benefician de Azure Cosmos DB

Cualquier [aplicación web, móvil, de juegos y de IoT](use-cases.md) que deba controlar grandes cantidades de lecturas y escrituras en una escala [global](distribute-data-globally.md) con bajos tiempos de respuesta para una variedad de datos se beneficiará de la disponibilidad, elevado rendimiento, latencia baja y coherencia ajustable [garantizados](https://azure.microsoft.com/support/legal/sla/cosmos-db/) de Azure Cosmos DB.

## <a name="key-capabilities"></a>Principales capacidades
Como un servicio de base de datos distribuida globalmente, Azure Cosmos DB proporciona las siguientes funcionalidades para ayudarle a crear aplicaciones escalables y de gran capacidad de respuesta:

* **Distribución global inmediata**
    * También puede [distribuir los datos](distribute-data-globally.md) a cualquier número de [regiones de Azure](https://azure.microsoft.com/regions/), con el [clic de un botón](tutorial-global-distribution-documentdb.md). Esto le permite colocar los datos en donde están los usuarios, con lo que asegura de esta manera la latencia más baja posible para sus clientes. 
    * Con las API de hospedaje múltiple de Azure Cosmos DB, la aplicación siempre sabe dónde está la región más cercana y enviará solicitudes al centro de datos más cercano. Todo esto es posible sin realizar ningún cambio de configuración, ya que el usuario establece la región de escritura y tantas regiones de lectura como desee y el resto se administra para el usuario.

* **Varios modelos de datos y API populares para acceder a los datos y consultarlos**
    * El modelo de datos basado en la secuencia de registro de átomos (ARS) de Azure Cosmos DB se integra en forma nativa y es compatible con varios modelos de datos, entre ellos documentos, grafos, par de clave y valor, tablas y columnas.
    * Se admiten API para los siguientes modelos de datos con SDK disponibles en varios idiomas:
        * [API de DocumentDB](documentdb-introduction.md)
        * [API de MongoDB](mongodb-introduction.md)
        * [Table API](table-introduction.md)
        * [API de Graph (Gremlin)](graph-introduction.md)
        * Modelos de datos adicionales disponibles próximamente 

* **Escalado elástico del rendimiento y el almacenamiento a petición en todo el mundo**
    * Escale fácilmente el rendimiento de la base de datos por [segundos](request-units.md) y cámbielo cuando quiera. 
    * Escale el tamaño del almacenamiento de manera [transparente y automática](partition-data.md) para controlar los requisitos de tamaño ahora y siempre.

* **Creación de aplicaciones muy adaptables y de misión crítica**
    * Azure Cosmos DB garantiza una baja latencia global en el percentil 99 para sus clientes. 
    * Para un elemento típico de 1 KB, Cosmos DB garantiza latencias de lectura globales de menos de 10 ms y escrituras indexadas de menos de 15 ms en el percentil 99, en la misma región de Azure. Las latencias medianas son mucho menores (menos de 5 ms).

* **Disponibilidad "siempre activa" garantizada**
    * Disponibilidad del 99,99 % en una única región.
    * Impleméntela en cualquier número de [regiones de Azure](https://azure.microsoft.com/regions) para mayor disponibilidad.
    * [Simule un error](regional-failover.md) en una o más regiones sin pérdida de datos, garantizada. 

* **Escritura de aplicaciones de distribución global de la forma correcta**
    * Cinco [modelos de coherencia](consistency-levels.md) ofrecen un espectro de homogeneidad similar a SQL hasta la coherencia definitiva similar a NoSQL y en todo lo intermedio. 
  
* **Garantía de devolución del dinero**
    * Los datos llegarán rápidamente o le devolvemos el dinero. 
    * [Acuerdos de nivel de servicio](https://aka.ms/acdbsla) para la disponibilidad, la latencia, el rendimiento y la coherencia. 

* **Sin administración de esquema o índice de la base de datos**
    * Deje de preocuparse acerca de cómo mantener sus esquemas e índices de la base de datos e índices en sincronía con el esquema de la aplicación. No usamos esquemas. 
    * El motor de la base de datos de Azure Cosmos DB es totalmente independiente del esquema: indexa automáticamente todos los datos que se introducen sin necesidad de esquemas ni índices y las consultas son ultrarrápidas. 

* **Costo de propiedad reducido**
    * De cinco a diez veces [más rentable](https://aka.ms/cosmos-db-tco-paper) que una solución no administrada.
    * Tres veces más económica que DynamoDB.

## <a name="capability-comparison"></a>Comparación de funcionalidades

Azure Cosmos DB proporciona las mejores funcionalidades de las bases de datos relacionales y no relacionales.

| Capacidades | Bases de datos relacionales   | Bases de datos no relacionales (NoSQL) |    Azure Cosmos DB |
| --- | --- | --- | --- |
| Distribución global | No | No | Sí, distribución inmediata en más de 30 regiones, con las API de hospedaje múltiple|
| Escalado horizontal | No | Sí | Sí, puede escalar de manera independiente el almacenamiento y el rendimiento | 
| Garantías de latencia | No | Sí | Sí, 99 % de lecturas en <10 ms y escrituras de <15 ms. | 
| Alta disponibilidad | No | Sí | Sí, Cosmos DB siempre está activo, ofrece compensaciones PACELC y proporciona opciones de conmutación por error automáticas y manuales|
| Modelo de datos + API | Relacional + SQL | Varios modelos + API de OSS | Varios modelos + SQL + API de OSS (más próximamente) |
| SLA | Sí | No | Sí, SLA integrales para la latencia, el rendimiento, la coherencia y la disponibilidad |


## <a name="next-steps"></a>Pasos siguientes
Comience a usar Azure Cosmos DB con una de nuestras guías rápidas:

* [Introducción a la API DocumentDB para Azure Cosmos DB](create-documentdb-dotnet.md)
* [Introducción a la API MongoDB para Azure Cosmos DB](create-mongodb-nodejs.md)
* [Introducción a la API Graph para Azure Cosmos DB](create-graph-dotnet.md)
* [Introducción a la API Table para Azure Cosmos DB](create-table-dotnet.md)

