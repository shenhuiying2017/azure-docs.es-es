---
title: "Introducción a Azure Cosmos DB: API para MongoDB | Microsoft Docs"
description: "Obtenga información sobre cómo puede usar Azure Cosmos DB para almacenar y consultar grandes volúmenes de documentos JSON con latencia baja mediante las populares API MongoDB de OSS."
keywords: "Qué es MongoDB"
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7dc474f9f940132dd363740e8288a70ee2f2b970
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-cosmos-db-api-for-mongodb"></a>Introducción a Azure Cosmos DB: API para MongoDB

[Azure Cosmos DB](../cosmos-db/introduction.md) es un servicio de base de datos con varios modelos distribuido globalmente de Microsoft para aplicaciones críticas. Azure Cosmos DB proporciona una [distribución global inmediata](../documentdb/documentdb-distribute-data-globally.md), [escalado elástico de rendimiento y almacenamiento](../documentdb/documentdb-partition-data.md) en todo el mundo, latencias de pocos milisegundos en el percentil 99, [cinco niveles de coherencia bien definidos](../documentdb/documentdb-consistency-levels.md) y alta disponibilidad garantizada, todo ello respaldado por [Acuerdos de Nivel de Servicio líderes en el sector](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/). Azure Cosmos DB [indexa datos automáticamente](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sin necesidad de que se encargue de la administración del esquema ni del índice. Incluye varios modelos y admite modelos de datos de documento, de clave-valor, de grafo y de columnas. 

![Azure API para MongoDB](./media/documentdb-protocol-mongodb/cosmosdb-mongodb.png) 

Las bases de datos de DocumentDB se pueden utilizar como almacenes de datos para aplicaciones escritas para [MongoDB](https://docs.mongodb.com/manual/introduction/). Esto significa que mediante [controladores](https://docs.mongodb.org/ecosystem/drivers/) existentes, la aplicación escrita para MongoDB se puede comunicar ahora con Cosmos DB y usar bases de datos de DocumentDB en lugar de bases de datos de MongoDB. En muchos casos, puede cambiar del uso de MongoDB a DocumentDB con solo cambiar una cadena de conexión. Mediante esta funcionalidad, se pueden crear y ejecutar fácilmente aplicaciones de base de datos de MongoDB en la nube de Azure con la distribución global de Azure Cosmos DB y los [completos Acuerdos de Nivel de Servicio líderes del sector](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/), al tiempo que se siguen usando conocimientos y herramientas conocidos para MongoDB.


## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>¿Cuál es la ventaja de utilizar Azure Cosmos DB para aplicaciones de MongoDB?

* **Rendimiento y almacenamiento con escalabilidad flexible**: escale o reduzca verticalmente la base de datos de MongoDB para cubrir las necesidades de su aplicación. Los datos se almacenan en discos de estado sólido (SSD) para las bajas latencias predecibles. Cosmos DB admite colecciones de MongoDB que pueden escalarse a tamaños de almacenamiento prácticamente ilimitado y rendimiento aprovisionado. Según vaya creciendo su aplicación, puede escalar Cosmos DB de manera flexible con un rendimiento predecible impecable. 

* **Replicación en varias regiones:** Cosmos DB replica los datos de forma transparente en todas las regiones que tenga asociadas con su cuenta de MongoDB. Esto permite desarrollar aplicaciones que requieran acceso global a los datos al mismo tiempo que proporciona un equilibrio entre la coherencia, la disponibilidad y el rendimiento, todo ello con las garantías pertinentes. Cosmos DB proporciona conmutación por error regional transparente con las API de hospedaje múltiple, así como la capacidad de escalar de forma elástica el rendimiento y el almacenamiento en todo el mundo. Obtenga más información en [Distribución de datos global con DocumentDB](documentdb-distribute-data-globally.md).

**Compatibilidad de MongoDB**: puede usar su experiencia, el código de la aplicación y las herramientas de MongoDB existentes. Puede desarrollar aplicaciones con MongoDB e implementarlas en producción mediante el servicio Cosmos DB distribuido globalmente completamente administrado.

**Sin administración de servidor**: no tiene que administrar y escalar las bases de datos de MongoDB. Cosmos DB es un servicio completamente administrado, lo que significa que no tiene que administrar ninguna infraestructura o máquina virtual por su cuenta. Cosmos DB está disponible en más de 30 [regiones de Azure](https://azure.microsoft.com/regions/services/).

* **Niveles de coherencia ajustables**: cinco niveles de coherencia bien definidos donde elegir para lograr un equilibrio óptimo entre coherencia y rendimiento. Para las consultas y las operaciones de lectura, Cosmos DB ofrece cinco niveles de coherencia diferentes: segura, obsolescencia limitada, sesión, prefijo coherente y posible. Estos niveles de coherencia bien definidos y pormenorizados le permiten realizar equilibrios profundos entre la coherencia, la disponibilidad y la latencia. Más información en el artículo sobre el [Uso de los niveles de coherencia para maximizar la disponibilidad y el rendimiento](documentdb-consistency-levels.md).

* **Indexación automática**: de manera predeterminada, Cosmos DB indexa automáticamente todas las propiedades en los documentos de la base de datos de MongoDB y no espera ni requiere ningún esquema, ni tampoco la creación de índices secundarios.

**Nivel empresarial**: Azure Cosmos DB admite varias réplicas locales para ofrecer protección de datos y disponibilidad del 99,99 % frente a errores locales y regionales. Azure Cosmos DB presenta [certificaciones de conformidad](https://www.microsoft.com/trustcenter) y características de seguridad de nivel empresarial. 

Aprenda más en este vídeo de Azure Friday con Scott Hanselman y Kirill Gavrylyuk, Administrador de ingeniería principal de Azure Cosmos DB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/DocumentDB-Database-as-a-Service-for-MongoDB-Developers/player]
> 

## <a name="how-to-get-started"></a>Primeros pasos

Cree una cuenta de Azure Cosmos DB en [Azure Portal](https://portal.azure.com) y cambie la cadena de conexión de MongoDB a la nueva cuenta. 

*Y ya está.*

Para obtener instrucciones más detalladas, siga los pasos para [crear una cuenta](documentdb-create-mongodb-account.md) y [conectar con su cuenta](documentdb-connect-mongodb-account.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo la API de MongoDB de Azure Cosmos DB se integra en la documentación de Azure Cosmos DB global, pero hay algunos indicadores para empezar:

* Para saber cómo obtener la información de la cadena de conexión de la cuenta, siga los pasos del tutorial [Conexión a una cuenta de MongoDB](documentdb-connect-mongodb-account.md).
* Para obtener información sobre cómo crear una conexión entre la base de datos de Azure Cosmos DB y la aplicación de MongoDB en MongoChef, siga los pasos del tutorial [Uso de MongoChef con Azure Cosmos DB](documentdb-mongodb-mongochef.md).
* Siga el tutorial [Migración de datos a Azure Cosmos DB compatible con los protocolos de MongoDB](documentdb-mongodb-migrate.md) para importar los datos a una base de datos de la API de MongoDB.
* Compile su primera aplicación de API de MongoDB con [Node.js](documentdb-mongodb-samples.md).
* Compile su primera aplicación web de API de MongoDB con .[NET](documentdb-mongodb-application.md).
* Para conectarse a una cuenta de la API de MongoDB con [Robomongo](documentdb-mongodb-robomongo.md).
* Obtenga información sobre cuántas RU utilizan las operaciones con el [comando GetLastRequestStatistics y las métricas de Azure Portal](documentdb-request-units.md#GetLastRequestStatistics).
* Obtenga información sobre cómo [configurar las preferencias de lectura para las aplicaciones distribuidas globalmente](../cosmos-db/tutorial-global-distribution-mongodb.md).

