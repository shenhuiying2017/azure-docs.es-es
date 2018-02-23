---
title: "Introducción a Azure Cosmos DB: API para MongoDB | Microsoft Docs"
description: "Obtenga información sobre cómo puede usar Azure Cosmos DB para almacenar y consultar grandes volúmenes de documentos JSON con latencia baja mediante las populares API MongoDB de OSS."
keywords: "Qué es MongoDB"
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: anhoh
ms.openlocfilehash: ffca8f4518361e8c5447d7bb7ed6022eb0e96a4a
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="introduction-to-azure-cosmos-db-api-for-mongodb"></a>Introducción a Azure Cosmos DB: API para MongoDB

[Azure Cosmos DB](../cosmos-db/introduction.md) es un servicio de base de datos con varios modelos y distribución global de Microsoft para aplicaciones críticas. Azure Cosmos DB ofrece una [distribución global inmediata](distribute-data-globally.md), [escalado elástico de rendimiento y almacenamiento](partition-data.md) en todo el mundo, latencias de menos de 10 ms en el percentil 99 y alta disponibilidad garantizada, todo ello respaldado por [acuerdos de nivel de servicio líderes del sector](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexa datos automáticamente](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sin que haya que ocuparse de la administración de esquemas ni de índices. Sigue varios modelos y es compatible con los modelos de datos de documento, de clave-valor, de gráfico y de columnas. 

![Azure Cosmos DB: API de MongoDB](./media/mongodb-introduction/cosmosdb-mongodb.png) 

Las bases de datos de Azure Cosmos DB se pueden utilizar como almacenes de datos para aplicaciones escritas para [MongoDB](https://docs.mongodb.com/manual/introduction/). Esta funcionalidad significa que mediante [controladores](https://docs.mongodb.org/ecosystem/drivers/) existentes, la aplicación escrita para MongoDB se puede comunicar ahora con Azure Cosmos DB y usar bases de datos de Azure Cosmos DB en lugar de bases de datos de MongoDB. En muchos casos, puede cambiar del uso de MongoDB a Azure Cosmos DB con solo cambiar una cadena de conexión. Mediante esta funcionalidad, se pueden crear y ejecutar fácilmente aplicaciones de base de datos de MongoDB en la nube de Azure con la distribución global de Azure Cosmos DB y los [completos Acuerdos de Nivel de Servicio líderes del sector](https://azure.microsoft.com/support/legal/sla/cosmos-db), al tiempo que se siguen usando conocimientos y herramientas conocidos para MongoDB.

**Compatibilidad con MongoDB**: puede usar la experiencia que tiene en MongoDB, el código de la aplicación y las herramientas a medida que Azure Cosmos DB implementa el protocolo de conexión de MongoDB 3.4 (versión 5) y admite la [canalización de agregación de MongoDB](mongodb-feature-support.md#aggregation-pipeline). Puede desarrollar aplicaciones con MongoDB e implementarlas en producción mediante el servicio Azure Cosmos DB distribuido globalmente completamente administrado.

## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>¿Cuál es la ventaja de utilizar Azure Cosmos DB para aplicaciones de MongoDB?

**Rendimiento y almacenamiento con escalabilidad flexible:** cubra las necesidades de su aplicación escalando o reduciendo verticalmente la base de datos de MongoDB. Los datos se almacenan en discos de estado sólido (SSD) para las bajas latencias predecibles. Azure Cosmos DB admite colecciones de MongoDB que pueden escalarse a tamaños de almacenamiento prácticamente ilimitados y rendimiento aprovisionado. Según vaya creciendo su aplicación, puede escalar Azure Cosmos DB de manera flexible con un rendimiento predecible impecable. 

**Replicación en varias regiones:** Azure Cosmos DB replica los datos de forma transparente en todas las regiones que tenga asociadas con su cuenta de MongoDB. Esto permite desarrollar aplicaciones que requieran acceso global a los datos al mismo tiempo que proporciona un equilibrio entre la coherencia, la disponibilidad y el rendimiento, todo ello con las garantías pertinentes. Azure Cosmos DB proporciona conmutación por error regional transparente con las API de hospedaje múltiple, así como la capacidad de escalar de forma elástica el rendimiento y el almacenamiento en todo el mundo. Obtenga más información en [Distribución de datos global con DocumentDB](distribute-data-globally.md).

**Sin administración de servidor**: no tiene que administrar y escalar las bases de datos de MongoDB. Azure Cosmos DB es un servicio completamente administrado, lo que significa que no tiene que administrar ninguna infraestructura o máquina virtual por su cuenta. Azure Cosmos DB está disponible en más de 30 [regiones de Azure](https://azure.microsoft.com/regions/services/).

**Niveles de coherencia ajustables:** Azure Cosmos DB implementa actualmente la versión 3.4 de MongoDB, que tiene dos opciones de configuración de coherencia, fuerte y eventual. Como Azure Cosmos DB tiene varias API, la configuración de coherencia se puede aplicar en el nivel de cuenta y cada una de las API controla el cumplimiento de la coherencia. Hasta MongoDB 3.6, no había ningún concepto de una coherencia de sesión, por lo que si establece una cuenta de API de MongoDB para usar coherencia de sesión, la coherencia cambia a eventual al usar las API de MongoDB. Si necesita una garantía de lectura de lo que ha escrito para una cuenta de API de MongoDB, el nivel de coherencia predeterminado para la cuenta se debe establecer en fuerte u obsolescencia limitada. Más información en el artículo sobre el [Uso de los niveles de coherencia para maximizar la disponibilidad y el rendimiento](consistency-levels.md).

| Nivel de coherencia predeterminado de Azure Cosmos DB |   API de Mongo (3.4) |
|---|---|
|Ocasional| Ocasional |
|De prefijo coherente| Eventual con orden coherente |
|Sesión| Eventual con orden coherente |
|De obsolescencia entrelazada| Alta |
| Alta | Alta |

**Indexación automática**: de manera predeterminada, Azure Cosmos DB indexa automáticamente todas las propiedades en los documentos de la base de datos de MongoDB y no espera ni requiere ningún esquema, ni tampoco la creación de índices secundarios. Además, la única funcionalidad de índice permite una restricción de unicidad en cualquier campo de documento que esté ya indexado automáticamente en Azure Cosmos DB.

**Nivel empresarial**: Azure Cosmos DB admite varias réplicas locales para ofrecer protección de datos y disponibilidad del 99,99 % frente a errores locales y regionales. Azure Cosmos DB presenta [certificaciones de conformidad](https://www.microsoft.com/trustcenter) y características de seguridad de nivel empresarial. 

Obtenga más información en este vídeo con el director de programas de Azure Cosmos DB, Aleksey Savateyev.

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T136/player]
> 

## <a name="how-to-get-started"></a>Primeros pasos

Siga las guías de inicio rápido sobre MongoDB si desea crear una cuenta de Azure Cosmos DB y migrar la aplicación de MongoDB para usar Azure Cosmos DB, o bien compile una nueva:

* [Migrar una aplicación web MongoDB existente de Node.js](create-mongodb-nodejs.md)
* [Compilar una aplicación web de API MongoDB con .NET y Azure Portal](create-mongodb-dotnet.md)
* [Compilar una aplicación de consola de la API MongoDB con Java y Azure Portal](create-mongodb-java.md)

## <a name="next-steps"></a>pasos siguientes

Obtenga información sobre cómo la API de MongoDB de Azure Cosmos DB se integra en la documentación de Azure Cosmos DB global, pero hay algunos indicadores para empezar:

* Para saber cómo obtener la información de la cadena de conexión de la cuenta, siga los pasos del tutorial [Conexión a una cuenta de MongoDB](connect-mongodb-account.md).
* Para obtener información sobre cómo crear una conexión entre la base de datos de Azure Cosmos DB y la aplicación de MongoDB en Studio 3T, siga los pasos del tutorial [Uso de Studio 3T (MongoChef) con Azure Cosmos DB](mongodb-mongochef.md).
* Siga el tutorial [Migración de datos a Azure Cosmos DB compatible con los protocolos de MongoDB](mongodb-migrate.md) para importar los datos a una base de datos de la API de MongoDB.
* Para conectarse a una cuenta de la API de MongoDB con [Robomongo](mongodb-robomongo.md).
* Obtenga información sobre cuántas RU utilizan las operaciones con el [comando GetLastRequestStatistics y las métricas de Azure Portal](request-units.md#GetLastRequestStatistics).
* Obtenga información sobre cómo [configurar las preferencias de lectura para las aplicaciones distribuidas globalmente](../cosmos-db/tutorial-global-distribution-mongodb.md).
